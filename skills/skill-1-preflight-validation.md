---
name: release-preflight
description: Validate release data via MCP connectors (GitHub, Jira, Confluence) — ensures commits, features, and documentation are ready before release publication
version: 1.0.0
frameworks:
  - rtcco
  - agentic-workflow
enterprise: true
---

# Release Pre-Flight Validation Skill

**Purpose:** Orchestrate multi-source data validation to ensure release readiness across GitHub (commits), Jira (feature status), and Confluence (documentation) before proceeding to release generation.

---

## RTCCO Framework

### ROLE
**Release Operations Auditor**
- Authority: Validate data integrity and completeness across all release sources
- Responsibility: Flag blockers, inconsistencies, and missing data before release publication
- Escalation path: If BLOCKED → escalate to Release Manager; if NEEDS-FIXING → notify contributors

### TASK
**Validate Release Data for $ARGUMENTS**

Given a release specification (version, date, scope), validate:
1. **GitHub** — commit history, branch status, PR merge state
2. **Jira** — feature status (resolved vs. in-progress), bug verification, epic closure
3. **Confluence** — release notes, changelog, breaking changes documentation

Succeed only when all validations pass. Block release on any critical failure.

### CONTEXT
**Data Sources & Integration Points**

#### GitHub MCP Connector
- **Endpoint:** `mcp://github`
- **Available operations:**
  - `list_repositories()` — enumerate release-tracked repos
  - `list_commits(repo, branch, since, until)` — fetch release commit range
  - `list_pull_requests(repo, state, labels)` — verify merged PRs
  - `get_branch(repo, branch)` — confirm release branch readiness

#### Jira MCP Connector
- **Endpoint:** `mcp://jira`
- **Available operations:**
  - `search_issues(jql)` — query by epic, status, version
  - `get_issue(key)` — fetch detailed issue state
  - `list_versions(project)` — enumerate release versions
  - `get_version(project, version)` — check version closure state

#### Confluence MCP Connector
- **Endpoint:** `mcp://confluence`
- **Available operations:**
  - `get_page(space, title)` — fetch release notes page
  - `list_pages(space, query)` — search for changelog, migration guide
  - `get_page_content(page_id)` — validate documentation completeness

#### Data Expectations
```
Example: v2.5 Release
├─ GitHub
│  ├─ Commits: 9 on release/v2.5 branch
│  ├─ PRs Merged: 6 (#123, #124, #127, #130, #131, #135)
│  └─ Branch Status: Protected, all checks passing
├─ Jira
│  ├─ Features: 6 RESOLVED (FEAT-100, FEAT-101, FEAT-102, FEAT-103, FEAT-104, FEAT-105)
│  ├─ Bugs: 3 VERIFIED (BUG-456, BUG-457, BUG-458)
│  ├─ Epic: v2.5 Release → 100% closed
│  └─ Version Status: RELEASED (in Jira)
└─ Confluence
   ├─ Release Notes: Exists, dated 2026-08-15
   ├─ Changelog: Complete, all 9 commits documented
   ├─ Breaking Changes: Listed with migration steps
   └─ API Documentation: Updated for v2.5 endpoints
```

### CONSTRAINT

**Validation Rules (Immutable)**

1. **Validation-Only Mode**
   - No data generation or mutation
   - No PR creation, issue closure, or page edits
   - Read-only access to all sources
   - All validations logged with timestamps

2. **Fail-Fast Behavior**
   - Stop validation chain on first CRITICAL failure
   - Continue on WARNING-level findings
   - Report all issues in final checklist

3. **Data Completeness Requirements**
   ```
   CRITICAL (blocks release):
   ✓ All commits on release branch must have linked Jira ticket
   ✓ All features marked DONE in Jira must be in changelog
   ✓ All closed Jira issues must exist in GitHub or be acknowledged
   ✓ Release notes page must exist and be dated correctly
   ✓ No "draft" or "TBD" sections in documentation
   ✓ Breaking changes section must be populated (or explicitly "None")
   ✓ Version in Jira must be RELEASED or READY-FOR-RELEASE
   
   WARNING (requires review but doesn't block):
   ⚠ Commit count mismatch (expected vs. actual)
   ⚠ Undocumented features in Confluence
   ⚠ Unlinked PRs without Jira references
   ⚠ Documentation timestamps >1 week old
   ⚠ Merged PRs awaiting final QA sign-off in Jira
   ```

