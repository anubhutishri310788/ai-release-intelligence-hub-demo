---
name: release-documentation
description: Complete release documentation generator with integrated quality audit. Generates enterprise-grade release documentation (Release Notes, Executive Summary, Quality Validation Report, SEO/GEO Recommendations) from release data, then automatically audits all outputs against quality standards. Produces: (1) 4 publishable documentation outputs, (2) comprehensive quality audit report with pass/fail status, (3) approval recommendation (APPROVED/NEEDS-REVIEW/REJECTED). End-to-end workflow combining documentation generation with built-in quality validation before publication. Do NOT auto-publish; generates and audits only.
compatibility: Requires release dataset from live sources (Jira, GitHub, Confluence) or sample data. Uses Atlassian MCP Server (Jira/Confluence) and GitHub MCP Server for data access.
---

# Complete Release Documentation Generator & Auditor

**Role:** Release Documentation Generator + Quality Auditor  
**Pipeline:** Generate → Audit → Recommend → Output  
**Input:** Validated release data (live MCP sources)  
**Outputs:** 
- 4 publishable documents (Release Notes, Executive Summary, Deployment Guide, API Docs/Breaking Changes)
- Comprehensive Quality Audit Report (6 quality dimensions, severity-based issue logging)
- Approval Recommendation (APPROVED / NEEDS-REVIEW / REJECTED)

**Constraint:** Generates and audits only; does NOT modify, auto-publish, or publish.

**Output Location:** All generated documentation files must be saved as markdown files in: `generated-outputs/`
- Release Notes → `generated-outputs/release-notes-[version].md`
- Executive Summary → `generated-outputs/executive-summary-[version].md`
- Quality Validation Report → `generated-outputs/quality-validation-report-[version].md`
- SEO/GEO Recommendations → `generated-outputs/seo-geo-recommendations-[version].md`
- Quality Audit Report → `generated-outputs/audit-report-[version].md`

---

## Overview

This skill provides a complete end-to-end workflow for release documentation: generate polished release documentation and validate quality in one operation.

### Workflow Stages

1. **Gather & Validate Input Data** — Collect release metadata from MCP servers
2. **Generate Documentation** — Produce 4 enterprise-grade outputs (Release Notes, Executive Summary, Validation Report, SEO/GEO Recommendations)
3. **Audit All Outputs** — Validate against 6 quality standards (structure, completeness, accuracy, clarity, consistency, compliance)
4. **Score & Recommend** — Calculate quality scores and provide approval recommendation
5. **Output Package** — Deliver all documents + audit report + recommendation

---

## Data Sources

**Primary Input Sources:**
- GitHub MCP Server (`@modelcontextprotocol/server-github`) — Release metadata, commits, contributors, PRs
- Atlassian MCP Server (`@modelcontextprotocol/server-atlassian`) — Jira issues, Confluence pages

**Quality Standards Reference:**
- Enterprise release management best practices
- Security and compliance requirements
- Internal documentation standards

---

## Phase 1: Gather & Validate Input Data

Query release data from your MCP-connected sources:

1. **Jira**: Query for your release version (e.g., `project = "Release Intelligence Hub" AND fixVersion = "v2.5"`)
   - Extract: issue keys, summaries, types (Feature/Bug/Security), priorities, labels
   
2. **Confluence**: Search for release-related pages
   - Extract: draft notes, deprecated features, breaking changes
   
3. **GitHub**: Fetch release data for your target version
   - Extract: commit count, contributors, PR count, release date

---

## Phase 2: Generate Documentation

### Output #1: Release Notes

**Purpose**: Customer-facing documentation organized by category.  
**Audience**: Customers, partners, support teams.  
**Structure**: Features, Fixes, Performance, Security, Deprecations, Known Issues.

**Template Structure:**
```markdown
# Release Notes — Release Intelligence Hub v2.5
**Release Date**: August 16, 2026 | **Status**: General Availability

## ✨ New Features
[Feature highlights with impact tags, benefits, and links]

## 🔧 Improvements & Fixes
[Categorized performance improvements and bug fixes with metrics]

## 🔒 Security Updates
[Security improvements with migration steps]

## 📋 Deprecations & Sunset Notices
[Deprecated features with sunset dates and migration paths]

## ⚠️ Known Issues
[Known limitations with workarounds]

## 🚀 What's Next
[Upcoming features and roadmap]

## 📞 Support
[Documentation links, support channels, contact info]
```

