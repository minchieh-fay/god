---
name: add-mail
description: 在 sample 项目中添加邮件告警系统，支持 SMTP 配置和多收件人。当需要实现邮件通知或告警功能时使用。
---

## [Context]
为 sample 项目（Go + Gin + Clean Architecture）添加邮件告警功能。
当系统发生异常时，自动通过 SMTP 发送告警邮件到配置的收件人列表。
涉及文件：`~/pkg/mailer/mailer.go`（新建）、`~/config/config.go`（新增邮件配置项）、`~/internal/service/alert_service.go`（新建）。

## [Blueprint]
**方案**：新建 `pkg/mailer` 包封装 SMTP 发送逻辑，使用 Go 标准库 `net/smtp`，不引入第三方库。
告警触发点在 `service` 层，通过调用 `mailer.Send()` 发送邮件。SMTP 账号密码从配置文件读取，不硬编码。

**3 个易错点**：
1. `net/smtp` 的 `SendMail` 函数要求收件人为 `[]string`，单个收件人也需包装为切片，否则编译报错
2. SMTP 连接在离线环境下超时默认值较长，需显式设置 `net.DialTimeout` 避免告警阻塞主流程
3. 邮件正文需设置 `Content-Type: text/plain; charset=UTF-8` header，否则中文内容乱码

## [Implementation Steps]

1. **新增邮件配置项**
   - 文件：`~/config/config.go` 的 `Config` 结构体
   - 操作：在结构体中追加 `Mail MailConfig` 字段，并新增如下结构体：
   ```go
   // MailConfig 邮件告警配置
   type MailConfig struct {
       SMTPHost   string   // SMTP 服务器地址
       SMTPPort   int      // SMTP 端口，通常为 465 或 587
       Username   string   // 发件人邮箱账号
       Password   string   // 发件人邮箱密码（从配置文件读取，禁止硬编码）
       Recipients []string // 收件人列表
   }
   ```

2. **新建 mailer 包**
   - 文件：`~/pkg/mailer/mailer.go`（新建）
   - 操作：实现 `Send(subject, body string) error` 函数，使用 `net/smtp.SendMail` 发送邮件。
     函数内部从全局 config 读取 SMTP 配置，设置超时为 5 秒，正文 header 指定 `text/plain; charset=UTF-8`。

3. **新建告警 service**
   - 文件：`~/internal/service/alert_service.go`（新建）
   - 操作：实现 `AlertService.SendErrorAlert(err error, context string) error`，
     组装邮件标题（`[告警] 系统异常`）和正文（错误信息 + 时间戳），调用 `mailer.Send()`。

4. **在异常捕获处接入告警**
   - 文件：`~/internal/handler/middleware.go` 的 `RecoveryMiddleware` 函数
   - 操作：在 panic recover 逻辑末尾，调用 `alertService.SendErrorAlert(err, "panic recovered")` 发送告警。
     调用需异步（使用 `go` 关键字），避免阻塞请求响应。
