# HPCC Evaluation metrics for ML algorithms
Evaluation metrics that may be used to monitor the performance of Machine Learning algorithms used by the HPCC production ML bundles.
The code is written in ECL, the language used by the HPCC framework.
# Summary of existing Evaluation Metrics
## General ML Core module
### Classification
#### Class stats
Returns the number and percentage of records that belong to each class in a classification, given the values predicted by the ML model.
#### Confusion matrix
Returns the Confusion Matrix, counting the number of records present for each combination of predicted Class and actual Class, given the actual values and the values predicted by the ML model.
#### Accuracy
Returns the accuracy of the prediction, given the actual values and the values predicted by the ML model.
#### Accuracy by class
Provides the accuracy, recall, and false positive rates, given the actual values and the values predicted by the ML model.
### Regression
#### Accuracy
Returns the R-squared, Mean squared, and Root Mean Sqared error metrics on the data, given the actual values and the values predicted by the ML model.
## Linear regression
### Coefficient evaluation
#### Standard Error (SE)
The standard error is an estimate of the standard deviation of the coefficient. Provides the standard error for each beta coefficient, and each dependent variable in the model. Only meaningful during the training phase.
#### T-statistic
The t statistic is the coefficient divided by its standard error. A larger absolute value of the T-statistic indicates that the coefficient is more significant.
#### P-value
Tests the null hypothesis that a given coefficient is insignificant. A low P-value indicates that the coefficient is significant. A high P-value indicates that the coefficient is insignificant.
#### Confidence Interval
The Confidence Interval determines the interval within which each estimated coefficient must exist to satisfy a given confidence level that is required
### Model evaluation
#### Analysis of Variance (ANOVA)
#### R-squared
#### Adjusted R-squared
#### F-Test
#### Akaike Information Criterion (AIC)
## Logistic regression
## Clustering
## SVM
## NLP
