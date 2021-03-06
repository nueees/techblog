---
toc: true
layout: post
description: 7장
categories: [statistics,python]
title: Nonparametric statistics
---

[python 예시](https://www.statology.org/)

---

# 07_비모수통계 (goodness-of-fit, gomogeneity, independence)
Nonparametric statistics  

## 모수통계 vs 비모수통계 특징

### 모수통계
모집단의 분포에 관해 특별한 가정을 세운 다음 통계적 추론을 시도하는 것을 말함  
1) 추론의 대상이 모평균, 모비율, 모분산 등과 같은 모집단의 특성치, 즉 모수(parameter)라는 점  
2) 추론방법이 비교적 엄격한 가정을 전제로 하고 있다는 점  

### 비모수통계
1) 검정하고자 하는 가설이 모수와 관련된 것이 아닌 경우로서 적합도 검정이나 독립성 또는 동일성 검정, 표본의 무작위성 검정 등이 여기에 속함  
2) 모집단의 분포가 명확하지 않아서 모수 통계방법을 사용하기 곤란할 때로서 모수통계의 대안으로 가정을 전제로 하지 않는 비모수 통계방법을 이용  
3) 자료가 서열(rank)을 나타내는 순위자료이거나 순수한 범주적 자료일 경우   
4) 간편한 방법으로 짧은 시간 내에 검정결과를 알고자 하는 경우  

<br>

## 유형

### 적합도
1) RUN 검정: 표본 배열이 무작위로 구성되어 있는지 검정  
2) Kolmogorov-Smirnov 검정(one sample): 표본의 분포가 가정한 분포를 따르는지 검정  

### 동질성
1) Wilcoxon Signed-Rank Test(부호 순위 검정): 두 paired 표본의 부호와 서열로 분포를 비교 (paired t-test)  
2) Mann-Whitney U 검정: 두 독립표본이 같은 분포를 따르는지 비교 (independent t-test)  
3) Kruskal-Wallis H 검정: 2 집단 이상의 독립표본 집단의 분포 비교 (one-way Anova)  
4) Friedman 검정: 3 집단 이상의 paired 표본 집단의 분포 비교 (two-way Anova)  

### 상관성
1) spearman 서열상관 계수: 서열 또는 비율 척도 자료로 평가대상간의 일치성 검정  
2) Kendall 서열상관 계수: 서열 또는 비율 척도 자료로 평가대상간의 일치성 검정  

<br><br>

## 적합도 goodness-of-fit

### RUN 검정
표본 배열이 무작위로 구성되어 있는지 검정   
hypothesis: H0 (null) is that The data was produced in a random manner   
syntax: runstest_1samp(x, cutoff='mean' or 'median', correction=True)   
result: z-test statistic, p-value  


```python
from statsmodels.sandbox.stats.runs import runstest_1samp 

#create dataset
data = [12, 16, 16, 15, 14, 18, 19, 21, 13, 13]

#Perform Runs test
runstest_1samp(data, correction=False)

# p값 0.50233이므로 0.05보다 크므로 귀무가설 수용, z-test statistic: -0.67082임 
```




    (-0.6708203932499369, 0.5023349543605021)



### Kolmogorov-Smirnov 검정
표본의 분포가 가정한 분포를 따르는지 검정   
hypothesis: H0 (null) is that The data come from a normal distribution    
syntax: one sample은 scipy.stats.kstest(), two sample은 scipy.stats.ks_2samp()  
result: statistic, pvalue  


```python
from numpy.random import seed
from numpy.random import poisson

seed(0)

#generate dataset of 100 values that follow a Poisson distribution with mean=5
data = poisson(5, 100)

from scipy.stats import kstest

#perform Kolmogorov-Smirnov test
kstest(data, 'norm')

# 포아송 분포로 추출했고 p값 1.0908062873170218e-103이므로 귀무가설 기각

```




    KstestResult(statistic=0.9072498680518208, pvalue=1.0908062873170218e-103)




