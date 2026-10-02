# Web Application Security Assessment — OWASP Juice Shop

**Target:** OWASP Juice Shop (Docker, bkimminich/juice-shop), self-hosted lab
**Tester:** [Your name]
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
| F-001 | IDOR on basket retrieval | A01 Broken Access Control | 7.5 | High |
| F-002 | SQL Injection → Auth Bypass | A03 Injection | 9.8 | Critical |
| F-003 | DOM-based XSS in search | A03 Injection | 6.1 | Medium |
| F-004 | Sensitive data (password hash) in JWT | A02 Crypto Failures | 5.3 | Medium |

## Findings
See individual finding files in `findings/F-001.md` through `F-004.md` for
full reproduction steps, evidence and remediation guidance.

## Remediation Priorities
1. **F-002 (Critical)** — parameterize all SQL queries, especially login.
2. **F-001 (High)** — add server-side ownership checks on all
   user-resource endpoints.
3. **F-003 / F-004 (Medium)** — sanitize DOM-bound input; strip sensitive
   fields from JWT claims.

## Limitations
This assessment covers four findings validated by manual testing; it is
not an exhaustive test of all OWASP Top 10 categories, and no automated
scan (ZAP) or code-level fixes were performed in this engagement.
EOF
2. Write a short README:


cat > ~/webapp-assessment/README.md << 'EOF'
# Web Application Security Assessment — OWASP Juice Shop

Personal-lab security assessment of OWASP Juice Shop, following the OWASP
Web Security Testing Guide. Four validated findings across three OWASP Top
10 categories (Broken Access Control, Injection, Cryptographic Failures),
each with CVSS scoring, reproduction steps and evidence.

See `report/REPORT.md` for the full write-up and `findings/` for individual
finding detail.

**Tools used:** Burp Suite, Docker.
