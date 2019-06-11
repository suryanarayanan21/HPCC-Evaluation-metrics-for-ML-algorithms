# F-score
The F-score in statistical analysis of binary classification is a measure of accuracy.
The traditional F-measure or balanced F-score (F1 score) is the harmonic mean of precision and recall:

![equation](https://github.com/suryanarayanan21/HPCC-Evaluation-metrics-for-ML-algorithms/blob/master/Planning/img/f1score.svg)

While precision is the fraction of True positives among all predicted as positive, recall is the fraction of True positives among all the points that are truly (actually) positive.
## Calculation
The F-score, or more specifically the precision and recall, can be obtained from the confusion matrix.

The precision is the ratio of the diagonal element to the row (column) sum, and

The recall is the ratio of the diagonal element to the column (row) sum depending on the orientation of the matrix.
## Design of implementation
| Item | Values |
| --- | --- |
| Parameters | DATASET(DiscreteField) predicted, DATASET(DiscreteField) actual |
| Return value | TABLE({ UNSIGNED2 wi , UNSIGNED4 classifier, REAL8 fscore }) |
### Proposed Location
ML_Core.Analysis.Classification, as it can be used with all classification algorithms and only requires the confusion matrix which is part of the same module.
### Parameters
DATASET(DiscreteField) predicted - Predicted values produced by the model
DATASET(DiscreteField) actual - Actual values of the values to be predicted
### Return value
TABLE({ UNSIGNED2 wi , UNSIGNED4 classifier, REAL8 fscore }) - Table containing the fscore per work item, per classifier
### Dependencies
ML_Core.Types
ML_Core
