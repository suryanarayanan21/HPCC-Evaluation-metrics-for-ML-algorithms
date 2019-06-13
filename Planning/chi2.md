# Chi squared test for feature selection
The Chi squared test for feature selection is statistical measure that helps establish the independence of two categorical variables. In machine learning, it can be used to determine whether a classifier is independent of a certain feature, and thus helps in feature selection.
**This test can only be used when both variables are categorical**
## Contingency table
The contingency table represents the number of samples present in the data for each combination of sample category and feature category. To start with the chi2 test, a contingency table of the data is first constructed.
## Chi2 value and Null hypothesis testing
In the Chi2 test, we first assume the null hypothesis that the two variables are independent. We then proceed to construct a contingency table that supports this claim, based off of the sums of rows and columns of our existing contingency table. We then calculate the chi2 value as

