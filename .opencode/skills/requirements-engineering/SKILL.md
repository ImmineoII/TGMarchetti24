---
name: requirements-engineering
description: Use when gathering, analyzing, and documenting software requirements. Covers user stories, acceptance criteria, functional and non-functional requirements, use cases, and requirements traceability matrix. Trigger keywords: requirements, user stories, acceptance criteria, functional requirements, non-functional requirements, use cases, PRD, product requirements document, epics, features.
---

# Requirements Engineering

## Goal
Gather, analyze, and document software requirements in a precise, complete, and traceable manner, creating the foundation for all subsequent development phases.

## Requirements gathering process

### 1. Domain analysis
- Identify stakeholders and key users
- Understand the business context and objectives
- Analyze existing systems and constraints
- Identify integrations with external systems

### 2. Requirements document structure (PRD)

```markdown
# Product Requirements Document (PRD)

## 1. Executive Summary
- Product objective
- Problem being solved
- Value for users

## 2. Stakeholders
- Primary stakeholders
- Secondary stakeholders
- End users and personas

## 3. Functional Requirements
### FR-XXX: [Requirement Name]
- **Description**: ...
- **Priority**: Must/Should/Could/Won't (MoSCoW)
- **Actors**: ...
- **Preconditions**: ...
- **Main flow**: ...
- **Alternative flows**: ...
- **Acceptance criteria**: ...

## 4. Non-Functional Requirements
### NFR-XXX: [Name]
- **Category**: Performance | Security | Usability | Reliability | Scalability
- **Description**: ...
- **Metric**: ...
- **Target**: ...

## 5. User Stories
### US-XXX: [Title]
**As a** [user type]
**I want** [feature]
**So that** [benefit/value]

**Acceptance criteria:**
- [ ] Given... When... Then...
- [ ] ...

**Definition of Done:**
- [ ] Code written and reviewed
- [ ] Unit tests passing (coverage >80%)
- [ ] Integration tests passing
- [ ] Documentation updated
- [ ] Deployed to staging

## 6. Use Cases
### UC-XXX: [Use Case Name]
- **Actors**: ...
- **Preconditions**: ...
- **Main flow**: ...
- **Alternative flows**: ...
- **Postconditions**: ...

## 7. Constraints and Assumptions
- Technical constraints
- Business constraints
- Assumptions

## 8. Glossary
- Term: definition

## 9. Traceability Matrix
| Requirement | User Story | Test Case | Status |
|-------------|------------|-----------|--------|
```

### 3. Requirements quality criteria (SMART)
- **S**pecific: clear and unambiguous
- **M**easurable: with verifiable criteria
- **A**chievable: technically feasible
- **R**elevant: aligned with objectives
- **T**ime-bound: with priority and timeline

### 4. Expected output for the next phase
Upon completion, produce:
1. `docs/requirements/PRD.md` - complete requirements document
2. `docs/requirements/user-stories.md` - user story backlog
3. `docs/requirements/glossary.md` - domain glossary
4. `docs/requirements/constraints.md` - constraints and assumptions

## Handoff to Architect
Signal to the Architect:
- Scalability and performance requirements
- Security and compliance requirements
- External system integrations
- Technology constraints imposed by the business
- Identified technical risk areas

## Final checklist
- [ ] All requirements have a unique ID
- [ ] All requirements have MoSCoW priority
- [ ] All user stories have acceptance criteria
- [ ] No ambiguous or contradictory requirements
- [ ] Non-functional requirements have measurable metrics
- [ ] Complete glossary of domain terms
- [ ] Stakeholder approval obtained
