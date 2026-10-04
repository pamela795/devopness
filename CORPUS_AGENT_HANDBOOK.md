# Corpus Assistant Agent: Engineering Handbook

Comprehensive guide for developers and agents working in the Devopness monorepo.

---

## Part 1: Foundations

### What Is This Repository?

Devopness is an open-source platform for provisioning cloud infrastructure and deploying applications across multiple cloud providers. The repository is organized as a monorepo containing:

- **SDKs**: Language-specific API clients (JavaScript, Python)
- **UI**: React component library and design system
- **Documentation**: Product guides and API references
- **Examples**: Sample integrations (Rails, Express, Laravel, etc.)

### Why This Handbook Exists

This handbook teaches you how to:
1. Navigate a complex monorepo efficiently
2. Find code by intent, not just by name
3. Make safe, scoped changes that respect package boundaries
4. Follow repo conventions and pass CI validation
5. Prepare work for review and release

---

## Part 2: Repository Structure Deep Dive

### Directory Map

```
devopness/
├── Root Configuration
│   ├── package.json             # Workspace root, Changesets config
│   ├── package-lock.json        # Locked dependencies
│   ├── .editorconfig            # Code formatting rules
│   ├── .gitignore               # Excluded paths
│   └── tsconfig.json            # TypeScript config (if present)
│
├── Guidelines & Policies
│   ├── README.md                # Project overview
│   ├── CONTRIBUTING.md          # How to contribute
│   ├── AGENTS.md                # AI agent rules
│   ├── CODE_OF_CONDUCT.md       # Community standards
│   ├── CORPUS_AGENT.md          # This corpus assistant (v1)
│   ├── CORPUS_AGENT_BRIEF.md    # Quick reference
│   ├── CORPUS_AGENT_POLICY.md   # Strict rules
│   └── CORPUS_AGENT_HANDBOOK.md # You are here
│
├── CI/CD & Automation
│   └── .github/
│       ├── workflows/
│       │   ├── pr-lint.yml          # PR title/description validation
│       │   ├── test.yml             # Run tests on PR
│       │   ├── publish.yml          # Release and publish
│       │   └── ...other workflows
│       ├── scripts/
│       │   ├── pr-validate-description.js  # PR section checker
│       │   └── ...other scripts
│       └── PULL_REQUEST_TEMPLATE.md # PR template
│
├── Packages (Workspaces)
│   └── packages/
│       ├── sdks/
│       │   ├── javascript/
│       │   │   ├── package.json
│       │   │   ├── src/
│       │   │   ├── tests/
│       │   │   └── README.md
│       │   │
│       │   └── python/
│       │       ├── pyproject.toml
│       │       ├── src/
│       │       ├── tests/
│       │       └── README.md
│       │
│       └── ui/
│           └── react/
│               ├── package.json
│               ├── src/components/
│               ├── stories/
│               └── README.md
│
├── Examples & Documentation
│   ├── examples/
│   │   └── applications/
│   │       ├── rails/
│   │       ├── express/
│   │       ├── laravel/
│   ��       └── ...others
│   │
│   └── docs/
│       ├── docs/
│       │   ├── authoring-guidelines.md
│       │   ├── README.md              # Docs structure
│       │   └── ...doc pages
│       └── README.md
│
└── Release Management
    └── .changeset/
        ├── config.json              # Changesets configuration
        └── [uuid].md                # Individual changeset files
```

### Package Workspace Details

#### JavaScript SDK (`packages/sdks/javascript`)
- **Published as:** `@devopness/sdk-js` on npm
- **Language:** TypeScript → JavaScript
- **Config:** `package.json`, `tsconfig.json`
- **Build:** ESM output
- **Tests:** Jest or Vitest
- **Linting:** ESLint + Prettier

#### Python SDK (`packages/sdks/python`)
- **Published as:** `devopness` on PyPI
- **Language:** Python 3.8+
- **Config:** `pyproject.toml`, `setup.py`
- **Build:** sdist + wheel
- **Tests:** pytest
- **Linting:** Black, isort, mypy

#### React UI (`packages/ui/react`)
- **Published as:** `@devopness/ui-react` on npm
- **Language:** TypeScript → React components
- **Config:** `package.json`, `tsconfig.json`
- **Build:** ESM + CJS
- **Tests:** Jest + React Testing Library
- **Storybook:** Component documentation

---

## Part 3: The Corpus Assistant Workflow

### Step 1: Request Intake

When you receive a task:
1. **Read the intent** — what does the user want to achieve?
2. **Classify the type:**
   - Exploration: "Show me how X works"
   - Implementation: "Add feature", "Fix bug"
   - Documentation: "Update guide", "Add example"
   - Review: "Check this for correctness"
   - Release: "Prepare packages for release"
3. **Identify scope:**
   - Single package or cross-package?
   - Public API impact?
   - Breaking changes?
4. **Determine effort** — quick fix or substantial work?

### Step 2: Search & Discovery

**For exploration tasks:**
```
Use semantic search → broad understanding
↓
Follow import chains → dependency graph
↓
Read tests & examples → expected behavior
↓
Summarize findings → answer
```

