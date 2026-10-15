# Lab 2: Written report

## 1. Data and labels

1. **Prediction task:** What does one row of the dataset represent? What are the three classes predicted in this lab, and how are they constructed from the BSA and DTA annotations?


Each row represents one breath from a patient. The label is calculated as bsa + 2 * dta: 0 means non-PVA, 1 means BSA, and 2 means DTA.


2. **Ambiguous labels:** The notebook removes breaths annotated as both BSA and DTA. Why are these rows a problem for the three-class target? Describe one alternative to removing them.


If both BSA and DTA are marked, the label becomes 3, which is outside our three-class target. One alternative is to add a fourth class for breaths with both annotations.


3. **Patient identifiers:** Why are `patient` and `breathid` excluded from the model inputs? Does excluding them guarantee that the model will generalize to patients it has never seen? Explain.


patient and breath_id identify records rather than describe breathing patterns. Removing them does not guarantee good performance on new patients because other features may still contain patient-specific patterns.


## 2. Features and preprocessing

4. **Feature extraction:** Why does the lab extract features from flow and pressure waveforms before training a classifier? Identify two features that capture different aspects of a breath, and explain what each contributes.


Feature extraction summarizes the waveforms into numerical inputs that the classifier can use. For example, i_time measures how long inhalation lasts, while tvi measures how much air is inhaled.


5. **Feature scaling:** Which scaling method did you use? Why should it be fitted on the training data and then applied to the validation data, rather than fitted on both datasets together?


I used MinMaxScaler. It learns each feature’s min and max from the training data and use the same scaling to the validation data. Fitting it on both datasets would leak validation information into preprocessing.


## 3. Model performance

6. **Random forest:** In your own words, how does a random forest combine decision trees to make a prediction? Give one reason it is a reasonable choice for this dataset.


A random forest combines predictions from many decision trees to make its final prediction. It is suitable for this dataset because it can learn nonlinear relationships among breath features.


7. **Class-specific errors:** Examine your random forest's classification report. Which class has the lowest recall, and what kinds of mistakes does that indicate? Why might accuracy alone hide this problem?


DTA has the lowest average recall, about 0.0308. This means the model misses about 97% of actual DTA breaths and predicts them as another class. Accuracy can hide this problem because DTA is a small part of the dataset.


8. **Precision and recall:** For detecting BSA or DTA, what is the difference between a false positive and a false negative? Explain a situation in which you would prioritize recall over precision for one of these classes.


A false positive means predicting BSA or DTA when that specific condition is absent. A false negative means missing a breath that has that condition. I would prioritize recall for DTA screening if missing an event is more costly than reviewing extra false alarms.


9. **Model comparison:** Compare the random forest with the other supervised-learning model you implemented. Which has the higher F1-score? Does the same model perform best for every class? Support your answer with your results.


Random forest has a higher macro F1-score, but it does not perform best for every class.

| F1-score | Random forest, 10-run average | kNN |
|---|---:|---:|
| non-PVA | 0.931 | 0.77 |
| BSA | 0.904 | 0.60 |
| DTA | 0.059 | 0.19 |
| Macro average | 0.631 | 0.52 |

So random forest performs better for non-PVA and BSA, while kNN performs better for DTA. The kNN misclassification rate is about 0.321.


## 4. Model selection and validation

10. **Feature selection:** Compare performance using all available breath features with performance using selected features or the lab's expert-defined rules. Did the reduced feature set improve validation performance? Why might a feature that appears useful on the training set fail to improve validation performance?


Using only tve and ep_auc reduced validation macro F1 from about 0.631 to 0.41, so the reduced feature set did not improve performance. A feature may reflect patterns specific to the training patients, and removing other features may also lose useful information.


11. **Cross-validation:** What does stratification preserve in 5-fold cross-validation? Report your F1-score for each fold and its average. What would substantial differences between folds suggest?


Stratification keeps approximately the same class proportions in each fold.

| Fold | Macro F1-score |
|---|---:|
| 1 | 0.8609 |
| 2 | 0.8309 |
| 3 | 0.8279 |
| 4 | 0.8427 |
| 5 | 0.8522 |
| Average | 0.8429 |

Large differences between folds would suggest that performance depends strongly on which samples are used for training and validation.


12. **Patient-level validation:** If breaths from the same patient appear in both training and validation sets, how might that affect the reported performance? Describe a validation strategy for estimating performance on patients the model has not seen before.


Breaths from the same patient may have similar patterns. Having them in both training and validation sets can make performance look better than it would on new patients. I would split the data by patient, keeping all breaths from each patient entirely in either training or validation.
