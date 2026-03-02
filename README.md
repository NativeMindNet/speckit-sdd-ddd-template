# Document-Driven Development Template

A comprehensive framework for managing software development through structured documentation. This template supports **SDD**, **DDD**, **TDD**, **VDD** workflows and **ADR** (Architecture Decision Records), enabling AI-assisted development with full traceability from requirements to implementation.

## Overview

This template treats documentation as the primary development artifact, where code is the "last-mile" implementation derived from well-defined documents. It provides:

- **Resumability**: Continue work across sessions without context loss
- **Traceability**: Every implementation decision traces back to requirements
- **Iteration**: Refine documents before committing to code
- **Parallelization**: Multiple agents can work from the same documents
- **Client Communication**: Clear, accessible documentation for stakeholders
- **Decision History**: ADRs capture architectural decisions with full rationale

## Auto-loaded Context

The `CLAUDE.md` file is automatically loaded at session start, providing:
- ADR Index Summary (all decisions with status and links)
- Active flows list (SDD/DDD/TDD/VDD in progress)
- Quick navigation to documentation and commands

## Supported Workflows

### SDD (Spec-Driven Development)
Focus on specifications as the core artifact with 4 phases:

```
REQUIREMENTS → SPECIFICATIONS → PLAN → IMPLEMENTATION
```

### DDD (Document-Driven Development)
Extended workflow that includes client-facing documentation with 5 phases:

```
REQUIREMENTS → SPECIFICATIONS → PLAN → IMPLEMENTATION → DOCUMENTATION
```

**Key difference**: DDD adds a final documentation phase to create client-friendly README files explaining features in simple terms.

## Quick Start

### 1. Choose Your IDE
This template supports multiple AI-powered IDEs:
- **Claude Code**: Commands in `.claude/commands/`
- **Cursor**: Commands in `.cursor/prompts/commands/`
- **Qwen**: Commands in `.qwen/commands/`
- **Gemini**: Commands in `.gemini/commands/`

### 2. Start a New Flow

#### For SDD:
```
/sdd start [feature-name]
```

#### For DDD:
```
/ddd start [feature-name]
```

This creates a new flow directory under `flows/` with all necessary templates.

### 3. Work Through Phases

Each phase requires explicit approval before advancing:

1. **Requirements**: Define what and why
2. **Specifications**: Design how at architectural level
3. **Plan**: Break down into actionable tasks
4. **Implementation**: Execute and log progress
5. **Documentation** (DDD only): Create client-facing README

## Project Structure

```
.
├── CLAUDE.md                     # Auto-loaded context (ADR index, flows list)
├── flows/
│   ├── sdd.md                    # SDD flow documentation
│   ├── ddd.md                    # DDD flow documentation
│   ├── tdd.md                    # TDD flow documentation
│   ├── vdd.md                    # VDD flow documentation
│   ├── adr.md                    # ADR flow documentation
│   ├── adr-index.md            # Master index of all ADRs
│   ├── .templates/               # Templates for new flows
│   │   ├── sdd/                  # SDD templates
│   │   ├── ddd/                  # DDD templates
│   │   ├── tdd/                  # TDD templates
│   │   ├── vdd/                  # VDD templates
│   │   └── adr/                  # ADR templates
│   │       ├── adr.md            # Full ADR template
│   │       ├── lightweight.md    # Quick ADR template
│   │       └── _status.md        # Status tracking
│   ├── sdd-[feature]/            # SDD flow instances
│   ├── ddd-[feature]/            # DDD flow instances
│   ├── tdd-[feature]/            # TDD flow instances
│   ├── vdd-[feature]/            # VDD flow instances
│   └── adr-[NNN]-[name]/         # ADR instances (numbered)
│
├── .claude/commands/             # Claude Code commands
├── .cursor/prompts/commands/     # Cursor commands
├── .qwen/commands/               # Qwen commands
└── .gemini/commands/             # Gemini commands
```

## Available Commands

### Core Workflows

| Command | Description |
|---------|-------------|
| `/sdd start [name]` | Start new SDD flow |
| `/sdd resume [name]` | Resume existing SDD flow |
| `/sdd fork [old] [new]` | Fork flow for context recovery |
| `/sdd status` | Show all active SDD flows |
| `/ddd start [name]` | Start new DDD flow |
| `/ddd resume [name]` | Resume existing DDD flow |
| `/ddd fork [old] [new]` | Fork DDD flow |
| `/ddd status` | Show all active DDD flows |

### Speckit Tools

Advanced tools for specification management:

| Command | Description |
|---------|-------------|
| `/speckit.specify` | Create or update feature specification |
| `/speckit.plan` | Execute implementation planning workflow |
| `/speckit.tasks` | Generate actionable task list |
| `/speckit.implement` | Execute implementation plan |
| `/speckit.checklist` | Generate custom checklist |
| `/speckit.clarify` | Identify underspecified areas |
| `/speckit.analyze` | Cross-artifact consistency analysis |
| `/speckit.constitution` | Create/update project constitution |
| `/speckit.taskstoissues` | Convert tasks to GitHub issues |
| `/speckit.sdd` | SDD sync (Speckit ↔ SDD flows) |
| `/speckit.ddd` | DDD sync (Speckit ↔ DDD flows) |

