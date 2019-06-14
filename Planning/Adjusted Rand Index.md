# Adjusted Rand Index (ARI)
## Rand Index
The Rand index or Rand measure in statistics and in data clustering, is a measure of the similarity between two data clusterings. The rand index is related to accuracy. The Rand index has a value between 0 and 1, with 0 indicating that the two data clusterings do not agree on any pair of points and 1 indicating that the data clusterings are exactly the same.
## Adjusted Rand Index
The rand index is not corrected for chance, and hence is not able to produce smaller values or neutral values for randomly guessed clusters. This is taken into account in the adjusted Rand Index, which is corrected for chace. The adjusted Rand index can yield negative values if the index is less than the expected index, that is, if the clustering is worse than a random attempt.

The value of Adjusted rand index may be calculated using a contingency table as shown below:

![contingency table](https://wikimedia.org/api/rest_v1/media/math/render/svg/6afaf9b66aa4f2b82c00c3030b17d92b78ab55e3)

From the table, ARI is calculated as:

![ARI](https://wikimedia.org/api/rest_v1/media/math/render/svg/b1850490e5209123ab6e5b905495b4d5f9a1f661)

*\*Images taken from Wikipedia*

## Design of implementation
| Item | Values |
| --- | --- |
| Parameters | DATASET(Cluster_Types.KMeans_Model.Labels) predicted, DATASET(Cluster_Types.KMeans_Model.Labels) true |
| Returns | TABLE({UNSIGNED2 wi, REAL8 ari}) |
### Parameters
**DATASET(Cluster_Types.KMeans_Model.Labels) predicted** - Labels assigned by the model.
**DATASET(Cluster_Types.KMeans_Model.Labels) true** - Actual labels or 'Ground Truth'
### Returns
**TABLE({UNSIGNED2 wi, REAL8 ari})** - The ARI per work unit
### Implementation
First the contingency table of the given data is formed. In order to do this, the data is converted to the DiscreteField format and fed to the function proposed in the [chi2 proposal](https://github.com/suryanarayanan21/HPCC-Evaluation-metrics-for-ML-algorithms/blob/master/Planning/chi2.md).
~~~
conv1 := PROJECT(predicted, TRANSFORM(Types.DiscreteField,
                                      SELF.wi := LEFT.wi,
                                      SELF.number := 1,
                                      SELF.id := LEFT.id,
                                      SELF.value := LEFT.label));

conv2 := PROJECT(true, TRANSFORM(Types.DiscreteField,
                                 SELF.wi := LEFT.wi,
                                 SELF.number := 1,
                                 SELF.id := LEFT.id,
                                 SELF.value := LEFT.label));

ct := contingencyTable(conv1, conv2);
~~~
Next, the three sums required by ARI are calculated.
~~~

rowSumsC2 := TABLE(ct, {wi, fclass, c:=SUM(GROUP,cnt)*(SUM(GROUP,cnt)-1)/2}, wi, fclass);
colSumsC2 := TABLE(ct, {wi, sclass, c:=SUM(GROUP,cnt)*(SUM(GROUP,cnt)-1)/2}, wi, sclass);
allSumC2 := TABLE(ct, {wi, value:=SUM(GROUP,cnt)*(SUM(GROUP,cnt)-1)/2}, wi);

a := TABLE(rowSumsC2, {wi, value:=SUM(GROUP,c)}, wi);
b := TABLE(colSumsC2, {wi, value:=SUM(GROUP,c)}, wi);

ct1 := PROJECT(ct, TRANSFORM(REORDOF(ct),
                             SELF.cnt := LEFT.cnt*(LEFT.cnt-1)/2,
                             SELF := LEFT));

nij := TABLE(ct1(fclass=sclass), {wi, value:=SUM(GROUP,cnt)}, wi);
~~~
Using these three values, ARI is calculated.

ari1 is the required ARI per work item.
~~~
ari := JOIN([a,b,nij,allSumC2],
            LEFT.wi = RIGHT.wi,
            TRANSFORM({INTEGER wi, INTEGER a, INTEGER b,
                       INTEGER nij, INTEGER n, REAL8 value},
                      SELF.wi := LEFT.wi,
                      SELF.a := ROWS(LEFT)[1].value,
                      SELF.b := ROWS(LEFT)[2].value,
                      SELF.nij := ROWS(LEFT)[3].value,
                      SELF.n := ROWS(LEFT)[4].value,
                      SELF.value := (SELF.nij - SELF.a*SELF.b/SELF.n)/
                                    (0.5*(SELF.a + SELF.b) - SELF.a*SELF.b/SELF.n)));

ari1 := TABLE(ari,{wi,value});
~~~
