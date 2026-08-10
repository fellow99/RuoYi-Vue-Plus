# feature-117-mcp 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-117-mcp | 最后更新：2026-08-10

## 1. 技术上下文
- Spring AI 2.0.0 MCP Server, transport=STREAMABLE, 端点=/mcp

## 2. 模块结构
- ruoyi-common-mcp: McpAutoConfiguration, McpClientTemplate, McpResourceReadResult, McpToolCallResult
- 配置: spring.ai.mcp.server.enabled=true, spring.ai.mcp.server.transport=STREAMABLE
- Demo: McpDemoController (/demo/mcp), McpDemoServerTool, McpDemoClientHandlers

## 3. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-mcp/.../McpAutoConfiguration.java | MCP 自动配置 |
| ruoyi-common-mcp/.../McpClientTemplate.java | MCP 客户端模板 |
| ruoyi-modules/ruoyi-demo/.../McpDemoController.java | Demo 控制器 |
| ruoyi-modules/ruoyi-demo/.../McpDemoServerTool.java | Demo 工具注册 |
