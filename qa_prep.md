# Q&A Prep

Likely questions with **key points**, not scripts. Practise answering each one **out loud in your own words**. If you can explain it simply, you understand it.

A good answer shape: **direct answer → the reason → the evidence (a number) → what you'd do next.**

---

## The problem and metrics

**Why didn't you use accuracy?**
- Only about 5% had a stroke, so a model that always says "no" is 95% accurate and finds nobody.
- I used PR AUC (focuses on the rare stroke cases) and "strokes reached per N calls" (what outreach actually cares about).

**Explain ROC AUC / PR AUC to a non-technical person.**
- ROC AUC: "if I pick one stroke patient and one non-stroke patient, how often does the model rank the stroke patient higher? We get 82%; a coin flip gets 50%."
- PR AUC: "how well stroke patients are concentrated at the top of the call list. Random is 0.06, we get 0.23, about 4× better."

**How confident are you in these numbers?**
- The test set has only 49 strokes, so they're approximate. One or two patients more or less moves them noticeably.
- Cross-validation (5 rounds) gave similar results (0.82 / 0.23), which increases confidence.

---

## Data decisions

**Why did you exclude children?**
- The outreach program (BP, glucose, lifestyle) targets adults. Coordinators wouldn't call parents of toddlers about stroke prevention.
- Only 2 of 249 strokes were under 18, so almost no signal was lost.
- Children are "easy negatives" that would inflate the metrics without helping with the real task.
- Documented as an assumption, and comparing with an all-ages model is in the parking lot.

**How did you handle missing BMI?**
- Missing BMI patients had a 20% stroke rate vs 4%, so the missingness itself is informative.
- Added a `bmi_missing` flag to keep that signal, and filled the gaps with the median **from the training data only** (inside the pipeline) to avoid leakage.

**Isn't the missing-BMI flag data leakage?**
- Possibly. If BMI is missing *because* of a stroke admission, it wouldn't exist for future patients.
- I tested it: without the flag, performance barely changes (CV PR AUC 0.23 → 0.20). The model doesn't depend on it.
- Next step: ask the data owner how BMI is recorded. If in doubt, deploy the version without it.

**Why cap BMI at 60 rather than remove those patients?**
- 13 patients. Values like 97.6 are almost certainly entry errors, but their other information is valid.
- Capping keeps the patients and limits the damage. None had a stroke, so the impact is small. It's a judgement call, and I documented it.

**What about "Unknown" smoking status?**
- 30% of patients. Every child under 10 was "Unknown", so it mostly reflects age (confounding).
- Among adults, "Unknown" had a similar stroke rate to known statuses. Kept as its own category.

---

## Modelling

**Why logistic regression and not a more advanced model?**
- I tried random forest and gradient boosting. Neither beat LR by more than the cross-validation noise.
- When models tie, choose the simplest: interpretable, easy to explain to clinicians, easy to maintain.

**What is overfitting and did you see it?**
- Memorising the training data instead of learning general patterns.
- The default random forest scored 1.00 on training data but 0.77 on new patients. Constraining it (min 20 patients per leaf) closed the gap.

**Why cross-validation?**
- To compare models fairly without using the test set. Using the test set to pick a winner would make its score optimistic.
- 5 rounds give an average plus a measure of noise (the standard deviation).

**Why didn't you balance the classes (class weights / oversampling / SMOTE)?**
- I need a good **ranking**, and balancing mostly shifts the probabilities up without changing the order much. A quick test with class weights gave the same CV scores.
- It also keeps the probabilities realistic. (SMOTE = Synthetic Minority Over-sampling TEchnique, which creates artificial stroke cases. Unnecessary here.)

**Heart disease looked important in your charts but has a small coefficient. Why?**
- Patients with heart disease are mostly older and often have hypertension. Once age and hypertension are in the model, heart disease adds little extra. The information overlaps.

**What would you do with another week?**
- Not more tuning: three very different models all hit ~0.82, which points to a **data ceiling**.
- Richer clinical data (BP readings, atrial fibrillation, cholesterol, prior mini-strokes), confirm feature timing with the data owner, fairness and calibration checks.

---

## Decisions and use

**How did you choose the threshold?**
- By **capacity**, not a fixed probability: rank patients and call from the top down as far as the team can.
- Recommended limit is about the top 20%, because beyond that the model is no better than random calling (diminishing returns).

**What happens if capacity changes?**
- Just move down or up the ranked list. The table shows the expected reach at 5/10/20/30%. There's no need to retrain.

**What should the outreach team expect?**
- Top 10% → reach ~41% of stroke patients, ~1 in 4 calls is to a stroke patient, ~4× better than random.
- Top 20% → ~63% reached, ~1 in 5–6 calls, ~3× better.

**What's the trade-off of your cutoff?**
- More calls = more patients reached (recall up) but more calls to people who won't have a stroke (precision down).
- A missed patient costs far more than a wasted call, which is why I'd go as far as capacity allows, up to the point where the model stops adding value.

**How would this run in practice?**
- Monthly: score all adult patients, sort by risk, hand coordinators the top of the list.
- Monitor reach and outcomes, and retrain periodically (data changes over time: drift).

**Can a coordinator see why a patient was flagged?**
- Yes. Logistic regression shows each factor's contribution (e.g. age 78, hypertension, high glucose). Another advantage over the tree models.

**Is the model fair across groups?**
- Not yet checked. It's in the parking lot. I'd compare reach for men vs women, urban vs rural, and age bands before launch.

---

## Curveballs

**You used an AI assistant. How do we know this is your thinking?**
- Be honest: yes, I used one as a tutor, as the brief allowed, and it taught me the concepts step by step.
- I wrote and ran the code myself, made the decisions, and can explain every one. (Then give an example: excluding children, or the leakage test.)

**What was the hardest part / what surprised you?**
- Ideas: that the fancier models didn't help; that missing BMI was so predictive; that "Unknown" smoking was really about age.

**What would you do differently?**
- Ask the data owner about timing and BMI recording *first*, before modelling, since it affects whether the target and features are valid at all.
