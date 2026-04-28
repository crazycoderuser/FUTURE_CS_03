# OWASP API Security Top 10 (2023) — Project Mapping

Full mapping of the OWASP API Security Top 10 (2023 edition) to this project's
security checks, findings, and coverage status.

---

## Coverage Summary

| OWASP ID    | Vulnerability Name                              | Covered  | Finding |
|-------------|------------------------------------------------|----------|---------|
| API1:2023   | Broken Object Level Authorization               | ✅ Yes   | F-02    |
| API2:2023   | Broken Authentication                           | ✅ Yes   | F-01, F-06 |
| API3:2023   | Broken Object Property Level Authorization      | ✅ Yes   | F-03    |
| API4:2023   | Unrestricted Resource Consumption               | ✅ Yes   | F-04    |
| API5:2023   | Broken Function Level Authorization             | ⚠️ Partial | Scope review only |
| API6:2023   | Unrestricted Access to Sensitive Business Flows | ❌ No    | Out of scope |
| API7:2023   | Server Side Request Forgery (SSRF)              | ❌ No    | Out of scope |
| API8:2023   | Security Misconfiguration                       | ✅ Yes   | F-05    |
| API9:2023   | Improper Inventory Management                   | ⚠️ Partial | Doc review only |
| API10:2023  | Unsafe Consumption of APIs                      | ❌ No    | Out of scope |

**Coverage: 4 fully covered, 2 partial, 4 out of scope (v1.0.0)**

---

## Detailed Descriptions

### API1:2023 — Broken Object Level Authorization
**Project Check:** F-02 (BOLA Check)

The most prevalent API vulnerability. Occurs when an API endpoint accesses a
data object using a client-supplied ID without checking whether the user is
authorized to access that specific object.

**How we test it:** Send GET /users/1, /users/2, /users/3. Confirm each
returns a different user's data without authorization enforcement.

---

### API2:2023 — Broken Authentication
**Project Checks:** F-01 (Unauthenticated Access), F-06 (Token Enforcement)

Covers missing, weak, or inconsistently enforced authentication mechanisms.
Includes APIs that require no credentials at all, or that issue tokens but
fail to validate them on downstream endpoints.

**How we test it:**
- F-01: Send GET /users with no Authorization header. 200 OK = finding.
- F-06: Obtain token via POST /api/login, then call GET /api/users without
  the token. 200 OK = finding.

---

### API3:2023 — Broken Object Property Level Authorization
**Project Check:** F-03 (Excessive Data Exposure)

APIs return more properties of a data object than the calling consumer requires.
This violates data minimization principles and amplifies the impact of other
vulnerabilities by increasing the data an attacker can harvest.

**How we test it:** Fetch GET /users/1 and audit every field in the response.
Flag fields with no documented consumer use case.

---

### API4:2023 — Unrestricted Resource Consumption
**Project Check:** F-04 (Rate Limiting)

No limits on the volume, frequency, or size of API requests. Enables automated
data scraping, credential stuffing attacks, and denial-of-service flooding.

**How we test it:** Send 5 sequential GET /users requests with 200ms gaps.
Check all responses for rate-limit headers. Absence = finding.

---

### API8:2023 — Security Misconfiguration
**Project Check:** F-05 (Security Headers)

Broad category covering any security-relevant misconfiguration: missing headers,
default credentials, unnecessary features enabled, verbose error messages.

**How we test it:** Inspect HTTP response headers for the five required
security directives. Any absent header = sub-finding.

---

## References

- OWASP API Security Top 10: https://owasp.org/API-Security/
- OWASP API Security Testing Guide: https://owasp.org/www-project-web-security-testing-guide/
- OWASP Cheat Sheet — REST Security: https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html
