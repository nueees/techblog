---
toc: true
layout: post
description: section4
categories: [algorithm, python]
title: Binary search
---

Python Algorithm Practice

---

## 4.1. Binary search  
임의의 N개의 숫자가 입력으로 주어집니다. N개의 수를 오름차순으로 정렬한 다음 N개의 수 중 한 개의 수인 M이 주어지면 이분검색으로 M이 정렬된 상태에서 몇 번째에 있는지 구하는 프로그램을 작성하세요. 단 중복값은 존재하지 않습니다.  

```
n, m = map(int, input().split())
a = list(map(int, input().split()))
a.sort()
lt = 0
rt = n-1
while lt <= rt:
    mid=(lt+rt)//2
    if a[mid] == m:
        print(mid+1)
        break
    elif a[mid]>m:
        rt = mid-1
    else:
        lt = mid+1
```


---

## 4.2. Cutting LAN cable
엘리트 학원은 자체적으로 K개의 랜선을 가지고 있다. 그러나 K개의 랜선은 길이가 제각각이다. 
선생님은 랜선을 모두 N개의 같은 길이의 랜선으로 만들고 싶었기 때문에 K개의 랜선을 잘라서 만들어야 한다.
예를 들어 300cm 짜리 랜선에서 140cm 짜리 랜선을 두 개 잘라내면 20cm 은 버려야 한다. (이미 자른 랜선은 붙일 수 없다.)
편의를 위해 랜선을 자를때 손실되는 길이는 없다고 가정하며, 기존의 K개의 랜선으로 N개의 랜선을 만들 수 없는 경우는 없다고 가정하자. 
그리고 자를 때는 항상 센티미터 단위로 정수 길이만큼 자른다고 가정하자. 
N개보다 많이 만드는 것도 N개를 만드는 것에 포함된다. 이때 만들 수 있는 최대 랜선의 길이를 구하는 프로그램을 작성하세요.

```
k, n = map(int,input().split())
line = []
for i in range(k):
    line.append(int(input()))

line.sort()

def count(len):
    cnt = 0
    for x in line:
        cnt += (x//len)
    return cnt


largest = line[k-1] 

lt = 1
rt = largest
while lt <= rt:
    mid = (lt+rt)//2
    if count(mid) >= n:
        res = mid
        lt = mid+1
    else:
        rt = mid-1

print(res)
```


---

## 4.3. DVD recording
가수의 라이브 동영상을 DVD로 만들어 판매하려 한다. 
DVD에는 총 N개의 곡이 들어가는데, DVD에 녹화할 때에는 라이브에서의 순서가 그대로 유지되어야 한다.
즉, 1번 노래와 5번 노래를 같은 DVD에 녹화하기 위해서는 1번과 5번 사이의 모든 노래도 같은 DVD에 녹화해야 한다.
또한 한 노래를 쪼개서 두 개의 DVD에 녹화하면 안된다.
DVD 생산 최소화를 위해 M개가 한 세트인 DVD에 모든 동영상을 녹화하기로 하였다. 
이 때 DVD의 크기(녹화 가능한 길이)를 최소로 하려고 한다. 
그리고 M개의 DVD는 모두 같은 크기여야 제조원가가 적게 들기 때문에 꼭 같은 크기로 해야 한다.

3개의 DVD용량이 17분짜리이면 (1, 2, 3, 4, 5) (6, 7), (8, 9) 이렇게 3개의 DVD로 녹음을 할 수 있다.
17분 용량보다 작은 용량으로는 3개의 DVD에 모든 영상을 녹화할 수 없다.

```
n, m = map(int, input().split())
music = list(map(int, input().split()))
max_music = max(music)

def count(capacity):
    cnt = 1
    sum = 0
    for x in music:
        if sum+x > capacity:
            cnt += 1
            sum = x
        else:
            sum += x
    return cnt

lt=1
rt=sum(music)

while lt <= rt:
    mid = (lt+rt)//2
    if mid > max_music and count(mid) <= m:
        res = mid
        rt = mid-1
    else:
        lt = mid+1
print(res)
```

---

## 4.4. Deciding Stables size
N개의 마구간이 수직선상에 있습니다. 각 마구간은 x1, x2, x3, ......, xN의 좌표를 가지며, 마구간 간에 좌표가 중복되는 일은 없습니다.
각 마구간에는 한 마리의 말만 넣을 수 있고, 가장 가까운 두 말의 거리가 최대가 되게 말을 마구간에 배치하고 싶습니다. 
C마리의 말을 N개의 마구간에 배치했을 때 가장 가까운 두 말의 거리가 최대가 되는 그 최대값을 출력하는 프로그램을 작성하세요.

```
n, c = map(int,input().split())
stall = []
for _ in range(n):
    stall.append(int(input()))

def count(len):
    cnt = 1
    ep = stall[0]

    for i in range(1, n):
        if stall[i] - ep >= len:
            cnt += 1
            ep = stall[i]
    return cnt

stall.sort()
lt = 1
rt = stall[n-1]

while lt <= rt:
    mid = (lt+rt)//2
    if count(mid) >= c:
        res = mid
        lt = mid+1
    else:
        rt = mid-1

print(res)
```

---

## 4.5. Allocating meeting room
한 개의 회의실이 있는데 이를 사용하고자 하는 n개의 회의들에 대하여 회의실 사용표를 만들려고 한다.
각 회의에 대해 시작시간과 끝나는 시간이 주어져 있고, 각 회의가 겹치지 않게 하면서 회의실을 사용할 수 있는 최대수의 회의를 찾아라.
단, 회의는 한번 시작하면 중간에 중단될 수 없으며 한 회의가 끝나는 것과 동시에 다음 회의가 시작될 수 있다.
```

```
---

## 4.6. Selecting Korean wrestling
현수는 씨름 감독입니다. 현수는 씨름 선수를 선발공고를 냈고, N명의 지원자가 지원을 했습니다.
현수는 각 지원자의 키와 몸무게 정보를 알고 있습니다.
현수는 씨름 선수 선발 원칙을 다음과 같이 정했습니다.
“다른 모든 지원자와 일대일 비교하여 키와 몸무게 중 적어도 하나는 크거나, 무거운 지원자만 뽑기로 했습니다.”
만약 A라는 지원자보다 키도 크고 몸무게도 무거운 지원자가 존재한다면 A지원자는 탈락입니다.

```

```
---

## 4.7. 

---

## 4.8. 

---

## 4.9. 

---

## 4.10. 

---
