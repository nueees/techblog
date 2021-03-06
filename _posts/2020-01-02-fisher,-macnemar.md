---
toc: true
layout: post
description: 5장
categories: [statistics,python]
title: Fisher, Macnemar
---

[hongl blog](https://hongl.tistory.com/105)
[alex blog](https://alex-blog.tistory.com/entry/데이터-분석을-위한-통계분석카이스퀘어-검정-feat-python)
[mansoostat blog](https://mansoostat.tistory.com/39)

---

# 2. Fisher의 직접확률 검정

만약 __사건 발생 수, 빈도 수가 극히 적거나__(특히 5개 이하의 빈도가 전체 셀 중 20%이상 존재하는 경우) __서로의 빈도 수의 차이가 큰 경우__<br>
→ *__카이제곱검정__* 의 정확도가 낮아짐
##### → 피셔의 직접확률 검정(피셔의 정확검정) 사용
---

* 귀무가설 : A의 효과가 없었다.
* 대립가설 : A의 효과가 있었다.


```python
# 패키지 import
import pandas as pd
import numpy as np
import scipy.stats as stats
```


```python
# fake : 가짜약, real : 진짜약, good : 효과 있음, bad : 효과 없음
# 귀무가설 : 약의 효과가 없었다, 대립가설: 약의 효과가 있었다.
data = pd.DataFrame([[1,6], [5,2]], index = ['good','bad'], columns = ['fake','real'])
data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fake</th>
      <th>real</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>good</th>
      <td>1</td>
      <td>6</td>
    </tr>
    <tr>
      <th>bad</th>
      <td>5</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>




```python
odds, pvalue = stats.fisher_exact(data)

print("오즈비 : ", round(odds,3),'\n''p-value : ', round(pvalue,3 ))
```

    오즈비 :  0.067 
    p-value :  0.103
    

결과해석 : p-value가 0.103으로 귀무가설을 채택한다. 즉, 약의 효과가 없었다고 볼 수 있다(약의 유효성 확인x)

<br><br>
---

# 3. Macnemar 검정

짝지어진 자료(paired data, 같은 대상자로부터 두 번 측정한 자료)에서는 그룹 간 값들이 종속적임(독립x)<br>
따라서, 그룹 간 비율을 비교하려면 카이제곱 검정보단 맥네마 검정을 해야함<br>

paired data 예) 인슐린 치료 전후의 당뇨병성 케톤산혈증 발생 여부 조사, 동일 환자의 MRI와 CT로 한 번씩 반복 검사하여 질병 발생 여부 확인

* 귀무가설 : 전후의 차이가 없다.
* 대립가설 : 전후의 차이가 있다.

---


```python
# 필요한 패키지 import
from statsmodels.stats.contingency_tables import mcnemar
import pandas as pd
import numpy as np
```


```python
# BF : 투약 전, AF : 투약 후, 0 : 발작 발생x, 1 : 발작 발생
data = pd.DataFrame([[16, 10], [27, 31]], index = ['BF_0','BF_1'], columns = ['AF_0','AF_1'])
data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>AF_0</th>
      <th>AF_1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>BF_0</th>
      <td>16</td>
      <td>10</td>
    </tr>
    <tr>
      <th>BF_1</th>
      <td>27</td>
      <td>31</td>
    </tr>
  </tbody>
</table>
</div>




```python
# b+c > 25일때
# 데이터 프레임 사용x, 배열 형태여야 함
data = np.array(data)
result = mcnemar(data, exact = False, correction = True)

print('stat : ', round(result.statistic,3),'\n''p-value : ', round(result.pvalue,3))
```

    stat :  6.919 
    p-value :  0.009
    


```python
# b+c < 25일때
result2 = mcnemar(data, exact = True)
print('stat : ', round(result2.statistic,3),'\n''p-value : ', round(result2.pvalue,3))
```

    stat :  10.0 
    p-value :  0.008
    

결과해석 : p-value가 0.009로 귀무가설을 기각한다. 즉, 투약 전후의 발작 발생 비율이 유의하다.



---
