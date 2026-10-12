# The Data Science Workflow

Almost every real-world modelling project follows the same broad steps. The take-home instructions are built around them.

```
1. Understand the problem   ->  What decision will this model help someone make?
2. Explore the data (EDA)   ->  What's in it? What's broken? What's interesting?
3. Clean & prepare          ->  Fix or handle the problems, get data into a model-ready shape
4. Baseline model           ->  The simplest reasonable model, to set a reference point
5. Improve & evaluate       ->  Try stronger models, compare them fairly
6. Decide & recommend       ->  Turn model output into an action for real people
7. Communicate              ->  Explain it to technical AND non-technical people
```

Real projects loop back a lot. For example, building a model might show you a data problem you missed, and you go back to step 3.

## Step 1 is the most important (and most often skipped)
Before touching any data, be clear about the **business question**. Here it's:

> Care coordinators can only call a limited number of patients each month. **Who should they call first?**

That matters because it tells us:
- We need to **rank** patients by risk. We don't just need a yes/no answer.
- The model is judged on how many real high-risk patients appear **near the top of the list**.
- Missing a real stroke patient (a false negative) is probably worse than calling someone who's fine (a false positive). A wasted call costs a few minutes. A missed stroke could cost a life.

## Why a "baseline" first?
A baseline is a simple model, like logistic regression, that you build before anything fancy. Without one, you can't tell whether a complicated model is actually better or just more complicated. Interviewers specifically like to see this.

## The "parking lot"
When you spot something interesting that would take too long, write it down and move on. Being disciplined about time is a real-world skill, and it's part of what they're assessing.

## Why it matters for this project
The presentation should follow these steps closely. If you can explain each step and why you did it, you've covered most of what they're looking for.
