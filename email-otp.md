# Passwordless Email OTP

Application users enter their email address, receive a six-digit code, and enter it to sign in
without a password. Your application keeps its existing OIDC authorization flow and callback.

**Opt-in per workspace:** a workspace owner enables email OTP after confirming that
authentication email delivery is configured.

## Enable for existing users

An active workspace's **OWNER** can enable this method after email delivery is configured:

1. Open **Dashboard → Settings → Advanced → Sign-In Methods**.
2. Enable **Email code (passwordless)**.
3. Leave **Create new application users after email verification** off for existing-user-only access.
4. Save, open your application's login in a fresh browser session, and request a code for an existing enabled user.
5. Enter the code from the email. Any required authenticator-app MFA follows, then the user returns to your application.

This setting applies to your workspace's application users. It does not enable passwordless
login to the Thakol workspace dashboard or bypass approval of workspace access requests.

## Signup on first login

1. Enable application **Self-Registration** in the workspace's advanced policies.
2. Under **Sign-In Methods**, enable **Email code (passwordless)** and
   **Create new application users after email verification**.
3. Save and test with an email address that has no application account.

No separate registration form is needed for the email-code step. An account is still created,
only after email possession has been proven. It has a verified email and no password credential.
Configured profile fields and additional authentication steps still apply. The setting never
creates workspace administrators or migrates users from a legacy password system.

## Code limits

- Codes expire after **five minutes**, are single-use, and allow **five verification attempts**.
- A resend invalidates the old code. Only the newest code works in the original browser session.
- Wait at least **60 seconds** between sends. Further limits apply after repeated requests.
- Configured bot protection applies to both send and resend.
- Unknown or ineligible accounts receive the same browser confirmation. Unknown users get no
  code when signup is disabled; disabled or locked accounts cannot sign in.

## Password fallback and MFA

**Use a password instead** opens the password login form. Email OTP is a primary sign-in
method; authenticator-app TOTP remains a separate second factor. If TOTP is required for the
user, it is still checked after the email code.

## Troubleshooting

- **Toggle missing or enablement fails:** confirm the platform update has reached the workspace.
  Contact the platform operator about email delivery; user email addresses must be unique.
- **No email:** check spam, wait before resending, and confirm the account is enabled or signup is allowed.
- **Invalid/expired code:** use the latest email in the original browser session, or request a new code.
- **Signup option unavailable:** enable application self-registration first.

To disable this method, turn off **Email code (passwordless)** and save. Users created without
a password need the allowed password-reset flow or email OTP restored to sign in again.

The rendered guide is at [thakol.io/docs/email-otp](https://thakol.io/docs/email-otp) after deployment.

## Mandatory authenticator-app MFA

With **Require it for every user** enabled, existing and new application users must enroll before receiving an OIDC authorization code. Enrolled users enter their authenticator code after password, email OTP or email-link verification. Enabling or repairing the policy ends older application SSO sessions once. Optional MFA allows unenrolled users to sign in. Applications still enforce normal JWT expiration and revocation. Test both enrolled and unenrolled users. See [current status](./roadmap.md).
