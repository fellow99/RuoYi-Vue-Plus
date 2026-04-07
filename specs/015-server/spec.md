# 015-server 服务监控模块规范

## 模块信息

| 属性 | 值 |
|------|-----|
| 模块编号 | 015 |
| 模块名称 | server |
| 中文名称 | 服务监控 |
| 所属系统 | RuoYi-Vue-Plus |
| 模块类型 | 监控管理 |
| 技术栈 | Spring Boot Actuator + OSHI |

## 功能概述

服务监控模块用于实时监控服务器运行状态，包括 CPU、内存、磁盘、JVM 等系统资源，帮助运维人员及时发现和解决系统问题。

## 功能清单

### 1. CPU 监控
- CPU 使用率
- CPU 核心数
- CPU 负载
- 使用率趋势图

### 2. 内存监控
- 物理内存使用
  - 总内存
  - 已用内存
  - 剩余内存
  - 使用率
- JVM 内存使用
  - 堆内存
  - 非堆内存
  - GC 统计

### 3. 磁盘监控
- 磁盘分区信息
  - 分区名称
  - 总容量
  - 已用容量
  - 剩余容量
  - 使用率
- 磁盘 I/O 统计

### 4. JVM 监控
- 线程信息
  - 线程总数
  - 活跃线程数
  - 守护线程数
- 堆栈信息
- GC 统计

### 5. 系统信息
- 操作系统信息
- Java 版本信息
- 系统运行时间

## 接口规范

### 获取服务器信息
```
GET /monitor/server
```
**响应数据**:
```json
{
  "code": 200,
  "data": {
    "cpu": {...},
    "memory": {...},
    "disk": [...],
    "jvm": {...},
    "sys": {...}
  }
}
```

## 数据模型

### ServerInfo
```java
public class ServerInfo {
    private CpuInfo cpu;
    private MemoryInfo memory;
    private List<DiskInfo> disk;
    private JvmInfo jvm;
    private SysInfo sys;
}
```

### CpuInfo
```java
public class CpuInfo {
    private Integer cpuNum;      // CPU 核心数
    private Double used;         // 使用率
    private Double sys;          // 系统使用率
    private Double wait;         // 等待率
    private Double free;         // 空闲率
}
```

### MemoryInfo
```java
public class MemoryInfo {
    private Double total;        // 总内存 (GB)
    private Double used;         // 已用内存 (GB)
    private Double free;         // 剩余内存 (GB)
    private Double usage;        // 使用率 (%)
}
```

## 权限配置

| 权限标识 | 权限名称 | 说明 |
|----------|----------|------|
| monitor:server:list | 服务监控 | 查看服务器信息 |

## 技术实现要点

### 1. 系统信息采集
- 使用 OSHI 库获取硬件信息
- 使用 JVM MXBean 获取 JVM 信息
- 使用 Spring Boot Actuator 获取应用信息

### 2. 数据刷新
- 支持手动刷新
- 支持定时自动刷新
- 刷新频率可配置

### 3. 性能优化
- 缓存采集结果
- 异步采集数据
- 避免频繁系统调用

## 依赖模块

- ruoyi-common-core: 核心工具类
- ruoyi-extend/ruoyi-monitor-admin: 监控扩展

## 外部依赖

- **OSHI**: 系统硬件信息采集
- **Spring Boot Actuator**: 应用监控

## 监控指标

- CPU 使用率
- 内存使用率
- 磁盘使用率
- JVM 堆内存使用率
- 线程数
