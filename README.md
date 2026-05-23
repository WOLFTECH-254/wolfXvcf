<div align="center">
  <img src="https://i.ibb.co/0jvxFXQR/Chat-GPT-Image-Mar-28-2026-09-15-11-AM.png" alt="WOLF TECH" width="160" style="border-radius: 50%;" />

  <h1>WolfVCF</h1>
  <p><strong>A multi-user SaaS platform for crowd-powered contact collection.</strong></p>
  <p>Every user gets a public digital card at <code>/u/username</code>. Visitors submit their name and phone number. When the user's target contact count is reached, a VCF file is unlocked for download.</p>

  <img src="https://img.shields.io/badge/Node.js-24-green?logo=node.js" />
  <img src="https://img.shields.io/badge/React-19-blue?logo=react" />
  <img src="https://img.shields.io/badge/Express-5-black?logo=express" />
  <img src="https://img.shields.io/badge/Drizzle-ORM-orange" />
  <img src="https://img.shields.io/badge/pnpm-workspaces-yellow?logo=pnpm" />
  <img src="https://img.shields.io/badge/TypeScript-5.9-blue?logo=typescript" />
</div>

---

## What Is WolfVCF?

WolfVCF is a full-stack multi-user platform. Each registered user gets:

- A **public shareable card** at `/u/username` with a live contact-collection form
- A **dashboard** to manage collected contacts, customise their card, and export a VCF
- A **contact target** — visitors must submit their info to help reach it; once hit, the VCF becomes downloadable for the card owner and optionally for submitters

The platform includes a **super-admin panel** for managing all users, plans, and Paystack payment configuration.

---

## Features

**Public card (`/u/:username`)**
- Dark black + neon green (WOLFBOT) UI — Orbitron + JetBrains Mono fonts
- Animated particle background (`NeonBg` component)
- International phone input with country flag selector and E.164 validation
- Live progress bar toward the user's contact target
- Duplicate phone detection with a friendly message
- Social link buttons: WhatsApp, YouTube, WA Channel, WA Group
- Dynamic OG image for rich social previews when the link is shared
- VCF download button unlocks automatically when target is reached

**Dashboard (authenticated users)**
- Stats overview: total contacts, target progress, plan info
- Contacts table with per-contact delete and bulk clear
- Card settings: display name, bio, contact target, social links
- Password change
- VCF download at any time (owner bypass)

**Auth**
- Signup with username (3–30 chars, `a-z 0-9 _`), email, password (bcrypt)
- Session tokens stored in DB with 30-day expiry
- Token stored in `localStorage` as `wolf_token` / `wolf_user`

**Plans**
| Plan | Price | Max Contacts |
|---|---|---|
| Free | KES 0 / forever | 200 |
| Pro | KES 500 / month | 2,000 |
| Enterprise | KES 2,000 / month | 10,000 |

**Super-admin panel (`/super`)**
- Authenticated via `SUPER_ADMIN_EMAIL` + `SUPER_ADMIN_PASSWORD` env vars (base64 token)
- Platform-wide stats: total users, total contacts
- User management: view, toggle active status
- Plan CRUD: create and edit plans
- Paystack configuration: public key, secret key, test mode toggle

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 19 + Vite 7 + TailwindCSS 4 |
| Routing | Wouter |
| Phone input | react-phone-number-input |
| Backend | Express 5 (Node.js 24) |
| Database | PostgreSQL (Neon / Replit / any PG host) |
| ORM | Drizzle ORM |
| Auth | bcryptjs + UUID session tokens |
| Validation | Zod |
| Monorepo | pnpm workspaces |
| Payments | Paystack (configurable via super-admin) |

---

## Project Structure

