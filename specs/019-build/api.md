# 019-构建工具 - 接口规范

**模块编号：** 019  
**模块名称：** 构建工具  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、接口概述

构建工具模块主要提供构建和部署相关的脚本和配置，不涉及 REST API 接口。

**主要接口类型：**
- Maven 构建命令
- Shell 脚本命令
- Docker 命令
- 健康检查接口

---

## 二、Maven 构建命令

### 2.1 基础构建命令

| 命令 | 说明 | 示例 |
|------|------|------|
| clean | 清理构建产物 | `mvn clean` |
| compile | 编译源代码 | `mvn compile` |
| test | 运行测试 | `mvn test` |
| package | 打包项目 | `mvn package` |
| install | 安装到本地仓库 | `mvn install` |
| deploy | 部署到远程仓库 | `mvn deploy` |

### 2.2 组合构建命令

```bash
# 清理并打包（跳过测试）
mvn clean package -DskipTests

# 清理、编译、测试、打包
mvn clean verify

# 多模块构建（只构建指定模块）
mvn clean package -pl ruoyi-admin -am

# 使用指定环境打包
mvn clean package -Pprod
```

### 2.3 多环境打包

```bash
# 开发环境
mvn clean package -Pdev -DskipTests

# 测试环境
mvn clean package -Ptest -DskipTests

# 生产环境
mvn clean package -Pprod -DskipTests
```

---

## 三、启动脚本命令

### 3.1 Linux 脚本命令

**脚本位置：** `script/bin/ry.sh`

| 命令 | 说明 | 示例 |
|------|------|------|
| start | 启动服务 | `./ry.sh start` |
| stop | 停止服务 | `./ry.sh stop` |
| restart | 重启服务 | `./ry.sh restart` |
| status | 查看状态 | `./ry.sh status` |

**使用示例：**

```bash
# 进入脚本目录
cd script/bin

# 启动服务
./ry.sh start

# 查看状态
./ry.sh status

# 重启服务
./ry.sh restart

# 停止服务
./ry.sh stop
```

### 3.2 Windows 脚本命令

**脚本位置：** `script/bin/ry.bat`

| 命令 | 说明 | 示例 |
|------|------|------|
| start | 启动服务 | `ry.bat start` |
| stop | 停止服务 | `ry.bat stop` |
| restart | 重启服务 | `ry.bat restart` |

**使用示例：**

```batch
REM 启动服务
ry.bat start

REM 重启服务
ry.bat restart

REM 停止服务
ry.bat stop
```

---

## 四、Docker 命令

### 4.1 构建镜像

```bash
# 构建 Docker 镜像
docker build -t ruoyi-plus:5.x.x .

# 构建并推送
docker build -t ruoyi-plus:5.x.x .
docker push ruoyi-plus:5.x.x
```

### 4.2 Docker Compose 命令

```bash
# 启动所有服务
docker-compose up -d

# 停止所有服务
docker-compose down

# 查看服务状态
docker-compose ps

# 查看日志
docker-compose logs -f

# 重启服务
docker-compose restart

# 重新构建并启动
docker-compose up -d --build
```

### 4.3 服务管理

```bash
# 进入容器
docker exec -it ruoyi-admin sh

# 查看容器日志
docker logs -f ruoyi-admin

# 重启容器
docker restart ruoyi-admin

# 停止容器
docker stop ruoyi-admin
```

---

## 五、健康检查接口

### 5.1 Spring Boot Actuator

**基础路径：** `/actuator`

| 接口 | 说明 | 示例 |
|------|------|------|
| health | 健康检查 | `GET /actuator/health` |
| info | 应用信息 | `GET /actuator/info` |
| metrics | 性能指标 | `GET /actuator/metrics` |
| env | 环境变量 | `GET /actuator/env` |

### 5.2 健康检查响应

**成功响应：**
```json
{
  "status": "UP",
  "components": {
    "db": {
      "status": "UP",
      "details": {
        "database": "MySQL",
        "validationQuery": "SELECT 1"
      }
    },
    "redis": {
      "status": "UP",
      "details": {
        "connection": "success"
      }
    },
    "ping": {
      "status": "UP"
    }
  }
}
```

