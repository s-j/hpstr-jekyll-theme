---
author: simon.jonassen@gmail.com
comments: true
date: 2018-04-01 00:00:00+01:00
layout: post
title: Understanding LogisticRegression prediction details in Scikit-Learn
tags: [python, programming, scikit-learn, machine learning]
share: true
---

In the following, I briefly show how `coef_`, `intercept_`, `decision_function`, `predict_proba` and `predict` are connected in case of a binary [LogisticRegression](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html) model.

Assume we train a model like this:

```python
>>> ...
>>> lrmodel = linear_model.LogisticRegression(C=0.1, class_weight='balanced')
>>> lrmodel.fit(X_train, y_train)
```

The model's `coef_` attribute will represend learned feature weights (w) and `intercept_` will represent the bias (b). Then the `decision_function` is equivalent to a matrix of x · w + b:

```python
>>> (X_test @ lrmodel.coef_[0].T + lrmodel.intercept_)[:5]
    array([-0.09915005,  0.17611527, -0.14162106, -0.03107271, -0.01813942])

>>> lrmodel.decision_function(X_test)[:5]
    array([-0.09915005,  0.17611527, -0.14162106, -0.03107271, -0.01813942])
```

Now if we take sigmoid of the decision function:

```python
>>> def sigmoid(X): return 1 / (1 + np.exp(-X))

>>> sigmoid(X_test @ lrmodel.coef_[0].T + lrmodel.intercept_)[:5]
    array([ 0.47523277,  0.54391537,  0.46465379,  0.49223245,  0.49546527])

>>> sigmoid(lrmodel.decision_function(X_test))[:5]
    array([ 0.47523277,  0.54391537,  0.46465379,  0.49223245,  0.49546527])
```

it will be equivalent to the output of `predict_proba` (each touple is probabilities for -1 and 1). We see that these numbers are exactly the second column (the positive class) here:

```python
>>> lrmodel.predict_proba(X_test)[:5]
    array([[ 0.52476723,  0.47523277],
           [ 0.45608463,  0.54391537],
           [ 0.53534621,  0.46465379],
           [ 0.50776755,  0.49223245],
           [ 0.50453473,  0.49546527]])
```

Finally, the `predict` function:
```python
>>> lrmodel.predict(X_test)[:5]
    array([-1,  1, -1, -1, -1], dtype=int64)
```

is eqivalent to:
```python
>>> [-1 if np.argmax(p) == 0 else 1 for p in lrmodel.predict_proba(X_test)] [:5]
    [-1, 1, -1, -1, -1]
```

which in our case is:
```python
>>> [1 if p[1] > 0.5 else -1 for p in lrmodel.predict_proba(X_test)] [:5]
    [-1, 1, -1, -1, -1]
```
