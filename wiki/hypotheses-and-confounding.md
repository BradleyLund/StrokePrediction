# Hypotheses and Confounding

## Treat every theory as a hypothesis to test
When you spot a pattern, you'll naturally come up with a story to explain it. That's good, because it's how analysts think. But a story is only a **hypothesis** (an idea that might be true) until you check it against the data.

The habit to build:
1. **Notice** a pattern ("missing-BMI patients have 4x the stroke rate")
2. **Hypothesise** ("maybe they're obese and embarrassed to give their weight")
3. **Ask:** if that were true, what *else* would I expect to see in the data?
4. **Check** that with a quick piece of code
5. If the data can't answer it, **write it down as a question for the data owner**

Step 5 is an honest and very professional answer. In a real job you'd email the colleague who extracted the data and ask: *"How was BMI recorded, and why might it be blank?"*

## Confounding
A **confounder** is a hidden third factor that affects *both* things you're comparing, which makes them look related when they might not be.

### Example from our data
- Patients with smoking = "Unknown" have a **low stroke rate** (3.0%).
- Tempting conclusion: "Unknown smokers are low risk."
- But **every single child under 10** (472 of them) has smoking = "Unknown". Nobody records smoking for a 4-year-old.
- Children rarely have strokes. So **age** is the confounder: it drives both "Unknown" *and* the low stroke rate.
- **Check:** look only at adults (18+). "Unknown" adults have a 5.2% stroke rate vs 6.0% for adults with a known status. That's much closer.

```
        Age  ──────►  Smoking = "Unknown"
         │
         └──────────►  Low stroke rate
```

### How to check for confounding
Compare groups **within a slice** where the suspected confounder is held roughly fixed. For example, compare only adults, or only patients aged 60–70. If the pattern mostly disappears, the confounder was doing the work.

## The classic analogy: ice cream and drowning
On days when more ice cream is sold, more people drown. Ice cream doesn't cause drowning. **Hot weather** causes both: more ice cream *and* more swimming. Hot weather is the confounder.

## Prediction vs explanation: what confounding does and doesn't break
This is the subtle part.

**For prediction, confounding is mostly harmless.** The model gets both `age` and `smoking_status`. It sees that "Unknown" patients are low-risk, but it *also* sees they're young, and age already explains that. So "Unknown" ends up with little extra weight of its own. It doesn't hurt the model. It just doesn't add much beyond age.

**For explanation, confounding is dangerous.** If you looked at the raw numbers and told the outreach team *"patients with unknown smoking status are low risk, so skip them"*, you'd be wrong. Unknown *adults* are about as risky as everyone else. The real message is "young patients are low risk".

**Where it can bite a model:** if the confounder (age) were *missing* from the model, "Unknown" would quietly stand in for "young" (it would become a **proxy**). Then it would get weight for the wrong reason, and the model would break if, say, the clinic started recording smoking for children.

So: **keep "Unknown" as a category, keep age in the model, and be careful what you *claim* about it.**

## Why it matters for this project
- It stops you making wrong claims in the presentation (e.g. "not knowing smoking status protects you").
- Age is the biggest stroke risk factor in this dataset, so it will confound almost everything. Remember that when you look at other features too. For example, `ever_married` will look like a risk factor mainly because married people are older.
- The good news: a model that includes age as a feature handles much of this automatically. You still need to be careful about how you **explain** what the model found.
