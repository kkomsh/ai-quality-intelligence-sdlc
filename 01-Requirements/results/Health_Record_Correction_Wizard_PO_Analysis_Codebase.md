# PO Gaps Analysis: Patient Record Correction Wizard - Treatment and Billing Comparison Logic

**Document Version:** 1.0  
**Date:** 2025-12-01  
**Status:** Analysis Complete (Codebase-Enhanced)  
**Priority:** High

---

## Task Definition

> At present billing adjustment and correction line items in the wizard are calculated based on latest submitted Patient Records' difference compared to the Visit Summary. It does not consider multiple revisions in a row with additional billing adjustments or treatment corrections.
>
> **How it should be done:**
> - Compare latest submitted Patient Record (Revision) with the previously submitted Patient Record (the one it replaced)
> - Compare the difference and find the billing adjustments and treatment corrections difference amount
> - Show the user information based on difference and do the correction for it
>
> **Scope:** Fix required for Billing Adjustments and Co-pay Corrections. Treatment Benefit Corrections are already working this way.

---

## 1. Business Gaps

### 1.1 Missing Requirements Needing PO Clarification

| # | Question | Code Context | Impact | Priority |
|---|----------|--------------|--------|----------|
| 1 | **What should happen when there's only one Patient Record (no previous)?** | `GetLatestTwoPatientRecordsForTreatmentBenefitCorrection` returns list - what if `Count < 2`? | Determines fallback: use Visit Summary baseline or block corrections? | **High** |
| 2 | **Should voided records be included in comparison chain?** | Current query uses `OrderByDescending(record.CreatedAt).Take(2)` without status filter | May compare against voided record incorrectly | **High** |
| 3 | **How to handle corrections already partially applied?** | `calculateBillingData` compares `visitLineItems` vs `transactions` - no tracking of applied corrections | Prevent double-corrections or cumulative approach? | **High** |
| 4 | **What if record comparison shows zero difference?** | Current UI shows `isShowNoNeedToCorrectView` when amounts match | Should match Treatment Benefits behavior? | **Medium** |
| 5 | **Should users see both record values or just the difference?** | Current UI shows `amountOnVisit` vs `amountOnRecord` | UX change needed for `amountOnPreviousRecord` vs `amountOnCurrentRecord`? | **Medium** |
| 6 | **How to handle negative differences (overpayment correction)?** | Current logic: `insuranceTransaction.amount - visitLineItem.amount` | Allow negative corrections or flag for review? | **Medium** |
| 7 | **Should `GetLatestTwoPatientRecordsForTreatmentBenefitCorrection` be renamed/generalized?** | Method name implies Treatment Benefits only | Create new method or rename existing? | **Low** |

### 1.2 Edge Cases Identified from Codebase

| Scenario | Current Code Behavior | Expected Behavior (Needs Clarification) |
|----------|----------------------|----------------------------------------|
| **First Record (no previous)** | `latestRecords[1]` would fail with index out of range | Handle gracefully - use Visit Summary as baseline? |
| **Voided Record in chain** | Query doesn't filter by status | Skip voided, compare with last submitted? |
| **Service removed in revision** | UI filters `billingAdjustment === true` | Should show as "previously billed, now removed"? |
| **New service added** | UI uses current record transactions only | Show as new item to correct with 0 on previous? |
| **Service code 413/414 (co-pay/deductible)** | Hardcoded in `findTransactionByServiceType(transactions, 413)` | Need to compare between records, not record vs visit |
| **Multiple same-day revisions** | Ordered by `CreatedAt` descending | Timestamp precision sufficient? |

---

## 2. Impact Analysis

### 2.1 Impacted Components (from Codebase Analysis)

#### Backend Components

| File | Current State | Required Changes | Impact |
|------|---------------|------------------|--------|
| `IVisitCorrectionService.cs` | Has `GetTreatmentBenefitsCorrectionAnalysis` | Add `GetBillingCorrectionAnalysis` method | Medium |
| `VisitCorrectionService.cs` (2036 lines) | Lines 146-320: TB uses record-vs-record comparison | Implement same pattern for Billing/Co-pay | High |
| `IPatientRecordRepository.cs` | `GetLatestTwoPatientRecordsForTreatmentBenefitCorrection` | Rename or create generic method | Low |
| `PatientRecordRepository.cs` | Lines 107-127: Query fetches 2 records | May need status filter for voided records | Medium |
| `VisitsController.cs` | Line 760: `/corrections/treatment-benefits` endpoint | Add `/corrections/billing-analysis` endpoint | Medium |

