---
toc: true
layout: post
description: section5
categories: [python]
title: Python Concurrency
---

Python Intermediate Programming

---

## 5.1. Generator Pattern
작은 메모리 조각 사용  
단위 실행 가능한 코루틴(Coroutine) 구현과 연동  

### iter function
파이썬 반복 가능한 타입: for, collections, text file, List, Dict, Set, Tuple, unpacking, \*args  

### next

```
class WordSplitGenerator:
    def __init__(self, text):
        self._text = text.split(' ')
    
    def __iter__(self):
        # print('Called __iter__')
        for word in self._text:
           yield word # 제네레이터
        return
    
    def __repr__(self):
        return 'WordSplit(%s)' % (self._text)


wg = WordSplitGenerator('One shot No break')
print(wg)
> WordSplit(['One', 'shot', 'No', 'break'])
wt = iter(wg)
print(wt)
> <generator object WordSplitGenerator.__iter__ at 0x000002142675C200>

print(next(wt)) # 내부적으로 iter 호출
> Called __iter__
> 'One'
print(next(wt)) # iter에서 yield 시점부터 재 호출
> 'shot'
print(next(wt))
> 'No'
print(next(wt))
> 'break'
print(next(wt))
>Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
  StopIteration

```
### genetator 생성

```
def generator_ex1():
    print('Start')
    yield 'A Point.'
    print('continue')
    yield 'B Point.'
    print('End')
```

```
temp2 = [x * 3 for x in generator_ex1()]

print(temp2)
> ['A Point.A Point.A Point.', 'B Point.B Point.B Point.']

for i in temp2:
    print(i)
> A Point.A Point.A Point.
> B Point.B Point.B Point.
```

```
temp3 = (x * 3 for x in generator_ex1())
print(temp3)
> <generator object <genexpr> at 0x000002142685DA50>

for i in temp3:
    print(i)
> Start
> A Point.A Point.A Point.
> continue
> B Point.B Point.B Point.
> End
```

---

## 5.2. Generator Functions

```
import itertools
```

### takewhile
```
gen2 = itertools.takewhile(lambda n : n < 3, [1,2,3,4,5,5,4,3,2,1])
for v in gen2:
    print(v)
> 1
> 2
```

### filter랑 비교   
```
gen1 = filter(lambda n : n < 3, [1,2,3,4,5,5,4,3,2,1])
for v in gen1:
    print(v)
> 1
> 2
> 2
> 1
```

### filterfalse
```
gen3 = itertools.filterfalse(lambda n : n < 3, [1,2,3,4,5,5,4,3,2,1])
for v in gen3:
    print(v)
> 3
> 4
> 5
> 5
> 4
> 3
```

### accumulate
```
gen4 = itertools.accumulate([x for x in range(1, 6)])
for v in gen4:
    print(v)
> 1
> 3
> 6
> 10
> 15
```

### chain
```
gen5 = itertools.chain('ABCDE', range(1,11,2))
print(list(gen5))
> ['A', 'B', 'C', 'D', 'E', 1, 3, 5, 7, 9]
gen6 = itertools.chain(enumerate('ABCDE'))
print(list(gen6))
> [(0, 'A'), (1, 'B'), (2, 'C'), (3, 'D'), (4, 'E')]
```

### product
```
gen7 = itertools.product('ABCDE')
print(list(gen7))
> [('A',), ('B',), ('C',), ('D',), ('E',)]

gen8 = itertools.product('ABCDE', repeat=2) # permutation 5P2 경우의 수 전부 출력
print(list(gen8))
> [('A', 'A'), ('A', 'B'), ('A', 'C'), ('A', 'D'), ('A', 'E'), ('B', 'A'), ('B', 'B'), ('B', 'C'), ('B', 
'D'), ('B', 'E'), ('C', 'A'), ('C', 'B'), ('C', 'C'), ('C', 'D'), ('C', 'E'), ('D', 'A'), ('D', 'B'), ('D', 'C'), ('D', 'D'), ('D', 'E'), ('E', 'A'), ('E', 'B'), ('E', 'C'), ('E', 'D'), ('E', 'E')]
```


### groupby
```
gen9 = itertools.groupby('AAABBCCCCDDEEE')
print(list(gen9))
> [('A', <itertools._grouper object at 0x0000021426987B20>)
, ('B', <itertools._grouper object at 0x0000021426987700>)
, ('C', <itertools._grouper object at 0x0000021426987BB0>)
, ('D', <itertools._grouper object at 0x00000214269879A0>)
, ('E', <itertools._grouper object at 0x0000021426987A90>)]

gen9 = itertools.groupby('AAABBCCCCDDEEE') # 위에 iter 다 돌아서 다시 생성해서 돌려줌
for chr, group in gen9:
    print(chr, ' : ', list(group))
> A  :  ['A', 'A', 'A']
> B  :  ['B', 'B']
> C  :  ['C', 'C', 'C', 'C']
> D  :  ['D', 'D']
> E  :  ['E', 'E', 'E']

```

---

## 5.3. Coroutine


### yield
메인 <-> 서브  

```
from inspect import getgeneratorstate
def coroutine2(x):
    print('>>> coroutine started : {}'.format(x))
    y = yield x
    print('>>> coroutine received : {}'.format(y))
    z = yield x + y
    print('>>> coroutine received : {}'.format(z))

cr3 = coroutine2(10)

print(getgeneratorstate(cr3))
> GEN_CREATED
print(next(cr3))
> coroutine started : 10
> 10
print(getgeneratorstate(cr3))
> GEN_SUSPENDED
print(cr3.send(15))
> coroutine received : 15
> 25

```
---

## 5.4. Futures

### GIL


### map

### wait, as_completed



---


## 5.5. asyncio


### async


### await




---
