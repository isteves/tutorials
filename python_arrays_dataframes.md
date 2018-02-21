Foray into data frames and arrays in Python
================

Since I'm still more or less mono-lingual in R (as far as coding languages go anyhow), trying to understand how arrays and data frames worked in Python :snake: was a bit of a head scratcher.

I started with what I thought was a relatively simple problem: I had a bunch of arrays that I wanted to put together into a dataframe to export as a csv. All except one were arrays with a single dimension. Turns out, I need to work on learning that Python syntax! Parentheses and brackets and colons have very different uses in Python.

At the very least, my problem depended only on two Python packages, `numpy` and `pandas`.

``` python
import numpy as np
import pandas as pd
```

Lesson 1: a list of arrays =/= an array with 2 dimensions
---------------------------------------------------------

This is an array with two dimensions, which you can split into its parts using `vsplit`.

``` python
c = np.array([(5,6,7,8), (9,10,11,12)])
print(c)
```

    ## [[ 5  6  7  8]
    ##  [ 9 10 11 12]]

``` python
c_split = np.vsplit(c, 2)
print(c_split)
```

    ## [array([[5, 6, 7, 8]]), array([[ 9, 10, 11, 12]])]

What that gets you is a list of arrays. If that's what you want from the get-go, you can write it out like this:

``` python
c1 = np.array([5,6,7,8])
c2 = np.array([9,10,11,12])
c = [c1, c2]
print(c)
```

    ## [array([5, 6, 7, 8]), array([ 9, 10, 11, 12])]

Lesson 2: dictionaries are handy for creating data frames
---------------------------------------------------------

Let's say we have two simple arrays, `a` and `b`. If we store the array names in the variable, `var_names`, we can create a dictionary :book: using a neat Python one-liner:

``` python
a = np.array([1,2,3,4])
b = np.array([11,22,33,44])
var_names = ["a", "b"]
var_dict = dict(zip(var_names, [eval(var) for var in var_names]))
```

Within the brackets is a small for-loop/`*apply` to evaluate each variable name stored in `var_names`. Each of these objects is associated with its corresponding `var_names` with `zip()`, and then converted to a dictionary.

Once you have the dictionary, you can simply pass it into `pd.DataFrame`:

``` python
ab_df = pd.DataFrame(var_dict)
print(ab_df)
```

    ##    a   b
    ## 0  1  11
    ## 1  2  22
    ## 2  3  33
    ## 3  4  44

Lesson 3: you can string together functions using "."
-----------------------------------------------------

To turn my list of arrays into a data frame, I wanted to first give them unique names. No need to go overboard with creativity though! Let's just stick to adding a number after "c\_" with a leading zero if it's only one digit. Again, we use the funky Python-bracket-version-of-apply.

``` python
c_names = ["c_" + str(i+1).zfill(2) for i in range(2)]
print(c_names)
```

    ## ['c_01', 'c_02']

With this, we can go ahead with the same dictionary to data frame method from before.

``` python
c_dict = dict(zip(c_names, [c[i] for i in range(2)]))
c_df = pd.DataFrame(c_dict)
print(c_df)
```

    ##    c_01  c_02
    ## 0     5     9
    ## 1     6    10
    ## 2     7    11
    ## 3     8    12

Lesson 4: Python has some great cheatsheets
-------------------------------------------

To merge my two dataframes together, I consulted the [Data Wrangling with pandas](https://github.com/pandas-dev/pandas/blob/master/doc/cheatsheet/Pandas_Cheat_Sheet.pdf) cheatsheet. The visuals make it easy to search for the function you need.

``` python
abc_df = pd.concat([ab_df, c_df], axis = 1)
print(abc_df)
```

    ##    a   b  c_01  c_02
    ## 0  1  11     5     9
    ## 1  2  22     6    10
    ## 2  3  33     7    11
    ## 3  4  44     8    12

That's all, folks! Now to make this work for my real data (all 33 variables x 6.5 million observations)...
