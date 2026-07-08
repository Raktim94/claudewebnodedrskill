---
name: cybersecurity
description: Defensive security standards for writing and reviewing secure code. Use this skill whenever the user writes any code that handles auth, login, passwords, payments, file uploads, user input, APIs, databases, or secrets — and whenever they ask for a security review, mention hacking/vulnerabilities/OWASP, or ask "is this safe?". Apply it proactively to ALL web/app code, even when the user doesn't mention security.
---

# Cybersecurity — Secure-by-Default Engineering

Write code that is secure by default, and review code like a security engineer. This skill is strictly defensive: harden systems, find and fix vulnerabilities, explain risks. Never produce exploit code, malware, or attack tooling.

## Non-negotiables (apply to all generated code)

1. **Parameterized queries always.** Never concatenate user input into SQL/NoSQL/shell/LDAP. Use prepared statements or an ORM. For shell: avoid `exec` with strings; use argument arrays.
2. **Validate input at the boundary.** Allowlist over blocklist. Use schema validation (Zod/Pydantic/Joi) on every API input: type, length, range, format. Reject, don't sanitize, malformed input.
3. **Encode output for context.** HTML-escape by default (React/modern templates do this — never bypass with `dangerouslySetInnerHTML`/`v-html` on user content without DOMPurify).
4. **Secrets never in code or git.** Environment variables at minimum; secret managers (Vault, AWS/GCP Secret Manager, Doppler) for production. Add `.env` to `.gitignore` immediately. If a secret was ever committed, rotate it — deleting the commit is not enough.
5. **HTTPS everywhere, secure headers on.** Set: `Strict-Transport-Security`, `Content-Security-Policy` (start with `default-src 'self'`), `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `Referrer-Policy: strict-origin-when-cross-origin`. Use helmet (Node) or framework equivalents.

## Authentication

- Passwords: hash with **argon2id** (or bcrypt cost ≥ 12). Never MD5/SHA for passwords. Enforce length ≥ 8, check against breached-password lists, no arbitrary complexity rules.
- Prefer delegating auth: OAuth/OIDC via a proven provider or library (Auth.js, Lucia, Clerk, Auth0, Keycloak). Hand-rolled auth is a last resort.
- Sessions: httpOnly + Secure + SameSite=Lax cookies; regenerate session ID on login; server-side revocation. JWTs: short expiry (≤15 min) + refresh token rotation; never store in localStorage; validate `alg` (reject `none`).
- MFA (TOTP/passkeys) for anything sensitive. Passkeys/WebAuthn are the 2026 default recommendation.
- Rate-limit login, signup, and password reset (per-IP and per-account). Return identical errors for "wrong user" vs "wrong password".

## Authorization

- Enforce on the **server** for every request — client-side checks are UX, not security.
- Check object ownership, not just role ("can this user access resource 123?" — IDOR is the most common real-world API bug).
- Deny by default; centralize policy (middleware/policy layer) instead of scattering `if` checks.
- Use UUIDs over sequential IDs in URLs (defense in depth, not a substitute for checks).

## Common vulnerability checklist (OWASP-aligned)

When reviewing code, check in this order: injection (SQL/command/template), broken access control (IDOR, missing authz), auth flaws (weak sessions, credential handling), XSS (reflected/stored/DOM), CSRF (state-changing GETs, missing tokens on cookie-auth apps), SSRF (user-supplied URLs fetched server-side — allowlist hosts, block internal IPs/metadata endpoints), insecure deserialization, path traversal on file operations (`../` — resolve and verify prefix), mass assignment (bind explicit fields, never whole request bodies to models), and security misconfiguration (debug mode on, default creds, open CORS `*` with credentials).

## File uploads

Validate type by magic bytes not extension; enforce size limits; store outside webroot or in object storage with random names; never execute or `include` uploaded content; serve with `Content-Disposition` and a separate domain/CDN if possible.

## Dependencies & supply chain

Run `npm audit` / `pip-audit` / `osv-scanner` in CI; pin lockfiles; enable Dependabot/Renovate; beware typosquatted packages; minimize dependency count. Review postinstall scripts on new packages.

## Data protection & privacy

Encrypt at rest (managed DBs default) and in transit; hash or tokenize PII where full values aren't needed; log security events (logins, permission changes, failures) but NEVER log passwords, tokens, or full card numbers; define data retention and deletion paths (GDPR/CCPA).

## Security review output format

When asked to review, deliver findings as a table: Severity (Critical/High/Medium/Low) | Location | Issue | Impact | Fix (with code). Lead with critical items. Include a "what's done well" line — accuracy builds trust. Finish with the top 3 fixes to do today.
