# 019-build 技术方案 (plan.md)

> 对应规格：spec.md | 模块：019-build | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, Maven 3.9.12（mvnw wrapper）
- Docker 20.10+, Docker Compose v2
- BellSoft Liberica JDK 21 基础镜像（`bellsoft/liberica-openjdk-rocky:21.0.12-cds`）

### 1.2 依赖
- `flatten-maven-plugin` — CI 友好扁平化 POM
- `maven-compiler-plugin` — 编译 + 注解处理器
- `maven-surefire-plugin` — 单测分组执行
- `spring-boot-maven-plugin` — 可执行 JAR 打包
- `maven-resources-plugin` — 资源过滤（默认插件，根 pom 配置）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 多环境支持 | ✅ | 3 Profile（local/dev/prod），Maven 过滤 + Spring Profile |
| 容器化 | ✅ | 4 Dockerfile + docker-compose 9 服务编排 |
| 版本统一 | ✅ | `${revision}` 版本号 + flatten-maven-plugin |

## 3. Maven Profile 矩阵

### 3.1 Profile 定义（root pom.xml）

| Profile | activeByDefault | profiles.active | logging.level | monitor.user/pass |
|---------|----------------|-----------------|---------------|-------------------|
| local | 否 | local | info | ruoyi / 123456 |
| dev | **是** | dev | info | ruoyi / 123456 |
| prod | 否 | prod | warn | ruoyi / 123456 |

### 3.2 资源过滤
```xml
<resources>
  <resource>
    <directory>src/main/resources</directory>
    <filtering>true</filtering>
    <includes>
      <include>application*</include>
      <include>bootstrap*</include>
      <include>banner*</include>
    </includes>
  </resource>
</resources>
```
- `application.yml` 中的 `spring.profiles.active: @profiles.active@` → 替换为 `local`/`dev`/`prod`

### 3.3 模块体系
```
root pom.xml
├── ruoyi-admin           (可执行 JAR, finalName=ruoyi-admin)
├── ruoyi-common          (24 个公共模块)
├── ruoyi-modules         (6 个业务模块: demo/gen/job/system/workflow/ai)
├── ruoyi-extend          (3 个可部署模块: monitor-admin/snailai-server/snailjob-server)
└── ruoyi-api             (跨模块接口)
```

## 4. Docker 镜像清单

### 4.1 镜像详情

| 模块 | Dockerfile 位置 | 镜像标签 | 暴露端口 | JVM 参数 |
|------|----------------|----------|---------|----------|
| ruoyi-admin | ruoyi-admin/Dockerfile | ruoyi/ruoyi-server:6.0.0 | 8080, 28080, 38080 | ZGC, SERVER_PORT/SNAIL_JOB_PORT/SNAIL_AI_PORT 环境变量 |
| ruoyi-monitor-admin | ruoyi-extend/ruoyi-monitor-admin/Dockerfile | ruoyi/ruoyi-monitor-admin:6.0.0 | 9090 | 默认 JVM 参数 |
| ruoyi-snailjob-server | ruoyi-extend/ruoyi-snailjob-server/Dockerfile | ruoyi/ruoyi-snailjob-server:6.0.0 | 8800, 17888 | 默认 JVM 参数 |
| ruoyi-snailai-server | ruoyi-extend/ruoyi-snailai-server/Dockerfile | ruoyi/ruoyi-snailai-server:6.0.0 | 8900, 18888 | 默认 JVM 参数 |

### 4.2 Dockerfile 模板（以 ruoyi-admin 为例）
```dockerfile
FROM bellsoft/liberica-openjdk-rocky:21.0.12-cds
ENV TZ=Asia/Shanghai
WORKDIR /ruoyi
EXPOSE 8080
EXPOSE 28080
EXPOSE 38080
COPY ./target/ruoyi-admin.jar app.jar
ENTRYPOINT ["java", "-server", "-XX:+UseZGC", "-jar", "app.jar"]
```

## 5. Docker Compose 编排

### 5.1 服务列表（script/docker/docker-compose.yml）

