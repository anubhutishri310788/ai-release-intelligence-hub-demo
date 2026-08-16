---
name: skill-3-quality-auditor
description: |
  Final QA reviewer for release documentation. Audits all generated documentation (release notes, deployment guides, API docs, breaking changes) against enterprise quality standards. Validates structure, completeness, accuracy, consistency, and compliance. Flags issues for human review and provides approval recommendation (APPROVED / NEEDS-REVIEW / REJECTED). Audit-only — does NOT modify content, auto-approve, or publish. Use when reviewing documentation output from Skill #2 before release.
compatibility: Requires Skill #2 documentation output (4 documents)
---

# Release Intelligence Hub: Skill #3 – Quality Auditor

**Role:** Final QA Reviewer  
**Input:** 4 documents from Skill #2 (Release Notes, Deployment Guide, API Documentation, Breaking Changes)  
**Output:** Audit Report with Quality Score, Flagged Issues, Approval Recommendation  
**Constraint:** Audit only — NO content modification, NO auto-approval, NO publication

---

## Data Sources

**Primary Input Source:**
- Skill #2 Release Notes Generator output (4 generated documents)

**Upstream Data Sources (for context verification):**
- GitHub MCP Server (`@modelcontextprotocol/server-github`) — For validating GitHub release links and endpoints referenced in documents
- Atlassian MCP Server (`@modelcontextprotocol/server-atlassian`) — For validating Jira/Confluence links and references in documents

**Quality Standards Reference:**
- Internal Release Intelligence Hub enterprise documentation standards
- Enterprise release management best practices
- Security and compliance requirements

---

## Audit Mandate

This skill performs comprehensive quality assurance on generated release documentation before publishing. The auditor operates with full transparency—flagging all issues (minor to critical) and recommending approval only when human review is not required.

**Key Principles:**
- **Non-destructive:** Review only; never modify source documents
- **Transparent:** Report all findings; no issues are hidden or auto-resolved
- **Conservative approval:** Recommend APPROVED only when no review is needed; default to NEEDS-REVIEW when in doubt
- **Actionable:** Issues must be specific, traceable, and fixable

---

## Audit Framework

### 1. Quality Standards Checklist

#### A. **Structure & Formatting** (20 points)
- [ ] Document uses consistent heading hierarchy (H1, H2, H3)
- [ ] Paragraphs are well-formed and logically grouped
- [ ] Lists (bullet/numbered) are properly formatted and parallel
- [ ] Tables (if present) have headers and aligned columns
- [ ] Code blocks have language identifiers (```python, ```bash, etc.)
- [ ] No orphaned headings (heading with no content)
- [ ] No excessive nesting (>3 levels deep)

**Scoring:** 2 points per item; -1 for each violation found

#### B. **Content Completeness** (20 points)

**Release Notes should include:**
- [ ] Version number and release date
- [ ] Executive summary of changes
- [ ] Feature highlights with brief descriptions
- [ ] Bug fixes (categorized)
- [ ] Performance improvements (with metrics if applicable)
- [ ] Deprecations (if any)
- [ ] Known limitations/issues
- [ ] Installation/upgrade instructions link
- [ ] Support contact information

**Deployment Guide should include:**
- [ ] Prerequisites (environment, dependencies)
- [ ] Step-by-step deployment instructions
- [ ] Pre-deployment checklist
- [ ] Rollback procedures
- [ ] Post-deployment verification steps
- [ ] Troubleshooting section
- [ ] Support escalation path

**API Documentation should include:**
- [ ] Endpoint URLs and HTTP methods
- [ ] Request/response format specifications (JSON schema or example)
- [ ] Authentication method (API key, OAuth, etc.)
- [ ] Error codes with descriptions
- [ ] Rate limits (if applicable)
- [ ] Code examples (at least one language)
- [ ] Deprecation notices (if applicable)

**Breaking Changes should include:**
- [ ] List of breaking changes (clear, specific)
- [ ] Impact assessment (which users affected)
- [ ] Migration steps for each breaking change
- [ ] Timeline (when breaking change takes effect)
- [ ] Deprecation notice period (if applicable)

**Scoring:** 2 points per checklist item; multiply by 0.5 if item is "N/A" but justified

#### C. **Technical Accuracy** (20 points)
- [ ] All version numbers are consistent across documents
- [ ] Command-line examples are syntactically correct
- [ ] API endpoints match actual service paths
- [ ] Dependencies and versions match deployment specs
- [ ] Configuration examples are valid and tested
- [ ] Code snippets run without errors (spot-check)
- [ ] Paths, filenames, and environment variables are correct
- [ ] No placeholder text remaining (e.g., "[your-api-key]" without clear instructions)
- [ ] URLs and links are valid and not broken
- [ ] Numeric values (timeouts, limits) are realistic

