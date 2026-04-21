# Architecture & Design Workflow
# ═══════════════════════════════════════════════════════════════

```
STEP 1          STEP 2          STEP 3          STEP 4          STEP 5          STEP 6          STEP 7
Validate   ───▶ Discover &  ───▶ UX/UI       ───▶ API Spec    ───▶ Architecture───▶ HLD & LLD  ───▶ Store in
Input           Plan            Design           Update          Docs             Update          GitHub
```

---

## CONFIGURATION

```yaml
workflow:
  name: architecture-design-workflow
  version: "1.0"
  trigger: manual (architect request)

paths:
  docs: /Users/prateeksharma/Documents/ai-native/agent-factory/loan-app-prompts/output/existing-system/docs
  backend_code: /Users/prateeksharma/Documents/ai-native/agent-factory/loan-app-prompts/output/existing-system/backend
  mobile_code: /Users/prateeksharma/Documents/ai-native/agent-factory/loan-app-prompts/output/existing-system/mobile

github:
  design_docs_repo: "org/tasheel-design-docs"
  ui_code_repo: "org/tasheel-mobile"
  api_code_repo: "org/tasheel-backend"
  branch_naming: "feature/{epic-id}-{description}"
  pr_required: true
  reviewers: ["tech-lead", "architect"]

knowledge_bases:
  - kb-L0-agent-quality-standards
  - kb-L1-enterprise-architecture
  - kb-L2-payments-domain
  - kb-L3-application-baseline

mode_detection:
  greenfield_if: [no existing docs, all features change_type "new", no existing_feature_ref]
  brownfield_if: [existing docs/code present, features reference existing components]
```

---

## STEP 1: Validate Input

**Role**: Quality Gate

**Input**: Epic + Stories (Jira IDs or uploaded doc)

**Actions**:
1. Parse epic and stories from source
2. Validate: epic has title/description/scope, stories have user_story/AC/data_sensitivity/change_type
3. Determine mode (greenfield/brownfield) by checking existing docs at configured paths
4. Classify stories: UI-impacting, API-impacting, integration-impacting
5. STOP if validation fails with actionable reasons

**Output**: `{ validation: "PASS", mode, ui_changes: [...], api_changes: [...], integration_changes: [...] }`

---

## STEP 2: System Discovery & Change Planning

**Role**: Solution Architect

**Input**: Validated epic + existing codebase + existing docs

**Actions (Brownfield)**:
1. Read existing docs (HLD, LLD, API spec, integration architecture)
2. Scan affected source code (backend handlers/entities, mobile screens/ViewModels)
3. Map stories → impacted components (screens, endpoints, tables, integrations)
4. Produce Change Impact Assessment

**Actions (Greenfield)**:
1. Define bounded contexts, services, screens, APIs from requirements
2. Produce Architecture Blueprint

**Output**: `{ change_impact: { screens_modified, screens_new, apis_modified, apis_new, tables_modified, tables_new, integrations_affected } }`

---

## STEP 3: UX/UI Design

**Role**: UX/UI Designer | **Applies to**: stories with `user_facing: true`

**Brownfield**: Read existing wireframes/design-system/user-flows → annotate modifications, add new screens following existing design system

**Greenfield**: Create design system, wireframes, user flows from scratch

**Output files**: `wireframes.md`, `design-system.md`, `user-flows.md`

---

## STEP 4: API Specification

**Role**: API Designer | **Applies to**: stories with api changes

**Brownfield**: Update existing `api-spec.yaml` — add endpoints, extend schemas, maintain backward compatibility

**Greenfield**: Create full OpenAPI 3.0 spec per EA3

**Output file**: `api-spec.yaml`

---

## STEP 5: Architecture Documents

**Role**: Enterprise Architect

**Brownfield**: Update `solution-architecture.md` and `integration-architecture.md` — add new components, integrations, ADRs

**Greenfield**: Create from scratch

**Output files**: `solution-architecture.md`, `integration-architecture.md`

---

## STEP 6: HLD & LLD

**Role**: Technical Architect

**Brownfield**: Update `hld.md` (system diagrams, data flows, state machine) and `lld.md` (domain model, DB schemas, CQRS handlers, migrations)

**Greenfield**: Create from scratch

**Output files**: `hld.md`, `lld.md`

---

## STEP 7: Store Artifacts in GitHub

**Role**: DevOps / Automation Agent

**Input**: All artifacts produced in Steps 3–6 + classification of which repo each belongs to

**Actions**:

1. **Create feature branches** in each affected repo:
   ```
   Branch: feature/{epic-id}-{short-description}
   Example: feature/EP-01-bridgenow-product-config
   ```

