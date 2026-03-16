# Feature: User Authentication

## What It Does
Allows users to create an account and log in using a magic link, Google OAuth, or Canva OAuth. All resume data is scoped to the authenticated user — nothing is accessible without a valid session.

---

## Login Methods
- **Magic link** — user enters their email and receives a one-time login link
- **Google OAuth** — standard OAuth 2.0 flow via Google
- **Canva OAuth** — OAuth 2.0 via Canva (non-standard, requires custom integration)

No email/password option.

---

## Data Model

### Auth Table *(managed by Supabase Auth)*
| Field | Type | Notes |
|---|---|---|
| id | uuid | Primary key |
| email | string | Unique |
| provider | enum | `magic_link`, `google`, `canva` |
| created_at | timestamp | |

### User Table
| Field | Type | Notes |
|---|---|---|
| id | uuid | Foreign key → Auth Table |
| email | string | |
| display_name | string | |
| created_at | timestamp | |

---

## API Contract

| Method | Endpoint | Description |
|---|---|---|
| POST | `/auth/magic-link` | Send magic link to email |
| GET | `/auth/callback` | OAuth + magic link redirect handler |
| POST | `/auth/logout` | Invalidate session |
| GET | `/auth/me` | Return current user |

---

## Key Technical Decisions
- Supabase Auth handles magic link and Google OAuth natively
- Canva OAuth requires a custom server-side implementation in the Auth Service
- Sessions are JWT-based; the API validates the token on every request
- Same email across providers resolves to the same user account

---

## Open Questions
- Which Canva OAuth scopes are needed?
- Should users be able to link multiple providers to one account post-signup?
- What is the session expiry duration?
- What happens to a user's data if they delete their account?
