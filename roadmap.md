# Validated roadmap — 2026-10-14

## Available

Password fallback, optional/required authenticator-app TOTP, external OIDC providers, lazy password migration, OIDC
client management, team/user administration, hosted branding, bot/lockout policies, data export,
session review/revocation/concurrency controls, scoped email delivery logs, and free-launch usage counters.

Email OTP and original-browser email links are shipped and independently opt-in. Signup requires
the selected method's explicit permission plus application self-registration; application users
are distinct from Thakol dashboard/platform accounts.

## Ordered work

Mandatory MFA now requires enrollment for existing and new users before the authorization code
is issued on password, OTP and link paths. Enabling or repairing it ends older application SSO
sessions once. The real Keycloak regression covers enrollment, valid/invalid TOTP, PKCE,
older SSO reset and optional/disabled restoration; first-client acceptance remains separate.

Email delivery logs are now deployed for workspaces and operators. SES subscriptions and the
sending-identity configuration set are connected, and real Send/Delivery records were verified
in both environments. Environment tags isolate attribution. Preproduction SMTP pairing was
repaired. See [delivery visibility](./email-delivery.md).

1. **First-client acceptance:** real receipt/setup, passwordless inbox delivery, application
   callback, token verification, usage, recovery, logout and data export.
2. **Reliable usage:** listener failures currently log without durable retry; free counters are
   not a paid-billing guarantee. Only successful LOGIN/REGISTER events count, not mail requests/previews.

Owner/operator work: replace routine root AWS access, prove isolated restore, define retention
and activate reachable-host pruning, and complete preview/legal review. Documentation is not completion evidence.

## Deferred

Passkeys, invite-only application users (different from workspace access approval), SMS, guided
SAML, IP geography and in-product conversational onboarding are not shipped. Coding-assistant
integration guides are available and do not imply an in-product AI onboarding engine.

Guides: [OTP](https://thakol.io/docs/email-otp), [links](https://thakol.io/docs/email-links),
[samples](https://github.com/thakol/thakol-samples), [AI agents](https://github.com/thakol/thakol-ai-agents).

## Managed social login and CAPTCHA

Use Thakol defaults, bring your own provider/CAPTCHA keys, or explicitly disable a method.
See [the configuration guide](./managed-auth.md) for availability, setup and identity scope.
