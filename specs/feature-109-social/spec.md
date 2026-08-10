# 社交登录功能规格 (spec.md)

> 模块：feature-109-social | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
集成 JustAuth 第三方登录组件，支持微信、钉钉、码云（Gitee）、GitHub、QQ、微博、支付宝、百度、抖音等二十余种社交平台的三方认证登录，通过 YAML 配置即可启用任一平台。

### 1.2 解决的问题
- 多种社交平台 OAuth 协议差异大，接入成本高
- 需要统一的授权码缓存管理（Redis 分布式）
- 社交账号与系统用户绑定关系管理

### 1.3 范围
- ✅ JustAuth 3.0.1 集成（二十余种社交平台）
- ✅ Redis 授权状态缓存（AuthRedisStateCache，支持分布式）
- ✅ YAML 配置驱动（justauth.type.{platform}）
- ✅ 社交账号绑定查询（SysSocialController）
- ❌ 社交登录前端页面（前端实现）

## 2. 用户故事
- 作为**普通用户**，我可以用微信/GitHub/Gitee 账号快速登录系统
- 作为**管理员**，我可以在配置文件中开关不同社交平台
- 作为**用户**，我可以查看已绑定的社交账号列表

## 3. 功能需求
- FR-109-001: 系统 MUST 支持 JustAuth 多平台三方登录（微信/钉钉/码云/GitHub 等）
- FR-109-002: 系统 MUST 支持 Redis 分布式授权状态缓存
- FR-109-003: 系统 MUST 支持通过 YAML 配置各平台 clientId/clientSecret/redirectUri
- FR-109-004: 系统 SHOULD 支持社交账号绑定关系查询（/system/social/list）

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| SocialProperties | justauth.type 配置映射（Map<String, SocialLoginConfigProperties>） |
| SocialLoginConfigProperties | 单平台配置：clientId, clientSecret, redirectUri, scopes 等 |
| AuthRedisStateCache | JustAuth AuthStateCache Redis 实现（3 分钟默认过期） |
| SysSocial | 社交账号绑定关系实体（sys_social 表） |

## 5. 支持的平台（SocialUtils 中硬编码）

| 平台 | source 值 | AuthRequest 类 |
|------|-----------|---------------|
| 钉钉 | dingtalk | AuthDingTalkV2Request |
| 百度 | baidu | AuthBaiduRequest |
| GitHub | github | AuthGithubRequest |
| 码云 | gitee | AuthGiteeRequest |
| 微博 | weibo | AuthWeiboRequest |
| Coding | coding | AuthCodingRequest |
| 开源中国 | oschina | AuthOschinaRequest |
| 支付宝 | alipay_wallet | AuthAlipayRequest |
| QQ | qq | AuthQqRequest |
| 微信开放平台 | wechat_open | AuthWeChatOpenRequest |
| 淘宝 | taobao | AuthTaobaoRequest |
| 抖音 | douyin | AuthDouyinRequest |
| LinkedIn | linkedin | AuthLinkedinRequest |
| 微软 | microsoft | AuthMicrosoftRequest (支持 tenantId) |
| 人人网 | renren | AuthRenrenRequest |
| Stack Overflow | stack_overflow | AuthStackOverflowRequest |
| 华为 | huawei | AuthHuaweiV3Request |
| 企业微信 | wechat_enterprise | AuthWeChatEnterpriseQrcodeV2Request |
| GitLab | gitlab | AuthGitlabRequest |
| 微信公众号 | wechat_mp | AuthWeChatMpRequest |
| 阿里云 | aliyun | AuthAliyunRequest |
| MaxKey | maxkey | AuthMaxKeyRequest |
| TopIAM | topiam | AuthTopIamRequest |
| Gitea | gitea | AuthGiteaRequest |

## 6. 依赖
- JustAuth 3.0.1 (io.github.windtool)
- ruoyi-common-redis（AuthRedisStateCache）
- ruoyi-system（SysSocial entity/mapper/service）
