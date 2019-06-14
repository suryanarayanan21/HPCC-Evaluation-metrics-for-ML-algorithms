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