**For implementation tasks:**
```
Use semantic search → find related code
↓
Use lexical search → exact symbols
↓
Map affected files → changes required
↓
Locate tests → existing test patterns
↓
Identify changesets → version bump needed
```

**For documentation tasks:**
```
Read authoring-guidelines.md → style rules
↓
Find similar docs → follow patterns
↓
Check frontmatter → metadata rules
↓
Review examples → code snippet patterns
```

### Step 3: Plan the Work

**What to decide:**
1. Which files will change?
2. Which packages are affected?
3. What's the minimal scope?
4. Will tests need updating?
5. Do we need a changeset?
6. What's the commit message?
7. What's the PR title?

**Example plan:**
```
Task: Add timeout handling to Python SDK

Scope: packages/sdks/python only
Files:
  - src/devopness/exceptions.py (add TimeoutError)
  - src/devopness/client.py (catch and re-raise)
  - tests/test_client.py (add test cases)

Changeset: Yes (minor bump, new feature)
Commit: feat(sdk-python): add timeout error handling
PR Title: feat: add timeout error handling for API requests
```

### Step 4: Execute Changes

**For each modified file:**
1. Read current version to understand context
2. Plan the specific edits
3. Apply changes
4. Check formatting (EditorConfig)
5. Verify syntax

**Example:**
```python
# exceptions.py - ADD NEW CLASS
class TimeoutError(DevopnessError):
    """Raised when API request times out."""
    pass

# client.py - CATCH AND RE-RAISE
try:
    response = requests.get(..., timeout=self.timeout)
except requests.Timeout:
    raise TimeoutError("API request timed out")
```

### Step 5: Validate Locally

**For implementation tasks:**
```bash
# Move to package directory
cd packages/sdks/python

# Run package-local linter
npm run lint  # or: black src/ && mypy src/

# Run tests
npm run test  # or: pytest tests/
```

**For documentation tasks:**
```bash
# Check frontmatter
grep -A5 "^---" docs/docs/your-page.md

# Check links
# (manually or with link checker)
```

### Step 6: Prepare PR

**Create or update changeset:**
```bash
npx @changesets/cli add
# Follow prompts to select package, type, description
```

**Write PR description:**
```markdown
## Description of changes
- [x] Added TimeoutError exception class
- [x] Updated client to catch and re-raise timeouts
- [x] Added test cases for timeout scenarios

## GitHub issues resolved by this PR
Fixes #123

## Quality Assurance
- [x] All existing tests pass
- [x] New tests cover timeout scenarios
- [x] Manual testing on Python 3.8+
- [x] Linting and formatting pass

## More info
This adds a new exception type for timeouts, making error handling more explicit.
```

### Step 7: Validate PR Format

**Before submitting, confirm:**
- [ ] Title is Conventional Commits format
- [ ] All required description sections present
- [ ] No placeholder text in sections
- [ ] Changeset file is valid (if needed)
- [ ] No hand-edits to generated files
- [ ] EditorConfig rules applied
- [ ] Package-local checks pass

---

## Part 4: Search Strategies

### Semantic Search Examples

**Query:** "How does authentication work?"
```
Results might include:
- auth/handlers.go (middleware)
- auth/jwt.go (token validation)
- auth/types.go (structs)
- examples/auth-examples.md
- tests/auth_test.py
```

**Query:** "How do SDKs connect to the API?"
```
Results might include:
- packages/sdks/javascript/src/client.ts
- packages/sdks/python/src/devopness/client.py
- packages/sdks/common/spec.json (API spec)
- examples/sdk-usage.md
```

**Query:** "Error handling patterns"
```
Results might include:
- exceptions.py (base error class)
- error_handler.py (error utilities)
- tests with error scenarios
- docs with error examples
```

### Lexical Search Examples

**Query:** `symbol:Client`
```
Finds all definitions and references to 'Client'
```

**Query:** `content:"timeout"`
```
Finds all mentions of 'timeout'
```

**Query:** `path:/packages\/sdks\/python/`
```
Limits search to Python SDK directory
```

**Query:** `symbol:TimeoutError repo:pamela795/devopness`
```
Finds all uses of TimeoutError in the repo
```

---

## Part 5: Common Tasks

### Task: Add a Feature to an SDK

1. **Explore:** Semantic search "How does [feature] work in other parts?"
2. **Design:** Plan where the code lives (which file, which class)
3. **Implement:** Add code, following existing patterns
4. **Test:** Add test cases, run package tests
5. **Document:** Add docstrings, update examples
6. **Release:** Add changeset (minor bump)
7. **PR:** Follow template, fill all sections

### Task: Fix a Bug

1. **Understand:** Semantic search for existing bug reports or related code
2. **Locate:** Lexical search for the broken symbol
3. **Reproduce:** Look at tests to understand expected behavior
4. **Fix:** Minimal change to correct the issue
5. **Test:** Add test case that catches the bug
6. **Release:** Add changeset (patch bump)
7. **PR:** Reference the issue number

### Task: Update Documentation

