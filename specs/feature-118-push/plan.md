# feature-118-push 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-118-push | 最后更新：2026-08-10

## 1. 技术上下文
- Spring SSE + Spring WebSocket, 消息路径: /resource/message

## 2. 模块结构
- ruoyi-common-push:
  - controller/SseController.java — SSE 连接管理
  - config/MessageSseConfiguration.java + MessageWebSocketConfiguration.java — 通道配置
  - core/SseEmitterSessionManager.java + WebSocketSessionManager.java — 会话管理
  - core/PushSessionManager.java — 统一会话抽象
  - handler/PlusWebSocketHandler.java — WebSocket 处理器
  - helper/PushHelper.java — 统一推送入口
  - listener/MessageTopicListener.java — Redis 消息队列监听
  - properties/MessageProperties.java — 配置属性

## 3. 消息推送架构
```
业务触发 → PushHelper.sendMessage()
    ├── SSE（默认）→ SseEmitterSessionManager → 浏览器
    └── WebSocket → WebSocketSessionManager → 浏览器
         ↓
    SysMessageService → sys_message 表持久化
```

## 4. 文件清单

| 文件 | 用途 |
|------|------|
| SseController.java | GET /resource/message SSE连接 |
| MessageWebSocketConfiguration.java | WebSocket 端点注册 |
| PushHelper.java | 统一推送工具类 |
| SysMessageController.java | 消息盒子查询 (/resource/message/box) |
