# feature-106-idempotent 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-106-idempotent | 最后更新：2026-08-10

## 1. 技术上下文
- 参考美团 GTIS 防重系统设计，简化实现
- 幂等 Key 格式：{keyPrefix}global:repeat_submit:{requestURI}:{MD5(token + ":" + JSON(params))}
- Redis 原子操作：RBucket.setIfAbsent(key, "", Duration) → SET NX EX
- 生命周期：@Before 判断 → 成功/失败处理 → 时间窗口过期自动释放
- 参数过滤：排除 MultipartFile / HttpServletRequest / HttpServletResponse / BindingResult

## 2. 模块结构
- ruoyi-common-redis: 3 个文件（annotation + aspectj + config）
- RepeatSubmitAspect：@Before 拦截检查 + @AfterReturning/@AfterThrowing 清理
- IdempotentConfig：@AutoConfiguration(after = RedisConfiguration.class)
- 无需独立模块，复用 ruoyi-common-redis 基础设施

## 3. 注解参数

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| interval | int | 5000 | 防重时间窗口(ms) |
| timeUnit | TimeUnit | MILLISECONDS | 时间单位 |
| message | String | "{repeat.submit.message}" | 国际化提示消息Key |

## 4. 控制器清单（使用方）

@RepeatSubmit 广泛用于所有新增/修改关键操作，已使用超过 26 处：

| 典型模块 | 典型接口 | 保护方式 |
|----------|----------|----------|
| SysUserController | /system/user (PUT) | @RepeatSubmit() |
| SysRoleController | /system/role (PUT) | @RepeatSubmit() |
| SysMenuController | /system/menu (PUT) | @RepeatSubmit() |
| SysOssConfigController | /resource/oss/config (POST/PUT) | @RepeatSubmit() |
| FlwDefinitionController | /workflow/definition (POST/PUT) | @RepeatSubmit() |
| TestDemoController | /demo/demo (POST/PUT) | @RepeatSubmit() |

## 5. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-redis/annotation/RepeatSubmit.java | @RepeatSubmit 注解定义 |
| ruoyi-common-redis/aspectj/RepeatSubmitAspect.java | 幂等切面（@Before/@AfterReturning/@AfterThrowing） |
| ruoyi-common-redis/config/IdempotentConfig.java | 自动配置（注册切面 bean） |
| ruoyi-common-redis/utils/RedisUtils.java | setObjectIfAbsent 原子操作 |
| ruoyi-common-core/constant/GlobalConstants.java | REPEAT_SUBMIT_KEY = "global:repeat_submit:" |
| ruoyi-admin/resources/i18n/messages.properties | repeat.submit.message 国际化 |
