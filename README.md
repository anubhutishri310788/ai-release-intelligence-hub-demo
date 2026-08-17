# Release Intelligent Hub

🚀 **AI-powered release notes automation using Claude skills and MCP connectors**

Automate your release communications workflow. Transform structured release data from GitHub, Jira, and Confluence into professional, multi-format documentation packages with built-in quality checks and SEO optimization.

---

## The Problem

Technical writers and release managers spend **6-8 hours per release** on:
- Gathering information from GitHub, Jira, Confluence
- Drafting release notes
- Validating content quality
- Optimizing for search discoverability

This manual process is **time-consuming**, **error-prone**, and **delays release communications**.

---

## The Solution

**Release Intelligent Hub** automates this entire workflow in **7-11 minutes** using Claude skills:

### Available Skills

1. **Release Pre-Flight Validation**
   - Pulls data from GitHub, Jira, Confluence via MCP
   - Validates completeness and quality
   - Prevents bad data from becoming automated bad data

2. **Release Documentation**
   - Combined generation + quality audit skill
   - Generates 4 professional outputs:
     - Release Notes (customer-facing, organized by benefit)
     - Executive Summary (exactly 100 words, business impact)
     - Quality Validation Report (automated checklist)
     - SEO/GEO Recommendations (keywords, metadata, FAQ opportunities)
   - Built-in quality audit with approval recommendations
   - Fact-checks generated content against source data
   - Maintains quality gates and brand voice consistency

---

## Data Sources (MCP Connectors)

### GitHub
- Pulls: Commits, tags, code changes
- Used for: Feature implementations, bug fixes, code quality
- Example: `feat: multi-cloud support`, `fix: race condition`

### Jira
- Pulls: Features, bug fixes, enhancements, impact levels, audiences
- Used for: Structured release data, status tracking, audience targeting
- Example: `CS-1: Multi-cloud support (High impact, Customers)`

### Confluence
- Pulls: Release specifications, style guides, documentation standards
- Used for: Release scope, tone/format requirements, brand voice
- Example: Release spec, style guide, previous releases (for consistency)

---

## Complete Workflow
CSV/Manual Input
↓
STAGE 1: Pre-Flight Validation (2-3 min)
├─ Connect to GitHub, Jira, Confluence via MCP
├─ Validate data quality
└─ Decision: READY / NEEDS-FIXING / BLOCKED
├─ READY → Continue
└─ NEEDS-FIXING → Fix data → Re-run
↓
STAGE 2: Generate & Audit Documentation (3-5 min)
├─ Pull validated data from all sources
├─ Generate Release Notes
├─ Generate Executive Summary (100 words)
├─ Generate Quality Validation Report
├─ Generate SEO/GEO Recommendations
├─ Built-in Quality Audit
└─ Decision: APPROVED / NEEDS-REVIEW / REJECTED
├─ APPROVED → Continue
└─ NEEDS-REVIEW → Human fixes items
↓
STAGE 3: Human Review & Publish (30-60 min)
├─ Technical writer approves
├─ Make final edits
└─ Publish to website, email, social media

TOTAL AUTOMATION: 5-8 minutes ⚡
TOTAL WITH HUMAN: 35-70 minutes
MANUAL EQUIVALENT: 6-8 hours 📊
TIME SAVED PER RELEASE: 5-7 hours 🎯


---

## Results

### Time Savings
| Activity | Before | After | Saved |
|----------|--------|-------|-------|
| Data gathering | 1-2 hrs | 15 min | 45-105 min |
| Release notes draft | 2-3 hrs | 2 min | 118-178 min |
| Quality check | 1-2 hrs | 1 min | 59-119 min |
| SEO optimization | 1-2 hrs | 1 min | 59-119 min |
| **TOTAL** | **6-8 hrs** | **1 hour** | **5-7 hours** |

### Quality Improvements
✅ **Consistency:** Same format, tone, structure every release  
✅ **Completeness:** All data sources represented  
✅ **Accuracy:** Pulls live data (no hallucination risk)  
✅ **Discoverability:** Built-in SEO optimization  
✅ **Customer-Ready:** Optimized for different audiences  

### Enterprise Safety
✅ **Human Approval:** Required before publishing  
✅ **Quality Gates:** Validation at every stage  
✅ **Audit Trail:** All steps logged  
✅ **Read-Only:** MCP connectors never modify systems  

---

## Skills Included

See the `/skills/` folder for complete skill definitions:

- **`skill-1-preflight-validation.md`** — Validates GitHub, Jira, Confluence data
- **`release-documentation.md`** — Generates 4-part documentation package with integrated quality audit

---

## Workflow Documentation

See `/workflow/` for complete documentation:

- **`workflow-complete-system-mcp.md`** — End-to-end workflow, timing, MCP integration details

