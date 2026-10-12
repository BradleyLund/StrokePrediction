# Domain: machine learning ideas for eligibility verification

Background for the presentation discussion. It connects the stroke model to the eligibility verification work I do at Quadax.

## Is a mapping table + stored procedure a "model"?

Yes, in the broad sense. Both take inputs and produce an output. The difference is **who writes the rules**:

| | Rules-based (mapping table + stored procedure) | Machine learning (ML) model |
|---|---|---|
| Rules come from | People writing them by hand | Learned from labelled historical examples |
| Output | One answer, or no match | A probability for each possible answer |
| Unseen or messy input (typos, new payer names) | Fails or falls through to a default | Makes its best guess, with a confidence score |
| Explainability | Very easy: "row 412 matched" | Harder, though there are tools for it |
| Maintenance | Someone keeps adding rows | Retrain on new data |

The stroke model is **binary classification** (two outcomes). Picking a payer and plan is **multi-class classification**: choosing one out of hundreds or thousands of possible answers. It's the same idea, but harder.

### The realistic setup is a hybrid
1. Keep the mapping table for exact, confident matches. It's fast, cheap and easy to audit.
2. When the table has no match or several matches, ask the model to **rank the most likely payers and plans**, each with a confidence score.
3. Accept high-confidence answers automatically and send low-confidence ones to a person.
4. Each time a person confirms a pattern, it can become a new row in the mapping table.

### Where the labels would come from
The eligibility response from the payer (the **271** transaction) confirms which payer and plan was correct. That history is the "right answer" the model learns from.

### Possible features for plan assignment
- The payer name as typed at registration (free text, often messy)
- Member ID format and prefix. Some payers use prefixes that identify the plan (e.g. the Blue Cross Blue Shield alpha prefix)
- Group number
- Patient age (65+ suggests Medicare) and state/ZIP (each state runs its own Medicaid program)
- The client or facility that sent the request
- The patient's previously confirmed plans

## Other eligibility use cases
| Idea | Type of problem | What it would help with |
|---|---|---|
| Will this eligibility check come back active? | Binary classification | Flag at-risk patients before the date of service. Closest to the stroke model |
| Will this inquiry be rejected (e.g. subscriber not found)? Which field is probably wrong? | Binary / multi-class | Fix the data before sending |
| Coverage discovery: does a self-pay patient have insurance they didn't mention? Which payers should we query first? | Ranking | Fewer wasted inquiries and less unpaid care |
| Coordination of benefits (COB): which plan is primary and which is secondary? | Classification | Avoid claims being sent to the wrong payer first |
| Will coverage end before the service date (e.g. Medicaid renewal)? | Binary / time-to-event | Re-verify at the right time |
| Is a payer's connection behaving abnormally (error spike, slow responses)? | Anomaly detection (unsupervised) | Spot outages early |
| How much will the patient owe (deductible remaining)? | Regression (predicting a number) | Price estimates for the patient |

## How the stroke model maps to eligibility
| Stroke project | Eligibility |
|---|---|
| Rank patients by stroke risk | Rank requests by risk of an eligibility problem |
| Limited outreach capacity sets the threshold | Limited staff review time sets the threshold |
| Missed stroke (false negative) is the costly error | Missed eligibility problem → denied claim |
| Wasted outreach call (false positive) | Wasted manual re-check |

## Using a zero-shot classifier (e.g. Jev) for payer name matching

**What Jev is:** a decision model from TypeSafe AI. It is *not* a chatbot-style LLM (Large Language Model) because it never writes text. You give it text or JSON (JavaScript Object Notation) plus a question you've defined. It returns structured JSON with a probability attached. There are three question types: yes/no, choice (pick one of 2–255 options you define) and score (an ordered scale). It is **zero-shot**: it isn't trained on your data, it relies on general language understanding.

**The problem:** payers return slightly different names. The payer and payer alias tables catch the known variations, but many names fall through.

**The catch:** a single choice question allows at most 255 options, and there are far more payers than that. So the work is done in stages:
1. **Candidate generation (cheap):** clean up the name (case, punctuation, "INC", "PLAN", common abbreviations), then use fuzzy string matching against the payer and alias tables to get the top ~10–20 candidates. Context like state or payer ID can narrow the list further.
2. **Decision (Jev):** ask it to choose among those candidates plus a "none of these" option. Alternatively, ask yes/no for each candidate: "Is X the same payer as Y?"
3. **Confidence routing:** above a threshold, auto-map and add the name to the alias table. Below it, send it to a person.

**Watch out for:**
- Ambiguous names: "Blue Cross" exists in many states, and parent companies have subsidiaries that are billed separately. The name alone isn't enough, so add context.
- Zero-shot means it doesn't know your internal payer quirks. Test it before trusting it.
- Sending data to an outside service: keep patient information out, and check that the vendor arrangement is approved (HIPAA, the Health Insurance Portability and Accountability Act).

**How to test it (same method as the stroke model):** take alias rows that are already mapped, hide the answers, and measure how often it picks correctly. Then check whether "90% confident" is right about 90% of the time (calibration). Compare it with the fuzzy-matching step on its own as a **baseline**.
