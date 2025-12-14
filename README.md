# 🎯 Your Code is Talking: Are You Listening?

> **Introducing Quality Intelligence — The Playbook for Codebase-Driven QA in Every Phase**

A collection of AI prompts and real-world examples for enhancing QA processes throughout the entire SDLC. This project demonstrates how to use **codebase analysis** as the primary source of intelligence for all quality activities.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![LinkedIn Article](https://img.shields.io/badge/LinkedIn-Article-blue?logo=linkedin)](https://www.linkedin.com/pulse/your-code-talking-you-listening-introducing-quality-komshilova-brfzf)

## 📖 About

Quality Assurance isn't just about test planning and automation—it's a continuous process spanning from requirements to production maintenance. This repository provides prompts for **all stakeholders**: QA Engineers, Developers, Product Owners, and Release Managers.

### 💡 Key Insight

| Traditional AI + QA | This Approach |
|---------------------|---------------|
| Test case generation | **Codebase-driven** gap analysis |
| Test automation assistance | **Impact analysis** for code changes |
| Test planning | **Proactive** risk identification |

## 🗂️ Repository Structure

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

## 🔄 SDLC Phases & Prompts

| Phase | Prompt | Stakeholder | Use Case |
|-------|--------|-------------|----------|
| 📋 **Requirements** | `po-codebase-task-analysis-prompt.md` | PO | Analyze feature against codebase |
| 📋 **Requirements** | `po-text-gap-analysis-prompt.md` | PO | Find gaps in text requirements |
| 📋 **Requirements** | `qa-testing-strategy-prompt.md` | QA | Generate testing strategy |
| 💻 **Development** | `branch-quality-assessment-prompt.md` | Dev/QA | Assess PR quality & test coverage |
| 🧪 **Testing** | `acceptance-test-cases-prompt.md` | QA | Generate acceptance test cases |
| 🚀 **Release** | `release-risk-assessment-prompt.md` | RM | Assess release risks |
| 🚀 **Release** | `release-notes-prompt.md` | RM | Generate release notes |
| 🏥 **Production** | `root-cause-analysis-prompt.md` | All | Investigate production issues |

## 🚀 Quick Start

### Prerequisites

- VS Code with GitHub Copilot **or** Cursor IDE
- Access to your project's Git repository
- AI model: Claude (recommended) or GPT-4

### Usage Examples

#### 1. Requirements Gap Analysis

```bash
# Open your project in VS Code/Cursor
# Copy prompt from: 01-Requirements/prompts/po-text-gap-analysis-prompt.md
# Paste requirements text and run
```

**Input:** Feature requirements text  
**Output:** Missing scenarios, edge cases, business gaps

#### 2. PR Quality Assessment

```bash
# Compare your feature branch to main
# Use: 02-Development/prompts/branch-quality-assessment-prompt.md
```

**Input:** Branch diff (feature vs main)  
**Output:** Unit test gaps, impacted areas, regression recommendations

#### 3. Release Risk Assessment

```bash
# Before deployment
# Use: 04-Release/prompts/release-risk-assessment-prompt.md
```

**Input:** Release branch with all merged PRs  
**Output:** Risk score, critical areas, testing focus

#### 4. Root Cause Analysis

```bash
# After production issue
# Use: 05-Production/prompts/root-cause-analysis-prompt.md
```

**Input:** Bugfix branch + release comparison  
**Output:** Technical root cause, preventability, recommendations

## ⏱️ Time Savings

| Activity | Traditional | AI-Enhanced | Improvement |
|----------|-------------|-------------|-------------|
| PR Analysis | 2-4 hours | 10-15 min | **~90%** |
| Release Notes | 1-2 hours | 10-15 min | **~90%** |
| Risk Assessment | 2-4 hours | 15-20 min | **~90%** |
| Root Cause Analysis | 4-8 hours | 30-60 min | **~85%** |

## ⚠️ Important Disclaimers

1. **AI makes mistakes** — Always validate outputs; humans own final decisions
2. **Customize prompts** — Adapt to your project's terminology and context
3. **Keep reports concise** — Use size limits in prompts to avoid verbose output
4. **Iterate** — Run prompts multiple times for complex analyses

## 📚 Related Article

This repository accompanies the LinkedIn article:  
**["Your Code is Talking: Are You Listening? Introducing Quality Intelligence"](https://www.linkedin.com/pulse/your-code-talking-you-listening-introducing-quality-komshilova-brfzf)**

The article explores how AI can help QA engineers inject quality intelligence across all SDLC phases—not just test planning and automation, but from requirements definition through production maintenance.

## 🤝 Contributing

1. Fork the repository
2. Add new prompts following the existing structure
3. Include example outputs when possible
4. Submit a pull request

## 📄 License

[MIT License](LICENSE) — Feel free to use and adapt these prompts for your projects.

---

