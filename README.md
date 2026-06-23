# TGMarchetti24

> A news web platform that aggregates articles from external feeds, categorizes them by topic, and delivers personalized news to users based on their interests. Includes user registration, authentication, and persistent preference storage.
>
> Product description and features are tracked in [Notion → Docs → Product Description](https://app.notion.com/p/Product-Description-388ff18d7a37817b8052fa7dab31e4af).

---

## Project structure

```
agentic-dev/
├── frontend/          # Frontend application
├── backend/           # Backend application
├── .opencode/         # AI agent configuration (skills, agents, context)
├── .env               # Environment variables — never commit
└── opencode.json      # OpenCode configuration
```

---

## Development workflow

This project uses [OpenCode](https://opencode.ai) with a full SDLC agent setup. Development is driven by the **Maestro** agent which coordinates the full lifecycle:

```
Requirements → Architecture → Implementation → Testing → Code Review
```

### Agents

| Agent                  | Role                                                 |
| ---------------------- | ---------------------------------------------------- |
| `maestro`              | Orchestrates the full SDLC loop                      |
| `requirement-engineer` | Gathers requirements, writes user stories in Notion  |
| `architect`            | Designs architecture, writes ADRs and PDRs in Notion |
| `frontend-dev`         | Implements UI features                               |
| `backend-dev`          | Implements API and business logic                    |
| `tester`               | Validates acceptance criteria, writes E2E tests      |
| `code-reviewer`        | Reviews code, approves or requests changes           |

### Start a new feature

Open OpenCode and invoke the Maestro agent:

```
@maestro I want to build [feature description]
```

---

## Branch strategy

- **Main branch**: `main`
- **Feature branches**: `feature/STORYID-short-description`
  - Example: `feature/US-001-user-login`
- Never commit directly to `main`
- One branch per Story — covers both frontend and backend
- Branches are deleted after merge

---

## Commit conventions

Format: `type(STORYID): description`

| Type       | When                            |
| ---------- | ------------------------------- |
| `feat`     | New feature                     |
| `fix`      | Bug fix                         |
| `test`     | Adding or fixing tests          |
| `refactor` | Code change, no behavior change |
| `docs`     | Documentation only              |
| `chore`    | Tooling, config, dependencies   |

---

## Tech stack

> To be defined after the requirements phase.

---

## Setup

```bash
# 1. Clone the repo
git clone <repo-url>
cd agentic-dev

# 2. Copy environment variables
cp .env.example .env

# 3. Run frontend and backend
# (Instructions will be added after stack is decided)
```

### Notion authentication

OpenCode connects to Notion via MCP using a Notion integration token.

1. Go to [https://www.notion.so/profile/integrations](https://www.notion.so/profile/integrations) and create a new internal integration.
2. Copy the **Internal Integration Secret** (starts with `ntn_` or `secret_`).
3. Add it to your `.env` file:
   ```
   NOTION_TOKEN=<your-integration-secret>
   ```
4. In Notion, open each database or page the agents need to access, click **Connect to** (or the `...` menu → **Connections**), and add your integration.

5. Authenticate the Notion MCP server by running:
   ```bash
   opencode mcp auth notion
   ```

> **Before starting an OpenCode session**, make sure `NOTION_TOKEN` is set in your `.env` and that you have run `opencode mcp auth notion`. The token is loaded once at startup — if it is missing or wrong, the Notion MCP server will fail to authenticate and all Notion tools will be unavailable for the entire session. Restart OpenCode after fixing the token.

---

## Documentation

All project documentation lives in Notion:

| Section                                                                                              | Content                              |
| ---------------------------------------------------------------------------------------------------- | ------------------------------------ |
| [Product Description](https://app.notion.com/p/Product-Description-388ff18d7a37817b8052fa7dab31e4af) | What the product is and does         |
| [Onboarding](https://app.notion.com/p/388ff18d7a378164a5c9f276d438f01d)                              | Getting started for new team members |
| [Architecture Overview](https://app.notion.com/p/388ff18d7a3781e5b855ddc63284bdc5)                   | High-level system design             |
| [API Reference](https://app.notion.com/p/388ff18d7a3781e58195e41e4fa91e56)                           | Endpoint documentation               |
| [Guides & How-tos](https://app.notion.com/p/388ff18d7a378109-98b7d289eb849299)                       | Feature guides                       |
| [Changelog](https://app.notion.com/p/388ff18d7a3781c4bd6dd0ebf3f4cd3c)                               | Release notes                        |
| [Decisions Log](https://app.notion.com/p/388ff18d7a378102-9a5cc868a344afa3)                          | Key decisions made                   |
