# PO Gaps Analysis: Health Record Correction Wizard - Adjustment and Billing Comparison Logic

**Document Version:** 1.0  
**Date:** 2025-12-01  
**Status:** Analysis Complete  
**Priority:** High

---

## 1. Executive Summary

### Gap Identified
The Correction Wizard for **Billing Adjustments and Claim Corrections** is comparing the latest submitted Health Record (Revision) against the **Patient Visit Summary** to calculate adjustment amounts. The correct behavior should compare the latest submitted Health Record with the **previously submitted Health Record** (the one it replaced).

### Impact
- **Incorrect adjustment amounts** are being calculated when there are differences between what was on the original Health Record vs what is on the revision
- **Billing correction amounts** (co-pay, insurance contribution, deductible) are also affected
- Users may adjust/correct more or less than the actual difference between record versions

### Current vs Expected Behavior

| Correction Type | Current Behavior | Expected Behavior | Status |
|-----------------|------------------|-------------------|--------|
| Treatment Benefits | ✅ Compares Record(n) with Record(n-1) | Compare Record(n) with Record(n-1) | **CORRECT** |
| Billing Adjustments (Overpayment) | ❌ Compares Record with Visit Summary | Compare Record(n) with Record(n-1) | **GAP** |
| Claim Corrections | ❌ Compares Record with Visit Summary | Compare Record(n) with Record(n-1) | **GAP** |

---

## 2. Technical Analysis

### 2.1 Current Architecture

#### Frontend Data Flow (Problem Area)
```
CorrectionWizardModal receives:
  └── recordContent: HealthRecord (current record only)
        └── transactions: Transaction[]
              └── filtered for billingAdjustment === true
                    └── calculateAdjustmentData() uses this directly
```

**Key Files:**
- `ui/src/views/PatientOverview/VisitProcess/Visit/healthrecord/correctionWizardComponents/CorrectionWizardModal.tsx`
- `ui/src/views/PatientOverview/VisitProcess/Visit/healthrecord/correctionWizardComponents/utils.ts`
- `ui/src/views/PatientOverview/VisitProcess/Visit/healthrecord/correctionWizardComponents/billingAdjustmentAndRecovery/BillingAdjustmentAndRecoveryCorrection.tsx`

#### Frontend Code - Current Implementation
```typescript
const transactions = recordContent.transactions;

// Filter transactions for billing adjustment
const filteredTransactions = useMemo(() => {
  return transactions.filter(
    (transaction) => transaction.transactionBasic.billingAdjustment === true,
  );
}, [transactions]);
```

**Problem:** This uses `recordContent.transactions` which is the current Health Record data only. There is no comparison with the previous record.

#### Backend Reference Implementation (Treatment Benefits - CORRECT)
```csharp
public async Task<TreatmentBenefitsCorrectionAnalysis> GetTreatmentBenefitsCorrectionAnalysis(long referencedVisitId)
{
    // ✅ CORRECT: Fetches BOTH current and previous records
    var latestRecords =
      await _healthRecordRepository.GetLatestTwoHealthRecordsForTreatmentBenefitCorrection(
        referencedVisitId
      );

    var currentRecord = latestRecords[0];
    var previousRecord = latestRecords[1];

    var currentReport = currentRecord.ToReport();
    var previousReport = previousRecord.ToReport();

    var currentTransactions = currentReport.Transactions ?? [];
    var previousTransactions = previousReport.Transactions ?? [];

    // ✅ CORRECT: Compares transactions between records
    foreach (var code in benefitCodes)
    {
      var currentGroup = currentTreatmentBenefits[code].ToList();
      var previousGroup = previousTreatmentBenefits[code].ToList();
      // ... calculates differences
    }
}
```

### 2.2 Backend API Endpoints

| Endpoint | Purpose | Record Comparison |
|----------|---------|-------------------|
| `GET /visits/{id}/corrections/treatment-benefits` | Treatment benefit analysis | ✅ Compares 2 Records |
| `GET /visits/{id}/corrections/copay` | Co-pay calculation | ❌ Uses visit data |
| `GET /visits/{id}/corrections/insuranceContribution` | Insurance contribution calc | ❌ Uses visit data |
| `GET /visits/{id}/corrections/deductible` | Deductible calc | ❌ Uses visit data |
| **MISSING** | Billing adjustment/overpayment analysis | ❌ **Does not exist** |

### 2.3 Repository Method Analysis

The repository already has the correct method for fetching two records:

```csharp
public async Task<List<HealthRecord>> GetLatestTwoHealthRecordsForTreatmentBenefitCorrection(
  long visitId
) =>
  await CoreDbContext
    .HealthRecords.AsSplitQuery()
    .Include(hr => hr.Visit.VisitLineItems)
      .ThenInclude(li => li.ServiceType)
    // ... other includes
    .Where(hr => hr.VisitId == visitId)
    .OrderByDescending(hr => hr.CreatedAt)
    .Take(2)
    .ToListAsync();
```