```
.
├── artifacts/
│   ├── vcf-card/               # React + Vite frontend
│   │   └── src/
│   │       ├── pages/
│   │       │   ├── Landing.tsx         # / — hero + feature cards
│   │       │   ├── Signup.tsx          # /signup
│   │       │   ├── Login.tsx           # /login
│   │       │   ├── Dashboard.tsx       # /dashboard (auth-protected)
│   │       │   ├── DashboardContacts.tsx
│   │       │   ├── DashboardSettings.tsx
│   │       │   ├── PublicCard.tsx      # /u/:username
│   │       │   ├── Plans.tsx           # /plans
│   │       │   ├── SuperAdmin.tsx      # /super
│   │       │   └── not-found.tsx
│   │       ├── components/
│   │       │   ├── NeonBg.tsx          # Animated particle background
│   │       │   ├── WolfNav.tsx         # Top navigation bar
│   │       │   └── DashboardLayout.tsx
│   │       └── lib/
│   │           └── api.ts              # Fetch helpers + token management
│   │
│   └── api-server/             # Express 5 API server (port 8080)
│       └── src/
│           ├── middleware/auth.ts      # requireAuth middleware
│           └── routes/
│               ├── auth.ts            # signup, login, logout, me
│               ├── public.ts          # public card + VCF + OG image
│               ├── dashboard.ts       # authenticated dashboard CRUD
│               ├── super.ts           # super-admin routes
│               └── health.ts          # GET /healthz
│
├── lib/
│   ├── db/                     # Drizzle ORM schema + migrations
│   │   ├── src/schema/
│   │   │   ├── users.ts               # users + plans tables
│   │   │   ├── user-settings.ts       # per-user card settings
│   │   │   ├── contacts.ts            # contacts (user_id FK)
│   │   │   ├── sessions.ts            # auth sessions
│   │   │   └── platform-config.ts     # key/value store (Paystack etc.)
│   │   └── migrate.mjs                # Direct SQL migration script
│   └── api-zod/                # Shared Zod validation schemas
│
├── config.ts                   # Reads DATABASE_URL + ADMIN_PIN from env
├── vercel.json                 # Vercel build config (frontend only)
├── pnpm-workspace.yaml
└── tsconfig.base.json
```

---

## API Routes

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | `/api/auth/signup` | — | Register (username, email, password) |
| POST | `/api/auth/login` | — | Login → session token |
| POST | `/api/auth/logout` | Session | Invalidate session |
| GET | `/api/auth/me` | Session | Current user + plan info |
| GET | `/api/u/:username` | — | Public card data |
| POST | `/api/u/:username/contact` | — | Submit a contact |
| GET | `/api/u/:username/download` | — | Download VCF (when target reached) |
| GET | `/api/u/:username/og-image` | — | Dynamic OG image (SVG) |
| GET | `/api/dashboard/contacts` | Session | List contacts |
| DELETE | `/api/dashboard/contacts/:id` | Session | Delete one contact |
| DELETE | `/api/dashboard/contacts` | Session | Clear all contacts |
| GET | `/api/dashboard/download` | Session | Download VCF (always) |
| PUT | `/api/dashboard/settings` | Session | Update card settings |
| PUT | `/api/dashboard/password` | Session | Change password |
| POST | `/api/super/login` | Super token | Verify super-admin credentials |
| GET | `/api/super/stats` | Super token | Platform stats |
| GET | `/api/super/users` | Super token | All users |
| GET/POST/PUT | `/api/super/plans` | Super token | Plan CRUD |
| GET/PUT | `/api/super/paystack` | Super token | Paystack config |
| GET | `/api/healthz` | — | Health check |

---

## Database Schema

### `plans`
| Column | Type | Notes |
|---|---|---|
| id | serial PK | 1 = Free (seeded) |
| name | text | "Free", "Pro", "Enterprise" |
| max_contacts | integer | Default 200 |
| price_kes | integer | Price in KES (0 = free) |
| features | text | JSON array string |
| is_active | boolean | |

### `users`
| Column | Type | Notes |
|---|---|---|
| id | serial PK | |
| username | text UNIQUE | Lowercase `a-z 0-9 _`, 3–30 chars |
| email | text UNIQUE | |
| password_hash | text | bcrypt (12 rounds) |
| plan_id | integer | FK → plans.id, default 1 |
| is_active | boolean | |
| created_at | timestamp | |

### `user_settings`
| Column | Type | Notes |
|---|---|---|
| user_id | integer PK | FK → users.id |
| card_name | text | Display name on public card |
| bio | text | Tagline/bio |
| whatsapp | text | URL |
| youtube | text | URL |
| wa_channel | text | URL |
| wa_group | text | URL |
| contact_target | integer | Contacts needed to unlock VCF |

