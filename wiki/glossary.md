# Glossary

Terms and acronyms in plain English. Added to as we go.

| Term | Meaning |
|---|---|
| **.ipynb** | IPython Notebook: the file format for Jupyter notebooks |
| **270 / 271** | The standard electronic eligibility inquiry (270) and the payer's response (271) |
| **AI** | Artificial Intelligence |
| **Anomaly detection** | Finding unusual patterns without labelled answers (a type of unsupervised learning) |
| **AUC** | Area Under the Curve: a single number summarising a performance curve (ROC or PR). Covered later |
| **Baseline** | The simple first model that every later model is compared against |
| **Binning** | Cutting a continuous number (like age) into ranges/groups |
| **Box plot** | A chart showing the median, middle 50% and outliers of a number. See [charts-for-eda.md](charts-for-eda.md) |
| **Binary classification** | Predicting one of exactly two outcomes (e.g. stroke / no stroke) |
| **BMI** | Body Mass Index: weight (kg) ÷ height (m)². A rough measure of body fat |
| **Calibration** | Whether predicted probabilities are honest: of things scored 80%, about 80% should be right |
| **Claim** | A bill a healthcare provider sends to an insurer asking to be paid for a service |
| **COB** | Coordination of Benefits: working out which insurer pays first when a patient has more than one |
| **CPT** | Current Procedural Terminology: codes for *what* was done to a patient |
| **Capping (clipping / winsorising)** | Replacing values above (or below) a limit with the limit itself |
| **Confounder** | A hidden third factor that affects both things you're comparing, making them look related. See [hypotheses-and-confounding.md](hypotheses-and-confounding.md) |
| **CSV** | Comma-Separated Values: a plain-text table where commas separate the columns |
| **Data cleaning** | Fixing errors, handling missing values and removing what doesn't belong, before modelling |
| **Data leakage** | When the model uses information it wouldn't really have at prediction time, so test results look better than real life |
| **DataFrame** | pandas' name for a table of data (rows and columns) |
| **Denial** | When an insurer refuses to pay a claim, with a reason code |
| **Domain knowledge** | Knowledge of the real-world subject the data comes from (here, medicine) |
| **dtype** | Data type of a column (int64 = whole numbers, float64 = decimals, object = text) |
| **Easy negatives** | Cases a model can trivially classify as negative (e.g. children for stroke). They make metrics look better than the model really is |
| **EDA** | Exploratory Data Analysis: looking at and summarising data before modelling |
| **Feature** | An input column used to make a prediction (also: predictor, variable, X) |
| **Fuzzy matching** | Comparing text by how similar it is, so small differences like typos still match |
| **HCPCS** | Healthcare Common Procedure Coding System: procedure/supply codes, includes CPT |
| **HIPAA** | Health Insurance Portability and Accountability Act: US law protecting patient health information |
| **ICD-10** | International Classification of Diseases, 10th revision: diagnosis codes, *why* the patient was seen |
| **Imbalanced data** | One outcome is much rarer than the other. See [class-imbalance.md](class-imbalance.md) |
| **JSON** | JavaScript Object Notation: a common text format for structured data |
| **Hypothesis** | An idea that might explain a pattern, which you then test against the data |
| **IQR** | InterQuartile Range: the distance between the 25th and 75th percentiles (the box in a box plot) |
| **Imputation** | Filling in missing values with an estimate (e.g. the median) |
| **Informative missingness** | When *whether* a value is missing is itself related to the outcome |
| **Kernel** | The Python process running behind a notebook. It holds all your variables in memory |
| **LLM** | Large Language Model: an AI that reads and generates text (e.g. ChatGPT, Claude) |
| **MAR / MCAR / MNAR** | Missing At Random / Missing Completely At Random / Missing Not At Random. See [missing-and-suspicious-data.md](missing-and-suspicious-data.md) |
| **Median** | The middle value when sorted. Less affected by extreme values than the mean |
| **ML** | Machine Learning |
| **Multi-class classification** | Predicting one of three or more possible outcomes |
| **N/A / NaN** | Not Available / Not a Number: how missing values appear in data |
| **NPI** | National Provider Identifier: a unique ID for each US healthcare provider |
| **Outlier** | A value far away from the rest |
| **Payer** | The insurer that pays the claim (Medicare, Medicaid, a commercial insurer) |
| **Percentile / quartile** | The value below which a given % of the data falls (25%, 50%, 75%) |
| **pip** | Python's package installer |
| **Pipeline** | A scikit-learn object that chains preparation steps and a model, so each step learns from training data only |
| **Positive class** | The outcome we're trying to detect (stroke = 1). It doesn't mean "good" |
| **Proxy** | A feature that quietly stands in for another feature (e.g. "Unknown" smoking standing in for "young") |
| **PR** | Precision–Recall (as in "PR curve"). Covered later |
| **Prior authorization** | Approval from the insurer required *before* certain services |
| **RCM** | Revenue Cycle Management: the process of billing and getting paid for healthcare services |
| **Regression** | Predicting a number (e.g. a dollar amount) rather than a category |
| **ROC** | Receiver Operating Characteristic: a performance curve (the name comes from WWII radar). Covered later |
| **Rules-based system** | Logic written by hand (e.g. a mapping table), as opposed to rules learned from data |
| **Series** | pandas' name for a single column |
| **sklearn** | Short name for the scikit-learn library |
| **Std (standard deviation)** | How spread out values are around the average |
| **Stratified split** | Splitting data so each piece keeps the same proportion of each class |
| **Supervised learning** | Learning from examples where the correct answer is already known |
| **Target population** | The group of people the model is built for and will be used on |
| **Target** | The column we're predicting (also: label, outcome, y) |
| **Threshold** | The probability cutoff above which we flag a patient as "high risk" |
| **venv** | Virtual environment: a private set of Python packages for one project |
| **Zero-shot** | A model that handles a task without being trained on examples of that task |
