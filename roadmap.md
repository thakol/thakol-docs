# Validated roadmap — 2026-10-14

## Available

Password fallback, enrolled-user TOTP, external OIDC providers, lazy password migration, OIDC
client management, team/user administration, hosted branding, bot/lockout policies, data export,
session review/revocation/concurrency controls, and free-launch usage counters.

Email OTP and original-browser email links are shipped and independently opt-in. Signup requires
the selected method's explicit permission plus application self-registration; application users
are distinct from Thakol dashboard/platform accounts.

## Ordered work

1. **Mandatory MFA:** existing unenrolled application users can complete primary login despite
   require-every-user. Enrolled users are challenged. Fix enrollment and enclosing flow coverage,
   then verify password, OTP and link paths before claiming universally enforced MFA.
2. **Email visibility:** webhook/model exist; SES set/destination/topic exist, but a subscription
   and sending-identity configuration-set binding are missing. Implement scoped logs and prove delivery.
3. **First-client acceptance:** real receipt/setup, passwordless inbox delivery, application
   callback, token verification, usage, recovery, logout and data export.
4. **Reliable usage:** listener failures currently log without durable retry; free counters are
   not a paid-billing guarantee. Only successful LOGIN/REGISTER events count, not mail requests/previews.

Owner/operator work: replace routine root AWS access, prove isolated restore, define retention
and activate reachable-host pruning, and complete preview/legal review. Documentation is not completion evidence.

## Deferred

Passkeys, invite-only application users (different from workspace access approval), SMS, guided
SAML, IP geography and in-product conversational onboarding are not shipped. Coding-assistant
integration guides are available and do not imply an in-product AI onboarding engine.

Guides: [OTP](https://thakol.io/docs/email-otp), [links](https://thakol.io/docs/email-links),
[samples](https://github.com/thakol/thakol-samples), [AI agents](https://github.com/thakol/thakol-ai-agents).
