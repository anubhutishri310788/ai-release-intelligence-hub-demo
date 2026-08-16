---
name: skill-1-preflight-validation
description: Validate GitHub release tags, Jira tickets, and Confluence documentation before release publication. Use this skill whenever preparing a release for v2.x of the Release Intelligence Hub, or when you need to audit source-of-truth completeness across GitHub, Jira (tickets/epics), and Confluence (release notes). Validates for data consistency, missing links, and blocking issues. Outputs READY / NEEDS-FIXING / BLOCKED status with itemized audit trail.
---

# Release Intelligence Hub: Preflight Validation Skill

**Purpose:** Validate GitHub, Jira, and Confluence data integrity before release publication. Audit-grade validation with zero generation — only identification of gaps and blockers.

---

## RTCCO Framework

### ROLE
**Release Operations Auditor**
- Verify data completeness across three sources of truth
- Flag blockers, gaps, and missing cross-references
- Generate audit trail for compliance and transparency
- Escalate blockers to delivery lead before publication

### TASK
**Validate release artifacts for v$ARGUMENTS**

Confirm that GitHub releases, Jira epics/tickets, and Confluence release notes are complete, consistent, and linked before shipping.

**Input:** Release version (e.g., `v2.5`, `v2.5-rc1`)  
**Output:** Validation report with status, itemized findings, and next steps

### CONTEXT

#### Data Sources
1. **GitHub Repository**
   - Repository: `anubhutishri310788/ai-release-intelligence-hub-demo`
   - Fetch: Release tag, version string, release assets, GitHub Actions status
   - URL: `https://github.com/anubhutishri310788/ai-release-intelligence-hub-demo`

2. **Jira (via Atlassian Rovo MCP)**
   - Search: Epics linked to release version
   - Retrieve: Ticket status, resolution, linked PRs, version field
   - Constraint: Access via MCP server (Atlassian Rovo) — requires live credentials

3. **Confluence (via Atlassian Rovo MCP)**
   - Search: Release notes page for version
   - Retrieve: Page status, last updated, linked issues, change summary
   - Constraint: Access via MCP server (Atlassian Rovo) — requires live credentials

#### Prerequisites
- Live access to GitHub repo (public, no auth required)
- Active Atlassian Rovo MCP connector (for Jira + Confluence)
- Release version string in semantic versioning format (v`MAJOR.MINOR` or v`MAJOR.MINOR-PRERELEASE`)

### CONSTRAINT

**Validation Only**
- ❌ Do NOT create, edit, or update any tickets, pages, or releases
- ❌ Do NOT generate release notes or commit messages
- ❌ Do NOT assume missing data — only report what exists vs. what's expected

**Flag All Issues**
- Report each gap, mismatch, or missing link individually
- Use numbered lists for audit trail
- Do NOT suppress warnings or combine issues

**Stop at Blockers**
- If critical data (e.g., GitHub release tag) is missing, output status **BLOCKED** and halt further validation

### OUTPUT

#### Report Structure
```
[STATUS: READY | NEEDS-FIXING | BLOCKED]

📋 VALIDATION AUDIT TRAIL
├─ GitHub Release Tag
├─ Jira Epics & Tickets
├─ Confluence Release Notes
└─ Cross-Reference Links

⚠️ FINDINGS
└─ [Item-by-item audit of gaps, mismatches, blockers]

✓ COMPLETION CHECKLIST
└─ [Versioned requirements met/unmet]

🚀 NEXT STEPS
└─ [Prioritized remediation or approval]
```

#### Status Definitions
- **READY:** All data present, consistent, linked; no blockers or warnings
- **NEEDS-FIXING:** Issues exist but non-blocking; items listed for remediation before release
- **BLOCKED:** Critical data missing (e.g., no GitHub tag, no Jira epic); release cannot proceed until fixed

---

## Validation Workflow

### Step 1: GitHub Release Tag Validation

**Query:** Search for release tag matching version pattern
```
GitHub Release: https://github.com/anubhutishri310788/ai-release-intelligence-hub-demo/releases/tag/v$ARGUMENTS
```

