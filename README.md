# Fraud Detection

## Summary
In this project I am building a predictive model on massive imbalanced dataset of credit card transactions to classify whether a given transaction is fraudulent.

## Description of Dataset
The dataset consists of rows with 22 features, including transaction amount, address of the cardholder, location of the merchant, etc. The training set has >1.4M rows, 7.5K of which are flagged as fraudulent. On the other hand, the test set has 55.5K rows, 2.1K of which are fraudulent. As can be seen, the dataset is highly imbalanced, and without care, the model will bias heavily toward correctly predicting non-fraud data, which is flagging all data as non-fraudulent. The dataset can be accessed in [part 1](data_set.part1.rar) and [part 2](data_set.part2.rar). Due to the limit on the maximum file size, I had to compress them before uploading.

## Goal of the Project
As uncatched fraudulent charges resultin losses for the credit card company, the main goal of this project is to decrease the ratio of uncatched fraudulent charges and all fraudulent ones, which translates to minimizing the **false negative rate**. 

On the other hand, mistakenly flagged transactions inconvenience customers and should be controlled as well, albeit not to the same level as the false negative rate. One good way to quantify the performance is to measure the **precision** of the model (false positive/ (all predicted positive)). 

Given that the dataset is highly imbalanced, we should not expect the precision to be very high. Instead, we examine the **lift** of this model (precision/real positive rate).

## Methodology

### Preparation of Data
We drop following features of the data: *transaction time, name, address, and date of birth of the cardholder*. We also convert the following categorical variables with one-hot encoding: *merchant, category, gender, and job*. After conversion, there are 1213 columns for the training set, and 1195 columns for the test set.

The mismatch between the training set and the test set is due to the difference in the composition of jobs. To remedy this issue, we compare beforehand the possible values of all categorical values between the two sets and take the intersection. The jobs not in both sets will be categorized as *'others'*.

### Addressing the Imbalance of Dataset
As mentioned above, the imbalance will bias the dataset towards the dominant outcome, which is the opposite of our goal. To address this issue, we decided to resample the training set to create a balanced set for model training. Since there are plenty of fraudulent data (7.5K), we take all fraudulent data plus the same amount of non-fraudulent ones sampled randomly from the training set as our balanced set.

After obtaining the balanced dataset, we further split the set randomly into a train-dev split with ratio 9:1. We train our model on the new training set and verify the performance on the development set.

### Evaluation Metrics
The most important metric we look at in this project is the **false negative rate**. Also, by selecting a different probability cutoff, we may be able to obtain a more desirable model. As such, we also examine the **ROC curve** of each model. As an aggregate metric, we look at the **area under the ROC curve (AUC)** as a gauge on the performance of a model.

Note that while the usual ROC curve is plotted with false and true positive rate, we are more interested in the false negative rate. As such, the ROC curve in this project is plotted with (1-tpr) and (1-fpr), which is the false negative rate and specificity.

### Choice of Models
As a baseline model, we selected the logistic regression model. We also considered support vector machine at one point, but the number of rows makes the non-linear SVM rather infeasible, so we ended up not using this model.

The second model we used is the decision tree classifier. By changing the limit on the maximum depth, we can obtain models with varying performance.

The third and final model we used is the random forest classifier. We can tune the model by changing the number of trees and the proportion of predictors to take.

### Batch Testing
The testing set is too big to be tested at the same time. Instead, a custom function is written to take in test data in batches and record the performance at each batch. The confusion matrix, ROC curve, and AUC are outputted at the end.

### Precision Boosting
With the methodology above, we compare each transaction with the whole training set. However, there may be people whose transaction behaviors deviate from the average population systematically. For those customers, it is possible that we will be able to make more accurate classifications by examining their past transactions as well. 

Given a transaction flagged as fraudulent, we look up the past transactions of the same credit card. By calculating the deviation from the past purchases (amount and location), we determine whether or not to re-classify those transactions as non-fraudulent. This procedure enhances the precision at the expense of the false negative rate.

The metric we use is as follows: we compare the embedded distance between the particular purchase and the average of past transactions. If the distance is less than a factor times the standard deviation, we reclassify them as non-fraudulent. Otherwise, we maintain the same decision.

## Results
### Comparison between Models

In terms of the performance of logistic regression, the accuracy is at 87%, and the false negative rate is at 19%. The AUC falls at around .80.

For decision trees, the maximum depth is very relevant in the performance in testing. At maximum depth 20, the accuracy rises to 95%, and the false negative rate is ~10%, with AUC at .93. However, choosing the maximum depth at 8, the accuracy slightly decreases to 93.5%, while the false negative rate plummets to **2.5%**. The AUC for it is at .98.

The random forest performed badly both in training and testing. In training, it only achieves an accuracy of 88%, much worse than the ~100% accuracy for decision trees. In testing, the random forest is heavily inclined towards predicting everything as non-fraud, and the AUC for it is also araound .80.

### Precision Boosting and the Trade-off

By employing the precision boosting algorithm we described above for decision trees with depth 8, depending on the tuning parameter, we can increase the precision at the expense of higher false negative rate. The false negative rate for the original model is ~2.5%, while the lift (precision/positive rate) is around 14.24. With the precision boosting, we can increase the lift to 16.21, but the false negative rate jumps up to 6.8%.

## Discussion
### Explaining the Bad Performance of Random Forest Models
We believe that the reason for the bad performance of random forest models is that there are very few powerful predictors in the feature set. While there are many different jobs and merchants, they do not give much information by themselves. Being drowned in a sea of weak predictors, the random forest models may include a lot of badly performing trees in their committee.

We can see that decision trees are not affected by this, and it is likely due to the fact that decision trees are less affected by the presence of useless predictors.

A way to solve this issue is to perform feature selection beforehand. I thought about performing a linear discriminant analysis to find features that induce high variance in the response, but since decision tree models are already really good, there is no need to go down this path.

### Remarks on the Precision Boosting Algorithm
To make the algorithm simple, we only compared the amount and location of the purchase with past transactions. However, it is possible that the time when it is purchased is also important. If we add in more features, it is possible that we will be able to improve this algorithm.

## Conclusion

The decision tree model is well-suited for the fraud detection problem, achieving very low false negative rate with high precision. With a precision boosting algorithm, we can further leverage between the precision and the false negative rate depending on our specific objectives.