---

## Sample Data

See `/sample-data/` for example:

- **`github-commits.txt`** — Example commits for release
- **`jira-features.txt`** — Example Jira issues
- **`confluence-spec.txt`** — Example release specification
- **`confluence-style-guide.txt`** — Example style guide

---

## How to Use This

### Step 1: Set Up Your Data Sources
- **GitHub:** Tag commits for release (v2.5, v3.0, etc.)
- **Jira:** Mark features as "Ready to Ship", bugs as "Verified"
- **Confluence:** Upload release specification and keep style guide current

### Step 2: Run Pre-Flight Validation
1. Copy Skill from `/skills/skill-1-preflight-validation.md`
2. Paste into Claude
3. Provide: Release version, GitHub repo, Jira project, Confluence pages
4. Claude pulls data and validates

### Step 3: Generate Documentation & Audit (If Pre-Flight Passes)
1. Copy Skill from `/skills/release-documentation.md`
2. Paste validated data
3. Claude generates: Release Notes + Executive Summary + Quality Report + SEO Recommendations
4. Built-in quality audit flags any issues and provides approval recommendation

### Step 4: Human Review & Publish
1. Technical writer reviews flagged items (if any)
2. Approves and publishes to website, email, social
3. Done! ✨

---

## Multi-Audience Support

Run the release-documentation skill three times with different audiences:

RUN 1: Audience = "Customers"
→ Release notes focused on benefits and ease-of-use

RUN 2: Audience = "Partners"
→ Release notes focused on integration and business opportunity

RUN 3: Audience = "Internal"
→ Release notes focused on technical details and deployment

All 3 versions generated from same GitHub/Jira/Confluence data


---

## MCP Connectors Required

- **GitHub:** Pull commits, tags, code references (read-only)
- **Jira:** Pull issues, features, status, impact levels (read-only)
- **Confluence:** Pull specifications, style guides, documentation (read-only)

All connectors are **read-only** (cannot modify systems). Release notes require **human approval** before publishing.

---

## Target Users

- **Technical Writers:** Drafting and formatting release documentation
- **Release Managers:** Coordinating release communications
- **Product Managers:** Ensuring feature visibility and discoverability
- **Documentation Teams:** Monitoring and leading release documentation

---

## Key Features

✅ **Automated data gathering** from GitHub, Jira, Confluence via MCP  
✅ **Multi-format outputs** (Release Notes, Executive Summary, Quality Report, SEO Recommendations)  
✅ **Audience customization** (Customers, Partners, Internal)  
✅ **Quality validation** with automated checklist and scoring  
✅ **SEO optimization** with keywords, metadata, FAQ suggestions  
✅ **Human oversight** at every stage (validation → generation → audit → publication)  
✅ **Version controlled** (all skills in GitHub, track changes over time)  
✅ **Reusable** (same skills work for all products/releases)  

---

## Built With

- **Claude** (Anthropic) — AI backbone for intelligent automation
- **Claude Skills** — Reusable prompt templates for specific jobs
- **MCP (Model Context Protocol)** — Safe connections to GitHub, Jira, Confluence
- **Markdown** — All skills defined in plain text (easy to understand and modify)

---

## Learning Path

This project demonstrates:

1. **Claude Skills Framework** — How to structure AI workflows using RTCCO (Role, Task, Context, Constraint, Output)
2. **MCP Integration** — How to connect Claude to enterprise tools safely
3. **Multi-Step Automation** — How to build enterprise-grade AI systems with quality gates
4. **Audience Customization** — How to generate tailored content for different users
5. **Quality Assurance** — How to maintain human oversight in automated workflows

Based on the TechWriters Tribe AI Certification course.

---

## License

[Choose a license — MIT, Apache 2.0, Creative Commons, etc.]

---

## Author

Built during TechWriters Tribe AI Certification Program  
**Anubhuti Shrivastava** — Technical Communication Lead, Persistent Systems

---

## Feedback & Questions?

- Questions about the skills? See `/skills/README.md`
- Questions about the workflow? See `/workflow/workflow-complete-system-mcp.md`
- Want to customize for your team? Edit the skill prompts in markdown
- Issues or suggestions? Open a GitHub issue

---

## Next Steps

1. ✅ Clone this repository
2. ✅ Set up your GitHub, Jira, Confluence with sample data
3. ✅ Test Skill #1 (Pre-Flight Validation) with your data
4. ✅ Test Release Documentation Skill (Generation + Audit) with validated data
5. ✅ Run end-to-end with your actual release data
6. ✅ Customize skills for your team's specific needs
7. ✅ Integrate into your release process

---

**Ready to automate your release communications?** 🚀

Start with Skill #1 and see how much time you save!
