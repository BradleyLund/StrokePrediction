# Charts for EDA

## Why chart at all?
Tables of numbers are hard to compare by eye, and charts make patterns obvious. In EDA, charts answer one question: **which features seem related to the target (stroke)?** They also become your presentation slides, especially for the non-technical audience.

## Pick the chart from the question
| Question | Chart |
|---|---|
| How does the stroke **rate** differ between groups? (categories, yes/no) | **Bar chart** of stroke rate per group |
| How does stroke rate change with a number like age? | **Bin** the number into groups, then a bar chart |
| How do the values of a number differ between stroke and no-stroke patients? | **Box plot**, one box per group |

Plot the stroke **rate** (%), not the count. There are far more non-stroke patients, so counts mostly show group size, not risk.

## Binning
**Binning** means cutting a continuous number into ranges (bins), e.g. ages 18–29, 30–39, ... In pandas:
```python
pd.cut(df["age"], bins=[18, 30, 40, 50, 60, 70, 83], right=False)
```
`right=False` means each bin includes its left edge but not its right one, so `[30, 40)` means 30 ≤ age < 40. The square bracket `[` means "included" and the round bracket `)` means "excluded".

## Reading a box plot
```
   o  o        ← outliers: individual points far from the rest
   ┬           ← whisker: reaches the furthest point within 1.5 × the box height
 ┌─┴─┐
 │   │         ← top of box = 75th percentile (Q3)
 ├───┤         ← line in the middle = MEDIAN
 │   │
 └─┬─┘         ← bottom of box = 25th percentile (Q1)
   ┴
```
- The **box** holds the middle 50% of patients. Its height is called the **IQR** (InterQuartile Range).
- Compare the **median lines** to see whether one group is typically higher.
- Compare the **box sizes** to see whether one group is more spread out.

## Reference lines
A dashed line at the **overall stroke rate** makes each bar easy to read: above the line = riskier than average, below = safer than average.

## Rate vs count: two different stories
A rate chart answers **"how risky is each group?"** A count answers **"where are most of the cases?"** Both are useful, but don't read one off the other's chart.

| Age group | Patients | Strokes (count) | Stroke rate |
|---|---|---|---|
| 50–59 | 834 | 48 | 5.8% |
| 60–69 | 621 | 47 | 7.6% |
| 70+ | 755 | 134 | **17.7%** |

- **Rate:** a 70+ patient is about 3× as likely to have a stroke as the average adult.
- **Count:** 73% of all strokes (181 of 247) are in patients aged 60+.

For outreach, both matter. The rate says who is riskiest *per call*, and the count says where most of the cases you could prevent actually are.

## Cautions when reading EDA charts
- **Small groups are noisy.** `Never_worked` contains only **5 adults**. With a 5.8% stroke rate, you'd expect 0.3 strokes among 5 people, so seeing 0 tells you nothing. Always ask "how many patients is this bar based on?"
- **Charts can hide missing data.** The BMI box plot silently leaves out patients with missing BMI, and that includes 39 of the 247 adult stroke patients (16%), the sickest group. A chart only shows the rows that have a value.
- **One feature at a time can mislead.** Each chart shows one feature on its own, so confounders (especially age) can create patterns that aren't real. `ever_married` looks risky mainly because married people are older. See [hypotheses-and-confounding.md](hypotheses-and-confounding.md).
- **Related ≠ causes.** "Correlation is not causation". A chart shows two things move together, not that one causes the other.

## matplotlib/seaborn essentials
| Code | Meaning |
|---|---|
| `fig, ax = plt.subplots(figsize=(8, 4))` | Create a figure (the whole image) containing one chart area (`ax`, short for "axes"). `figsize` is width and height in inches |
| `fig, axes = plt.subplots(2, 4)` | A grid of 2 rows × 4 columns of charts. `axes` holds all 8 |
| `axes.flatten()` | Turns the 2×4 grid into a simple list of 8, so you can loop over it |
| `zip(a, b)` | Pairs items from two lists: (a[0], b[0]), (a[1], b[1]), ... |
| `ax.axhline(y)` | Draw a horizontal line at height y |
| `ax.set_title / set_xlabel / set_ylabel` | Chart title and axis labels |
| `PercentFormatter(1.0)` | Shows 0.05 on the axis as 5%. The `1.0` means "1.0 = 100%" |
| `plt.tight_layout()` | Auto-adjusts spacing so labels don't overlap |
| `plt.show()` | Display the chart |
| `fig.savefig("name.png", dpi=150)` | Save the chart as an image file (for slides). `dpi` = dots per inch (resolution) |
