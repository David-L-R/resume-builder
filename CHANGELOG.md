# Changelog

## [0.0.0] - 2026-03-16 (Pre-Alpha)

### Added

#### Documentation
- `docs/architecture.md` — full system architecture covering infrastructure zones (Vercel, AWS EC2, Supabase), component responsibilities, and request flows per feature
- `docs/decisions.md` — record of key technical decisions: TypeScript, React Router 7, Node/Express, Supabase (Postgres, Auth, Storage, Vector), passwordless auth, and AI provider strategy (Phase 1 → Bedrock)

#### Feature Design
- `docs/features/auth.md` — auth feature spec (magic link, Google OAuth, Canva OAuth)
- `docs/features/markdown-editor.md` — markdown editor feature spec
- `docs/features/ai-assistant.md` — AI assistant feature spec
- `docs/features/resume-versions.md` — resume version history feature spec
- `docs/features/pdf-upload.md` — PDF upload feature spec

#### Tooling
- `.claude/agents/builder-agent.md` — project-specific Claude Code agent that reads architecture and decisions docs before executing any code
- `docs/agents/builder-agent.md` — copy of the builder agent for team reference and onboarding
