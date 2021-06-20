# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Summary
This dataset is related to direct marketing campaigns of a banking institution. The task is to predict if the individual will subscribe to the bank service (bank term deposit). It's a classification task.

Two approaches were used to find the solution:
1) A standard Scikit-learn Logistic Regression model whose hyperparameters were optimized using HyperDrive.
2) Using Azure AutoML Approach.

The best performing model was the VotingEnsemble model which was obtained from Azure AutoML approach with an accuracy of 91.69%.

The Scikit-learn Logistic Regression model obtained an accuracy of 91.36%

## Scikit-learn Pipeline

**Explain the pipeline architecture, including data, hyperparameter tuning, and classification algorithm.**

The dataset (Tabular) used was the following:
https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv.

After the dataset is loaded: 
1) It is then cleaned (null values are dropped, few features are one-hot encoded, etc.)
2) It is split into training and test set

A sklearn Logistic Regression model is then used for the above classification task with two parameters to tune:
1) regularization strength (C)
2) maximum number of iterations (max_iter)

Hyperdrive was used to identify the best hyperparameters since it randomly draws parameter from the available options and continues until it hits its early stopping policy or runs out of options.

**What are the benefits of the parameter sampler you chose?**
Random Parameter Sampling was used because it is much less time consuming and the accuracy obtained is comparable to other parameter sampling techniques (like Grid Sampling).

The regularization strength (C) was uniformly chosen between 0 and 1 and maximum number of iterations were randomly selected between 50 and 100.

**What are the benefits of the early stopping policy you chose?**
Bandit Policy was used for early stopping. It is based on evaluation interval and slack factor. Bandit policy only keeps the best performing runs that are within the allowed slack factor and automatically terminate runs that are not producing good results.

## AutoML
**In 1-2 sentences, describe the model and hyperparameters generated by AutoML.**

The following AutoML config was used:

```
automl_config = AutoMLConfig(
  experiment_timeout_minutes=30,
  task='classification',
  primary_metric='accuracy',
  training_data=ds,
  label_column_name='y',
  n_cross_validations=3)
```

The best performing AutoML model was the VotingEnsemble model. 

The ensemble models uses the weighted averages of models already been trained. In current case, the best VotingEnsemble model was weighted average of 10 different models. 

Each of these 10 models were a combination of: 
1) one training algorithm (XGBoostClassifier, LightGBM, Logistic Regression) and 
2) one data transformation technique (Sparse Normalizer, Standard Scaler, Max Abs Scaler). 

## Pipeline comparison
**Compare the two models and their performance. What are the differences in accuracy? In architecture? If there was a difference, why do you think there was one?**

## Future work
**What are some areas of improvement for future experiments? Why might these improvements help the model?**

## Proof of cluster clean up
**If you did not delete your compute cluster in the code, please complete this section. Otherwise, delete this section.**
**Image of cluster marked for deletion**
