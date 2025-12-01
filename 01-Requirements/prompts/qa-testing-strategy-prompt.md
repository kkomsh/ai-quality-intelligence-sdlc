# QA Prompt: Testing Strategy and Impact Analysis

> **Use Case**: QA specialists planning test strategy from requirements/pitch

## When to Use

- During requirements review (QA perspective)
- Test planning phase
- Work estimation for QA activities

## Prompt
Analyze the feature pitch and generate:

1. Test Cases
   - Generate 3+ E2E test cases for acceptance testing and test automation
   - Cover happy paths, edge cases, and error scenarios

2. Impacted Areas
   - List affected components, APIs, databases, and UIs

3. Regression Recommendations
   - Identify existing functionality requiring regression testing


## Expected Output

### 1. Test Cases
| ID | Scenario | Type | Priority |
|----|----------|------|----------|
| TC-001 | Happy path scenario | E2E | Critical |
| TC-002 | Edge case scenario | E2E | High |
| TC-003 | Error handling scenario | E2E | High |

### 2. Impacted Areas
- **Components**: List of affected modules
- **APIs**: Endpoints that need testing
- **Database**: Tables/schemas affected
- **UI**: User interface areas impacted

### 3. Regression Recommendations
- Existing features requiring regression testing
- Integration points to verify
- Related functionality areas

