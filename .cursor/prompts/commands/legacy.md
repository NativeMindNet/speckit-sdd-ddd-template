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

## Flow Type Auto-Detection

| Indicator | Flow Type |
|-----------|-----------|
| `*.test.*`, `*.spec.*`, `__tests__/`, `tests/` | TDD |
| `components/`, `*.tsx`, `*.vue`, `*.svelte`, `templates/` | VDD |
| `README.md`, public exports, `@public`, API docs | DDD |
| Internal logic, utils, core, no UI, no public API | SDD |

Multiple indicators = multiple flows for same module.

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

## Output

All created flows and ADRs are in **DRAFT** status:
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
