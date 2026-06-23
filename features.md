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

## 🔑 4. Multi-Factor Auth (MFA) & WebAuthn

Meet modern security compliance audits (such as SOC2 or ISO 27001) effortlessly:
* **OTP Tokens**: Force or allow users to configure time-based one-time password (TOTP) credentials via Google Authenticator or Microsoft Authenticator.
* **WebAuthn Support**: Allow hardware keys (such as YubiKeys) or biometric checks (like TouchID, FaceID, or Windows Hello) to authenticate sessions cryptographically.

---

## 💻 5. Active Session Control

Halt session hijacking attacks by checking and controlling active logins:
* **Concurrent Logins**: Limit the number of concurrent active sessions allowed per user.
* **Remote Revocation**: Developers and administrators can review active user sessions, check geographic locations, and trigger instant session sign-outs remotely.
