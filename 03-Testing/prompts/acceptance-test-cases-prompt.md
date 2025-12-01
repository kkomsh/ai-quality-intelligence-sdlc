# Acceptance Test Cases Generation Prompt

> **Use Case**: Generate acceptance test cases and acceptance criteria for a PR/branch

## When to Use

- Test planning for a specific PR
- Creating acceptance test documentation
- QA handoff from development

## Prompt

Analyze the current development branch and generate acceptance test cases.

For each test case include:
- Test Case ID (TC-<TicketID>-XXX)
- Priority (Critical/High/Medium/Low)
- Type (Functional/Edge Case/Negative/Regression)
- Steps and Expected Result

Include:
1. Test Scope (what's covered, what's not)
2. Test Cases (minimum 5: happy paths, edge cases, error scenarios)
3. Acceptance Criteria Checklist

File name: '<PR_ID>_<DDMMYY>_Acceptance_Test_Cases.md'

The size of the file should not exceed 1000 words
