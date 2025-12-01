# QA Testing Strategy: Patient Record Correction Wizard - Treatment and Billing Comparison Logic

**Document Version:** 1.0  
**Date:** 2025-12-01  
**Feature:** Patient Record Correction Wizard - Sequential Record Replacement Handling  
**Priority:** High

---

## Feature Pitch Summary

> At present billing adjustments and claim correction line items in the wizard are calculated based on latest submitted Patient Records' difference compared to the Visit Summary. It does not consider multiple replacements in a row with additional billing adjustments or claim corrections.
>
> **Expected Behavior:**
> - Compare latest submitted Patient Record (Revision) with the previously submitted Patient Record (the one it replaced)
> - Calculate overpayment and correction difference amounts between records
> - Show user information based on difference and apply corrections
>
> **Scope:** Billing Adjustments and Claim Corrections only (Treatment Benefits already works correctly)

---

## 1. Test Cases

### 1.1 Happy Path Scenarios

| ID | Scenario | Type | Priority | Preconditions | Expected Result |
|----|----------|------|----------|---------------|-----------------|
| TC-001 | Overpayment correction with Record-to-Record comparison | E2E | **Critical** | Visit with 2+ submitted Records, billing adjustment on current Record | Wizard shows difference between current Record and previous Record amounts (not Visit Summary) |
| TC-002 | Claim correction (co-pay) with Record-to-Record | E2E | **Critical** | Visit with 2+ Records, service type CPY differs between Records | Co-pay correction amount = currentRecord.amount - previousRecord.amount |
| TC-003 | Claim correction (deductible DED) with Record-to-Record | E2E | **Critical** | Visit with 2+ Records, service type DED differs | Deductible correction calculated from Record difference |
| TC-004 | Claim correction (coinsurance COI) with Record-to-Record | E2E | **Critical** | Visit with 2+ Records, service type COI differs | Coinsurance correction calculated from Record difference |
| TC-005 | Multiple sequential replacements (Record₁ → Record₂ → Record₃) | E2E | **High** | 3 submitted Records in chain | Wizard compares only Record₃ vs Record₂ (latest two) |
| TC-006 | Complete correction wizard flow with new logic | E2E | **Critical** | Full flow: open wizard → verify amounts → submit | Correction amounts submitted match Record-to-Record differences |

### 1.2 Edge Case Scenarios

| ID | Scenario | Type | Priority | Preconditions | Expected Result |
|----|----------|------|----------|---------------|-----------------|
| TC-007 | First Record only (no previous Record exists) | E2E | **High** | Visit with single submitted Record | Graceful handling - use Visit Summary as baseline OR show "no correction needed" |
| TC-008 | Identical Records (zero difference) | E2E | **High** | Two Records with identical transaction amounts | "No correction needed" view displayed |
| TC-009 | Voided Record in replacement chain | E2E | **High** | Record₁ → Record₂(voided) → Record₃ | Skip voided Record, compare Record₃ with Record₁ |
| TC-010 | New transaction added in replacement Record | E2E | **Medium** | Transaction exists in current Record, not in previous | Show as new billing adjustment with 0 on previous |
| TC-011 | Transaction removed in replacement Record | E2E | **Medium** | Transaction existed in previous Record, removed in current | Handle as "previously reported, now removed" |
| TC-012 | Negative difference (amount decreased) | E2E | **Medium** | Current Record amount < previous Record amount | Show negative correction or flag for review |
| TC-013 | Same-day multiple Record replacements | E2E | **Medium** | Multiple Records with same date, different timestamps | Order by CreatedAt, compare latest two |

### 1.3 Error Handling Scenarios

| ID | Scenario | Type | Priority | Preconditions | Expected Result |
|----|----------|------|----------|---------------|-----------------|
| TC-014 | API error when fetching billing analysis | E2E | **High** | Backend returns 500 error | Appropriate error message displayed, wizard remains usable |
| TC-015 | Network timeout on correction submission | E2E | **Medium** | Slow network during POST | Loading state shown, retry option available |

### 1.4 Regression Test Cases (Treatment Benefits)

| ID | Scenario | Type | Priority | Preconditions | Expected Result |
|----|----------|------|----------|---------------|-----------------|
| TC-REG-001 | Treatment Benefits correction still works | E2E | **Critical** | Visit with treatment benefit changes between Records | TB correction amounts unchanged by new changes |
| TC-REG-002 | TB wizard step unaffected by Billing changes | E2E | **Critical** | Complete TB correction flow | All TB deduction calculations correct |
| TC-REG-003 | Mixed correction (TB + Billing) in same session | E2E | **High** | Visit needing both TB and Billing corrections | Each type calculated independently using correct logic |

