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

| Database | ID | Collection | Who writes | Who reads |
|---|---|---|---|---|
| Requirements | `5a4ff18d-7a37-8357-b5bf-81ab50dacf06` | — | requirement-engineer | all agents |
| ADRs | `4f2f4e68-31fb-427a-9dbb-270f02c8f213` | — | architect | all agents |
| Epics | `fac09e246ca8475891999ef800a83237` | `collection://fedd88bf-913f-4381-9ae7-b4500f7ed814` | requirement-engineer, maestro | all agents |
| Stories | `cbd654962bd849c78cce28cf29b9454c` | `collection://39588308-94e0-4d9e-88f9-2f183d4f9ae4` | requirement-engineer, devs | all agents |
| Bugs | `a60045a296534310a16d09ad7ac4ca19` | `collection://3d06961f-c023-4130-83f8-55e724c6205b` | tester, devs | all agents |
| PDRs | `9b2ff18d-7a37-83bd-ad00-8161ba879217` | `collection://dd0ff18d-7a37-8297-b00c-0739a7f04e52` | architect | all agents |

### Epics schema

| Property | Type | Values |
|---|---|---|
| `Title` | title | Epic name |
| `Status` | status | `Not started`, `In progress`, `Done` |
| `Priority` | select | `EP0` (red), `EP1` (orange), `EP2` (yellow), `EP3` (gray) |
| `Description` | rich_text | Summary of the Epic |
| `Created` | created_time | Read-only |

### Stories schema

| Property | Type | Values |
|---|---|---|
| `Title` | title | Story name |
| `Status` | select | `Backlog`, `Ready`, `In progress`, `In review`, `Done`, `Rejected` |
| `Priority` | select | `SP0` (red), `SP1` (orange), `SP2` (yellow), `SP3` (gray) |
| `Epic` | relation | Links to Epics (`collection://fedd88bf-913f-4381-9ae7-b4500f7ed814`) |
| `Description` | rich_text | Story details |
| `Created` | created_time | Read-only |

### PDRs schema

| Property | Type | Values |
|---|---|---|
| `Title` | title | Decision name |
| `Status` | status | `Proposed`, `Accepted`, `Superseded`, `Rejected` |
| `Context` | rich_text | Business/product context driving the decision |
| `Decision` | rich_text | Chosen direction and justification |
| `Consequences` | rich_text | Impact on product, team, cost, timeline |
| `PDR ID` | auto_increment_id | Read-only |
| `Created` | created_time | Read-only |

### Bugs schema

| Property | Type | Values |
|---|---|---|
| `Title` | title | Bug name |
| `Status` | select | `Backlog`, `Ready`, `In progress`, `In review`, `Done`, `Rejected` |
| `Priority` | select | `P0` (red), `P1` (orange), `P2` (yellow), `P3` (gray) |
| `Story` | relation | Links to Stories (`collection://39588308-94e0-4d9e-88f9-2f183d4f9ae4`) |
| `Description` | rich_text | Bug details |
| `Created` | created_time | Read-only |

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
