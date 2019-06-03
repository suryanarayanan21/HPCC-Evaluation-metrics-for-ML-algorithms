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
## Logistic regression
## Clustering
## SVM
## NLP
