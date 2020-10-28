---
title: "Machine Learning"
date: 2020-10-22
tags: [python]
header:
  image: "/images/python_impute.png"
excerpt: "Python"
mathjax: "true"
---

# The fundamentals of Machine Learning using Python

What is machine learning?

## AI vs MACHINE LEARNING vs DEEP LEARNING
Artificial intelligence is the science that empowers computers to mimic human intelligence such as decision making, text processing and visual perception. One subfield of AI is Machine Learning that enables machines to improve at a given task with experience. Other subfields include robotics or computer vision. Machine Learning has also various subfields: Supervised Learning, Unsupervised Learning and Reinforcement Learning. Deep Learning is a specialized filed of Machine Learning that relies on training of Deep Artificial Neural Networks (ANN) using a large dataset such as images or texts.

### SUPERVISED LEARNING
Supervised Learning algorithms are trained algorithms using labeled input/output data

#### Regression
Regression models (both linear and non-linear) are used for predicting a real value, like salary for example. Regression technique vary from Linear Regression to SVR and Random Forests Regression.

1. Simple Linear Regression
2. Multiple Linear Regression
3. Polynomial Regression
4. Support Vector for Regression (SVR)
5. Decision Tree Classification
6. Random Forest Classification

#### 1. Simple Linear Regression


#### Classification

### UNSUPERVISED LEARNING
Unsupervised Learning algorithms are trained algorithms with no labeled data. It attempts at discovering hidden patterns on its own.

#### Clustering

### REINFORCEMENT LEARNING
Algorithms take actions to maximize cumulative reward.



## Data preprocessing

Data was preprocessed as follows:

```python
# Import the libraries
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd

# Import dataset
dataset = pd.read_csv('Data.csv')
X = dataset.iloc[:, :-1].values
y = dataset.iloc[;.-1].values
    
# Take care of missing data using imputation
from sklearn.impute import SimpleImputer
imputer = SimpleImputer(missing_values = np.nan, strategy = 'mean')
imputer.fit(X[:, 1:3])
X[:, 1:3] = imputer.transform(X[:,1:3]) 

# Encoding categorical data
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import OneHotEncoder
ct = ColumnTransformer(transformers=[('encoder','OneHotEncoder'(),[0])], remainder='passthrough' 
X = np.array(ct.fit_transform(X))

from sklearn.preprocessing import LabelEncoder
le = LabelEncoder()
y = le.fit_transform(y)
```

For training the machine learning algorithms the dataset was split into a training ((80%) and test set (20%):
```python
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 1)
```

For some of the machine learning models feature scaling was applied in order to avoid that some features are dominated by other features and are not considered by the model.

Feature scaling is a technique that will get the mean and standard deviation and apply two techniques:
1. **Standardisation:** subtracting each value of a specific feature by the mean of all the values of this feature and then dividing by the standard deviation (which is the square root of the variance). This will put all the values of the feature between around minus three and plus three.
2. **Normalisation:** subtracting each value of a specific feauture by the minimum value of this feature and then dividing by the difference between the maximum value and the minimum value of this feature. All the values of the features will become between 0 and 1.

Standardization actually works well all the time, but Normalization is recommended when there is a normal distribution in most of the features.

Feature scaling needs to be applied after the data split, as the test set is supposed to be a brand new data set that the model has never seen before. 
If we were to include the test data in the feature scaling process, the test data would be incorporated in the training set as well and cause information leakage.

Dummy variables do not need to be standardised as they are already within the range of -3 and 3 and it will make interpretation more difficult.

```python
from sklearn.preprocessing import StandardScaler
sc = StandardScaler()
X_train[:,3:] = sc.fit_transform(X_train[:,3:])
X_test[:,3:] = sc.transform(X_test[:,3:])
```