**Generation Instructions:**
1. Organize by category (Features, Fixes, Security, Deprecations, Known Issues)
2. Use descriptive headers with impact tags (High/Medium/Low)
3. Include benefits, not just features — focus on customer value
4. Add links to docs, guides, API references
5. Highlight security updates prominently
6. Include version/build metadata at bottom
7. Keep tone professional but accessible — avoid internal jargon
8. Target reading time: 5–7 minutes for v2.5 release notes

---

### Output #2: Executive Summary

**Purpose**: 100-word business-impact summary for C-suite, investors, partnerships.  
**Audience**: Executive leadership, board members, partners.  
**Format**: Single paragraph, exactly 100 words.  
**Tone**: Strategic, business-focused, emphasizing ROI and competitive advantage.

**CRITICAL: MUST be exactly 100 words. Use word counter. No exceptions.**

**Template Example:**
```
Release Intelligence Hub v2.5 delivers enterprise-grade release automation and AI-powered documentation, 
reducing release cycle time by 40% and cutting manual documentation effort by 60%. The new Advanced 
Analytics Dashboard provides real-time visibility into release health and deployment velocity, enabling 
data-driven decisions. Claude AI integration auto-generates polished release notes from Jira, GitHub, 
and Confluence, ensuring consistency and compliance. Slack integration keeps teams synchronized in 
real-time. The MCP Connector Framework (Enterprise) enables custom integrations without vendor lock-in. 
Security hardening includes AES-256 token encryption and enhanced RBAC. Backward compatible; zero 
migration required. Available now for all plans.

[Word count: 100]
```

**Generation Process:**
1. Extract key business metrics (efficiency gains, revenue impact, competitive advantages, risk mitigation)
2. Draft summary using business-focused language
3. Count exact words — use word processor or `wc -w`
4. Iterate to hit exactly 100 words
5. Validate tone and messaging

---

### Output #3: Quality Validation Report

**Purpose**: Automated checklist validating release readiness before publication.  
**Audience**: QA leads, release managers, engineering teams.  
**Structure**: Executive checklist, documentation quality, code quality, deployment readiness, known issues, sign-offs.

**Template Structure:**
```markdown
# Quality Validation Report — Release Intelligence Hub v2.5

## Executive Checklist
[8–10 critical pass/fail items with evidence]

## Detailed Validation
- Documentation Quality (all customer-facing docs signed off)
- Code Quality (coverage, security, performance metrics)
- Deployment Readiness (staging validated, rollback tested)

## Known Issues & Waivers
[Severity, accepted by, deadline]

## Sign-Off
[QA Lead, Release Manager, Product Owner, Eng Lead signatures]

## Appendix: Test Results
[Performance test data, security audit results]

## Release Authorization
**Release Candidate**: v2.5.0 (Build 2026.08.16.142)
**Target Release Date**: August 16, 2026
**Approval Status**: ✅ APPROVED
**Go-Live Decision**: PROCEED WITH RELEASE
```

**Validation Criteria:**
1. Executive checklist: 8–10 critical pass/fail items
2. Documentation quality: All customer-facing docs signed off
3. Code quality metrics: Coverage, security, performance
4. Deployment readiness: Staging validated, rollback tested
5. Known issues register: Severity, acceptance, remediation
6. Sign-off table: QA, Release Manager, Product Owner, Eng Lead
7. Appendix: Performance test data, security audit results
8. Go/no-go decision: Clear approval status

---

### Output #4: SEO/GEO Recommendations

**Purpose**: Optimize release visibility in search, metadata, and FAQ discovery.  
**Audience**: Marketing, content, SEO teams.  
**Components:**
- Primary/Secondary/Long-tail keywords with search volume and difficulty
- Meta tags (title, description, Open Graph, Twitter Card)
- Schema.org JSON-LD markup
- Internal linking strategy
- FAQ section for featured snippets
- Geographic targeting and localization
- Backlink acquisition targets
- Social media tactics
- Competitive positioning
- Analytics setup (Google Search Console, GA4)
- Pre-launch checklist (15+ items)
- 30-day monitoring plan

**Generation Instructions:**
1. Research primary keywords with search volume and difficulty
2. Optimize meta title (≤60 chars), description (155–160 chars)
3. Create FAQ section targeting featured snippets
4. Provide platform-specific social media messaging
5. Recommend backlink targets and content marketing opportunities
6. Set up analytics tracking and KPI dashboard
7. Create pre-launch and post-launch monitoring checklist

