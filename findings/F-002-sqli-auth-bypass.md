# F-002: SQL Injection leading to Authentication Bypass

**OWASP Category:** A03:2021 - Injection
**CWE:** CWE-89 (SQL Injection)
**CVSS v3.1:** 9.8 (Critical) — AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H
**Endpoint:** `POST /rest/user/login`

## Description
The login endpoint builds a SQL query using the submitted email field without
parameterization. Submitting a crafted email value injects SQL that always
evaluates true, bypassing the password check entirely and authenticating as
an arbitrary existing user (observed: admin account) without knowledge of
any valid credentials.

## Steps to Reproduce
1. Navigate to `/#/login`.
2. In the Email field, enter: `' OR 1=1--`
3. In the Password field, enter any value (e.g. `x`).
4. Submit the form.
5. Observe: the application returns a valid auth token and logs the attacker
   in as a pre-existing account (the first row matched by the injected query).

## Evidence
See evidence/F-002-sqli-login-bypass.txt — request with payload `{"email":"' OR 1=1--","password":"x"}` returned HTTP 200 with a valid JWT authenticating as admin@juice-sh.op (role: admin), umail confirms account takeover without valid credentials.

## Impact
Full authentication bypass. An unauthenticated attacker can gain access to
any account, including administrative accounts, without credentials.

## Remediation
Use parameterized queries / an ORM query builder with bound parameters for
all user-supplied input in authentication logic. Never concatenate user
input into SQL strings.
