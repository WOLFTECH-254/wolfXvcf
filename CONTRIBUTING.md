# Contributing to WolfVCF

Thanks for taking the time to contribute! This document explains how to get involved — from reporting bugs to submitting pull requests.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
  - [Reporting Bugs](#reporting-bugs)
  - [Suggesting Features](#suggesting-features)
  - [Submitting Pull Requests](#submitting-pull-requests)
- [Development Setup](#development-setup)
- [Project Conventions](#project-conventions)
  - [Branch Naming](#branch-naming)
  - [Commit Messages](#commit-messages)
  - [TypeScript](#typescript)
  - [Code Style](#code-style)
- [Testing](#testing)
- [Pull Request Checklist](#pull-request-checklist)

---

## Code of Conduct

Please read [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) before participating. We expect all contributors to follow it.

---

## How Can I Contribute?

### Reporting Bugs

Before opening an issue, please:

1. Search [existing issues](https://github.com/WOLFTECH-254/wolfXvcf/issues) to avoid duplicates.
2. Confirm the bug is reproducible on the latest `main` branch.

When you open an issue, include:

- A clear, descriptive title
- Steps to reproduce the problem
- What you expected vs. what actually happened
- Node.js version (`node -v`), OS, and browser (if frontend)
- Relevant console output or error messages

For security vulnerabilities, **do not open a public issue** — see [SECURITY.md](SECURITY.md) instead.

---

### Suggesting Features

Open an issue with the label `enhancement` and describe:

- The problem you are trying to solve
- Your proposed solution
- Any alternative approaches you considered

Feature discussions are welcome before any code is written.

---

### Submitting Pull Requests

1. **Fork** the repository and create your branch from `main`.
2. Make your changes — keep them focused and minimal.
3. Ensure `pnpm run typecheck` passes with no errors.
4. Run `pnpm exec prettier --check .` and fix any formatting issues.
5. Update documentation (README, inline comments) if your change affects behaviour or setup.
6. Open a pull request against `main` with a clear description of what changed and why.

PRs that include unrelated changes, massive reformatting, or no description will be asked to revise before review.

---

## Development Setup

### Requirements

- Node.js 24+
- pnpm 9+ (`npm install -g pnpm`)
- A PostgreSQL database — [Neon free tier](https://neon.tech) works great

### Steps

```bash
# 1 — Fork and clone
git clone https://github.com/YOUR_USERNAME/wolfXvcf.git
cd wolfXvcf

# 2 — Install dependencies
pnpm install

# 3 — Set up environment variables
cp .env.example .env        # create this file if it doesn't exist yet
# Then edit .env:
#   DATABASE_URL=postgresql://...
#   SUPER_ADMIN_EMAIL=admin@example.com
#   SUPER_ADMIN_PASSWORD=supersecret

# 4 — Run DB migrations (creates tables + seeds Free plan)
node lib/db/migrate.mjs

# 5 — Start dev servers (two terminals)
pnpm --filter @workspace/api-server run dev   # Express API → http://localhost:8080
pnpm --filter @workspace/vcf-card run dev     # React SPA  → http://localhost:5173
```

The Vite dev server proxies all `/api` requests to `:8080` automatically.

---

## Project Conventions

### Branch Naming

| Type | Pattern | Example |
|---|---|---|
| Bug fix | `fix/<short-description>` | `fix/duplicate-phone-check` |
| Feature | `feat/<short-description>` | `feat/csv-export` |
| Docs | `docs/<short-description>` | `docs/update-readme` |
| Refactor | `refactor/<short-description>` | `refactor/auth-middleware` |
| Chore | `chore/<short-description>` | `chore/update-deps` |

Work directly on a feature branch, never on `main`.

### Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <short summary>

[optional body]

[optional footer]
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

Examples:
```
feat(dashboard): add CSV export button
fix(public-card): prevent VCF download before target reached
docs(readme): update API route table
chore: upgrade drizzle-orm to 0.45.x
```

Keep the subject line under 72 characters. Use the body to explain *why*, not *what*.

### TypeScript

- Every package extends `tsconfig.base.json` with `composite: true`.
- Run `pnpm run typecheck` from the root before pushing. This checks all packages.
- Do not use `any` unless absolutely necessary — prefer `unknown` and narrow the type.
- New API route handlers must include request/response types.

### Code Style

- **Formatter**: Prettier (config in root `package.json`). Run `pnpm exec prettier --write .` before committing.
- **Imports**: Use workspace aliases (`@workspace/db`, `@/components/...`) — no relative `../../` chains.
- **Env vars**: Always read via `process.env["KEY"]` (bracket notation), never via dot notation, to satisfy TypeScript strict checks.
- **Error handling**: All Express route handlers must wrap DB calls in try/catch and return a typed JSON error response.

---

## Testing

There is currently no automated test suite. If you are adding a significant feature:

- Manually verify it works end-to-end in local dev
- Include testing steps in your PR description so reviewers can verify
- If you want to add tests, open an issue first to discuss the approach

---

## Pull Request Checklist

Before requesting review, confirm the following:

- [ ] `pnpm run typecheck` passes with zero errors
- [ ] `pnpm exec prettier --check .` shows no formatting issues
- [ ] The change is limited in scope to what's described in the PR
- [ ] Any new env vars are documented in the README
- [ ] Any new API routes are listed in the README route table
- [ ] Breaking changes are clearly labelled in the PR description
- [ ] No secrets, keys, or personal data are included in the diff

---

## Questions?

Open a [GitHub Discussion](https://github.com/WOLFTECH-254/wolfXvcf/discussions) or drop a message in the [WhatsApp Group](https://chat.whatsapp.com/HjFc3pud3IA0R0WGr1V2Xu).

---

<div align="center">
  <sub>Built by <strong>WOLF TECH</strong> · I explore systems</sub>
</div>