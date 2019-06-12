# Silhouette coefficient
Silhouette analysis is a way to measure how close each point in a cluster is to the points in its neighboring clusters as well as in its own cluster. It provides an easy way to find out the optimum value for k during k-means clustering. 

Silhouette values lie in the range of (-1, 1). A value of +1 indicates that the sample point is far away from its neighboring cluster and very close to the cluster it is assigned. Similarly, a value of -1 indicates that the point is closer to its neighboring cluster than to the cluster it is assigned.
## Computation
The Silhouette score for each individual sample is calculated as follows. Let a be the average of the distances of the sample from all other samples in its assigned cluster. Let b be the average of the distances of the sample from all points assigned to the closest cluster to the sample, not assigned to it. Here, the closest cluster is the one that essentially produces the smallest b value. Once these values are computed, the coefficient is given by the formula :

![Sihouette score](https://github.com/suryanarayanan21/HPCC-Evaluation-metrics-for-ML-algorithms/blob/master/Planning/img/silCoeff.svg)
## Design of implementation
| Item | Values |
| --- | --- |
| Parameters | TABLE({UNSIGNED2 wi, UNSIGNED8 id, UNSIGNED8 label}) labels, DATASET(Types.NumericField) samples |
| Returns | {UNSIGNED2 wi, UNSIGNED8 id, REAL8 s} |
### Parameters
TABLE({UNSIGNED2 wi, UNSIGNED8 id, UNSIGNED8 label}) labels - The labels (closest cluster) assigned to the training samples as returned by KMeans.Labels

DATASET(Types.NumericField) samples - The training samples used to train the model, as passed to KMeans.Fit

*These parameters can also be any samples and their corresponding labelling, not necessarily the training samples.*
### Returns
{UNSIGNED2 wi, UNSIGNED8 id, REAL8 s} - The silhouette coefficient per work unit, per cluster.
### Implemantation
The implementation involves three parts.
1. Calculating the 'a' value for all samples
2. Calculating the 'b' value for all samples
3. Calculating the silhouette coefficient
#### Preparation
For convenience, the samples and labels are JOINed to form a single dataset.
~~~
points := JOIN(samples, labels,LEFT.wi = RIGHT.wi and LEFT.id = RIGHT.id);
~~~
#### Part 1 : Calculating a value for all samples
To calculate the a value for all samples, all pairs of points within a cluster are formed, and in the process the square of the distance between their number values for each number (coordinate) is found.

~~~
a1 := JOIN(samples, samples,
           LEFT.wi=RIGHT.wi and 
           LEFT.number=RIGHT.number and 
           LEFT.id <> RIGHT.id and 
           LEFT.label=RIGHT.label,
           TRANSFORM({INTEGER wi, INTEGER id1, INTEGER id2, 
                      INTEGER number, INTEGER label, REAL8 sq_diff},
                     SELF.wi := LEFT.wi,
                     SELF.id1 := LEFT.id,
                     SELF.id2 := RIGHT.id,
                     SELF.number := LEFT.number,
                     SELF.label := LEFT.label,
                     SELF.sq_diff := POWER(LEFT.value-RIGHT.value,2)));
 ~~~
 
 This dataset is then used to calculate the distances between all these points.
 
 ~~~
 a2 := TABLE(a1, {wi,id1,id2,label,dist:=SQRT(SUM(GROUP,sq_diff))},wi,id1,id2,label);
 ~~~
 
 These distances are then averaged to finally get the a values.
 
 ~~~
 a3 := TABLE(a2, {wi, id:=id1, label,value:=AVE(GROUP,dist)}, wi,id1,label);
 ~~~
#### Part 2 : Calculating b value for all samples
To calculate the b value for all samples, all pairs of points which do not belong to the same cluster are formed, and in the process the square of the distance between their values for each number is found.

~~~
b1 := JOIN(samples, samples,
           LEFT.wi=RIGHT.wi and
           LEFT.number=RIGHT.number and
           LEFT.id <> RIGHT.id,
           LEFT.label <> RIGHT.label,
           TRANSFORM({INTEGER wi, INTEGER id1, INTEGER id2, INTEGER Llabel,
                      INTEGER number, INTEGER Rlabel, REAL8 sq_diff},
                     SELF.wi := LEFT.wi,
                     SELF.id1 := LEFT.id,
                     SELF.id2 := RIGHT.id,
                     SELF.number := LEFT.number,
                     SELF.Llabel := LEFT.label,
                     SELF.Rlabel := RIGHT.label,
                     SELF.sq_diff := POWER(LEFT.value-RIGHT.value,2)));
~~~

Now, the distances between these points is calculated.

~~~
b2 := TABLE(b1,
            {wi,id1,id2,Llabel,Rlabel,dist:=SQRT(SUM(GROUP,sq_diff))},
            wi,id1,id2,Llabel,Rlabel);
~~~

Now, the average distance of a sample, from all samples in each cluster is calculated.

~~~
b3 := TABLE(b2,
            {wi,id:=id1,Llabel,Rlabel,avgDist:=AVE(GROUP,dist)},
            wi,id1,Llabel,Rlabel);
~~~

The minimums among these values for every point are the required b values.

~~~
b4 := TABLE(b3,
            {wi,id,label:=Llabel,value:=MIN(GROUP,avgDist)},
            wi,id,Llabel);
~~~
#### Part 3 : Calculating the Silhouette coefficient
To calculate the coefficient, the a and b values are used to find them, first for all samples, and then for each cluster.

~~~
sampleCoeffs := JOIN(a3,b4,
                     LEFT.id=RIGHT.id and LEFT.wi=RIGHT.wi,
                     TRANSFORM({INTEGER wi, INTEGER id, INTEGER label, REAL8 s},
                               SELF.wi := LEFT.wi,
                               SELF.id := LEFT.id,
                               SELF.label := LEFT.label,
                               SELF.value := (RIGHT.value-LEFT.value)/MAX(RIGHT.value,LEFT.value)));
~~~

These sample coefficients are averaged to form the coefficients for the cluster, which is the required result.

~~~
clusterCoeffs := TABLE(sampleCoeffs,{wi,label,s:=AVE(GROUP,value)},wi,label);
~~~
