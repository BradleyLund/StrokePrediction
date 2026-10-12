# Data Science Case Study: Stroke Risk Prediction

Thank you for taking the time to work on this case study. This exercise is designed to show us how you approach a modeling problem, the decisions you make along the way, and how you turn model output into a recommendation.

**Please limit yourself to 2-3 hours of work.** We do not expect you to explore everything. A focused, well-reasoned piece of work with a clear list of what you skipped is exactly what we're looking for.

---

## The Scenario

You are a deata scientist at a regional health system. The population health team wants to launch a **stroke prevention outreach program**. Care coordinators will contact patients identified as high risk to schedule a preventative visit, review blood pressure and glucose management, and discuss lifestyle changes.

Care coordinators have limited capacity. The team can only reach out to a small portion of the patient population each month, so they want a model that helps them decide **who to contact first**.

Your job is to build a model that predicts whether a patient will have a stroke, and then recommend how the outreach team should use this prediction.

---

## The Data

Provided in email

id: unique patient identifier
gender: patient gender
age patient age in years
hypertensionn 1 if the patient has hypertension, 0 otherwise
heart_disease 1 if the patient has heart disease, 0 otherwise
ever_married whether the patient has ever been married
work_type type of work (eg. Private, self-employed, govt_job, children, never_worked)
residence_type urban or rural
avg_glucose_level average blood glucose level
bmi body mass index
smoking_status formerly smoked, never smoked, smokes, or unknown.
stroke **Target** 1 if the patient had a stroke, 0 otherwise

Treat this data as you would a real extract handed to you by a colleague. It may contain quality issues, and you should decide for yourself how to handle them.

---

## What We're asking you to do.

1. **Explore and prepare the data.** Understand what's in it, identify any issues, and decide how to handle them.
2. **Build and evaluate one or more classification models.** Start simple. Choose evaluation metrics that make sense for this problem and explain why.
3. **Make decisions based on your model's output.** At minimum, tell us:

- which model you would recommend and why
- How you would choose a threshold or cutoff for flagging patients, and what trade-off that cutoff represents.
- What the outreach team should expect if they use your model (for example, how many true high-risk patients they'd reach for a given number of calls)

4. **Document what you didn't get to.** See the parking lot section below

---

## Time Guardrails

**Total Time: 2-3 Hours.**

Data exploration and cleaning, 30-40 minutes. understand the data, find issues, make and document cleaning decisions.
Baseline model 20-30 min, a simple interpretable model to set a reference point
Improved models and evaluation 45-60 mins One or two stronger approaches, appropriate validation, metric comparison.
Decisions and recommendation 20-30 min Threshold choice, expected impact, recommendation to the outreach team
presentation prep 20-30 min slides summarizing approach, decisions, and parking lot

-** When you notice something interesting but time-consuming, write it down and move on.** It belongs in your parking lot

--

## The parking lot

It's completely fine, and expected, that you won't investigate everything in the time available. Keep a running list of anything you chose not to pursue, and **include it in your presentation**. For each item, briefly note:

-What you would investigate or try
-Why you think it could matter
-Roughly how you would approach it with more time

---

## What to Submit

Please send the following before or immediately after your presentation:

1. **Your code**, as as Jupyter notebook or script(s) in Python. It should run end to end.
2. **Your slides**, in PDF or PowerPoint format.

---

## The presentation

You'll have **20 minutes to present**, followed by 15-20 minutes of questions. Assume your audience includes both technical and non-technical team members

---

## Ground Rules

- you may use any libraries, documentation, or online resources you'd normally use at work.
- If you use AI coding assistants, that's fine, but please be ready to explain every decision in your own words.
- Please don't copy an existing public notebook for this dataset..
  We're interested in your thinking.
  If something in these instructions is unclear, make a reasonable assumption, write it down and keep going, you're also welcome to email with questions.

good luck and we look forward to seeing your work.
