# OpenAI Profile

## Purpose
- Provide model-specific operating rules for implementation work in this repository.
- Keep all generated code aligned with the approved architecture and stack.

## Project Context
- Project: ClientHub
- Tech Stack: React, TypeScript, Node.js, Express, PostgreSQL, JWT, Prisma ORM, Tailwind CSS
- Architecture Summary: A full-stack web application utilizing a decoupled client-server architecture. The React frontend interacts with a RESTful Node.js API, while a PostgreSQL database handles relational data persistence, all secured via JWT-based authentication and a centralized middleware layer.
- Brief Snapshot: Project Name: ClientHub

Build a client management web app for small agencies to organize clients, projects, and communication notes.

Core features:

Create and manage clients and contacts
Track project status, deadlines, and ownership
Store meeting notes and follow-up actions
Activity timeline per client/project
Responsive UI for desktop and mobile
Tech stack:

Frontend: React + TypeScript
Backend: Node.js + Express
Database: PostgreSQL
Auth: JWT
Goal:
Deliver an MVP that lets teams manage client relationships and project updates in one place end-to-end.

## Repo Rules
- Use monorepo layout with `frontend/` and `backend/`.
- Mobile segment is in scope: include `mobile/` rules and deploy checks.
- Keep shared contracts explicit; do not break segment boundaries.
- Follow architecture decisions from `ARCHITECTURE.md` before introducing new patterns.

## Segment Rules
- Frontend work stays under `frontend/` with typed service calls and route-aware modules.
- Backend work stays under `backend/` with modular route/controller/service layers.
- Deployment logic is segment-based and triggered via `.github/workflows/deploy-segments.yml`.

## Do
- Ship small, reviewable patches tied to one clear behavior change.
- Update tests and run validation for touched segments.
- Call out assumptions and edge cases before high-impact changes.

## Don't
- Do not mix unrelated frontend/backend changes in a single patch unless required.
- Do not replace architecture conventions without explicit rationale.
- Do not leave deployment or environment assumptions undocumented.

## PR Checklist
- Scope limited to intended segment(s): frontend/backend/mobile.
- Tests updated or validation plan documented.
- Config/workflow impact reviewed (`.github/workflows/deploy-segments.yml`).
- Documentation/comments updated where behavior changed.

## Prompting Style
- Be concise and execution-focused; optimize for small, testable patches.
- Prioritize fast verification steps after each change.
- End with explicit next actions and any residual risks to verify.
