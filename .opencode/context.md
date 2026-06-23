# Project Context

> This file is read by all agents before starting any work. It defines the project structure, conventions, and tooling. Keep it up to date as the project evolves.

---

## Workspace structure

```
agentic-dev/
├── frontend/          # Frontend application
├── backend/           # Backend application
├── .opencode/         # Agent configuration (skills, agents, context)
├── .env               # Environment variables (never commit)
└── opencode.json      # OpenCode configuration
```

---

## Tech stack

> To be defined after requirements phase.

| Layer | Technology | Notes |
|---|---|---|
| Frontend | TBD | — |
| Backend | TBD | — |
| Database | TBD | — |
| Auth | TBD | — |
| Hosting | TBD | — |

---

## Branch strategy

- **Main branch**: `main`
- **Feature branches**: `feature/STORYID-short-description`
  - Example: `feature/US-001-user-login`
  - Story ID comes from the Notion Requirements database
  - Description: lowercase, hyphen-separated, max 5 words
- **Never commit directly to `main`**
- **One branch per Story** — covers both frontend and backend changes for that story
- After code review is approved, branch is merged to `main` and deleted

---

## Commit conventions

Format: `type(STORYID): description`

| Type | When to use |
|---|---|
| `feat` | New feature |
| `fix` | Bug fix |
| `test` | Adding or fixing tests |
| `refactor` | Code change without behavior change |
| `docs` | Documentation only |
| `chore` | Tooling, config, dependencies |

Examples:
- `feat(US-001): add login form component`
- `fix(US-003): handle empty email validation`
- `test(US-001): add unit tests for auth hook`

---

## Notion databases

| Database | ID | Who writes | Who reads |
|---|---|---|---|
| Requirements | `5a4ff18d-7a37-8357-b5bf-81ab50dacf06` | requirement-engineer | all agents |
| ADRs | `4f2f4e68-31fb-427a-9dbb-270f02c8f213` | architect | all agents |
| PDRs | `9b2ff18d-7a37-83bd-ad00-8161ba879217` | architect | all agents |
| SDLC Tracker | `88120587-36b1-452b-a9d4-02451a5dfd3e` | orchestrator, devs | all agents |

## Notion Docs pages

| Page | ID | Owner |
|---|---|---|
| Onboarding | `388ff18d-7a37-8164-a5c9-f276d438f01d` | requirement-engineer |
| Architecture Overview | `388ff18d-7a37-81e5-b855-ddc63284bdc5` | architect |
| Decisions Log | `388ff18d-7a37-8102-9a5c-c868a344afa3` | architect, code-reviewer |
| API Reference | `388ff18d-7a37-81e5-8195-e41e4fa91e56` | frontend-dev, backend-dev |
| Guides & How-tos | `388ff18d-7a37-8109-98b7-d289eb849299` | frontend-dev, backend-dev |
| Changelog | `388ff18d-7a37-81c4-bd6d-d0ebf3f4cd3c` | tester, code-reviewer |

---

## Environment variables

Stored in `.env` at the workspace root. Never commit this file.

| Variable | Used by | Description |
|---|---|---|
| `NOTION_TOKEN` | opencode | Notion integration token |

> Add new variables here as the project grows.

---

## How to run (TBD after stack is decided)

```bash
# Frontend
cd frontend
# TBD

# Backend
cd backend
# TBD
```

---

## Key decisions log

> Updated by agents as decisions are made. Also mirrored in Notion Decisions Log.

| Date | Decision | Reason |
|---|---|---|
| — | Stack TBD | Waiting for requirements phase |