This method is only used for treatment benefits but should also be used for billing/claim corrections.

---

## 3. Proposed Solution

### 3.1 Backend Changes

#### 3.1.1 New Service Method
Add to `IVisitCorrectionService.cs`:
```csharp
Task<BillingCorrectionAnalysis> GetBillingCorrectionAnalysis(long referencedVisitId);
```

#### 3.1.2 Implementation in VisitCorrectionService
```csharp
public async Task<BillingCorrectionAnalysis> GetBillingCorrectionAnalysis(long referencedVisitId)
{
    var latestRecords = await _healthRecordRepository
        .GetLatestTwoHealthRecordsForTreatmentBenefitCorrection(referencedVisitId);

    if (latestRecords.Count < 2)
    {
        // Handle case where there's no previous record
        return new BillingCorrectionAnalysis(
            OverpaymentItems: [],
            ClaimItems: [],
            IsFirstCorrection: true
        );
    }

    var currentRecord = latestRecords[0];
    var previousRecord = latestRecords[1];

    var currentReport = currentRecord.ToReport();
    var previousReport = previousRecord.ToReport();

    // Compare billing adjustment transactions between records
    var overpaymentItems = CalculateOverpaymentDifferences(
        currentReport.Transactions,
        previousReport.Transactions
    );

    // Compare claim transactions between records
    var claimItems = CalculateClaimDifferences(
        currentReport.Transactions,
        previousReport.Transactions
    );

    return new BillingCorrectionAnalysis(
        OverpaymentItems: overpaymentItems,
        ClaimItems: claimItems,
        IsFirstCorrection: false
    );
}
```

#### 3.1.3 New API Endpoint
Add to `VisitsController.cs`:
```csharp
[HttpGet("{referencedVisitId}/corrections/billing-analysis")]
[Authorize(Roles = Permissions.VisitsEdit)]
public async Task<ActionResult<BillingCorrectionAnalysisResult>> GetBillingCorrectionAnalysis(
    long referencedVisitId)
{
    var analysis = await _visitCorrectionService.GetBillingCorrectionAnalysis(referencedVisitId);
    var result = BillingCorrectionAnalysisResult.FromBillingCorrectionAnalysis(analysis);
    return Ok(result);
}
```

### 3.2 Frontend Changes

#### 3.2.1 New Query Hook
Add to `correctionWizard.ts`:
```typescript
export const useBillingCorrectionAnalysis = (visitId: string) => {
  const get = useFetchWithAuthDeserialize();

  return useQuery({
    queryKey: ["billingCorrectionAnalysis", visitId],
    queryFn: () =>
      get(
        billingCorrectionAnalysisResultSchema,
        `/visits/${visitId}/corrections/billing-analysis`,
      ),
  });
};
```

#### 3.2.2 Update CorrectionWizardModal
Replace direct transaction filtering with API call:
```typescript
// Remove:
const filteredTransactions = useMemo(() => {
  return transactions.filter(
    (transaction) => transaction.transactionBasic.billingAdjustment === true,
  );
}, [transactions]);

// Add:
const { data: billingAnalysis } = useBillingCorrectionAnalysis(visitId);
const overpaymentData = billingAnalysis?.overpaymentItems ?? [];
```

### 3.3 Data Types

#### New Backend Types
```csharp
public record BillingCorrectionAnalysis(
    IReadOnlyList<OverpaymentItem> OverpaymentItems,
    IReadOnlyList<ClaimItem> ClaimItems,
    bool IsFirstCorrection
);

public record OverpaymentItem(
    int ServiceTypeCode,
    string ServiceTypeName,
    decimal AmountOnPreviousRecord,
    decimal AmountOnCurrentRecord,
    decimal ToCorrect
);

public record ClaimItem(
    int ServiceTypeCode,
    string ServiceTypeName,
    decimal AmountOnPreviousRecord,
    decimal AmountOnCurrentRecord,
    decimal ToCorrect
);
```

---

## 4. Files Requiring Changes

### Backend (C#)
| File | Change Type | Description |
|------|-------------|-------------|
| `Application/Visits/IVisitCorrectionService.cs` | Add | New method signature |
| `Application/Visits/VisitCorrectionService.cs` | Add | Implementation of billing analysis |
| `Application/Visits/BillingCorrectionAnalysis.cs` | Create | New domain types |
| `Web.Api/Controllers/Visits/VisitsController.cs` | Add | New endpoint + response types |

### Frontend (TypeScript/React)
| File | Change Type | Description |
|------|-------------|-------------|
| `ui/src/queries/correctionWizard.ts` | Add | New query hook + types |
| `ui/src/views/.../CorrectionWizardModal.tsx` | Modify | Use new API instead of direct filtering |
| `ui/src/views/.../BillingAdjustmentAndRecoveryCorrection.tsx` | Modify | Accept data from API |
| `ui/src/views/.../correctionWizardComponents/utils.ts` | Modify | Update calculateAdjustmentData logic |