```python
from numpy.random import randn
from numpy.random import lognormal

#generate two datasets
data1 = randn(100)
data2 = lognormal(3, 1, 100)

from scipy.stats import ks_2samp

#perform Kolmogorov-Smirnov test
ks_2samp(data1, data2)

# standard normal이랑 lognormal로 추출했고, pvalue 4.417521386399011e-57이므로 귀무가설 기각
```




    KstestResult(statistic=0.98, pvalue=4.395433779467016e-55)


<br><br>

## 동질성 homogeneity

### Wilcoxon Signed-Rank Test  
두 paired 표본의 부호와 서열로 분포를 비교 (paired t-test)   
두 paired 표본의 차이를 나타내는 분포가 정규분포를 따르지 않을 때, 모평균의 차이가 있는지  
hypothesis: H0 (null) is that there is a no difference in the data between the two groups  
                        혹은  the mean data is equal between the two group  
syntax: wilcoxon(x, y, alternative='two-sided', 'less' or 'greater')  
result: t statistic, pvalue  


```python
# mpg by new fuel treatment
group1 = [20, 23, 21, 25, 18, 17, 18, 24, 20, 24, 23, 19]
group2 = [24, 25, 21, 22, 23, 18, 17, 28, 24, 27, 21, 23]

import scipy.stats as stats

#perform the Wilcoxon-Signed Rank Test
stats.wilcoxon(group1, group2)

# 양측검정으로 p값이 0.044이므로 mpg가 같다는 귀무가설 기각
```

    C:\Users\Administrator\anaconda3\lib\site-packages\scipy\stats\morestats.py:2967: UserWarning: Exact p-value calculation does not work if there are ties. Switching to normal approximation.
      warnings.warn("Exact p-value calculation does not work if there are "
    




    WilcoxonResult(statistic=10.5, pvalue=0.044065400736826854)



### Mann-Whitney U 검정
두 독립표본이 같은 분포를 따르는지 비교 (independent t-test)  
sample 크기가 30보다 작고, 표본분포가 정규분포를 따르지 않을 때, 두 독립표본의 차이가 있는지
hypothesis: H0 (null) is that there is a no difference in the data between the two groups   
syntax: mannwhitneyu(x, y, alternative='two-sided', 'less' or 'greater')  
result: t statistic, pvalue  


```python
# mpg by new fuel treatment
group1 = [20, 23, 21, 25, 18, 17, 18, 24, 20, 24, 23, 19]
group2 = [24, 25, 21, 22, 23, 18, 17, 28, 24, 27, 21, 23]

import scipy.stats as stats

#perform the Mann-Whitney U test
stats.mannwhitneyu(group1, group2, alternative='two-sided')

# 양측검정으로 p값이 0.21138945901258455이므로 mpg가 같다는 귀무가설 기각
```




    MannwhitneyuResult(statistic=50.0, pvalue=0.21138945901258455)



### Kruskal-Wallis H 검정
2 집단 이상의 독립표본 집단의 분포 비교 (one-way Anova)  
세 독립표본의 평균간에 차이가 있는지  
hypothesis: H0 (null) is that the median is equal across all groups  
syntax: stats.kruskal(a,b,c)
result: statistic, pvalue   


```python
# plant measurement
group1 = [7, 14, 14, 13, 12, 9, 6, 14, 12, 8]
group2 = [15, 17, 13, 15, 15, 13, 9, 12, 10, 8]
group3 = [6, 8, 8, 9, 5, 14, 13, 8, 10, 9]

from scipy import stats

#perform Kruskal-Wallis Test 
stats.kruskal(group1, group2, group3)

# p값이 0.043114289703508814이므로 세 plant growth의 중앙값은 다르다
```




    KruskalResult(statistic=6.287801578353988, pvalue=0.043114289703508814)




### Friedman 검정
3 집단 이상의 paired 표본 집단의 분포 비교 (two-way Anova)  
3 집단 이상의 평균이 통계적으로 유의미한 차이가 있는지  
hypothesis: The mean for each population is equal
syntax: stats.friedmanchisquare(a), b, c)
result: statistic, pvalue   


