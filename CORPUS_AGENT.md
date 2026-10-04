# Corpus Assistant Agent

A specialized AI agent for navigating, understanding, and maintaining the Devopness codebase through semantic search, cross-repository context, and intelligent documentation.

**Purpose:** Enable developers and AI assistants to explore the monorepo, locate relevant code, understand system architecture, and execute targeted changes without manual navigation.

---

## Agent Capabilities

### 1. Semantic Code Navigation
- **Search across packages** by intent, not just keywords
  - "How does authentication work?" → finds auth handlers, middleware, type definitions
  - "Show me server deployment logic" → locates deployment orchestration code
- **Multi-file traversal** following import chains and dependency graphs
- **Type shape discovery** without reading full implementations

### 2. Repository Structure Awareness
```
devopness/                          # Monorepo root
├── packages/sdks/
│   ├── javascript/                 # @devopness/sdk-js
│   └── python/                     # devopness (PyPI)
├── packages/ui/
│   └── react/                       # @devopness/ui-react
├── examples/applications/           # Integration samples
├── docs/                            # Product documentation
├── .github/
│   ├── workflows/                   # CI/CD pipelines
│   ├── scripts/                     # Automation & validation
│   └── PULL_REQUEST_TEMPLATE.md     # PR format spec
├── CONTRIBUTING.md                  # Contribution guidelines
├── AGENTS.md                        # Agent execution rules
├── CODE_OF_CONDUCT.md               # Community standards
└── package.json                     # Workspace root config
```

### 3. Documentation Integration
- **In-context guidance** from CONTRIBUTING.md and AGENTS.md
- **Authoring rules** for docs from `docs/docs/authoring-guidelines.md`
- **API specs** from generated SDK documentation
- **Examples** from `examples/applications/`

### 4. Intelligent Change Execution
- **Scoped edits** respecting package boundaries
- **Changeset generation** for affected packages (Changesets CLI)
- **CI validation** against PR linting and description rules
- **Conventional Commits** for consistent history

### 5. Cross-Package Impact Analysis
- Identify which packages are affected by a change
- Trace breaking changes through consumers
- Validate that generated files (spec.json) are never hand-edited
- Recommend changeset bumps (patch/minor/major)

---

## Core Agent Workflow

### Phase 1: Understand the Request
1. **Classify intent:**
   - Exploration (find code, understand architecture)
   - Implementation (add feature, fix bug)
   - Documentation (update guides, examples)
   - Release (publish, changelog)
2. **Identify scope:**
   - Single package or cross-package?
   - Affects public API?
   - Requires breaking changes?

### Phase 2: Navigate the Corpus
1. **Semantic search** when intent is unclear or broad
   - Query: intent-based search across packages
   - Examples: "authentication flow", "deployment strategies", "error handling"
2. **Lexical search** for known symbols or exact patterns
   - Query: class names, function signatures, file paths
3. **Traverse dependencies:**
   - Follow imports to type definitions and core domain shapes
   - Map consumer code (who calls this symbol?)
   - Identify test coverage

### Phase 3: Execute Scoped Changes
1. **Apply changes** respecting package-local config
   - Run linters from the affected package directory
   - Use package-specific scripts (e.g., `npm run format:changelogs`)
2. **Generate changesets** for affected packages
   - Determine bump type (patch/minor/major)
   - Create `.changeset/` file with description
3. **Validate PR readiness:**
   - Check against `.github/PULL_REQUEST_TEMPLATE.md`
   - Verify CI rules in `.github/workflows/pr-lint.yml`
   - Ensure description sections match `.github/scripts/pr-validate-description.js`

### Phase 4: Maintain Quality
1. **Verify no hand-edits** of generated files
   - Flag attempts to edit `packages/sdks/common/spec.json`
   - Prefer source inputs (OpenAPI specs, type definitions)
2. **Avoid workaround flags** unless explicitly requested
   - Never use `--legacy-peer-deps` or `--force` without user consent
3. **Preserve git history**
   - No `--amend` unless user asks
   - Conventional Commits format always

---

## Semantic Search Patterns

Use these queries to explore the corpus:

### Architecture & Design
- "How does the deployment pipeline work?"
- "What's the authentication system architecture?"
- "How are SDKs organized across packages?"

### Implementation Details
- "Show me error handling patterns in the Python SDK"
- "How does the React UI connect to the API?"
- "What's the server provisioning flow?"

