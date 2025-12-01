# Release Risk Assessment Prompt

> **Use Case**: Comprehensive analysis of all PRs in a release branch for risk assessment

## When to Use

- Before release deployment
- Release quality gate
- Release assessment

## Prompt

Please conduct a comprehensive analysis of all PRs merged in the current branch. 

Primary Objectives:
1. Assess unit test coverage completeness for all PRs in the release
2. Evaluate overall release risk based on unit testing gaps and change complexity
3. Provide actionable recommendations for manual testing focus areas

Required Analysis Structure:

1. Executive Summary:
   - Overall risk level assessment for this release
   - Summary categorization of changes (bug fixes vs. new features vs. enhancements)
   - Key risk drivers and testing gaps identified

2. Detailed PR Analysis Table:
   | PR # | PR Name | Date | Files Changed | Test Automation Coverage Status | Risk Level | Risk Rationale |
   Note: "PR Name" should be copied from the PR summary not AI generated summary.

3. For each PR execute Unit Test Assessment:
   - Confirm if unit tests are implemented for code changes in this branch.
   - If tests are missing or incomplete, identify specific functionalities or code paths lacking coverage.
   - Provide concrete examples of missing unit test cases, including edge cases, error handling, and typical successful execution paths.
   - Also check if there are other automated tests like Integration or E2E tests covering the changes in this branch.


4. Testing Recommendations:
   - PR-Specific Testing Requirements: Organized by individual PR with specific testing needs for each change
   - Required Unit Tests: Exact file paths and test cases needed for each PR
   - Manual Testing Requirements: List of tests without tests description
   - Security Testing Scenarios: Where applicable for security-critical PRs only
   - Regression Testing Focus: Based on component dependencies, evaluate the functional areas that require regression testing.

5. Application-Wide Regression Testing Strategy:
   Based on the collective impact of all PRs in the release, provide:
   - Critical functional areas requiring end-to-end testing
   - Integration points between modified components (if applicable ONLY)
   - Performance testing focus areas (if applicable ONLY)

6. Risk Mitigation:
   - Specific testing scenarios to address identified gaps organized by risk priority

! Notes:
- AVOID generic statements. Each recommendation must be directly tied to the PR analysis findings.
- AVOID vague terms like "improve testing" without concrete actions.
- AVOID high-level statements like "ensure quality" without specific testing strategies.
- AVOID duplicate content across sections. Each section must provide unique insights or recommendations.
- The size of the document should not exceed 2500 words.
- The name of the document should be 'Release_<Version_ID>_<YEAR>_Risk_Assessment_Analysis.md'.



