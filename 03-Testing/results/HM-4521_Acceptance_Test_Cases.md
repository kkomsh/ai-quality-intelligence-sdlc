# Acceptance Test Cases: HM-4521 - Fix Import Patient Record Using Medical Code

**Branch:** HM-4521-fix-import-patient-record-using-medical-code  
**Ticket:** HM-4521  
**Test Plan Created:** 2025-11-19  
**Feature:** Patient Record Import Medical Code Validation & Chronic Condition Processing

---

## Test Scope

This test plan covers:
1. Medical code validation logic fixes in patient record import
2. ProcessChronicCondition functionality for automatic entry creation
3. Preventive Care medical code + 0 diagnosis code special case handling
4. Health Information Exchange (HIE) patient record import with various medical code scenarios


---

## Test Environment Setup

### Prerequisites
- [ ] Healthcare facility with active patient database
- [ ] Medical departments configured with standard departments:
  - 2000: Internal Medicine
  - 1000: Emergency Department
  - Allergy management accounts configured in system settings
  - Chronic condition tracking accounts configured in system settings
- [ ] Diagnosis codes configured: ICD-10 codes (E11.9, Z00.00, I10, etc.)
- [ ] Medical codes configured: ACUTE (Acute condition), CHR001 (Chronic condition), PREV001 (Preventive care), EMERG (Emergency)
- [ ] Health Information Exchange (HIE) API enabled
- [ ] Test user with permissions for patient record import via API

### Test Data
```xml
<!-- Base patient record template -->
<Root>
  <PatientRecord>
    <RecordType>diagnosis</RecordType>
    <RecordDate format="ansi">2025-11-19</RecordDate>
    <RecordClass>Outpatient</RecordClass>
    <Description>Test patient record HM-4521</Description>
    <PatientRecordEntry>
      <RecordSum type="diagnosis">{{AMOUNT}}</RecordSum>
      <Description>Test entry</Description>
      <DepartmentNumber>{{DEPARTMENT}}</DepartmentNumber>
      <DiagnosisCode medicalcode="{{MEDICALCODE}}">{{DIAGNOSISCODE}}</DiagnosisCode>
    </PatientRecordEntry>
  </PatientRecord>
</Root>
```

---

## Test Cases

### TC-HM4521-001: Chronic Condition Code Auto-Creates Additional Entry

**Priority:** Critical  
**Type:** Functional  

**Objective:** Verify ProcessChronicCondition creates automatic entry for chronic conditions

**Steps:**
1. Prepare patient record XML with:
   - DepartmentNumber: 2000
   - RecordSum: 1
   - DiagnosisCode: ICD-10 E11.9 (Type 2 diabetes)
   - medicalcode: "CHR001" (Chronic condition)
2. Import patient record via HIE API
3. Retrieve created patient record via API or UI
4. Verify patient record entry count
5. Check entry details

**Expected Result:**
- Patient record created successfully with RecordID returned
- Patient record contains **2 entries:**
  - **Entry 1:** Department 2000, Diagnosis E11.9, Medical Code CHR001 (primary diagnosis)
  - **Entry 2:** Department 2000, Diagnosis Code 0, Medical Code "0" (NoSpecialHandling) - auto-created for chronic condition tracking
- Patient record is properly linked to patient
- Chronic condition tracking entry created correctly

**Actual Result:** _______________  
**Status:** [ ] Pass [ ] Fail [ ] Blocked  
**Notes:** _______________

---

### TC-HM4521-002: Preventive Care Medical Code with 0 Diagnosis Code - Success Case

**Priority:** Critical  
**Type:** Functional  

**Objective:** Verify Preventive Care medical code validation allows 0 diagnosis code (bug fix)

**Steps:**
1. Prepare patient record XML with:
   - DepartmentNumber: 1000
   - RecordSum: 1
   - DiagnosisCode: 0
   - medicalcode: "PREV001" (Preventive care)
2. Import patient record via HIE API
3. Retrieve created patient record

**Expected Result:**
- Patient record import succeeds (HTTP 200 OK)
- Patient record created with 1 entry:
  - Department 1000, Diagnosis Code 0, Medical Code PREV001
- No validation error thrown
- No additional entries auto-created

**Actual Result:** _______________  
**Status:** [ ] Pass [ ] Fail [ ] Blocked  
**Notes:** _______________

---

### TC-HM4521-003: Preventive Care Medical Code with Non-Zero Diagnosis Code - Validation Error

**Priority:** Critical  
**Type:** Negative Test  

**Objective:** Verify Preventive Care medical code validation rejects non-zero diagnosis code

**Steps:**
1. Prepare patient record XML with:
   - DepartmentNumber: 1000
   - RecordSum: 1
   - DiagnosisCode: ICD-10 Z00.00 (General health examination)
   - medicalcode: "PREV001" (Preventive care)
