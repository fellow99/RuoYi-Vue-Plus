# 019-构建工具 - 功能规格

**模块编号：** 019  
**模块名称：** 构建工具 (Build Tools)  
**所属模块：** 基础设施  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、功能概述

构建工具模块为 RuoYi-Vue-Plus 提供完整的项目构建、部署和运维支持。包括 Maven 构建配置、启动脚本、Docker 容器化部署等工具，确保项目可以快速构建、部署和运行。

### 1.1 核心功能

- **Maven 构建**：项目依赖管理和编译打包
- **启动脚本**：Linux/Windows 启动脚本
- **Docker 部署**：容器化部署配置
- **环境配置**：多环境配置支持
- **数据库脚本**：数据库初始化脚本
- **日志管理**：日志配置和管理

### 1.2 与 RuoYi-Vue 的主要差异

| 特性 | RuoYi-Vue | RuoYi-Vue-Plus | 说明 |
|------|-----------|----------------|------|
| **Java 版本** | Java 8 | Java 17+ | 使用新特性 |
| **Spring Boot** | 2.x | 3.x | 最新版本 |
| **容器化** | 基础支持 | 完整支持 | Docker Compose 配置 |
| **启动脚本** | 基础脚本 | 增强脚本 | 更完善的日志管理 |
| **多环境** | 手动配置 | 自动切换 | 环境配置文件 |

### 1.3 业务规则

1. **构建顺序**：先编译后端，再构建前端
2. **环境隔离**：开发、测试、生产环境隔离
3. **版本管理**：Maven 版本号统一管理
4. **日志保留**：日志文件保留策略
5. **健康检查**：服务启动后健康检查

---

## 二、功能详细设计

### 2.1 Maven 构建

**功能描述：** 使用 Maven 进行项目构建

**构建命令：**
```bash
# 清理编译
mvn clean

# 编译项目
mvn compile

# 运行测试
mvn test

# 打包项目
mvn package

# 安装到本地仓库
mvn install

# 跳过测试打包
mvn package -DskipTests
```

**构建产物：**
- `ruoyi-admin/target/ruoyi-admin.jar` - 可执行 JAR 包

**业务规则：**
1. 多模块项目统一版本
2. 依赖版本在父 POM 统一管理
3. 支持多环境打包

### 2.2 启动脚本（Linux）

**功能描述：** Linux 环境下启动/停止/重启服务

**脚本位置：** `script/bin/ry.sh`

**支持命令：**
- `./ry.sh start` - 启动服务
- `./ry.sh stop` - 停止服务
- `./ry.sh restart` - 重启服务
- `./ry.sh status` - 查看状态

**业务规则：**
1. 检查 Java 环境
2. 检查端口占用
3. 记录启动日志
4. 后台运行

### 2.3 启动脚本（Windows）

**功能描述：** Windows 环境下启动服务

**脚本位置：** `script/bin/ry.bat`

**支持命令：**
- `ry.bat start` - 启动服务
- `ry.bat stop` - 停止服务
- `ry.bat restart` - 重启服务

### 2.4 Docker 部署

**功能描述：** 使用 Docker 容器化部署

**配置文件：**
- `script/docker/docker-compose.yml` - Docker Compose 配置
- `script/docker/database.yml` - 数据库配置

**支持服务：**
- RuoYi-Vue-Plus 应用
- MySQL 数据库
- Redis 缓存
- Nginx 反向代理

**业务规则：**
1. 使用官方基础镜像
2. 配置健康检查
3. 数据卷持久化
4. 网络隔离

### 2.5 多环境配置

**功能描述：** 支持多环境配置

**环境类型：**
- `dev` - 开发环境
- `test` - 测试环境
- `prod` - 生产环境

**配置文件：**
- `application-dev.yml` - 开发环境
- `application-test.yml` - 测试环境
- `application-prod.yml` - 生产环境

**业务规则：**
1. 通过参数指定环境
2. 环境配置优先级高于默认配置
3. 敏感信息使用环境变量

### 2.6 数据库脚本

**功能描述：** 数据库初始化和升级脚本

**脚本位置：** `script/sql/`

**脚本类型：**
- `schema.sql` - 表结构脚本
- `data.sql` - 初始化数据脚本
- `upgrade/` - 升级脚本

