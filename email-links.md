# Passwordless email login links

For the hosted setup guide, see [thakol.io/docs/email-links](https://thakol.io/docs/email-links).

Email links are opt-in for customer application realms. They use the original browser's OIDC
session and do not replace workspace approval or platform administrator authentication.

## Enable

1. Deploy the updated provider and matching login/email themes to auth before the dashboard.
   The dashboard checks for `thakol-email-link-password-form`; the older OTP-only provider is
   insufficient. Both factories use the same passwordless flow implementation.
2. Confirm working SMTP, a verified sender and unique user email addresses.
3. As workspace owner, open Settings → Advanced → Sign-In Methods and enable **Email login link
   (passwordless)**. Codes and links can coexist or be enabled independently.
4. Keep the link signup checkbox off for existing-user-only access. Signup requires its own
   explicit permission and application self-registration. OTP signup does not permit link signup.
5. Test real delivery to a controlled inbox and a successful application callback before first
   client use. Request the link from your app; open it in that same browser/profile, check the
   displayed address, and confirm sign-in. Copy the link into the original browser if the mail
   app opens a different browser. Another device/private window requires a fresh login request.

## Security and session behaviour

Links contain a cryptographically random 256-bit secret, not a password or OTP. The shared
Keycloak single-use cache stores its hash with a five-minute expiry and the original realm,
client, root authentication session and tab binding. The secret is only in the emailed URL and
confirmation form, never a session note, user record or authentication event.

GET/HEAD previews never authenticate or consume the challenge. Confirmation requires POST to
Keycloak's session-protected action URL; the handler rechecks the link, user eligibility, email,
signup permission and duplicate-email policy. Atomic consumption plus the shared session lease
prevents concurrent replay. Required MFA and profile steps remain in the original browser flow.
The confirmation response sets `Referrer-Policy: no-referrer` and `Cache-Control: no-store`.

Links and OTP share the existing cooldown and send quotas. Resend invalidates the preceding
challenge; password fallback revokes it. Unknown/disabled/locked accounts receive the same screen
without email. SMTP failures invalidate the challenge. Avoid logging full sign-in URLs; redact
`email_link` in any reverse-proxy query logging. Never paste a live link into logs or support tickets.

## Test and rollback

`mvn -f keycloak/spi/pom.xml package` covers expiry, one-use, concurrency, browser binding,
GET-only preview, confirmation, eligibility and separate signup permissions. `npm test` covers
provider capability detection and coexistence of the two methods.

Run `node scripts/smoke-local-email-otp.mjs` against an isolated Keycloak on loopback with its
updated provider and themes. The SMTP catcher drives real OTP and link OIDC/PKCE flows, scanner
previews, wrong-browser rejection, signup, password fallback, MFA and disabling. It does not send
to real people or prove real-inbox delivery.

Disable links while the provider is still deployed. Confirm OTP remains bound if enabled; if
both methods are off, native password login is restored. Never remove a provider referenced by
an active flow. Accounts created without passwords need password-reset access or a passwordless
method reenabled. Older dashboard callers that omit link settings preserve the current link policy.

## Mandatory MFA rollout limitation

Enrolled authenticator-app users are challenged, but enforcement for existing unenrolled application users is being corrected. Confirm enrollment for each user before relying on a workspace-wide MFA requirement. See [current status](./roadmap.md).
