# Fraud Detection

### Summary
In this project I am building a predictive model on massive imbalanced dataset of credit card transactions to classify whether a given transaction is fraudulent.

### Description of Dataset
The dataset consists of rows with 22 features, including transaction amount, address of the cardholder, location of the merchant, etc. The training set has >1.4M rows, 7.5K of which are flagged as fraudulent. On the other hand, the test set has 55.5K rows, 2.1K of which are fraudulent. As can be seen, the dataset is highly imbalanced, and without care, the model will bias heavily toward correctly predicting non-fraud data, which is flagging all data as non-fraudulent.

### Goal of the Project
As uncatched fraudulent charges resultin losses for the credit card company, the main goal of this project is to decrease the ratio of uncatched fraudulent charges and all fraudulent ones, which translates to minimizing the **false negative rate**. 

On the other hand, mistakenly flagged transactions inconvenience customers and should be controlled as well, albeit not to the same level as the false negative rate. One good way to quantify the performance is to measure the **precision** of the model (false positive/ (all predicted positive)). 

Given that the dataset is highly imbalanced, we should not expect the precision to be very high. Instead, we examine the **lift** of this model (precision/real positive rate).

