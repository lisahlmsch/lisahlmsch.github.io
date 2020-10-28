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

### 1. Supervised Learning
Supervised Learning algorithms are trained algorithms using labeled input/output data

#### Classification

### 2. Unsupervised Learning
Unsupervised Learning algorithms are trained algorithms with no labeled data. It attempts at discovering hidden patterns on its own.

#### Clustering

### 3. Reinforcement Learning
Algorithms take actions to maximize cumulative reward.



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
```




