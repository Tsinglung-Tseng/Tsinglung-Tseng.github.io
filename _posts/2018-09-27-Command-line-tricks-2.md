---
title: knowledge base
key: 20180927
tags: 法器集合
---

### 逻辑运算集合图

![](https://cl.ly/c96720801a53/download/283cc23ed0431a3aa1d371d531606748.jpg)

### 对 numpy 数组进行 strip 操作
**2018.09.29 update**

今天在做仿真的时候遇到一个问题，之前把 phantom 的灰度值存进 h5 的时候为了使数组长度相同使用了 pad，在数组后加了数量不一的0或-1. 重建的时候需要把它们再除去。

```python
>>> ds.root.sheppLogans[123][0]
array([  0., 240., 235.,   5., 248., 243., 250., 245., 253., 255.,  -1.,
        -1.,  -1.,  -1.,  -1.,  -1.,  -1.,  -1.,  -1.,  -1.],
      dtype=float32)

>>> def trim(input_array, trim = -1):
>>>     return input_array[~np.in1d(input_array, trim).reshape(input_array.shape)]

>>> trim(ds.root.sheppLogans[123][0], -1)
array([  0., 240., 235.,   5., 248., 243., 250., 245., 253., 255.],
      dtype=float32)
```

### Vectoring python 
**2018.09.20 update**

```python
x, y = np.meshgrid(range(canvas.shape[0]), range(canvas.shape[1]))
mask = (x - p[0])**2 + (y - p[1])**2 <= r**2
canvas[mask] = level
```