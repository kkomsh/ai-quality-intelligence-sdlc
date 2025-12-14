# Root Cause Analysis Prompt

> **Use Case**: Compare bugix branches against release branches to identify preventable issues

## When to Use

- After bugfix deployment
- Post-mortem analysis
- Process improvement discussions

## Prompt

Please conduct a **Root Cause Analysis (RCA)** comparing the `{BUGFIX_IDENTIFIER}` bugfix branch against the `{RELEASE_BRANCH}` release.

### I. Primary Objectives
1.  Identify the **causative PR(s)** from `{RELEASE_BRANCH}` that introduced the bug.
2.  Determine the **specific technical root cause** (e.g., logic error, missing enum value, configuration).
3.  Provide a clear **Preventability Verdict**: Was this bugfix preventable?

### II. Comparative Analysis Tasks
Analyze the two branches to complete the following:

**1. bugfix Correlation & Scope:**
* Identify the exact branch names, dates, and scope of changes (files/lines modified) for both the bugfix and the original release.
* Analyze the timeline: Release Date → Issue Discovery → bugfix Deployment.

**2. Technical Root Cause Investigation:**
* Examine the code changes that caused the issue (logic errors, missing edge cases, integration failures).
* Provide a side-by-side code comparison showing the original problematic code and the bugfix correction.

### III. Detailed Preventability Assessment
Evaluate whether this bugfix was preventable by analyzing the failure across **all testing layers**.

| Testing Layer | Prevention Analysis |
| :--- | :--- |
| **A. Unit Testing** | Could unit tests have caught the core logic error? Identify specific missing unit test cases (including edge cases, error handling, mocking requirements, and implementation complexity assessment). |
| **B. Integration Testing** | Would integration tests (API, Database, Component) have detected this issue? Specify the exact integration scenarios that were missing. |
| **C. Manual Acceptance Testing** | Could this issue have been caught during manual acceptance testing? Define the specific test scenarios, edge cases, or exploratory testing approaches that should have been executed. Include any UAT criteria gaps. |
| **D. End-to-End (E2E) Automated Testing** | Should automated E2E user workflows have identified this? Define the specific E2E test scenarios that were necessary. |
| **E. Code Review** | What specific logic or security flaws should human code reviewers have flagged? Recommend checklist additions. |
| **F. Requirements & Specification** | Was the issue caused by missing, unclear, or incorrect requirements/specifications? If yes, identify the specific requirement gap and the process step that failed (e.g., requirement elicitation, documentation, review). |
### IV. Required Deliverables

Generate **TWO separate reports** in markdown format:

---

#### Report 1: General RCA Analysis Document
**Filename pattern:** `{JIRA_KEY}_{DDMMYY-HHMM}_Root_Cause_Analysis.md`
**Maximum length:** 1000 words

**Structure:**
1.  **Executive Summary** - Brief issue description, causative PRs, and clear Preventability Verdict.
2.  **Technical Root Cause** - Detailed code analysis and explanation of the failure.
3.  **Preventability Assessment Summary** - Concise analysis for each testing layer (Unit, Integration, Manual Acceptance, E2E Automated, Code Review, Requirements).
4.  **Key Recommendations** - Top 3-5 actionable items to prevent similar issues.

---

#### Report 2: E2E Test Automation Recommendations
**Filename pattern:** `{JIRA_KEY}_{DDMMYY-HHMM}_E2E_Test_Recommendations.md`
**No word limit**

**Structure:**
1.  **Summary** - Brief description of what E2E tests are needed and why.
2.  **Targeted E2E Test Strategy for Recurrence Prevention** - Specific, actionable E2E test plan for this hotfix.
3.  **Test Scenarios** - Detailed test cases with:
    - Test name
    - Preconditions
    - Test steps
    - Expected results
    - Priority (P0/P1/P2)
4.  **Implementation Guidance** - Analyze Test Automation projects from this workspace and generate actual, implementable test code/scenarios. If the project uses a specific framework (Selenium, Playwright, etc.), adapt the generation to that framework.
5.  **Regression Suite Integration** - Recommendations for integrating these tests into the existing regression suite.

---

### V. Constraints
* **AVOID** generic statements (e.g., "improve testing").
* Each recommendation **MUST** be directly tied to the analysis findings.
* Report 1 must not exceed 1000 words.
* Both reports must be generated in markdown format.
* Save reports to the `/00 PR Reports/` directory.