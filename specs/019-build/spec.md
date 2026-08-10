# 构建工具功能规格 (spec.md)

> 模块：019-build | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
构建工具模块定义项目的 Maven 多环境构建配置、Docker 容器化打包以及服务启动/停止脚本，支持本地开发、开发联调、生产部署三种环境的快速切换。

### 1.2 解决的问题
- 开发团队需要在本地、开发服务器、生产环境使用不同的数据库/Redis/日志配置，但不想手动修改配置文件
- 需要一键构建 Docker 镜像并编排所有服务（MySQL/Redis/Nginx/MinIO + 4 个业务服务）
- 需要在 Linux/Windows 服务器上方便地启动/停止/重启服务
- 需要 CI/CD 友好的版本管理（${revision} 统一版本号）

### 1.3 范围
- ✅ Maven 三环境构建（local / dev / prod）
- ✅ Docker 镜像构建（4 个 Dockerfile）
- ✅ Docker Compose 服务编排（MySQL/Redis/Nginx/MinIO + 业务服务）
- ✅ Linux Shell 启动/停止/重启脚本
- ✅ Windows 批处理启动脚本
- ✅ Maven Wrapper（mvnw）统一 Maven 版本
- ✅ CI 友好的扁平化 POM（flatten-maven-plugin）
- ❌ CI/CD Pipeline 脚本（.github/、Jenkinsfile 等）
- ❌ Kubernetes Helm Chart
- ❌ 环境变量文件（.env）— 所有变量在 docker-compose.yml 中有默认值

## 2. 用户故事
- 作为**开发人员**，我执行 `mvn clean package` 即可自动使用 dev 配置编译打包，无需额外参数
- 作为**开发人员**，我执行 `mvn clean package -P local` 可切换到本机环境配置
- 作为**运维人员**，我可以执行 `docker compose up` 一键启动所有服务
- 作为**运维人员**，我可以执行 `./ry.sh start` 在 Linux 服务器上启动单个 JAR 服务

## 3. 功能需求

- FR-019-001: 系统 MUST 提供 3 个 Maven Profile：`local`（本地开发）、`dev`（开发服务器，默认激活）、`prod`（生产环境）
- FR-019-002: 每个 Profile MUST 定义至少 `profiles.active`（激活的 Spring Profile）、`logging.level`（日志级别）、`monitor.username` 和 `monitor.password`（监控凭据）
- FR-019-003: Maven 资源过滤 MUST 对 `application*`、`bootstrap*`、`banner*` 文件生效，使 `@xxx@` 占位符被替换为 Profile 值
- FR-019-004: 系统 MUST 提供 4 个 Dockerfile，分别构建主服务（ruoyi-admin）、监控中心（ruoyi-monitor-admin）、SnailJob 服务（ruoyi-snailjob-server）、SnailAI 服务（ruoyi-snailai-server）镜像
- FR-019-005: Docker Compose MUST 编排 MySQL 8.4.9、Redis 8.6.3、Nginx 1.31.1、MinIO 以及上述 4 个业务服务
- FR-019-006: 系统 MUST 提供 Linux Shell 脚本 `ry.sh` 支持 start/stop/restart/status 操作
- FR-019-007: 系统 MUST 提供 Windows 批处理脚本 `ry.bat` 支持交互式启动/停止
- FR-019-008: Maven Wrapper (`mvnw` / `mvnw.cmd`) MUST 确保所有开发者使用相同 Maven 版本（3.9.12）
- FR-019-009: `flatten-maven-plugin` MUST 在 install/deploy 时生成扁平化 POM，使 `${revision}` 变量展开
- FR-019-010: Docker Compose MUST 使用 host 网络模式，便于服务间通过 localhost 通信
- FR-019-011: Nginx 配置 MUST 反向代理 `/prod-api/` → 主服务集群、`/admin/` → 监控中心、`/snail-job/` → SnailJob、`/snail-ai/` → SnailAI

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|----------|
| Maven Profile (local/dev/prod) | 构建环境配置 | profiles.active, logging.level, monitor.username, monitor.password |
| Dockerfile ×4 | 容器镜像构建文件 | 基础镜像（bellsoft/liberica-openjdk-rocky:21.0.12-cds）, EXPOSE 端口, ZGC JVM 参数 |
| docker-compose.yml | 容器编排 | 9 个服务，host 网络模式，镜像标签 `ruoyi/*:6.0.0` |
| nginx.conf | 反向代理 | upstream 负载均衡，SSE/WebSocket 支持，actuator 路径拦截 |
| ry.sh / ry.bat | 启动脚本 | JVM 参数（512m/1024m, ZGC, OOM dump），nohup 后台运行 |

## 5. 验收场景

### 场景：默认构建使用 dev 配置
- Given 开发者未指定 `-P` 参数
- When 执行 `mvn clean package`
- Then `${profiles.active}` 替换为 `dev`，打出的 JAR 包内 `application.yml` 包含 `spring.profiles.active: dev`

### 场景：构建 Docker 镜像
- Given 已执行 `mvn clean package` 生成 JAR 包
- When 进入 `ruoyi-admin/` 目录执行 `docker build -t ruoyi/ruoyi-server:6.0.0 .`
- Then 生成 `ruoyi/ruoyi-server:6.0.0` 镜像，JVM 启动参数为 ZGC

### 场景：Docker Compose 一键启动
- Given Docker 环境已就绪，已完成镜像构建
- When 在 `script/docker/` 目录执行 `docker compose up -d`
- Then 9 个容器全部启动，nginx:80 对外提供服务

### 场景：Linux 服务器启停
- Given 服务器上已有 JAR 包和 ry.sh 脚本
- When 执行 `./ry.sh start`
- Then 服务后台启动，PID 记录在 `.pid` 文件中
- When 执行 `./ry.sh stop`
- Then 服务进程被 kill，`.pid` 文件删除

## 6. 非功能需求
- 所有 Docker 镜像 MUST 使用 BellSoft Liberica JDK 21（含 CDS 类数据共享优化）
- JVM MUST 使用 ZGC（低延迟垃圾回收器），堆内存 512m-1024m
- `application-local.yml` MUST 不提交到 Git（.gitignore 忽略），防止本地敏感配置泄漏
- 构建产出的 JAR 命名 MUST 统一：`ruoyi-admin.jar`、`ruoyi-monitor-admin.jar` 等

## 7. 依赖
- Maven 3.9+（通过 mvnw wrapper 管理版本，从华为云镜像下载）
- Docker 20.10+（用于镜像构建和容器编排）
- BellSoft Liberica JDK 21 基础镜像
- Huawei Cloud Maven 仓库（加速依赖下载）
- IntelliJ IDEA `.run/` 配置（可选的 IDE 内 Docker 构建辅助）