**业务规则：**
1. 按版本号组织升级脚本
2. 支持增量升级
3. 记录升级历史

### 2.7 日志管理

**功能描述：** 日志配置和管理

**日志框架：** Logback

**配置文件：** `logback.xml`

**日志级别：**
- DEBUG - 调试日志
- INFO - 信息日志
- WARN - 警告日志
- ERROR - 错误日志

**业务规则：**
1. 按天滚动日志文件
2. 日志文件保留 30 天
3. 日志文件大小限制 100MB
4. 生产环境关闭 DEBUG 日志

---

## 三、用户故事

### 3.1 开发人员 - 本地构建项目

**作为** 开发人员  
**我想要** 在本地构建项目  
**以便于** 运行和调试

**验收标准：**
- 可以使用 Maven 命令构建
- 构建成功后生成 JAR 包
- 可以在本地运行

### 3.2 运维人员 - 部署服务

**作为** 运维人员  
**我想要** 使用脚本部署服务  
**以便于** 快速上线

**验收标准：**
- 可以使用启动脚本
- 可以启动/停止/重启服务
- 可以查看服务状态

### 3.3 运维人员 - Docker 部署

**作为** 运维人员  
**我想要** 使用 Docker 部署服务  
**以便于** 容器化管理

**验收标准：**
- 可以使用 Docker Compose
- 可以一键启动所有服务
- 数据持久化保存

### 3.4 开发人员 - 多环境切换

**作为** 开发人员  
**我想要** 切换不同环境配置  
**以便于** 在不同环境测试

**验收标准：**
- 可以指定环境启动
- 环境配置自动加载
- 数据库连接正确

---

## 四、验收场景

### 4.1 本地构建项目

**场景：** 开发人员本地构建

**给定** 已安装 Java 17 和 Maven  
**当** 开发人员执行 `mvn package -DskipTests`  
**那么** 构建成功  
**并且** 生成 `ruoyi-admin.jar` 文件

### 4.2 启动服务

**场景：** 使用脚本启动服务

**给定** 已准备好 JAR 包  
**当** 运维人员执行 `./ry.sh start`  
**那么** 服务启动  
**并且** 日志输出到文件  
**并且** 服务后台运行

### 4.3 Docker 部署

**场景：** 使用 Docker Compose 部署

**给定** 已安装 Docker  
**当** 运维人员执行 `docker-compose up -d`  
**那么** 所有服务启动  
**并且** 应用可以访问  
**并且** 数据持久化

---

## 五、技术实现要点

### 5.1 Maven 配置

**父 POM 配置：**
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.x.x</version>
</parent>

<properties>
    <java.version>17</java.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```

### 5.2 启动脚本配置

**JVM 参数：**
```bash
JVM_OPTS="-Dname=ruoyi-admin.jar -Duser.timezone=Asia/Shanghai 
-Xms512m -Xmx1024m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=512m 
-XX:+HeapDumpOnOutOfMemoryError -XX:+UseZGC"
```

### 5.3 Docker 配置

**Dockerfile：**
```dockerfile
FROM openjdk:17-slim
WORKDIR /app
COPY ruoyi-admin.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 5.4 日志配置

**logback.xml：**
```xml
<configuration>
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/app.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>logs/app.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
    </appender>
</configuration>
```

---

## 六、依赖与约束

### 6.1 外部依赖

- Java 17+
- Maven 3.6+
- Docker 20.x+
- Docker Compose 2.x+

### 6.2 系统要求

- Linux/Windows/MacOS
- 内存：最低 2GB，推荐 4GB+
- 磁盘：最低 1GB，推荐 10GB+

### 6.3 配置要求

- 数据库连接配置
- Redis 连接配置
- 端口配置
- 日志路径配置

---

## 七、待澄清事项

1. **[NEEDS CLARIFICATION]** 是否需要支持 Kubernetes 部署
2. **[NEEDS CLARIFICATION]** 是否需要 CI/CD 集成配置
3. **[NEEDS CLARIFICATION]** 是否需要支持多节点集群部署

---

**文档版本：** 1.0  
**创建日期：** 2026-03-13  
**审核状态：** 待审核