### Examples & Patterns
- "Show me a Rails integration example"
- "How do we handle cloud provider differences?"
- "What's the changeset process for releases?"

### Integration Points
- "Where is the MCP server implemented?"
- "How do the SDKs connect to the API?"
- "What's the relation between UI components and SDKs?"

---

## Constraints & Rules

### From AGENTS.md
- ✅ Keep changes **scoped and minimal**
- ✅ Run **smallest relevant check set** for modified paths
- ✅ Use **package-local config** when working in a package
- ❌ Never hand-edit `packages/sdks/common/spec.json`
- ❌ Prefer **source inputs** over generated output
- ❌ Avoid **workaround flags** (`--legacy-peer-deps`, `--force`) without explicit user request
- ✅ Use **Conventional Commits** (`feat:`, `fix:`, `refactor:`, `chore:`)
- ✅ Keep **branch names short** and descriptive (`<type>/<name>`)
- ✅ **All PRs MUST pass CI** before submission

### From CONTRIBUTING.md
- ✅ PR titles in **active imperative form**, no period
  - ✅ "fix: broken links on user profile page"
  - ❌ "Fixes a bug" or "Feature now does something"
- ��� PR description must include:
  - `## Description of changes` — checklist with `- [x]` items (not placeholder text)
  - `## GitHub issues resolved by this PR` — issue numbers or `N/A`
  - `## Quality Assurance` — success criteria (not template text)
  - `## More info` — optional additional context
- ✅ **Visual evidence** for UI/docs changes (screenshots, before/after)
- ✅ **Changesets required** for package changes
  - Use `npx @changesets/cli` to generate or create manually in `.changeset/`
- ✅ No assignments on issues — comment "I'd like to work on this" instead

### Code Quality
- ✅ All files end with **final newline** (EditorConfig: `insert_final_newline = true`)
- ✅ **Trim trailing whitespace** (EditorConfig: `trim_trailing_whitespace = true`)
- ✅ **2-space indent** for most files, **4-space for Python**, **tab for Makefile**
- ✅ **Single quotes** for YAML files (`quote_type = single`)
- ✅ **UTF-8 charset** for all files

---

## Workflow: Implementation Example

**User Request:** "Add error handling to the Python SDK for timeout scenarios"

### Agent Response:

**Phase 1: Understand**
```
Intent: Implementation (add feature + tests)
Scope: packages/sdks/python
Affected: Python SDK package only (may require version bump)
Breaking change? No (additive only)
```

**Phase 2: Navigate**
```
Search: "timeout handling in Python SDK"
↓ Find: packages/sdks/python/devopness/client.py
↓ Find: packages/sdks/python/tests/test_client.py
↓ Follow: Exception types in packages/sdks/python/devopness/exceptions.py
↓ Map: How timeouts are used in examples/applications/
```

**Phase 3: Execute**
```
1. Add TimeoutError class to exceptions.py
2. Update client.py to catch and re-raise as TimeoutError
3. Add tests in test_client.py
4. Run package linter: cd packages/sdks/python && npm run lint
5. Generate changeset: npx @changesets/cli add
   - Package: devopness
   - Type: minor (new feature)
   - Description: "Add timeout error handling for API requests"
```

**Phase 4: Validate**
```
✓ No hand-edits to spec.json
✓ No workaround flags used
✓ Conventional Commits message: "feat(sdk-python): add timeout error handling"
✓ PR description complete with checklist and QA criteria
✓ CI checks passing before submitting
```

---

## Commands & Tools

### Search Operations
- **Semantic search:** Find code by meaning and intent
  - `semantic-code-search` (best for conceptual queries)
- **Lexical search:** Find exact symbols and patterns
  - `lexical-code-search` (best for known functions, classes, exact strings)

### Navigation
- **Get file contents:** `getfile` (retrieve by path)
- **Get repository data:** `get-github-data` (REST API queries, directory listings)
- **Search issues/PRs:** `semantic_issues_search` (find related discussions)

### Execution
- **Create/update files:** `create_or_update_file` (single file, one commit message)
- **Bulk file push:** `push_files` (multiple files, one atomic commit)
- **Create branches:** `create_branch` (from existing repo)
- **Create repositories:** `create_repository` (when explicitly requested)

### Session & History
- **View session logs:** `get-agent-logs` (see what prior agents did on a PR/task)
- **Session search:** `session-search` (find prior work on specific files or features)

