# Thresholds and Decisions

## Threshold vs "top N": two ways to say the same thing
- **Threshold:** flag everyone whose predicted probability is ≥ X.
- **Top N (capacity-based):** sort everyone by risk and call the top N, as many as the team can handle.

Every "top N" corresponds to a threshold (the probability of the Nth patient), and vice versa. For outreach, **start from capacity**: the team knows how many calls it can make, not what probability it wants. Convert to a threshold afterwards if you need a fixed rule for future months.

## Diminishing returns
Each extra block of calls finds **fewer** stroke patients than the block before, because the model puts the highest-risk patients first. Test set, baseline logistic regression:

| Calls (top % of patients) | Strokes reached in **that block** | Hit rate in that block |
|---|---|---|
| First 5% | 14 | 33% |
| Next 5% (5–10%) | 6 | 14% |
| Next 10% (10–20%) | 11 | 13% |
| Next 10% (20–30%) | 4 | **5%, same as random calling** |

Beyond about the top 20%, the model adds almost nothing over picking patients at random. That's a natural **upper limit** for model-driven outreach.

## The trade-off a threshold represents
- **A lower threshold (more calls)** reaches more stroke patients (higher **recall**), but each call is less likely to be useful (lower **precision**), so coordinator time goes on lower-risk patients.
- **A higher threshold (fewer calls)** makes every call count, but misses more high-risk patients.

Which is "right" depends on **capacity** and the **relative cost** of a wasted call (a few minutes of coordinator time) vs a missed patient (a possible stroke). In prevention outreach a missed patient is far more costly, which argues for calling as many as capacity allows, **up to the point where returns flatten out**.

## Expected impact: how to phrase it
Always give **three numbers** for a chosen call volume:
1. **Recall:** "you'll reach about X% of the stroke patients"
2. **Precision:** "about 1 in Y calls will be to a stroke patient"
3. **Lift:** "that's Z× better than calling at random"

And **one caveat**: these come from a test set with only 49 strokes, so treat them as approximate ranges, not exact promises.

## Calibration (why the probabilities themselves need care)
A model is **calibrated** if, among patients it scores at 20%, about 20% really have the outcome. Ranking can be good even when the raw probabilities are off. Because we use the model to **rank**, calibration matters less, but you'd check it before showing individual risk percentages to clinicians.
