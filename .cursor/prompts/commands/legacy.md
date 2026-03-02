# Legacy - Reverse Engineering Documentation

Analyzes existing code and generates documentation (ADR/SDD/DDD/TDD/VDD flows) automatically.

## Command: $ARGUMENTS

```
/legacy                              # BFS: analyze entire project from root
/legacy src/auth                     # BFS: analyze only src/auth
/legacy src/auth "JWT validation"    # DFS: focus on JWT validation in src/auth
```

### No comment - BFS Mode

Breadth-first recursive analysis:
```
Iteration 1: Root → top-level modules (depth 0)
Iteration 2: Each module → submodules (depth 1)
Iteration 3: Submodules → components (depth 2)
...continue until fully analyzed
```

### With comment - DFS Mode

Depth-first focused analysis:
```
1. Find code related to comment
2. Analyze deeply in that area
3. Create focused flows for that functionality
```

---

## Flow Type Detection - Module-First Approach

**Critical**: Flows are per-module, not per-file-type. Tests and UI are attributes of modules, not separate entities.

### Wrong Approach (file-type grouping)
```
tests/*.test.ts      → tdd-all-tests     ❌
components/*.tsx     → vdd-all-ui        ❌
```

### Correct Approach (module-centric)
```
src/auth/
├── auth.service.ts  ─┐
├── auth.test.ts     ─┼→ ONE flow for auth module
├── LoginForm.tsx    ─┤   Type determined by PRIMARY PURPOSE
└── README.md        ─┘
```

### Module Detection Algorithm

```
1. IDENTIFY MODULE BOUNDARIES
   - Directory with cohesive functionality
   - Has entry point (index.ts, mod.rs, __init__.py)
   - Clear single responsibility

2. ANALYZE MODULE PURPOSE (primary driver)
   - What problem does this module solve?
   - Who is the primary consumer?
   - What is the core deliverable?

3. DETERMINE FLOW TYPE BY PURPOSE
   Purpose: Internal service logic      → SDD
   Purpose: Client-facing feature       → DDD
   Purpose: Critical correctness        → TDD (test-first is essential)
   Purpose: User interface/experience   → VDD (visual is primary)

4. RECORD ATTRIBUTES (secondary)
   - has_tests: true/false
   - has_ui: true/false
   - has_public_api: true/false
   - has_docs: true/false
```

### Flow Type Decision Matrix

| Primary Purpose | Has Tests | Has UI | Stakeholder-facing | → Flow Type |
|-----------------|-----------|--------|-------------------|-------------|
| Internal logic | any | no | no | SDD |
| Internal logic | any | yes | no | SDD + ui_components attribute |
| Needs explanation to clients/execs | any | any | yes | DDD |
| Correctness-critical | essential | any | any | TDD |
| User experience | any | primary | any | VDD |

### DDD Selection Criteria - Stakeholder Communication

**DDD is NOT about public API**. DDD flow is for features that:

```
1. REQUIRE STAKEHOLDER BUY-IN
   - Client needs to understand value
   - Executive needs to approve
   - Sales needs to pitch it
   - Users need onboarding

2. FEATURE IS "SELLABLE"
   - Has clear value proposition
   - Solves visible problem
   - Worth explaining to non-technical people

3. DOCUMENTATION IS DELIVERABLE
   - README.md goes to client
   - Feature announcement needed
   - Marketing materials required
```

**Indicators in existing code**:
```
- Has README.md with user-facing explanation
- Has changelog entries describing value
- Has user documentation
- Referenced in marketing/sales materials
- Has onboarding/tutorial content
```

**Counter-indicators (use SDD instead)**:
```
- Pure internal refactoring
- Infrastructure changes
- Developer-only tooling
- No user-visible impact
```

### TDD Selection Criteria - Cases-First Indicator

**TDD is NOT just "has tests"**. TDD flow is for modules where:

```
1. CORRECTNESS IS CRITICAL
   - Financial calculations
   - Security logic
   - Data integrity
   - State machines
   - Protocol implementations

2. BEHAVIOR MUST BE EXHAUSTIVELY DEFINED
   - All edge cases matter
   - Failures have serious consequences
   - Logic is complex with many branches

3. TESTS SHOULD DRIVE DESIGN
   - Interface not yet defined
   - Multiple valid implementations possible
   - Need to discover design through cases
```

**Indicators in existing code**:
```
- High test coverage (>80%)
- Tests check edge cases, not just happy paths
- Tests define behavior (not just verify implementation)
- Test names describe behaviors, not methods
- Property-based tests present
- Mutation testing used
```

**Counter-indicators (use SDD instead)**:
```
- Tests exist but only smoke tests
- Tests verify implementation details
- Low coverage, tests added later
- Tests named after methods (test_foo_method)
```

### Module Attributes (not separate flows)

```markdown
## Module: auth

**Flow**: sdd-auth
**Type**: SDD (internal service)

**Attributes**:
- has_tests: true (12 test files)
- has_ui: true (LoginForm, RegisterForm)
- has_public_api: false
- test_coverage: 85%

**Note**: UI components are part of auth module,
not a separate VDD flow. They implement auth UX.
```

### When to Create Separate Flows

Create SEPARATE flow only when:
```
1. Different ownership/team
2. Independent release cycle
3. Separate requirements lifecycle
4. Can be developed in isolation
```

Do NOT create separate flow for:
```
1. Tests of a module (attribute of module)
2. UI of a module (attribute of module)
3. Docs of a module (attribute of module)
```

### Colocation Principle

