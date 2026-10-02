# Web Application Security Assessment — OWASP Juice Shop

**Target:** OWASP Juice Shop (Docker, bkimminich/juice-shop), self-hosted lab
**Tester:** Janardan Bhetwal
**Scope:** Self-authorised personal lab assessment, isolated network
(192.168.100.10:3001), no production systems involved.
**Methodology:** OWASP Web Security Testing Guide, manual testing with
Burp Suite.

## Executive Summary
Four findings were identified and validated, spanning three OWASP Top 10
categories: Broken Access Control, Injection (SQL and DOM XSS), and
Cryptographic Failures. The most severe issue (SQL injection in the login
endpoint) allows complete authentication bypass, including administrative
account takeover, without any valid credentials.

| ID | Title | Category | CVSS | Severity |
|----|-------|----------|------|----------|
| F-001 | IDOR on basket retrieval | A01 Broken Access Control | 6.5 | Medium |
| F-002 | SQL Injection → Auth Bypass | A03 Injection | 9.8 | Critical |
| F-003 | DOM-based XSS in search | A03 Injection | 6.1 | Medium |
| F-004 | Sensitive data (password hash) in JWT | A02 Crypto Failures | 5.3 | Medium |

## Findings
See individual finding files in `findings/F-001.md` through `F-004.md` for
full reproduction steps, evidence and remediation guidance.

## Remediation Priorities
1. **F-002 (Critical)** — parameterize all SQL queries, especially login.
2. **F-001 (Medium)** — add server-side ownership checks on all
   user-resource endpoints.
3. **F-003 / F-004 (Medium)** — sanitize DOM-bound input; strip sensitive
   fields from JWT claims.

## Limitations
This assessment covers four findings validated by manual testing; it is
not an exhaustive test of all OWASP Top 10 categories, and no automated
scan (ZAP) or code-level fixes were performed in this engagement.
