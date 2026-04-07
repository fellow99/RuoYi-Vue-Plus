# 019-构建工具 - 数据模型

**模块编号：** 019  
**模块名称：** 构建工具  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、Maven 项目模型

### 1.1 父 POM 结构

```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.dromara</groupId>
    <artifactId>ruoyi-plus</artifactId>
    <version>5.x.x</version>
    <packaging>pom</packaging>
    
    <modules>
        <module>ruoyi-admin</module>
        <module>ruoyi-common</module>
        <module>ruoyi-modules</module>
        <module>ruoyi-extend</module>
    </modules>
</project>
```

### 1.2 模块依赖关系

```
ruoyi-plus (父 POM)
├── ruoyi-admin (启动模块)
│   ├── ruoyi-common (公共模块)
│   ├── ruoyi-modules (业务模块)
│   └── ruoyi-extend (扩展模块)
├── ruoyi-common
│   ├── ruoyi-common-core
│   ├── ruoyi-common-log
│   ├── ruoyi-common-security
│   └── ...
├── ruoyi-modules
│   ├── ruoyi-system
│   ├── ruoyi-generator
│   └── ...
└── ruoyi-extend
```

---

## 二、配置文件模型

### 2.1 application.yml 结构

```yaml
spring:
  application:
    name: ruoyi-admin
  profiles:
    active: dev
  datasource:
    url: jdbc:mysql://localhost:3306/ruoyi_plus
    username: root
    password: password
  redis:
    host: localhost
    port: 6379
    password: 
    database: 0

server:
  port: 8080
  servlet:
    context-path: /

sa-token:
  token-name: Authorization
  timeout: 86400
```

### 2.2 多环境配置

**application-dev.yml:**
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/ruoyi_plus_dev
  redis:
    host: localhost
    
server:
  port: 8080
```

**application-prod.yml:**
```yaml
spring:
  datasource:
    url: jdbc:mysql://prod-db:3306/ruoyi_plus_prod
  redis:
    host: prod-redis
    
server:
  port: 80
```

---

## 三、Docker 配置模型

### 3.1 Docker Compose 配置

```yaml
version: '3.8'
services:
  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: ruoyi_plus
    volumes:
      - mysql-data:/var/lib/mysql
    ports:
      - "3306:3306"
  
  redis:
    image: redis:7-alpine
    volumes:
      - redis-data:/data
    ports:
      - "6379:6379"
  
  ruoyi-admin:
    build: .
    depends_on:
      - mysql
      - redis
    environment:
      - SPRING_PROFILES_ACTIVE=prod
    ports:
      - "8080:8080"
  
  nginx:
    image: nginx:alpine
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/html:/usr/share/nginx/html
    ports:
      - "80:80"
    depends_on:
      - ruoyi-admin

volumes:
  mysql-data:
  redis-data:
```

### 3.2 Dockerfile 配置

```dockerfile
FROM openjdk:17-slim

LABEL maintainer="Lion Li"
LABEL version="5.x.x"

WORKDIR /app

COPY ruoyi-admin.jar app.jar

EXPOSE 8080

ENV JAVA_OPTS="-Xms512m -Xmx1024m"

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]
```

---

## 四、启动脚本模型

### 4.1 Linux 启动脚本 (ry.sh)

```bash
#!/bin/sh
AppName=ruoyi-admin.jar

JVM_OPTS="-Dname=$AppName -Duser.timezone=Asia/Shanghai 
-Xms512m -Xmx1024m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=512m 
-XX:+HeapDumpOnOutOfMemoryError -XX:+UseZGC"

APP_HOME=`pwd`
LOG_PATH=$APP_HOME/logs/$AppName.log

function start() {
    PID=`ps -ef |grep java|grep $AppName|grep -v grep|awk '{print $2}'`
    if [ x"$PID" != x"" ]; then
        echo "$AppName is running..."
    else
        nohup java $JVM_OPTS -jar $AppName > /dev/null 2>&1 &
        echo "Start $AppName success..."
    fi
}

function stop() {
    echo "Stop $AppName"
    PID=`ps -ef |grep java|grep $AppName|grep -v grep|awk '{print $2}'`
    if [ x"$PID" != x"" ]; then
        kill -TERM $PID
        echo "$AppName (pid:$PID) exiting..."
    else
        echo "$AppName already stopped."
    fi
}

case $1 in
    start) start;;
    stop) stop;;
    restart) stop; sleep 2; start;;
    status) 
        PID=`ps -ef |grep java|grep $AppName|grep -v grep|wc -l`
        if [ $PID != 0 ]; then
            echo "$AppName is running..."
        else
            echo "$AppName is not running..."
        fi
    ;;
