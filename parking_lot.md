# Parking Lot

Things noticed but not pursued in the time available. For each: **what** I'd investigate, **why** it could matter, and **how** I'd approach it with more time.

This is a living list. Add to it whenever something interesting comes up that would take too long.

---

## Data and data quality

### 1. Why is BMI missing, and is it data leakage?
- **What:** Find out how BMI was recorded and why 201 values are blank.
- **Why:** Patients with missing BMI had a **20% stroke rate vs 4.3%**. They were also older and had more hypertension, more heart disease and higher glucose. If BMI is missing *because* of the stroke (e.g. patients admitted in an emergency weren't weighed), the `bmi_missing` flag is **leakage**. It would help the model in testing but wouldn't exist for the patients outreach is trying to reach (people who haven't had a stroke yet).
- **How:** Ask the data owner how and when BMI is recorded. Train the model with and without the `bmi_missing` flag and compare. If performance drops a lot without it, the model is leaning heavily on a signal that may not hold up in real use.

### 2. When were the features recorded relative to the stroke?
- **What:** Confirm whether hypertension, glucose, BMI etc. were measured **before** the stroke or **after** it.
- **Why:** The target is "had a stroke", which looks like a historical record rather than a future event. If features were recorded after the stroke (e.g. hypertension diagnosed during the stroke admission), the model is partly learning the *consequences* of stroke, not its warning signs. That would make it look better than it will be for outreach.
- **How:** Ask the data owner about dates. Ideally, rebuild the dataset so features come from a fixed point in time and the outcome is "stroke in the following X years".

### 3. Sensitivity of the BMI cap
- **What:** Test other ways of handling extreme BMI (cap at 50 or 70, or treat values over 60 as missing).
- **Why:** The cap of 60 is a judgement call. It's worth confirming the choice doesn't change the results.
- **How:** Re-run the final model with each alternative and compare the metrics. With only 13 patients affected and none with a stroke, I expect little difference.

### 4. Smarter filling of missing BMI
- **What:** Fill missing BMI using similar patients (e.g. by age group and gender) rather than one overall median.
- **Why:** A single median ignores that BMI varies with age. Better estimates could slightly improve the model.
- **How:** Group-wise median imputation, or a model-based method such as scikit-learn's `KNNImputer` (fills each gap using the most similar patients), kept inside the pipeline so it learns from training data only.

### 5. How is "Unknown" smoking status recorded?
- **What:** Find out why 30% of patients (862 adults) have "Unknown" smoking status.
- **Why:** For children it's simply never asked. For adults, "Unknown" might mean "not asked", "declined to answer" or "record not transferred", and each could carry different risk. Adults with "Unknown" had a 5.2% stroke rate vs 6.0% for known statuses, so the difference is currently small.
- **How:** Ask the data owner. Check whether "Unknown" adults differ on other features (age, visit type, clinic).

### 6. Gender "Other" and unseen categories
- **What:** Plan how the model handles categories it never saw in training (1 patient with gender "Other" was removed).
- **Why:** In live use, new or rare categories will appear. The model should handle them gracefully rather than crash or give odd scores.
- **How:** Configure the encoder to ignore unknown categories (`handle_unknown="ignore"` in scikit-learn) and add a data quality check that flags new categories for review.

---

## Scope and modelling

### 7. Compare with a model trained on all ages
- **What:** Train the same model on all patients, including under-18s, and compare.
- **Why:** I excluded the 856 under-18s (only 2 strokes) because outreach targets adults. It's worth confirming this didn't change the adult results, and seeing how much the children ("easy negatives") inflate the metrics.
- **How:** Re-run the pipeline without the age filter, then evaluate both models **on adults only**, so it's a like-for-like comparison.

### 8. Childhood strokes
- **What:** The 2 strokes in under-18s (a 1-year-old and a 14-year-old).
- **Why:** Childhood strokes usually have different causes (e.g. congenital conditions) from adult strokes. They're out of scope for an adult prevention program but might deserve a separate clinical review.
- **How:** Not a modelling task. Flag them to the clinical team.

### 9. Why doesn't smoking (or BMI) show a stronger link to stroke?
- **What:** Smoking and BMI are well-known stroke risk factors medically, but show little difference in this data. Only "formerly smoked" stands out (8.1% vs ~5%).
- **Why:** "Formerly smoked" patients are older (median age 58 vs ~48), so that bar may just be age again (confounding). The data also lacks detail: no amount or duration of smoking, 30% "Unknown", and BMI's effect may run *through* hypertension and diabetes, which are already in the data. Missing-BMI patients (the sickest) are also absent from the BMI comparison.
- **How:** Compare smoking groups within age bands (e.g. 60–69 only). Ask whether richer data (pack-years, smoking duration) is available. The model, which sees all features together, will partly answer this.

### 10. Tune the tree models properly
- **What:** Systematically tune random forest and gradient boosting hyperparameters (depth, leaf size, learning rate).
- **Why:** With default or lightly constrained settings they tied with logistic regression. Careful tuning *might* find a small gain, though with only ~200 training strokes I'd expect little.
- **How:** `GridSearchCV` or `RandomizedSearchCV` with stratified 5-fold CV on the training set, scored on PR AUC. Only adopt a tree model if it beats LR by more than the CV noise.

### 10b. Richer clinical features (likely the biggest win)
- **What:** Add features known to drive stroke risk that this extract doesn't have: blood pressure readings (not just yes/no hypertension), atrial fibrillation, cholesterol, diabetes diagnosis and HbA1c, prior TIA (mini-stroke), medications, family history, smoking amount.
- **Why:** Three very different model types all scored about the same (ROC AUC ≈ 0.81–0.82). That suggests we've hit the **data ceiling**: the limit is the information in these 10 columns, not the algorithm.
- **How:** Work with the data/clinical teams to pull these from the health record, then re-run the same pipeline and comparison.

### 11. Calibration of predicted probabilities
- **What:** Check whether a predicted 20% risk really means about 20% of such patients have a stroke.
- **Why:** We use the model to *rank*, which doesn't need calibrated probabilities. But if coordinators or clinicians see individual risk percentages, those need to be trustworthy.
- **How:** A calibration plot (predicted vs actual rate, by risk band). If needed, `CalibratedClassifierCV`.

### 12. Fairness check across groups
- **What:** Check whether the model performs equally well for men and women, urban and rural patients, and different age bands.
- **Why:** An outreach list that systematically under-ranks one group would make health inequalities worse.
- **How:** Compute recall@N and PR AUC separately per group on the test set or CV folds, and compare.

### 13. Monitoring after launch
- **What:** Track the model's performance and the patient mix over time.
- **Why:** Populations and recording practices change (e.g. if BMI starts being recorded differently), and model performance can quietly degrade. This is called **drift**.
- **How:** Monthly report on outcomes for contacted vs non-contacted patients, feature distributions, and recall@N. Re-train periodically.

### 14. Measure the outreach program's real effect
- **What:** Find out whether calls actually *prevent* strokes, not just whether the model finds high-risk patients.
- **Why:** The model identifies risk. It doesn't prove the intervention works.
- **How:** A pilot that compares contacted high-risk patients with a similar group not yet contacted (e.g. a staggered rollout).

---

<!-- Add new items below as we go (models, metrics, thresholds, fairness...) -->
