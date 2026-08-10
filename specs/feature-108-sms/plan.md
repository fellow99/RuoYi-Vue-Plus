# feature-108-sms 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-108-sms | 最后更新：2026-08-10

## 1. 技术上下文
- SMS4J 3.3.5 (sms4j-spring-boot-starter), JDK 21, Spring Boot 4.1
- SMS4J 自动配置通过 spring.factories 加载，框架提供自定义 SmsDao 实现

## 2. 宪法合规
| 原则 | 状态 |
|------|------|
| 插件化设计 | ✅ 独立模块 ruoyi-common-sms，按需引入 |
| 分布式就绪 | ✅ PlusSmsDao 基于 RedisUtils，支持集群共享缓存 |

## 3. 模块结构
- ruoyi-common/ruoyi-common-sms: 3 个文件
  - config/SmsAutoConfiguration.java — 注册 PlusSmsDao 和 SmsExceptionHandler
  - core/dao/PlusSmsDao.java — 基于 RedisUtils 的 SmsDao 缓存实现（短信重试/拦截缓存）
  - handler/SmsExceptionHandler.java — @RestControllerAdvice 全局捕获 SmsBlendException

## 4. 接口契约
- Demo: SmsController (/demo/sms):
  - GET /demo/sms/sendAliyun?phones=&templateId= — 使用 config1 发送阿里云短信
  - GET /demo/sms/sendTencent?phones=&templateId= — 使用 config2 发送腾讯云短信
  - GET /demo/sms/addBlacklist?phone= — 添加黑名单
  - GET /demo/sms/removeBlacklist?phone= — 移除黑名单

## 5. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-sms/pom.xml | 依赖 sms4j-spring-boot-starter、ruoyi-common-redis |
| ruoyi-common-sms/.../config/SmsAutoConfiguration.java | 注册 PlusSmsDao(SmsDao)、SmsExceptionHandler |
| ruoyi-common-sms/.../core/dao/PlusSmsDao.java | Redis 缓存实现（set/get/remove/clean），前缀 GlobalConstants.GLOBAL_REDIS_KEY |
| ruoyi-common-sms/.../handler/SmsExceptionHandler.java | 全局异常处理，SmsBlendException → R.fail(500) |
| ruoyi-modules/ruoyi-demo/.../controller/SmsController.java | Demo: sendAliyun, sendTencent, addBlacklist, removeBlacklist |