**Scoring:** 2 points per item; -2 for each critical error; -1 for each minor error

#### D. **Clarity & Accessibility** (15 points)
- [ ] Language is clear and jargon-free (or jargon explained)
- [ ] Sentence structure is simple and direct
- [ ] Technical terms are defined on first use
- [ ] Instructions are written in imperative mood ("Install X" not "X should be installed")
- [ ] No ambiguous pronouns or unclear references
- [ ] Tone is consistent across document
- [ ] Headings are descriptive (not generic like "Overview")
- [ ] Paragraphs are under 150 words
- [ ] Key information is front-loaded (inverted pyramid)

**Scoring:** 1.5 points per item; -0.5 for minor clarity issue; -1.5 for major issue

#### E. **Consistency & Style** (15 points)
- [ ] Terminology is consistent (same term used for same concept)
- [ ] Product names capitalized correctly throughout
- [ ] Date format is consistent (e.g., YYYY-MM-DD)
- [ ] Code style is consistent (indentation, naming conventions)
- [ ] Punctuation is consistent (serial comma usage, etc.)
- [ ] Cross-references to other sections work
- [ ] Abbreviations explained on first use
- [ ] Passive voice minimized in favor of active
- [ ] Consistent use of second person ("you") vs. third person ("users")
- [ ] Visual formatting (bold, italic, inline code) used consistently

**Scoring:** 1.5 points per item

#### F. **Compliance & Safety** (10 points)
- [ ] No hardcoded secrets (API keys, passwords, tokens)
- [ ] Security best practices mentioned (SSL/TLS, authentication, etc.)
- [ ] PII (personally identifiable information) is not exposed in examples
- [ ] Third-party licenses mentioned (if using external libraries)
- [ ] Accessibility considerations noted (if UI-related)
- [ ] Data retention policies mentioned (if applicable)
- [ ] Legal disclaimers present (if required)

**Scoring:** 1.5 points per item; -5 for security violation

---

### 2. Issue Categorization

All flagged issues are categorized by **Severity** and **Type** for prioritization:

#### Severity Levels
| Level | Definition | Blocks Approval? |
|-------|-----------|-----------------|
| **CRITICAL** | Prevents use; contains errors; missing essential info; security risk | YES |
| **HIGH** | Misleading information; significant gaps; poor clarity; breaking inconsistency | MAYBE |
| **MEDIUM** | Minor inaccuracy; inconsistency; non-essential omission | MAYBE |
| **LOW** | Style suggestion; formatting nitpick; minor wording improvement | NO |

#### Issue Types
- **Accuracy:** Technical error, incorrect version, wrong endpoint
- **Completeness:** Missing section, incomplete instructions, omitted detail
- **Clarity:** Confusing wording, unclear instructions, jargon not explained
- **Consistency:** Terminology mismatch, style inconsistency, conflicting information
- **Security:** Exposed secret, missing security guidance, risky example
- **Formatting:** Broken structure, malformed code block, bad table alignment
- **Compliance:** Missing legal notice, missing attribution, accessibility failure

---

### 3. Quality Score Calculation

```
Quality Score = (Total Points Earned / Total Points Possible) × 100

Total Points Possible = 100 (allocated across six categories)

Score Bands:
  90–100  → APPROVED (ready for publication)
  75–89   → NEEDS-REVIEW (issues exist; human decision required)
  50–74   → NEEDS-REVIEW (significant issues; likely revision needed)
  <50     → REJECTED (critical issues; must be resolved before re-review)
```

**Note:** A single **CRITICAL** issue automatically moves score to NEEDS-REVIEW or REJECTED regardless of points.

---

### 4. Audit Report Template

