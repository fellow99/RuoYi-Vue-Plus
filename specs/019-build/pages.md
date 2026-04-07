# 019-构建工具 - 页面交互

**模块编号：** 019  
**模块名称：** 构建工具  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、页面概述

构建工具模块主要提供构建和部署相关的脚本和配置，不涉及 Web 页面交互。

**相关页面：**
- Swagger API 文档页面（由 018-swagger 模块提供）
- Actuator 监控页面（可选）
- Grafana 监控仪表板（可选）

---

## 二、Actuator 监控页面（可选）

### 2.1 访问地址

**基础路径：** `/actuator`

**页面列表：**
- 健康检查：`/actuator/health`
- 应用信息：`/actuator/info`
- 性能指标：`/actuator/metrics`
- 环境变量：`/actuator/env`
- 日志级别：`/actuator/loggers`

### 2.2 页面布局

```
┌────────────────────────────────────────────┐
│  Actuator Endpoints                        │
├────────────────────────────────────────────┤
│  ┌──────────────────────────────────────┐  │
│  │ Health                               │  │
│  │ Status: UP                           │  │
│  │ - Database: UP                       │  │
│  │ - Redis: UP                          │  │
│  └──────────────────────────────────────┘  │
│  ┌──────────────────────────────────────┐  │
│  │ Metrics                              │  │
│  │ - JVM Memory: 512MB / 1024MB         │  │
│  │ - HTTP Requests: 1000/s              │  │
│  │ - Active Threads: 50                 │  │
│  └──────────────────────────────────────┘  │
└────────────────────────────────────────────┘
```

---

## 三、Grafana 监控仪表板（可选）

### 3.1 访问地址

**默认地址：** `http://localhost:3000`

**默认账号：** admin / admin

### 3.2 仪表板布局

```
┌─────────────────────────────────────────────────────────┐
│  RuoYi-Vue-Plus Monitoring Dashboard                    │
├──────────────┬──────────────┬──────────────┬───────────┤
│  CPU Usage   │  Memory      │  Disk I/O    │  Network  │
│  45%         │  2.1GB/4GB   │  100MB/s     │  50Mbps   │
├──────────────┴──────────────┴──────────────┴───────────┤
│  JVM Heap Memory                                        │
│  ┌──────────────────────────────────────────────────┐   │
│  │ ████████████░░░░░░░░░░░░░░░░░░░░ 45%             │   │
│  └──────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────┤
│  HTTP Request Rate                                      │
│  ┌──────────────────────────────────────────────────┐   │
│  │ ▃▄▅▆▇▆▅▄▃▂▁▂▃▄▅▆▇▆▅▄▃▂▁▂▃▄▅▆▇▆▅▄▃▂▁▂▃▄▅▆▇▆▅▄▃▂  │   │
│  └──────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────┤
│  Database Connections                                   │
│  Active: 20  Idle: 10  Max: 100                         │
└─────────────────────────────────────────────────────────┘
```

---

## 四、启动脚本交互

### 4.1 Linux 脚本输出

**启动服务：**
```bash
$ ./ry.sh start
Start ruoyi-admin.jar success...
```

**查看状态：**
```bash
$ ./ry.sh status
ruoyi-admin.jar is running...
```

**停止服务：**
```bash
$ ./ry.sh stop
Stop ruoyi-admin.jar
ruoyi-admin.jar (pid:12345) exiting...
ruoyi-admin.jar exited.
```

**重启服务：**
```bash
$ ./ry.sh restart
Stop ruoyi-admin.jar
ruoyi-admin.jar (pid:12345) exiting...
ruoyi-admin.jar exited.
Start ruoyi-admin.jar success...
```

### 4.2 Windows 脚本输出

**启动服务：**
```batch
C:\> ry.bat start
Start ruoyi-admin.jar success...
```

**停止服务：**
```batch
C:\> ry.bat stop
Stop ruoyi-admin.jar success...
```

---

## 五、Docker Compose 交互

### 5.1 启动服务

```bash
$ docker-compose up -d
Creating network "ruoyi_default" with the default driver
Creating ruoyi_mysql_1 ... done
Creating ruoyi_redis_1 ... done
Creating ruoyi_admin_1 ... done
Creating ruoyi_nginx_1 ... done
```

### 5.2 查看状态

```bash
$ docker-compose ps
      Name                     Command               State          Ports
-----------------------------------------------------------------------------------
ruoyi_admin_1       java -jar app.jar              Up      0.0.0.0:8080->8080/tcp
ruoyi_mysql_1       docker-entrypoint.sh mysqld    Up      0.0.0.0:3306->3306/tcp
ruoyi_nginx_1       nginx -g daemon off;           Up      0.0.0.0:80->80/tcp
ruoyi_redis_1       docker-entrypoint.sh redis ... Up      0.0.0.0:6379->6379/tcp
```

### 5.3 查看日志

