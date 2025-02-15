# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Summary
**In 1-2 sentences, explain the problem statement: e.g "This dataset contains data about... we seek to predict..."**

This dataset contains data about customers approached by a bank with a subscription offer. The dataset contains attributes such as age, marital status, and education, and we seek to predict which factors lead to a person to subscribe to the bank.

**In 1-2 sentences, explain the solution: e.g. "The best performing model was a ..."**

The best performing model was an AutoML model that used a VotingEnsemble derived from iterations using LightGBM, XGBoostClassifiers, LogisticRegression, and RandomForest methods. 
## Scikit-learn Pipeline
**Explain the pipeline architecture, including data, hyperparameter tuning, and classification algorithm.**

The Scikit-learn pipeline architecture comprised of taking in the raw data as a CSV file and creating a tabular dataset. From there, the data was cleaned. one-hot encoded, and transformed into a Pandas dataframe. For our algorithm, we chose to use a LogisticRegression algorithm for classification. This algorithm took in two hyperparameters, maximum # of iterations and regularization factor. We ran the algorithm on remote Standard_D2_V2 clusters (max 4 nodes). Before submitting our job, we also split the data into training and test datasets in an 80:20 ratio. 
**What are the benefits of the parameter sampler you chose?**

The RandomSampling parameter sampler allows us to choose discrete and continuous values for our parameter selection at random. In our case, our regularization factor (-C) was chosen by randomly selecting values between 0.1 and 10 uniformly. However, because continuous sampling can provide non-integer values, we have to use the choice selector to randomly select the max number of iterations (-max_iter).

**What are the benefits of the early stopping policy you chose?**

The BanditPolicy allows us to save time and compute resources by using slack criteria to define an early termination policy. We could use this policy to determine if a model was unlikely to give a better score than our current best run's score and terminate it in order to save time and resources. Early termination is especially important with cloud-native machine learning in order to avoid wasting money running jobs that won't change the best model.
## AutoML
**In 1-2 sentences, describe the model and hyperparameters generated by AutoML.**

The model generated by AutoML is a VotingEnsemble using LightGBM, 4 XGBoostClassifiers, LogisticRegression, and RandomForest. The hyperparameters generated were ensembled_iterations (iterations that were ensembled), ensembled_algorithms (algorithms used to build the ensemble), and the ensembled_weights (weights for each component of the ensemble).
## Pipeline comparison
**Compare the two models and their performance. What are the differences in accuracy? In architecture? If there was a difference, why do you think there was one?**

The best AutoML model was a VotingEnsemble (using LightGBM, 4 XGBoostClassifiers, LogisticRegression, and RandomForest) with an accuracy metric of .91869 while the best HyperDrive model had a regularization strength of 6.629 and a maximum of 160 iterations with an accuracy metric of .89529. 
AutoML could tune a wider array of hyperparameters as well as use more algorithms and methods such as the powerful VotingEnsemble. On the other hand, our HyperDrive model was much more constrained, being limited only to LogisticRegression.

## Future work
**What are some areas of improvement for future experiments? Why might these improvements help the model?**

AutoML detected issues with class balancing, in particular the "yes" class was only size 3692, which signaled possible model bias and data imbalance. This runs the risk of a biased model due to the data it was trained and tested on. We could first improve our model's real-world usability by balancing our data.

Furthermore, we were only limited to two hyperparameters during our HyperDrive runs. Experimenting with more hyperparameters as well as more parameter sampling and early termination methods could close the gap between the HyperDrive SK-Learn LogisticRegression classifier and our AutoML classifier. We could also use more algorithms for our HyperDrive runs and see how different algorithms work with our dataset.
