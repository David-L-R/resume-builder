# Architecture

## Overview

The system is split across three infrastructure zones: **Vercel** (client), **AWS** (backend services), and **Supabase** (data + auth). All client traffic enters through a single gateway.

---

## Infrastructure Map

```
Client
  │
  ▼
Gateway (Ngrok / AWS)
  │
  ├──▶ Auth Service (EC2) ──────────────▶ Supabase Auth (Auth Table + User Table)
  │
  ├──▶ AI Service (EC2) ───────────────▶ AWS Bedrock
  │                                            │
  │                                            └──▶ Supabase Vector (Resume DB)
  │
  └──▶ Resume Service (EC2) ──────────▶ Supabase Postgres (Resume DB)


Vercel
  └──▶ React Server (React Router 7)
```

---

## Components

### Client
The browser-based user. Communicates exclusively through the gateway.

---

### Gateway — Ngrok (AWS)
Single entry point for all API traffic. Routes requests to the appropriate backend service running on EC2.

---

### AWS EC2 — Backend Services

Three services run on EC2, each with a distinct responsibility:

#### Auth Service
Handles all authentication flows — magic link, Google OAuth, and Canva OAuth. Reads and writes to the Supabase Auth tables.

#### AI Service
Receives resume section content from the client and calls an external LLM provider to generate improvement suggestions. In Phase 1 this is an external API (via API key); in Phase 2 this migrates to AWS Bedrock. Also reads from the Supabase Vector store to provide context-aware responses (e.g. similarity search over past resume content) — planned for Phase 2.

#### Resume Service
All CRUD operations for resumes and their versions. Reads and writes to the Supabase Postgres database.

---

### Vercel — React Server
The frontend application, served via Vercel. Built with React and uses **React Router 7** for routing.

---

### AI Provider

**Phase 1:** An external LLM provider (e.g. Anthropic, OpenAI) called via API key from the AI Service. The API key is stored server-side and never exposed to the client.

**Phase 2:** Migrate to AWS Bedrock — AWS's managed LLM service — to keep inference within the AWS infrastructure.

---

### Supabase (Authentication)
Manages user identity. Contains two tables:
- **Auth Table** — sessions, tokens, OAuth provider links
- **User Table** — user profile data

---

### Supabase (Postgres)
Relational database. Contains the **Resume DB** — stores resumes and all saved versions, keyed by user.

---

### Supabase (Vector)
Vector database. Contains an embedding representation of resume content, used by the AI Service and Bedrock for semantic search and context retrieval.

---

## Request Flow by Feature

| Feature | Path |
|---|---|
| Login | Client → Gateway → Auth Service → Supabase Auth |
| Load / save resume | Client → Gateway → Resume Service → Supabase Postgres |
| AI section assist | Client → Gateway → AI Service → Bedrock → (Supabase Vector) |
| Upload PDF | Client → Gateway → Resume Service → Supabase Postgres |
| View version history | Client → Gateway → Resume Service → Supabase Postgres |
