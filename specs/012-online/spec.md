# 012-online 在线用户管理模块规范

## 模块信息

| 属性 | 值 |
|------|-----|
| 模块编号 | 012 |
| 模块名称 | online |
| 中文名称 | 在线用户管理 |
| 所属系统 | RuoYi-Vue-Plus |
| 模块类型 | 监控管理 |
| 技术栈 | Spring Boot + Sa-Token + Redis |

## 功能概述

在线用户管理模块用于实时监控和管理当前已登录系统的用户会话，提供在线用户列表查看、会话详情查询和强制踢出用户功能，保障系统安全性和管理员对用户会话的控制能力。

## 功能清单

### 1. 在线用户列表查询
- **功能描述**: 展示当前所有已登录系统的在线用户会话信息
- **显示字段**:
  - 会话编号 (tokenId)
  - 用户名称 (userName)
  - 部门名称 (deptName)
  - 客户端类型 (clientKey)
  - 设备类型 (deviceType)
  - 登录 IP 地址 (ipaddr)
  - 登录地点 (loginLocation)
  - 浏览器类型 (browser)
  - 操作系统 (os)
  - 登录时间 (loginTime)
- **搜索条件**:
  - 登录账号 (模糊查询)
  - 登录 IP 地址 (模糊查询)
- **排序**: 按登录时间倒序排列
- **分页**: 支持分页展示

### 2. 当前用户在线设备查询
- **功能描述**: 查询当前登录用户的所有在线设备会话
- **接口**: GET /monitor/online
- **权限**: 登录用户无需额外权限
- **用途**: 用于用户查看和管理自己的登录设备

### 3. 强制踢出用户
- **功能描述**: 管理员可以强制终止指定用户的会话，使其立即下线
- **操作权限**: 仅超级管理员或拥有 `monitor:online:forceLogout` 权限的用户可操作
- **确认机制**: 操作前需二次确认，防止误操作
- **日志记录**: 记录强制踢出操作日志
- **操作结果**: 
  - 成功：用户会话被清除，用户被强制下线
  - 失败：返回错误信息

### 4. 强退当前在线设备
- **功能描述**: 用户可以强制踢出自己的其他在线设备
- **接口**: DELETE /monitor/online/myself/{tokenId}
- **权限**: 登录用户可操作自己的设备
- **日志记录**: 记录操作日志
- **用途**: 用于用户管理自己的多设备登录

## 接口规范

### 查询在线用户列表
```
GET /monitor/online/list
```
**请求参数**:
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| ipaddr | String | 否 | IP 地址 |
| userName | String | 否 | 登录账号 |

**响应数据**:
```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "total": 100,
    "rows": [
      {
        "tokenId": "xxx-xxx-xxx",
        "userName": "admin",
        "deptName": "研发部门",
        "clientKey": "default",
        "deviceType": "pc",
        "ipaddr": "192.168.1.1",
        "loginLocation": "北京",
        "browser": "Chrome 120",
        "os": "Windows 10",
        "loginTime": "2024-01-15 10:30:00"
      }
    ]
  }
}
```

### 强制踢出用户
```
DELETE /monitor/online/{tokenId}
```
**请求参数**:
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| tokenId | String | 是 | 会话 ID，路径参数 |

**响应数据**:
```json
{
  "code": 200,
  "msg": "强退成功"
}
```

### 获取当前用户在线设备
```
GET /monitor/online
```
**响应数据**:
```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "total": 2,
    "rows": [
      {
        "tokenId": "xxx-xxx-xxx",
        "userName": "admin",
        "deptName": "研发部门",
        "clientKey": "default",
        "deviceType": "pc",
        "ipaddr": "192.168.1.1",
        "loginLocation": "北京",
        "browser": "Chrome 120",
        "os": "Windows 10",
        "loginTime": "2024-01-15 10:30:00"
      }
    ]
  }
}
```

### 强退当前用户在线设备
```
DELETE /monitor/online/myself/{tokenId}
```
**请求参数**:
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| tokenId | String | 是 | 会话 ID，路径参数 |

