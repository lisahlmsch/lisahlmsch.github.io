---
title: "Classification"
date: 2020-04-12
tags: [classification, neural net, lasso, random forest, gbm, h2o, stacked model, keras]
header:
  image: "/images/mnist.png"
excerpt: "classification, neural net, lasso, random forest, gbm, h2o, stacked model, keras"
mathjax: "true"
---

## Image class prediction using deep neural net models
Together with my peer Alexandra we took the “Fashion MNIST dataset” where images of fashion items were to be classified . Images were provided as 28x28 pixel grayscale images. We built deep neural net models using accuracy as a measure of predictive power.

You can [get the html here](/assets/image_class_prediction.html).

## Binary classification
The task of this kaggle competition was to predict which articles are shared the most in social media. I submitted several types of solutions:

Using the caret package, I trained a LASSO model, a random forest model, a gradient boosting machine model and a neural net. Among these random forest had the best results in terms of AUC on the validation set. Using h2o I trained an elastic net model, a random forest, a gradient boosting machine model and a neural net and stacked them using both glm and gbm as a meta learner. The stacked model using glm as a metalearner produced the best results in terms of AUC on the validation set. Among the base models gbm showed the best results, while gbm using caret was rather weak in predicting the target variable.

You can [get the html here](/assets/kaggle_competition.html).
