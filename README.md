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

### Choice of Models
As a baseline model, we selected the logistic regression model. We also considered support vector machine at one point, but the number of rows makes the non-linear SVM rather infeasible, so we ended up not using this model.

The second model we used is the decision tree classifier. By changing the limit on the maximum depth, we can obtain models with varying performance.

The third and final model we used is the random forest classifier. We can tune the model by changing the number of trees and the proportion of predictors to take.

### Precision Boosting
With the methodology above, we compare each transaction with the whole training set. However, there may be people whose transaction behaviors deviate from the average population systematically. For those customers, it is possible that we will be able to make more accurate classifications by examining their past transactions as well. 

Given a transaction flagged as fraudulent, we look up the past transactions of the same credit card. By calculating the deviation from the past purchases (amount and location), we determine whether or not to re-classify those transactions as non-fraudulent. This procedure enhances the precision at the expense of the false negative rate.

## Results

