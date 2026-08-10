# 消息推送功能规格 (spec.md)

> 模块：feature-118-push | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
统一 SSE 和 WebSocket 消息推送通道，提供消息盒子功能，支持系统通知、工作流消息等实时推送。

### 1.2 范围
- ✅ SSE 服务端推送（默认通道）
- ✅ WebSocket 实时通信
- ✅ 消息盒子查询
- ✅ 消息持久化存储
- ✅ PushHelper 统一推送接口

## 2. 用户故事
- 作为**用户**，我可以实时收到系统通知和工作流消息
- 作为**管理员**，发布通知公告后在线用户立即收到推送

## 3. 功能需求
- FR-118-001: 系统 MUST 支持 SSE 连接建立与关闭
- FR-118-002: 系统 MUST 支持 WebSocket 连接（Token 鉴权）
- FR-118-003: 系统 MUST 支持消息持久化到 sys_message 表
- FR-118-004: 系统 MUST 支持查询当前用户消息盒子

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| SysMessage | 消息主表（messageId, category, type, title, content, sendUserIds） |

## 5. 验收场景
- Given 用户已登录
- When 管理员发布通知公告
- Then 用户浏览器实时收到 SSE 推送通知

## 6. 依赖
- ruoyi-common-push（核心推送）
- ruoyi-common-push SseController（SSE 端点）
- MessageWebSocketConfiguration（WebSocket 端点）