---

## 5. Risk Assessment

### 5.1 Risk Matrix

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Breaking existing corrections | Medium | High | Comprehensive regression testing |
| Performance impact from additional API call | Low | Medium | Lazy loading, caching |
| Data migration for existing corrections | N/A | N/A | No migration needed - only affects new corrections |
| Edge case: First record (no previous) | High | Medium | Handle gracefully in backend |
| HIPAA compliance impact | Low | High | Ensure audit logging is maintained |

### 5.2 Edge Cases to Handle

1. **First Record Submission**: No previous record exists to compare with
   - Solution: Return empty differences, or use visit summary as baseline

2. **Multiple Record versions**: More than 2 record versions exist
   - Solution: Always compare latest vs second-latest (already handled by `Take(2)`)

3. **Cancelled Record**: Previous record was cancelled/voided
   - Solution: Skip cancelled records in the comparison

4. **Partial corrections**: Some items corrected in previous wizard run
   - Solution: Compare with the record that was submitted after the last correction

5. **Insurance claim already processed**: External payer has already processed claim
   - Solution: Flag for manual review, prevent auto-correction

---

## 6. Testing Requirements

### Unit Tests
- [ ] BillingCorrectionAnalysis calculation logic
- [ ] Overpayment difference calculation
- [ ] Claim difference calculation
- [ ] Edge case: no previous record
- [ ] Edge case: identical records

### Integration Tests
- [ ] API endpoint returns correct data
- [ ] Frontend correctly displays record-to-record differences
- [ ] Correction wizard submits correct amounts

### E2E Tests
- [ ] Complete correction wizard flow with record comparison
- [ ] Verify corrected amounts match record differences
- [ ] Audit trail verification for compliance

---

## 7. Implementation Estimate

| Phase | Effort | Description |
|-------|--------|-------------|
| Backend service + types | 4-6 hours | New method + types |
| API endpoint | 2-3 hours | Controller + response mapping |
| Frontend integration | 4-6 hours | Query hook + component updates |
| Unit tests | 4-6 hours | Comprehensive test coverage |
| E2E tests | 3-4 hours | End-to-end verification |
| Compliance review | 2-3 hours | HIPAA/audit trail verification |
| **Total** | **19-28 hours** | **~3-4 days** |

---

## 8. Business Gaps Requiring PO Clarification

### 8.1 Missing Requirements

| # | Question | Impact | Priority |
|---|----------|--------|----------|
| 1 | What should happen when no previous record exists? | Determines baseline calculation | High |
| 2 | Should cancelled/voided records be included in comparison? | Affects accuracy | High |
| 3 | How should partial corrections be handled across multiple sessions? | User experience | Medium |
| 4 | Should there be a maximum correction threshold requiring approval? | Compliance | Medium |
| 5 | How to handle corrections for claims already submitted to payers? | Integration | High |

### 8.2 Edge Cases Requiring Clarification

1. **Patient with multiple visits on same day** - Which visit should corrections apply to?
2. **Insurance change mid-treatment** - Which insurance rates to use for correction calculation?
3. **Retroactive rate changes** - How to handle corrections when service rates have changed?

---

## 9. Acceptance Criteria (Definition of Done)

### Functional Requirements
- [ ] Correction wizard compares latest record with previous record (not visit summary)
- [ ] Treatment benefits continue to work correctly (no regression)
- [ ] Billing adjustments show correct difference amounts
- [ ] Claim corrections show correct difference amounts
- [ ] First-time corrections handled gracefully (no previous record scenario)

### Non-Functional Requirements
- [ ] API response time < 500ms for correction analysis
- [ ] All corrections logged in audit trail
- [ ] Unit test coverage > 80% for new code
- [ ] E2E tests pass for all correction scenarios

### Compliance Requirements
- [ ] PHI access properly logged
- [ ] User authorization verified for correction operations
- [ ] Audit trail captures before/after values

---

## 10. Appendix: Architecture Diagrams

### Current Flow (Incorrect)
```
┌─────────────────┐     ┌──────────────────┐
│  Visit Summary  │────▶│ Current Record   │
└─────────────────┘     └──────────────────┘
         │                       │
         └───────┬───────────────┘
                 ▼
        ┌────────────────┐
        │  COMPARE HERE  │  ❌ Wrong comparison
        └────────────────┘
```

### Expected Flow (Correct)
```
┌──────────────────┐     ┌──────────────────┐
│ Previous Record  │────▶│ Current Record   │
│    (n-1)         │     │    (n)           │
└──────────────────┘     └──────────────────┘
         │                       │
         └───────┬───────────────┘
                 ▼
        ┌────────────────┐
        │  COMPARE HERE  │  ✅ Correct comparison
        └────────────────┘
```
