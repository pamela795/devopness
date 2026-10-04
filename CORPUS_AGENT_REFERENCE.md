# Corpus Assistant Agent: Reference Card

A cheat sheet for all agent modes, rules, and commands.

---

## At a Glance

| Aspect | Answer |
|--------|--------|
| **Mission** | Navigate → Search → Edit → Validate → PR |
| **When to use** | Finding code, understanding design, making safe changes |
| **Primary tools** | semantic-code-search, lexical-code-search, getfile |
| **Source of truth** | AGENTS.md, CONTRIBUTING.md, .github/ config |
| **Golden rule** | Keep changes minimal, scoped, and validated |

---

## Intent Classification

| Intent | Trigger | Primary Action | Changeset? |
|--------|---------|-----------------|------------|
| **Exploration** | "Where...", "How...", "What's..." | Semantic search + traverse | No |
| **Implementation** | "Add...", "Fix...", "Refactor..." | Navigate → Implement → Test | Yes (maybe) |
| **Documentation** | "Update docs...", "Add example..." | Read guidelines → Edit → Link | No |
| **Review** | "Review this...", "Is this correct?" | Analyze → Check patterns → Suggest | No |
| **Release** | "Publish...", "Prepare release..." | Validate changesets → Version → Publish | Yes (controlled) |

---

## Search Decision Tree

```
Do you know the exact symbol name?
  YES → Use lexical search: symbol:ClassName
  NO → Do you need broad understanding?
         YES → Use semantic search: "Intent query"
         NO → Use lexical search: content:"phrase"
```

---

## Search Syntax

### Semantic (Intent-Based)
```
query: "How does X work?"
query: "Show me X pattern"
query: "Explain X architecture"

Results: Ranked by semantic relevance
```

### Lexical (Exact Match)
```
symbol:ClientClass
content:"error_message"
path:/packages\/sdks\/python/
content:"TODO"

boolean: OR, NOT, AND
regex: /pattern/
```

---

## Package Map

```
@devopness/sdk-js
  Location: packages/sdks/javascript/
  Config: package.json, tsconfig.json
  Language: TypeScript → ESM
  Test: Jest/Vitest
  Lint: ESLint + Prettier

devopness (PyPI)
  Location: packages/sdks/python/
  Config: pyproject.toml, setup.py
  Language: Python 3.8+
  Test: pytest
  Lint: Black, isort, mypy

@devopness/ui-react
  Location: packages/ui/react/
  Config: package.json, tsconfig.json
  Language: TypeScript → ESM/CJS React
  Test: Jest + React Testing Library
  Storybook: Yes
```

---

## Conventional Commits Quick Ref

```
feat(scope): description     # New feature
fix(scope): description      # Bug fix
refactor(scope): description # Code restructure
chore(scope): description    # Maintenance
docs(scope): description     # Documentation
test(scope): description     # Tests only

Scope options: sdk-python, sdk-js, ui-react, docs, etc.
No period at end. Imperative mood.
```

---

## Changeset Decision

```
Is this a user-visible change?
  YES → Add changeset
  NO → Skip changeset

If changeset needed:
  New feature? → minor bump
  Bug fix? → patch bump
  Breaking change? → major bump
```

---

## PR Description Template

```markdown
## Description of changes
- [x] Item 1 (checklist, not placeholder)
- [x] Item 2

## GitHub issues resolved by this PR
#123 or N/A

## Quality Assurance
Actual success criteria (not template text)

## More info
Optional additional context
```

**Validation:**
- ✅ All sections present
- ✅ Checklist has real items
- ✅ QA has actual criteria
- ✅ No placeholder text

---

## EditorConfig Rules

```
Default: 2-space indent, UTF-8, final newline, trim whitespace

Python files: 4-space indent
Makefiles: tab indent
YAML files: single quotes

All files: trim_trailing_whitespace = true
All files: insert_final_newline = true
```

---

## Pre-Submission Checklist

```
☐ Intent classified
☐ Scope identified (affected packages)
☐ Minimal changes only
☐ Package-local checks passed
☐ No generated files edited
☐ Conventional Commits used
☐ EditorConfig rules applied
☐ Changesets added (if needed)
☐ PR title follows format
☐ PR description complete
☐ All sections have real content
☐ CI validation will pass
```

---

## Policy Quick Ref

| Do ✅ | Don't ❌ |
|------|--------|
| Scope changes minimally | Broad refactors |
| Use package-local tools | Run from repo root unnecessarily |
| Follow Conventional Commits | Amend commits without asking |
| Add changesets when needed | Hand-edit spec.json |
| Check EditorConfig | Use --legacy-peer-deps without asking |
| Fill PR sections with real content | Leave placeholder text |
| Validate with CI before PR | Submit PRs that fail CI |
| Ask user for clarification | Guess on ambiguous decisions |

---

## Common Commands

```bash
# Search
semantic-code-search --query "authentication flow"
lexical-code-search --query "symbol:Client"

# File operations
getfile packages/sdks/python/src/devopness/client.py
create_or_update_file packages/sdks/python/src/devopness/exceptions.py

# Package validation (from package directory)
cd packages/sdks/python
npm run lint
npm run test

# Changesets
npx @changesets/cli add

# Git
git checkout -b feat/feature-name
git commit -m "feat(scope): description"
```

---

## When to Escalate (Ask User)

```
❓ Scope ambiguous (single vs. cross-package)
❓ Changeset bump type uncertain
❓ Breaking change status unclear
❓ Policy exception needed
❓ Multiple valid approaches exist
❓ Risk assessment requires human judgment
```

---

## Refusal Template

```
I cannot [action] because [policy].
This violates [reference].

Alternative: [suggestion]

If you want to proceed anyway, please explicitly request and justify.
```

---

## Validation Checklist (Agent)

```
Before completion, confirm:

☐ Scoped changes only (Policy 1)
☐ No generated files edited (Policy 2)
☐ Conventional Commits used (Policy 3)
☐ No workaround flags (Policy 4)
☐ EditorConfig rules applied (Policy 5)
☐ Changesets added if needed (Policy 6)
☐ PR format correct (Policy 7)
☐ CI will pass (Policy 8)
☐ Package-local tools used (Policy 9)
☐ Right search strategy used (Policy 10)
☐ Nothing refused unnecessarily (Policy 11)
☐ Output clear and complete (Policy 12)
☐ Ambiguity handled (Policy 13)
```

---

## Links to Source Files

- **Agent Rules:** `AGENTS.md`
- **Contributing:** `CONTRIBUTING.md`
- **PR Template:** `.github/PULL_REQUEST_TEMPLATE.md`
- **PR Linting:** `.github/workflows/pr-lint.yml`
- **PR Validation Script:** `.github/scripts/pr-validate-description.js`
- **Code Formatting:** `.editorconfig`
- **Doc Guidelines:** `docs/docs/authoring-guidelines.md`
- **Community Standards:** `CODE_OF_CONDUCT.md`

---

Version: 1.0 | Quick Reference | Updated: 2026-10-04
