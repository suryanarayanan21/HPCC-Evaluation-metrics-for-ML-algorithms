# Silhouette coefficient
Silhouette analysis is a way to measure how close each point in a cluster is to the points in its neighboring clusters as well as in its own cluster. It provides an easy way to find out the optimum value for k during k-means clustering. 

Silhouette values lie in the range of (-1, 1). A value of +1 indicates that the sample point is far away from its neighboring cluster and very close to the cluster it is assigned. Similarly, a value of -1 indicates that the point is closer to its neighboring cluster than to the cluster it is assigned.
## Computation
The Silhouette score for each individual sample is calculated as follows. Let a be the average of the distances of the sample from all other samples in its assigned cluster. Let b be the average of the distances of the sample from all points assigned to the closest cluster to the sample, not assigned to it. Here, the closest cluster is the one that essentially produces the smallest b value. Once these values are computed, the coefficient is given by the formula :

![Sihouette score](https://github.com/suryanarayanan21/HPCC-Evaluation-metrics-for-ML-algorithms/blob/master/Planning/img/silCoeff.svg)
## Design of implementation
| Item | Values |
| --- | --- |
| Parameters | DATASET(Types.NumericField) labels, DATASET(Types.NumericField) samples |
| Returns | {UNSIGNED2 wi, UNSIGNED8 id, REAL8 s} |
### Parameters
DATASET(Types.NumericField) labels - The labels (closest cluster) assigned to the training samples as returned by KMeans.Labels

DATASET(Types.NumericField) samples - The training samples used to train the model, as passed to KMeans.Fit
