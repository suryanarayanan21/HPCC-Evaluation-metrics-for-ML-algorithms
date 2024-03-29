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

### Proposed location
Logistic regression bundle. (As this metric can only be used when the probability / confidence of the classification is available)
### Parameters
DATASET(Raw_Prediction) predicted_raw - The raw predictions (logit scores) obtained from LogitScore
DATASET(DiscreteField) actual - The actual or real values of the fields the model tried to predict

*Alternatively, in view of convenience, the **DATASET(Raw_Prediction)** may be changed to **DATASET(Classify_Result)** and the raw values may be calculated from the prediction and confidence values as an additional step.*
### Return value
{INTEGER wi, INTEGER regressor, REAL8 value} AUC - The area under the curve, per regressor, per work item.
### Implementation
Since AUC is the probability that a random positive example is ranked higher than a random negative example, one way to calculate it would be to create a set of all positive-negative pairs (per work item, per regressor) and determine what fraction of them are *good* i.e, in how many of them the positive example is marked higher than the negative example.

First, the raw predictions and their true classifications are combined for convenience.

~~~
combined := JOIN(predicted_raw, actual, 
                 LEFT.wi = RIGHT.wi and LEFT.number = RIGHT.number and LEFT.id = RIGHT.id);
~~~

Next, the combined data is JOINed with itself to produce pairs. To eliminate duplicates and pairs of the same item, only positive examples are taken on the left, and only negative examples are taken on the right. This way, a pair *isGood* if the left's value is greater than the right's value. Since the join places a restriction on 'wi' and 'number', these pairs are formed only within the same work units *and* the same regressors.

~~~
pairs := JOIN(combined, combined,
              LEFT.wi = RIGHT.wi and LEFT.number = RIGHT.number and LEFT.raw = 1 and RIGHT.raw = 0,
              TRANSFORM({INTEGER wi, INTEGER regressor, BOOLEAN isGood},
                        SELF.wi = LEFT.wi,
                        SELF.regressor = LEFT.regressor,
                        SELF.isGood = IF(LEFT.value >= RIGHT.value,true,false)));
~~~

Now these values are counted per work unit per regressor, grouped using the table functionality.

~~~
auc := TABLE(pairs,
             {wi, regressor, value := COUNT(GROUP,isGood=true) / COUNT(GROUP)},
             wi, regressor);
~~~