#### Frontend Components

| File | Current State | Required Changes | Impact |
|------|---------------|------------------|--------|
| `CorrectionWizardModal.tsx` (445 lines) | Lines 88-92: Filters `transactions` for `billingAdjustment === true` | Replace with API call to new endpoint | **High** |
| `utils.ts` (865 lines) | Lines 622-700: `calculateBillingData` compares record vs visit | Update to accept record-vs-record data | **High** |
| `correctionWizard.ts` (485 lines) | Line 473: `useTreatmentBenefitsCorrectionAnalysis` pattern exists | Add `useBillingCorrectionAnalysis` hook | Medium |
| `BillingAdjustmentAndRecoveryCorrection.tsx` | Uses `recordContent.transactions` directly | Accept data from new API | **High** |
| `useBillingFormLogic.ts` (310 lines) | Uses `billingAdjustmentData` prop | Update prop source from API | Medium |

### 2.2 Data Flow Comparison

#### Current Flow (INCORRECT for Billing/Co-pay)
```
CorrectionWizardModal
  └── recordContent.transactions (current record only)
        └── filter(billingAdjustment === true)
              └── calculateBillingData(visitLineItems, transactions)
                    └── Compare record amount vs visit amount ❌
```

#### Reference Flow (CORRECT - Treatment Benefits)
```
CorrectionWizardModal
  └── useTreatmentBenefitsCorrectionAnalysis(visitId)
        └── GET /visits/{id}/corrections/treatment-benefits
              └── GetLatestTwoPatientRecordsForTreatmentBenefitCorrection
                    └── Compare currentRecord vs previousRecord ✅
```

#### Required Flow (TO IMPLEMENT)
```
CorrectionWizardModal
  └── useBillingCorrectionAnalysis(visitId)  [NEW]
        └── GET /visits/{id}/corrections/billing-analysis  [NEW]
              └── GetLatestTwoPatientRecords (reuse/rename)
                    └── Compare currentRecord vs previousRecord ✅
```

### 2.3 Regression Testing Areas

| Area | Files Affected | Test Focus | Priority |
|------|----------------|------------|----------|
| **Treatment Benefits Corrections** | `VisitCorrectionService.cs` | Ensure no regression from shared code | **Critical** |
| **Billing Wizard Step** | `BillingAdjustmentAndRecoveryCorrection.tsx` | New data source works correctly | **Critical** |
| **Co-pay Wizard Step** | `CopayCorrection.tsx`, `utils.ts` | New calculation logic | **Critical** |
| **Deductible Calculation** | `useDeductibleCorrectionAmounts` | May need record-based input | High |
| **Correction Submission** | `useAddCorrections`, `AddCorrections` | Correct amounts submitted | **Critical** |
| **Existing Unit Tests** | `VisitCorrectionServiceTests.cs` (3075 lines) | Update/add tests for new logic | High |

---

## 3. Development Tasks

### 3.1 Backend Tasks

| Task | Component | Effort | Dependencies | Specific Files |
|------|-----------|--------|--------------|----------------|
| B1. Add `GetBillingCorrectionAnalysis` to interface | Service Interface | 0.5h | None | `IVisitCorrectionService.cs` |
| B2. Implement billing analysis in service | Service Layer | 4h | B1 | `VisitCorrectionService.cs` (follow lines 146-320 pattern) |
| B3. Implement co-pay comparison logic | Service Layer | 3h | B2 | `VisitCorrectionService.cs` |
| B4. Create response DTOs | Models | 1h | B2-B3 | New file: `BillingCorrectionAnalysis.cs` |
| B5. Add edge case handling | Service Layer | 2h | B2-B3 | `VisitCorrectionService.cs` |
| B6. Add API endpoint | Controller | 1.5h | B1-B5 | `VisitsController.cs` (follow line 760 pattern) |
| B7. Consider renaming repository method | Repository | 0.5h | None | `IPatientRecordRepository.cs`, `PatientRecordRepository.cs` |
| B8. Add status filter to record query | Repository | 1h | B7 | `PatientRecordRepository.cs` (line 107-127) |
| B9. Unit tests for service | Tests | 4h | B2-B5 | `VisitCorrectionServiceTests.cs` |

**Backend Subtotal:** 17.5 hours

