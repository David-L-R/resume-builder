# Feature: Markdown Editor

## What It Does
Provides a text editor where users can write and format their resume using Markdown. The editor renders a live preview alongside the raw text. This is the core surface of the product — all other features (AI assistant, versions, PDF import) feed into or out of it.

---

## Data Model

### Resume Table
| Field | Type | Notes |
|---|---|---|
| id | uuid | Primary key |
| user_id | uuid | Foreign key → User Table |
| title | string | User-defined name (e.g. "Senior Engineer CV") |
| created_at | timestamp | |
| updated_at | timestamp | |

### Resume Content Table
| Field | Type | Notes |
|---|---|---|
| id | uuid | Primary key |
| resume_id | uuid | Foreign key → Resume Table |
| content | text | Full markdown content |
| updated_at | timestamp | |

---

## API Contract

| Method | Endpoint | Description |
|---|---|---|
| GET | `/resumes` | List all resumes for the current user |
| POST | `/resumes` | Create a new resume |
| GET | `/resumes/:id` | Get a resume and its content |
| PATCH | `/resumes/:id` | Update title or content |
| DELETE | `/resumes/:id` | Delete a resume |

---

## Key Technical Decisions
- Content is stored as raw Markdown — no proprietary format
- Editor and preview are shown side-by-side (split pane)
- Section structure (e.g. `## Experience`, `## Education`) is Markdown-convention-based, not enforced by the data model — this keeps the editor flexible and supports AI section targeting

---

## Open Questions
- Should there be an autosave on a timer, or only on explicit user action?
- What Markdown features should be supported? (tables, code blocks, etc.)
- Should the editor support a "focus mode" per section?
- How is a "section" defined for the AI assistant — by heading level?
