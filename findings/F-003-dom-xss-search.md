# F-003: DOM-based XSS in Product Search

**OWASP Category:** A03:2021 - Injection (XSS)
**CWE:** CWE-79 (Improper Neutralization of Input During Web Page Generation)
**CVSS v3.1:** 6.1 (Medium) — AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N
**Endpoint/Sink:** Client-side search component (Angular), query string `q` parameter

## Description
The product search feature takes the user-supplied search term and renders
it into the DOM without sanitization. Submitting HTML/JS-bearing input
causes it to be parsed and executed by the browser entirely client-side —
no server round-trip occurs in Burp's HTTP history, confirming this is
DOM-based XSS rather than reflected XSS.

## Steps to Reproduce
1. Click the search icon, enter: `<iframe src="javascript:alert(\`xss\`)">`
2. Press Enter.
3. Observe: a JavaScript alert box fires immediately in the browser.
4. Confirmed no corresponding request appears in Burp's HTTP history —
   payload is processed purely client-side (DOM sink).

## Evidence
Manually verified in-browser: alert fired with payload above. No server
request logged in Burp HTTP history for this action (see evidence note
in F-002 regarding available Burp session).

## Impact
An attacker who gets a victim to open a crafted link containing this
payload in the search query can execute arbitrary JavaScript in the
victim's browser session — session token theft, UI redressing, phishing
overlays, or further attacks against the logged-in user.

## Remediation
Sanitize/encode any user-controlled value before binding it into the DOM.
In Angular, avoid binding unsanitized input via `[innerHTML]` or bypassing
the built-in `DomSanitizer`; render user input as text content rather than
raw HTML wherever possible.
