# Missing and Suspicious Data

## Rule 1: investigate before you "fix"
When you see a strange value, ask **"is this an error, or is it real but unusual?"** before changing anything. To answer that you usually need two things:
- **Context from other columns.** An age of 0.08 means one thing on its own, and something else when you also know the patient's work type is "children".
- **Domain knowledge**, meaning knowledge of the real-world subject (here, medicine). What's a realistic BMI? Do babies appear in hospital records? (Yes.)

Deleting real-but-unusual data throws away information. Keeping genuine errors misleads the model.

## Types of "missing"
| Type | Example in our data | How pandas sees it |
|---|---|---|
| **True blank** | `bmi` = N/A for 201 patients | `NaN`, which `isna()` counts |
| **Disguised missing** | `smoking_status` = "Unknown" for 1,544 patients | A normal text value. `isna()` does **not** count it |

Disguised missing values are the dangerous ones, because the standard checks don't catch them. You only find them by reading the categories with `value_counts()`.

## Is the missingness random? (This matters a lot)
Always check whether the rows with missing values are **different** from the rest. A good way is to compare the target rate:

> Stroke rate when BMI is present: **4.3%**
> Stroke rate when BMI is missing: **19.9%**

Patients with missing BMI are about **4–5 times more likely** to have had a stroke. The missingness is **informative**: the fact that it's missing tells you something. A real-world guess is that very sick patients (e.g. admitted after a stroke) weren't weighed and measured in the usual way.

Statisticians have formal names for this:
- **MCAR** (Missing Completely At Random): the gaps are pure chance. Safe to drop or fill.
- **MAR** (Missing At Random): the gaps are explained by *other* columns you have (e.g. older people more often missing).
- **MNAR** (Missing Not At Random): the gaps are related to something you *can't* see, possibly the outcome itself. The most dangerous kind.

You don't need to prove which type you have. Just show you **checked** whether missingness is random and let that guide your decision.

## Common ways to handle missing values
| Option | Pros | Cons |
|---|---|---|
| **Drop the rows** | Simple | Loses data. With informative missingness, you'd throw away a disproportionate share of stroke cases (40 of our 249!) |
| **Fill with the median** (called **imputation**) | Keeps all rows | Hides the fact that it was missing |
| **Fill + add a "was missing" flag column** | Keeps the rows *and* the signal | One extra column |
| **Treat "Unknown" as its own category** | Honest, keeps the signal | Fine for categorical columns |

**Imputation** = filling in missing values with an estimate.

## Data leakage warning (preview)
Be careful when a column might have been recorded *after* or *because of* the outcome. Missing BMI being linked to stroke could mean BMI was missing *because* the patient had a stroke. In real outreach, we'd be scoring patients who haven't had a stroke yet, so this signal might not exist for them. The model would look great in testing and then disappoint in real use. This is called **data leakage**. It has its own page later, and it's a great point to raise in the presentation.

## Outliers: real or error?
- BMI 10.3 belongs to a **1-year-old**. Babies have low BMIs, so that's plausible.
- BMI 97.6 belongs to a 17-year-old. That's extremely unlikely and probably a data entry error. 13 patients have BMI over 60.
- Common handling: **cap** (also called *clip* or *winsorise*) extreme values at a sensible maximum, or set them to missing. Document whichever you choose.
