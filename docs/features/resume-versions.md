# Feature: Save and Load Resume Versions

## What It Does
Users can save a named snapshot of their resume at any point and retrieve any previous version. This allows maintaining separate tailored copies for different job applications without losing prior work.

---

## Data Model

### Version Table
| Field | Type | Notes |
|---|---|---|
| id | uuid | Primary key |
| resume_id | uuid | Foreign key → Resume Table |
| user_id | uuid | Foreign key → User Table |
| label | string | Optional user-defined name (e.g. "Google application") |
| content | text | Snapshot of full Markdown content at save time |
| created_at | timestamp | |

Versions are immutable once saved — they are never modified after creation.

---

## API Contract

| Method | Endpoint | Description |
|---|---|---|
| POST | `/resumes/:id/versions` | Save current content as a new version |
| GET | `/resumes/:id/versions` | List all versions for a resume |
| GET | `/resumes/:id/versions/:versionId` | Get the content of a specific version |

### POST `/resumes/:id/versions` — Request Body
```json
{
  "label": "Google application"
}
```

---

## Key Technical Decisions
- Versions are snapshots, not diffs — full content is stored each time. Simple to implement and query; storage cost is acceptable for text content.
- Loading a version does not auto-overwrite the current editor state — the user explicitly applies it
- Versions belong to a user; deleting a user should cascade-delete their versions

---

## Open Questions
- Should there be a maximum number of versions per resume?
- Should loading a version create a new version automatically (to preserve current state before overwriting)?
- Should versions be comparable (diff view between two versions)?
- What happens to versions if the parent resume is deleted?
