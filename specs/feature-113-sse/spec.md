# SSE 服务端事件推送功能规格 (spec.md)

> 模块：feature-113-sse | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
基于 Spring SSE (Server-Sent Events) 提供 Token 鉴权的服务端单向推送能力，支持分布式集群环境下的会话同步，通过 Redis Pub/Sub 实现跨节点消息推送，支持心跳检测、多终端同时在线等功能。SSE 是消息推送的默认通道。

### 1.2 解决的问题
- 需要轻量级的服务端推送通道（相比 WebSocket 更简单，浏览器原生支持）
- Token 鉴权的 SSE 连接建立和会话管理
- 集群环境下 SSE 消息需要跨节点分发

### 1.3 范围
- ✅ SSE Token 鉴权（通过 Sa-Token StpUtil.getTokenValue()）
- ✅ 分布式会话同步（Redis Pub/Sub 跨节点消息推送）
- ✅ 多终端支持（userId → token → SseEmitter 二级映射）
- ✅ 心跳检测（定时发送 SSE 心跳事件维持连接）
- ✅ 定时会话监控（清理失效 SseEmitter）
- ✅ 条件启用（message.transport=sse，默认通道）
- ❌ SSE 消息体加密（使用框架统一接口加密即可）

## 2. 用户故事
- 作为**用户**，登录系统后自动建立 SSE 连接，实时接收通知消息
- 作为**管理员**，发布通知后所有在线用户通过 SSE 立即收到推送
- 作为**前端开发**，SSE 使用标准 EventSource API，无需额外依赖

## 3. 功能需求
- FR-113-001: 系统 MUST 支持 SSE 端点连接（Token 鉴权）
- FR-113-002: 系统 MUST 支持 Redis Pub/Sub 分布式消息同步
- FR-113-003: 系统 MUST 支持多终端同时在线（同用户多 SseEmitter）
- FR-113-004: 系统 MUST 支持 SSE 连接关闭接口
- FR-113-005: 系统 MUST 支持定时清理失效 SseEmitter
- FR-113-006: SSE MUST 作为消息推送默认通道（message.transport=sse）

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| SseController | SSE 连接端点（GET /resource/message, text/event-stream） |
| SseEmitterSessionManager | SSE 会话管理器（USER_TOKEN_EMITTERS: userId → token → SseEmitter） |
| SseEmitter (Spring) | Spring MVC SSE 发射器（超时、完成、错误回调） |
| MessageSseConfiguration | SSE 自动装配（@ConditionalOnMessageTransport("sse")） |
| PushSessionManager | 统一推送会话接口（SseEmitterSessionManager 实现） |

## 5. 连接流程
```
前端 EventSource("/resource/message") → SseController.connect()
    → StpUtil.getTokenValue() 获取 Token
    → LoginHelper.getUserId() 获取用户 ID
    → response.setContentType("text/event-stream")
    → sessionManager.connect(userId, token)
        → 创建 SseEmitter（配置超时）
        → onCompletion → disconnect(userId, token)
        → onError → disconnect(userId, token)
        → onTimeout → disconnect(userId, token)
        → 存入 ConcurrentHashMap
```

## 6. 消息推送流程
```
业务调用 PushHelper.sendMessage() → PushDTO → RedisUtils.publish(MESSAGE_TOPIC)
    → MessageTopicListener 订阅 → 本节点 SseEmitterSessionManager.sendMessage()
        → USER_TOKEN_EMITTERS[userId] → SseEmitter.send(event)
```

## 7. 与 WebSocket 的关系
SSE 和 WebSocket 是两种独立的推送通道，通过 message.transport 配置切换，不共存：
- `message.transport=sse` — 启用 SSE 通道（默认）
- `message.transport=websocket` — 启用 WebSocket 通道

## 8. 依赖
- Spring Web MVC（SseEmitter）
- ruoyi-common-satoken（LoginHelper、StpUtil）
- ruoyi-common-redis（Redis Pub/Sub）
- ruoyi-common-push（MessageSseConfiguration、SseEmitterSessionManager）
