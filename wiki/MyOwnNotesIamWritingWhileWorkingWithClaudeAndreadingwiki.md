**Training:** some models just learn to predict "no" for everyone. Common fixes are **class weights**, which tell the model a missed stroke counts more, and **resampling** techniques. We'll cover these when we build models.

This was quite interesting to me, and I think will be useful to pick up and show that I understand. If a model does learn to predict no for everyone, that might be okay in another scenario or example like a gambling or betting model. but a model where we are trying to predict a stroke, it is important as above, where a missed stroke would be huge for society, and makes sense that class weights, which i don't know yet would be used to make sure a missed stroke counts more to avoid that situation.

Data leakage because of the bmi missing. If the bmi missing from the dataset is because they weren't measured because they were actively having a stroke. We can't really use that data point of an unknown bmi because that is data leakage. we are trying to predict strokes for patients who haven't had a stroke yet. the model would be using a clue that won't exist for the newer patients we are trying to predict.

This was interesting to me. like we could use this to increase the probability of the stroke, but it doesn't really matter. the ranking between the patients wouldn't change much. We can give them a complete ranking for the datasets and then they can decide based on capacity who they reach out to.

## Class weights (an option we didn't use for the baseline)

`LogisticRegression(class_weight="balanced")` tells the model to treat each missed stroke as more costly, to make up for strokes being rare. It pushes predicted probabilities upward, so they stop being realistic risk estimates. The **ranking** of patients barely changes, though, and ranking is what outreach needs. We kept the baseline simple and unweighted, so its probabilities stay realistic.

one hot encoder is done because otherwise if we assinged different work types numbers like 0,1,2,3,
2(home) would be twice the size of 1 (factory) for no real reason and the model just sees numbers

Precision: Of the patients we called, what share were real stroke patients at risk (True positive / (True Positive + False Positive))

Recall: Of the number of stroke patients in total, how many did we reach out to? TP / TP + FN

these top two metrics go against each other, because if we call more patients and reach more stroke patients, recall goes up, but precision goes down I would very much argue, that we should be aiming for highest recall here, and use the full capacity that we have to call as many as possible?