| 服务 | 镜像 | 端口 | 说明 |
|------|------|------|------|
| mysql | mysql:8.4.9 | 3306 | 数据库 |
| nginx-web | nginx:1.31.1 | 80 | 反向代理 |
| redis | redis:8.6.3 | 6379 | 缓存 |
| minio | pgsty/minio | 9000/9001 | 对象存储 |
| ruoyi-server1 | ruoyi/ruoyi-server:6.0.0 | 8080 | 主服务实例 1 |
| ruoyi-server2 | ruoyi/ruoyi-server:6.0.0 | 8081 | 主服务实例 2 |
| ruoyi-monitor-admin | ruoyi/ruoyi-monitor-admin:6.0.0 | 9090 | 监控中心 |
| ruoyi-snailjob-server | ruoyi/ruoyi-snailjob-server:6.0.0 | 8800/17888 | 任务调度 |
| ruoyi-snailai-server | ruoyi/ruoyi-snailai-server:6.0.0 | 8900/18888 | AI 服务 |

- 所有服务使用 `network_mode: "host"`（性能优化，简化网络配置）
- 日志挂载到 `/docker/monitor/logs`

### 5.2 可选数据库（script/docker/database.yml）
- oracle12c, sqlserver 2017, postgres 14.2, postgres 13.6

### 5.3 Nginx 路由规则
```
location /prod-api/     → upstream server (8080/8081, ip_hash)
location /admin/        → upstream monitor-admin (9090)
location /snail-job/    → upstream snailjob (8800)
location /snail-ai/     → upstream snailai (8900)
location ~* /actuator   → return 403 (禁止外部访问)
```

## 6. 启动脚本

### 6.1 Linux（script/bin/ry.sh）
```bash
# 使用方式：./ry.sh {start|stop|restart|status}
# JVM 参数：
#   -Xms512m -Xmx1024m
#   -XX:+UseZGC
#   -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=512m
#   -XX:+HeapDumpOnOutOfMemoryError
# 后台运行：nohup java ... > /dev/null 2>&1 &
# PID 管理：echo $! > .pid
```

### 6.2 Windows（script/bin/ry.bat）
```batch
# 交互式菜单：1.启动 2.停止 3.重启 4.退出
# 使用 jps/taskkill 管理进程
```

## 7. 构建流程

### 7.1 标准构建
```bash
# 开发环境（默认）
mvn clean package -DskipTests

# 生产环境
mvn clean package -P prod -DskipTests

# 本地开发（需要自行创建 application-local.yml）
mvn clean package -P local -DskipTests
```

### 7.2 Docker 构建
```bash
# 1. 先 Maven 打包
mvn clean package -DskipTests

# 2. 构建所有镜像
cd ruoyi-admin && docker build -t ruoyi/ruoyi-server:6.0.0 .
cd ruoyi-extend/ruoyi-monitor-admin && docker build -t ruoyi/ruoyi-monitor-admin:6.0.0 .
cd ruoyi-extend/ruoyi-snailjob-server && docker build -t ruoyi/ruoyi-snailjob-server:6.0.0 .
cd ruoyi-extend/ruoyi-snailai-server && docker build -t ruoyi/ruoyi-snailai-server:6.0.0 .

# 3. 启动编排
cd script/docker && docker compose up -d
```

## 8. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| pom.xml | 根 POM（3 Profile, 版本管理, 模块聚合） | / |
| pom.xml (ruoyi-admin) | 主应用打包（spring-boot-maven-plugin） | ruoyi-admin/ |
| Dockerfile ×4 | 容器镜像构建 | ruoyi-admin/, ruoyi-extend/ruoyi-monitor-admin/, ruoyi-extend/ruoyi-snailjob-server/, ruoyi-extend/ruoyi-snailai-server/ |
| docker-compose.yml | 9 服务编排 | script/docker/ |
| database.yml | 可选数据库编排 | script/docker/ |
| nginx.conf | 反向代理 + 安全拦截 | script/docker/nginx/conf/ |
| redis.conf | Redis 持久化配置 | script/docker/redis/conf/ |
| ry.sh | Linux 启停脚本 | script/bin/ |
| ry.bat | Windows 启停脚本 | script/bin/ |
| mvnw / mvnw.cmd | Maven Wrapper | / |
| maven-wrapper.properties | wrapper 版本配置 | .mvn/wrapper/ |
| .gitignore | 忽略 target/, application-local.yml | / |
| .run/*.run.xml ×4 | IntelliJ IDEA Docker 构建配置 | .run/ |