---

## 2. Impacted Areas

### 2.1 Components

| Component | File Path | Impact Level | Test Focus |
|-----------|-----------|--------------|------------|
| **VisitCorrectionService** | `backend/Application/Visits/VisitCorrectionService.cs` | **High** | New `GetBillingCorrectionAnalysis` method |
| **CorrectionWizardModal** | `ui/src/views/.../CorrectionWizardModal.tsx` | **High** | Data source change from direct filtering to API call |
| **BillingAdjustmentCorrection** | `ui/src/views/.../BillingAdjustmentCorrection.tsx` | **High** | Props now from API response |
| **calculateBillingData utility** | `ui/src/views/.../utils.ts` | **High** | Calculation logic change |
| **useBillingFormLogic** | `ui/src/views/.../useBillingFormLogic.ts` | **Medium** | Input data structure change |
| **correctionWizard queries** | `ui/src/queries/correctionWizard.ts` | **Medium** | New `useBillingCorrectionAnalysis` hook |
| **PatientRecordRepository** | `backend/Persistence/.../PatientRecordRepository.cs` | **Low** | Possible query modification for status filter |

### 2.2 APIs

| Endpoint | Method | Change Type | Test Focus |
|----------|--------|-------------|------------|
| `GET /visits/{id}/corrections/billing-analysis` | GET | **New** | Response structure, data accuracy |
| `GET /visits/{id}/corrections/treatment-benefits` | GET | Unchanged | **Regression** - verify still works |
| `GET /visits/{id}/corrections/copay` | GET | Review | May need Record-based input |
| `GET /visits/{id}/corrections/deductible` | GET | Review | May need Record-based input |
| `GET /visits/{id}/corrections/coinsurance` | GET | Review | May need Record-based input |
| `POST /visits/{id}/corrections` | POST | Input change | Verify correct amounts submitted |
| `GET /visits/referencedPeriods` | GET | Unchanged | **Regression** |

### 2.3 Database

| Table/Entity | Impact | Test Focus |
|--------------|--------|------------|
| `PatientRecords` | Read query change | Verify correct 2 Records returned, status filtering |
| `Visits` | Unchanged | No direct changes |
| `VisitLineItems` | Unchanged | Verify correction line items created correctly |

### 2.4 UI Areas

| UI Area | Component | Test Focus |
|---------|-----------|------------|
| **Correction Wizard Modal** | Step navigation, data display | Correct amounts displayed |
| **Billing Adjustment Table** | Data rows | Shows Record-vs-Record differences, not Record-vs-Summary |
| **Overpayment Line Items** | Amount inputs | Pre-filled with correct difference values |
| **Claim Correction Section** | Co-pay/Deductible rows | Shows Record-vs-Record differences |
| **Coinsurance Section** | Coinsurance calculation display | Based on Record difference amounts |
| **Summary/Confirmation Step** | Final amounts | Matches calculated differences |
| **Loading/Error States** | New API integration | Proper loading spinner, error handling |

---

## 3. Regression Recommendations

### 3.1 Critical Regression Areas

| Area | Existing Tests | Additional Verification |
|------|----------------|------------------------|
| **Treatment Benefits Corrections** | `TreatmentBenefitCorrectionTests.cs` (2730 lines) | Run full test suite, verify no changes to TB logic |
| **Correction Submission** | `VisitCorrectionControllerTests.cs` (1454 lines) | Verify `AddCorrections` still works correctly |
| **Billing Wizard Flow** | Existing E2E tests | Verify UI still navigates correctly |
| **Claim Calculations** | Unit tests in `VisitCorrectionServiceTests.cs` | Verify percentage calculations unchanged |

### 3.2 Integration Points to Verify

| Integration Point | Test Strategy |
|-------------------|---------------|
| Wizard Modal → New API Endpoint | Verify data binding, loading states |
| Billing Form → Amount Calculations | Verify form pre-fills with correct values |
| Claim Form → Cost Calculations | Verify co-pay uses Record amounts |
| Submit → Backend Processing | Verify correct amounts saved to VisitLineItems |
| Record Query → Status Filtering | Verify voided Records excluded |

