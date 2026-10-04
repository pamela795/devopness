# Corpus Assistant Agent: Quick Brief

For fast reference during execution.

## Mission
Navigate. Search. Edit. Validate. PR.

## In 30 Seconds

1. **Understand** the intent (explore, implement, document, review, release)
2. **Search** semantically for intent, lexically for symbols
3. **Edit** minimal scope, use package-local config
4. **Validate** with smallest relevant checks
5. **PR** with Conventional Commits + Changesets + Template sections

## Golden Rules

✅ Do:
- Keep changes scoped and minimal
- Use Conventional Commits (`feat:`, `fix:`, `refactor:`, `chore:`)
- Run package-local linters/tests
- Add Changesets for user-visible changes
- Follow PR template and CI rules

❌ Don't:
- Hand-edit `packages/sdks/common/spec.json`
- Use `--legacy-peer-deps`, `--force`, `--amend` without asking
- Broad refactors unless requested
- Miss PR sections or CI validation

## Search Patterns

**Semantic** (intent-based):
- "How does authentication work?"
- "What's the deployment flow?"

**Lexical** (exact symbols):
- `symbol:Client`
- `content:"timeout"`
- `path:/packages\/sdks\/python/`

## PR Template (Required Sections)

```
## Description of changes
- [x] Item 1 (checklist, not placeholder)
- [x] Item 2

## GitHub issues resolved by this PR
#123 or N/A

## Quality Assurance
Success criteria here (not template text)

## More info
Optional context
```

## Changeset (When Needed)

For package-affecting changes:
```bash
npx @changesets/cli add
# Select package, bump type (patch/minor/major), describe
```

## Pre-Submission Checklist

- [ ] Intent classified
- [ ] Minimal scope
- [ ] Package-local checks passed
- [ ] No generated files edited
- [ ] Conventional Commits used
- [ ] Changesets added (if needed)
- [ ] PR template complete
- [ ] CI will pass

## Tools

- `semantic-code-search` — intent queries
- `lexical-code-search` — exact symbols
- `getfile` — read files
- `create_or_update_file` — edit single file
- `push_files` — edit multiple files

## When Stuck

- Architecture: read `docs/docs/authoring-guidelines.md`
- PR rules: check `.github/PULL_REQUEST_TEMPLATE.md`
- CI validation: see `.github/scripts/pr-validate-description.js`
- Changesets: read `CONTRIBUTING.md` "Releases" section
- Agent rules: `AGENTS.md`

---

Version: 1.0 | For everyday use | Updated: 2026-10-04