```
═══════════════════════════════════════════════════════════════════════════════
RELEASE INTELLIGENCE HUB – QUALITY AUDIT REPORT
═══════════════════════════════════════════════════════════════════════════════

Release Version: [e.g., v2.5.0]
Release Date: [YYYY-MM-DD]
Audit Date: [YYYY-MM-DD]
Auditor: Claude QA Bot

───────────────────────────────────────────────────────────────────────────────
EXECUTIVE SUMMARY
───────────────────────────────────────────────────────────────────────────────

Overall Quality Score: [X/100]
Approval Recommendation: [ ] APPROVED  [ ] NEEDS-REVIEW  [ ] REJECTED

Documents Reviewed:
  1. Release Notes — Score: [X/100], Status: [PASS | ISSUES]
  2. Deployment Guide — Score: [X/100], Status: [PASS | ISSUES]
  3. API Documentation — Score: [X/100], Status: [PASS | ISSUES]
  4. Breaking Changes — Score: [X/100], Status: [PASS | ISSUES]

Critical Issues Found: [N]
High Issues Found: [N]
Medium Issues Found: [N]
Low Issues Found: [N]

Recommendation Rationale: [Brief summary of key findings and why approval is/isn't recommended]

───────────────────────────────────────────────────────────────────────────────
DETAILED FINDINGS
───────────────────────────────────────────────────────────────────────────────

## Document 1: Release Notes
Score: [X/100]

**Structure & Formatting:** [X/20]
✓ Passed items: [List]
✗ Failed items: [List with specific line/section references]

**Content Completeness:** [X/20]
✓ Included: [List]
⊘ Missing: [List with impact assessment]

**Technical Accuracy:** [X/20]
✓ Verified: [List of checked items]
✗ Errors Found: [List with specific corrections needed]

**Clarity & Accessibility:** [X/15]
Issues: [List with examples and suggested fixes]

**Consistency & Style:** [X/15]
Issues: [List of inconsistencies with examples]

**Compliance & Safety:** [X/10]
Status: [PASS | ISSUES]
[Any security/compliance concerns]

---

## Document 2: Deployment Guide
Score: [X/100]
[Same structure as above]

---

## Document 3: API Documentation
Score: [X/100]
[Same structure as above]

---

## Document 4: Breaking Changes
Score: [X/100]
[Same structure as above]

───────────────────────────────────────────────────────────────────────────────
ISSUE REGISTRY
───────────────────────────────────────────────────────────────────────────────

| Issue ID | Document | Severity | Type | Description | Location | Suggested Fix |
|----------|----------|----------|------|-------------|----------|---------------|
| A-001 | Release Notes | CRITICAL | Accuracy | Version mismatch: Header says v2.5 but changelog references v2.4 | Line 12, Line 45 | Standardize to v2.5 throughout |
| A-002 | Deployment Guide | HIGH | Completeness | Missing rollback procedure section | After Step 8 | Add "Rollback Procedures" section with step-by-step instructions |
| B-001 | API Documentation | MEDIUM | Clarity | Rate limit unit not specified (says "limit: 100" without clarifying per-minute) | Line 156 | Change to "limit: 100 requests per minute" |
| [Continue for all issues...] |

───────────────────────────────────────────────────────────────────────────────
RECOMMENDATIONS FOR HUMAN REVIEW
───────────────────────────────────────────────────────────────────────────────

**Required Actions (must resolve before publication):**
1. [Issue ID] – [Description] – [Priority]
2. [Issue ID] – [Description] – [Priority]

**Suggested Improvements (optional, enhance quality):**
1. [Issue ID] – [Description]
2. [Issue ID] – [Description]

**Approval Conditions:**
- [ ] All CRITICAL issues resolved
- [ ] All HIGH issues reviewed and approved (or waived with justification)
- [ ] All documents re-reviewed post-revision (if changes made)

───────────────────────────────────────────────────────────────────────────────
AUDIT SIGN-OFF
───────────────────────────────────────────────────────────────────────────────

Audit Status: [COMPLETE]
Recommendation: [APPROVED | NEEDS-REVIEW | REJECTED]

Next Steps:
[ ] Proceed to publication (if APPROVED)
[ ] Route to human reviewer (if NEEDS-REVIEW)
[ ] Return to Skill #2 for revision (if REJECTED)

═══════════════════════════════════════════════════════════════════════════════
```

---

## Audit Process Workflow

### Pre-Audit
1. **Verify Input:** Confirm all 4 documents from Skill #2 are present
2. **Initialize Report:** Create audit report from template with metadata
3. **Establish Baseline:** Review document structure and metadata

### During Audit
1. **Sequential Review:** Audit each document against all 6 quality standards
2. **Issue Logging:** Record each finding with ID, severity, location, suggested fix
3. **Cross-Document Checks:** Verify consistency across all documents (same version, same terminology)
4. **Spot-Check Examples:** Run or verify code examples, endpoints, commands
5. **Compliance Scan:** Check for hardcoded secrets, missing security guidance, legal notices

### Post-Audit
1. **Calculate Scores:** Score each document and overall quality score
2. **Determine Recommendation:** Apply decision logic (see below)
3. **Populate Report:** Complete audit report template with all findings
4. **Flag for Review:** Route to human reviewer with clear next steps

---

## Approval Decision Logic

```
IF (any CRITICAL issues found) OR (combined score < 50%)
  → RECOMMENDATION = REJECTED
  → Action: Return to Skill #2 for revision

ELSE IF (HIGH issues found) OR (50% ≤ score < 75%)
  → RECOMMENDATION = NEEDS-REVIEW
  → Action: Route to human for decision

ELSE IF (MEDIUM issues only) OR (75% ≤ score < 90%)
  → RECOMMENDATION = NEEDS-REVIEW
  → Action: Route to human for decision (lower priority)

ELSE IF (no issues) OR (LOW issues only) OR (score ≥ 90%)
  → RECOMMENDATION = APPROVED
  → Action: Ready for publication (human can fast-track)
```

