# Thakol documentation

Add hosted sign-in and user management to your application with OpenID Connect (OIDC).

## Start here

1. [Quick start](https://thakol.io/docs/quickstart): request a workspace, register your callback and connect your app.
2. [Integration guide](./integration-guide.md): choose your language, or [use an AI assistant](./ai-agents.md).
3. [Configure sign-in](./features.md): set branding, login methods and security policies.

## Login methods

- [Email codes](./email-otp.md) and [email links](./email-links.md): passwordless sign-in, enabled separately by the workspace owner.
- [Social login and CAPTCHA](./managed-auth.md): use Thakol defaults, bring your own keys or disable a method. CAPTCHA uses invisible reCAPTCHA v3.
- [Email delivery](./email-delivery.md): review delivery status and failures.

## Moving providers

[Export users](./user-migration.md) as JSON or CSV. Owners and administrators can migrate profiles and assignments without exporting authentication secrets.

## Reference

[How OIDC works](./how-it-works.md) · [Current capabilities and roadmap](./roadmap.md) · [Security controls](https://thakol.io/security) · [Sample applications](https://github.com/thakol/thakol-samples)

During launch, each workspace includes 1,000,000 tokens and free top-ups. One successful login or registration uses one token. No credit card is required. [Plan details](https://thakol.io/docs/billing).

The [hosted documentation](https://thakol.io/docs) is the main setup guide. Existing detailed guides in this repository remain available for integration and operations.
