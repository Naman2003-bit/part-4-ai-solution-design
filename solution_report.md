# Part 4: AI Solution Design — Patient Readmission Prediction

## Task 1: Business Domain
**Healthcare**

---

## Task 2: Business Problem Definition

**What problem is being solved?**
Government and private hospitals across India face significant operational
and financial burden when patients are readmitted within 30 days of discharge.
These unplanned readmissions indicate gaps in treatment quality, discharge
planning, or post-care follow-up. Currently no automated system exists to
flag high-risk patients before they leave the hospital.

**Who are the users and stakeholders?**
- Treating doctors and resident physicians
- Hospital discharge planning and care coordination teams
- Health insurance providers tracking readmission-linked penalties
- Patients and their family caregivers

**What is the current manual or traditional process?**
Doctors rely on clinical experience and basic rule-based checklists to
assess whether a patient is ready for discharge. There is no systematic
data-driven risk scoring in place. High-risk patients are typically
identified only after readmission has already occurred.

**Limitations of the current process:**
- Highly subjective — outcome depends on individual doctor experience
- Cannot detect complex multi-variable risk patterns across patient history
- Single-variable thinking — only checking latest vitals or one lab result
- Readmission penalties under Ayushman Bharat cause direct financial loss
- High cost of unplanned readmissions on hospital bed availability

---

## Task 3: AI Task Type

**Binary Classification**

**Why?**
The model must predict one of two outcomes — whether a patient will be
readmitted within 30 days (1) or not (0). This is a binary classification
problem. A neural network classifier can learn non-obvious relationships
between patient age, diagnosis severity, number of medications, lab result
flags, and discharge destination to generate an accurate readmission
risk score before the patient leaves the hospital.

---

## Task 4: Data Requirement Plan

**Type of Data:** Structured tabular data from Electronic Health Records

**Structured or Unstructured:** Structured

**Input Features:**

| Feature                          | Type        |
|----------------------------------|-------------|
| Patient Age                      | Numerical   |
| Gender                           | Categorical |
| Primary Diagnosis (ICD-10 Code)  | Categorical |
| Number of Prior Hospitalizations | Numerical   |
| Length of Current Stay (days)    | Numerical   |
| Total Medications at Discharge   | Numerical   |
| Abnormal Lab Flags Count         | Numerical   |
| Discharge Destination            | Categorical |
| Number of Comorbidities          | Numerical   |
| Insurance / Scheme Type          | Categorical |

**Target Variable:** readmitted_30days (1 = Readmitted, 0 = Not Readmitted)

**Data Collection Method:**
- Hospital EHR and HMIS systems
- Ayushman Bharat Pradhan Mantri Jan Arogya Yojana (AB-PMJAY) database
- Laboratory Information Management System (LIMS)

**Data Quality Risks:**
- Missing lab values for patients with incomplete discharge workups
- Inconsistent ICD-10 coding practices across hospital departments
- Severe class imbalance — readmissions are minority events (15-20%)
- Patient data governed by Digital Personal Data Protection Act 2023
- Outdated records if EHR systems are not regularly synced

---

## Task 5: Model Recommendation

**Recommended Model: Feed-Forward Neural Network (MLP)**

**Architecture:**

| Layer         | Details                              |
|---------------|--------------------------------------|
| Input Layer   | 10 features (post encoding/scaling)  |
| Hidden Layer 1| 64 neurons, ReLU, Dropout 0.3        |
| Hidden Layer 2| 32 neurons, ReLU                     |
| Output Layer  | 1 neuron, Sigmoid activation         |

**Loss Function:** Binary Crossentropy
**Optimizer:** Adam (learning rate = 0.001)
**Class Imbalance Handling:** class_weight parameter during training

**Why Feed-Forward Neural Network?**
- Handles mixed feature types (categorical + numerical) after preprocessing
- Learns complex non-linear interactions between clinical variables
- Lightweight enough to deploy within existing hospital IT infrastructure
- Can be retrained monthly as new patient discharge data accumulates
- Sigmoid output gives interpretable probability score (0.0 to 1.0)

**Alternative Considered:**
Random Forest — good baseline and interpretable but less capable of
learning complex feature interactions compared to a neural network

---

## Task 6: Evaluation Plan

**Technical Metrics:**

| Metric              | Why Important                                      |
|---------------------|----------------------------------------------------|
| Recall (Sensitivity)| Most critical — must catch actual readmissions     |
| Precision           | Avoid unnecessary interventions for low-risk cases |
| AUC-ROC             | Measures overall model discrimination ability      |
| F1-Score            | Balances precision and recall on imbalanced data   |
| Accuracy            | General performance reference                      |

**Business Metrics:**
- Reduction in 30-day readmission rate (%)
- Cost saved per avoided unplanned readmission (INR)
- Average time saved per patient discharge assessment (hours)
- Number of high-risk patients successfully flagged per month

**Possible Failure Cases:**
- False Negative: Model misses a high-risk patient who then gets readmitted
- False Positive: Model over-flags low-risk patients causing unnecessary
  interventions and increased hospital workload

**Human Review / Validation Process:**
- All predictions with probability between 0.40 and 0.60 must be
  reviewed by the treating physician before acting on them
- Monthly audit of model performance metrics by hospital analytics team
- Quarterly retraining with latest patient discharge data

---

## Task 7: Responsible AI Considerations

**Bias in Data:**
Historical discharge records may reflect past disparities in care quality
across age groups, gender, or geographic regions. The model can inherit
and amplify such biases if not audited regularly.

**Incorrect Predictions:**
A false negative means a genuinely high-risk patient is discharged without
additional support and gets readmitted — directly harming patient health.
A false positive causes unnecessary resource use and patient anxiety.

**Privacy Concerns:**
Patient health records are among the most sensitive personal data.
All data collection and storage must comply with the Digital Personal
Data Protection Act 2023 and relevant hospital data governance policies.

**Over-Reliance on AI:**
Doctors may blindly trust model predictions without applying their own
clinical judgment. This is especially dangerous for edge cases not well
represented in training data.

**Impact on Users:**
Elderly patients or those with rare conditions may be systematically
underrepresented in training data, leading to less accurate predictions
for these vulnerable groups.

**Need for Human Oversight:**
The AI system should assist clinical decisions, not replace them. Every
flagged high-risk prediction must have a human-reviewable explanation,
and final discharge decisions must always rest with the treating doctor.

---

## Task 8: Final One-Page Solution Summary

| Field                    | Details                                              |
|--------------------------|------------------------------------------------------|
| Problem                  | Predict which patients are at risk of readmission    |
| Domain                   | Healthcare                                           |
| AI Task Type             | Binary Classification                                |
| Proposed AI Solution     | Feed-Forward Neural Network (MLP)                    |
| Required Data            | Patient EHR records, lab results, discharge info     |
| Model Recommendation     | 2-layer MLP with Sigmoid output layer                |
| Key Evaluation Metrics   | Recall, AUC-ROC, F1-Score                            |
| Expected Business Impact | 25-35% reduction in readmission rate                 |
| Top Risk                 | Bias against elderly or rare condition patients      |
| Mitigation Plan          | Regular bias audits, mandatory human review,         |
|                          | quarterly retraining, explainability layer added     |
