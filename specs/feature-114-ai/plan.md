# feature-114-ai 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-114-ai | 最后更新：2026-08-10

## 1. 技术上下文
- Java 21, Spring Boot 4.1, Spring AI 2.0.0, SnailAI 1.1.1
- 模块位置：ruoyi-modules/ruoyi-ai, ruoyi-common/ruoyi-common-ai, ruoyi-extend/ruoyi-snailai-server

## 2. 宪法合规
| 原则 | 状态 |
|------|------|
| 插件化设计 | ✅ 独立模块，条件启用(snail-ai.enabled) |
| 分布式就绪 | ✅ 独立 SnailAI Server 部署 |

## 3. 接口契约
- SnailAiController: POST /snail-ai/user/register
- MCP Server: /mcp (Spring AI STREAMABLE transport)
- SnailAI OpenAPI: 内置 SnailAiOpenApiService

## 4. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-modules/ruoyi-ai/.../SnailAiController.java | AI 用户注册 |
| ruoyi-common/ruoyi-common-ai/.../SnailAiConfig.java | AI 配置 |
| ruoyi-extend/ruoyi-snailai-server/ | SnailAI 独立服务 |
