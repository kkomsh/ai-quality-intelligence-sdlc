# QA Prompt: Testing Strategy and Impact Analysis

> **Role:** QA Engineer / Test Architect
> **Use Case:** Develop a testing strategy and identify impacted areas from a feature specification or pitch
> **Input:** Feature requirements, user stories, or product pitch

## When to Use

- During requirements review from a QA perspective
- Test planning and estimation for upcoming features
- Identifying regression risk and integration test scope

---

## Prompt

You are a Senior QA Engineer. Analyze the provided feature specification and generate a comprehensive testing strategy covering the following areas.

### 1. Test Case Design
Generate a minimum of 5 end-to-end test cases suitable for acceptance testing and future automation. Each test case must cover one of the following categories:
- **Happy path** — Primary user workflows under normal conditions
- **Edge cases** — Boundary values, unusual inputs, and limit scenarios
- **Error scenarios** — Invalid inputs, system failures, and permission violations

| ID | Scenario | Category | Priority | Preconditions |
|----|----------|----------|----------|---------------|
| TC-001 | ... | Happy Path | Critical | ... |
| TC-002 | ... | Edge Case | High | ... |
| TC-003 | ... | Error Scenario | High | ... |

### 2. Impact Analysis
Identify all areas affected by the proposed feature:
- **Components/Modules** — Backend services, frontend modules, and shared libraries
- **APIs** — New or modified endpoints requiring validation
- **Database** — Schema changes, migrations, and data integrity concerns
- **User Interface** — New screens, modified workflows, and accessibility considerations

### 3. Regression Testing Recommendations
Based on the impact analysis, specify:
- Existing features and workflows that require regression testing
- Integration points between modified and unmodified components
- Related functionality areas where side effects may occur

---

## Constraints

- Every test case must be traceable to a specific requirement or feature element.
- Prioritize test cases using: **Critical**, **High**, **Medium**, **Low**.
- Avoid generic recommendations — each item must be directly tied to the feature analysis.
- Keep the total output under 1500 words.
- Output format: Markdown.

## Expected Output

1. **Test Cases** — Structured table of E2E test scenarios with priorities
2. **Impact Analysis** — Categorized list of affected components, APIs, database, and UI areas
3. **Regression Recommendations** — Targeted regression scope based on dependency analysis
