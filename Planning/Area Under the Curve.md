# Area Under the Receiver Operating Characteristics Curve
This evaluation metric is a single score that is a measure of how well the model is able to distinguish between classes.
## The Receiver Operating Characteristics curve
This curve is a plot of the True Positive Rate versus the False Positive Rate as the threshold value is increased. The threshold value is a value between zero and one that specifies the probability above which an example is classified as positive.
## Area under the curve as a probabitlity
The area under the ROC curve can be used as a measure of how well the model classifies because it represents the probability that a random positive example is ranked higher than a random negative example.

This feature will be used to calculate the value of the metric.
## Design of the implementation
| Item | Values |
| --- | --- |
| Parameters | DATASET(Raw_Prediction) predicted_raw, DATASET(DiscreteField) actual |
| Returned values | {INTEGER wi, INTEGER regressor, REAL8 value} AUC |
### Parameters
DATASET(Raw_Prediction) predicted_raw - The raw predictions (logit scores) obtained from LogitScore
DATASET(DiscreteField) actual - The actual or real values of the fields the model tried to predict
### Return value
{INTEGER wi, INTEGER regressor, REAL8 value} AUC - The area under the curve, per regressor, per work item.
*Alternatively, in view of convenience, the **DATASET(Raw_Prediction)** may be changed to **DATASET(Classify_Result)** and the raw values may be calculated from the prediction and confidence values as an additional step.*