```bash
$ docker-compose logs -f admin
Attaching to ruoyi_admin_1
admin_1  | 
admin_1  |   ____              _   _
admin_1  |  |  _ \ _   _  ___ | \ | |_   _
admin_1  |  | |_) | | | |/ _ \|  \| | | | |
admin_1  |  |  _ <| |_| | (_) | |\  | |_| |
admin_1  |  |_| \_\\__,_|\___/|_| \_|\__, |
admin_1  |                           |___/
admin_1  | 
admin_1  | Starting ruoyi-admin on port 8080...
admin_1  | Started RuoyiApplication in 10.5s
```

### 5.4 停止服务

```bash
$ docker-compose down
Stopping ruoyi_nginx_1 ... done
Stopping ruoyi_admin_1 ... done
Stopping ruoyi_redis_1 ... done
Stopping ruoyi_mysql_1 ... done
Removing ruoyi_nginx_1 ... done
Removing ruoyi_admin_1 ... done
Removing ruoyi_redis_1 ... done
Removing ruoyi_mysql_1 ... done
```

---

## 六、Maven 构建交互

### 6.1 构建输出

```bash
$ mvn clean package -DskipTests
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] ruoyi-plus                                                         [pom]
[INFO] ruoyi-common                                                       [pom]
[INFO] ruoyi-common-core                                                  [jar]
[INFO] ruoyi-common-log                                                   [jar]
[INFO] ruoyi-modules                                                      [pom]
[INFO] ruoyi-system                                                       [jar]
[INFO] ruoyi-generator                                                    [jar]
[INFO] ruoyi-admin                                                        [jar]
[INFO] 
[INFO] Building ruoyi-plus 5.x.x
[INFO] --- maven-clean-plugin:3.2.0:clean (default-clean) @ ruoyi-plus ---
[INFO] 
[INFO] --- maven-jar-plugin:3.3.0:jar (default-jar) @ ruoyi-admin ---
[INFO] Building jar: /path/to/ruoyi-admin/target/ruoyi-admin.jar
[INFO] 
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  45.123 s
[INFO] Finished at: 2026-03-13T10:00:00Z
[INFO] ------------------------------------------------------------------------
```

### 6.2 构建失败输出

```bash
[ERROR] COMPILATION ERROR : 
[ERROR] /path/to/File.java:[10,5] cannot find symbol
  symbol:   class SomeClass
  location: class SomeOtherClass
[ERROR] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.11.0:compile (default-compile) on project ruoyi-admin: Compilation failure
```

---

## 七、日志查看交互

### 7.1 实时查看日志

```bash
# 使用 tail 命令
tail -f logs/ruoyi-admin.jar.log

# 使用 docker logs
docker logs -f ruoyi-admin

# 使用 journalctl（systemd）
journalctl -u ruoyi-admin -f
```

### 7.2 搜索日志

```bash
# 搜索错误日志
grep "ERROR" logs/ruoyi-admin.jar.log

# 搜索最近 100 行
tail -100 logs/ruoyi-admin.jar.log

# 搜索特定时间范围
sed -n '/2026-03-13 10:00/,/2026-03-13 11:00/p' logs/ruoyi-admin.jar.log
```

---

## 八、健康检查交互

### 8.1 命令行检查

```bash
# 使用 curl
curl http://localhost:8080/actuator/health

# 使用 wget
wget -qO- http://localhost:8080/actuator/health

# 使用 httpie
http GET localhost:8080/actuator/health
```

### 8.2 浏览器访问

访问 `http://localhost:8080/actuator/health` 显示 JSON 响应：

```json
{
  "status": "UP",
  "components": {
    "db": {"status": "UP"},
    "redis": {"status": "UP"},
    "ping": {"status": "UP"}
  }
}
```

---

## 九、文件结构

### 9.1 项目目录结构

```
ruoyi-plus/
├── ruoyi-admin/
│   ├── src/
│   ├── target/
│   │   └── ruoyi-admin.jar
│   └── pom.xml
├── ruoyi-common/
│   └── ...
├── ruoyi-modules/
│   └── ...
├── script/
│   ├── bin/
│   │   ├── ry.sh
│   │   └── ry.bat
│   ├── docker/
│   │   ├── docker-compose.yml
│   │   └── database.yml
│   └── sql/
│       ├── schema.sql
│       └── data.sql
├── logs/
│   ├── app.log
│   └── error.log
└── pom.xml
```

---

## 十、注意事项

### 10.1 权限要求

- Linux 脚本需要执行权限：`chmod +x ry.sh`
- Docker 需要 root 权限或 docker 组
- 日志目录需要写权限

### 10.2 端口要求

- 应用端口：8080（可配置）
- MySQL 端口：3306
- Redis 端口：6379
- Nginx 端口：80

### 10.3 环境变量

- `JAVA_HOME` - Java 安装路径
- `MAVEN_HOME` - Maven 安装路径
- `DOCKER_HOST` - Docker 主机地址

---

**文档版本：** 1.0  
**创建日期：** 2026-03-13  
**审核状态：** 待审核