```python
# response times for patient on each of the three drugs
group1 = [4, 6, 3, 4, 3, 2, 2, 7, 6, 5]
group2 = [5, 6, 8, 7, 7, 8, 4, 6, 4, 5]
group3 = [2, 4, 4, 3, 2, 2, 1, 4, 3, 2]

from scipy import stats

#perform Friedman Test
stats.friedmanchisquare(group1, group2, group3)

# p값이 0.0012612201221243594이므로 평균 반응시간이 다른다
```




    FriedmanchisquareResult(statistic=13.351351351351344, pvalue=0.0012612201221243594)


<br><br>


## 상관성 (독립성) independence

서열 또는 비율 척도 자료로 평가대상간의 일치성 검정  
ex) 값이 1,2,3,9999 여도 어차피 1,2,3,4 순위로 판단   

### Spearman Rank Correlation 서열상관 계수
hypothesis: H0 (null) is that correlation coefficient is 0.
syntax: 
result: rho
    -1: a perfect negative relationship between two variables  
    0: no relationship between two variables  
    1: a perfect positive relationship between two variables  
, p-value


```python
# math, science exam score of 10 students
import pandas as pd

#create DataFrame
df = pd.DataFrame({'student': ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J'],
                   'math': [70, 78, 90, 87, 84, 86, 91, 74, 83, 85],
                   'science': [90, 94, 79, 86, 84, 83, 88, 92, 76, 75]})

from scipy.stats import spearmanr

#calculate Spearman Rank correlation and corresponding p-value
rho, p = spearmanr(df['math'], df['science'])

#print Spearman rank correlation and p-value
print(rho)

# -0.41818181818181815이므로 음의 상관관계 존재

print(p)

# p값이 0.22911284098281892이므로 그렇게 significant 하진 않음
```

    -0.41818181818181815
    0.22911284098281892
    

### kendall 서열상관 계수
[kendall_서열상관계수](https://www.geeksforgeeks.org/python-kendall-rank-correlation-coefficient/)
[kendall_추가예제](https://medium.com/@leejukyung/켄달타우-kendalltau-18fb90ba4e7)
spear만과 비교해서 표본이 적고, 동점이 많을 때에는 켄달 타우 계수 사용  

x1 > x2 and y1 > y2 or x1 < x2 and y1 < y2 -> 일치
x1 > x2 and y1 < y2 or x1 < x2 and y1 > y2 -> 불일치


hypothesis: H0 (null) is that correlation coefficient is 0.
syntax: kendalltau(x, y)
result: corr, p-value


```python
from scipy.stats import kendalltau
  
# 권위주의적성격(X)과 지위추구성향(Y)
X = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12] # [x1, x2, x3, ...]
Y = [1, 3, 6, 2, 7, 4, 5, 10, 11, 8, 9, 12] # [y1, y2, y3, ...]


# Calculating Kendall Rank correlation
corr, _ = kendalltau(X, Y)
print('Kendall Rank correlation: %.5f' % corr)

# 0.0.69697이므로 X와 Y의 순위 상관은 비교적 높다고 판단

```

    Kendall Rank correlation: 0.69697
    



---

<br><br>

## 연관성분석 Association analysis
### 특징
- 어떤 상품이 함께 팔리는가를 분석함으로써 경향성을 파악
- 연관규칙(if->then)을 통해 **A 제품을 구매하는 사람이 n% 확률로 B제품을 구매**하는지 확인
- 연관성 규칙의 종류
  - 행동 가능한 규칙: 룰의 의미가 이해되고 실행이 바로 가능한 정보
    ex) 물-라면, 기저귀-맥주
  - 사소한 규칙: 이미 다 알고 있는 규칙으로 활용 관점은 낮지만 신뢰성 재확인 가능
    ex) 삼겹살-상추
  - 설명 불가능한 규칙: 설명되지 않고 실제 행동을 취할 수 없는 규칙
    ex) 허리케인->딸기맛사탕