### Architecture Decision Records

| Command | Description |
|---------|-------------|
| `/adr start [name]` | Start new ADR in DRAFT phase |
| `/adr resume [n]` | Resume existing ADR by number or name |
| `/adr quick [name]` | Create lightweight ADR |
| `/adr list` | Show all ADRs with status |
| `/adr status` | Show active ADRs (DRAFT/REVIEW) |

**ADR Flow Phases**: `DRAFT → REVIEW → APPROVED | REJECTED`

Each phase requires explicit approval:
- "ready for review" (DRAFT → REVIEW)
- "ADR approved" or "ADR rejected" (REVIEW → decision)

## Workflow Phases Explained

### Phase 1: Requirements
**Goal**: Capture WHAT and WHY

- Problem statement
- User stories (As a... I want... So that...)
- Acceptance criteria (Given/When/Then)
- Constraints and non-goals
- Open questions

**Output**: `01-requirements.md`

### Phase 2: Specifications
**Goal**: Design HOW at architectural level

- Affected systems/components
- Data models and interfaces
- Behavior descriptions
- Edge cases and error handling
- Dependencies and integration points

**Output**: `02-specifications.md`

### Phase 3: Plan
**Goal**: Break down into actionable steps

- Task breakdown with dependencies
- File changes (create/modify/delete)
- Testing strategy
- Rollback considerations
- Complexity estimates

**Output**: `03-plan.md`

### Phase 4: Implementation
**Goal**: Execute the plan

- Track progress against plan
- Document deviations and rationale
- Capture learnings for spec refinement

**Output**: Working code + `04-implementation-log.md`

### Phase 5: Documentation (DDD only)
**Goal**: Create client-facing documentation

- Feature functionality in plain language
- Simple, non-technical explanation
- Usage examples
- Benefits for end users
- Avoid technical jargon

**Output**: `README.md`

## Phase Transitions

Each phase requires explicit user approval before advancing:

- **Requirements → Specifications**: "requirements approved"
- **Specifications → Plan**: "specs approved"
- **Plan → Implementation**: "plan approved"
- **Implementation → Documentation**: "ready for docs" (DDD only)
- **Documentation → Complete**: "docs approved" (DDD only)

## Status Tracking

Each flow maintains a `_status.md` file tracking:

- Current phase
- Phase status (drafting, review, approved, blocked)
- Last updated timestamp
- Blockers
- Progress checklist
- Context notes for resuming
- Fork history (if applicable)
- Next actions

## Best Practices

### Do
- Always update `_status.md` after significant changes
- Wait for explicit approval before phase transitions
- Ask questions when uncertain
- Document deviations from the plan
- Keep client documentation simple and jargon-free (DDD)

### Don't
- Skip phases (each catches different error classes)
- Assume approval without explicit confirmation
- Make silent scope changes without updating artifacts
- Leave `_status.md` stale
- Use technical jargon in client-facing docs (DDD)

## Integration with Meta-Projects

The Speckit sync commands enable bidirectional synchronization between:

- **Meta-Project**: High-level architecture specs
- **Sub-Projects**: Detailed implementation flows

### Push (Meta → Sub)
Transfer architectural requirements to implementation context:
```
/speckit.sdd push [feature] [subproject-path]
/speckit.ddd push [feature] [subproject-path]
```

### Pull (Sub → Meta)
Update architecture with implementation realities:
```
/speckit.sdd pull [feature] [subproject-path]
/speckit.ddd pull [feature] [subproject-path]
```

### Status Check
Verify alignment between meta and sub-projects:
```
/speckit.sdd status [feature]
/speckit.ddd status [feature]
```

## SDD vs DDD: Which to Use?

### Use SDD when:
- Working on internal features
- Documentation is primarily for developers
- Client communication is handled separately
- You need a lightweight 4-phase process

### Use DDD when:
- Building client-facing features
- Stakeholder communication is important
- You need to deliver user-friendly documentation
- Documentation is part of deliverables
- You want a complete 5-phase process

## Example Flow

```bash
# Start a new DDD flow
/ddd start user-authentication

# Work through requirements phase
# ... iterate on 01-requirements.md ...
# User: "requirements approved"

# Move to specifications
# ... design architecture in 02-specifications.md ...
# User: "specs approved"

# Create implementation plan
# ... break down tasks in 03-plan.md ...
# User: "plan approved"

# Execute implementation
# ... implement features, log in 04-implementation-log.md ...
# User: "ready for docs"

# Create client documentation
# ... write README.md in simple terms ...
# User: "docs approved"

# Flow complete!
```

## Session Handoff

When ending a session mid-flow:

1. Update `_status.md` with current state
2. Document in-flight reasoning
3. List explicit next steps
4. Note any context that might be lost

New sessions read `_status.md` first to reconstruct context.

## Contributing

When adding new templates or commands:

1. Update all IDE-specific command directories
2. Keep templates in `flows/.templates/`
3. Document in relevant flow markdown files
4. Update this README

## License

[Add your license here]

## Support

For issues, questions, or contributions, please refer to the project repository.

---

**Note**: This template is designed to work with AI-powered development tools. The structured approach enables AI agents to maintain context across sessions and deliver high-quality, traceable implementations.
