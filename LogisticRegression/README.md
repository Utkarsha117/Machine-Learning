# Logistic Regression

## Overview
This folder contains code for applying Logistic Regression to a classification task.

## Aim
To train a Logistic Regression model and evaluate its ability to predict class labels.

## Theory
Despite its name, Logistic Regression is commonly used for classification. It estimates class probabilities using a logistic function and assigns a class according to a decision threshold. Multiclass classification can also be supported by suitable configurations.

## General Procedure
1. Load the dataset and identify input features and the target class.
2. Preprocess the data and encode labels if necessary.
3. Split the data into training and testing sets.
4. Scale features if appropriate for the model and workflow.
5. Create and train a Logistic Regression model.
6. Predict class labels and, when useful, class probabilities.
7. Evaluate the predictions using classification metrics.

## Evaluation Metrics
- Accuracy
- Precision
- Recall
- F1-score
- Confusion matrix

## Libraries
- Python
- Pandas and NumPy
- Scikit-learn
- Matplotlib or Seaborn, if visualization is used

## Result
Run the program to view predicted classes and the metrics calculated for the dataset.

## Conclusion
Logistic Regression is a widely used classification method that can provide class-probability estimates and serves as a useful baseline for classification problems.
