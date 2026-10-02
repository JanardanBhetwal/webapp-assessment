# F-004: Sensitive Data Exposure via JWT Claims

**OWASP Category:** A02:2021 - Cryptographic Failures
**CWE:** CWE-200 (Exposure of Sensitive Information to an Unauthorized Actor)
**CVSS v3.1:** 5.3 (Medium) — AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N
**Endpoint:** `POST /rest/user/login` (and every subsequent authenticated request via the `Authorization: Bearer` header / `token` cookie)

## Description
The JWT issued at login embeds the full user record in its payload,
including the user's password hash, in addition to id, email, role and
other profile fields. JWT payloads are base64-encoded, not encrypted —
anyone who obtains the token (e.g. via XSS, as demonstrated in F-003, or a
leaked log/proxy history) can trivially decode it and recover the password
hash offline for cracking, with no server-side protection.

## Steps to Reproduce
1. Log in (see F-002 evidence) and capture the returned JWT.
2. Base64-decode the JWT payload (middle segment, between the two `.`).
3. Observe the decoded JSON contains:
   `"password":"0192023a7bbd7325051 6f069df18b500"` (an MD5 hash) along with
   email, role, and other PII.

## Evidence
See evidence/F-002-sqli-login-bypass.txt — same JWT, decoded payload shown
in the accompanying note.

## Impact
Password hashes (and other sensitive fields) are exposed to any party who
can read the token — the browser, any XSS payload (chainable with F-003),
browser extensions, or intermediate logging/proxies. Combined with a weak
hash algorithm (MD5, unsalted), this significantly lowers the cost of
offline password cracking if a token is ever leaked.

## Remediation
Exclude sensitive fields (password hash, security answers, etc.) from the
JWT payload entirely. JWTs should carry only the minimal claims needed for
authorization (e.g. user id, role, expiry) — look up anything else
server-side from the database when needed.
