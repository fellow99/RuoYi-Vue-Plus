# feature-112-websocket 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-112-websocket | 最后更新：2026-08-10

## 1. 技术上下文
- Spring WebSocket, JDK 21, Spring Boot 4.1, Jetty 12 (Netty)
- 配置: message.enabled=true(matchIfMissing), message.transport=websocket
- 依赖: spring-boot-starter-websocket（排除 Tomcat）

## 2. 宪法合规
| 原则 | 状态 |
|------|------|
| 插件化设计 | ✅ ruoyi-common-push 独立模块，条件启用 transport=websocket |
| 分布式就绪 | ✅ Redis Pub/Sub 跨节点消息同步 + ConcurrentHashMap 会话管理 |

## 3. 模块结构
- ruoyi-common/ruoyi-common-push:
  - handler/PlusWebSocketHandler.java — WebSocket 生命周期处理器（连接/心跳/消息/断开/异常）
  - interceptor/PlusWebSocketInterceptor.java — 握手拦截器（Token 鉴权）
  - core/WebSocketSessionManager.java — 会话管理（userId→token→WebSocketSession 二级 Map）
  - config/MessageWebSocketConfiguration.java — WebSocket 自动装配（@EnableWebSocket, 条件启用）
  - config/MessageAutoConfiguration.java — 公共配置（MessageProperties, ScheduledExecutorService）
  - core/PushSessionManager.java — 统一推送接口（subscribeMessage/sendMessage/publishMessage/publishAll）
  - listener/MessageTopicListener.java — Redis 消息订阅监听器
  - dto/PushDTO.java — 推送消息 DTO
  - constant/MessageConstants.java — 消息常量（MESSAGE_TOPIC, PING, PONG, KICKED 等）

## 4. PlusWebSocketHandler 生命周期

| 事件 | 处理逻辑 |
|------|----------|
| afterConnectionEstablished | 校验 LoginUser/Token → webSocketSessionManager.connect(userId, token, session) |
| handleTextMessage | PING → 回复 PONG; 普通消息 → 发布到 Redis 主题 |
| handlePongMessage | 维持心跳，发送 PongMessage |
| handleTransportError | 记录异常日志 → disconnect(userId, token, SERVER_ERROR) |
| afterConnectionClosed | disconnect(userId, token) → 清理会话 |

## 5. WebSocketSessionManager 关键设计
```
USER_TOKEN_SESSIONS: ConcurrentHashMap<Long, Map<String, WebSocketSession>>
  userId → { token1 → session1, token2 → session2 }  // 多终端支持

定时监控（60s 间隔）:
  sessionMonitor() → 遍历所有会话
    → 移除已关闭的 session
    → 移除无有效会话的 userId

消息发送:
  sendMessage(userId, payload) → 遍历 userId 的所有 token 会话
    → 已关闭的自动移除 → 调用 WebSocketSession.sendMessage()
```

## 6. 接口契约
- WebSocket 连接路径: message.path（默认 /resource/message）
- 路径通过 MessageWebSocketConfiguration.webSocketConfigurer() 注册
- 跨域: message.allowedOrigins 配置

## 7. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-push/pom.xml | 依赖 spring-boot-starter-websocket（排除 Tomcat） |
| ruoyi-common-push/.../handler/PlusWebSocketHandler.java | 160 行，WebSocket 生命周期处理器 |
| ruoyi-common-push/.../interceptor/PlusWebSocketInterceptor.java | 44 行，握手 Token 鉴权 |
| ruoyi-common-push/.../core/WebSocketSessionManager.java | 292 行，会话管理器 |
| ruoyi-common-push/.../config/MessageWebSocketConfiguration.java | 79 行，WebSocket 自动装配 |
| ruoyi-common-push/.../config/MessageAutoConfiguration.java | 17 行，公共配置（MessageProperties） |
| ruoyi-common-push/.../core/PushSessionManager.java | 统一推送会话接口 |
| ruoyi-common-push/.../listener/MessageTopicListener.java | Redis 主题消息监听 |
| ruoyi-common-push/.../dto/PushDTO.java | 推送消息 DTO |
| ruoyi-common-push/.../constant/MessageConstants.java | 消息常量定义 |
