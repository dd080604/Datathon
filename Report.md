# Datathon Report
Dylan Day, Natalie Schwartz

## Introduction
This project aimed to develop a robust and interpretable modeling pipeline to predict auto insurance losses using a combination of actuarial techniques and modern machine learning methods. The workflow followed a structured approach beginning with data engineering, followed by exploratory analysis, and finally model selection and evaluation.

## Data ETL

The analysis began by importing, cleaning, and standardizing the raw policy and claims data. Key target variables were engineered, including claim count, claim severity, capped loss amounts, and pure premium. A severity cap at the 99.5th percentile was applied to control extreme outliers common in insurance loss data. The dataset was split into modeling and inference subsets, and exposure adjustments were consistently applied to ensure comparability across policy terms.

## Data Exploration

Exploratory work focused on understanding the behavior of both the target variables and the predictors. Distributions of claim frequency, severity, and total loss were analyzed through descriptive statistics, histograms, and gamma-distribution diagnostics, confirming the heavy-tailed nature of the loss data. Predictors were evaluated through binned frequency, severity, and pure premium plots, and Kendall’s Tau correlations were computed to measure monotonic relationships with the target. Consistency checks between modeling and inference datasets were performed to ensure stability over time and prevent drift. This exploration identified key rating variables driving risk variation and provided direction for variable reduction.

### Variable Reduction

To manage the high number of predictors and reduce redundancy, a two-stage variable reduction procedure was applied. First, tree-based models were used in a screening workflow, where predictors from each data source were evaluated sequentially using variable importance, retaining only the stronger variables and removing low-signal or noisy features. Next, the remaining variables were processed through the VarClusHi clustering algorithm to address multicollinearity. For each cluster, representative variables were selected using RS_Ratio along with permutation importance and Kendall Tau values. This approach resulted in a streamlined, non-redundant set of predictors ready for modeling.

## Model Selection

With the final feature set, several model families were developed and compared. A baseline XGBoost Tweedie model provided strong initial performance. Hyperparameter tuning improved its accuracy and stability, achieving the best overall predictive results. A TabPFN transformer-based model was also tested on a subset due to computational limits. While it was extremely fast and required no training, it underperformed relative to XGBoost on this dataset. In addition, a traditional actuarial composite Poisson-Gamma model where Poisson regression estimates the expected number of claims and Gamma regression estimates the severity of claims. Although interpretable, it delivered lower predictive power and ranking performance compared to XGBoost.

## Model Deployment

After selecting the tuned XGBoost Tweedie model, we deployed it on the unseen inference dataset using a fully consistent preprocessing pipeline. The finalized model was then applied to generate expected loss predictions for each policy. To support business use, predicted losses were converted into standardized risk tiers using expose- weighted binning. Model explainability was incorporated through SHAP global, and local interpretations tools. Global SHAP values confirmed key drivers of loss across the portfolio: vehivle body type (SUV's), driving history score, credit score, vehicle value, and engine power. These results reinforce that risk is primarily driven by vehicle characteristics and behavioral indicators rather than demographic factors. Local SHAP waterfall plots were used to eplain individual predications. For example, Quote ID 55 showed slightly above- average predicted loss due to its SUV bodytype, moderate credit score, high engine power, and higher vehicle value. Customer-level explanations like this help underwriters understand why a quote is assessed as higher or lower risk. 

## Conclusions

Through this structured workflow we have developed a streamlined and effective pipeline for predicting auto insurance losses. Careful data engineering and exploratory analysis ensure a stable foundation, while a disciplined two-stage variable reduction process produces a focused and meaningful feature set. Across all modeling approaches, the tuned XGBoost Tweedie model emerged as the strongest performer in terms of RMSE, MAE, R², and Gini ranking metrics. The composite model provided valuable interpretability and acted as a realistic actuarial baseline, while TabPFN demonstrated the ability to capture some signal despite limited training data. Deployment of the final model on the inference dataset generated reliable policy-level risk estimates supported by transparent SHAP explanations. Overall, the combination of thorough exploration, disciplined variable reduction, and systematic model comparison resulted in a highly effective predictive modeling framework. This solution is suitable for immediate underwriting use or further refinement, calibration, and integration into real world pricing workflows.