esac
```

### 4.2 Windows 启动脚本 (ry.bat)

```batch
@echo off
set APP_NAME=ruoyi-admin.jar
set JVM_OPTS=-Dname=%APP_NAME% -Duser.timezone=Asia/Shanghai -Xms512m -Xmx1024m

if "%1" == "start" (
    start javaw %JVM_OPTS% -jar %APP_NAME%
    echo Start %APP_NAME% success...
) else if "%1" == "stop" (
    for /f "tokens=2" %%i in ('tasklist ^| findstr java') do (
        taskkill /PID %%i /F
    )
    echo Stop %APP_NAME% success...
) else if "%1" == "restart" (
    call :stop
    timeout /t 2 /nobreak
    call :start
)

goto :eof

:start
start javaw %JVM_OPTS% -jar %APP_NAME%
goto :eof

:stop
for /f "tokens=2" %%i in ('tasklist ^| findstr java') do (
    taskkill /PID %%i /F
)
goto :eof
```

---

## 五、日志配置模型

### 5.1 logback.xml 配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    
    <!-- 日志输出格式 -->
    <property name="LOG_PATTERN" 
              value="%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n"/>
    
    <!-- 控制台输出 -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
    </appender>
    
    <!-- 文件输出 -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/app.log</file>
        <encoder>
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>logs/app.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>30</maxHistory>
            <maxFileSize>100MB</maxFileSize>
        </rollingPolicy>
    </appender>
    
    <!-- 错误日志 -->
    <appender name="ERROR_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/error.log</file>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <encoder>
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>logs/error.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
    </appender>
    
    <!-- 根日志级别 -->
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="FILE"/>
        <appender-ref ref="ERROR_FILE"/>
    </root>
    
</configuration>
```

---

## 六、数据库脚本模型

### 6.1 表结构脚本 (schema.sql)

```sql
-- 用户信息表
DROP TABLE IF EXISTS sys_user;
CREATE TABLE sys_user (
    user_id BIGINT PRIMARY KEY AUTO_INCREMENT COMMENT '用户 ID',
    user_name VARCHAR(30) NOT NULL COMMENT '用户账号',
    nick_name VARCHAR(30) NOT NULL COMMENT '用户昵称',
    email VARCHAR(50) COMMENT '邮箱',
    phonenumber VARCHAR(11) COMMENT '手机号码',
    sex CHAR(1) DEFAULT '0' COMMENT '用户性别',
    avatar VARCHAR(100) COMMENT '头像地址',
    status CHAR(1) DEFAULT '0' COMMENT '帐号状态',
    create_time DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    update_time DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间'
) COMMENT='用户信息表';

-- 角色信息表
DROP TABLE IF EXISTS sys_role;
CREATE TABLE sys_role (
    role_id BIGINT PRIMARY KEY AUTO_INCREMENT COMMENT '角色 ID',
    role_name VARCHAR(30) NOT NULL COMMENT '角色名称',
    role_key VARCHAR(100) NOT NULL COMMENT '角色权限字符串',
    status CHAR(1) DEFAULT '0' COMMENT '角色状态',
    create_time DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间'
) COMMENT='角色信息表';
```

### 6.2 初始化数据脚本 (data.sql)

```sql
-- 初始化超级管理员
INSERT INTO sys_user (user_id, user_name, nick_name, email, phonenumber, sex, status) 
VALUES (1, 'admin', '超级管理员', 'admin@example.com', '13800138000', '0', '0');

-- 初始化管理员角色
INSERT INTO sys_role (role_id, role_name, role_key, status) 
VALUES (1, '超级管理员', 'superadmin', '0');

-- 关联用户和角色
INSERT INTO sys_user_role (user_id, role_id) VALUES (1, 1);
```

---

## 七、环境变量模型

### 7.1 Docker 环境变量

```bash
# 数据库配置
SPRING_DATASOURCE_URL=jdbc:mysql://mysql:3306/ruoyi_plus
SPRING_DATASOURCE_USERNAME=root
SPRING_DATASOURCE_PASSWORD=root

# Redis 配置
SPRING_REDIS_HOST=redis
SPRING_REDIS_PORT=6379

# 应用配置
SPRING_PROFILES_ACTIVE=prod
SERVER_PORT=8080
```

### 7.2 .env 文件示例

```bash
# Docker Compose 环境变量
MYSQL_ROOT_PASSWORD=root
MYSQL_DATABASE=ruoyi_plus
REDIS_PASSWORD=
APP_VERSION=5.x.x
```

---

**文档版本：** 1.0  
**创建日期：** 2026-03-13  
**审核状态：** 待审核
