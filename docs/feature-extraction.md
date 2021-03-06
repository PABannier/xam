# Feature extraction

## Bayesian target encoding

Based on [this](https://www.wikiwand.com/en/Additive_smoothing).

```python
>>> import pandas as pd
>>> import xam

>>> X = pd.DataFrame({'x_0': ['a'] * 5 + ['b'] * 5, 'x_1': ['a'] * 9 + ['b'] * 1})
>>> y = pd.Series([1, 1, 1, 1, 0, 1, 0, 0, 0, 0])

>>> encoder = xam.feature_extraction.BayesianTargetEncoder(
...     columns=['x_0', 'x_1'],
...     prior_weight=3,
...     suffix=''
... )
>>> encoder.fit_transform(X, y)
      x_0       x_1
0  0.6875  0.541667
1  0.6875  0.541667
2  0.6875  0.541667
3  0.6875  0.541667
4  0.6875  0.541667
5  0.3125  0.541667
6  0.3125  0.541667
7  0.3125  0.541667
8  0.3125  0.541667
9  0.3125  0.375000

```

## Count encoding

```python
>>> import pandas as pd
>>> import xam

>>> X = pd.DataFrame({'x_0': ['a'] * 5 + ['b'] * 5, 'x_1': ['a'] * 9 + ['b'] * 1})
>>> y = pd.Series([1, 1, 1, 1, 0, 1, 0, 0, 0, 0])

>>> encoder = xam.feature_extraction.CountEncoder(
...     columns=['x_0', 'x_1'],
...     suffix=''
... )
>>> encoder.fit_transform(X, y)
   x_0  x_1
0    5    9
1    5    9
2    5    9
3    5    9
4    5    9
5    5    9
6    5    9
7    5    9
8    5    9
9    5    1

```

## Combining features

```python
>>> import pandas as pd
>>> import xam

>>> df = pd.DataFrame({
...     'col_a': ['a', 'b', 'c'],
...     'col_b': ['d', 'e', 'f'],
...     'col_c': ['g', 'h', 'i'],
... })

>>> xam.feature_extraction.FeatureCombiner(separator='+', orders=[2, 3]).fit_transform(df)  # doctest: +SKIP
  col_a col_b col_c col_a+col_b col_a+col_c col_b+col_c col_a+col_b+col_c
0     a     d     g         a+d         a+g         d+g             a+d+g
1     b     e     h         b+e         b+h         e+h             b+e+h
2     c     f     i         c+f         c+i         f+i             c+f+i

```


## Cyclic features

Day of week, hours, minutes, are cyclic ordinal features; cosine and sine transforms should be used to express the cycle. See [this StackEchange discussion](https://datascience.stackexchange.com/questions/5990/what-is-a-good-way-to-transform-cyclic-ordinal-attributes) and [this r/learnmachinelearning thread answer](https://www.reddit.com/r/learnmachinelearning/comments/9yg4sa/one_hot_encoding_should_i_split_feature_into_as/ea16yln/). This transformer returns an array with twice as many columns as the input array; the first columns are the cosine transforms and the last columns are the sine transforms.

```python
>>> import numpy as np
>>> import xam

>>> times = np.array([
...    np.linspace(0, 23, 4),
...    np.linspace(0, 59, 4),
... ]).T

>>> trans = xam.feature_extraction.CycleTransformer()
>>> trans.fit_transform(times)
array([[ 1.        ,  1.        ,  0.        ,  0.        ],
       [-0.42261826, -0.46947156,  0.90630779,  0.88294759],
       [-0.64278761, -0.5591929 , -0.76604444, -0.82903757],
       [ 0.96592583,  0.9945219 , -0.25881905, -0.10452846]])

```
