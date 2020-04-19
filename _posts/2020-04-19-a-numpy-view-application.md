---
layout: post
published: false
title: a numpy view application
date: '2020-04-19'
---
## numpy view

`ndarray.view(dtype)` reinterprets the memory of a `numpy.ndarray`. [Here is the numpy reference page](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.view.html). The way I typically use it is to reinterpret a 2-D array as a 1-D array of tuples. For example, if I have a 2D array and I want to get the **unique rows**, I can't just use [`np.unique`](https://numpy.org/doc/stable/reference/generated/numpy.unique.html) because it flattens multidimensional arrays and gives the unique elements of the resulting 1-D arrays. Providing the _axis_ kwarg just results in `np.unique` being called seperately over elements along the provided axis. In order to find the unique rows of a 2D array, you have to first "convert" the 2D array into a 1D array where each element is a tuple. Then, calling `np.unique` on the resulting array will give you all the unique tuples of the array, after which you can convert it back to a regular 2D array.

```

```

I actually am realizing that I have no idea how this works. Not publishing until I get it