4. **Escalation Rules**
   - **BLOCKED:** Release Manager must unlock; document reason in issue
   - **NEEDS-FIXING:** Automated notifications to contributors with fix checklist
   - **READY:** Proceed to Release Generation workflow

5. **No Auto-Publication**
   - Pre-flight validation does NOT publish release
   - Separate Release Generation skill handles publication
   - This skill is gate-keeper only

---

## OUTPUT: Validation Checklist

### Structure (Standardized)

```yaml
Release: v2.5
Status: [READY | NEEDS-FIXING | BLOCKED]
Validated: 2026-08-15T14:32:00Z
Validator: release-preflight-v1.0.0

Validation Results:
  GitHub:
    status: PASS
    commits_count: 9
    commits_validated: [hash1, hash2, ...]
    pr_status: MERGED (6/6)
    branch_protection: ENABLED
    findings: []
    
  Jira:
    status: PASS
    features_resolved: 6/6
    bugs_verified: 3/3
    epic_closure: 100%
    version_status: RELEASED
    findings: []
    
  Confluence:
    status: PASS
    release_notes_exists: true
    changelog_dated: 2026-08-15
    breaking_changes_documented: true
    api_docs_updated: true
    findings: []

Critical Issues: 0
Warnings: 0

Recommendation: ✓ READY FOR RELEASE GENERATION
Next Step: Invoke release-generation skill with output of this validation
```

### Status Definitions

| Status | Definition | Action |
|--------|-----------|--------|
| **READY** | All validations pass; no critical issues | Proceed to Release Generation |
| **NEEDS-FIXING** | Warnings present but no critical blockers | Address warnings, re-run validation |
| **BLOCKED** | Critical validation failures detected | Escalate to Release Manager; document blockers |

---

## Validation Workflow

### Phase 1: GitHub Validation (Parallel)

**Steps:**
1. Connect to GitHub MCP via configured credentials
2. List all release-tracked repositories
3. For each repo with release branch:
   - Fetch commits in range [previous-tag...HEAD] on release branch
   - Validate each commit has linked Jira ticket in message (pattern: `FEAT-\d+`, `BUG-\d+`)
   - Verify all PRs are merged (state = CLOSED with merge_commit)
   - Check branch protection is enabled
   - Confirm all status checks (CI/CD) are passing

**Critical Failures:**
- Missing release branch
- Unmerged PRs on release branch
- Branch protection disabled
- Status checks failing
- Commits without Jira references (>50% of commits)

**Warnings:**
- Old commits (>30 days without new commits)
- Uncommitted changes on release branch
- Force-push history detected

---

### Phase 2: Jira Validation (Parallel)

**Steps:**
1. Connect to Jira MCP via configured project key
2. Query version: `project = RELEASE AND fixVersion = "v2.5"`
3. For each feature (status = RESOLVED):
   - Validate issue has acceptance criteria
   - Check assignee is not empty
   - Verify linked GitHub PR exists
4. For each bug (status = VERIFIED or RESOLVED):
   - Validate root cause documented
   - Check fix verified in GitHub
5. Confirm epic closure: all child issues are CLOSED or RESOLVED
6. Check version release status (must be RELEASED or READY-FOR-RELEASE)

**Critical Failures:**
- Open issues in release epic
- Features without GitHub links
- Unverified bugs in release
- Version not marked for release in Jira
- Missing acceptance criteria on 50%+ of features

**Warnings:**
- Features in REVIEW (not yet RESOLVED)
- Bugs awaiting final QA sign-off
- Incomplete root cause analysis on some bugs

---

### Phase 3: Confluence Validation (Parallel)