---

## Phase 3: Audit All Outputs

Immediately after generating all 4 documents, perform comprehensive quality audit using enterprise standards.

### Quality Standards Framework

#### A. Structure & Formatting (20 points)
- [ ] Document uses consistent heading hierarchy (H1, H2, H3)
- [ ] Paragraphs are well-formed and logically grouped
- [ ] Lists are properly formatted and parallel
- [ ] Tables have headers and aligned columns
- [ ] Code blocks have language identifiers
- [ ] No orphaned headings or excessive nesting

#### B. Content Completeness (20 points)

**Release Notes should include:**
- Version number and release date
- Executive summary
- Feature highlights
- Bug fixes
- Performance improvements
- Deprecations
- Known limitations
- Installation/upgrade instructions
- Support contact information

**SEO/GEO Recommendations should include:**
- Keywords with search volume and difficulty
- Meta tags optimized
- FAQ section
- Social media templates
- Geographic targeting
- Analytics setup

#### C. Technical Accuracy (20 points)
- [ ] All version numbers consistent across documents
- [ ] Command-line examples syntactically correct
- [ ] API endpoints match actual service paths
- [ ] Dependencies and versions match specs
- [ ] Configuration examples valid and tested
- [ ] Code snippets run without errors
- [ ] Paths, filenames, environment variables correct
- [ ] No placeholder text remaining
- [ ] URLs and links valid
- [ ] Numeric values realistic

#### D. Clarity & Accessibility (15 points)
- [ ] Language clear and jargon-free (or explained)
- [ ] Sentence structure simple and direct
- [ ] Technical terms defined on first use
- [ ] Instructions in imperative mood
- [ ] No ambiguous pronouns
- [ ] Consistent tone
- [ ] Descriptive headings
- [ ] Paragraphs under 150 words
- [ ] Key information front-loaded

#### E. Consistency & Style (15 points)
- [ ] Terminology consistent
- [ ] Product names capitalized correctly
- [ ] Date format consistent
- [ ] Code style consistent
- [ ] Punctuation consistent
- [ ] Cross-references work
- [ ] Abbreviations explained
- [ ] Active voice preferred
- [ ] Consistent person (you vs. users)
- [ ] Consistent visual formatting

#### F. Compliance & Safety (10 points)
- [ ] No hardcoded secrets (API keys, passwords, tokens)
- [ ] Security best practices mentioned
- [ ] PII not exposed in examples
- [ ] Third-party licenses mentioned
- [ ] Accessibility considerations noted
- [ ] Data retention policies mentioned
- [ ] Legal disclaimers present

---

### Issue Categorization

#### Severity Levels
| Level | Definition | Blocks Approval? |
|-------|-----------|-----------------|
| **CRITICAL** | Prevents use; contains errors; missing essential info; security risk | YES |
| **HIGH** | Misleading information; significant gaps; poor clarity | MAYBE |
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

### Quality Score Calculation

```
Quality Score = (Total Points Earned / 100) × 100

Score Bands:
  90–100  → APPROVED (ready for publication)
  75–89   → NEEDS-REVIEW (issues exist; human decision required)
  50–74   → NEEDS-REVIEW (significant issues; likely revision needed)
  <50     → REJECTED (critical issues; must be resolved before re-review)

Note: A single CRITICAL issue automatically moves score to NEEDS-REVIEW or REJECTED.
```

---

## Phase 4: Audit Report

### Quality Audit Report Template

