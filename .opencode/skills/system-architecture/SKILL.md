---
name: system-architecture
description: Use when designing software architecture, system design, technical decisions, component diagrams, API contracts, database schema, infrastructure planning, and technology stack selection. Trigger keywords: architecture, system design, components, services, microservices, API design, database schema, infrastructure, tech stack, ADR, architectural decision record, scalability design.
---

# System Architecture

## Goal
Design a robust, scalable, and maintainable software architecture from requirements, producing technical artifacts that guide frontend dev and backend dev through implementation.

## Architectural design process

### 1. Architectural requirements analysis
- Review PRD and non-functional requirements
- Identify quality attributes (scalability, availability, security)
- Identify technical and business constraints
- Evaluate technical risks

### 2. Architectural Decision Records (ADR)

```markdown
# ADR-XXX: [Decision Title]

## Status
Proposed | Accepted | Deprecated | Superseded

## Context
[Situation requiring the decision]

## Options considered
### Option 1: [Name]
- Pros: ...
- Cons: ...

### Option 2: [Name]
- Pros: ...
- Cons: ...

## Decision
[Chosen option and rationale]

## Consequences
[Positive and negative impact of the decision]
```

### 3. Software Architecture Document (SAD)

```markdown
# Software Architecture Document (SAD)

## 1. Overview
- Architectural style (monolith | microservices | serverless | event-driven)
- Guiding principles (SOLID, DRY, KISS, separation of concerns)

## 2. System Components
### [Component Name]
- Responsibilities
- Exposed interfaces
- Dependencies
- Technology stack

## 3. Diagrams
- Context diagram (C4 Level 1)
- Container diagram (C4 Level 2)
- Component diagram (C4 Level 3)
- Sequence diagrams for critical flows

## 4. API Contracts
### REST
- Endpoint: METHOD /path
- Request schema
- Response schema
- Error codes

### OpenAPI/GraphQL schema

## 5. Data Architecture
- Database schema (ERD)
- Data access strategies
- Caching strategy
- Data migration plan

## 6. Infrastructure
- Deployment topology
- Networking
- CI/CD pipeline overview
- Monitoring and logging

## 7. Security Architecture
- Authentication/Authorization
- Data encryption (at rest, in transit)
- Network security

## 8. Scalability & Performance
- Scaling strategy (horizontal/vertical)
- Performance targets
- Anticipated bottlenecks
```

### 4. Detailed API contracts

For each endpoint define:
```yaml
# api-contract.yaml
openapi: 3.0.0
paths:
  /resource:
    post:
      summary: ...
      requestBody:
        schema:
          $ref: '#/components/schemas/ResourceInput'
      responses:
        '201':
          schema:
            $ref: '#/components/schemas/Resource'
        '400':
          schema:
            $ref: '#/components/schemas/Error'
```

### 5. Database Schema

Define for each entity:
- Tables/Collections with fields and types
- Relationships and foreign keys
- Indexes and optimizations
- Partitioning strategies if necessary

## Expected output

1. `docs/architecture/SAD.md` - architecture document
2. `docs/architecture/ADR/` - folder with all ADRs
3. `docs/architecture/api-contract.yaml` - OpenAPI spec
4. `docs/architecture/database-schema.md` - ERD and schema
5. `docs/architecture/diagrams/` - C4 diagrams

## Handoff to Frontend Dev
- Complete API contracts with request/response examples
- Shared data models (DTOs)
- Client-side authentication requirements
- URLs and environment configuration

## Handoff to Backend Dev
- Service architecture and responsibilities
- Complete database schema
- Patterns to use (repository, CQRS, event sourcing)
- Performance and scalability requirements

## Final checklist
- [ ] All functional requirements covered by the architecture
- [ ] All non-functional requirements addressed
- [ ] ADRs written for significant decisions
- [ ] API contracts complete and validated
- [ ] Database schema normalized
- [ ] Security review completed
- [ ] Performance analysis carried out
- [ ] Scalability plan defined
