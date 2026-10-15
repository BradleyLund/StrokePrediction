# pandas Basics

pandas is the Python library for working with tables. Think of it as Excel controlled by code.

## Importing
```python
import pandas as pd
```
`import` loads a library. `as pd` gives it a short nickname, so you type `pd.read_csv` instead of `pandas.read_csv`. Everyone uses these nicknames: `pd` (pandas), `np` (numpy), `plt` (matplotlib.pyplot), `sns` (seaborn).

## Key objects
- **DataFrame**: a whole table (rows and columns). We usually call ours `df`.
- **Series**: a single column. `df["age"]` gives you a Series.

## Common operations
| Code | What it does |
|---|---|
| `df = pd.read_csv("file.csv")` | Load a CSV file into a DataFrame |
| `df.head()` / `df.head(10)` | Show the first 5 (or 10) rows |
| `df.shape` | (number of rows, number of columns). No brackets, because it's a property, not a function |
| `df.info()` | Column names, data types, non-missing counts |
| `df.describe()` | Summary statistics for the number columns |
| `df["col"]` | Select one column |
| `df["col"].value_counts()` | Count how many times each value appears |
| `df.isna()` | True/False table: is each cell missing? |
| `df.isna().sum()` | Count missing cells per column (True counts as 1) |
| `df[df["age"] > 60]` | **Filter**: keep only rows where the condition is true |
| `df.groupby("col")["stroke"].mean()` | Split rows into groups by `col`, then average `stroke` within each group |

## Method chaining
pandas lets you string steps together: `df.isna().sum()` means "take df → mark missing cells → add them up per column". Read it left to right.

## Data types (dtypes)
| dtype | Meaning |
|---|---|
| `int64` | Whole numbers (e.g. 0, 1, 67) |
| `float64` | Decimal numbers (e.g. 228.69). Missing values force a column to be float |
| `object` | Text (strings) in pandas |
| `bool` | True / False |

## Shapes: 1-D vs 2-D
| Code | Type | Shape | Think of it as |
|---|---|---|---|
| `df["stroke"]` (single brackets) | Series | `(4253,)` | A **list** of 4,253 values: one dimension |
| `df[["stroke"]]` (double brackets) | DataFrame | `(4253, 1)` | A **table** with 4,253 rows and 1 column: two dimensions |

`(4253,)` is a shape with only one number, meaning "1-dimensional, length 4253". The trailing comma is just how Python writes a one-item tuple. scikit-learn expects **y as 1-D** and **X as 2-D**.

## numpy arrays and `[rows, columns]` indexing
scikit-learn often returns **numpy arrays**: grids of numbers without column names. You pick parts of them with `array[rows, columns]`:

| Code | Meaning |
|---|---|
| `probs[0, 1]` | Row 0, column 1: a single number |
| `probs[:5]` | The first 5 rows (all columns) |
| `probs[:, 1]` | **All** rows, column 1 only |
| `probs[:5, 1]` | First 5 rows, column 1 only |

- `:` on its own means "everything along this dimension".
- `:5` means "from the start up to (not including) 5", i.e. positions 0–4.
- Python counts from **0**, so column 0 is the first column and column 1 is the second.

`predict_proba` returns 2 columns: `[P(no stroke), P(stroke)]`. Each row adds up to 1. `[:, 1]` keeps just the stroke probability for every patient.

## A note on the trick behind `.mean()` on a 0/1 column
If a column is all 0s and 1s, its **mean is the proportion of 1s**. So `df["stroke"].mean()` = 0.049 means 4.9% of patients had a stroke. You'll use this trick constantly.
