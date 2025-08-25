# Capstone-Project---MediCare-Claims-Hub

# Phase 1: Advanced SQL Analysis – Healthcare Claims

# Project Overveiw

This capstone simulates analytics at a healthcare claims organization in the U.S., using multi-table claims data (Beneficiary, Inpatient, Outpatient, Provider) to answer business questions and build fraud-focused ML models.
Phase 1 focuses on Advanced SQL to quantify reimbursements, profile providers, study demographics, and explore chronic-condition patterns.



# Questions

Q1. Retrieve the total amount reimbursed for inpatient claims (InscClaimAmtReimbursed), grouped by provider.

Total reimbursed for inpatient claims, grouped by provider

```sql

SELECT provider, 
SUM(InscClaimAmtReimbursed) AS total_amount 
FROM inpatientdata
GROUP BY provider;

```

Q2. Identify the top 5 providers with the highest number of outpatient claims.

Top 5 providers by count of outpatient claims

```sql

SELECT provider, 
COUNT(ClaimID) as claim_count 
FROM outpatientdata
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;

```

Q3. Find the total number of beneficiaries with claims indicating chronic conditions such as diabetes (ChronicCond_Diabetes = 1)

Total beneficiaries with diabetes-related claims (ChronicCond_Diabetes = 1)

```sql

SELECT COUNT(BeneID) as Total_Claims_Diabetes 
FROM beneficiarydata
WHERE ChronicCond_Diabetes = 1;

```

Q4. Calculate the average inpatient claim amount reimbursed by gender.

Average inpatient claim reimbursement by gender

```sql

SELECT Gender, 
AVG(ip.InscClaimAmtReimbursed) as Avg_Claim_Amount
FROM inpatientdata as ip
JOIN beneficiarydata as bf
WHERE ip.BeneID = bf.BeneID
GROUP BY Gender;

```

Q5. Retrieve all claims (inpatient & outpatient) for a given BeneID, to enable individual beneficiary case history reviews.

Retrieve full claim history (inpatient & outpatient) for a given BeneID

```sql

SELECT BeneID, ClaimID, ClaimStartDt, ClaimEndDt, Provider, InscClaimAmtReimbursed
FROM inpatientdata
WHERE BeneID = 'BENE11725'
UNION
SELECT BeneID, ClaimID, ClaimStartDt, ClaimEndDt, Provider, InscClaimAmtReimbursed
FROM outpatientdata
WHERE BeneID = 'BENE11725';

```

Q6. Identify providers with claims where the admission date is in 2009 and the reimbursed amount exceeds $10,000.

Providers with admissions in 2009 AND reimbursed amount > $10,000

```sql

SELECT Provider, AdmissionDt, InscClaimAmtReimbursed 
FROM inpatientdata
WHERE YEAR(AdmissionDt) = 2009
AND InscClaimAmtReimbursed > 10000;

```

Q7. Combine beneficiary demographics with inpatient claims to calculate the average deductible amount (IPAnnualDeductibleAmt) for beneficiaries aged 65 and above.

Average inpatient deductible (IPAnnualDeductibleAmt) for beneficiaries aged 65+

```sql

SELECT AVG(DeductibleAmtPaid) AS Avg_deductible 
FROM inpatientdata AS ip
JOIN beneficiarydata AS bf
ON ip.BeneID = bf.BeneID
WHERE TIMESTAMPDIFF(YEAR, bf.DOB, CURDATE()) >= 65;

```

Q8. List all claims involving more than one physician (i.e., where AttendingPhysician, OperatingPhysician, or OtherPhysician are non-null simultaneously).

Claims involving more than one physician simultaneously

(any combination where AttendingPhysician, OperatingPhysician, OtherPhysician are non-null at the same time)


```sql

SELECT BeneID, ClaimID, ClaimStartDt, ClaimEndDt, Provider, InscClaimAmtReimbursed, AttendingPhysician, OperatingPhysician, OtherPhysician 
FROM inpatientdata
WHERE (
(AttendingPhysician IS NOT NULL AND AttendingPhysician <> '')
+(OperatingPhysician IS NOT NULL AND OperatingPhysician <> '')
+(OtherPhysician IS NOT NULL AND OtherPhysician <> '')) >= 2

UNION

SELECT BeneID, ClaimID, ClaimStartDt, ClaimEndDt, Provider, InscClaimAmtReimbursed, AttendingPhysician, OperatingPhysician, OtherPhysician 
FROM outpatientdata
WHERE (
(AttendingPhysician IS NOT NULL AND AttendingPhysician <> '')
+(OperatingPhysician IS NOT NULL AND OperatingPhysician <> '')
+(OtherPhysician IS NOT NULL AND OtherPhysician <> '')) >= 2;

```
