# SwiftDrop Logistics: System Audit & Strategy Report

## Introduction: Understanding the Problem First

SwiftDrop Logistics is facing a problem with its delivery time prediction system. The app sometimes promises delivery in 15 minutes, but the package actually takes 45 minutes. This affects customer trust and creates a poor delivery experience.

As the new AI engineering team, our first responsibility is to understand why the existing model is failing before building a replacement. The previous data scientist has left, so we also need to make the system easier to understand, test, reproduce, and maintain.

## 1. Defining the ML Problem

**Problem type: Regression**

The objective is to predict the estimated delivery time in minutes. Since delivery time is a continuous numerical value, regression is the appropriate machine learning approach.

**Objective metric: Mean Absolute Error (MAE)**

MAE measures the average absolute difference between predicted delivery time and actual delivery time. A lower MAE means predictions are closer to the actual delivery times.

However, the team should also measure how often the model underestimates delivery time and how large those underestimates are. This matters because customers lose trust when the app promises delivery much earlier than it happens.

**Ground truth: Actual delivery duration**

The actual delivery duration should be calculated using reliable timestamps, from order acceptance to confirmed delivery completion. The team must define these timestamps consistently and check for missing or incorrect records.

The model should be evaluated using separate training, validation, and test data. The test data should remain untouched until final evaluation so that the reported performance gives a fair indication of how the model performs on unseen orders.

## 2. Feature Mapping

The following 10 features could help predict delivery time. Each feature must be available when the prediction is made.

| No. | Feature | Data type | Reason |
|---|---|---|---|
| 1 | Route distance | Numerical | Longer distances may increase delivery time. |
| 2 | Order hour | Numerical | Delivery conditions can vary throughout the day. |
| 3 | Day of the week | Categorical | Traffic and demand patterns may differ by day. |
| 4 | Weather condition | Categorical | Conditions such as sunny, rainy, or cloudy may affect delivery. |
| 5 | Rainfall intensity | Numerical | Heavy rainfall can slow down travel. |
| 6 | Traffic level | Categorical | Traffic can be classified as low, medium, or high. |
| 7 | Package weight | Numerical | Heavier packages may affect handling time. |
| 8 | Vehicle type | Categorical | Different vehicles may have different travel and handling constraints. |
| 9 | Number of preceding delivery stops | Numerical | More stops may increase the time before the package reaches the customer. |
| 10 | Estimated pickup preparation time | Numerical | Preparation delays can affect the overall delivery estimate. |

These features are initial candidates, not guaranteed predictors. The team should check data quality, feature availability, and usefulness before training the model. Actual future delivery information must not be used as an input because it would cause data leakage.

## 3. Real-World Noise and Data Anomalies

One important source of real-world noise in Bengaluru is unexpected traffic caused by rain, waterlogging, accidents, or road closures. A route that normally takes 20 minutes may take much longer under these conditions.

Other possible data problems include incorrect GPS locations, missing timestamps, and delays in recording order pickup or delivery completion.

The team should distinguish between genuine unusual deliveries and incorrect records. A delivery that takes much longer because of heavy rain is still a real delivery and should not automatically be removed as an outlier. Incorrect timestamps or GPS errors should be investigated and handled according to documented data-quality rules.

The team should also compare model performance across different conditions, such as rainy and dry days, to identify situations where predictions are less reliable.

## 4. MLflow Tracking Rules

The previous model reportedly worked on a laptop but failed or produced unreasonable estimates in production. This suggests that the team needs stronger experiment tracking, reproducibility, and deployment checks.

The following rules should be established:

**Notebook and code organization**

- Use clear notebook names that describe their purpose instead of names such as `final.ipynb` or `final_final.ipynb`.
- Use notebooks mainly for exploration and initial analysis.
- Move reusable data processing and model training logic into version-controlled Python modules or scripts.
- Test important functions before using them in production.

**Mandatory MLflow experiment tracking**

Every model training run must be logged in MLflow. The team should record:

- Model type and relevant hyperparameters.
- Dataset version, training date, and data split details.
- Random seed and code commit identifier.
- Feature selection, preprocessing steps, and missing-value handling.
- Software dependencies and relevant training configuration.
- Training, validation, and final test MAE.
- Underprediction rate and the average size of underpredictions.
- The trained model, preprocessing artifacts, and relevant evaluation results.

Each run should have a descriptive name and enough information for another engineer to reproduce the experiment. The team should compare new experiments against a simple baseline and the existing model where possible.

**Before production deployment**

The model should pass agreed evaluation thresholds and basic tests before release. The team should verify that training and production use the same feature definitions and preprocessing steps. Predictions should be checked for unreasonable values, and failures should be logged.

After deployment, the team should monitor prediction errors, underestimation, missing inputs, and changes in incoming data. If performance deteriorates, the team should investigate before retraining or replacing the model.

## Conclusion

SwiftDrop should not replace its model without first auditing the problem, data, evaluation process, and deployment setup. A regression model evaluated with MAE, supported by reliable ground truth and carefully selected features, is a reasonable starting point.

Consistent MLflow tracking, clear code organization, testing, and production monitoring will help the new team reproduce results and investigate failures. Most importantly, delivery estimates should become more reliable so that customers receive realistic expectations rather than promises the system cannot keep.op-ml-system-audit
Phase 1 capstone case study: ML system audit and strategy report
