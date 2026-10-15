# Managed social login and CAPTCHA

Workspace owners open **Dashboard → Login & Bot Protection** and choose, for each provider:

- **Use Thakol default**: Thakol manages the upstream provider application and consent branding.
- **Bring my own credentials**: register a provider application with the exact callback shown in the dashboard, then supply its client ID and secret.
- **Disabled**: do not offer that provider in the customer realm.

Supported configuration types include Google, Facebook, GitHub, LinkedIn, Microsoft, Apple and generic OpenID Connect. Availability depends on installed provider factories and platform configuration. Apple requires an additional server provider installation in the current distribution.

Thakol's hosted website and authentication endpoints are protected by Cloudflare at the network edge, including TLS termination and edge protection for public traffic. This layer remains active when **No CAPTCHA** is selected. Your own application's hosting and Cloudflare configuration are managed separately.

CAPTCHA uses **Google reCAPTCHA v3**, which assesses requests in the background without a checkbox or image puzzle. Thakol checks the resulting score against the configured threshold. Cloudflare edge protection is separate from reCAPTCHA.

CAPTCHA offers **No CAPTCHA**, **Use Thakol CAPTCHA keys**, or **Bring my own CAPTCHA keys**. Customer keys must be reCAPTCHA v3 keys for the authentication hostname. Platform mode uses the platform score and keys. Existing customer keys are retained when switching to defaults. New realms inherit configured defaults; an operator can synchronize defaults to existing realms without replacing explicit overrides or disabled choices.

Applications keep their customer realm issuer and normal OIDC integration. Do not copy social/CAPTCHA secrets into the application or use the shared broker realm as the application's issuer. Mandatory MFA also applies after social sign-in. A provider hint can select a login button but does not restrict which providers a client can use.

Platform administrators configure credentials and stable callbacks under **Admin → Login & CAPTCHA defaults**, then apply saved defaults to existing realms. One dedicated broker realm per provider gives all customer realms one upstream callback. Platform settings are encrypted and secrets are never returned to customers.

Managed login stores a shared upstream broker identity alongside separate customer application accounts. Deleting one customer account does not automatically erase a broker identity used by other customers; platform-wide erasure requires a verified request and review of the linked identities.

Enterprise federation uses OIDC. Guided SAML and direct LDAP/Active Directory synchronization are not shipped.

See the [hosted guide](https://thakol.io/docs/managed-auth).
