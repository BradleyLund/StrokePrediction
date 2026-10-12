# Scoping the Population

## What it is
Before modelling, decide **who the model is for**. The model should be trained and tested on the same kind of people it will actually be used on. In data science this is the **target population** (not to be confused with the *target* column).

Let the **business problem** drive this, not just the data. Ask: *"Who will the outreach team actually call?"*

## Our decision: adults only (18+)
The scenario is a stroke-prevention outreach program: preventative visits, blood pressure and glucose management, lifestyle changes. That's aimed at adults. Care coordinators won't be calling parents of 4-year-olds about stroke prevention.

The brief says: *"If something is unclear, make a reasonable assumption, write it down and keep going."* So this is a **documented assumption**:

> **Assumption:** the outreach program targets adults. Patients under 18 are excluded from modelling.

### Supporting reasons
| Reason | Detail |
|---|---|
| Matches the use case | Outreach is for adults |
| Almost no signal lost | Only 2 of 249 strokes were under 18 (a 1-year-old and a 14-year-old). Both are rare and probably have different causes from adult strokes |
| Removes cleaning headaches | Median-filling a 1-year-old's missing BMI with an adult median (~28) would be nonsense. All under-10s have "Unknown" smoking |
| More honest metrics | See below |

### The "easy negatives" problem
Children are **easy negatives**: patients the model can confidently rank as "no stroke" just from their age. Including 856 of them makes the model *look* better, because it gets lots of easy cases right. But the outreach team never needed help ruling out children. Testing on adults only gives a more honest picture of how well the model separates **adults who will have a stroke from adults who won't**, which is the hard part that matters.

## Why not build two models (with and without children)?
It's a good instinct, but it **doubles every later step** (training, tuning, evaluation, charts, slides) in a 2–3 hour exercise, and it makes the presentation harder to follow. Better practice:
- Pick **one** primary population and justify it.
- Put the comparison in the **parking lot**: *"With more time, I'd compare a model trained on all ages to check that excluding children doesn't change the adult results."*

## After filtering
| | All patients | Adults (18+) |
|---|---|---|
| Patients | 5,109 | 4,253 |
| Strokes | 249 | 247 |
| Stroke rate | 4.9% | 5.8% |
| Missing BMI | 201 | 181 |
| "Unknown" smoking | 1,544 | 862 |
