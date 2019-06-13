# Chi squared test for feature selection
The Chi squared test for feature selection is statistical measure that helps establish the dependence of two categorical variables. In machine learning, it can be used to determine whether a classifier is dependent on a certain feature, and thus helps in feature selection.
**This test can only be used when both variables are categorical**
## Contingency table
The contingency table represents the number of samples present in the data for each combination of sample category and feature category. To start with the chi2 test, a contingency table of the data is first constructed.
## Chi2 value and Null hypothesis testing
In the Chi2 test, we first assume the null hypothesis that the two variables are independent. We then proceed to construct a contingency table that supports this claim, based off of the sums of rows and columns of our existing contingency table. We then calculate the chi2 value as

![chi2](https://github.com/suryanarayanan21/HPCC-Evaluation-metrics-for-ML-algorithms/blob/master/Planning/img/Chi2.svg)

For a large population, the propability distribution of this value for a random sampling (small subset) of the population is known to approximately follow a chi2 distribution, and hence the name of the coefficient. A large value of chi2 indicates that it is **less probable** that the sample is **not an outlier** and represents the population well.

Hence, when the null hypothesis that the sample is independent of a feature produces a large chi2 value, it indicates that there is a low probability that the null hypothesis is true and hence **null hypothesis is rejected** and the dependence of the two variables is established. A lower chi2 value indicates that it is more probable that the null hypothesis is true. It is not known for certain, however **the null hypothesis cannot be rejected** and the dependence of the variables cannot be established.
## Design of implementation
The implementation consists of two separate functionalities. The first is to create a contingency table of the data, and the second to find the chi2 values.
### Contingency table
| Item | Values |
| --- | --- |
| Parameter | DATASET(DiscreteField) samples, DATASET(DiscreteField) features |
| Returns | TABLE({UNSIGNED2 wi, UNSIGNED4 fnumber, UNSIGNED4 snumber, INTEGER4 fclass, INTEGER4 sclass, UNSIGNED8 cnt}) |
#### Parameters
DATASET(DiscreteField) samples - The classifiers which are categorical (dependent data)

DATASET(DiscreteField) features - The features which are also categorical (independent data)
#### Returns
TABLE({UNSIGNED2 wi, UNSIGNED4 fnumber, UNSIGNED4 snumber, INTEGER4 fclass, INTEGER4 sclass, UNSIGNED8 cnt}) - The contingency tables per work item for each combination of feature (fnumber) and classifier (snumber).
#### Implementation
To obtain the contingency tables, the samples and features are first combined into a single table, with every feature mapped to every classifier.

~~~
combined := JOIN(samples, features,
                 LEFT.wi=RIGHT.wi and LEFT.id=RIGHT.id,
                 TRANSFORM({INTEGER wi, INTEGER id, INTEGER fnumber,
                            INTEGER snumber, INTEGER fclass, INTEGER sclass},
                           SELF.wi := LEFT.wi,
                           SELF.id := LEFT.id,
                           SELF.fnumber := RIGHT.number,
                           SELF.snumber := LEFT.number,
                           SELF.fclass := RIGHT.value,
                           SELF.sclass := LEFT.value));
~~~

This combined data is then grouped using the table functionality to obtain the contingency tables.

~~~
ct := TABLE(combined, {wi,fnumber,snumber,fclass,sclass,cnt:=COUNT(GROUP)},
            wi,fnumber,snumber,fclass,sclass);
~~~
### Chi2
| Item | Values |
| --- | --- |
| Parameter | DATASET(DiscreteField) samples, DATASET(DiscreteField) features |
| Returns | TABLE({UNSIGNED2 wi, UNSIGNED4 fnumber, UNSIGNED4 snumber, REAL8 chi2}) |
#### Parameters
**DATASET(DiscreteField) samples** - The classifiers which are categorical (dependent data)

**DATASET(DiscreteField) features** - The features which are also categorical (independent data)
#### Returns
**TABLE({UNSIGNED2 wi, UNSIGNED4 fnumber, UNSIGNED4 snumber, REAL8 chi2})** - The chi2 value per work item for each combination of feature (fnumber) and classifier (snumber).
#### Implementation
To obtain the chi2 values, the expected contingency table of the data is formed based on the null hypothesis that the data is independent and the existing contingency table of the data (ct).

The expected value for a cell is as follows, where Eij is a cell of the expected contingency table and Oij is a cell of the observed contingency table.

![expected](https://github.com/suryanarayanan21/HPCC-Evaluation-metrics-for-ML-algorithms/blob/master/Planning/img/chi2exp.svg)

To achieve this, the row and column (i.e, the feature and sample) sums, as well as the whole sum, are first calculated from the contingency table.

~~~
featureSums := TABLE(ct, {wi,fnumber,snumber,fclass,cnt:=COUNT(GROUP)},wi,fnumber,snumber,fclass);

sampleSums := TABLE(ct, {wi,fnumber,snumber,sclass,cnt:=COUNT(GROUP)},wi,fnumber,snumber,sclass);

allSum := TABLE(ct, {wi,fnumber,snumber,cnt:=COUNT(GROUP)},wi,fnumber,snumber);
~~~

These are then used to find the expected contingency table ex, using two JOINs

~~~
ex1 := JOIN(featureSums, sampleSums,
            LEFT.wi=RIGHT.wi and LEFT.fnumber=RIGHT.fnumber and LEFT.snumber=RIGHT.snumber,
            TRANSFORM({INTEGER wi, INTEGER fnumber, INTEGER snumber, 
                       INTEGER fclass, INTEGER sclass, REAL8 value},
                      SELF.wi := LEFT.wi,
                      SELF.value := LEFT.cnt * RIGHT.cnt,
                      SELF.fnumber := LEFT.fnumber,
                      SELF.snumber := LEFT.snumber,
                      SELF.fclass := LEFT.fclass,
                      SELF.sclass := RIGHT.sclass));

ex2 := JOIN(ex1, allSum,
            LEFT.wi=RIGHT.wi and LEFT.fnumber=RIGHT.fnumber and LEFT.snumber=RIGHT.snumber,
            TRANSFORM(RECORDOF(ex1),
                      SELF.value := LEFT.value/RIGHT.cnt,
                      SELF := LEFT));
~~~

The two contingency tables are now used to calculate the chi2 values, first for each cell of the table, and then grouped for each table.

Here, the **LEFT OUTER** JOIN flag is used. This is because the contingency table obtained (ct) does not contain entries for combinations where no samples are available (i.e, when cnt = 0). However, the expected contingency table contains entries for all combinations of sample and feature classes due to the nature of its construction. Hence the OUTER condition is used to produce entries for all combinations of sample and feature classes.

chi2 is the required coefficient value for each combination of feature and classifier, per work item.
~~~
chi2_1 := JOIN(ex2, ct,
               LEFT.wi=RIGHT.wi and
               LEFT.fnumber=RIGHT.fnumber and
               LEFT.snumber=RIGHT.snumber and
               LEFT.fclass=RIGHT.fclass and
               LEFT.sclass=RIGHT.sclass,
               TRANSFORM(RECORDOF(ex2),
                         SELF.value := POWER(RIGHT.value-LEFT.value,2)/LEFT.value,
                         SELF := LEFT), LEFT OUTER);

chi2 := TABLE(chi2_1, {wi,fnumber,snumber,x2:=SUM(GROUP,value)},wi,fnumber,snumber);
~~~
