# Exploratory Data Analysis (EDA)

## What it is
**EDA** means getting to know your data before you build anything. Think of it as inspecting a house before you buy it: you check every room and look for cracks.

You are trying to answer:
1. **What's in it?** How many rows and columns, and what kind of data is each column?
2. **What's broken?** Missing values, impossible values, typos, duplicates, odd categories.
3. **What's interesting?** Which features seem related to the target (stroke)?

## The standard first checks (and the pandas code for each)
| Question | pandas code | What to look for |
|---|---|---|
| What does it look like? | `df.head()` | Do the columns match the description you were given? |
| How big is it? | `df.shape` | (rows, columns) |
| What type is each column? | `df.info()` | Numbers stored as text? Missing counts? |
| Any missing values? | `df.isna().sum()` | Which columns have gaps, and how many |
| Summary of numbers | `df.describe()` | Impossible min/max values, odd averages |
| Categories in text columns | `df["col"].value_counts()` | Typos, rare categories, "Unknown"-style placeholders |
| Duplicates? | `df.duplicated().sum()` | Same row appearing twice |

## Reading `describe()`
| Row | Meaning |
|---|---|
| count | how many non-missing values |
| mean | the average |
| std | **standard deviation**: how spread out the values are (bigger = more spread) |
| min / max | the smallest / largest value. **Always sanity-check these** |
| 25% / 50% / 75% | **percentiles** (also called quartiles): 25% of values are below the "25%" number. The 50% value is the **median**, the middle value |

When the mean and median are far apart, a few extreme values are usually pulling the average up or down. Those extreme values are called **outliers**.

## Hidden missing values
Data isn't always missing as a blank. Watch for:
- Text placeholders like `"N/A"`, `"Unknown"`, `"?"`, `"none"`
- Impossible numbers used as fillers, like `0` or `-1` or `999`

pandas only counts real blanks (`NaN`) as missing, so you have to spot the disguised ones yourself.

## Why it matters for this project
The brief explicitly says the data "may contain quality issues". Finding them and **writing down what you decided and why** is a scored part of the exercise. Keep a running "Cleaning decisions" list in your notebook as you go.
