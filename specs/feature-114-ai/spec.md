# AI 模块功能规格 (spec.md)

> 模块：feature-114-ai | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
AI 模块将 Spring AI 2.0 + SnailAI 集成到 RuoYi-Vue-Plus 平台，提供统一的 AI 模型调用接口、MCP 协议支持和 AI 服务管理。

### 1.2 解决的问题
- 统一多 AI 模型调用接口（Spring AI 抽象层）
- MCP (Model Context Protocol) 服务端支持
- AI 用户注册与管理（SnailAI OpenAPI）

### 1.3 范围
- ✅ Spring AI 2.0 集成（多模型支持）
- ✅ MCP Server 端点
- ✅ SnailAI OpenAPI 对接
- ✅ AI 用户注册
- ❌ AI 模型训练

## 2. 用户故事
- 作为**开发人员**，我可以通过 Spring AI 统一接口调用不同 AI 模型
- 作为**管理员**，我可以注册 AI 用户以使用 AI 服务
- 作为**开发者**，我可以通过 MCP 协议暴露工具给 AI 调用

## 3. 功能需求
- FR-114-001: 系统 MUST 支持通过 SnailAI OpenAPI 注册 AI 用户
- FR-114-002: 系统 SHOULD 提供 MCP Server 端点（/mcp）
- FR-114-003: 系统 SHOULD 支持 SnailAI Agent 功能

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| SnailAiController | AI 用户注册接口 |
| SnailAiConfig | AI 配置（@EnableSnailAiAgent） |

## 5. 依赖
- Spring AI 2.0.0
- SnailAI 1.1.1
- ruoyi-extend/ruoyi-snailai-server（独立 AI 服务）