**Steps:**
1. Connect to Confluence MCP to configured space
2. Locate release notes page: `/pages/v2.5-Release-Notes` (or configured path)
3. Validate page exists and contains:
   - Release date and version number
   - Feature list (cross-reference with Jira)
   - Known issues / limitations
   - Breaking changes section (must exist; can be "None")
   - Migration guide (if breaking changes present)
   - API changelog (if applicable)
   - Contributors section (optional)
4. Check for draft markers (`[DRAFT]`, `[TBD]`, `TODO:`)
5. Verify page was updated within 3 days of release date
6. Validate API documentation pages updated for new endpoints

**Critical Failures:**
- Release notes page does not exist
- Page contains `[DRAFT]` or `[TBD]` markers
- Breaking changes section missing (when changes exist)
- Migration guide absent (when breaking changes documented)
- Page not updated by release cutoff date

**Warnings:**
- Page updated >7 days before release
- Incomplete feature descriptions
- Missing contributor attribution
- API docs not cross-referenced with release notes

---

## Example: v2.5 Release Validation

### Input
```json
{
  "release_version": "v2.5",
  "release_date": "2026-08-15",
  "project_key": "REL",
  "github_repos": ["ai-release-intelligence-hub-demo"],
  "jira_project": "INTECH",
  "confluence_space": "RELEASES",
  "previous_tag": "v2.4"
}
```

### Execution Log

```
[14:32:00] Starting Pre-Flight Validation for v2.5
[14:32:01] Phase 1: GitHub Validation...

  Checking ai-release-intelligence-hub-demo
  ✓ Release branch: release/v2.5 found
  ✓ Commits: 9 new commits since v2.4
    - 059acd1: docs: document v2.5 release commits (FEAT-201)
    - 9d3d95e: Enhance README with detailed workflow (FEAT-202)
    - 5b97eda: Revise README for Release Intelligent Hub (FEAT-203)
    - ...6 more commits validated
  ✓ PRs Merged: 6/6 (#123, #124, #127, #130, #131, #135)
  ✓ Branch protection: ENABLED
  ✓ Status checks: PASSING
  
  GitHub Result: PASS

[14:32:15] Phase 2: Jira Validation...

  Project: INTECH, Version: v2.5
  ✓ Features: 6 RESOLVED
    - FEAT-201: Release Documentation System
    - FEAT-202: Automated Changelog Generation
    - FEAT-203: Multi-Source Integration
    - FEAT-204: Validation Framework
    - FEAT-205: Release Orchestration
    - FEAT-206: Cloud Deployment Support
  ✓ Bugs: 3 VERIFIED
    - BUG-456: Off-by-one in commit counter (VERIFIED)
    - BUG-457: Confluence page sync delay (VERIFIED)
    - BUG-458: Jira API timeout on large queries (VERIFIED)
  ✓ Epic v2.5 Release: 100% CLOSED
  ✓ Version Status: RELEASED
  
  Jira Result: PASS

[14:32:30] Phase 3: Confluence Validation...

  Space: RELEASES
  ✓ Release Notes page exists: /pages/v2.5-Release-Notes
  ✓ Page dated: 2026-08-15 (correct)
  ✓ Features documented: 6/6 matched with Jira
  ✓ Breaking changes: Section present, 2 items listed
  ✓ Migration guide: Present with step-by-step instructions
  ✓ API documentation: Updated (3 new endpoints)
  ✓ No draft markers found
  ✓ Page last updated: 2026-08-14T16:45:00Z (current)
  
  Confluence Result: PASS

[14:32:45] Compilation: Generating validation report...
```

### Output Report

