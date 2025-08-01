### Predicting Multiple Births from CDC Natality Data

**Ariel Alas**

#### Executive summary
Built and compared six classification pipelines to predict whether a pregnancy will result in twins (or more) using publicly available CDC natality records. Our top model—SMOTE + XGBoost (tuned via GridSearchCV)—achieved 0.96 accuracy, 1.00 precision for multiples, and a weighted F1 of 0.95. This high‐confidence pipeline can help clinical teams allocate extra monitoring resources only when truly needed.


#### Rationale
This project explores the use of machine learning to predict whether a woman is likely to have a multiple birth (twins or more) based on publicly available CDC natality data. The goal is to provide early insights that could support prenatal planning and resource allocation in clinical settings. A baseline logistic regression model was implemented, and initial results show promising recall for identifying multiple births, with room for improving precision in future iterations.

#### Research Question
Can we predict whether a pregnancy will result in a multiple birth using demographic and prenatal factors reported in natality records?

#### Data Sources
 - Data: CDC Natality Public Use Files 2024
 - Link: https://www.cdc.gov/nchs/data_access/vitalstatsonline.htm

#### Methodology
1. Feature extraction: Selected 20 features from the raw fixed-width natality file.
2. Data cleaning:
   - Imputed missing values using median/mode strategies;
   - Applied winsorization (1st–99th percentile capping) to handle outliers.
3. Feature encoding:
   - Mapped binary categorical values (Y, N, U, X) to numeric (1/0)
   - One-hot encoded for a categorical variable
   - Created the binary target
4.	Train-test split: Used train_test_split with stratification to preserve class balance.
5. Feature Selection: Used `SequentialFeatureSelector` to compare top‐k predictors.  
5.	SMOTE: Applied to training set only to oversample the minority class (multiple births).
6.	Modeling: 
      - **Baseline LR** with all features.  
      - **LR + SequentialFeatureSelector**
      - **LR + SFS + SMOTE** for class imbalance.  
      - **RandomForest**, **XGBoost**, and **LightGBM** with FS + SMOTE.  
      - **SMOTE + XGBoost + GridSearchCV** for hyperparameter tuning (n_estimators, max_depth, learning_rate) with 3-fold CV, optimizing F1.

7.	Evaluation:
      - Metrics: **Precision, Recall, F1** for the minority class (multiples), overall **Accuracy**, and confusion matrices.  
      - Chose **F1** as primary metric to balance false positives & false negatives, with clinical cost-benefit in mind.


#### Results
| Model                                 | Acc  | Prec₁ | Rec₁ | F1₁ |  
|---------------------------------------|:----:|:-----:|:----:|:---:|
| Baseline Logistic Regression          | 0.94 | 0.98  | 0.14 | 0.24|
| LR + Feature Selection                | 0.94 | 0.99  | 0.14 | 0.24|
| LR + FS + SMOTE                       | 0.70 | 0.15  | 0.68 | 0.24|
| RF + FS + SMOTE                       | 0.82 | 0.23  | 0.63 | 0.34|
| XGB + FS + SMOTE                      | 0.82 | 0.23  | 0.63 | 0.34|
| LGBM + FS + SMOTE                     | 0.82 | 0.23  | 0.63 | 0.34|
| **SMOTE + XGB + GridSearchCV (final)**| **0.96** |**1.00**|**0.41**|**0.59**|

**Top features** (by gain): `months_since_last_birth`, `prior_live_births`, `weight_gain_category`, etc.  
**Confusion matrix** for final model shows zero false positives and 6,675 true positives.

```
[[212171      0]
 [  9447   6675]]
```

#### Next steps and recommendations
- **Threshold Tuning:** adjust decision threshold to increase recall while maintaining acceptable precision.  
- **Grouping & combining features:** Group key numbers into a few buckets (e.g. turn “months since last birth” into categories like 0–12 months, 13–24 months, 25+ months) so the model sees clear ranges instead of every single value.

#### Notebook

- [https://github.com/AriAlas/ML-AI-Capstone/capstone](https://github.com/AriAlas/ML-AI-Capstone/blob/main/capstone.ipynb)


##### Contact and Further Information
For questions or further collaboration, please contact: arieru@gmail.com