### 3.3 Related Functionality

| Related Feature | Test Focus |
|-----------------|------------|
| Record Submission | Verify new Record creates correct comparison baseline |
| Record Voiding | Verify voided Records properly excluded from comparison |
| Visit Recalculation | Verify corrections trigger proper recalc |
| Claims Report Generation | Verify corrections reflected in reports |
| Insurance Claim Integration | Verify corrected amounts appear in claims |

---

## 4. Test Data Requirements

### 4.1 Required Test Scenarios

| Scenario | Data Setup |
|----------|------------|
| Basic Record-to-Record comparison | Patient with 2 submitted Records, different billing adjustment amounts |
| Multiple replacements | Patient with 3+ Records in chain |
| First Record only | Patient with single submitted Record |
| Voided Record | Record chain with one voided Record |
| Zero difference | Two Records with identical amounts |
| Negative difference | Current Record amount < previous Record |

### 4.2 Test Data Values

```
Scenario: Basic Billing Correction
- Record₁ (Previous): Overpayment = €500, Service Type BILL-01
- Record₂ (Current):  Overpayment = €750, Service Type BILL-01
- Expected Difference: €250 (to be adjusted)

Scenario: Claim Correction
- Record₁: Co-pay (CPY) = €150, Deductible (DED) = €50
- Record₂: Co-pay (CPY) = €180, Deductible (DED) = €60
- Expected: Co-pay correction = €30, Deductible correction = €10
```

---

## 5. Test Execution Priority

### Phase 1: Smoke Testing (Day 1)
- [ ] TC-001: Basic billing correction with Record-to-Record
- [ ] TC-006: Complete wizard flow
- [ ] TC-REG-001: Treatment Benefits regression

### Phase 2: Core Functionality (Day 2)
- [ ] TC-002, TC-003, TC-004: All claim types
- [ ] TC-005: Multiple replacements
- [ ] TC-007: First Record edge case

### Phase 3: Edge Cases (Day 3)
- [ ] TC-008 through TC-013: All edge cases
- [ ] TC-014, TC-015: Error handling

### Phase 4: Full Regression (Day 4)
- [ ] TC-REG-002, TC-REG-003
- [ ] Existing E2E test suite
- [ ] Exploratory testing

---

## 6. Automation Recommendations

### 6.1 New E2E Tests to Create

```typescript
// Suggested test file: ui/e2e_tests/correctionWizardRecordComparison.spec.e2e.ts

describe('Correction Wizard Record-to-Record Comparison', () => {
  test('TC-001: Billing shows Record difference, not Summary difference', async () => {
    // Setup: Create visit with 2 Records
    // Action: Open correction wizard
    // Verify: Billing adjustment amount = Record₂ - Record₁
  });

  test('TC-007: First Record handles gracefully', async () => {
    // Setup: Create visit with 1 Record only
    // Action: Open correction wizard
    // Verify: Appropriate fallback behavior
  });
});
```

### 6.2 Backend Tests to Add

```csharp
// Add to: VisitCorrectionServiceTests.cs

[Test]
public async Task GetBillingCorrectionAnalysis_ComparesLatestTwoRecords()
{
    // Arrange: Create visit with 2 Records
    // Act: Call GetBillingCorrectionAnalysis
    // Assert: Returns difference between Record₂ and Record₁
}

[Test]
public async Task GetBillingCorrectionAnalysis_WithSingleRecord_HandlesGracefully()
{
    // Arrange: Create visit with 1 Record
    // Act: Call GetBillingCorrectionAnalysis
    // Assert: Returns appropriate fallback
}
```

---

## 7. Risk Assessment for QA

| Risk | Impact | Mitigation |
|------|--------|------------|
| Regression in Treatment Benefits | **High** | Run existing TB test suite first |
| Incorrect calculation logic | **High** | Create specific test data with known expected values |
| Edge cases not covered | **Medium** | Exploratory testing after automated tests pass |
| UI data binding issues | **Medium** | Visual verification during manual testing |
| Performance degradation | **Low** | Monitor API response times |

---

## 8. Definition of Done (QA Perspective)

- [ ] All 15 test cases executed and passed
- [ ] Treatment Benefits regression tests passed
- [ ] Existing E2E test suite passed
- [ ] No critical or high-severity bugs open
- [ ] Edge cases verified manually
- [ ] API response times < 500ms
- [ ] Error handling verified
- [ ] Test automation updated with new scenarios
