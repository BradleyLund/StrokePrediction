# Domain: predicting insurance claim denials

Not part of the stroke take-home itself. This is background on the kind of model the company is likely to build, so the stroke project can be connected to their real work in the presentation and interview.

## What a claim is

When a doctor, hospital or lab treats a patient, they send a **claim** (a bill) to the patient's insurer (the **payer**). The payer either pays it, pays part of it, or **denies** it. Each denial has a reason code. The provider can then fix the claim and resubmit it, or appeal.

Companies in **RCM** (Revenue Cycle Management) handle this process for providers. A denied claim costs money to rework, and some are never paid at all. Catching a likely denial *before* the claim is sent is worth a lot.

### What's on a claim
| Part | Examples |
|---|---|
| **Patient** | Age, sex, insurance member ID, plan type, whether their coverage was active on the date of service |
| **Payer** | Which insurer (Medicare, Medicaid, a commercial insurer such as Aetna), and which specific plan |
| **Provider** | Who did the work (identified by **NPI**, National Provider Identifier), their specialty, in-network or out-of-network |
| **Diagnosis codes** | **ICD-10** codes (International Classification of Diseases, 10th revision): *why* the patient was seen |
| **Procedure codes** | **CPT** (Current Procedural Terminology) or **HCPCS** (Healthcare Common Procedure Coding System) codes: *what* was done |
| **Modifiers** | 2-character add-ons to a procedure code that change its meaning (e.g. "59" = a separate, distinct procedure) |
| **Units and charges** | How many of each service, and the billed dollar amount |
| **Dates** | Date of service, date the claim was submitted |
| **Place of service** | Office, hospital inpatient, outpatient, lab, home |
| **Authorization** | Prior authorization number, if the payer required approval in advance |

### Common reasons claims are denied
- Patient wasn't eligible or covered on the date of service
- Missing or invalid prior authorization
- Diagnosis code doesn't justify the procedure ("not medically necessary")
- Coding errors: wrong code, wrong modifier, codes that can't be billed together
- Missing or wrong information (typo in the member ID, missing NPI)
- Duplicate claim
- Sent too late (each payer has a **timely filing** deadline)
- Service not covered by the patient's plan

## Turning it into a model

**Target:** denied on first submission = 1, paid = 0. It's the same kind of problem as the stroke model: [binary classification](classification-features-targets.md), and usually [imbalanced](class-imbalance.md) too, because most claims get paid.

A harder version predicts the *reason* for the denial (multi-class classification), which tells staff what to fix.

### Possible features
| Group | Feature ideas |
|---|---|
| Payer | Payer name, plan type, payer's historical denial rate |
| Codes | Procedure code, diagnosis code, whether this diagnosis + procedure pair is usually approved by this payer, number of lines on the claim, modifiers used |
| Authorization | Payer requires prior auth for this procedure? Auth number present? |
| Eligibility | Was an eligibility check run before the visit? Did it come back active? |
| Timing | Days between service and submission, days left before the timely filing deadline, day of week / month (payer rule changes often start on 1 January) |
| Money | Billed amount, amount compared with the usual amount for that code |
| Provider | Specialty, in-network flag, provider's historical denial rate |
| Data quality | Number of empty fields, whether the member ID format is valid for that payer |
| History | Patient's previous denials, whether this claim is a resubmission |

### Things to watch out for
- **Data leakage:** only use what is known *when the claim is about to be sent*. Anything from the payer's response (the paid amount, the reason code) gives the answer away.
- **Historical rates** ("this payer denies 20% of claims") are strong features, but they must be calculated from *older* data only, otherwise the model is peeking at the answers.
- **Rules change:** payers update their rules, so a model trained on last year may drift. Split train/test by *date*, not randomly.
- **High-cardinality codes:** there are thousands of procedure and diagnosis codes, so one column per code doesn't work well. Group them, or use the historical rate per code instead.

## How this connects to the stroke project
| Stroke project | Claim denials |
|---|---|
| Patient → stroke yes/no | Claim → denied yes/no |
| ~5% positive | Usually a minority are denied |
| Flag high-risk patients for outreach | Flag risky claims for review before sending |
| Threshold set by outreach capacity | Threshold set by how many claims staff can review |

In both cases the model's job is to **rank** cases so a limited team spends its time where it helps most.
