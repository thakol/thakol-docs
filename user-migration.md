# Export users and move providers

Workspace **owners and administrators** can open **Users → Export users**, choose JSON or CSV, confirm sign-in with the same account, then download. Confirmation lasts ten minutes. Members cannot export users. The broader workspace backup remains owner-only.

## Included

User IDs, usernames, email and verification status, names, enabled status, creation timestamps, custom profile attributes, direct realm/client role assignments and group paths. The full directory is paginated without a fixed user cap. JSON includes a format version, counts and a completion marker. CSV puts structured attributes and assignments into JSON cells and escapes formula-like values for spreadsheet safety.

## Moving to another provider

1. Pause user and membership changes while exporting. This is not a point-in-time snapshot. Count changes and lookup failures abort the dashboard download, but changes that keep the same count may go undetected.
2. Import profiles using your next provider's tools. This is a portable Thakol format, not a provider-specific import file. Recreate role/group definitions and map exported assignments.
3. Arrange password resets or credential enrollment with the new provider. Passwords and hashes, MFA secrets, social identity links, sessions and tokens are excluded. Internal and credential-like profile attributes are also omitted.
4. Test sign-in and authorization in the new application before switching traffic.

Exports contain personal data: store them securely, restrict access and delete them when no longer needed. Keep the page open until the download finishes. Large exports can take longer; a failed stream does not produce a completed dashboard download. Starts, completions and failures are audited. Five attempts per hour are allowed per workspace.

[Hosted user guide](https://thakol.io/docs/user-management#user-migration-export)
