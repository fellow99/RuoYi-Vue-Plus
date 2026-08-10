# MCP 协议功能规格 (spec.md)

> 模块：feature-117-mcp | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
实现 Model Context Protocol (MCP) 支持，通过 Spring AI MCP Server 暴露工具和资源，使 AI 模型能够调用后端服务。

### 1.2 范围
- ✅ Spring AI MCP Server（STREAMABLE transport）
- ✅ MCP 客户端模板（McpClientTemplate）
- ✅ Demo 模块演示

## 2. 功能需求
- FR-117-001: 系统 MUST 提供 MCP Server 端点（/mcp）
- FR-117-002: 系统 SHOULD 支持自定义 MCP 工具注册

## 3. 依赖
- Spring AI 2.0.0（MCP 支持）
- ruoyi-common-mcp（McpAutoConfiguration, McpClientTemplate）