```python
import warnings
warnings.warn('once')
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```


```python
df_lotto = pd.read_csv('C:/Users/Administrator/Documents/Python/211120_ADP스터디/ADP/data/lotto.csv')
df_lotto.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 859 entries, 0 to 858
    Data columns (total 7 columns):
     #   Column   Non-Null Count  Dtype
    ---  ------   --------------  -----
     0   time_id  859 non-null    int64
     1   num1     859 non-null    int64
     2   num2     859 non-null    int64
     3   num3     859 non-null    int64
     4   num4     859 non-null    int64
     5   num5     859 non-null    int64
     6   num6     859 non-null    int64
    dtypes: int64(7)
    memory usage: 47.1 KB
    


```python
df_lotto.head()
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
      <th>time_id</th>
      <th>num1</th>
      <th>num2</th>
      <th>num3</th>
      <th>num4</th>
      <th>num5</th>
      <th>num6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>859</td>
      <td>8</td>
      <td>22</td>
      <td>35</td>
      <td>38</td>
      <td>39</td>
      <td>41</td>
    </tr>
    <tr>
      <th>1</th>
      <td>858</td>
      <td>9</td>
      <td>13</td>
      <td>32</td>
      <td>38</td>
      <td>39</td>
      <td>43</td>
    </tr>
    <tr>
      <th>2</th>
      <td>857</td>
      <td>6</td>
      <td>10</td>
      <td>16</td>
      <td>28</td>
      <td>34</td>
      <td>38</td>
    </tr>
    <tr>
      <th>3</th>
      <td>856</td>
      <td>10</td>
      <td>24</td>
      <td>40</td>
      <td>41</td>
      <td>43</td>
      <td>44</td>
    </tr>
    <tr>
      <th>4</th>
      <td>855</td>
      <td>8</td>
      <td>15</td>
      <td>17</td>
      <td>19</td>
      <td>43</td>
      <td>44</td>
    </tr>
  </tbody>
</table>
</div>



### Transaction 데이터로 변환하기
1) melt를 사용하는 방법 (ADP 제공 패키지에 없음)


```python
## transaction data로 변환 후 가장 많이 등장한 상위 10개 번호를 막대그래프로 뽑아내고 이에 대해 설명
melt = pd.melt(df_lotto, id_vars = ['time_id'])
melt.head()
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
      <th>time_id</th>
      <th>variable</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>859</td>
      <td>num1</td>
      <td>8</td>
    </tr>
    <tr>
      <th>1</th>
      <td>858</td>
      <td>num1</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2</th>
      <td>857</td>
      <td>num1</td>
      <td>6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>856</td>
      <td>num1</td>
      <td>10</td>
    </tr>
    <tr>
      <th>4</th>
      <td>855</td>
      <td>num1</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
pivot = melt.groupby('value')[['variable']].count().sort_values(by='variable', ascending = False)[:10].reset_index() #groupby시 variable에 count 값이 들어가고 index는 value인 pivot table
pivot.head()
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
      <th>value</th>
      <th>variable</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>34</td>
      <td>134</td>
    </tr>
    <tr>
      <th>1</th>
      <td>40</td>
      <td>126</td>
    </tr>
    <tr>
      <th>2</th>
      <td>27</td>
      <td>126</td>
    </tr>
    <tr>
      <th>3</th>
      <td>43</td>
      <td>125</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20</td>
      <td>124</td>
    </tr>
  </tbody>
</table>
</div>




```python
sns.barplot(x = 'value', y = 'variable', data= pivot, order = pivot['value'])
plt.show()
```


    
![bascket_analysis2]({{site.baseurl}}/images/post/bascket_analysis1.png)
    


2) mlxtend를 사용하는 법(ADP 제공 패키지에 존재)

http://rasbt.github.io/mlxtend/user_guide/preprocessing/TransactionEncoder/


```python
from mlxtend.preprocessing import TransactionEncoder
## mlxtend는 list 속 list형식의 데이터를 One-hot encoding된 numpy array로 변환
list_lotto = df_lotto[df_lotto.columns[1:]].values.tolist() ## num1~num5 column의 value가 row마다 list형식으로 출력, 2d array를 tolist()로 리스트변환
te = TransactionEncoder()
te_ary = te.fit(list_lotto).transform(list_lotto) ##행:구매회차, 열: value(구매물품), 값: Boolean type 인 numpy 2d array
te_lotto = pd.DataFrame(te_ary, columns = te.columns_)

