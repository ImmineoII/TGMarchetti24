---
name: sdlc-workflow
description: Use when orchestrating the full software development lifecycle (SDLC) loop across multiple agents: requirement-engineer, architect, frontend-dev, backend-dev, tester, code-reviewer. Covers workflow coordination, phase transitions, artifact handoff, and iteration management. Trigger keywords: SDLC, development loop, workflow, orchestration, phase, handoff, iteration, sprint, dev loop, software lifecycle.
---

# SDLC Workflow Orchestration

## Goal
Coordinate the workflow across all software development lifecycle agents, ensuring each phase produces the artifacts required by the next phase, and that the loop iterates until the desired quality is achieved.

## The SDLC Loop

```
┌─────────────────────────────────────────────────────────┐
│                    SDLC LOOP                             │
│                                                          │
│  1. REQUIREMENTS                                         │
│     requirement-engineer                                 │
│     Output: PRD, User Stories, Acceptance Criteria       │
│                    │                                     │
│                    ▼                                     │
│  2. ARCHITECTURE                                         │
│     architect                                            │
│     Output: SAD, ADR, API Contracts, DB Schema           │
│                    │                                     │
│                    ▼                                     │
│  3. IMPLEMENTATION (parallel)                            │
│     frontend-dev ◄──────────────────► backend-dev       │
│     Output: UI Components, Tests     Output: APIs, Tests │
│                    │                                     │
│                    ▼                                     │
│  4. TESTING                                              │
│     tester                                               │
│     Output: Test Results, Bug Reports                    │
│                    │                                     │
│          ┌─────────┴──────────┐                         │
│          │ Bugs found?         │                         │
│          ▼                    ▼                          │
│         YES                   NO                         │
│    Return to step 3       5. CODE REVIEW                 │
│    (with bug report)      code-reviewer                  │
│                          Output: Review, Approval        │
│                               │                          │
│                    ┌──────────┴──────────┐               │
│                    │ Changes requested?   │               │
│                    ▼                     ▼               │
│                   YES                    NO              │
│              Return to step 3      DONE / DEPLOY         │
│              (with feedback)                             │
└─────────────────────────────────────────────────────────┘
```

## Phases and artifacts

### Phase 1: Requirements (requirement-engineer)
**Input**: Customer idea/feature request
**Activities**:
- Interview stakeholders (simulate questions and answers)
- Write PRD
- Define User Stories with acceptance criteria
- Identify constraints and risks

**Required output**:
- `docs/requirements/PRD.md`
- `docs/requirements/user-stories.md`
- `docs/requirements/constraints.md`

**Exit gate**: All requirements have an ID, priority, and acceptance criteria

---

### Phase 2: Architecture (architect)
**Input**: PRD, User Stories, Constraints
**Activities**:
- Design system architecture
- Define API contracts
- Design database schema
- Write ADRs for key decisions

**Required output**:
- `docs/architecture/SAD.md`
- `docs/architecture/api-contract.yaml`
- `docs/architecture/database-schema.md`
- `docs/architecture/ADR/`

**Exit gate**: API contracts complete, DB schema validated, ADRs written

---

### Phase 3: Implementation (frontend-dev + backend-dev in parallel)
**Input**: SAD, API contracts, User Stories

**Backend-dev**:
- Implement API endpoints
- Implement business logic
- Database migration
- Unit and integration tests

**Frontend-dev**:
- Implement UI components
- Integrate with APIs (using the contracts)
- Unit and integration UI tests

**Required output**:
- Implemented and working code
- Tests with coverage >80%
- PR/Branch ready for review

**Exit gate**: All tests pass, coverage >80%

---

### Phase 4: Testing (tester)
**Input**: Implemented code, User Stories, Acceptance criteria
**Activities**:
- Write test plan
- Run tests for each acceptance criterion
- Run E2E tests
- Write bug reports for issues found

**Required output**:
- `docs/testing/test-plan.md`
- `docs/testing/test-results.md`
- `docs/testing/bug-reports.md` (if bugs present)

**Exit gate**: Zero critical bugs, all acceptance criteria verified

**If bugs found**: Create bug reports and send to frontend-dev or backend-dev for fixes, then return to Phase 4

---

### Phase 5: Code Review (code-reviewer)
**Input**: Complete code, test results, PR
**Activities**:
- Systematic code review
- Security review
- Performance review
- Quality and maintainability

**Required output**:
- `docs/review/code-review-[feature].md`

**Exit gate**: Review Approved (zero blocking issues)

**If changes requested**: Send feedback to frontend-dev or backend-dev, then return to Phase 4-5

---

## Loop state

The orchestrator must track:
```markdown
# SDLC Status - [Feature/Sprint Name]

## Current phase: [1-5]
## Iteration: [N]

## Artifacts produced:
- [x] PRD
- [x] User Stories
- [ ] Architecture SAD
- [ ] API Contracts
- ...

## Open issues:
- BUG-001: [description] - Assigned to: backend-dev
- REVIEW-001: [description] - Assigned to: frontend-dev

## Decisions made:
- ADR-001: ...
- ADR-002: ...
```

## Orchestration rules

1. **Do not advance to the next phase** until all gates are satisfied
2. **Always track** produced artifacts in `docs/sdlc-status.md`
3. **In case of bugs**: create a ticket with ID, severity, and steps to reproduce
4. **In case of review changes**: feedback must be specific and actionable
5. **Maximum 3 iterations** for the same phase before escalating to the user
6. **Document every significant decision** as an ADR

## Initial status template

At loop start, create:
```markdown
# SDLC Status

## Feature: [name]
## Start date: [date]
## Iteration: 1

## Phases:
- [ ] 1. Requirements (requirement-engineer)
- [ ] 2. Architecture (architect)
- [ ] 3. Implementation (frontend-dev + backend-dev)
- [ ] 4. Testing (tester)
- [ ] 5. Code Review (code-reviewer)

## Notes: ...
```
