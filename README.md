# Your Code is Talking: Are You Listening?

> **Quality Intelligence — A Codebase-Driven Playbook for AI-Enhanced QA Across Every SDLC Phase**

A curated collection of AI prompt templates and real-world examples for embedding quality intelligence throughout the entire Software Development Lifecycle. This framework treats the **codebase itself as the primary source of truth** for all quality activities — from requirements analysis to production incident response.

[![License: MIT](https://img.shields.io/badge/Code-MIT-yellow.svg)](LICENSE)
[![License: CC BY 4.0](https://img.shields.io/badge/Prompts-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![LinkedIn Article](https://img.shields.io/badge/LinkedIn-Article-blue?logo=linkedin)](https://www.linkedin.com/pulse/your-code-talking-you-listening-introducing-quality-komshilova-brfzf)

## Author

**Kira Komshilova** — Creator of the Quality Intelligence Framework

The Quality Intelligence approach — using codebase analysis as the primary source for AI-driven QA across all SDLC phases — is an original framework developed by Kira Komshilova, drawing on 20+ years of experience in software development and testing.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Kira_Komshilova-blue?logo=linkedin)](https://www.linkedin.com/in/kira-komshilova/)

## About

Quality Assurance extends far beyond test planning and automation. It is a continuous discipline spanning requirements definition through production maintenance. This repository provides ready-to-use prompt templates for **every stakeholder** in the delivery pipeline: QA Engineers, Developers, Product Owners, and Release Managers.

### Core Differentiator

| Traditional AI + QA | Quality Intelligence Approach |
|---------------------|-------------------------------|
| Test case generation | **Codebase-driven** gap analysis |
| Test automation assistance | **Impact analysis** from actual code changes |
| Test planning | **Proactive** risk identification based on code context |

## Repository Structure

```
ai-quality-intelligence-sdlc/
├── 01-Requirements/
│   ├── prompts/
│   │   ├── po-codebase-task-analysis-prompt.md
│   │   ├── po-text-gap-analysis-prompt.md
│   │   └── qa-testing-strategy-prompt.md
│   └── results/
│       ├── Health_Record_Correction_Wizard_PO_Analysis_Codebase.md
│       ├── Health_Record_Correction_Wizard_PO_Gaps_Analysis.md
│       └── Health_Record_Correction_Wizard_QA_Testing_Strategy.md
├── 02-Development/
│   ├── prompts/
│   │   └── branch-quality-assessment-prompt.md
│   └── results/
│       └── HM-4521_Branch_Quality_Assessment.md
├── 03-Testing/
│   ├── prompts/
│   │   └── acceptance-test-cases-prompt.md
│   └── results/
│       ├── HM-4521_Acceptance_Test_Cases.md
│       └── HM-4521_E2E_Test_Automation_Plan.md
├── 04-Release/
│   ├── prompts/
│   │   ├── release-notes-prompt.md
│   │   └── release-risk-assessment-prompt.md
│   └── results/
│       ├── risk-assessments/
│       │   └── Release_Version30_2_2025_Risk_Assessment_Analysis_HealthManagement.md
│       └── release-notes/
│           └── ReleaseNotes_Version30_2_2025_HealthManagement.md
└── 05-Production/
    ├── prompts/
    │   └── root-cause-analysis-prompt.md
    └── results/
        ├── HM-49313-Root-Cause-Analysis-Report-ANONYMIZED.md
        ├── HM-12345_131225-0030_Root_Cause_Analysis.md
        └── HM-12345_131225-0030_E2E_Test_Recommendations.md
```

## SDLC Phases and Prompts

| Phase | Prompt | Stakeholder | Purpose |
|-------|--------|-------------|---------|
| **Requirements** | `po-codebase-task-analysis-prompt.md` | PO | Analyze features against an existing codebase |
| **Requirements** | `po-text-gap-analysis-prompt.md` | PO | Identify gaps in text-based requirements |
| **Requirements** | `qa-testing-strategy-prompt.md` | QA | Generate a testing strategy from specifications |
| **Development** | `branch-quality-assessment-prompt.md` | Dev / QA | Assess branch quality and test coverage |
| **Testing** | `acceptance-test-cases-prompt.md` | QA | Generate acceptance test cases for a PR |
| **Release** | `release-risk-assessment-prompt.md` | RM | Assess release risk across all merged PRs |
| **Release** | `release-notes-prompt.md` | RM | Generate customer-facing release notes |
| **Production** | `root-cause-analysis-prompt.md` | All | Investigate and document production incidents |

## Quick Start

### Prerequisites

- VS Code with GitHub Copilot, Cursor IDE, or any AI-assisted coding environment
- Access to your project's Git repository
- AI model: Claude (recommended) or GPT-4

### Usage Examples

#### 1. Requirements Gap Analysis
**Prompt:** `01-Requirements/prompts/po-text-gap-analysis-prompt.md`
**Input:** Feature requirements text
**Output:** Missing scenarios, edge cases, and business gaps

#### 2. PR Quality Assessment
**Prompt:** `02-Development/prompts/branch-quality-assessment-prompt.md`
**Input:** Feature branch diff against main
**Output:** Unit test gaps, impacted areas, and regression recommendations

#### 3. Release Risk Assessment
**Prompt:** `04-Release/prompts/release-risk-assessment-prompt.md`
**Input:** Release branch with all merged PRs
**Output:** Risk score, critical areas, and testing focus

#### 4. Root Cause Analysis
**Prompt:** `05-Production/prompts/root-cause-analysis-prompt.md`
**Input:** Bugfix branch + corresponding release branch
**Output:** Technical root cause, preventability verdict, and actionable recommendations

## Observed Time Savings

| Activity | Traditional Effort | AI-Enhanced | Reduction |
|----------|-------------------|-------------|-----------|
| PR Quality Analysis | 2-4 hours | 10-15 min | ~90% |
| Release Notes | 1-2 hours | 10-15 min | ~90% |
| Release Risk Assessment | 2-4 hours | 15-20 min | ~90% |
| Root Cause Analysis | 4-8 hours | 30-60 min | ~85% |

## Important Considerations

1. **AI outputs require validation** — Always review and verify results. Humans own the final decision.
2. **Customize for your context** — Adapt prompts to your project's terminology, tech stack, and conventions.
3. **Enforce conciseness** — Use word limits in prompts to prevent verbose or unfocused output.
4. **Iterate for depth** — For complex analyses, run prompts multiple times or refine inputs progressively.

## Related Article

This repository accompanies the LinkedIn article:
**["Your Code is Talking: Are You Listening? Introducing Quality Intelligence"](https://www.linkedin.com/pulse/your-code-talking-you-listening-introducing-quality-komshilova-brfzf)**

The article explores how AI can help QA professionals embed quality intelligence across every SDLC phase — from requirements definition through production maintenance.

## License

This repository uses a **dual license**:

| Content Type | License | Usage |
|--------------|---------|-------|
| **Code & Implementations** | MIT License | Free to use, modify, distribute |
| **Prompts & Methodology** | CC BY 4.0 | Free to use with **attribution required** |

**Attribution example:**
> "Quality Intelligence" framework by Kira Komshilova, licensed under CC BY 4.0.

See [LICENSE](LICENSE) for full details.

---
