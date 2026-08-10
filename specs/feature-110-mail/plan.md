# feature-110-mail 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-110-mail | 最后更新：2026-08-10

## 1. 技术上下文
- Jakarta Mail API + Angus Mail (参考实现), Hutool 5.8.47 JakartaMail 封装
- 配置前缀: mail, 条件启用: mail.enabled=true, 默认关闭

## 2. 宪法合规
| 原则 | 状态 |
|------|------|
| 插件化设计 | ✅ 独立模块 ruoyi-common-mail，条件启用 |
| 分布式就绪 | ✅ 邮件服务本身无状态，水平扩展无影响 |

## 3. 模块结构
- ruoyi-common/ruoyi-common-mail: 3 个文件
  - config/MailConfig.java — @ConditionalOnProperty(mail.enabled=true)，创建 MailAccount Bean
  - config/properties/MailProperties.java — @ConfigurationProperties(prefix="mail")，SMTP 配置属性
  - core/MailBuilder.java — 流式邮件构建器（409 行），支持 to/cc/bcc/subject/text/html/files/images/send

## 4. MailBuilder 设计
```
MailBuilder.of()                         // 静态工厂，使用全局 MailAccount Bean
    .to("a@x.com")                       // 收件人（逗号/分号分隔）
    .cc("b@x.com")                       // 抄送
    .bcc("c@x.com")                      // 密送
    .subject("标题")                      // 邮件标题（必填）
    .text("纯文本内容")                    // 文本正文（isHtml=false）
    .html("<h1>HTML内容</h1>")            // HTML 正文（isHtml=true）
    .image("cid1", inputStream)           // 内嵌图片（cid 引用）
    .files(new File("附件.pdf"))          // 附件文件
    .send()                               // 发送 → 返回 message-id
```

MailConfig → MailProperties.toMailAccount() → MailAccount (Hutool) → JakartaMail (Hutool) → Jakarta Mail 底层发送

## 5. 接口契约
- Demo: MailSendController (/demo/mail):
  - GET /demo/mail/sendSimpleMessage?to=&subject=&text= — 纯文本邮件
  - GET /demo/mail/sendMessageWithAttachment?to=&subject=&text= — 单附件邮件（附件路径硬编码，禁止前端传递）
  - GET /demo/mail/sendMessageWithAttachments?to=&subject=&text= — 多附件邮件

## 6. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-mail/pom.xml | 依赖 jakarta.mail-api, angus-mail, ruoyi-common-core |
| ruoyi-common-mail/.../config/MailConfig.java | 条件启用，创建 MailAccount Bean |
| ruoyi-common-mail/.../config/properties/MailProperties.java | mail.* 配置属性（12 个字段 + toMailAccount()） |
| ruoyi-common-mail/.../core/MailBuilder.java | 流式邮件构建器（409 行，final class） |
| ruoyi-modules/ruoyi-demo/.../controller/MailSendController.java | Demo: 纯文本/附件/多附件发送 |
