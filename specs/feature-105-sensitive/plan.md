# feature-105-sensitive 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-105-sensitive | 最后更新：2026-08-10

## 1. 技术上下文
- 脱敏引擎：Jackson 序列化后置处理，基于 ruoyi-common-json 的 JsonValueEnhancer 树遍历引擎
- JsonFieldProcessor SPI：3 阶段生命周期（supports → collect → prepare → process）
- SensitiveJsonFieldProcessor：@Order(100)，在 JsonValueEnhancer 遍历 POJO 时检查 @Sensitive 注解
- 条件脱敏：SensitiveService SPI → SysSensitiveServiceImpl（Sa-Token 角色/权限判断）
- 脱敏基类：Hutool DesensitizedUtil + 自定义 DesensitizedUtils

## 2. 模块结构
- ruoyi-common-sensitive: 5 个 Java 文件（annotation + config + core + handler）
- 依赖 ruoyi-common-json 的 JsonFieldProcessor SPI 接口
- 自动装配：SensitiveConfig (@AutoConfiguration) → 注册 SensitiveJsonFieldProcessor bean

## 3. 脱敏策略清单

| 策略常量 | 脱敏规则 | 示例 |
|----------|----------|------|
| ID_CARD | 保留前3后4，中间掩码 | 320\*\*\*\*\*\*\*\*\*\*0576 |
| PHONE | 保留前3后4 | 138\*\*\*\*1234 |
| ADDRESS | 保留前8位 | 北京市海淀区\*\*\*\*\*\*\*\* |
| EMAIL | 仅显示 @ 前首字符 | t\*\*\*@example.com |
| BANK_CARD | 保留前6后4 | 622202\*\*\*\*\*\*\*\*1234 |
| CHINESE_NAME | 保留首字 | 张\*\* |
| FIXED_PHONE | 保留前3后2 | 010\*\*\*\*\*89 |
| USER_ID | 固定掩码 | \*\*\*\*\*\*\*\*\*\*\*\*\*\* |
| PASSWORD | 固定掩码 | \*\*\*\*\*\*\*\*\*\*\*\*\*\* |
| IPV4 | 保留首段 | 192.\*.\*.\* |
| IPV6 | 保留首段 | fe80:\*:\*:\*:\*:\*:\*:\* |
| CAR_LICENSE | 保留前2 | 京A\*\*\*\*\* |
| FIRST_MASK | 仅显示首字符 | 张\*\*\*\*\*\*\* |
| STRING_MASK | 前4可见，中4掩码，后4可见 | hell\*\*\*\*orld |
| MASK_HIGH_SECURITY | 前2后2可见，中间全掩码 | ab\*\*\*\*\*\*\*\*\*\*\*\*yz |
| CLEAR | 清空为 "" | (空字符串) |
| CLEAR_TO_NULL | 清空为 null | null |

## 4. 控制器清单（使用方）

| Controller | 路径 | 用途 |
|------------|------|------|
| TestSensitiveController | /demo/sensitive | Demo 演示所有脱敏策略 |
| SysUserController | /system/user | 用户列表（手机/邮箱脱敏） |
| SysProfileController | /system/user/profile | 个人信息（单独VO避免脱敏） |

## 5. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-sensitive/annotation/Sensitive.java | @Sensitive 注解（strategy + roleKey + perms） |
| ruoyi-common-sensitive/core/SensitiveStrategy.java | 脱敏策略枚举（18种） |
| ruoyi-common-sensitive/core/SensitiveService.java | 脱敏决策 SPI 接口 |
| ruoyi-common-sensitive/config/SensitiveConfig.java | 自动配置，注册 SensitiveJsonFieldProcessor |
| ruoyi-common-sensitive/handler/SensitiveJsonFieldProcessor.java | Jackson 序列化时脱敏处理器 |
| ruoyi-common-core/utils/DesensitizedUtils.java | 自定义掩码工具（mask/maskHighSecurity） |
| ruoyi-common-json/enhance/JsonFieldProcessor.java | 字段处理器 SPI |
| ruoyi-common-json/enhance/JsonValueEnhancer.java | JSON 树遍历引擎 |
| ruoyi-common-json/enhance/JsonFieldContext.java | 字段上下文 |
| ruoyi-common-json/enhance/JsonEnhancementContext.java | 增强上下文 |
| ruoyi-common-json/config/JsonEnhancementConfig.java | JSON 增强自动配置 |
| ruoyi-common-web/advice/ResponseEnhancementAdvice.java | ResponseBodyAdvice 钩子 |
| ruoyi-system/service/impl/SysSensitiveServiceImpl.java | 默认 SensitiveService 实现 |
