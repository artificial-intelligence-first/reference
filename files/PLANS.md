---
title: PLANS.md Guide (ExecPlans)
slug: plans
status: living
last_updated: 2025-10-23
tags: [plans, execplans, project-management, documentation, workflow]
summary: "ExecPlan workflow for documenting and tracking complex, multi-step development initiatives."
sources:
  - { id: R1, title: "OpenAI Cookbook - Exec Plans", url: "https://cookbook.openai.com/articles/codex_exec_plans", accessed: "2025-10-23" }
  - { id: R2, title: "Agile User Stories - Mountain Goat Software", url: "https://www.mountaingoatsoftware.com/agile/user-stories", accessed: "2025-10-23" }
  - { id: R3, title: "Architecture Decision Records (ADR)", url: "https://adr.github.io/", accessed: "2025-10-23" }
---

# PLANS.md Guide (ExecPlans)

> **For Humans**: This guide explains the ExecPlan workflow for documenting multi-step initiatives, inspired by OpenAI Cookbook patterns<sup>[1](#footnote-openai-cookbook)</sup>.
>
> **For AI Agents**: ExecPlans are execution specifications that keep multi-hour coding tasks aligned. Maintain these documents throughout work execution, updating progress, decisions, and discoveries in real-time.

## ExecPlan in Five Minutes

**What**: A document that tracks complex, multi-step initiatives from start to finish.

**When**: Create an ExecPlan for tasks spanning multiple hours, involving multiple phases, or requiring decision documentation.

**Essential Sections**:
- **To-do**: Checklist of tasks with checkboxes
- **Progress**: Timestamped updates of completed work (UTC)
- **Decision Log**: Major decisions with rationale
- **Surprises & Discoveries**: Unexpected findings and learnings

**Workflow**: Create → Plan → Operate (update continuously) → Validate → Close

**Key Principle**: Anyone should be able to resume the work using only the ExecPlan and the working tree.

---

## Overview

ExecPlans (Execution Plans) are structured documents that serve as the single source of truth for complex, multi-step development initiatives. They ensure that:

- Progress remains observable to both humans and AI agents
- Decisions are documented with rationale
- Any contributor can resume work from the plan alone
- Surprises and discoveries are captured for learning
- Validation criteria are clear and actionable

**Key Principle**: An ExecPlan must be self-contained—a new contributor should be able to complete the task using only the working tree and the document.

## When to Create an ExecPlan

Create an ExecPlan when:

- Work spans multiple hours or sessions
- The task involves multiple milestones or phases
- Multiple agents or humans collaborate on the same initiative
- You receive an `exec plan` command from a user
- Decisions need to be traceable for future reference
- The task is complex enough that resuming requires context restoration

**Don't create ExecPlans for**:
- Simple bug fixes
- Single-file changes
- Trivial refactoring
- Work that completes in under an hour

## Document Structure

### Required Sections

Every ExecPlan must include these canonical sections:

```markdown
# <Short, action-oriented description>

Keep Progress, Surprises & Discoveries, Decision Log, and Outcomes & Retrospective current as work proceeds.

## Purpose / Big Picture
*Why this work matters, what problem it solves, and what success looks like*

## To-do
*Actionable checklist with checkboxes, organized by phase or priority*
- [ ] Task 1
- [ ] Task 2
  - [ ] Subtask 2a
  - [ ] Subtask 2b

## Progress
*Timestamped updates (UTC) tracking what has been completed*
- [YYYY-MM-DD HH:MM UTC] Completed initial setup
- [YYYY-MM-DD HH:MM UTC] Implemented core functionality

## Surprises & Discoveries
*Unexpected findings, blockers, workarounds, and learnings*
- [YYYY-MM-DD HH:MM UTC] Discovered existing utility function that simplifies implementation

## Decision Log
*Major decisions with rationale, alternatives considered, and outcomes*
- [YYYY-MM-DD HH:MM UTC] Chose library X over Y because of better TypeScript support

## Outcomes & Retrospective
*Final results, lessons learned, and follow-up items*
(Filled in when work completes)

## Context and Orientation
*Background information, links to related docs, repository structure notes*

## Plan of Work
*High-level strategy, phases, dependencies, risk assessment*

## Concrete Steps
*Detailed implementation instructions, commands to run, files to modify*

## Validation and Acceptance
*How to verify success: test commands, expected outputs, acceptance criteria*

## Idempotence and Recovery
*How to retry, rollback, or resume if something fails*

## Artifacts and Notes
*Links to PRs, issues, significant diffs, meeting notes, design docs*

## Interfaces and Dependencies
*APIs touched, external services, other teams/systems involved, integration points*
```

## Best Practices

### For Repository Maintainers

1. **Store in consistent location** - Use `collab/execplans/` or similar designated directory
2. **Name descriptively** - Use kebab-case slugs: `implement-user-authentication.md`
3. **Update in real-time** - Don't batch updates; refresh as work progresses
4. **Link from AGENTS.md** - Reference ExecPlan workflow in project documentation
5. **Archive completed plans** - Keep for historical reference, mark as completed

### For AI Agents Maintaining ExecPlans

1. **Update Progress immediately** - After each significant action, add timestamped entry
2. **Document all decisions** - Even small choices should be logged with brief rationale
3. **Capture surprises** - Unexpected behaviors, blockers, or helpful discoveries
4. **Check off To-dos** - Mark items complete as you finish them
5. **Keep Concrete Steps current** - If implementation deviates, update the plan
6. **Validate continuously** - Run validation steps and document results

## Lifecycle Workflow

### 1. Initiate

```markdown
## Purpose / Big Picture
Implement OAuth2 authentication to replace legacy password-only login

## Context and Orientation
- Current auth: `/api/auth/login` with username/password
- OAuth providers: GitHub, Google, Microsoft
- Security requirements in `docs/security-policy.md`
```

### 2. Plan

```markdown
## Plan of Work
1. Research OAuth2 libraries (evaluate Passport.js, Auth.js)
2. Set up OAuth provider registrations
3. Implement OAuth flow endpoints
4. Update database schema for OAuth tokens
5. Migrate existing users
6. Update frontend login UI
7. Test and validate

## To-do
- [ ] Research OAuth2 libraries
- [ ] Register with GitHub OAuth
- [ ] Register with Google OAuth
- [ ] Implement `/auth/oauth/callback` endpoint
...
```

### 3. Operate *(Sample flow—adjust to your team)*

```markdown
## Progress
- [2025-01-20 14:00 UTC] Evaluated Passport.js and Auth.js
- [2025-01-20 14:30 UTC] Registered GitHub OAuth app (Client ID: xxxxx)
- [2025-01-20 15:00 UTC] Implemented callback endpoint

## Decision Log
- [2025-01-20 14:15 UTC] Chose Auth.js over Passport.js
  - Rationale: Better TypeScript support, active maintenance, simpler API
  - Alternatives: Passport.js (more mature but TypeScript types incomplete)

## Surprises & Discoveries
- [2025-01-20 14:45 UTC] GitHub OAuth requires separate client_id for dev/prod
- [2025-01-20 15:30 UTC] Found existing `TokenService` that handles refresh logic
```

### 4. Validate *(Sample criteria)*

```markdown
## Validation and Acceptance
- [ ] OAuth flow completes successfully for GitHub
- [ ] OAuth flow completes successfully for Google
- [ ] Existing password login still works
- [ ] User sessions persist across OAuth refresh
- [ ] Tests pass: `npm test -- auth`
- [ ] Security scan clean: `npm run security-audit`

## Concrete Steps
```bash
# Test OAuth flow
curl -X GET http://localhost:3000/auth/oauth/github
# Should redirect to GitHub authorization

# Run test suite
npm test -- auth.test.ts

# Expected: All tests pass, coverage > 80%
```
```

### 5. Close *(Sample retrospective)*

```markdown
## Outcomes & Retrospective
- Successfully implemented OAuth2 authentication
- All 3 providers (GitHub, Google, Microsoft) working
- Existing users can link OAuth accounts without losing data
- Test coverage: 87%

**What went well:**
- Auth.js library was easy to integrate
- Existing TokenService saved implementation time

**What could improve:**
- OAuth provider registration process was confusing
- Should have created migration script earlier

**Follow-up items:**
- Monitor OAuth error rates in production
- Add user documentation for account linking
- Consider adding SAML support (see issue #234)
```

## Writing Effective Sections

### Purpose / Big Picture

- **Do**: "Enable users to log in with GitHub/Google accounts to reduce password fatigue and improve security"
- **Don't**: "Add OAuth"

### To-do

- **Do**: Use actionable checkboxes with clear completion criteria
```markdown
- [ ] Implement OAuth callback endpoint that handles authorization code exchange
- [ ] Add database migration for oauth_tokens table
```
- **Don't**: Use vague items
```markdown
- [ ] OAuth stuff
- [ ] Database
```

### Progress

- **Do**: Timestamp (UTC), specific action, outcome
```markdown
- [2025-01-20 14:30 UTC] Completed database migration, added oauth_tokens table with provider/user_id/access_token columns
```
- **Don't**: Undated or vague entries
```markdown
- Did some database work
```

### Decision Log

- **Do**: Include decision, rationale, alternatives, outcome
```markdown
- [2025-01-20 14:00 UTC] Chose PostgreSQL JSONB for token storage
  - Rationale: Flexible schema for different provider token formats
  - Alternatives considered: Separate columns (too rigid), encrypted TEXT (harder to query)
  - Outcome: Implemented with proper indexes on provider + user_id
```

### Surprises & Discoveries

- **Do**: Document unexpected findings with timestamps and impact
```markdown
- [2025-01-20 15:30 UTC] Discovered GitHub OAuth rate limits apply per client_id
  - Impact: Need separate dev/prod OAuth apps
  - Workaround: Registered both, using env var to switch
```

## Integration with Other Conventions

### ExecPlans + AGENTS.md

- AGENTS.md provides standing procedures (how to test, lint, PR)
- ExecPlans reference AGENTS.md for standard practices
- Example: "Run validation following AGENTS.md testing instructions"

### ExecPlans + CHANGELOG.md

- ExecPlans track detailed implementation decisions
- CHANGELOG.md distills user-facing impact
- When closing ExecPlan, summarize in CHANGELOG.md Unreleased section

### ExecPlans + SSOT.md

- SSOT.md defines canonical terms and data contracts
- ExecPlans use SSOT terminology consistently
- When ExecPlan introduces new concepts, update SSOT.md

## Common Patterns

### Parallel Implementation Paths

```markdown
## Decision Log
- [2025-01-20 14:00 UTC] Pursuing parallel spike for two approaches
  - Path A: Use existing AuthService with OAuth adapter
  - Path B: Replace AuthService with Auth.js completely
  - Validation: Whichever completes tests first with <100 LOC wins
  - Converge by: 2025-01-21 EOD
```

### Handling Blockers

```markdown
## Surprises & Discoveries
- [2025-01-20 16:00 UTC] BLOCKER: Google OAuth requires domain verification
  - Impact: Cannot complete Google integration in dev environment
  - Workaround: Using localhost override for testing
  - Resolution plan: Submitted domain verification, ETA 2-3 business days
  - Updated To-do: Marked Google OAuth as blocked, proceeding with GitHub

## To-do
- [x] GitHub OAuth integration
- [ ] ⏸️ Google OAuth integration (BLOCKED: awaiting domain verification)
- [ ] Microsoft OAuth integration
```

### Prototype/Spike Milestones

```markdown
## Plan of Work
Phase 1: Spike (2 hours max)
- Quick proof-of-concept with Auth.js
- Validate token flow with GitHub only
- Decision point: Continue or pivot to alternative

Phase 2: Full Implementation (if spike succeeds)
- Add all providers
- Database integration
- Production hardening
```

## Automation Integration

### Creating ExecPlans

```bash
# Generate starter ExecPlan
uv run -m automation.execplan --task-name "Implement OAuth Authentication"

# Output: collab/execplans/implement-oauth-authentication.md
```

### Compliance Checks

```bash
# Verify ExecPlan exists for current work
uv run -m automation.compliance.pre

# Fails if no active ExecPlan found in collab/execplans/
```

### Linking to Issues/PRs

```markdown
## Artifacts and Notes
- Original feature request: #234
- Design discussion: #235
- Implementation PR: #236
- Security review: #237
```

## Advanced Topics

### Multi-Agent Collaboration

```markdown
## To-do
Backend (assigned: Backend-Agent)
- [ ] Implement OAuth endpoints
- [ ] Add database migrations

Frontend (assigned: Frontend-Agent)
- [ ] Update login UI
- [ ] Add OAuth button components

DevOps (assigned: DevOps-Agent)
- [ ] Register OAuth apps in production
- [ ] Update environment variables
```

### Idempotence Example

```markdown
## Idempotence and Recovery

### Safe to re-run
```bash
# Database migration uses `IF NOT EXISTS`
npm run migrate:up

# OAuth registration is idempotent
npm run oauth:register
```

### Rollback procedure
```bash
# Revert migration
npm run migrate:down

# Remove OAuth configuration
npm run oauth:unregister

# Restore previous auth endpoints
git checkout HEAD~1 -- src/auth/
```
```

## Template for New ExecPlans

```markdown
# [Action-Oriented Title]

Keep Progress, Surprises & Discoveries, Decision Log, and Outcomes & Retrospective current as work proceeds.

## Purpose / Big Picture


## To-do
- [ ]

## Progress


## Surprises & Discoveries


## Decision Log


## Outcomes & Retrospective


## Context and Orientation


## Plan of Work


## Concrete Steps


## Validation and Acceptance


## Idempotence and Recovery


## Artifacts and Notes


## Interfaces and Dependencies


---
**Created**: [YYYY-MM-DD HH:MM UTC]
**Last Updated**: [YYYY-MM-DD HH:MM UTC]
```

## Adoption Checklist

- [ ] Create `collab/execplans/` directory
- [ ] Add ExecPlan workflow to AGENTS.md
- [ ] Create template file for new plans
- [ ] Set up automation for plan generation (optional)
- [ ] Configure compliance checks (optional)
- [ ] Train team on ExecPlan maintenance
- [ ] Establish review cadence for active plans
- [ ] Define archival process for completed plans
- [ ] Link ExecPlan workflow from README.md

## Further Resources

- [OpenAI Cookbook: ExecPlans](https://cookbook.openai.com/articles/codex_exec_plans) [R1]
- [Agile User Stories](https://www.mountaingoatsoftware.com/agile/user-stories) [R2] - Task decomposition patterns
- [Architecture Decision Records](https://adr.github.io/) [R3] - Decision documentation best practices
- Community examples and adaptations
- Integration with project management tools

## References

- [R1] [OpenAI Cookbook - Exec Plans](https://cookbook.openai.com/articles/codex_exec_plans) - Original ExecPlan pattern and workflow documentation
- [R2] [Agile User Stories - Mountain Goat Software](https://www.mountaingoatsoftware.com/agile/user-stories) - Task decomposition and planning techniques
- [R3] [Architecture Decision Records (ADR)](https://adr.github.io/) - Decision documentation patterns and templates

## Update Log

- 2025-10-23: Added official references (OpenAI Cookbook, Agile User Stories, ADR) for ExecPlan patterns and decision documentation.
- 2025-10-20: Published the initial edition (summarizing community examples and the OpenAI Cookbook ExecPlan article).

---

<a name="footnote-openai-cookbook">1</a>: Reference: [OpenAI Cookbook – Exec Plans](https://cookbook.openai.com/articles/codex_exec_plans)

**Remember**: ExecPlans exist to maintain clarity and continuity. Update them continuously, write for both humans and agents, and keep them self-contained enough that anyone can resume the work.
