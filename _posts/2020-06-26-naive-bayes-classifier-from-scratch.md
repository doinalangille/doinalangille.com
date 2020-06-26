---
layout: post
title: Naive Bayes Classifier From Scratch
subtitle: A better understanding of the algorithm
image: /img/groups.jpg
tags: [Naive Bayes, model, classifier, python]
comments: true
---

The Naive Bayes algorithm is a classification technique based on Bayes Theorem. It assumes that a feature in a class is unrelated to the presence of any other feature. The algorithm relies on the posterior probability of the class given a predictor, as shown in the following formula:

![posterior_proba](http://www.sciweavers.org/tex2img.php?eq=P%28c%7Cx%29%3D%5Cfrac%7BP%28x%7Cc%29%2AP%28c%29%7D%7BP%28x%29%7D&bc=White&fc=Black&im=jpg&fs=12&ff=arev&edit=0)

where:
* P(c\|x) - the posterior probability of class $$c$$ given a predictor $$x$$
* P(x\|c) - the probability of the predictor $$x$$ given the class $$c$$. Also known as Likelihood
* P(c) - the prior probability of the class
* P(x) - the prior probability of predictor.

The Naive Bayes classifier is easy to implement and performs well, even with a small training data set. It is one of the best fast solutions when it comes to predicting the class of the data. [Scikit-learn](https://scikit-learn.org/stable/modules/naive_bayes.html) offers different algorithms for various types of problems. One of them is the **Gaussian Naive Bayes**. It is used when the features are continuous variables, and it assumes that the features follow a Gaussian distribution. It is straightforward to apply the open-source model on data, but a good analyst has to understand how the model is built so that he can use it to the appropriate data.

The best way to understand a model is to build one from scratch. All the following methods are defined in a `GaussianNBClassifier` class. Let's have some fun!  
  

### 1. Instantiate the class

We will use only the `numpy` library for arithmetical operations.

```python
import numpy as np

class GaussianNBClassifier:
    def __init__(self):
        pass
```  
  

### 2. Separate classes

According to the Bayes Theorem, we need to know the class prior probability. To calculate it, we have to assign the feature values to the specific class. We can do this by separating the classes and saving them into a dictionary.

```python
def separate_classes(self, X, y):
    separated_classes = {}
    for i in range(len(X)):
        feature_values = X[i]
        class_name = y[i]
        if class_name not in separated_classes:
            separated_classes[class_name] = []
        separated_classes[class_name].append(feature_values)
    return separated_classes
```  
  

### 3. Summary of features

The likelihood, or the probability of predictor given the class, is assumed to be Gaussian and is calculated based on mean and standard deviation *(see formula on step 4)*. Let's create a summary for each feature in the data set.

```python
def summarize(self, X):
    for feature in zip(*X):
        yield {
            'stdev' : np.std(feature),
            'mean' : np.mean(feature)
        }
```

* The `zip()` function here is an iterator of tuples where the values are paired together for each feature.
* We choose to `yield` because we want to produce a sequence of values over which we will iterate later on, without explicitly saving the sequence in memory.  
  

### 4. Gaussian distribution function

The likelihood for features following a normal distribution is calculated using the Gaussian distribution function:

![likelihood](https://bit.ly/38aClFN)

```python
def gauss_distribution_function(self, x, mean, stdev):
    exponent = np.exp(-((x-mean)**2 / (2*stdev**2)))
    return exponent / (np.sqrt(2*np.pi)*stdev)
```  
  

### 5. Train the model

Training the model means telling the model to learn from a dataset. In the Gaussian Bayes classifier, training involves calculating the mean and standard deviation for each feature of each class. This will allow us to calculate the likelihoods to be used for predictions.

```python
def fit(self, X, y):
    separated_classes = self.separate_classes(X, y)
    self.class_summary = {}
    for class_name, feature_values in separated_classes.items():
        self.class_summary[class_name] = {
            'prior_proba': len(feature_values)/len(X),
            'summary': [i for i in self.summarize(feature_values)],
        }     
    return self.class_summary
```

First, we separate the classes in the training data set. Then, calculate the mean and standard deviation for each class and the prior probability of the class: `len(feature_values)/len(X)`.  
  

### 6. Predict

To predict a class, we have to calculate the posterior probability for each one. The class with the highest posterior probability will be the predicted class. The posterior probability is the joint probability divided by the marginal probability. The marginal probability, or the denominator, is the total joint probability of all classes and will be the same across all classes. We need the class with the highest posterior probability, which means it will be the greatest joint probability.

**Joint probability**

The joint probability is the numerator of the fraction used to calculate the posterior probability. Having multiple features, the joint probability is:

![joint_proba](http://www.sciweavers.org/tex2img.php?eq=P%28y%29%5Cprod_%7Bi%3D1%7D%5E%7Bn%7DP%28x_i%7Cy%29&bc=White&fc=Black&im=jpg&fs=12&ff=arev&edit=0)

```python
joint_proba = {}
for class_name, features in self.class_summary.items():
    total_features = len(features['summary'])
    likelihood = 1
    for idx in range(total_features):
        feature = row[idx]
        mean = features['summary'][idx]['mean']
        stdev = features['summary'][idx]['stdev']
        normal_proba = self.gauss_distribution_function(feature, mean, stdev)
        likelihood *= normal_proba
    prior_proba = features['prior_proba']
    joint_proba[class_name] = prior_proba * likelihood
```

For each class:
* Get the summary (mean, standard deviation, and prior probability)
* Calculate the Normal Probability of each feature
* Get the total likelihood (the product of all normal probabilities)
* Get the joint probability by multiplying the prior probability with the total likelihood.

**Predict the class**

After having the joint probability of each class, we can select the class with the maximum value for the joint probability: `max(joint_proba, key=joint_proba.get)`.

**Put it all together**

Finally, we can predict the class for each row in a test data set, as follows:

```python
def predict(self, X):
    MAPs = []
    for row in X:
        joint_proba = {}
        for class_name, features in self.class_summary.items():
            total_features = len(features['summary'])
            likelihood = 1
            for idx in range(total_features):
                feature = row[idx]
                mean = features['summary'][idx]['mean']
                stdev = features['summary'][idx]['stdev']
                normal_proba = self.gauss_distribution_function(feature, \
                mean, stdev)
                likelihood *= normal_proba
            prior_proba = features['prior_proba']
            joint_proba[class_name] = prior_proba * likelihood
        MAP = max(joint_proba, key=joint_proba.get)
        MAPs.append(MAP)
    return MAPs
```  
  

### 7. Calculate the accuracy

To test the model's performance, we divide the number of correct predictions by the total number of predictions. This is also known as **accuracy**.

```python
def accuracy(self, y_test, y_pred):
    true_true = 0
    for y_t, y_p in zip(y_test, y_pred):
        if y_t == y_p:
            true_true += 1
    return true_true / len(y_test)
```  
  

### GaussianNBClassifier vs. sklearn GaussianNB

Let's use the [UCI Wine data set](https://archive.ics.uci.edu/ml/datasets/Wine) to compare our model's performance with the performance of the GaussianNB model from scikit-learn.

**GaussianNBClassifier**
```python
from GaussianNBClassifier import GaussianNBClassifier

model = GaussianNBClassifier()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)
model.accuracy(y_test, y_pred)
```
Output:
```
GaussianNBClassifier accuracy: 0.972
```  

**Scikit-learn GaussianNB**
```python
from sklearn.naive_bayes import GaussianNB
from sklearn.metrics import accuracy_score

model = GaussianNB()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)
print ("Scikit-learn GaussianNB accuracy: {0:.3f}".format(accuracy_score(y_test, y_pred)))
```
Output:
```
Scikit-learn GaussianNB accuracy: 0.972
```

The accuracy of the models is the same, meaning that we implemented a successful Gaussian Naive Bayes model from scratch.

Find [here](https://github.com/doinalangille/Naive-Bayes-from-scratch) the entire code and the notebook with the comparison of the algorithms.  
  

### References
* [6 Easy Steps to Learn Naive Bayes Algorithm with codes in Python and R](https://www.analyticsvidhya.com/blog/2017/09/naive-bayes-explained/)
* [How The Naive Bayes Classifier Works In Machine Learning](https://dataaspirant.com/2017/02/06/naive-bayes-classifier-machine-learning/)
* [UCI Wine Data Set](https://archive.ics.uci.edu/ml/datasets/Wine)
* [Scikit-learn Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html)