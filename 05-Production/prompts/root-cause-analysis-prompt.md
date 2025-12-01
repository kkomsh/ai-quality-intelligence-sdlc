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
| **C. End-to-End (E2E) Testing** | Should E2E user workflows have identified this? Define the specific E2E test scenarios that were necessary. |
| **D. Code Review** | What specific logic or security flaws should human code reviewers have flagged? Recommend checklist additions. |

### IV. Required Deliverables & Structure
Please structure your analysis report using the following markdown format:

1.  **Executive Summary:** (Brief issue description, causative PRs, and clear Preventability Verdict.)
2.  **Technical Root Cause:** (Detailed code analysis and explanation of the failure.)
3.  **Detailed Preventability Verdict:** (Comprehensive analysis for each testing layer [Unit, Integration, E2E, Review].)
4.  **Testing Strategy Recommendations:** (Specific, concrete actions to prevent similar issues, including immediate testing additions, process improvements, and tool recommendations.)

### V. Constraints
* **AVOID** generic statements (e.g., "improve testing").
* Each recommendation **MUST** be directly tied to the analysis findings.
* Keep the report concise (max 1000 words).
* Generate the report in markdown format.