**响应数据**:
```json
{
  "code": 200,
  "msg": "操作成功"
}
```

## 数据模型

### SysUserOnline (在线用户实体)
```java
public class SysUserOnline {
    /** 会话编号 */
    private String tokenId;
    
    /** 部门名称 */
    private String deptName;
    
    /** 用户名称 */
    private String userName;
    
    /** 客户端 */
    private String clientKey;
    
    /** 设备类型 */
    private String deviceType;
    
    /** 登录 IP 地址 */
    private String ipaddr;
    
    /** 登录地址 */
    private String loginLocation;
    
    /** 浏览器类型 */
    private String browser;
    
    /** 操作系统 */
    private String os;
    
    /** 登录时间 */
    private Long loginTime;
}
```

### UserOnlineDTO (在线用户数据传输对象)
```java
public class UserOnlineDTO {
    /** 会话编号 */
    private String tokenId;
    
    /** 用户 ID */
    private Long userId;
    
    /** 用户名称 */
    private String userName;
    
    /** 部门名称 */
    private String deptName;
    
    /** 客户端类型 */
    private String clientKey;
    
    /** 设备类型 */
    private String deviceType;
    
    /** 登录 IP */
    private String ipaddr;
    
    /** 登录地点 */
    private String loginLocation;
    
    /** 浏览器 */
    private String browser;
    
    /** 操作系统 */
    private String os;
    
    /** 登录时间 */
    private Long loginTime;
}
```

## 权限配置

| 权限标识 | 权限名称 | 说明 |
|----------|----------|------|
| monitor:online:list | 在线用户列表 | 查看在线用户列表 |
| monitor:online:forceLogout | 强制踢出用户 | 强制用户下线 |

## 技术实现要点

### 1. 会话存储
- **存储方式**: 基于 Redis 存储在线用户会话信息
- **键格式**: `online_token:{tokenId}`
- **过期策略**: 使用 Sa-Token 的自动续期机制

### 2. 会话管理
- **框架**: 使用 Sa-Token 进行会话管理
- **Token 验证**: 通过 StpUtil 进行 Token 状态检查
- **踢出机制**: 使用 StpUtil.kickoutByTokenValue() 强制踢出用户

### 3. 数据过滤
- **支持条件**: IP 地址、用户名模糊查询
- **实现方式**: 使用 StreamUtils 进行内存过滤
- **排序**: 按登录时间倒序排列

### 4. 安全性
- **权限控制**: 使用 @SaCheckPermission 注解控制接口访问
- **操作日志**: 强制踢出操作记录日志
- **幂等性**: 使用@RepeatSubmit 防止重复提交

### 5. 性能优化
- **缓存**: 在线用户数据缓存在 Redis 中
- **批量查询**: 使用 Redis keys 命令批量获取 token
- **过期检查**: 自动跳过已过期的 token

## 依赖模块

- ruoyi-common-redis: Redis 工具类
- ruoyi-common-core: 核心工具类和常量
- ruoyi-common-log: 操作日志记录
- ruoyi-common-mybatis: 分页和数据权限
- ruoyi-system: 用户和部门基础信息

## 外部依赖

- **Sa-Token**: 会话管理和权限控制
- **Redis**: 会话数据存储
- **Spring Boot**: 基础框架

## 异常处理

| 异常类型 | 处理方式 |
|----------|----------|
| NotLoginException | 捕获并忽略，用户已下线 |
| Redis 连接异常 | 返回错误提示，记录日志 |
| 权限不足 | 返回 403 错误 |

## 日志记录

### 操作日志
- **标题**: 在线用户
- **业务类型**: FORCE (强制操作)
- **记录内容**: 操作人、被踢出用户、操作时间、操作结果

## 监控指标

- 在线用户数量
- 会话平均在线时长
- 强制踢出操作次数
- Redis 会话存储占用

## 版本历史

| 版本 | 日期 | 变更说明 |
|------|------|----------|
| 1.0 | 2024-01 | 初始版本 |