---

## Integration with Repository Tools

### CI/CD Validation Pipeline
When creating PRs, the agent validates against:

**1. PR Lint Workflow** (`.github/workflows/pr-lint.yml`)
```yaml
Checks:
  - Title format (Conventional Commits)
  - Description required sections present
  - No merge conflicts with base branch
```

**2. Description Validator** (`.github/scripts/pr-validate-description.js`)
```
Required sections:
  ✓ ## Description of changes (with checklist items, not placeholder)
  ✓ ## GitHub issues resolved (issue numbers or N/A)
  ✓ ## Quality Assurance (success criteria, not template)
  ✓ ## More info (optional)
```

**3. Package Changesets**
```
When modifying packages/:
  ✓ .changeset/ file present
  ✓ Bump type matches user-visible change
  ✓ Description is clear
```

---

## Knowledge Base: Key Files

### Configuration & Metadata
| File | Purpose |
|------|---------|
| `package.json` | Root workspace, Changesets config, shared dependencies |
| `.editorconfig` | Code formatting rules (indent, trailing whitespace) |
| `.gitignore` | Excluded paths (node_modules, build artifacts, docs-sdk-js) |

### Guidelines
| File | Purpose |
|------|---------|
| `CONTRIBUTING.md` | Contribution flow, PR standards, changeset process |
| `AGENTS.md` | AI agent execution rules, constraints, workflow |
| `CODE_OF_CONDUCT.md` | Community standards and enforcement ladder |
| `docs/docs/authoring-guidelines.md` | Documentation writing style and frontmatter rules |

### CI/CD & Validation
| File | Purpose |
|------|---------|
| `.github/workflows/pr-lint.yml` | PR title/description validation |
| `.github/scripts/pr-validate-description.js` | PR description section checker |
| `.github/PULL_REQUEST_TEMPLATE.md` | PR template with required sections |

### Package Configs
| Path | Purpose |
|------|---------|
| `packages/sdks/javascript/` | JS/TS SDK, ESM build, TypeScript config |
| `packages/sdks/python/` | Python SDK, setuptools, pytest config |
| `packages/ui/react/` | React UI library, Storybook, component exports |

---

## Agent Operating Modes

### Exploration Mode
**Trigger:** User asks "where," "how," "what's the relationship"
**Behavior:**
- Semantic search first (intent-based)
- Traverse dependencies and callers
- Map related files and examples
- Return visual summaries (file tree, flow diagrams)

### Implementation Mode
**Trigger:** User asks to add, fix, or refactor
**Behavior:**
- Navigate to affected packages
- Run package-local checks
- Apply scoped changes
- Generate/update changesets
- Prepare PR with validation

### Documentation Mode
**Trigger:** User asks to update docs, examples, or guides
**Behavior:**
- Follow authoring guidelines from `docs/docs/authoring-guidelines.md`
- Preserve frontmatter and metadata
- Add visual evidence (screenshots, examples)
- Link to related code and issues

### Review Mode
**Trigger:** User asks to review a file, PR, or change
**Behavior:**
- Analyze attachment or fetch target
- Check against patterns and conventions
- Cross-reference with tests and callers
- Surface risks and suggestions

---

## Quick Reference: Agent Checklist

- [ ] **Request understood** — intent and scope clear
- [ ] **Corpus navigated** — relevant files and symbols located
- [ ] **Changes scoped** — affects only necessary packages
- [ ] **Package config used** — ran linters from package directory
- [ ] **No generated files edited** — avoided spec.json, etc.
- [ ] **Conventional Commits** — feat/fix/refactor/chore prefix
- [ ] **Changesets added** — for all affected packages
- [ ] **PR template matched** — all required sections present
- [ ] **CI validation ready** — checked linting rules
- [ ] **No workaround flags** — clean, standard commands only

---

## Getting Help

- **Architecture questions?** Use semantic search to explore similar patterns
- **Validation failures?** Check `.github/scripts/pr-validate-description.js`
- **Changeset confusion?** Read `CONTRIBUTING.md` "Releases" section
- **Integration examples?** Browse `examples/applications/`
- **Community support:** Discord #open-source-contributions or GitHub Discussions

---

**Version:** 1.0  
**Last Updated:** 2026-10-04  
**Maintained by:** Devopness Core Team  
**For:** AI agents and developers using this corpus