te_lotto.head()
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
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>10</th>
      <th>11</th>
      <th>12</th>
      <th>13</th>
      <th>14</th>
      <th>15</th>
      <th>16</th>
      <th>17</th>
      <th>18</th>
      <th>19</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
      <th>30</th>
      <th>31</th>
      <th>32</th>
      <th>33</th>
      <th>34</th>
      <th>35</th>
      <th>36</th>
      <th>37</th>
      <th>38</th>
      <th>39</th>
      <th>40</th>
      <th>41</th>
      <th>42</th>
      <th>43</th>
      <th>44</th>
      <th>45</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>




```python
freq_lotto = te_lotto.sum(axis = 0).sort_values(ascending = False) ## axis=0이 column 기준으로 모든 row의 값을 더함, pd.Series로 return
sns.barplot(x = freq_lotto.index[:10].tolist(), y = freq_lotto.values[:10].tolist(), order = freq_lotto.index[:10].tolist())
plt.show()
```


    
![bascket_analysis2]({{site.baseurl}}/images/post/bascket_analysis2.png)
    


### 연관규칙(Association Rule) 만들기
- Apriori algorithm이 사용되는 예시
  - 신용카드 사기를 당했을 때 주로 결제되는 내역 패턴
  - 암 발생 시 빈번히 나타나는 DNA 패턴과 단백질 서열 검사
- 빈도 평가 척도
  - 지지도
    - support(X) = n(X) / N
    - support(X,Y) = n(X∩Y) / N
    - 특정 아이템이 발생한 빈도
  - 신뢰도
    - confidence(X→Y) = n(X∩Y) / n(X)
    - 아이템 X를 포함하는 거래 중 아이템 Y도 거래하는 비율
  - 향상도
    - lift(X→Y) = confidence(X→Y) / support(Y)
    - Y 구매가 이루어진 확률 대비 X를 샀을 때 Y도 같이 살 확률
    - lift가 1보다 클수록 +관계, 작을수록 -관계로 우연적 확률에서 멀어짐


```python
## 기출문제) 최소지지도 0.002, 신뢰도 0.8, 최소조합 항목 수 2, 최대조합 항목 수 6 으로 설정한 연관규칙을 향상도 기준으로 내림차순 정렬하여 상위 30개 규칙을 확인
from mlxtend.frequent_patterns import association_rules, apriori
freq_items = apriori(te_lotto, min_support = 0.002, max_len = 6, use_colnames = True)  ## 이 두 개 조건밖에 지정이 안되는 듯?
freq_items  ## tuple 형태의 itemset - support(itemsets) 의 dataframe
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
      <th>support</th>
      <th>itemsets</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.142026</td>
      <td>(1)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.130384</td>
      <td>(2)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.129220</td>
      <td>(3)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.133877</td>
      <td>(4)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.138533</td>
      <td>(5)</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6358</th>
      <td>0.002328</td>
      <td>(40, 43, 13, 14, 26)</td>
    </tr>
    <tr>
      <th>6359</th>
      <td>0.002328</td>
      <td>(14, 15, 18, 21, 26)</td>
    </tr>
    <tr>
      <th>6360</th>
      <td>0.002328</td>
      <td>(40, 14, 27, 30, 31)</td>
    </tr>
    <tr>
      <th>6361</th>
      <td>0.002328</td>
      <td>(34, 44, 15, 19, 21)</td>
    </tr>
    <tr>
      <th>6362</th>
      <td>0.002328</td>
      <td>(36, 43, 16, 26, 31)</td>
    </tr>
  </tbody>
</table>
<p>6363 rows × 2 columns</p>
</div>




```python
freq_items['length'] = freq_items['itemsets'].apply(lambda x:len(x))  ## 조합항목 수 구하기

