# 邮件服务功能规格 (spec.md)

> 模块：feature-110-mail | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
提供通用邮件协议发送能力，基于 Jakarta Mail API + Hutool 邮件工具，支持 SMTP/SSL/STARTTLS 等标准邮件协议，支持发送文本邮件、HTML 邮件、带附件的邮件，通过 YAML 配置即可连接任意邮件服务商。

### 1.2 解决的问题
- 统一邮件发送接口，无需关心底层邮件协议细节
- Builder 模式流式 API，链式调用简洁易用
- 条件启用（mail.enabled=true），不配置则不加载

### 1.3 范围
- ✅ Jakarta Mail API 集成（通用 SMTP 协议）
- ✅ 文本邮件发送（text/plain）
- ✅ HTML 邮件发送（text/html）
- ✅ 附件邮件发送（单附件/多附件）
- ✅ 内嵌图片支持（cid 引用）
- ✅ 收件人/抄送/密送支持
- ✅ MailBuilder 流式构建器 API
- ❌ 邮件模板引擎（需自行集成 Thymeleaf 等）

## 2. 用户故事
- 作为**开发人员**，我可以用一行代码发送文本邮件：`MailBuilder.of().to("user@example.com").subject("测试").text("内容").send()`
- 作为**开发人员**，我可以发送带附件的 HTML 格式邮件
- 作为**运维人员**，我可以在 YAML 中配置邮件服务器地址和认证信息

## 3. 功能需求
- FR-110-001: 系统 MUST 支持 SMTP 标准邮件协议发送
- FR-110-002: 系统 MUST 支持文本和 HTML 格式邮件
- FR-110-003: 系统 SHOULD 支持邮件附件（单个/多个）
- FR-110-004: 系统 SHOULD 支持内嵌图片（HTML cid 引用）
- FR-110-005: 系统 SHOULD 支持收件人/抄送/密送
- FR-110-006: 系统 MUST 条件启用（mail.enabled=true）

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| MailBuilder | 邮件发送流式构建器（静态工厂 of() / of(MailAccount)） |
| MailAccount (Hutool) | 邮件账户配置（host/port/auth/user/pass/ssl 等） |
| MailProperties | 邮件配置属性（prefix="mail"），含 toMailAccount() 转换方法 |
| MailConfig | 条件启用配置类（@ConditionalOnProperty mail.enabled=true） |

## 5. 配置示例

```yaml
mail:
  enabled: true
  host: smtp.example.com
  port: 465
  auth: true
  user: your-email@example.com
  pass: your-password-or-auth-code
  from: "系统通知 <your-email@example.com>"
  sslEnable: true
  starttlsEnable: false
```

## 6. API 使用示例

```java
// 文本邮件
MailBuilder.of().to("user@example.com").subject("标题").text("内容").send();

// HTML 邮件
MailBuilder.of().to("user@example.com").subject("标题").html("<h1>内容</h1>").send();

// 带附件邮件
MailBuilder.of().to("user@example.com").subject("标题").text("内容")
    .files(new File("/path/to/file.pdf")).send();

// 自定义账户
MailBuilder.of(customAccount).to("user@example.com").subject("标题").text("内容").send();
```

## 7. 依赖
- Jakarta Mail API (jakarta.mail-api)
- Angus Mail (org.eclipse.angus:jakarta.mail, Jakarta Mail 参考实现)
- Hutool 5.8.47（MailAccount, JakartaMail 封装）
- ruoyi-common-core（SpringUtils）
