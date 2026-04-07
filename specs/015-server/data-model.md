# 015-server 数据模型文档

## 数据模型

### ServerInfo (服务器信息)

| 字段 | 类型 | 说明 |
|------|------|------|
| cpu | CpuInfo | CPU 信息 |
| memory | MemoryInfo | 内存信息 |
| disk | List<DiskInfo> | 磁盘列表 |
| jvm | JvmInfo | JVM 信息 |
| sys | SysInfo | 系统信息 |

### CpuInfo

| 字段 | 类型 | 说明 |
|------|------|------|
| cpuNum | Integer | CPU 核心数 |
| used | Double | 使用率 |
| sys | Double | 系统使用率 |
| wait | Double | 等待率 |
| free | Double | 空闲率 |

### MemoryInfo

| 字段 | 类型 | 说明 |
|------|------|------|
| total | Double | 总内存 (GB) |
| used | Double | 已用内存 (GB) |
| free | Double | 剩余内存 (GB) |
| usage | Double | 使用率 (%) |

### DiskInfo

| 字段 | 类型 | 说明 |
|------|------|------|
| dirName | String | 目录名称 |
| total | Double | 总容量 (GB) |
| used | Double | 已用容量 (GB) |
| free | Double | 剩余容量 (GB) |
| usage | Double | 使用率 (%) |

### JvmInfo

| 字段 | 类型 | 说明 |
|------|------|------|
| version | String | Java 版本 |
| home | String | Java 安装目录 |
| total | Double | 总内存 (MB) |
| max | Double | 最大内存 (MB) |
| free | Double | 空闲内存 (MB) |
| used | Double | 已用内存 (MB) |
| threadCount | Integer | 线程数 |
