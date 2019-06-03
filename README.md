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
The analysis of variance report provides an analysis of the various sources of variance produced in the predicted dependent data provided. It details the amount of variance explained by the model, and the unexplained variance due to error. The various metrics provided are as follows

Metric | Explanation
------ | -----------
Total_SS | Total Sum of Squares (SS) variance of the dependent data.
Model_SS | The SS variance represented within the model.
Error_SS | The SS variance not reflected by the model.
Total_DF | The total degrees of freedom within the dependent data.
Model_DF | Degrees of freedom of the model.
Error_DF | Degrees of freedom of the error component.
Total_MS | The Mean Square (MS) variance of the dependent data.
Model_MS | MS variance represented within the model.
Error_MS | MS variance not reflected by the model.
Model_F  | The F-Test statistic: Model_MS / Error_MS.

#### R-squared
Statistical measure of how close the data points are to the fitted regression line.
#### Adjusted R-squared
A normalized version of R Squared that does not arbitrarily increase with the number of features
#### F-Test
A measure of the likelihood that all coefficients of the model are insignificant. It indicates whether the regression model created provides a better fit to the data than a model that contains no independent variables.
#### Akaike Information Criterion (AIC)
AIC is an Information Theory based criterion for assessing Goodness of Fit. It is used to create a *ranking* of various models, rather than to provide an absolute measure of quality. A smaller value indicates a better fit.
## Support Vector Machines
### Classification
#### Report (Confusion matrix)
Returns the confusion matrix containing the number of predicted values for each pair of predicted and actual labels.
#### N-Fold cross validation
Cross validates the model across different datasets, each characterised by a different work unit id. The validation metrics used are as follows

Metric |
------ |
Mean squared error |
Number of correct values |
R squared error |

### Regression
#### N-Fold cross validation
## Logistic regression
## General Linear Model
## Learning Trees
## K-Means
## Text Vectors