1. **Read:** `docs/docs/authoring-guidelines.md`
2. **Find:** Similar docs to follow the pattern
3. **Edit:** Update frontmatter, content, examples
4. **Check:** Verify links and formatting
5. **PR:** No changeset needed (docs-only change)

### Task: Refactor Code

1. **Map:** Semantic search to find all usages
2. **Plan:** Ensure changes are minimal and scoped
3. **Implement:** Refactor, preserving public API
4. **Test:** Run all tests in affected package
5. **Release:** Add changeset (patch or minor, depending on API change)
6. **PR:** Explain rationale and benefits

---

## Part 6: Conventions & Patterns

### Naming

**Branches:**
- `feat/timeout-handling` (feature)
- `fix/auth-edge-case` (bug fix)
- `refactor/simplify-client` (refactor)
- `docs/add-auth-guide` (documentation)

**Commits:**
- `feat(sdk-python): add timeout error handling`
- `fix: broken links in README`
- `refactor(ui-react): extract component logic`

**PRs:**
- `feat: add timeout error handling for API requests`
- `fix: broken links on user profile page`
- `docs: add authentication guide`

### Code Formatting

**From .editorconfig:**
- 2 spaces for JS/YAML/etc.
- 4 spaces for Python
- Tabs for Makefiles
- Final newline on all files
- Trim trailing whitespace
- UTF-8 charset

**Example:**
```python
# Python file (4 spaces)
def timeout_handler(timeout_seconds):
    """Handle request timeout."""
    return timeout_seconds
```

```javascript
// JavaScript file (2 spaces)
function timeoutHandler(timeoutSeconds) {
  return timeoutSeconds;
}
```

### Testing Patterns

**Python (pytest):**
```python
def test_timeout_error():
    client = DevopnessClient(timeout=0.001)
    with pytest.raises(TimeoutError):
        client.get("/api/endpoint")
```

**JavaScript (Jest):**
```javascript
test('raises TimeoutError on timeout', async () => {
  const client = new DevopnessClient({ timeout: 0.001 });
  await expect(client.get('/api/endpoint')).rejects.toThrow(TimeoutError);
});
```

### Changeset Examples

**File:** `.changeset/abc123.md`
```markdown
---
'@devopness/sdk-js': minor
'@devopness/sdk-python': minor
---

Add timeout error handling for API requests, making error handling more explicit.
```

---

## Part 7: Troubleshooting

### Problem: CI validation failed

**Check:**
1. PR title follows Conventional Commits
2. All required description sections present
3. No placeholder text in sections
4. Package-local linting passes

**Fix:**
- See `.github/scripts/pr-validate-description.js`
- Update PR description with actual content
- Ensure checklist items are not templates

### Problem: Tests fail locally

**Check:**
1. Are you in the package directory?
2. Did you install dependencies? (`npm install`)
3. Do all files follow EditorConfig rules?
4. Are imports correct?

**Fix:**
```bash
cd packages/sdks/python
npm install
npm run lint
npm run test
```

### Problem: Can't find the right file

**Strategy:**
1. Use semantic search: "Where is X functionality?"
2. Then use lexical search on results: `symbol:ClassName`
3. Follow import chains from main files
4. Check examples for usage patterns

### Problem: Don't know if change is breaking

**Questions to ask:**
1. Does the public API change?
2. Will existing code stop working?
3. Do users need to migrate code?

If yes to any: **major** bump. Otherwise: **minor** (feature) or **patch** (fix).

---

## Part 8: Checklist Template

**Before starting work:**
- [ ] Task intent is clear
- [ ] Scope is defined
- [ ] Affected packages identified

**Before implementation:**
- [ ] Similar code patterns located
- [ ] Test patterns understood
- [ ] Changeset need determined

**During implementation:**
- [ ] Code follows repo conventions
- [ ] EditorConfig rules applied
- [ ] Tests updated or added
- [ ] Docstrings added

**Before PR submission:**
- [ ] Package-local checks pass
- [ ] All tests pass
- [ ] No generated files edited
- [ ] Changeset file created (if needed)
- [ ] PR description complete
- [ ] All sections filled with real content
- [ ] PR title is Conventional Commits
- [ ] Branch merged into latest main

---

## Part 9: Reference & Links

### Repository Files
- `CONTRIBUTING.md` — contribution process
- `AGENTS.md` — AI agent rules
- `CODE_OF_CONDUCT.md` — community standards
- `docs/docs/authoring-guidelines.md` — documentation style

### CI/CD Files
- `.github/PULL_REQUEST_TEMPLATE.md` — PR template
- `.github/workflows/pr-lint.yml` — PR validation
- `.github/scripts/pr-validate-description.js` — PR section checker

### Configuration
- `.editorconfig` — code formatting
- `.gitignore` — excluded paths
- `package.json` — workspace root
- `.changeset/config.json` — release config

### External Tools
- [Changesets](https://github.com/changesets/changesets) — release automation
- [Conventional Commits](https://www.conventionalcommits.org/) — commit standard

---

**Version:** 1.0  
**For:** Developers and agents  
**Updated:** 2026-10-04  
**Scope:** Devopness monorepo workflow and best practices