**Validate:**
- [ ] Tag exists and matches semantic version format
- [ ] Release body contains version string, date, and summary
- [ ] All expected assets are attached (if applicable)
- [ ] GitHub Actions workflow completed without failure
- [ ] No draft status (must be published)

**Flag if:**
- Tag does not exist → **BLOCKED**
- Tag is draft → **NEEDS-FIXING**
- Release body is empty → **NEEDS-FIXING**
- Assets missing or corrupted → **NEEDS-FIXING**

---

### Step 2: Jira Epic/Ticket Validation

**Query (via Atlassian Rovo):**
```
JQL: fixVersion = "v$ARGUMENTS" OR labels ~ "release-v$ARGUMENTS"
```

**Retrieve:**
- All epics marked for this release version
- All tickets with matching version/label
- Resolution status, linked PRs, assignee

**Validate:**
- [ ] Release epic exists and is marked "In Release"
- [ ] All epic children (stories/bugs) are resolved (Status: Done)
- [ ] No unresolved blockers or critical priority items
- [ ] Linked GitHub PR numbers are correct
- [ ] Version field matches release version exactly

**Flag if:**
- No epic found for version → **BLOCKED**
- Unresolved tickets in epic → **NEEDS-FIXING**
- Resolution mismatch (Jira says Done, GitHub PR is draft) → **NEEDS-FIXING**
- Missing or broken PR links → **NEEDS-FIXING**

---

### Step 3: Confluence Release Notes Validation

**Query (via Atlassian Rovo):**
```
CQL: type = "page" AND space = "RELEASES" AND title ~ "v$ARGUMENTS"
```

**Retrieve:**
- Release notes page for this version
- Page status (published vs. draft)
- Last modified date and author
- Linked Jira issues/epic
- Attached assets or tables

**Validate:**
- [ ] Release notes page exists
- [ ] Page is published (not in draft)
- [ ] Contains version number, release date, and change summary
- [ ] All major features/fixes are listed (cross-check vs. Jira epic)
- [ ] Page updated within last 7 days (close to release)
- [ ] Breaking changes (if any) clearly documented

**Flag if:**
- No release notes page → **BLOCKED**
- Page is in draft → **NEEDS-FIXING**
- No version number or date → **NEEDS-FIXING**
- Change summary incomplete or doesn't match Jira epic → **NEEDS-FIXING**
- Page not updated recently → **NEEDS-FIXING**

---

### Step 4: Cross-Reference Validation

**Validate Links:**
- [ ] GitHub release links to Jira epic
- [ ] Jira epic title matches GitHub release name
- [ ] Confluence release notes link to GitHub release
- [ ] Confluence page references Jira epic/tickets
- [ ] All three sources reference the same version string

