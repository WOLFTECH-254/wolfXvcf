# Security Policy

## Supported Versions

Only the latest commit on `main` is actively maintained. Please ensure you are testing against `main` before reporting a vulnerability.

| Branch | Supported |
|---|---|
| `main` (latest) | ✅ |
| Older commits | ❌ |

---

## Reporting a Vulnerability

**Please do not open a public GitHub issue for security vulnerabilities.**

If you discover a security issue — including authentication bypasses, data leaks, SQL injection vectors, or privilege escalation — report it privately:

1. Email **wolftech254@proton.me** (or the contact listed on the GitHub profile) with:
   - A description of the vulnerability
   - Steps to reproduce it
   - The potential impact
   - Any suggested mitigation (optional)

2. You will receive an acknowledgement within **72 hours**.

3. We aim to investigate and ship a fix within **14 days** for critical issues. You will be credited in the release notes unless you prefer to remain anonymous.

Please do not disclose the issue publicly until a fix has been released.

---

## Scope

Issues in scope:

- Authentication and session management (signup, login, token storage, expiry)
- Super-admin access control (`/super` routes, `x-super-token` header)
- SQL injection or ORM misuse in any route handler
- Insecure direct object references (accessing another user's contacts or settings)
- Exposure of environment variables or secrets in responses
- VCF download access control bypass

Out of scope:

- Theoretical vulnerabilities with no practical exploit path
- Issues that require physical access to the server
- Social engineering attacks
- Issues in third-party dependencies that have already been publicly disclosed (open a normal issue or PR to update the dep)

---

## Security Best Practices for Self-Hosters

If you are deploying WolfVCF on your own infrastructure:

- Set `SUPER_ADMIN_EMAIL` and `SUPER_ADMIN_PASSWORD` to strong, unique values — never the defaults.
- Use a dedicated PostgreSQL role with only the permissions the app needs (`SELECT`, `INSERT`, `UPDATE`, `DELETE` on the app tables — no `CREATE`, `DROP`, or `SUPERUSER`).
- Always use `sslmode=require` in your `DATABASE_URL` for any cloud database.
- Keep Node.js and pnpm up to date.
- Do not commit `.env` files — they are in `.gitignore` by default.
- Rotate `SUPER_ADMIN_PASSWORD` regularly if you manage a public instance.

---

<div align="center">
  <sub>Built by <strong>WOLF TECH</strong> · I explore systems</sub>
</div>