# Release Management Prompt: Release Risk Assessment

> **Role:** QA Lead / Release Manager
> **Use Case:** Comprehensive risk assessment of all pull requests in a release branch prior to deployment
> **Input:** Release branch containing all merged pull requests

## When to Use

- Pre-deployment quality gate evaluation
- Release readiness assessment
- Identifying testing gaps and risk areas before go-live

---

## Prompt

You are a Senior QA Lead responsible for release quality. Conduct a comprehensive analysis of all pull requests merged into the current release branch and deliver a structured risk assessment.

### 1. Executive Summary
- Overall risk level for this release: **Critical / High / Medium / Low**
- Categorization of changes: bug fixes vs. new features vs. enhancements
- Key risk drivers and testing gaps identified

### 2. PR Analysis Matrix

| # | PR Number | PR Title | Merge Date | Files Changed | Test Coverage Status | Risk Level | Risk Rationale |
|---|-----------|----------|------------|---------------|---------------------|------------|----------------|
| 1 | ... | ... | ... | ... | ... | ... | ... |

> **Note:** PR titles must be taken verbatim from the repository — do not paraphrase or generate summaries.

### 3. Unit Test Assessment (per PR)
For each PR, evaluate:
- Whether unit tests are implemented for the code changes
- Specific functions or code paths lacking test coverage
- Concrete examples of missing test cases (edge cases, error handling, happy paths)
- Whether integration or E2E tests provide supplementary coverage

### 4. Testing Recommendations
- **PR-Specific Testing Requirements** — Organized by individual PR with targeted testing needs
- **Required Unit Tests** — Exact file paths and specific test cases needed
- **Manual Testing Requirements** — Concise list of required manual test scenarios (no detailed steps)
- **Security Testing** — Applicable only for PRs with security-critical changes
- **Regression Testing Focus** — Functional areas requiring regression based on component dependencies

### 5. Application-Wide Regression Strategy
Based on the collective impact of all PRs in the release:
- Critical functional areas requiring end-to-end testing
- Integration points between modified components (only if applicable)
- Performance testing focus areas (only if applicable)

### 6. Risk Mitigation Plan
- Specific testing scenarios to address identified gaps, organized by risk priority
- Recommended deployment strategy considerations (e.g., phased rollout, feature flags)

---

## Constraints

- Every recommendation must be directly tied to specific PR analysis findings.
- Avoid generic statements such as "improve testing" or "ensure quality."
- Avoid vague directives — provide concrete, actionable testing scenarios.
- No duplicate content across sections; each section must deliver unique value.
- Maximum document length: 2500 words.
- Output format: Markdown.
- Filename: `Release_<Version_ID>_<YEAR>_Risk_Assessment_Analysis.md`

## Expected Output

1. **Executive Summary** — Release risk level with key drivers
2. **PR Analysis Matrix** — All PRs with risk ratings and test coverage status
3. **Unit Test Assessment** — Per-PR coverage analysis with identified gaps
4. **Testing Recommendations** — Targeted testing plan organized by PR and type
5. **Regression Strategy** — Application-wide regression scope based on collective impact
6. **Risk Mitigation Plan** — Prioritized testing scenarios to close identified gaps
