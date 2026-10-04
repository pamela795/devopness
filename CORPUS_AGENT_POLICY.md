# Corpus Assistant Agent: Operating Policy

Strict rules for autonomous and semi-autonomous agent execution in the Devopness monorepo.

## Policy Authority

This policy is derived from and supplements:
- `AGENTS.md` — agent execution rules
- `CONTRIBUTING.md` — contribution workflow
- `.github/workflows/pr-lint.yml` — CI validation
- `.github/scripts/pr-validate-description.js` — PR description validation

**Source of Truth:** If this document conflicts with those files, the source files win. Agents must re-check source files before each execution.

---

## Policy 1: Scope & Minimalism

### Requirement
All changes must be minimal, scoped, and targeted.

### Rules
1. Identify affected packages before editing
2. Edit only those packages and nothing else
3. Avoid cross-package changes unless explicitly required
4. No broad refactors, cleanups, or "while-I'm-here" edits
5. Each commit should address one intent

### Enforcement
- Agent must state affected packages in output
- Agent must justify cross-package changes if they occur
- Agent must refuse requests for broad refactors

---

## Policy 2: Generated Files

### Requirement
Never hand-edit generated or derived files. Always prefer source inputs.

### Prohibited Files
- `packages/sdks/common/spec.json` — generated from OpenAPI specs
- Any `*.pb.go` files
- `package-lock.json` — regenerate with `npm install`
- Auto-generated documentation

### Enforcement
- Agent must check file type before editing
- Agent must refuse edits to known generated files
- Agent must suggest source file alternatives

---

## Policy 3: Git & Version Control