### 3.2 Frontend Tasks

| Task | Component | Effort | Dependencies | Specific Files |
|------|-----------|--------|--------------|----------------|
| F1. Add Zod schema for response | Types | 0.5h | B4 | `correctionWizard.ts` |
| F2. Add `useBillingCorrectionAnalysis` hook | Query | 1h | F1, B6 | `correctionWizard.ts` (follow line 473 pattern) |
| F3. Update CorrectionWizardModal | Component | 3h | F2 | `CorrectionWizardModal.tsx` (replace lines 88-130) |
| F4. Update BillingAdjustmentAndRecoveryCorrection | Component | 2h | F3 | `BillingAdjustmentAndRecoveryCorrection.tsx` |
| F5. Update calculateBillingData utility | Utils | 2h | F3 | `utils.ts` (lines 622-700) |
| F6. Update useBillingFormLogic | Hook | 1.5h | F4 | `useBillingFormLogic.ts` |
| F7. Add loading/error states | Component | 1h | F3 | `CorrectionWizardModal.tsx` |
| F8. Update CopayCorrection component | Component | 1.5h | F5 | `CopayCorrection.tsx` |
| F9. Unit tests for components | Tests | 2h | F3-F8 | Test files |
| F10. Unit tests for utilities | Tests | 1h | F5 | Test files |

**Frontend Subtotal:** 15.5 hours

### 3.3 Integration & QA Tasks

| Task | Component | Effort | Dependencies | Notes |
|------|-----------|--------|--------------|-------|
| I1. Integration testing | Testing | 3h | B*, F* | API + Frontend integration |
| I2. E2E test - billing flow | E2E | 2h | I1 | Use existing E2E patterns |
| I3. E2E test - co-pay flow | E2E | 2h | I1 | Co-pay, deductible calculations |
| I4. E2E test - edge cases | E2E | 2h | I1 | First record, voided record |
| I5. Regression test - treatment benefits | E2E | 1.5h | I1 | **Critical - no regression** |
| I6. Manual QA testing | QA | 4h | I1-I5 | Exploratory testing |
| I7. Bug fixes buffer | Dev | 4h | I6 | Address QA findings |

**Integration & QA Subtotal:** 18.5 hours

### 3.4 Task Summary

| Phase | Effort | Key Deliverables |
|-------|--------|------------------|
| Backend Development | 17.5h | New service method, API endpoint, DTOs |
| Frontend Development | 15.5h | New hook, updated components |
| Integration & QA | 18.5h | E2E tests, regression verification |
| **Total Estimate** | **51.5 hours** | |
| **Calendar Days** | **~7 days** | (1 developer) |
| **With Buffer (+20%)** | **~62 hours / 8 days** | |

---

## 4. Risks & Acceptance Criteria

### 4.1 Key Risks with Mitigations

| Risk | Probability | Impact | Mitigation Strategy | Code Reference |
|------|-------------|--------|---------------------|----------------|
| **Regression in Treatment Benefits** | Low | **High** | No changes to `GetTreatmentBenefitsCorrectionAnalysis`; comprehensive regression tests | Lines 146-320 in `VisitCorrectionService.cs` |
| **Index out of range (< 2 records)** | Medium | High | Add null check before accessing `latestRecords[1]` | Line 154: `var previousRecord = latestRecords[1];` |
| **Incorrect calculation logic** | Medium | **High** | Follow exact pattern from TB implementation; thorough unit tests | Copy pattern from lines 160-210 |
| **Breaking change to existing corrections** | Low | High | New logic only affects new wizard sessions | No migration needed |
| **Performance degradation** | Low | Medium | Query already optimized with `Take(2)` and `AsSplitQuery()` | Lines 107-127 in repository |
| **Voided record in comparison** | Medium | Medium | Add status filter to repository query | Modify `Where` clause |
| **UI confusion (labels change)** | Medium | Low | Clear labeling: "Previous Record" vs "Current Record" | Update UI text |
| **HIPAA compliance impact** | Low | **High** | Ensure audit logging maintained for all PHI access | Verify audit trail |

### 4.2 Definition of Done Checklist

#### Functional Requirements
- [ ] Billing corrections compare latest record with previous record (not Visit Summary)
- [ ] Co-pay corrections (service types 413, 414) compare record-to-record
- [ ] Deductible calculations use record difference amounts
- [ ] Treatment Benefits corrections unchanged (no regression)
- [ ] First-time corrections (no previous record) handled gracefully
- [ ] Voided records excluded from comparison
- [ ] UI displays correct difference amounts
- [ ] Submitted correction amounts match calculated differences