## 규칙 생성하기
rule1 = association_rules(freq_items, metric = "confidence", min_threshold = 0.8)  ## metric에 추가로 선택할 수 있는 leverage와 conviction은 뭔지 알아봐야 할 듯
rule1.sort_values(by = 'confidence', ascending=False)
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
      <th>antecedents</th>
      <th>consequents</th>
      <th>antecedent support</th>
      <th>consequent support</th>
      <th>support</th>
      <th>confidence</th>
      <th>lift</th>
      <th>leverage</th>
      <th>conviction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>(1, 3, 43)</td>
      <td>(12)</td>
      <td>0.002328</td>
      <td>0.143190</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>6.983740</td>
      <td>0.001995</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>1</th>
      <td>(1, 3, 15)</td>
      <td>(25)</td>
      <td>0.002328</td>
      <td>0.129220</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>7.738739</td>
      <td>0.002027</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>464</th>
      <td>(26, 22, 14)</td>
      <td>(44)</td>
      <td>0.002328</td>
      <td>0.131548</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>7.601770</td>
      <td>0.002022</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>465</th>
      <td>(35, 22, 14)</td>
      <td>(39)</td>
      <td>0.003492</td>
      <td>0.137369</td>
      <td>0.003492</td>
      <td>1.0</td>
      <td>7.279661</td>
      <td>0.003013</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>466</th>
      <td>(14, 22, 39)</td>
      <td>(35)</td>
      <td>0.003492</td>
      <td>0.123399</td>
      <td>0.003492</td>
      <td>1.0</td>
      <td>8.103774</td>
      <td>0.003061</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>236</th>
      <td>(32, 18, 6)</td>
      <td>(31)</td>
      <td>0.002328</td>
      <td>0.137369</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>7.279661</td>
      <td>0.002008</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>237</th>
      <td>(34, 6, 31)</td>
      <td>(18)</td>
      <td>0.002328</td>
      <td>0.140861</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>7.099174</td>
      <td>0.002000</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>238</th>
      <td>(18, 43, 31)</td>
      <td>(6)</td>
      <td>0.002328</td>
      <td>0.125728</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>7.953704</td>
      <td>0.002036</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>239</th>
      <td>(43, 6, 31)</td>
      <td>(18)</td>
      <td>0.002328</td>
      <td>0.140861</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>7.099174</td>
      <td>0.002000</td>
      <td>inf</td>
    </tr>
    <tr>
      <th>703</th>
      <td>(16, 26, 31)</td>
      <td>(43, 36)</td>
      <td>0.002328</td>
      <td>0.012806</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>78.090909</td>
      <td>0.002298</td>
      <td>inf</td>
    </tr>
  </tbody>
</table>
<p>704 rows × 9 columns</p>
</div>




