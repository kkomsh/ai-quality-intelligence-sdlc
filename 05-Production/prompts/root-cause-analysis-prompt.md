# Production Prompt: Root Cause Analysis

> **Role:** QA Engineer / Incident Response Team
> **Use Case:** Investigate production issues by comparing bugfix branches against release branches to identify root causes and preventability
> **Input:** Bugfix branch + corresponding release branch for comparison

## When to Use

- After a hotfix or bugfix deployment to production
- Post-incident analysis and retrospectives
- Process improvement initiatives to prevent recurrence

---

## Prompt

You are a Senior QA Engineer conducting a Root Cause Analysis (RCA). Compare the `{BUGFIX_IDENTIFIER}` bugfix branch against the `{RELEASE_BRANCH}` release and deliver a comprehensive investigation.

### I. Primary Objectives

1. Identify the **causative PR(s)** from `{RELEASE_BRANCH}` that introduced the defect.
2. Determine the **specific technical root cause** (e.g., logic error, missing enum value, race condition, configuration issue).
3. Deliver a clear **Preventability Verdict**: Was this defect preventable with existing processes and tools?

### II. Comparative Analysis

**1. Bugfix Correlation and Scope**
- Identify exact branch names, dates, and scope of changes (files and lines modified) for both the bugfix and the original release.
- Reconstruct the timeline: Release Date > Issue Discovery > Bugfix Deployment.

**2. Technical Root Cause Investigation**
- Examine the code changes that caused the issue: logic errors, missing edge cases, integration failures, or configuration problems.
- Provide a side-by-side code comparison showing the original defective code and the bugfix correction.

### III. Preventability Assessment

Evaluate whether this defect was preventable by analyzing each testing layer:

| Testing Layer | Assessment Criteria |
|:---|:---|
| **A. Unit Testing** | Could unit tests have caught the core logic error? Identify specific missing test cases, including edge cases, error handling, mocking requirements, and estimated implementation effort. |
| **B. Integration Testing** | Would integration tests (API, database, component) have detected this issue? Specify the exact integration scenarios that were missing. |
| **C. Manual Acceptance Testing** | Could this issue have been caught during manual acceptance testing? Define the specific test scenarios, edge cases, or exploratory testing approaches that should have been executed. Include any UAT criteria gaps. |
| **D. E2E Automated Testing** | Should automated E2E workflows have identified this? Define the specific E2E test scenarios that were missing. |
| **E. Code Review** | What specific logic, data-handling, or architectural flaws should reviewers have flagged? Recommend code review checklist additions. |
| **F. Requirements and Specification** | Was the issue caused by missing, unclear, or incorrect requirements? If yes, identify the specific requirement gap and the process step that failed (e.g., elicitation, documentation, review). |

### IV. Required Deliverables

Generate **two separate reports** in Markdown format:

---

#### Report 1: Root Cause Analysis Document

**Filename:** `{JIRA_KEY}_{DDMMYY-HHMM}_Root_Cause_Analysis.md`
**Maximum length:** 1000 words

**Structure:**
1. **Executive Summary** — Issue description, causative PRs, and Preventability Verdict.
2. **Technical Root Cause** — Detailed code analysis explaining the failure mechanism.
3. **Preventability Assessment** — Concise evaluation across all six testing layers.
4. **Key Recommendations** — Top 3-5 actionable items to prevent recurrence.

---

#### Report 2: E2E Test Automation Recommendations

**Filename:** `{JIRA_KEY}_{DDMMYY-HHMM}_E2E_Test_Recommendations.md`
**No word limit**

**Structure:**
1. **Summary** — What E2E tests are needed and why.
2. **Targeted E2E Test Strategy** — Specific, actionable test plan for preventing recurrence of this defect.
3. **Test Scenarios** — Detailed test cases including:
   - Test name and description
   - Preconditions
   - Step-by-step test procedure
   - Expected results
   - Priority: P0 (critical) / P1 (high) / P2 (medium)
4. **Implementation Guidance** — Analyze test automation projects in the workspace and generate implementable test code. Adapt to the project's existing framework (e.g., Playwright, Selenium, Cypress).
5. **Regression Suite Integration** — Recommendations for incorporating these tests into the existing regression suite.

---

## Constraints

- Every recommendation must be directly tied to specific analysis findings.
- Avoid generic statements such as "improve testing" — provide concrete, actionable items.
- No duplicate content across the two reports; each must deliver unique value.
- Report 1 must not exceed 1000 words.
- Both reports must be generated in Markdown format.
- Save reports to the `/00 PR Reports/` directory.

## Expected Output

1. **Report 1: Root Cause Analysis** — Executive summary, technical root cause, preventability assessment, and key recommendations
2. **Report 2: E2E Test Recommendations** — Targeted test strategy, detailed test scenarios, implementation guidance, and regression integration plan
