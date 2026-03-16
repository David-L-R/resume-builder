# Technical Decisions

A record of key technical choices made in this project and the reasoning behind them.

---

## Language — TypeScript

Used across both client and API. Provides type safety at compile time, better IDE support, and catches contract mismatches between frontend and backend early.

---

## Client — React + React Router 7

React is the UI framework. React Router 7 handles client-side routing. Hosted on Vercel.

React Router 7 introduces a file-based routing convention and built-in data loading patterns, reducing boilerplate for route-level data fetching.

---

## API — Node + Express

Lightweight, well-understood, easy to extend. Runs on AWS EC2. Acts as the single backend entry point — handles auth validation, resume CRUD, PDF parsing, and proxying AI requests.

---

## Database — Supabase (Postgres)

Supabase provides a managed Postgres instance with a built-in REST and realtime API. Row Level Security (RLS) policies enforce that users can only access their own data at the database layer.

---

## Authentication — Supabase Auth

Supabase Auth handles session management, magic link emails, and Google OAuth natively. Canva OAuth requires a custom integration and will need additional work.

Deliberately no email/password auth — passwordless and OAuth only.

---

## File Storage — Supabase Storage

Used to store raw uploaded PDF files. Keeps file storage co-located with the database provider, simplifying access control.

---

## AI Provider

**Phase 1:** External LLM API called via API key (provider TBD). Key is stored server-side only.

**Phase 2:** Migrate to AWS Bedrock for managed, AWS-native inference.

---

## Vector Store — Supabase Vector

Supabase's pgvector extension stores resume embeddings for semantic search. Used by the AI service in Phase 2 to provide retrieval-augmented generation (RAG) context.

---

## Gateway — Ngrok (AWS)

*To be decided:* Ngrok is used as the gateway entry point. Clarify whether this is a development tunnel or intended for production — may be replaced with AWS API Gateway or an Application Load Balancer (ALB) before launch.