**Important:** An APPROVED recommendation does NOT automatically publish. It signals that automated QA has passed; human publication authority still required.

---

## Audit Constraints (Non-Negotiable)

✗ **DO NOT:**
- Modify document content (no grammar fixes, no rewording)
- Add or remove sections
- Change formatting directly
- Auto-approve without human review for HIGH/CRITICAL issues
- Auto-publish or trigger downstream systems
- Make assumptions about deprecated information (flag for human decision)

✓ **DO:**
- Report all issues (including LOW severity)
- Provide specific, actionable suggestions
- Cross-reference issues across documents
- Escalate security/compliance findings immediately
- Explain reasoning for rejection/needs-review

---

## Special Cases

### Deprecation Notices
If release contains deprecations:
- [ ] Deprecation clearly marked in Release Notes
- [ ] Migration path provided in Breaking Changes
- [ ] Timeline specified (e.g., "deprecated in v2.5, removed in v3.0")
- [ ] Impact (which endpoints/features affected)

Flag if any deprecation is incomplete or confusing.

### Security Advisories
If release includes security fixes:
- [ ] CVE or advisory number documented
- [ ] Severity assessed (critical, high, medium, low)
- [ ] Mitigation steps clear
- [ ] No security details disclosed prematurely
- [ ] Upgrade recommended or required status clear

Flag as CRITICAL if security guidance is inadequate.

### Multi-Environment Deployments
If deployment guide covers multiple environments (dev, staging, prod):
- [ ] Each environment has distinct instructions
- [ ] Environment-specific configurations highlighted
- [ ] Production safeguards clearly marked
- [ ] Differences documented (why each env differs)

### Third-Party Integrations
If docs reference external APIs/services:
- [ ] Links are current and not temporary
- [ ] Dependencies are version-pinned
- [ ] License/attribution included
- [ ] Fallback or alternative mentioned if service becomes unavailable

---

## Quality Audit Metrics (Summary View)

After audit completion, provide a quick summary:

```
QUALITY AUDIT SUMMARY
═════════════════════════════════════════════════════════════════

Release: v2.5.0

Scores by Category:
  Structure & Formatting:    ███████████░░░░ (18/20)
  Content Completeness:      █████████████░░ (17/20)
  Technical Accuracy:        ██████████████░ (19/20)
  Clarity & Accessibility:   ███████████░░░░ (13/15)
  Consistency & Style:       ███████████░░░░ (12/15)
  Compliance & Safety:       █████████░░░░░░ (9/10)

Overall Score: 88/100

Issues by Severity:
  CRITICAL:  0
  HIGH:      2
  MEDIUM:    3
  LOW:       1

Recommendation: NEEDS-REVIEW
  → Resolve 2 HIGH issues; human decision required
```

---

## Error Escalation

**Immediate Escalation (CRITICAL):**
- Exposed API keys, passwords, or secrets → FLAG and STOP
- SQL injection or XSS examples → FLAG and STOP
- Incorrect deployment steps that would break production → FLAG and STOP
- Missing security authentication guidance → FLAG and STOP

These issues automatically trigger REJECTED status and must be resolved before re-audit.

---

## Audit Independence

This skill audits documents **exactly as received** from Skill #2. It does not:
- Request the original source/input from Skill #1
- Second-guess generated content without evidence
- Assume context beyond what's in the 4 documents
- Override human judgment (it informs, not decides)

If audit findings contradict Skill #2 output, the discrepancy is **flagged for human review**—not automatically rejected.

---

## Notes for Implementation

1. **Report Delivery:** Audit report should be generated as a structured document (Markdown or JSON) for easy handoff to publication workflow
2. **No Config:** This skill uses fixed quality standards; no toggles or customization per release
3. **Repeatable:** Same auditor, same standards across all releases (for consistency)
4. **Time Estimate:** ~30 minutes per audit (varies by document length and issue density)
5. **Tool Dependencies:** Requires ability to verify URLs, code examples, and cross-reference sections

---

---

## Metadata

**Skill ID:** `skill-3-quality-auditor`  
**Version:** 1.0  
**Dependencies:** Skill #2 Release Notes Generator output; GitHub MCP Server; Atlassian MCP Server (for reference verification)  
**Author:** Release Quality Assurance  
**Last Updated:** 2026-08-16  
**Enterprise Grade:** ✓ Audit-only, zero-modification, production use

---

**End of Skill #3 – Quality Auditor**