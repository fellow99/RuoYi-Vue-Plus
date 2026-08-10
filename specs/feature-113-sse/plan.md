# feature-113-sse 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-113-sse | 最后更新：2026-08-10

## 1. 技术上下文
- Spring SSE (SseEmitter), JDK 21, Spring Boot 4.1
- 配置: message.enabled=true(matchIfMissing), message.transport=sse（默认）
- 端点: message.path（默认 /resource/message）

## 2. 宪法合规
| 原则 | 状态 |
|------|------|
| 插件化设计 | ✅ ruoyi-common-push 独立模块，条件启用 transport=sse |
| 分布式就绪 | ✅ Redis Pub/Sub 跨节点消息同步 + ConcurrentHashMap 会话管理 |

## 3. 模块结构
- ruoyi-common/ruoyi-common-push:
  - controller/SseController.java — SSE 连接端点（GET /resource/message, text/event-stream）
  - core/SseEmitterSessionManager.java — SSE 会话管理器（ConcurrentHashMap + 心跳检测）
  - config/MessageSseConfiguration.java — SSE 自动装配（@ConditionalOnMessageTransport("sse")）
  - config/MessageAutoConfiguration.java — 公共配置（MessageProperties, ScheduledExecutorService）
  - core/PushSessionManager.java — 统一推送接口（subscribeMessage/sendMessage/publishMessage/publishAll）
  - listener/MessageTopicListener.java — Redis 消息订阅监听器
  - properties/MessageProperties.java — 推送公共配置

## 4. SseController 端点设计

| 端点 | 方法 | 说明 |
|------|------|------|
| ${message.path:/resource/message} | GET | 建立 SSE 连接（text/event-stream），返回 SseEmitter |
| ${message.path:/resource/message}/close | GET | 关闭当前用户 SSE 连接（@SaIgnore） |

SSE 响应头配置:
```java
response.setContentType(MediaType.TEXT_EVENT_STREAM_VALUE);  // text/event-stream
response.setHeader("Cache-Control", "no-cache");
response.setHeader("X-Accel-Buffering", "no");               // 禁止 Nginx 缓冲
```

## 5. SseEmitterSessionManager 关键设计
```
USER_TOKEN_EMITTERS: ConcurrentHashMap<Long, Map<String, SseEmitter>>
  userId → { token1 → emitter1, token2 → emitter2 }  // 多终端支持

connect(userId, token):
  → 创建 SseEmitter(timeout)
  → 注册 onCompletion/onError/onTimeout 回调 → disconnect
  → 发送初始连接成功事件
  → 存入 ConcurrentHashMap

定时监控（心跳检测）:
  sseMonitor() → 遍历所有 emitter
    → 已完成的移除
    → 存活 emitter 发送心跳事件

消息发送:
  sendMessage(userId, payload) → 遍历 userId 的所有 token emitter
    → 已完成的自动移除 → SseEmitter.send(SseEmitter.event().data(jsonPayload))
```

## 6. 消息推送统一架构
```
MessageAutoConfiguration (公共)
    ├── message.enabled=true (matchIfMissing)
    ├── MessageProperties 配置注册
    └── ScheduledExecutorService 线程池

MessageSseConfiguration (after=MessageAutoConfiguration)        ← transport=sse
    ├── SseEmitterSessionManager
    ├── MessageTopicListener (Redis 订阅)
    └── SseController

MessageWebSocketConfiguration (after=MessageAutoConfiguration)  ← transport=websocket
    ├── WebSocketSessionManager
    ├── PlusWebSocketHandler
    ├── PlusWebSocketInterceptor
    └── MessageTopicListener (Redis 订阅)
```

## 7. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-push/.../controller/SseController.java | 105 行，SSE 连接/关闭端点 |
| ruoyi-common-push/.../core/SseEmitterSessionManager.java | 309 行，SSE 会话管理器 |
| ruoyi-common-push/.../config/MessageSseConfiguration.java | 57 行，SSE 自动装配 |
| ruoyi-common-push/.../config/MessageAutoConfiguration.java | 17 行，公共配置入口 |
| ruoyi-common-push/.../core/PushSessionManager.java | 统一推送会话接口 |
| ruoyi-common-push/.../listener/MessageTopicListener.java | Redis 主题消息监听 |
| ruoyi-common-push/.../properties/MessageProperties.java | 推送配置属性（path/transport/heartbeat 等） |
| ruoyi-common-push/.../annotation/ConditionalOnMessageTransport.java | 条件注解实现 |
| ruoyi-common-push/.../condition/MessageTransportCondition.java | transport 条件判断 |
