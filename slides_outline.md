# Slide Outline: Stroke Risk Outreach Model

20 minutes, about 10 slides (around 2 minutes each). Mixed audience: lead every slide with the **plain-English message**, and keep the technical detail for questions.

Numbers below come from our analysis. **Check them against your own notebook output** before you put them on slides.

---

## 1. Title
**Prioritising stroke-prevention outreach with a risk model**
Your name, date.

## 2. The problem and how I approached it
- **Goal:** help care coordinators decide **who to call first**, given limited capacity.
- So this is a **ranking** problem. Success = how many real high-risk patients appear near the top of the call list.
- **Approach:** explore and clean → simple baseline → stronger models → choose a call volume → recommendation.
- **Assumption:** the outreach program targets **adults (18+)**.

## 3. The data and its quality issues
- 5,110 patients, **only 4.9% had a stroke**. Rare outcome → accuracy is misleading (an "always no" model is 95% accurate and useless).
- Issues found, and what I did:
  - BMI missing (201): kept, with a "BMI missing" flag. Filled with the training median inside the pipeline
  - Smoking "Unknown" (30%): kept as a category. Mostly children, whose smoking isn't recorded
  - BMI > 60 (13): capped at 60 (likely entry errors)
  - Gender "Other" (1): removed
  - Under-18s (856, only 2 strokes): excluded. Out of scope for adult outreach
- *Visual:* a simple cleaning table.

## 4. What the data tells us (EDA)
- **Age is the dominant factor:** a 70+ stroke rate of ~18% vs 5.8% overall.
- Hypertension, heart disease and higher glucose roughly double or triple the rate.
- **Missing BMI → 20% stroke rate** (vs 4%): a big signal, but a possible leakage risk (next slides).
- Gender, residence and smoking show little difference. Some apparent effects (married, self-employed) are **age in disguise** (confounding).
- *Visuals:* `figures/stroke_by_age.png`, `figures/stroke_by_category.png`

## 5. Models and how I compared them
- **Metrics:** PR AUC (focuses on the rare stroke cases, random = 0.058) and ROC AUC (random = 0.5). Plus "strokes reached per N calls" for the business view.
- **Fair comparison:** 5-fold stratified cross-validation on the training set. The test set was kept sealed.
- Results (CV):

| Model | ROC AUC | PR AUC |
|---|---|---|
| Logistic regression | 0.82 | 0.23 |
| Random forest (constrained) | 0.81 | 0.22 |
| Gradient boosting | 0.81 | 0.23 |

- The differences are **smaller than the noise** between folds → tie.
- The default random forest **overfit** (train 1.00 vs CV 0.77). Constraining it fixed the gap but didn't beat LR.

## 6. Recommended model: logistic regression
- Performs as well as the complex models, and is **interpretable, simple to run and easy to trust**.
- **Test set (unseen patients):** ROC AUC 0.82, PR AUC 0.23 (**about 4× random**).
- What drives risk: age, missing BMI, hypertension, glucose.
- *Visual:* coefficient chart `figures/coefficients.png`

## 7. How many to call: the trade-off
- Calling more patients → reach more stroke patients, **but** each extra call is less likely to be useful.
- *Visual:* gains chart `figures/gains_baseline.png`
- **Diminishing returns:** beyond the top ~20%, the model is no better than random calling.

## 8. What the outreach team should expect
| Call the top... | Reach this share of stroke patients | Hit rate (1 in...) | vs random |
|---|---|---|---|
| 5% | ~29% | 1 in 3 | ~6× |
| 10% | ~41% | 1 in 4 | ~4× |
| 20% | ~63% | 1 in 5–6 | ~3× |

- **Recommendation:** rank all adult patients monthly and call from the top down **as far as capacity allows, up to about the top 20%**. If capacity is about 10% of patients, expect to reach about 4 in 10 stroke-risk patients, with about 1 in 4 calls being to someone who's had a stroke.
- **Caveat:** based on 49 test strokes, so treat these as approximate.

## 9. Risks and caveats
- **Possible leakage:** BMI may be missing *because* of a stroke. **Check:** without the flag, the model still performs similarly (CV ROC AUC 0.81 vs 0.82, PR AUC 0.20 vs 0.23), so it doesn't depend on it. Before launch, confirm with the data owner, and use the no-flag version if in doubt.
- **Timing:** we don't know whether features were recorded before or after the stroke. The target is "had a stroke", not "will have one".
- The model identifies risk; it doesn't prove outreach prevents strokes.

## 10. Parking lot and next steps
Pick the 5–6 most important from `parking_lot.md`, one line each:
- **Richer clinical data** (BP readings, atrial fibrillation, cholesterol, prior TIA). Three model types all hit ~0.82, which points to a data ceiling, not a model one
- Confirm with the data owner how and when BMI and other features are recorded (leakage and timing)
- Rebuild the target as "stroke in the next X years" for true prediction
- Fairness check across gender, residence and age groups
- Calibration check before showing individual risk %
- Tune the tree models (unlikely to change the recommendation)
- Pilot to measure whether outreach actually reduces strokes

---

## Likely questions: prepare an answer for each
- Why not accuracy?
- Why exclude children?
- Why logistic regression if gradient boosting is "better" in general?
- Isn't the missing-BMI flag leakage?
- How would you choose the threshold if capacity changes?
- What would you do with another week?