**失败响应：**
```json
{
  "status": "DOWN",
  "components": {
    "db": {
      "status": "DOWN",
      "details": {
        "error": "Connection refused"
      }
    }
  }
}
```

---

## 六、配置管理接口

### 6.1 环境配置查看

```bash
# 查看当前环境
java -jar ruoyi-admin.jar --spring.profiles.active=dev

# 查看配置
curl http://localhost:8080/actuator/env
```

### 6.2 动态刷新配置

```bash
# 刷新配置
curl -X POST http://localhost:8080/actuator/refresh
```

---

## 七、日志管理接口

### 7.1 日志级别查看

```bash
# 查看日志级别
curl http://localhost:8080/actuator/loggers
```

### 7.2 动态修改日志级别

```bash
# 修改指定包的日志级别为 DEBUG
curl -X POST http://localhost:8080/actuator/loggers/org.dromara \
  -H "Content-Type: application/json" \
  -d '{"configuredLevel": "DEBUG"}'

# 恢复为 INFO
curl -X POST http://localhost:8080/actuator/loggers/org.dromara \
  -H "Content-Type: application/json" \
  -d '{"configuredLevel": "INFO"}'
```

---

## 八、数据库管理命令

### 8.1 MySQL 命令

```bash
# 连接数据库
mysql -h localhost -u root -p

# 导入数据库脚本
mysql -u root -p ruoyi_plus < script/sql/schema.sql
mysql -u root -p ruoyi_plus < script/sql/data.sql

# 导出数据库
mysqldump -u root -p ruoyi_plus > backup.sql

# 查看数据库状态
mysql -u root -p -e "SHOW DATABASES;"
mysql -u root -p -e "USE ruoyi_plus; SHOW TABLES;"
```

### 8.2 Redis 命令

```bash
# 连接 Redis
redis-cli -h localhost -p 6379

# 查看 Redis 状态
redis-cli INFO

# 清空数据库
redis-cli FLUSHALL

# 查看键数量
redis-cli DBSIZE
```

---

## 九、Nginx 管理命令

### 9.1 Nginx 配置测试

```bash
# 测试配置文件
nginx -t

# 重新加载配置
nginx -s reload

# 停止 Nginx
nginx -s stop

# 重启 Nginx
nginx -s restart
```

### 9.2 Nginx 日志查看

```bash
# 查看访问日志
tail -f /var/log/nginx/access.log

# 查看错误日志
tail -f /var/log/nginx/error.log
```

---

## 十、监控命令

### 10.1 系统监控

```bash
# 查看 Java 进程
ps -ef | grep java

# 查看端口占用
netstat -tlnp | grep 8080

# 查看内存使用
free -h

# 查看磁盘使用
df -h

# 查看 CPU 使用
top
```

### 10.2 应用监控

```bash
# 查看应用日志
tail -f logs/ruoyi-admin.jar.log

# 查看错误日志
tail -f logs/error.log

# 查看 GC 日志
jstat -gc <pid> 1000
```

---

## 十一、最佳实践

### 11.1 构建最佳实践

1. **本地开发**：使用 `mvn clean compile` 快速编译
2. **提交前**：使用 `mvn clean verify` 完整验证
3. **打包发布**：使用 `mvn clean package -DskipTests -Pprod`

### 11.2 部署最佳实践

1. **开发环境**：使用脚本启动，方便调试
2. **测试环境**：使用 Docker Compose，环境一致
3. **生产环境**：使用 Kubernetes 或 Docker Swarm

### 11.3 监控最佳实践

1. **健康检查**：配置 Actuator 健康检查端点
2. **日志收集**：使用 ELK 或 Loki 收集日志
3. **指标监控**：使用 Prometheus + Grafana

---

**文档版本：** 1.0  
**创建日期：** 2026-03-13  
**审核状态：** 待审核
