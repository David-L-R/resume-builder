# Feature: AI Assistant for Resume Sections

## What It Does
Allows users to select a specific section of their resume and open a chat interface to get AI-powered suggestions for improving the wording, tone, or impact of that section. The user decides whether to apply any suggestion — nothing is auto-applied.

---

## Data Model

### Conversation Table
| Field | Type | Notes |
|---|---|---|
| id | uuid | Primary key |
| user_id | uuid | Foreign key → User Table |
| resume_id | uuid | Foreign key → Resume Table |
| section_heading | string | The Markdown heading of the targeted section |
| created_at | timestamp | |

### Message Table
| Field | Type | Notes |
|---|---|---|
| id | uuid | Primary key |
| conversation_id | uuid | Foreign key → Conversation Table |
| role | enum | `user`, `assistant` |
| content | text | |
| created_at | timestamp | |

---

## API Contract

| Method | Endpoint | Description |
|---|---|---|
| POST | `/ai/chat` | Send a message; returns AI response |
| GET | `/ai/conversations/:resumeId` | List past conversations for a resume |
| GET | `/ai/conversations/:id/messages` | Get messages in a conversation |

### POST `/ai/chat` — Request Body
```json
{
  "resume_id": "uuid",
  "section_heading": "## Experience",
  "section_content": "...",
  "message": "Make this sound more impactful"
}
```

---

## Key Technical Decisions
- AI calls are always server-side — the API key is never exposed to the client
- The AI receives only the targeted section content, not the full resume (keeps context focused and cost-efficient)
- Phase 1: external LLM via API key (provider TBD)
- Phase 2: AWS Bedrock + Supabase Vector for RAG over past resume content

---

## Open Questions
- Which AI provider for Phase 1?
- Should the full resume be passed as background context alongside the section?
- Should conversations persist, or be ephemeral per session?
- Should the user be able to provide a target job description for more tailored suggestions?
