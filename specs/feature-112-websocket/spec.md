# WebSocket 实时通信功能规格 (spec.md)

> 模块：feature-112-websocket | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
基于 Spring WebSocket 协议提供 Token 鉴权的实时双向通信能力，支持分布式集群环境下的会话同步，通过 Redis Pub/Sub 实现跨节点消息推送，支持心跳检测、多终端同时在线、消息持久化等功能。

### 1.2 解决的问题
- 原生 Spring WebSocket 无认证机制，需要集成 Sa-Token 鉴权
- 单机 WebSocket 无法在集群环境下跨节点推送消息
- 需要自动清理失效连接、防止内存泄漏

### 1.3 范围
- ✅ WebSocket Token 鉴权（PlusWebSocketInterceptor 握手拦截）
- ✅ 分布式会话同步（Redis Pub/Sub 跨节点消息推送）
- ✅ 多终端支持（userId → token → WebSocketSession 二级映射）
- ✅ 心跳检测（客户端 ping → 服务端 pong）
- ✅ 同 token 踢出旧连接（防止重复连接）
- ✅ 定时会话监控（60 秒间隔清理失效连接）
- ✅ 条件启用（message.transport=websocket）
- ❌ WebSocket 消息体加密（使用框架统一接口加密即可）

## 2. 用户故事
- 作为**用户**，登录系统后自动建立 WebSocket 长连接，实时接收通知消息
- 作为**管理员**，发布通知后所有在线用户通过 WebSocket 立即收到推送
- 作为**运维人员**，WebSocket 支持集群部署，任意节点故障不影响消息推送

## 3. 功能需求
- FR-112-001: 系统 MUST 支持 WebSocket 握手阶段 Token 鉴权
- FR-112-002: 系统 MUST 支持 Redis Pub/Sub 分布式消息同步
- FR-112-003: 系统 MUST 支持多终端同时在线（同用户多设备）
- FR-112-004: 系统 MUST 支持心跳检测维持长连接
- FR-112-005: 系统 MUST 支持定时清理失效会话防止内存泄漏
- FR-112-006: 系统 SHOULD 支持同 token 新连接踢出旧连接

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| PlusWebSocketHandler | WebSocket 生命周期处理器（连接/消息/心跳/断开/异常） |
| PlusWebSocketInterceptor | 握手拦截器（提取 LoginUser + Token 到 session attributes） |
| WebSocketSessionManager | 会话管理器（USER_TOKEN_SESSIONS: userId → token → WebSocketSession） |
| MessageWebSocketConfiguration | WebSocket 自动装配（@EnableWebSocket, 条件启用） |
| PushSessionManager | 统一推送会话接口（WebSocketSessionManager 实现） |

## 5. 连接流程
```
客户端发起 WebSocket → PlusWebSocketInterceptor.beforeHandshake()
    → LoginHelper.getLoginUser() 获取当前登录用户
    → StpUtil.getTokenValue() 获取 Token
    → 存入 session attributes (LOGIN_USER_KEY, LOGIN_TOKEN_KEY)
→ PlusWebSocketHandler.afterConnectionEstablished()
    → 校验 loginUser + token 非空，否则关闭连接
    → ConcurrentWebSocketSessionDecorator 并发安全包装
    → WebSocketSessionManager.connect(userId, token, session)
        → 移除同 token 旧连接（踢出提示）
        → 存入 ConcurrentHashMap
```

## 6. 消息推送流程
```
业务调用 PushHelper.sendMessage() → PushDTO → RedisUtils.publish(MESSAGE_TOPIC)
    → MessageTopicListener 订阅 → 本节点 WebSocketSessionManager.sendMessage()
        → USER_TOKEN_SESSIONS[userId] → WebSocketSession.sendMessage()
```

## 7. 依赖
- Spring WebSocket (spring-boot-starter-websocket, 排除 Tomcat)
- ruoyi-common-satoken（LoginHelper 获取用户信息）
- ruoyi-common-redis（Redis Pub/Sub 消息通道）
- ruoyi-common-push（MessageWebSocketConfiguration）