```
═══════════════════════════════════════════════════════════════════════════════
RELEASE INTELLIGENCE HUB – QUALITY AUDIT REPORT
═══════════════════════════════════════════════════════════════════════════════

Release Version: [v2.5.0]
Release Date: [2026-08-16]
Audit Date: [YYYY-MM-DD]
Auditor: Claude QA Bot

───────────────────────────────────────────────────────────────────────────────
EXECUTIVE SUMMARY
───────────────────────────────────────────────────────────────────────────────

Overall Quality Score: [X/100]
Approval Recommendation: [ ] APPROVED  [ ] NEEDS-REVIEW  [ ] REJECTED

Documents Reviewed:
  1. Release Notes — Score: [X/100], Status: [PASS | ISSUES]
  2. Executive Summary — Score: [X/100], Status: [PASS | ISSUES]
  3. Quality Validation Report — Score: [X/100], Status: [PASS | ISSUES]
  4. SEO/GEO Recommendations — Score: [X/100], Status: [PASS | ISSUES]

Critical Issues Found: [N]
High Issues Found: [N]
Medium Issues Found: [N]
Low Issues Found: [N]

Recommendation Rationale: [Brief summary]

───────────────────────────────────────────────────────────────────────────────
DETAILED FINDINGS (per document)
───────────────────────────────────────────────────────────────────────────────

[Document-by-document breakdown with scores and issues]

───────────────────────────────────────────────────────────────────────────────
ISSUE REGISTRY
───────────────────────────────────────────────────────────────────────────────

| Issue ID | Document | Severity | Type | Description | Location | Suggested Fix |
|----------|----------|----------|------|-------------|----------|---------------|
| [IDs and details for all issues found] |

───────────────────────────────────────────────────────────────────────────────
RECOMMENDATIONS FOR HUMAN REVIEW
───────────────────────────────────────────────────────────────────────────────

Required Actions (must resolve before publication):
1. [Issue ID] – [Description] – [Priority]

Suggested Improvements (optional):
1. [Issue ID] – [Description]

Approval Conditions:
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
[ ] Return for revision (if REJECTED)

═══════════════════════════════════════════════════════════════════════════════
```

---

## Approval Decision Logic

