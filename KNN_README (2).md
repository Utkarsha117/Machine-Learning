# K-Nearest Neighbors (KNN)

## 1. Aim
To implement the K-Nearest Neighbors (KNN) algorithm for classification using Python and understand its working.

## 2. Theory
K-Nearest Neighbors (KNN) is a supervised machine learning algorithm used for classification and regression. It predicts the output for a new data point by finding the K nearest data points in the training dataset.

For classification, KNN assigns the class that occurs most frequently among the K nearest neighbors.

The distance between data points is commonly calculated using Euclidean distance.

## 3. Objectives
- To understand the working of the KNN algorithm.
- To train a KNN classification model.
- To predict the class of new data points.
- To evaluate the model using accuracy.

## 4. Software Requirements
- Python
- Jupyter Notebook or VS Code
- NumPy
- Pandas
- Scikit-learn
- Matplotlib

## 5. Algorithm
1. Import the required libraries.
2. Load the dataset.
3. Separate the input features and target variable.
4. Split the dataset into training and testing sets.
5. Standardize the features if required.
6. Create a KNN classifier with a selected value of K.
7. Train the model using the training data.
8. Predict the output for the test data.
9. Calculate the accuracy of the model.

## 6. Python Implementation

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import accuracy_score, classification_report

# Load the dataset
iris = load_iris()
X = iris.data
y = iris.target

# Split the dataset
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

# Standardize the features
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)

# Create and train the KNN model
model = KNeighborsClassifier(n_neighbors=5)
model.fit(X_train, y_train)

# Make predictions
y_pred = model.predict(X_test)

# Display results
print("Actual values:", y_test)
print("Predicted values:", y_pred)
print("Accuracy:", accuracy_score(y_test, y_pred))
print(classification_report(y_test, y_pred,
                            target_names=iris.target_names))
```

## 7. Expected Output
The program displays:
- Actual class labels.
- Predicted class labels.
- Classification accuracy.
- Precision, recall, and F1-score for each class.

The exact results depend on the dataset and train-test split.

## 8. Applications
- Pattern recognition
- Image classification
- Recommendation systems
- Medical diagnosis support
- Customer classification

## 9. Advantages
- Simple and easy to understand.
- No complex training process is required.
- Can be used for classification and regression.
- Works well on small datasets with suitable features.

## 10. Limitations
- Prediction can be slow for large datasets.
- Performance depends on the choice of K.
- Sensitive to feature scaling and irrelevant features.
- Requires sufficient memory to store training data.

## 11. Result
The K-Nearest Neighbors algorithm was implemented using Python and Scikit-learn. The model was trained on the Iris dataset, and its classification performance was evaluated using accuracy and other classification metrics.

## 12. Conclusion
KNN is a simple supervised learning algorithm that predicts a class based on the nearest data points. This experiment demonstrates how KNN can classify Iris flowers using their measured features.