2. **Commit design docs** to design-docs repo:
   ```
   Repo: org/tasheel-design-docs
   Branch: feature/{epic-id}-...
   Files:
     docs/solution-architecture.md    (if modified)
     docs/integration-architecture.md (if modified)
     docs/hld.md                      (if modified)
     docs/lld.md                      (if modified)
     docs/api-spec.yaml               (if modified)
     docs/wireframes.md               (if modified)
     docs/design-system.md            (if modified)
     docs/user-flows.md               (if modified)
   Commit message: "docs({epic-id}): {epic title} - architecture & design updates"
   ```

3. **Commit UI artifacts** to mobile/UI repo:
   ```
   Repo: org/tasheel-mobile
   Branch: feature/{epic-id}-...
   Files:
     docs/wireframes/{epic-id}-wireframes.md   (screen specs for devs)
     docs/design-system-updates.md             (new components if any)
   Commit message: "docs({epic-id}): UX wireframes and design specs"
   ```

4. **Commit API artifacts** to backend repo:
   ```
   Repo: org/tasheel-backend
   Branch: feature/{epic-id}-...
   Files:
     docs/api-spec.yaml                        (updated OpenAPI spec)
     docs/lld/{epic-id}-lld.md                 (handler/schema changes)
     docs/migrations/{epic-id}-migration.md    (DB migration plan)
   Commit message: "docs({epic-id}): API spec and LLD updates"
   ```

5. **Create Pull Requests** in each repo:
   ```yaml
   pr:
     title: "[{epic-id}] Architecture & Design: {epic title}"
     body: |
       ## Summary
       {epic description}
       
       ## Changes
       - {list of modified/new documents}
       
       ## Stories Covered
       - {list of story IDs and titles}
       
       ## Mode
       {greenfield | brownfield}
       
       ## Review Checklist
       - [ ] Architecture decisions align with EA standards
       - [ ] API spec is backward compatible (brownfield)
       - [ ] Wireframes follow design system
       - [ ] LLD is consistent with API spec
       - [ ] No contradictions between documents
     reviewers: [tech-lead, architect]
     labels: [architecture, design, {epic-id}]
     linked_epic: {jira-epic-key}
   ```

6. **Link artifacts back to Jira**:
   - Add comment on Jira epic with links to all PRs
   - Attach PR URLs to relevant stories
   - Update epic status to "Design Complete" (or equivalent)

**Artifact → Repo Mapping**:

| Artifact | Repo | Path |
|----------|------|------|
| solution-architecture.md | design-docs | docs/ |
| integration-architecture.md | design-docs | docs/ |
| hld.md | design-docs | docs/ |
| lld.md | design-docs + backend | docs/ + docs/lld/ |
| api-spec.yaml | design-docs + backend | docs/ + docs/ |
| wireframes.md | design-docs + mobile | docs/ + docs/wireframes/ |
| design-system.md | design-docs + mobile | docs/ + docs/ |
| user-flows.md | design-docs + mobile | docs/ + docs/ |
| migration plan | backend | docs/migrations/ |

**Quality checks before push**:
- All files are valid (YAML parses, Markdown renders, no broken links)
- No PII or secrets in committed files
- Branch name follows convention
- Commit messages follow conventional commits format
- PR description is complete

---

## EXECUTION RULES

1. **Brownfield is default** when existing docs/code found at configured paths
2. **Never delete existing content** — only add or annotate with `[{EPIC-ID}]` markers
3. **Backward compatibility** — existing APIs/schemas/flows must continue to work
4. **Cite knowledge bases** — decisions reference EA/PD sections
5. **No hallucination** — mark gaps as `INSUFFICIENT_CONTEXT`
6. **Incremental** — each step builds on previous output
7. **Atomic commits** — each repo gets one commit per workflow run (not per file)
8. **PR required** — no direct push to main; all changes go through review

---

## EXAMPLE INVOCATION

```
Architect: "Run architecture-design-workflow for EP-02 (BridgeNow Customer App Journey).
Stories: US-013 through US-025. Mode: brownfield."
```

**Execution**:
1. ✅ Validates 13 stories, mode=brownfield, UI+API changes identified
2. ✅ Discovers: CardCollectionScreen.kt modified, new BridgeNowLandingScreen, existing APIs reused
3. ✅ Updates wireframes.md (new landing page, modified card screen), user-flows.md (new BridgeNow path)
4. ✅ Updates api-spec.yaml (no new endpoints for EP-02, existing ones reused)
5. ✅ Updates solution-architecture.md (BridgeNow app journey noted)
6. ✅ Updates hld.md (new screen in nav graph), lld.md (dual auto-debit logic)
7. ✅ Pushes to GitHub:
   - `org/tasheel-design-docs` → PR with all doc updates
   - `org/tasheel-mobile` → PR with wireframe specs for mobile devs
   - `org/tasheel-backend` → PR with API spec (no changes, but LLD for dual AD)
   - Jira epic EP-02 updated with PR links
```
