# Development Prompt: Branch Quality Assessment

> **Role:** QA Engineer / Senior Developer
> **Use Case:** Comprehensive quality assessment of a feature branch or pull request before merge
> **Input:** Feature branch diff against the main/target branch

## When to Use

- Pull request code reviews requiring quality analysis
- Pre-merge quality gate assessments
- Identifying test coverage gaps in feature branches

---

## Prompt

You are a Senior QA Engineer conducting a quality assessment of the current development branch. Analyze all code changes and deliver a structured report covering the following areas.

### 1. Unit Test Assessment
- Confirm whether unit tests are implemented for all code changes in this branch.
- If tests are missing or incomplete, identify the specific functions, methods, or code paths lacking coverage.
- Provide concrete examples of missing unit test cases, including:
  - Edge cases and boundary conditions
  - Error handling and exception paths
  - Typical successful execution paths
- Evaluate the implementation complexity of missing tests, considering:
  - Complexity of the code under test
  - Test data availability and setup requirements
  - Dependencies on other modules or external services
  - Mocking and stubbing requirements
  - Estimated effort for implementation

### 2. Integration and API Test Assessment
- Evaluate whether integration and API tests adequately cover the changes in this branch.
- Identify missing integration test scenarios, particularly at service boundaries.

### 3. End-to-End Test Case Recommendations
- Generate a list of essential E2E test cases required to validate this branch's changes.
- Include test scenario descriptions and priority levels — do not generate test implementation code.

### 4. Regression Impact Analysis
- Based on component dependencies, identify functional areas that require regression testing.
- Map modified components to their downstream consumers and integration points.

---

## Constraints

- Do NOT include security or performance assessments unless directly relevant to the code changes.
- Every recommendation must be directly tied to specific findings in the branch analysis.
- Avoid generic statements such as "improve testing" or "ensure quality" — provide concrete, actionable items.
- No duplicate content across sections; each section must provide unique insights.
- Maximum report length: 1000 words.
- Output format: Markdown.
- Filename: `<Branch_Name>_Branch_Quality_Assessment_<DDMMYYYY-HHMM>.md`

## Expected Output

A structured quality assessment report containing:
1. **Branch Metadata** — Branch name, date, contributors, and commit summary
2. **Overall Quality Rating** — Risk level with justification
3. **Unit Test Analysis** — Coverage gaps with specific missing test cases
4. **Integration Test Assessment** — API and service-level test coverage evaluation
5. **E2E Test Recommendations** — Prioritized list of recommended end-to-end test scenarios
6. **Regression Impact** — Functional areas requiring regression testing
7. **Actionable Recommendations** — Prioritized list of concrete next steps