### `contacts`
| Column | Type | Notes |
|---|---|---|
| id | serial PK | |
| user_id | integer | FK → users.id |
| full_name | text | Required |
| phone | text | E.164 format, unique per user |
| email | text | Optional |
| organization | text | Optional |
| created_at | timestamp | |

Unique constraint: `(user_id, phone)` — per-card duplicate prevention.

### `sessions`
| Column | Type | Notes |
|---|---|---|
| id | text PK | UUID token |
| user_id | integer | FK → users.id |
| expires_at | timestamp | 30 days |
| created_at | timestamp | |

### `platform_config`
Key/value store. Keys: `paystack_public_key`, `paystack_secret_key`, `paystack_test_mode`.

---

## Local Development

### Prerequisites
- Node.js 24+
- pnpm 9+ (`npm install -g pnpm`)
- A PostgreSQL database (Neon free tier works perfectly)

### Setup

```bash
# Clone the repo
git clone https://github.com/WOLFTECH-254/wolfXvcf.git
cd wolfXvcf

# Install all workspace dependencies
pnpm install
```

### Environment variables

Create a `.env` file in the project root (or export in your shell):

```bash
DATABASE_URL=postgresql://user:password@host/dbname?sslmode=require
SUPER_ADMIN_EMAIL=your-admin-email@example.com
SUPER_ADMIN_PASSWORD=your-super-secret-password
```

> `ADMIN_PIN` is still read by `config.ts` for legacy compatibility but the main auth system uses the env vars above.

### Run migrations

```bash
node lib/db/migrate.mjs
```

This creates all tables and seeds the default **Free** plan. It is idempotent — safe to re-run.

### Start dev servers

```bash
# Terminal 1 — Express API (port 8080)
pnpm --filter @workspace/api-server run dev

# Terminal 2 — React frontend (port 5173, proxies /api → :8080)
pnpm --filter @workspace/vcf-card run dev
```

Visit `http://localhost:5173`.

---

## Deployment

### Build

```bash
pnpm run typecheck     # Type-check all packages
pnpm run build         # Build frontend + API server
```

The frontend build output lands in `artifacts/vcf-card/dist/`.  
The API server is bundled via esbuild (`artifacts/api-server/build.mjs`).

### Vercel (frontend only)

`vercel.json` is configured for the React SPA:

```json
{
  "buildCommand": "pnpm --filter @workspace/vcf-card run build",
  "outputDirectory": "dist",
  "installCommand": "pnpm install",
  "rewrites": [
    { "source": "/((?!api/).*)", "destination": "/index.html" }
  ]
}
```

For production you still need to run the Express API server separately (e.g. Railway, Render, or Replit) and point the frontend's `/api` proxy at it.

### Environment variables (production)

| Variable | Required | Description |
|---|---|---|
| `DATABASE_URL` | ✅ | PostgreSQL connection string |
| `SUPER_ADMIN_EMAIL` | ✅ | Super-admin login email |
| `SUPER_ADMIN_PASSWORD` | ✅ | Super-admin login password |

---

## VCF Format

Contacts are stored and exported in **E.164 international format** (e.g. `+254713046497`). The generated `.vcf` file is compatible with:

- Android (Contacts app, WhatsApp)
- iPhone (Contacts app)
- Google Contacts
- Outlook

---

## Stay Updated

- 📡 **WhatsApp Channel** — [Follow for updates](https://whatsapp.com/channel/0029Vb6dn9nEQIaqEMNclK3Y)
- 👥 **WhatsApp Group** — [Join the community](https://chat.whatsapp.com/HjFc3pud3IA0R0WGr1V2Xu)

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on submitting issues, pull requests, and coding standards.

---

## Security

Please do **not** open public issues for security vulnerabilities. See [SECURITY.md](SECURITY.md) for responsible disclosure instructions.

---

## License

MIT — see [LICENSE](LICENSE) for details.

---

<div align="center">
  <sub>Built by <strong>WOLF TECH</strong> · I explore systems</sub>
</div>