**Flag if:**
- Mismatched version strings across sources → **NEEDS-FIXING**
- Missing back-links (e.g., Jira doesn't link to GitHub) → **NEEDS-FIXING**
- Outdated or deprecated references → **NEEDS-FIXING**

---

## Example: Validation Run for v2.5

### Input
```
Release version: v2.5
Repository: anubhutishri310788/ai-release-intelligence-hub-demo
```

### Execution

**GitHub Release Tag Check**
- ✓ Tag `v2.5` exists: `https://github.com/anubhutishri310788/ai-release-intelligence-hub-demo/releases/tag/v2.5`
- ✓ Release body: "Release Intelligence Hub v2.5 — Enhanced MCP connector support, live GitHub Actions integration"
- ✓ Published (not draft)
- ⚠️ Asset `release-notes.pdf` has no checksum

**Jira Epic Check**
- ✓ Epic found: `RIH-42` "Release v2.5 — MCP + GitHub Actions"
- ✓ All 12 child tickets marked "Done"
- ⚠️ PR link `#287` points to closed PR, but should reference merged commit in main

**Confluence Release Notes Check**
- ✓ Page exists: `/wiki/spaces/RELEASES/pages/245801/Release+Intelligence+Hub+v2.5`
- ✓ Page published
- ✓ Contains features list, breaking changes section, upgrade instructions
- ✓ Updated 2 days ago
- ⚠️ No direct link to GitHub release tag (reference by URL only)

**Cross-Reference Check**
- ✓ GitHub release links to Jira epic via PR reference
- ✓ Confluence page links to GitHub release via URL
- ✓ Version string consistent (`v2.5` across all sources)
- ⚠️ Jira epic missing Confluence page link

### Output

```
[STATUS: NEEDS-FIXING]

📋 VALIDATION AUDIT TRAIL FOR v2.5

1. GitHub Release Tag ✓ PRESENT
   - Tag: v2.5 (published)
   - Release body: 82 characters
   - Assets: 3 files (1 missing checksum)

2. Jira Epic ✓ PRESENT
   - Epic: RIH-42 (In Release)
   - Tickets: 12 items, 12 Done, 0 In Progress, 0 Blocked
   - PR links: #287 (merged)

3. Confluence Release Notes ✓ PRESENT
   - Page: Release Intelligence Hub v2.5
   - Status: Published
   - Updated: 2024-01-18 (2 days ago)
   - Sections: Features, Breaking Changes, Upgrade Guide

4. Cross-References ✓ VERIFIED
   - GitHub → Jira: OK
   - Jira → GitHub: OK (via PR)
   - Jira → Confluence: MISSING
   - Confluence → GitHub: OK (URL)
   - Version consistency: OK

⚠️ FINDINGS (3 ISSUES)

1. ⚠️ GitHub Release Asset Checksum Missing
   - File: release-notes.pdf
   - Impact: Users cannot verify download integrity
   - Severity: LOW
   - Remediation: Add SHA-256 checksum to release notes body

2. ⚠️ Jira Epic Missing Confluence Link
   - Location: RIH-42 description
   - Impact: No navigation from ticket to release notes
   - Severity: LOW
   - Remediation: Add Confluence link to epic description

3. ⚠️ GitHub PR Reference Ambiguous
   - PR #287 is closed (merged)
   - Link in Jira points to PR timeline, not commit
   - Impact: Users must track commit history manually
   - Severity: LOW
   - Remediation: Update Jira PR link to reference specific merged commit hash

✓ COMPLETION CHECKLIST

✓ GitHub release tag published
✓ Jira epic all tasks resolved
✓ Confluence release notes published
✓ Version strings consistent
✓ No critical blockers
✓ No unresolved tickets
⚠️ Cross-reference links incomplete (non-blocking)
⚠️ Documentation metadata incomplete (non-blocking)

🚀 NEXT STEPS

**Before Release Approval:**
1. Add SHA-256 checksum to GitHub release notes (10 min)
2. Add Confluence link to Jira epic RIH-42 (5 min)
3. Update Jira PR link to commit hash (5 min)
4. Re-run validation to confirm all items cleared

**Timeline:** Can ship in 1 hour after fixes applied
**Sign-off:** Release lead approval after all NEEDS-FIXING items resolved

---

STATUS: Ready for remediation → Ready for release approval
```

---

## Integration with Release Operations Workflow

### Pre-Release
1. Receive release version from delivery lead
2. Run this validation skill with `$ARGUMENTS = "v2.X"`
3. If BLOCKED → Escalate, do not proceed
4. If NEEDS-FIXING → Create remediation checklist (max 30 min work)
5. If READY → Proceed to release announcement

### Post-Release
1. Archive validation report with release notes
2. Tag report with release version and approval timestamp
3. Store in compliance log (audit trail for v2.5, v2.6, etc.)

---

## Troubleshooting

| Issue | Resolution |
|-------|-----------|
| Atlassian Rovo MCP not accessible | Verify credentials, check network access to Jira/Confluence Cloud |
| GitHub repo 404 | Confirm repo visibility, verify branch/tag spelling |
| Jira query returns no results | Check version field format (e.g., "v2.5" vs "2.5") |
| Confluence search timeout | Retry with simpler CQL; break into smaller queries |
| Cross-ref mismatch (e.g., different version strings) | Document inconsistency in FINDINGS; do not assume correct version |

---

## Metadata

**Skill ID:** `skill-1-preflight-validation`  
**Version:** 1.0  
**Dependencies:** Atlassian Rovo MCP (Jira + Confluence), web_fetch (GitHub)  
**Author:** Release Operations  
**Last Updated:** 2024-01-18  
**Enterprise Grade:** ✓ Validated, audit-ready, production use---
name: skill-1-preflight-validation
description: Validate GitHub release tags, Jira tickets, and Confluence documentation before release publication. Use this skill whenever preparing a release for v2.x of the Release Intelligence Hub, or when you need to audit source-of-truth completeness across GitHub, Jira (tickets/epics), and Confluence (release notes). Validates for data consistency, missing links, and blocking issues. Outputs READY / NEEDS-FIXING / BLOCKED status with itemized audit trail.
---

# Release Intelligence Hub: Preflight Validation Skill

**Purpose:** Validate GitHub, Jira, and Confluence data integrity before release publication. Audit-grade validation with zero generation — only identification of gaps and blockers.

---

## RTCCO Framework

### ROLE
**Release Operations Auditor**
- Verify data completeness across three sources of truth
- Flag blockers, gaps, and missing cross-references
- Generate audit trail for compliance and transparency
- Escalate blockers to delivery lead before publication

### TASK
**Validate release artifacts for v$ARGUMENTS**

Confirm that GitHub releases, Jira epics/tickets, and Confluence release notes are complete, consistent, and linked before shipping.

**Input:** Release version (e.g., `v2.5`, `v2.5-rc1`)  
**Output:** Validation report with status, itemized findings, and next steps

### CONTEXT

#### Data Sources
1. **GitHub Repository**
   - Repository: `anubhutishri310788/ai-release-intelligence-hub-demo`
   - Fetch: Release tag, version string, release assets, GitHub Actions status
   - URL: `https://github.com/anubhutishri310788/ai-release-intelligence-hub-demo`

2. **Jira (via Atlassian Rovo MCP)**
   - Search: Epics linked to release version
   - Retrieve: Ticket status, resolution, linked PRs, version field
   - Constraint: Access via MCP server (Atlassian Rovo) — requires live credentials

3. **Confluence (via Atlassian Rovo MCP)**
   - Search: Release notes page for version
   - Retrieve: Page status, last updated, linked issues, change summary
   - Constraint: Access via MCP server (Atlassian Rovo) — requires live credentials

#### Prerequisites
- Live access to GitHub repo (public, no auth required)
- Active Atlassian Rovo MCP connector (for Jira + Confluence)
- Release version string in semantic versioning format (v`MAJOR.MINOR` or v`MAJOR.MINOR-PRERELEASE`)

### CONSTRAINT

**Validation Only**
- ❌ Do NOT create, edit, or update any tickets, pages, or releases
- ❌ Do NOT generate release notes or commit messages
- ❌ Do NOT assume missing data — only report what exists vs. what's expected

**Flag All Issues**
- Report each gap, mismatch, or missing link individually
- Use numbered lists for audit trail
- Do NOT suppress warnings or combine issues

**Stop at Blockers**
- If critical data (e.g., GitHub release tag) is missing, output status **BLOCKED** and halt further validation

### OUTPUT

#### Report Structure
```
[STATUS: READY | NEEDS-FIXING | BLOCKED]

📋 VALIDATION AUDIT TRAIL
├─ GitHub Release Tag
├─ Jira Epics & Tickets
├─ Confluence Release Notes
└─ Cross-Reference Links

⚠️ FINDINGS
└─ [Item-by-item audit of gaps, mismatches, blockers]

✓ COMPLETION CHECKLIST
└─ [Versioned requirements met/unmet]

🚀 NEXT STEPS
└─ [Prioritized remediation or approval]
```

#### Status Definitions
- **READY:** All data present, consistent, linked; no blockers or warnings
- **NEEDS-FIXING:** Issues exist but non-blocking; items listed for remediation before release
- **BLOCKED:** Critical data missing (e.g., no GitHub tag, no Jira epic); release cannot proceed until fixed

---

## Validation Workflow

### Step 1: GitHub Release Tag Validation

**Query:** Search for release tag matching version pattern
```
GitHub Release: https://github.com/anubhutishri310788/ai-release-intelligence-hub-demo/releases/tag/v$ARGUMENTS
```

**Validate:**
- [ ] Tag exists and matches semantic version format
- [ ] Release body contains version string, date, and summary
- [ ] All expected assets are attached (if applicable)
- [ ] GitHub Actions workflow completed without failure
- [ ] No draft status (must be published)

**Flag if:**
- Tag does not exist → **BLOCKED**
- Tag is draft → **NEEDS-FIXING**
- Release body is empty → **NEEDS-FIXING**
- Assets missing or corrupted → **NEEDS-FIXING**

---

### Step 2: Jira Epic/Ticket Validation

**Query (via Atlassian Rovo):**
```
JQL: fixVersion = "v$ARGUMENTS" OR labels ~ "release-v$ARGUMENTS"
```

**Retrieve:**
- All epics marked for this release version
- All tickets with matching version/label
- Resolution status, linked PRs, assignee

**Validate:**
- [ ] Release epic exists and is marked "In Release"
- [ ] All epic children (stories/bugs) are resolved (Status: Done)
- [ ] No unresolved blockers or critical priority items
- [ ] Linked GitHub PR numbers are correct
- [ ] Version field matches release version exactly

**Flag if:**
- No epic found for version → **BLOCKED**
- Unresolved tickets in epic → **NEEDS-FIXING**
- Resolution mismatch (Jira says Done, GitHub PR is draft) → **NEEDS-FIXING**
- Missing or broken PR links → **NEEDS-FIXING**

---

### Step 3: Confluence Release Notes Validation

**Query (via Atlassian Rovo):**
```
CQL: type = "page" AND space = "RELEASES" AND title ~ "v$ARGUMENTS"
```

**Retrieve:**
- Release notes page for this version
- Page status (published vs. draft)
- Last modified date and author
- Linked Jira issues/epic
- Attached assets or tables

**Validate:**
- [ ] Release notes page exists
- [ ] Page is published (not in draft)
- [ ] Contains version number, release date, and change summary
- [ ] All major features/fixes are listed (cross-check vs. Jira epic)
- [ ] Page updated within last 7 days (close to release)
- [ ] Breaking changes (if any) clearly documented

**Flag if:**
- No release notes page → **BLOCKED**
- Page is in draft → **NEEDS-FIXING**
- No version number or date → **NEEDS-FIXING**
- Change summary incomplete or doesn't match Jira epic → **NEEDS-FIXING**
- Page not updated recently → **NEEDS-FIXING**

---

### Step 4: Cross-Reference Validation

**Validate Links:**
- [ ] GitHub release links to Jira epic
- [ ] Jira epic title matches GitHub release name
- [ ] Confluence release notes link to GitHub release
- [ ] Confluence page references Jira epic/tickets
- [ ] All three sources reference the same version string

**Flag if:**
- Mismatched version strings across sources → **NEEDS-FIXING**
- Missing back-links (e.g., Jira doesn't link to GitHub) → **NEEDS-FIXING**
- Outdated or deprecated references → **NEEDS-FIXING**

---

## Example: Validation Run for v2.5

### Input
```
Release version: v2.5
Repository: anubhutishri310788/ai-release-intelligence-hub-demo
```

### Execution

**GitHub Release Tag Check**
- ✓ Tag `v2.5` exists: `https://github.com/anubhutishri310788/ai-release-intelligence-hub-demo/releases/tag/v2.5`
- ✓ Release body: "Release Intelligence Hub v2.5 — Enhanced MCP connector support, live GitHub Actions integration"
- ✓ Published (not draft)
- ⚠️ Asset `release-notes.pdf` has no checksum

**Jira Epic Check**
- ✓ Epic found: `RIH-42` "Release v2.5 — MCP + GitHub Actions"
- ✓ All 12 child tickets marked "Done"
- ⚠️ PR link `#287` points to closed PR, but should reference merged commit in main

**Confluence Release Notes Check**
- ✓ Page exists: `/wiki/spaces/RELEASES/pages/245801/Release+Intelligence+Hub+v2.5`
- ✓ Page published
- ✓ Contains features list, breaking changes section, upgrade instructions
- ✓ Updated 2 days ago
- ⚠️ No direct link to GitHub release tag (reference by URL only)

**Cross-Reference Check**
- ✓ GitHub release links to Jira epic via PR reference
- ✓ Confluence page links to GitHub release via URL
- ✓ Version string consistent (`v2.5` across all sources)
- ⚠️ Jira epic missing Confluence page link

### Output

```
[STATUS: NEEDS-FIXING]

📋 VALIDATION AUDIT TRAIL FOR v2.5

1. GitHub Release Tag ✓ PRESENT
   - Tag: v2.5 (published)
   - Release body: 82 characters
   - Assets: 3 files (1 missing checksum)

2. Jira Epic ✓ PRESENT
   - Epic: RIH-42 (In Release)
   - Tickets: 12 items, 12 Done, 0 In Progress, 0 Blocked
   - PR links: #287 (merged)

3. Confluence Release Notes ✓ PRESENT
   - Page: Release Intelligence Hub v2.5
   - Status: Published
   - Updated: 2024-01-18 (2 days ago)
   - Sections: Features, Breaking Changes, Upgrade Guide

4. Cross-References ✓ VERIFIED
   - GitHub → Jira: OK
   - Jira → GitHub: OK (via PR)
   - Jira → Confluence: MISSING
   - Confluence → GitHub: OK (URL)
   - Version consistency: OK

⚠️ FINDINGS (3 ISSUES)

1. ⚠️ GitHub Release Asset Checksum Missing
   - File: release-notes.pdf
   - Impact: Users cannot verify download integrity
   - Severity: LOW
   - Remediation: Add SHA-256 checksum to release notes body

2. ⚠️ Jira Epic Missing Confluence Link
   - Location: RIH-42 description
   - Impact: No navigation from ticket to release notes
   - Severity: LOW
   - Remediation: Add Confluence link to epic description

3. ⚠️ GitHub PR Reference Ambiguous
   - PR #287 is closed (merged)
   - Link in Jira points to PR timeline, not commit
   - Impact: Users must track commit history manually
   - Severity: LOW
   - Remediation: Update Jira PR link to reference specific merged commit hash

✓ COMPLETION CHECKLIST

✓ GitHub release tag published
✓ Jira epic all tasks resolved
✓ Confluence release notes published
✓ Version strings consistent
✓ No critical blockers
✓ No unresolved tickets
⚠️ Cross-reference links incomplete (non-blocking)
⚠️ Documentation metadata incomplete (non-blocking)

🚀 NEXT STEPS

**Before Release Approval:**
1. Add SHA-256 checksum to GitHub release notes (10 min)
2. Add Confluence link to Jira epic RIH-42 (5 min)
3. Update Jira PR link to commit hash (5 min)
4. Re-run validation to confirm all items cleared

**Timeline:** Can ship in 1 hour after fixes applied
**Sign-off:** Release lead approval after all NEEDS-FIXING items resolved

---

STATUS: Ready for remediation → Ready for release approval
```

---

## Integration with Release Operations Workflow

### Pre-Release
1. Receive release version from delivery lead
2. Run this validation skill with `$ARGUMENTS = "v2.X"`
3. If BLOCKED → Escalate, do not proceed
4. If NEEDS-FIXING → Create remediation checklist (max 30 min work)
5. If READY → Proceed to release announcement

### Post-Release
1. Archive validation report with release notes
2. Tag report with release version and approval timestamp
3. Store in compliance log (audit trail for v2.5, v2.6, etc.)

---

## Troubleshooting

| Issue | Resolution |
|-------|-----------|
| Atlassian Rovo MCP not accessible | Verify credentials, check network access to Jira/Confluence Cloud |
| GitHub repo 404 | Confirm repo visibility, verify branch/tag spelling |
| Jira query returns no results | Check version field format (e.g., "v2.5" vs "2.5") |
| Confluence search timeout | Retry with simpler CQL; break into smaller queries |
| Cross-ref mismatch (e.g., different version strings) | Document inconsistency in FINDINGS; do not assume correct version |

---

## Metadata

**Skill ID:** `skill-1-preflight-validation`  
**Version:** 1.0  
**Dependencies:** Atlassian Rovo MCP (Jira + Confluence), web_fetch (GitHub)  
**Author:** Release Operations  
**Last Updated:** 2024-01-18  
**Enterprise Grade:** ✓ Validated, audit-ready, production use