```yaml
Release: v2.5
Status: READY
Validated: 2026-08-15T14:32:45Z
Validator: release-preflight-v1.0.0

Validation Results:
  GitHub:
    status: PASS ✓
    repository: ai-release-intelligence-hub-demo
    commits_count: 9
    commits_validated: 9/9 (100%)
    prs_merged: 6/6 (100%)
    branch_protection: ENABLED
    status_checks: PASSING
    issues: []
    
  Jira:
    status: PASS ✓
    features_resolved: 6/6 (100%)
    bugs_verified: 3/3 (100%)
    epic_closure: 100%
    version_status: RELEASED
    issues: []
    
  Confluence:
    status: PASS ✓
    release_notes_present: true
    feature_coverage: 6/6 (100%)
    breaking_changes_documented: true
    migration_guide_present: true
    api_docs_updated: true
    draft_markers_found: 0
    issues: []

Summary:
  Critical Issues: 0
  Warnings: 0
  Overall Coverage: 100%

Recommendation: ✓ READY FOR RELEASE GENERATION
Next Step: Invoke release-generation skill
Command: /release-generation v2.5 --preflight-validation-id=pf_2026_08_15_v2_5
```

---

## Error Handling & Escalation

### Connection Failures

| Scenario | Handling |
|----------|----------|
| GitHub API unreachable | Retry 3x with exponential backoff; escalate to Ops if persistent |
| Jira auth failed | Stop validation; require credential refresh; log incident |
| Confluence page not found | FLAG as CRITICAL; halt validation; notify Release Manager |

### Data Inconsistencies

| Issue | Severity | Action |
|-------|----------|--------|
| Commit in GitHub but not in Jira | WARNING | Add to findings; request contributor link |
| Feature in Jira not in changelog | CRITICAL | Block release; require documentation update |
| Conflicting dates across sources | WARNING | Flag for manual review; continue validation |
| Orphaned PRs (no Jira issue) | WARNING | Log; request retroactive linking if significant |

### Escalation Matrix

```
Status: BLOCKED
├─ Notify: Release Manager (via email + Slack)
├─ Include: Detailed findings + remediation steps
├─ Timeout: 4 hours for Release Manager review
└─ Fallback: Create JIRA incident ticket AUTO-ESCALATE

Status: NEEDS-FIXING
├─ Notify: Contributors via Slack + GitHub mentions
├─ Include: Clear checklist of required fixes
├─ Timeout: 24 hours for contributors to fix
└─ Auto-revalidate: Triggered by new commits

Status: READY
├─ Notify: Release Manager + Release team
├─ Include: Summary + validation report link
└─ Action: Ready for release-generation skill invocation
```

---

## Configuration & Usage

### Prerequisites
- MCP connectors configured: GitHub, Jira, Confluence
- Release branch naming convention: `release/v*`
- Jira version naming convention: Matches semantic versioning (e.g., `v2.5`)
- Confluence release notes path: `/[SPACE]/v[VERSION]-Release-Notes` (configurable)

### Invocation

```bash
# Basic invocation
/release-preflight v2.5

# With custom Jira project
/release-preflight v2.5 --jira-project=CUSTOM

# Force strict validation (fail on warnings)
/release-preflight v2.5 --strict

# Output to file
/release-preflight v2.5 --output=reports/v2.5-preflight.yaml
```

### Integration Points

- **Slack Notifications:** Status updates to `#releases` channel
- **GitHub:** Validation results posted to release PR as comment
- **Jira:** Validation ticket (AUTO-CREATED in v1.5) for audit trail
- **Confluence:** Validation report linked in release notes footer

---

## Enterprise Requirements Met

✓ **Audit Trail** — All validations logged with timestamps and validator identity  
✓ **Fail-Safe Design** — No data mutation; read-only validation  
✓ **Escalation Workflows** — Clear paths for BLOCKED → NEEDS-FIXING → READY  
✓ **Multi-Source Integrity** — Validates consistency across GitHub, Jira, Confluence  
✓ **Compliance** — Enforces documentation and breaking-change requirements  
✓ **Reproducibility** — Deterministic output; same input → same validation result  
✓ **Integration** — Works with GitHub, Jira, Confluence MCP connectors  
✓ **Error Handling** — Graceful degradation; clear escalation on failures  

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-08-15 | Initial release; RTCCO framework implementation |

---

**Skill Owner:** Release Engineering  
**Last Updated:** 2026-08-15  
**Support:** #release-intelligence-hub on Slack
