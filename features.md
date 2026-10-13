# Thakol Platform Security & Features

Thakol packs enterprise-grade security protocols into a developer-friendly framework. Here are the core features available in your workspace console:

---

## 🚀 1. Zero-Downtime User Migration (JIT)

Migrating existing users to a new authentication platform is notoriously painful, usually requiring you to force all users to reset their passwords. 

Thakol solves this with our **Just-In-Time (JIT) User Migration Engine**:
* **Lazy Password Import**: When a user attempts to log in, Thakol first checks your Keycloak realm DB. If the user doesn't exist, our migration SPI proxies the credential verification securely via an HTTP POST request to your **legacy database login API**.
* **Instant Migration**: If your legacy API validates the credentials and returns a success response, Thakol automatically creates the user record locally, caches their details, and hashes their password. 
* **Zero Disruption**: Future logins are handled entirely inside Thakol. Your users never know a migration happened and never have to reset their passwords.

### Migration Configuration parameters:
You can toggle and configure this engine in your dashboard settings:
* **Legacy User Login Endpoint**: The URL Thakol sends the verification request to.
* **Request Body Template**: A JSON template mapping credentials, e.g. `{"username":"${username}","password":"${password}"}`.
* **Custom Security Header**: Header key/value secrets to verify that the request originates from Thakol (e.g. `X-Thakol-Migration-Secret`).

---

## 🎨 2. Dynamic Custom Branding

Keycloak traditionally requires developers to compile custom Java `.jar` files to inject styles and custom templates. Thakol removes this friction by utilizing **dynamic CSS theme overlays** at runtime:

* **Live Previews**: Adjust colors, themes (Classic, Minimal, Glassmorphic), logo URLs, and favicons directly inside your settings panel.
* **Instant Refresh**: During a user redirect, the hosted Thakol login interface queries your workspace's branding API in real-time, fetching your custom logo and injecting your theme's HSL color variables instantly.

---

## 🛡️ 3. Brute-Force & Lockout Defenses

Guard your user database against robotic credential-stuffing and password-guessing lockouts:
* **Max Login Failures**: Configure a threshold of consecutive failures (e.g. 5 attempts) allowed before an account is locked.
* **Lockout Duration**: Set temp-locks in seconds (e.g. 900 seconds / 15 minutes) before the system automatically resets the user's status.
* **Velocity Tracking**: Block brute-force vectors by tracking request frequencies across IP addresses.

---

## Passwordless Email OTP

Email OTP is available as an opt-in passwordless method for application users.
Workspace owners can enable **Email code (passwordless)** under
**Dashboard → Settings → Advanced → Sign-In Methods**.

* **No password needed**: application users receive a six-digit email code and enter it in the browser where they requested it.
* **Optional signup**: with application self-registration and **Create new application users after email verification** enabled, Thakol creates the account only after email verification.
* **Bounded codes**: five-minute expiry, single use, five verification attempts, and send/resend limits. Resending invalidates the previous code.
* **Existing OIDC integration**: the app uses its usual authorization flow and callback.
* **Password fallback and MFA**: password login remains available, and any required authenticator-app MFA still follows email verification.

This option is for application users, not Thakol workspace approval or platform administrators.
See the [email OTP setup guide](./email-otp.md) for enablement, signup policy, and troubleshooting.

---

## 🔑 4. Multi-Factor Authentication (MFA)

Add a second factor across every application in your workspace without touching your own code:
* **OTP Tokens**: Require or offer time-based one-time passwords (TOTP), compatible with Google Authenticator, Microsoft Authenticator, 1Password, Authy, and any other RFC 6238 app.
* **Workspace-wide**: Thakol sets the OTP step on your realm's browser login flow, so it applies to all of your OIDC clients at once. Your integration and OIDC flow are unchanged.
* **Enforced or optional**: Require enrolment for every user on next sign-in, or let users enrol themselves and challenge only those who have.

> **Passkeys / WebAuthn are not available yet.** Hardware keys and biometric sign-in were withdrawn from the initial release and are being rebuilt — see the [roadmap](https://thakol.io/roadmap). Earlier versions of this page listed them as available; that was incorrect. TOTP is the supported second factor today.

---

## 💻 5. Active Session Control

Halt session hijacking attacks by checking and controlling active logins:
* **Concurrent Logins**: Limit the number of concurrent active sessions allowed per user, and choose whether an over-limit login is denied or replaces that user's oldest session. Disabled by default — enable it per workspace under Login Policies.
* **Session Review**: Inspect every active session for a user from the Users directory: originating IP address, when the session started, when it was last used, and which of your applications it covers.
* **Remote Revocation**: Sign a single session out, or sign a user out everywhere at once. Revocation takes effect immediately across every application in the workspace and is written to the audit trail.

---

## 📦 6. Your Data, On Demand

Thakol is built on standard OIDC precisely so you are never trapped. That principle extends to your data:

* **Full Workspace Export**: Download everything Thakol holds for your workspace as a single JSON file — configuration, OIDC applications, identity providers, administrators, your end-user directory, usage transactions and audit history. Available to the workspace owner from **Settings → Advanced**.
* **Secrets Excluded By Design**: Exports never contain client secrets, captcha keys or migration credentials. An export is safe to hand to an auditor; rotate secrets from their own screens instead.
* **Permanent User Deletion**: Remove an end user outright, not merely disable them — the Keycloak account and the email-delivery records Thakol holds for that address are both erased. This is what lets you answer a GDPR erasure request as the data controller. Every deletion is audited, and the audit record deliberately stores no email address.
