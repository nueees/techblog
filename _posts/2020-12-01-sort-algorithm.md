---
toc: false
layout: post
description: section1
categories: [algorithm, python]
title: Sort Algorithm
---

Python Algorithm Practice

---

```
import random
arr = random.sample(range(1,11),10)
size = len(arr)
k = 1 # search 1st
```


## 1.1. Selection sort
```
for i in range(0,size-1):
    min_idx = i
    for j in range(i+1, size):
        # print(i,j)
        if (arr[j] < arr[min_idx]):
            min_idx = j
    arr[min_idx], arr[i] = arr[i], arr[min_idx]
print(arr[k])
```


---
## 1.2. Bubble sort
```
for end in range(size-1, 0, -1):
    for i in range(end):
        # print(end, i)
        if arr[i] > arr[i+1]:
            arr[i], arr[i+1] = arr[i+1], arr[i]
print(arr[k])
```



---
## 1.3. Insertion sort
```
for end in range(size):
    for i in range(end, 0, -1):
        # print(end, i)
        if arr[i-1] > arr[i]:
            arr[i-1], arr[i] = arr[i], arr[i-1]
print(arr[k])
```



---
