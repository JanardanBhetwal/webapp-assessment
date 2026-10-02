# F-001: Broken Access Control (IDOR) on Basket Retrieval

**OWASP Category:** A01:2021 - Broken Access Control
**CWE:** CWE-639 (Authorization Bypass Through User-Controlled Key)
**CVSS v3.1:** 6.5 (Medium) — AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:N/A:N
**Endpoint:** `GET /rest/basket/{id}`

## Description
The basket retrieval endpoint takes a numeric basket ID directly from the
URL path and returns its contents without verifying that the requesting
(authenticated) user actually owns that basket. Any logged-in user can
enumerate basket IDs sequentially and read other users' basket contents.

## Steps to Reproduce
1. Log in as any user; note your own basket ID from a normal
   `GET /rest/basket/{own_id}` request (e.g. id 6).
2. In Burp Repeater, change the ID in the URL to a different value
   (e.g. `/rest/basket/1`).
3. Send the request.
4. Observe: HTTP 200 OK with another user's basket contents (product IDs,
   quantities), despite not being that user.

## Evidence
Request: GET /rest/basket/1 (authenticated as a different user, own basket id 6)
Response: HTTP 200 OK with basket data belonging to basket id 1, not the
requesting user's own basket.

## Impact
Any authenticated user can read (and, depending on further testing, modify)
any other user's basket by simply changing an ID in the URL — a direct
violation of object-level authorization, exposing other users' order
contents and potentially enabling tampering.

## Remediation
Enforce a server-side ownership check on every basket operation: the
authenticated user's ID (from the validated session/JWT) must match the
basket's owner before any read or write is permitted. Return 403/404 for
any other case rather than the resource contents.
