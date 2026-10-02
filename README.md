# Web Application Security Assessment — OWASP Juice Shop

A structured, manual security assessment of OWASP Juice Shop (a deliberately
vulnerable Node.js/Angular application) using Burp Suite, following the
OWASP Web Security Testing Guide and mapped against the OWASP Top 10.

## Lab Setup

| Host | Role | Address |
|---|---|---|
| Ubuntu (VM) | Target: Juice Shop (Docker) | 192.168.100.10:3001 |
| Kali Linux (VM) | Attacker: Burp Suite (proxy + Repeater) | 192.168.100.20 |

Isolated internal network; target version pinned via Docker image tag.

## Methodology

1. **Mapping** — browsed the app as anonymous and authenticated users through
   Burp's proxy, building an inventory of REST endpoints (`/rest/...`) behind
   the Angular frontend.
2. **Manual testing by OWASP category** — using Burp's Proxy and Repeater to
   intercept, modify and replay requests.
3. **Validation** — every finding reproduced directly via Burp Repeater with
   full request/response evidence, scored with CVSS v3.1 and mapped to CWE.

## Findings Summary

| ID | Title | OWASP Category | CWE | CVSS | Severity |
|---|---|---|---|---|---|
| F-001 | IDOR on basket retrieval — any user can read another user's basket by changing an ID in the URL | A01 Broken Access Control | CWE-639 | 7.5 | High |
| F-002 | SQL Injection in login → full authentication bypass as any account, including admin | A03 Injection | CWE-89 | 9.8 | Critical |
| F-003 | DOM-based XSS in product search — unsanitized input executed client-side | A03 Injection | CWE-79 | 6.1 | Medium |
| F-004 | Password hash embedded in JWT payload, exposed to anyone holding the token | A02 Cryptographic Failures | CWE-200 | 5.3 | Medium |

Full reproduction steps, raw request/response evidence, and remediation
guidance for each finding are in [`findings/`](findings/).

### Finding spotlight — F-002, SQL Injection Auth Bypass

Submitting `' OR 1=1--` as the login email, with any password, returns a
valid signed JWT authenticating as the first matched user — in this case the
**administrator account** — with zero knowledge of real credentials. This is
a textbook example of why authentication logic must never concatenate
user input into a SQL query, however convenient it looks.

```
POST /rest/user/login
{"email":"' OR 1=1--","password":"x"}

→ HTTP/1.1 200 OK
  {"authentication":{"token":"...","umail":"admin@juice-sh.op"}}
```

## Report

Full write-up with executive summary, methodology, and remediation
priorities: [`report/REPORT.md`](report/REPORT.md).

## Skills Demonstrated

- OWASP Web Security Testing Guide methodology
- Burp Suite (Proxy, HTTP history, Repeater) for manual request tampering
- SQL injection identification and exploitation
- Broken access control / IDOR testing
- DOM-based XSS vs. reflected XSS — distinguishing them from traffic evidence
- JWT structure analysis and claims-based data exposure review
- CVSS v3.1 scoring and CWE mapping
- Security finding documentation to a professional, evidence-backed standard

**Tools:** Burp Suite, Docker, OWASP Juice Shop.

## Scope & Limitations

Self-authorised assessment of an intentionally vulnerable application in an
isolated lab network. This round focused on four validated, high-value
findings rather than exhaustive coverage of every OWASP Top 10 category;
no code-level remediation was performed in this pass (see project notes for
the fix-and-retest extension this project is designed to support).

## Disclosure

Personal lab exercise. No production systems or third-party infrastructure
were involved.

