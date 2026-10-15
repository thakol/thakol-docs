# Email delivery visibility

Open **Dashboard → Email Delivery** with an active workspace owner, administrator or member
account. This is a Thakol workspace session, separate from your application's OIDC user session.

Choose 7, 30 or 90 days, optionally filter an exact recipient or event, and use Next/Previous
for older records. Each row shows time, recipient, event, subject and diagnostic details.

- **Send:** accepted by the email service for sending.
- **Delivery:** accepted by the recipient's mail server; not proof of inbox placement or opening.
- **DeliveryDelay:** delivery is delayed; check later events.
- **Bounce:** delivery failed, such as a nonexistent mailbox.
- **Complaint:** the recipient reported spam.
- **Reject:** the service rejected the message before delivery.

One email can produce several events. Counts represent recipient events, not sign-ins or
billing tokens. Email bodies are not included; known sign-in proof formats in subjects and
diagnostics are redacted. Workspace scope is derived from the session. Operators use a separate
administrator-host view; environment tags prevent cross-environment realm-name collisions.

The SES configuration set and both environment subscriptions are connected. Real Send and
Delivery events have been verified. Historical messages from before setup cannot be recovered
through this log. No event may mean SMTP failed before SES accepted the message; check sending
status or contact support. Always verify actual passwordless proof and the client callback as
separate acceptance steps.

See [OTP](./email-otp.md), [email links](./email-links.md) and the
[website guide](https://thakol.io/docs/email-delivery).
