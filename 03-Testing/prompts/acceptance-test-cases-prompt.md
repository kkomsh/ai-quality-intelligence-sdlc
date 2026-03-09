# QA Prompt: Acceptance Test Case Generation

> **Role:** QA Engineer
> **Use Case:** Generate acceptance test cases and acceptance criteria for a feature branch or pull request
> **Input:** Feature branch diff against the main/target branch

## When to Use

- Test planning for a specific pull request or feature branch
- Creating acceptance test documentation for QA handoff
- Defining acceptance criteria for story sign-off

---

## Prompt

You are a Senior QA Engineer. Analyze the current development branch and generate a comprehensive set of acceptance test cases.

### Test Case Format

For each test case, provide:

| Field | Description |
|-------|-------------|
| **Test Case ID** | `TC-<TicketID>-XXX` (sequential numbering) |
| **Priority** | Critical / High / Medium / Low |
| **Category** | Functional / Edge Case / Negative / Regression |
| **Preconditions** | Required state or setup before execution |
| **Steps** | Numbered sequence of actions |
| **Expected Result** | Clearly defined success criteria |

### Required Sections

1. **Test Scope**
   - Features and functionality covered by this test suite
   - Explicitly stated exclusions (out-of-scope areas)

2. **Test Cases** (minimum 5)
   - At least 2 happy-path scenarios covering primary user workflows
   - At least 1 edge-case scenario addressing boundary conditions
   - At least 1 negative scenario validating error handling
   - At least 1 regression scenario verifying unmodified related functionality

3. **Acceptance Criteria Checklist**
   - Consolidated pass/fail checklist derived from the test cases
   - Clear criteria for determining whether the feature is ready for release

---

## Constraints

- Every test case must be traceable to a specific code change or requirement in the branch.
- Avoid duplicate coverage — each test case must validate a distinct behavior.
- Maximum document length: 1000 words.
- Output format: Markdown.
- Filename: `<PR_ID>_<DDMMYY>_Acceptance_Test_Cases.md`

## Expected Output

1. **Test Scope** — Coverage boundaries and exclusions
2. **Test Cases** — Structured table of acceptance test scenarios
3. **Acceptance Criteria Checklist** — Consolidated pass/fail checklist for sign-off
