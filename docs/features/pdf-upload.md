# Feature: PDF Upload and Import

## What It Does
Users can upload an existing PDF resume. The content is extracted and placed into the Markdown editor so the user can continue editing without retyping. The original PDF is stored and remains accessible.

---

## Data Model

### PDF Upload Table
| Field | Type | Notes |
|---|---|---|
| id | uuid | Primary key |
| user_id | uuid | Foreign key → User Table |
| resume_id | uuid | Foreign key → Resume Table (created on import) |
| storage_path | string | Path in Supabase Storage |
| original_filename | string | |
| parsed | boolean | Whether extraction succeeded |
| created_at | timestamp | |

---

## API Contract

| Method | Endpoint | Description |
|---|---|---|
| POST | `/resumes/upload` | Upload a PDF; returns extracted Markdown |
| GET | `/resumes/:id/pdf` | Get the original PDF download URL |

### POST `/resumes/upload` — Request
- `multipart/form-data` with a `file` field containing the PDF

### Response
```json
{
  "resume_id": "uuid",
  "markdown": "# Jane Doe\n\n## Experience\n...",
  "parsed": true,
  "warnings": ["Some formatting could not be preserved"]
}
```

---

## Key Technical Decisions
- PDF parsing happens server-side in the Resume Service — never on the client
- The raw PDF is stored in Supabase Storage regardless of parse success, so it is never lost
- Extracted content is placed into the editor as editable Markdown — the user is always able to correct it
- This is the highest-risk feature for user experience: parse quality varies widely depending on the source PDF

---

## Open Questions
- Which PDF parsing library to use? (e.g. `pdf-parse`, `pdfjs-dist`, or a third-party extraction API)
- How should scanned / image-based PDFs be handled? (OCR, or graceful failure with a message?)
- What is the maximum accepted file size?
- Should password-protected PDFs be rejected immediately with a clear error?