2. Attempt to import patient record via HIE API
3. Capture error response

**Expected Result:**
- Patient record import fails with validation error
- Error message contains: "Medical code PREV001 is not compatible with diagnosis code Z00.00" (or localized equivalent)
- HTTP status: 400 Bad Request or similar
- No patient record created in database

**Actual Result:** _______________  
**Status:** [ ] Pass [ ] Fail [ ] Blocked  
**Notes:** _______________

---

### TC-HM4521-004: Acute Condition Medical Code - Baseline Test

**Priority:** High  
**Type:** Regression  

**Objective:** Verify existing acute condition patient record import still works correctly

**Steps:**
1. Prepare patient record XML with:
   - DepartmentNumber: 2000
   - RecordSum: 1
   - DiagnosisCode: ICD-10 I10 (Essential hypertension)
   - medicalcode: "ACUTE" (Acute condition)
2. Import patient record via HIE API
3. Retrieve created patient record

**Expected Result:**
- Patient record import succeeds
- Patient record created with 1 entry (no additional entries):
  - Department 2000, Diagnosis I10, Medical Code ACUTE
- Chronic condition tracking entry not created
- Patient record properly linked

**Actual Result:** _______________  
**Status:** [ ] Pass [ ] Fail [ ] Blocked  
**Notes:** _______________

---

### TC-HM4521-005: Chronic Condition with Zero Diagnosis Code

**Priority:** Medium  
**Type:** Edge Case  

**Objective:** Verify chronic condition code with 0 diagnosis code does not create additional entry

**Steps:**
1. Prepare patient record XML with:
   - DepartmentNumber: 2000
   - RecordSum: 1
   - DiagnosisCode: 0
   - medicalcode: "CHR001" (Chronic condition)
2. Import patient record via HIE API
3. Retrieve created patient record

**Expected Result:**
- Patient record import succeeds
- Patient record created with 1 entry only (no additional entry because diagnosisCode = 0):
  - Department 2000, Diagnosis Code 0, Medical Code CHR001
- No chronic condition tracking entry created

**Actual Result:** _______________  
**Status:** [ ] Pass [ ] Fail [ ] Blocked  
**Notes:** _______________

---

### TC-HM4521-006: Multiple Entries with Mixed Medical Codes

**Priority:** High  
**Type:** Complex Scenario  

**Objective:** Verify patient record with multiple entries and different medical codes processes correctly

**Steps:**
1. Prepare patient record XML with 3 entries:
   - Entry 1: Department 2000, Diagnosis I10, medicalcode "ACUTE" (Acute condition)
   - Entry 2: Department 2000, Diagnosis E11.9, medicalcode "CHR001" (Chronic condition)
   - Entry 3: Department 1000, Diagnosis Code 0, medicalcode "PREV001" (Preventive care)
2. Import patient record via HIE API
3. Retrieve created patient record and analyze entries

**Expected Result:**
- Patient record import succeeds
- Patient record created with **4 entries:**
  - Entry 1: Department 2000, Diagnosis I10, Medical Code ACUTE
  - Entry 2: Department 2000, Diagnosis E11.9, Medical Code CHR001
  - Entry 3: Department 2000, Diagnosis Code 0, Medical Code 0 (auto-created for chronic condition)
  - Entry 4: Department 1000, Diagnosis Code 0, Medical Code PREV001
- All entries properly linked to patient
- Patient record balanced

**Actual Result:** _______________  
**Status:** [ ] Pass [ ] Fail [ ] Blocked  
**Notes:** _______________

---

## Acceptance Criteria Checklist

| Criterion | Status | Notes |
|-----------|--------|-------|
| All critical test cases pass | [ ] | TC-001, TC-002, TC-003 |
| No debug statements in production code | [X] | Removed in commit f8e36638 |
| Unit test coverage ≥ 80% for new code | [ ] | ProcessChronicCondition |
| Integration tests pass | [ ] | ITC-001 minimum |
| Performance impact < 10% | [ ] | PTC-001 |
| Backward compatibility maintained | [ ] | RTC-001 |
| Database migration scripts validated | [ ] | HM-48904.sql, HM-48672.sql |
| Documentation updated | [ ] | API docs, release notes |

---

## Test Execution Summary

**Date:** _______________  
**Tester:** _______________  
**Environment:** [ ] Dev [ ] Test [ ] Staging [ ] Production  

**Results:**
- Total Test Cases: 6 functional + 2 integration + 1 performance + 1 regression suite
- Passed: _____ / _____
- Failed: _____ / _____
- Blocked: _____ / _____

**Critical Issues Found:** _______________

**Sign-off:**
- QA Lead: _______________ Date: _______________
- Dev Lead: _______________ Date: _______________
- Clinical Lead: _______________ Date: _______________

---

**Document Version:** 1.0  
**Last Updated:** 2025-11-19  
**Next Review:** After deployment to production

