# feature-102-demo 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-102-demo | 最后更新：2026-08-10

## 1. 技术上下文
- Spring Boot 4.1, MyBatis-Plus 3.5.10.1, Redisson, Lock4j, Sms4j, Easy-Es 3.0.2, Spring AI 2.0.0 MCP, Mica-MQTT 2.6.8
- 无独立启动类，作为库模块挂载到 ruoyi-admin (DromaraApplication)
- 包路径：org.dromara.demo，自动组件扫描生效

## 2. 模块结构
- ruoyi-modules/ruoyi-demo: 20 个 Controller + 5 个 Service + 3 个 Mapper + 1 个 MCP 工具
- 资源配置: resources/mapper/demo/*.xml, resources/excel/*.xlsx
- 无独立 application.yml，继承 ruoyi-admin 配置

## 3. 控制器清单

| Controller | 路径 | 演示功能 |
|------------|------|----------|
| TestDemoController | /demo/demo | CRUD 示例（分页/新增/修改/删除/导出） |
| TestTreeController | /demo/tree | 树形结构 CRUD |
| TestBatchController | /demo/batch | 批量插入与更新 |
| TestExcelController | /demo/excel | Excel 导入导出（单sheet/多sheet/合并单元格） |
| TestI18nController | /demo/i18n | 国际化动态切换 |
| TestSensitiveController | /demo/sensitive | 数据脱敏（身份证/手机号/邮箱等） |
| TestEncryptController | /demo/encrypt | API 传输加密 + 数据库字段加解密 |
| SaTokenTestController | /demo/saTokenDoc | Sa-Token 权限认证示例 |
| Swagger3DemoController | /swagger/demo | SpringDoc 接口文档示例 |
| SmsController | /demo/sms | 短信发送（阿里云/腾讯云等） |
| MailSendController | /demo/mail | 邮件发送（文本/HTML/附件） |
| RedisCacheController | /demo/cache | Redis 缓存（Spring Cache 扩展注解） |
| RedisLockController | /demo/redisLock | 分布式锁（Lock4j 注解） |
| RedisPubSubController | /demo/redis/pubsub | Redis 发布订阅 |
| RedisRateLimiterController | /demo/rateLimiter | 分布式限流（RateLimiter 注解） |
| EsCrudController | /es | Elasticsearch 文档 CRUD |
| WebSocketController | /demo/websocket | WebSocket 消息推送 |
| MqttController | /demo/mqtt | MQTT 消息订阅与发布 |
| McpDemoController | /demo/mcp | MCP 工具注册与客户端调用 |
| PriorityQueueController | /demo/queue/priority | Redis 优先级队列 |

## 4. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-modules/ruoyi-demo/pom.xml | 模块依赖（18个 common 模块） |
| ruoyi-modules/ruoyi-demo/controller/TestDemoController.java | CRUD 示例 |
| ruoyi-modules/ruoyi-demo/controller/TestTreeController.java | 树形示例 |
| ruoyi-modules/ruoyi-demo/controller/TestBatchController.java | 批量示例 |
| ruoyi-modules/ruoyi-demo/controller/TestExcelController.java | Excel 示例 |
| ruoyi-modules/ruoyi-demo/controller/TestI18nController.java | 国际化示例 |
| ruoyi-modules/ruoyi-demo/controller/TestSensitiveController.java | 脱敏示例 |
| ruoyi-modules/ruoyi-demo/controller/TestEncryptController.java | 加密示例 |
| ruoyi-modules/ruoyi-demo/controller/SaTokenTestController.java | 权限认证示例 |
| ruoyi-modules/ruoyi-demo/controller/Swagger3DemoController.java | 接口文档示例 |
| ruoyi-modules/ruoyi-demo/controller/SmsController.java | 短信示例 |
| ruoyi-modules/ruoyi-demo/controller/MailSendController.java | 邮件示例 |
| ruoyi-modules/ruoyi-demo/controller/RedisCacheController.java | 缓存示例 |
| ruoyi-modules/ruoyi-demo/controller/RedisLockController.java | 分布式锁示例 |
| ruoyi-modules/ruoyi-demo/controller/RedisPubSubController.java | 发布订阅示例 |
| ruoyi-modules/ruoyi-demo/controller/RedisRateLimiterController.java | 限流示例 |
| ruoyi-modules/ruoyi-demo/controller/EsCrudController.java | ES 示例 |
| ruoyi-modules/ruoyi-demo/controller/WebSocketController.java | WebSocket 示例 |
| ruoyi-modules/ruoyi-demo/controller/MqttController.java | MQTT 示例 |
| ruoyi-modules/ruoyi-demo/controller/McpDemoController.java | MCP 示例 |
| ruoyi-modules/ruoyi-demo/controller/queue/PriorityQueueController.java | 优先级队列示例 |
| ruoyi-modules/ruoyi-demo/mapper/TestDemoMapper.java | CRUD Mapper |
| ruoyi-modules/ruoyi-demo/mapper/TestTreeMapper.java | 树形 Mapper |
| ruoyi-modules/ruoyi-demo/mapper/TestDemoEncryptMapper.java | 加密表 Mapper |
| ruoyi-modules/ruoyi-demo/service/ITestDemoService.java | CRUD 服务接口 |
| ruoyi-modules/ruoyi-demo/service/ITestTreeService.java | 树形服务接口 |
| ruoyi-modules/ruoyi-demo/mcp/McpDemoServerTool.java | MCP 服务端工具 |
| ruoyi-modules/ruoyi-demo/mcp/McpDemoClientService.java | MCP 客户端服务 |
| ruoyi-modules/ruoyi-demo/mcp/McpDemoClientHandlers.java | MCP 客户端处理器 |
| ruoyi-modules/ruoyi-demo/listener/ExportDemoListener.java | Excel 导出监听器 |
| ruoyi-modules/ruoyi-demo/esmapper/DocumentMapper.java | Easy-Es Mapper |
