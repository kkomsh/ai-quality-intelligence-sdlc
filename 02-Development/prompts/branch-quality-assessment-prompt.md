# Branch Quality Assessment Prompt

> **Use Case**: Comprehensive PR/branch analysis for quality assessment during development

## When to Use

- PR code review
- Before merging feature branches
- Quality gate assessment

## Prompt

Analyse the following for the current development branch.

1. Unit Test Assessment:
   - Confirm if unit tests are implemented for code changes in this branch.
   - If tests are missing or incomplete, identify specific functionalities or code paths lacking coverage.
   - Provide concrete examples of missing unit test cases, including edge cases, error handling, and typical successful execution paths.
   - Evaluate the difficulty of implementing the missing unit tests, considering factors such as:
     - Complexity of the code changes
     - Availability of test data
     - Dependencies on other modules or components
     - Potential impact on existing tests
     - Mocking requirements for external dependencies
     - Time estimates for test development and integration into CI/CD pipelines

2. Integration API Tests:
   - Evaluate if integration API tests cover changes in this branch and identify any missing test cases.

3. End-to-End (E2E) Test Case Generation:
   - Generate a list of essential E2E test cases for this branch's changes (do not generate the tests themselves).

4. Impacted Areas:
   - Based on component dependencies, evaluate the functional areas that require regression testing.

! Notes:
- DO NOT include Security assessment or performance if not applicable.
- Avoid generic statements. Each recommendation must be directly tied to the analysis findings.
- Avoid vague terms like "improve testing" without concrete actions.
- Avoid high-level statements like "ensure quality" without specific testing strategies.
- Avoid duplicate content across sections. Each section must provide unique insights or recommendations.
- Keep the report concise - max 1000 words
- Generate the report in markdown format using the structure provided above
- File name should be "<Branch_Name>_Branch_Quality_Assessment_<DDMMYYYY-HHMM>.md"


## Expected Output

A structured report containing:
- Branch metadata (name, date, contributors, commits)
- Overall quality assessment with risk level
- Unit test coverage analysis with specific gaps
- Integration test assessment
- E2E test cases list
- Impacted areas for regression testing
- Specific recommendations

