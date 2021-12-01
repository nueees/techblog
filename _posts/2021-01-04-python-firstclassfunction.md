---
toc: true
layout: post
description: section4
categories: [python]
title: Python First Class Function
---

Python Intermediate Programming

---

## 4.1. First Class Function
an instance of the Object type  
이미 정의된 여러 함수를 간단히 재활용 가능  

### Python Function 특징
1.런타임 초기화   
2.변수 할당  
3.함수 인수 전달  
4.함수 결과 반환  

```
def factorial(n):
    '''Factorial Function -> n : int'''
    if n == 1: # n < 2
        return 1
    return n * factorial(n-1)

class A:
    pass

print(factorial(5))
> 120

print(factorial.__name__) # 이름
> factorial

print(factorial.__code__) # 코드 위치와 객체 정보
> <code object factorial at 0x00000209FFE98EA0, file "<stdin>", line 1>

print(type(factorial), type(A)) # factorial 함수는 객체 타입이 function이고, A 함수는 객체 타입이 type으로 나옴
> <class 'function'> <class 'type'>
```

### 변수 할당  
```
var_func = factorial
print(factorial, var_func)
> <function factorial at 0x00000209FFDBAC10> <function factorial at 0x00000209FFDBAC10>
print(var_func(5))
> 120
print(list(map(var_func, range(1,6))))
> [1, 2, 6, 24, 120]
```

### 함수 인수 전달 및 함수 결과 반환
고위 함수(Higher-order function):  map, filter, reduce...  

```
print(list(map(var_func, filter(lambda x: x % 2, range(1,6))))) # map함수 첫 인자로 계산할 함수 var_func 전달
> [1, 6, 120]

from functools import reduce
from operator import add
print(reduce(add, range(1,11))) # reduce함수 첫 인자로 계산할 함수 add 전달, 누적
> 55
```

### Callable 확인
```
print(set(sorted(dir(factorial))) - set(sorted(dir(A)))) # function 객체의 variables와 method 중 type 객체꺼 빼서 보면 __call__이 있음 factorial.__call(5) 계산 가능
> {'__globals__', '__name__', '__annotations__', '__call__', '__code__', '__kwdefaults__', '__qualname__', '__get__', '__defaults__', '__closure__'}

print(callable(str), callable(A), callable(var_func), callable(3.14))
> True True True False

from inspect import signature

sg = signature(var_func)

print(sg)
print(sg.parameters)
```
callable: 함수 생성 후 해당 함수가 method 형태로 호출이 가능한지 확인 할 수 있음   
signature: 어떤 파라메터를 받고 좀 더 상세하게 확인 가능한 lib  

### partial
인수를 고정해서 함수 사용 시 편리
```
from operator import add
from functools import partial

print(add(10,10))
> 20
five = partial(add, 5)
print(five(10)) # 5+10
> 15

# 추가
six = partial(five, 6)
print(six()) # 5+6
> 11

print([five(i) for i in range(1,11)])
> [6, 7, 8, 9, 10, 11, 12, 13, 14, 15]
print(list(map(five, range(1,11))))
> [6, 7, 8, 9, 10, 11, 12, 13, 14, 15]
```


---

## 4.2. Closure
자신의 영역 밖에서 호출된 함수의 변수값과 레퍼런스를 복사하고 저장한 뒤, 이 캡처한 값들에 액세스할 수 있게 도와줌  
다른 함수의 지역변수를 그 함수가 종료된 이후에도 기억  
프리변수(free variable): 클로저가 만들어지는 당시의 값과 레퍼런스에 맵핑하여 주는 역할  

### Closure 특징
1. 서버 프로그래밍 -> 동시성(Concurrency)제어 -> 메모리 공간에 여러 자원이 접근 -> 교착상태(Dead Lock)  
2. 메모리를 공유하지 않고 메시지 전달로 처리하기 위한 -> Erlang  
3. 클로저는 공유하되 변경되지 않는(Immutable, Read Only) 적극적으로 사용 -> 함수형 프로그래밍  
4. 클로저는 불변자료구조 및 atom, STM -> 멀티스레드(Coroutine) 프로그래밍에 강점  


```
def outer_func(): # 1
    message = 'Hi' # 3

    def inner_func(): # 4
        print(message) # 6 

    return inner_func() # 5

outer_func() # 2
> Hi
```

5에서 괄호를 지워면 Hi가 출력되지 않음  
```
def outer_func(): # 1
    message = 'Hi' # 3

    def inner_func(): # 4
        print(message) # 6 

    return inner_func # 5

outer_func() # 2
```
inner_func을 실행하지 않고 함수 object를 return함  

```
def outer_func(): # 1
    message = 'Hi' # 3

    def inner_func(): # 4
        print(message)  # 6

    return inner_func # 5

my_func = outer_func() # 2

my_func() # 7
my_func() # 8
my_func() # 9
> Hi
> Hi
> Hi
```


---

## 4.3. Decorator
기존의 코드에 여러가지 기능을 추가하는 구문  
함수를 다른 함수의 인자로 전달한다는 점이 특징  

### Decorator 특징
#### 장점
1. 중복 제거, 코드 간결, 공통 함수 작성  
2. 로깅, 프레임워크, 유효성 체크 -> 공통 기능  
3. 조합해서 사용 용이  

#### 단점
1. 가독성 감소  
2. 특정 기능에 한정된 함수는 -> 단일 함수로 작성하는 것이 유리  
3. 디버깅 불편  



---