Related artifacts belong to same flow:
```
sdd-auth/
├── 01-requirements.md     ← includes UI requirements
├── 02-specifications.md   ← includes component specs
├── 03-plan.md            ← includes test plan
└── _status.md

Code it describes:
src/auth/
├── auth.service.ts
├── auth.test.ts          ← tests are part of auth
├── LoginForm.tsx         ← UI is part of auth
└── README.md             ← docs are part of auth
```

---

## ADR Auto-Detection

### Constraining (choices already made)
- Framework/library selections
- Architectural patterns (MVC, hexagonal, etc.)
- Database choices
- API styles (REST, GraphQL, gRPC)
- Authentication methods

### Enabling (capabilities to support)
- Extension points
- Plugin systems
- Configuration options
- Feature flags
- i18n/l10n support

---

## Execution Steps

### Step 1: Initialize

```
1. Read flows/legacy/_status.md
2. Determine mode (BFS/DFS) from arguments
3. Set target path (root or specified)
4. Set focus (comment if DFS)
```

### Step 2: Analyze (Iterative)

**Per depth level:**
```
1. List contents at current depth
2. Identify module boundaries
3. Detect flow types for each module
4. Identify ADR candidates
5. Update flows/legacy/analysis/depth-N.md
6. Update flows/legacy/mapping.md
7. Log to flows/legacy/log.md
8. Update flows/legacy/_status.md
9. Proceed to next depth
```

### Step 3: Generate Flows

For each identified module:
```
1. Create flows/[type]-[name]/ directory
2. Generate 01-requirements.md from code behavior
3. Generate 02-specifications.md from implementation
4. Generate 03-plan.md (empty, for future changes)
5. Create _status.md with phase = REQUIREMENTS, status = DRAFT
```

### Step 4: Generate ADRs

For each identified decision:
```
1. Create flows/adr-[NNN]-[name]/ directory
2. Generate adr.md with:
   - Context from code analysis
   - Decision identified
   - Consequences observed
   - Type: constraining | enabling
3. Create _status.md with phase = DRAFT
```

### Step 5: Generate Review List

```
1. Update flows/legacy/review.md
2. List all created flows with confidence level
3. List all created ADRs with confidence level
4. List questions requiring clarification
```

---

## Files - Dual Context

Legacy works with its own files AND creates new flows:

```
flows/legacy/                      flows/[type]-[name]/  (created)
├── _status.md                     ├── _status.md
├── log.md                         ├── 01-requirements.md
├── analysis/                      ├── 02-specifications.md
│   ├── depth-0.md                 └── 03-plan.md
│   ├── depth-1.md
│   └── ...                        flows/adr-[NNN]-[name]/  (created)
├── mapping.md                     ├── adr.md
└── review.md                      └── _status.md
```

---

## Analysis Templates

### Module Analysis (per depth)
```markdown
## [Module Name]

**Path**: [path]
**Type detected**: SDD | DDD | TDD | VDD
**Confidence**: high | medium | low

### Purpose
[What this module does - derived from code]

### Dependencies
- Internal: [other modules]
- External: [libraries]

### Public Interface
- [exports, APIs]

### Key Behaviors
1. [behavior from code]
2. [behavior from code]

### ADR Candidates
- [pattern/choice identified]
```

---

## Confidence Levels

**High confidence:**
- Clear module boundaries
- Explicit tests
- Documentation present
- Standard patterns

**Medium confidence:**
- Implicit boundaries
- Partial tests
- Some documentation
- Non-standard patterns

**Low confidence:**
- Unclear purpose
- No tests
- No documentation
- Complex/tangled code

---

## Idempotency & Upsert Semantics

**Critical**: `/legacy` must be idempotent - running multiple times produces consistent results without duplicates.

### Check-Before-Write Pattern

For each identified module/decision:
```
1. Check if flows/[type]-[name]/ already exists
2. If EXISTS:
   - Read existing documents
   - Detect delta (new info not in existing)
   - Append delta only (additive changes)
   - Mark as UPDATED in mapping.md
3. If NOT EXISTS:
   - Create new flow
   - Mark as CREATED in mapping.md
```

### Additive-Only Changes

When updating existing flows:
```markdown
## [Existing Section]
[existing content unchanged]

## [Existing Section] - Legacy Additions
> Added by /legacy on [DATE]

- [new detail discovered]
- [nuance not previously documented]
```

### Delta Detection

Compare analysis results with existing docs:
- **New behaviors** → append to requirements
- **New interfaces** → append to specifications
- **New dependencies** → append to relevant section
- **Conflicts** → add to review.md with CONFLICT flag

### Conflict Handling

If analysis contradicts existing documentation:
```
1. Do NOT overwrite existing
2. Add conflict note to review.md
3. Mark flow as NEEDS_RECONCILIATION
4. Human must resolve
```

### Mapping Status Values

| Status | Meaning |
|--------|---------|
| CREATED | New flow created |
| UPDATED | Existing flow appended to |
| UNCHANGED | Exists, no new info found |
| CONFLICT | Analysis contradicts existing |

---

## Output

All created/updated flows and ADRs are in **DRAFT** status:
- Require human review
- Listed in `flows/legacy/review.md`
- After review → approve or revise
- Then available for `/roadmap`

---

## Resume

If interrupted, `/legacy` reads `_status.md` and continues from last depth.

---

## Always

- Create flows in DRAFT status only
- Log every iteration
- Update mapping as analysis progresses
- Generate review list with confidence levels
- Never auto-approve - all items need human review