### Conventional Commits
- All commits must use format: `<type>(<scope>): <description>`
- Types: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`
- Scope: package name or feature area (optional)
- Description: imperative, lowercase, no period

**Examples:**
- `feat(sdk-python): add timeout error handling`
- `fix: broken links in README`
- `refactor(ui-react): extract component logic`

### Branch Names
- Format: `<type>/<descriptive-name>`
- Short and readable
- Examples: `feat/timeout-handling`, `fix/auth-edge-case`

### Amend Policy
- Never use `git --amend` unless user explicitly requests
- Never force-push unless user explicitly requests

---

## Policy 4: Package Management & Installation

### Workaround Flags
Never use:
- `--legacy-peer-deps`
- `--force`
- `--no-save`
- Other bypass or workaround flags

### Exceptions
- User must explicitly request the bypass
- Agent must explain the tradeoff and risk
- Agent must document the justification in PR or commit

### Lock Files
- `package-lock.json` is source of truth
- After install commands, commit lock file if changed
- Regenerate with `npm install` if corrupted

---

## Policy 5: Code Quality Standards

### EditorConfig (.editorconfig)
All files must comply:
- 2-space indentation (default)
- 4-space indentation for Python files
- Tab indentation for Makefiles
- UTF-8 charset
- Trim trailing whitespace
- Insert final newline
- Single quotes for YAML

### Enforcement
- Agent must check `.editorconfig` before editing
- Agent must apply formatting rules to all new/modified files
- Agent must flag formatting violations

---

## Policy 6: Changesets & Releases

### When Required
Add a changeset for any change that:
- Modifies a public API
- Adds user-visible features
- Fixes reported bugs
- Changes behavior
- Deprecates functionality

### When Not Required
- Documentation-only changes
- Internal refactors with no API change
- CI/CD config changes
- Build tooling changes

### Changeset Process
1. Use `npx @changesets/cli add` or create `.changeset/*.md` manually
2. Select affected package(s)
3. Choose bump type:
   - **patch**: bug fixes, internal changes
   - **minor**: new features, additions
   - **major**: breaking changes, removals
4. Write clear, user-facing description

### Enforcement
- Agent must identify which packages are affected
- Agent must determine correct bump type
- Agent must create changeset before PR
- Agent must refuse improperly bumped changesets

---

## Policy 7: Pull Request Format

### Title Requirements
- Active imperative voice: "Add", "Fix", "Update", not "Adds", "Fixed", "Updates"
- No trailing period
- Natural sounding: "This PR will [title]"
- Conventional Commits format preferred

**Bad Examples:**
- "Fixes a bug"
- "Adds a feature."
- "Updates the code"

**Good Examples:**
- `feat: add timeout error handling`
- `fix: broken links in user profile page`
- `refactor: simplify auth middleware`

### Description Required Sections

#### 1. Description of changes
- Format: checklist with `- [x]` items
- Must contain actual checklist items, not placeholder text
- Examples:
  ```
  - [x] Added TimeoutError exception class
  - [x] Updated client to catch timeouts
  - [x] Added tests for timeout scenarios
  ```

#### 2. GitHub issues resolved by this PR
- Format: Issue numbers or "N/A"
- Examples:
  ```
  Fixes #123
  Related to #456, #789
  N/A
  ```

#### 3. Quality Assurance
- Format: Actual success criteria, not template text
- Examples:
  ```
  - [x] All existing tests pass
  - [x] New tests cover timeout scenarios
  - [x] Manual testing on Python 3.8+
  ```

#### 4. More info (Optional)
- Additional context, screenshots, design docs

### Enforcement
- Agent must generate PR description matching template
- Agent must include all required sections
- Agent must refuse PRs with placeholder or missing sections
- Agent must validate against `.github/scripts/pr-validate-description.js`

---

## Policy 8: CI/CD Validation

### Pre-Submission Validation
Agent must confirm before marking PR ready:

1. **PR Lint Check** (`.github/workflows/pr-lint.yml`)
   - Title format is Conventional Commits
   - Description has required sections
   - No merge conflicts with base branch

2. **Description Validator** (`.github/scripts/pr-validate-description.js`)
   - All required sections present
   - Checklist items are not placeholder text
   - QA criteria are actual, not template

3. **Changeset Check** (if applicable)
   - Changeset file present and valid
   - Bump type is correct
   - Description is clear

### Enforcement
- Agent must state "CI will pass" or "CI will fail"
- Agent must not submit PR that will fail CI
- Agent must re-validate if code changes after initial check

---

## Policy 9: Package-Local Configuration

### Package Directory Structure
When working inside a package:
```
packages/sdks/python/
├── package.json
├── pyproject.toml
├── pytest.ini
├── .eslintrc.js
├── src/
├── tests/
└── README.md
```

### Package-Local Commands
- Always run commands from package directory
- Example: `cd packages/sdks/python && npm run test`
- Never run from repo root when package has local config
- Use package-specific linters, formatters, test frameworks

### Enforcement
- Agent must identify affected package
- Agent must run package-local validation
- Agent must report validation results
- Agent must fail if validation fails

---

## Policy 10: Search & Navigation

### Semantic Search Use Cases
- Architecture understanding
- Intent-based queries
- Cross-package relationships
- High-level feature tracing

### Lexical Search Use Cases
- Exact function/class names
- File paths
- Error messages
- API endpoints
- Specific strings or patterns

### Search Discipline
- Start with semantic search for broad understanding
- Move to lexical search for precision
- Follow import chains to understand dependencies
- Map callers and usages before editing

---

## Policy 11: Agent Refusal

Agents must refuse:
1. Requests to edit generated files without justification
2. Requests for broad refactors without scoping
3. Requests to use workaround flags without explanation
4. Requests to amend commits without user consent
5. Requests that violate contributor guidelines
6. Requests to break established patterns or conventions
7. Requests that would fail CI validation

**Refusal Response:**
```
I cannot [action] because [policy]. This violates [policy reference].

Alternative: [suggestion]

If you want to proceed anyway, please explicitly request and justify.
```

---

## Policy 12: Transparency & Logging

### Agent Output Requirements
Agent must clearly state:
1. Task intent classification
2. Affected packages
3. Search strategy and results
4. Files to be edited and why
5. Validation results
6. PR readiness assessment
7. Any policy exceptions requested

### Example Output
```
**Intent:** Implementation (add feature)
**Scope:** packages/sdks/python only
**Search:** Found timeout patterns in client.py and exceptions.py
**Changes:** 3 files modified (see below)
**Validation:** Passed (pytest, ESLint)
**Changeset:** Added (minor bump)
**PR Ready:** Yes, meets all requirements
```

---

## Policy 13: Escalation & Ambiguity

Agent must escalate (ask user) when:
1. Scope is ambiguous (single vs. cross-package)
2. Bump type is uncertain (patch vs. minor)
3. Breaking change status unclear
4. Policy conflict or exception needed
5. Multiple valid approaches exist
6. Risk assessment requires human judgment

**Escalation Format:**
```
❓ **Clarification needed:** [question]

Option A: [description]
Option B: [description]

Which would you prefer, or would you like to proceed differently?
```

---

## Audit & Compliance

### Agent Self-Check Before Completion
- [ ] Policy 1 (Scope): Changes are minimal and scoped?
- [ ] Policy 2 (Generated Files): No hand-edits to prohibited files?
- [ ] Policy 3 (Git): Commits follow Conventional format?
- [ ] Policy 4 (Package Mgmt): No workaround flags used?
- [ ] Policy 5 (Code Quality): EditorConfig rules applied?
- [ ] Policy 6 (Changesets): Added when required?
- [ ] Policy 7 (PR Format): All required sections present?
- [ ] Policy 8 (CI): Validation passes?
- [ ] Policy 9 (Package Config): Used package-local tools?
- [ ] Policy 10 (Search): Right search strategy used?
- [ ] Policy 11 (Refusal): Nothing refused unnecessarily?
- [ ] Policy 12 (Transparency): Output is clear and complete?
- [ ] Policy 13 (Escalation): Ambiguity handled correctly?

---

**Version:** 1.0  
**Authority:** Derived from AGENTS.md, CONTRIBUTING.md, CI/CD config  
**Effective:** 2026-10-04  
**Scope:** All agent execution in pamela795/devopness repository