#### Technical Requirements
- [ ] New API endpoint: `GET /visits/{id}/corrections/billing-analysis`
- [ ] New service method: `GetBillingCorrectionAnalysis`
- [ ] New frontend hook: `useBillingCorrectionAnalysis`
- [ ] Backend unit test coverage ≥ 80% for new code
- [ ] Frontend unit test coverage ≥ 80% for new code
- [ ] All E2E tests passing
- [ ] No console errors or TypeScript warnings

#### Compliance Requirements
- [ ] PHI access properly logged in audit trail
- [ ] User authorization verified for correction operations
- [ ] Before/after values captured for compliance

#### Quality Requirements
- [ ] Code reviewed and approved
- [ ] No critical or high-severity bugs
- [ ] Regression tests passing (especially Treatment Benefits)
- [ ] Manual QA sign-off
- [ ] Performance: API response < 500ms

---

## 5. Code References

### 5.1 Reference Implementation (Treatment Benefits - CORRECT)

**Backend Service Method:**
```csharp
// VisitCorrectionService.cs - Lines 146-167
public async Task<TreatmentBenefitsCorrectionAnalysis> GetTreatmentBenefitsCorrectionAnalysis(long referencedVisitId)
{
    var latestRecords = await _patientRecordRepository
        .GetLatestTwoPatientRecordsForTreatmentBenefitCorrection(referencedVisitId);

    var currentRecord = latestRecords[0];
    var previousRecord = latestRecords[1];  // ⚠️ Needs null check
    
    var currentReport = currentRecord.ToReport();
    var previousReport = previousRecord.ToReport();

    var currentTransactions = currentReport.Transactions ?? [];
    var previousTransactions = previousReport.Transactions ?? [];
    // ... comparison logic
}
```

**Frontend Query Hook:**
```typescript
// correctionWizard.ts - Lines 473-483
export const useTreatmentBenefitsCorrectionAnalysis = (visitId: string) => {
  const get = useFetchWithAuthDeserialize();
  return useQuery({
    queryKey: ["treatmentBenefitsCorrectionAnalysis", visitId],
    queryFn: () => get(
      treatmentBenefitsCorrectionAnalysisResultSchema,
      `/visits/${visitId}/corrections/treatment-benefits`,
    ),
  });
};
```

### 5.2 Current Implementation (Billing/Co-pay - INCORRECT)

**Frontend - Problem Code:**
```typescript
// CorrectionWizardModal.tsx - Lines 88-92
const filteredTransactions = useMemo(() => {
  return transactions.filter(
    (transaction) => transaction.transactionBasic.billingAdjustment === true,
  );
}, [transactions]);  // ❌ Uses current record only, no comparison
```

```typescript
// utils.ts - Lines 640-647
const copayAmountOnVisit =
  copayTransaction?.transactionBasic.amount ?? 0;
const copayAmountOnRecord = visitLineItem?.amount ?? 0;
const copayAmountToCorrect = calculateContributionAmountToCorrect(
  copayAmountOnVisit,
  copayAmountOnRecord,
);  // ❌ Compares record vs visit, not record vs previous record
```

---

## 6. Appendix: File Locations

| Purpose | File Path | Lines of Interest |
|---------|-----------|-------------------|
| Service Interface | `backend/Application/Visits/IVisitCorrectionService.cs` | Full file (19 lines) |
| Service Implementation | `backend/Application/Visits/VisitCorrectionService.cs` | 146-320 (TB reference) |
| Repository Interface | `backend/Application/PatientRecords/IPatientRecordRepository.cs` | Line 25 |
| Repository Implementation | `backend/Persistence/PatientRecords/PatientRecordRepository.cs` | 107-127 |
| Controller | `backend/Web.Api/Controllers/Visits/VisitsController.cs` | 760-770 |
| Frontend Queries | `ui/src/queries/correctionWizard.ts` | 473-483 |
| Wizard Modal | `ui/src/views/.../CorrectionWizardModal.tsx` | 88-130 |
| Calculation Utils | `ui/src/views/.../utils.ts` | 622-700 |
| Unit Tests | `backend/Tests/Application/VisitCorrectionServiceTests.cs` | 3075 lines total |