```python
## antecedents의 length를 새로운 데이터 column으로 빼서 그 값이 2와 6 사이인 데이터를 추출함
rule1['antecedents_len'] = rule1['antecedents'].apply(lambda x: len(x))
output = rule1[(rule1['antecedents_len']>=2) & (rule1['antecedents_len']<=6)]
output
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
      <th>antecedents</th>
      <th>consequents</th>
      <th>antecedent support</th>
      <th>consequent support</th>
      <th>support</th>
      <th>confidence</th>
      <th>lift</th>
      <th>leverage</th>
      <th>conviction</th>
      <th>antecedents_len</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>(1, 3, 43)</td>
      <td>(12)</td>
      <td>0.002328</td>
      <td>0.143190</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>6.983740</td>
      <td>0.001995</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>(1, 3, 15)</td>
      <td>(25)</td>
      <td>0.002328</td>
      <td>0.129220</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>7.738739</td>
      <td>0.002027</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>(25, 3, 15)</td>
      <td>(1)</td>
      <td>0.002328</td>
      <td>0.142026</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>7.040984</td>
      <td>0.001998</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>(25, 3, 20)</td>
      <td>(1)</td>
      <td>0.002328</td>
      <td>0.142026</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>7.040984</td>
      <td>0.001998</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>(29, 3, 37)</td>
      <td>(1)</td>
      <td>0.002328</td>
      <td>0.142026</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>7.040984</td>
      <td>0.001998</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>699</th>
      <td>(16, 26, 43, 31)</td>
      <td>(36)</td>
      <td>0.002328</td>
      <td>0.133877</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>7.469565</td>
      <td>0.002017</td>
      <td>inf</td>
      <td>4</td>
    </tr>
    <tr>
      <th>700</th>
      <td>(16, 43, 36)</td>
      <td>(26, 31)</td>
      <td>0.002328</td>
      <td>0.015134</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>66.076923</td>
      <td>0.002293</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>701</th>
      <td>(16, 26, 36)</td>
      <td>(43, 31)</td>
      <td>0.002328</td>
      <td>0.018626</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>53.687500</td>
      <td>0.002285</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>702</th>
      <td>(16, 26, 43)</td>
      <td>(36, 31)</td>
      <td>0.002328</td>
      <td>0.013970</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>71.583333</td>
      <td>0.002296</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>703</th>
      <td>(16, 26, 31)</td>
      <td>(43, 36)</td>
      <td>0.002328</td>
      <td>0.012806</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>78.090909</td>
      <td>0.002298</td>
      <td>inf</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
<p>704 rows × 10 columns</p>
</div>




```python
## 향상도 기준으로 내림차순 정리
output.sort_values(by = 'lift', ascending = False, inplace = True)
output.head(30)
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
      <th>antecedents</th>
      <th>consequents</th>
      <th>antecedent support</th>
      <th>consequent support</th>
      <th>support</th>
      <th>confidence</th>
      <th>lift</th>
      <th>leverage</th>
      <th>conviction</th>
      <th>antecedents_len</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>703</th>
      <td>(16, 26, 31)</td>
      <td>(43, 36)</td>
      <td>0.002328</td>
      <td>0.012806</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>78.090909</td>
      <td>0.002298</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>643</th>
      <td>(24, 34, 22)</td>
      <td>(31, 7)</td>
      <td>0.002328</td>
      <td>0.012806</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>78.090909</td>
      <td>0.002298</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>642</th>
      <td>(34, 31, 7)</td>
      <td>(24, 22)</td>
      <td>0.002328</td>
      <td>0.012806</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>78.090909</td>
      <td>0.002298</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>682</th>
      <td>(26, 21, 14)</td>
      <td>(18, 15)</td>
      <td>0.002328</td>
      <td>0.013970</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>71.583333</td>
      <td>0.002296</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>652</th>
      <td>(34, 10, 36)</td>
      <td>(44, 22)</td>
      <td>0.002328</td>
      <td>0.013970</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>71.583333</td>
      <td>0.002296</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>646</th>
      <td>(24, 22, 31)</td>
      <td>(34, 7)</td>
      <td>0.002328</td>
      <td>0.013970</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>71.583333</td>
      <td>0.002296</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>666</th>
      <td>(24, 20, 15)</td>
      <td>(12, 30)</td>
      <td>0.002328</td>
      <td>0.013970</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>71.583333</td>
      <td>0.002296</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>702</th>
      <td>(16, 26, 43)</td>
      <td>(36, 31)</td>
      <td>0.002328</td>
      <td>0.013970</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>71.583333</td>
      <td>0.002296</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>700</th>
      <td>(16, 43, 36)</td>
      <td>(26, 31)</td>
      <td>0.002328</td>
      <td>0.015134</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>66.076923</td>
      <td>0.002293</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>653</th>
      <td>(34, 10, 22)</td>
      <td>(36, 44)</td>
      <td>0.002328</td>
      <td>0.016298</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>61.357143</td>
      <td>0.002290</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>658</th>
      <td>(26, 11, 36)</td>
      <td>(17, 21)</td>
      <td>0.002328</td>
      <td>0.016298</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>61.357143</td>
      <td>0.002290</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>645</th>
      <td>(24, 31, 7)</td>
      <td>(34, 22)</td>
      <td>0.002328</td>
      <td>0.017462</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>57.266667</td>
      <td>0.002288</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>676</th>
      <td>(26, 43, 13)</td>
      <td>(40, 14)</td>
      <td>0.002328</td>
      <td>0.017462</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>57.266667</td>
      <td>0.002288</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>675</th>
      <td>(40, 26, 13)</td>
      <td>(43, 14)</td>
      <td>0.002328</td>
      <td>0.017462</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>57.266667</td>
      <td>0.002288</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>636</th>
      <td>(45, 6, 31)</td>
      <td>(18, 38)</td>
      <td>0.002328</td>
      <td>0.017462</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>57.266667</td>
      <td>0.002288</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>635</th>
      <td>(18, 45, 6)</td>
      <td>(38, 31)</td>
      <td>0.002328</td>
      <td>0.017462</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>57.266667</td>
      <td>0.002288</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>664</th>
      <td>(12, 20, 15)</td>
      <td>(24, 30)</td>
      <td>0.002328</td>
      <td>0.017462</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>57.266667</td>
      <td>0.002288</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>644</th>
      <td>(31, 22, 7)</td>
      <td>(24, 34)</td>
      <td>0.002328</td>
      <td>0.017462</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>57.266667</td>
      <td>0.002288</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>701</th>
      <td>(16, 26, 36)</td>
      <td>(43, 31)</td>
      <td>0.002328</td>
      <td>0.018626</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>53.687500</td>
      <td>0.002285</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>688</th>
      <td>(40, 14, 30)</td>
      <td>(27, 31)</td>
      <td>0.002328</td>
      <td>0.018626</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>53.687500</td>
      <td>0.002285</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>665</th>
      <td>(12, 30, 15)</td>
      <td>(24, 20)</td>
      <td>0.002328</td>
      <td>0.020955</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>47.722222</td>
      <td>0.002280</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>674</th>
      <td>(40, 13, 14)</td>
      <td>(26, 43)</td>
      <td>0.002328</td>
      <td>0.022119</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>45.210526</td>
      <td>0.002277</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>694</th>
      <td>(19, 44, 21)</td>
      <td>(34, 15)</td>
      <td>0.002328</td>
      <td>0.024447</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>40.904762</td>
      <td>0.002271</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>673</th>
      <td>(40, 43, 13)</td>
      <td>(26, 14)</td>
      <td>0.002328</td>
      <td>0.025611</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>39.045455</td>
      <td>0.002269</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>667</th>
      <td>(20, 30, 15)</td>
      <td>(24, 12)</td>
      <td>0.002328</td>
      <td>0.027939</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>35.791667</td>
      <td>0.002263</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>324</th>
      <td>(17, 14, 33)</td>
      <td>(9)</td>
      <td>0.002328</td>
      <td>0.103609</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>9.651685</td>
      <td>0.002087</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>330</th>
      <td>(18, 35, 23)</td>
      <td>(9)</td>
      <td>0.002328</td>
      <td>0.103609</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>9.651685</td>
      <td>0.002087</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>335</th>
      <td>(32, 43, 38)</td>
      <td>(9)</td>
      <td>0.002328</td>
      <td>0.103609</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>9.651685</td>
      <td>0.002087</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>254</th>
      <td>(28, 7, 23)</td>
      <td>(9)</td>
      <td>0.002328</td>
      <td>0.103609</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>9.651685</td>
      <td>0.002087</td>
      <td>inf</td>
      <td>3</td>
    </tr>
    <tr>
      <th>639</th>
      <td>(24, 34, 31, 7)</td>
      <td>(22)</td>
      <td>0.002328</td>
      <td>0.107101</td>
      <td>0.002328</td>
      <td>1.0</td>
      <td>9.336957</td>
      <td>0.002079</td>
      <td>inf</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>