```
IF (any CRITICAL issues found) OR (combined score < 50%)
  → RECOMMENDATION = REJECTED
  → Action: Return for revision and re-audit

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
- Modify document content
- Add or remove sections
- Auto-approve without human review for HIGH/CRITICAL issues
- Auto-publish or trigger downstream systems

✓ **DO:**
- Report all issues (including LOW severity)
- Provide specific, actionable suggestions
- Cross-reference issues across documents
- Escalate security/compliance findings
- Explain reasoning for rejection/needs-review

---

## Special Cases

### Deprecation Notices
If release contains deprecations:
- [ ] Deprecation clearly marked in Release Notes
- [ ] Migration path provided in Breaking Changes
- [ ] Timeline specified
- [ ] Impact documented

### Security Advisories
If release includes security fixes:
- [ ] CVE or advisory number documented
- [ ] Severity assessed
- [ ] Mitigation steps clear
- [ ] No premature security disclosure
- [ ] Upgrade status (recommended or required) clear

### Multi-Environment Deployments
If deployment guide covers multiple environments:
- [ ] Each environment has distinct instructions
- [ ] Environment-specific configurations highlighted
- [ ] Production safeguards clearly marked
- [ ] Differences documented

### Third-Party Integrations
If docs reference external APIs/services:
- [ ] Links are current and not temporary
- [ ] Dependencies version-pinned
- [ ] License/attribution included
- [ ] Fallback or alternative mentioned

---

## Final Checklist: Before Output

### Quality Gates

- [ ] **All 4 documents generated**:
  - [ ] Release Notes (2,000–3,000 words, organized by category)
  - [ ] Executive Summary (exactly 100 words; word count verified)
  - [ ] Quality Validation Report (all sign-offs collected; go/no-go decision made)
  - [ ] SEO/GEO Recommendations (15+ items, ready for marketing handoff)

- [ ] **Release Notes quality**:
  - [ ] No internal jargon; customer-friendly language
  - [ ] All features have benefits, not just descriptions
  - [ ] Security updates highlighted
  - [ ] Deprecations with sunset dates and migration paths
  - [ ] Known issues documented with workarounds
  - [ ] Links to docs, guides, and API reference work
  - [ ] Version/build metadata included

- [ ] **Executive Summary quality**:
  - [ ] Exactly 100 words (verified via word counter)
  - [ ] Business impact emphasized (ROI, efficiency, competitive advantage)
  - [ ] No technical jargon
  - [ ] Suitable for board-level communication

- [ ] **Quality Audit Report quality**:
  - [ ] All issues categorized by severity and type
  - [ ] Scores calculated across all 6 dimensions
  - [ ] Recommendation justified and clear
  - [ ] Next steps defined

- [ ] **SEO Recommendations quality**:
  - [ ] Keywords with search volume and difficulty researched
  - [ ] Meta tags optimized
  - [ ] FAQ section targets featured snippets
  - [ ] Social media templates include platform-specific CTAs
  - [ ] 30-day monitoring plan is actionable
  - [ ] Analytics events configured

### Publication Safety

- [ ] **Do NOT auto-publish**; all outputs require manual review by release manager
- [ ] **Versioning**: All documents stamped with v2.5 and generation date
- [ ] **Audit trail**: Document shows who generated, when, and for what release
- [ ] **Compliance**: Security/legal review completed
- [ ] **Links**: All external links tested and live
- [ ] **Formatting**: All markdown renders correctly

---

## Using These Outputs

### Release Notes & Executive Summary
**Publish to**:
- Official product website
- GitHub Releases page
- Email to customer base
- Confluence wiki (internal synchronization)

**Audience**: Customers, partners, support teams

### Quality Validation Report
**Share with**:
- Release management team
- Engineering leadership
- QA team
- Operations/DevOps

**Retention**: Archive in company wiki or release management system for audit trail

### SEO/GEO Recommendations
**Hand to**: Marketing + Content team  
**Timeline**: Execute pre-launch checklist 7–14 days before release  
**Responsibility**: Marketing owns implementation; Content owns FAQ and blog posts; Dev Rel owns social/community outreach

### Audit Report
**Use for**:
- Go/no-go release decision
- Post-release metrics tracking
- Process improvement feedback
- Compliance and audit trail documentation

---

## Workflow Integration

**This skill accepts release data from:**
- Jira (via Atlassian MCP Server): issues, versions, release notes
- GitHub (via GitHub MCP Server): commits, contributors, pull requests, releases
- Confluence (via Atlassian MCP Server): release documentation, notes
- Sample data provided in Phase 1 (validated v2.5 template)

**Release data should include:**
- Version number, release date, status
- Feature list with impacts and category
- Bug fixes and performance improvements
- Security updates and deprecations
- Known issues and workarounds

---

## Troubleshooting

### Common Issues

**Issue**: Executive summary is not exactly 100 words  
**Fix**: Use word counter tool (`wc -w`, Google Docs, or text editor). Adjust words until exactly 100.

**Issue**: Release notes read as too technical  
**Fix**: Replace jargon with customer-friendly language. Ask: "Would a non-technical customer understand this?"

**Issue**: Audit report shows HIGH issues but I want to proceed  
**Fix**: Route report to human reviewer with justification. Human can approve, waive, or require fixes.

**Issue**: Links in documents are broken  
**Fix**: During audit phase, test all URLs. Flag broken links as CRITICAL accuracy issues.

---

## Support & Resources

- **Claude Documentation**: https://docs.anthropic.com
- **GitHub MCP Server**: https://github.com/modelcontextprotocol/servers/tree/main/src/github
- **Atlassian MCP Server**: https://github.com/modelcontextprotocol/servers/tree/main/src/atlassian
- **GitHub Release API**: https://docs.github.com/en/rest/releases
- **Jira API Documentation**: https://developer.atlassian.com/cloud/jira/rest/v3
- **Confluence API Documentation**: https://developer.atlassian.com/cloud/confluence/rest/v2
- **SEO Best Practices**: https://developers.google.com/search/docs
- **Release Management Resources**: https://www.atlassian.com/team-playbook

---

## Metadata

**Skill ID:** `release-documentation`  
**Version:** 1.0  
**Type:** Generation + Audit Pipeline  
**Data Sources:** Jira, GitHub, Confluence (via MCP servers) or sample data  
**Author:** Release Intelligence Hub  
**Last Updated:** August 16, 2026  
**Enterprise Grade:** ✓ Complete end-to-end release documentation with quality assurance

---

## Output Instructions

When running this skill in Claude, save all generated outputs as individual markdown files in the `generated-outputs/` folder:

```bash
# After running this skill, create and save the following files:

generated-outputs/
├── release-notes-[version].md          # Output #1: Release Notes
├── executive-summary-[version].md      # Output #2: Executive Summary
├── quality-validation-report-[version].md  # Output #3: Quality Validation Report
├── seo-geo-recommendations-[version].md    # Output #4: SEO/GEO Recommendations
└── audit-report-[version].md           # Quality Audit Report

# Example:
generated-outputs/
├── release-notes-v2.5.md
├── executive-summary-v2.5.md
├── quality-validation-report-v2.5.md
├── seo-geo-recommendations-v2.5.md
└── audit-report-v2.5.md
```

**Naming Convention:** Replace `[version]` with your actual release version (e.g., v2.5, v3.0)

---

**End of Skill: Complete Release Documentation Generator & Auditor**
