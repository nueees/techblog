---
toc: true
layout: post
description: 5장
categories: [statistics,python]
title: CDA, T-Test, ANOVA
---

[kaggle/GREG HAMEL/Hypothesis Testing](https://www.kaggle.com/hamelg/python-for-data-24-hypothesis-testing)

---

# 05_CDA(Confirmatory Data Analysis)

- 어떤 현상이 '우연'인지 그렇지 않은지를 확인하기 위함


```python
import scipy.stats as spst
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns
import math
```

## Comparative Type Test

### One Sample T-Test
- 샘플 A의 평균이 x와 다른가? - p-value가 낮으면 '다르다!'
- 귀무가설 : 같다
- 대립가설 : 다르다


```python
print(spst.poisson.rvs.__doc__)
```

    
            Random variates of given type.
    
            Parameters
            ----------
            arg1, arg2, arg3,... : array_like
                The shape parameter(s) for the distribution (see docstring of the
                instance object for more information).
            loc : array_like, optional
                Location parameter (default=0).
            size : int or tuple of ints, optional
                Defining number of random variates (Default is 1).  Note that `size`
                has to be given as keyword, not as positional argument.
            random_state : {None, int, `~np.random.RandomState`, `~np.random.Generator`}, optional
                This parameter defines the object to use for drawing random
                variates.
                If `random_state` is `None` the `~np.random.RandomState` singleton
                is used.
                If `random_state` is an int, a new ``RandomState`` instance is used,
                seeded with random_state.
                If `random_state` is already a ``RandomState`` or ``Generator``
                instance, then that object is used.
                Default is None.
    
            Returns
            -------
            rvs : ndarray or scalar
                Random variates of given `size`.
    
            
    


```python
np.random.seed(6)

population_ages1 = spst.poisson.rvs(loc=18, mu=35, size=150000) # loc: lowest x value, mu: middle of distribution
population_ages2 = spst.poisson.rvs(loc=18, mu=10, size=100000)
population_ages = np.concatenate((population_ages1, population_ages2))

minnesota_ages1 = spst.poisson.rvs(loc=18, mu=30, size=30)
minnesota_ages2 = spst.poisson.rvs(loc=18, mu=10, size=20)
minnesota_ages = np.concatenate((minnesota_ages1, minnesota_ages2))

print( population_ages.mean())
print( minnesota_ages.mean())
```

    43.000112
    39.26
    


```python
spst.ttest_1samp(a = minnesota_ages,               # Sample data
                 popmean = population_ages.mean())  # Pop mean
```




    Ttest_1sampResult(statistic=-2.5742714883655027, pvalue=0.013118685425061678)



**pvalue** : 0.013118685425061678
* 정직한 설명 : 귀무가설이 참이라는 전제하에 이렇게 데이터가 관찰될 확률이 0.0....01%정도라는 뜻이다.
* 발칙한 설명 : 기존 배너보다 나을 확률이 99%를 넘는다는 뜻이다.

**statistic** : 2.5742714883655027
* 신호/노이즈, 즉 신호가 노이즈보다 2.57배 높다는 뜻


```python
vals = spst.t.ppf([0.05, 0.95] # Quantile to check
                  , 49)# minnesota_ages' Degrees of freedom
print(vals)
```

    [-1.67655089  1.67655089]
    


```python
# print(spst.t.ppf.__doc__)
# print(spst.t.cdf.__doc__)
spst.t.cdf(vals, 49)
```




    array([0.05, 0.95])



1) For a lower-tailed test, p-value = cdf(test_statistic)
2) For an upper-tailed test, p-value = 1 - cdf(test_statistic)
3) For a two-tailed test, p-value = 2 * (1 - cdf(|test_statistic|))


```python
spst.t.cdf(x= -2.5742,      # T-test statistic
               df= 49) * 2   # two-tailed test
```




    0.013121066545690117



t-test의 p-value 0.013121066545690117과 같음



```python
sigma = minnesota_ages.std()/math.sqrt(50)  # Sample stdev/sample size

spst.t.interval(0.95,                        # Confidence level 95로 할 때 43 평균이 포함 안됨
                 df = 49,                     # Degrees of freedom
                 loc = minnesota_ages.mean(), # Sample mean
                 scale= sigma)                # Standard dev estimate
```




    (36.369669080722176, 42.15033091927782)




```python

spst.t.interval(0.99,                        # 99로 하면 43 평균이 포함됨
                 df = 49,                     
                 loc = minnesota_ages.mean(), 
                 scale= sigma)                
```




    (35.40547994092107, 43.11452005907893)



### Two Sample T-Test
- A와 B가 다른가? p-value가 낮으면 '다르다'!


```python
np.random.seed(12)
wisconsin_ages1 = spst.poisson.rvs(loc=18, mu=33, size=30)
wisconsin_ages2 = spst.poisson.rvs(loc=18, mu=13, size=20)
wisconsin_ages = np.concatenate((wisconsin_ages1, wisconsin_ages2))

print( wisconsin_ages.mean() )
```

    42.8
    


```python
# 분산의 동일성 검정
spst.levene(minnesota_ages, wisconsin_ages)
# pvalue가 통상적인 기준인 0.1보다 크면 분산 동일하다는 가정 받아들임.
```




    LeveneResult(statistic=0.028047686012903684, pvalue=0.8673418686154897)




```python
spst.ttest_ind(minnesota_ages, wisconsin_ages, equal_var=True) # equal_var는 등분산 여부인데, 모르면 False
```




    Ttest_indResult(statistic=-1.7083870793286842, pvalue=0.09073015386514256)



minnesota와 wisconsin 두 그룹이 동일할 경우, 다른(차이가 나는) 샘플 데이터를 볼 가능성은 p값인 0.09073015386514256 확률임.  
p값이 유의수준 5%보다 크기 때문에 두 그룹은 같다고 판단.

### Paired T-Test
- 한 집단에서 전-후 비교(Before-After) - 당연히 p-value 낮으면 다른 것


```python
np.random.seed(11)

before= spst.norm.rvs(scale=30, loc=250, size=100)

after = before + spst.norm.rvs(scale=5, loc=-1.25, size=100)

weight_df = pd.DataFrame({"weight_before":before,
                          "weight_after":after,
                          "weight_change":after-before})

weight_df.describe()    
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
      <th>weight_before</th>
      <th>weight_after</th>
      <th>weight_change</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>250.345546</td>
      <td>249.115171</td>
      <td>-1.230375</td>
    </tr>
    <tr>
      <th>std</th>
      <td>28.132539</td>
      <td>28.422183</td>
      <td>4.783696</td>
    </tr>
    <tr>
      <th>min</th>
      <td>170.400443</td>
      <td>165.913930</td>
      <td>-11.495286</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>230.421042</td>
      <td>229.148236</td>
      <td>-4.046211</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>250.830805</td>
      <td>251.134089</td>
      <td>-1.413463</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>270.637145</td>
      <td>268.927258</td>
      <td>1.738673</td>
    </tr>
    <tr>
      <th>max</th>
      <td>314.700233</td>
      <td>316.720357</td>
      <td>9.759282</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.subplot(1,2,1)
weight_df['weight_before'].plot(kind = 'box', ylim = (100,300))

plt.subplot(1,2,2)
weight_df['weight_after'].plot(kind = 'box', ylim = (100,300))
```




    <AxesSubplot:>




    
![png](2020-01-01-%EA%B8%B0%EB%B3%B8%ED%86%B5%EA%B3%841_files/2020-01-01-%EA%B8%B0%EB%B3%B8%ED%86%B5%EA%B3%841_25_1.png)
    



```python
spst.ttest_rel(a = before,
                b = after)
```




    Ttest_relResult(statistic=2.5720175998568284, pvalue=0.011596444318439857)



### ANOVA(Analysis of Variance)
**귀무가설 : A,B,C 다 똑같다**  
**대립가설 : A,B,C 중 '무언가 하나는' 다를 것이다.**

대립가설 조심, A, B, C 중 뭐가 다르고, 얼마나 다르고 등은 전혀 알 수 없다. 따로 계산해야 한다.

[ 분산분석  검정의 가정사항 (assumptions of ANOVA test) ]

  (1) 독립성: 각 샘플 데이터는 서로 독립이다.  
  (2) 정규성: 각 샘플 데이터는 정규분포를 따르는 모집단으로 부터 추출되었다.  
  (3) 등분산성: 그룹들의 모집단의 분산은 모두 동일하다.  
  


```python
np.random.seed(12)

races =   ["asian","black","hispanic","other","white"]

# Generate random data
voter_race = np.random.choice(a= races,
                              p = [0.05, 0.15 ,0.25, 0.05, 0.5],
                              size=1000)

voter_age = spst.poisson.rvs(loc=18,
                              mu=30,
                              size=1000)

# Group age data by race
voter_frame = pd.DataFrame({"race":voter_race,"age":voter_age})
groups = voter_frame.groupby("race").groups

print(voter_frame)

# Etract individual groups
asian = voter_age[groups["asian"]]
black = voter_age[groups["black"]]
hispanic = voter_age[groups["hispanic"]]
other = voter_age[groups["other"]]
white = voter_age[groups["white"]]

print(asian)

# 첫번째 방법 Perform the ANOVA
spst.f_oneway(asian, black, hispanic, other, white)
```

             race  age
    0       black   51
    1       white   49
    2    hispanic   51
    3       white   48
    4       asian   56
    ..        ...  ...
    995     white   47
    996     asian   40
    997     white   50
    998     white   51
    999  hispanic   43
    
    [1000 rows x 2 columns]
    [56 52 37 50 53 47 56 43 46 54 45 54 42 44 55 50 45 49 51 57 56 46 43 53
     48 54 54 44 40 46 51 52 44 54 43 44 53 42 54 44 59 47 54 40]
    




    F_onewayResult(statistic=1.7744689357329695, pvalue=0.13173183201930463)



f통계량 1.774와 p값 0.1317은 각 그룹의 평균이 큰 차이가 없다는 것을 보여줌.  


```python

# 두번째 방법 statsmodels lib 사용
import statsmodels.api as sm
from statsmodels.formula.api import ols

model = ols('age ~ race',                 # Model formula
            data = voter_frame).fit()
                
anova_result = sm.stats.anova_lm(model, typ=2)
print (anova_result)
```

                 sum_sq     df         F    PR(>F)
    race        199.369    4.0  1.774469  0.131732
    Residual  27948.102  995.0       NaN       NaN
    

##### 정석적인 해석 : 귀무가설이 참일 때, 이러한 데이터가 관측될 확률은 1.85% 정도이다. (1.85%확률을 뚫고 이런 데이터가 관측될 수도 있다.)

발칙한 해석 : 뭔가 하나는 차이가 날 확률이 98%는 넘는다.


```python
from statsmodels.stats.multicomp import pairwise_tukeyhsd

tukey = pairwise_tukeyhsd(endog=voter_age,     # Data
                          groups=voter_race,   # Groups
                          alpha=0.05)          # Significance level

tukey.plot_simultaneous()    # Plot group confidence intervals
plt.vlines(x=49.57,ymin=-0.5,ymax=4.5, color="red")
plt.show()
tukey.summary()              # See test summary
```

    C:\Users\Administrator\anaconda3\lib\site-packages\statsmodels\sandbox\stats\multicomp.py:775: UserWarning: FixedFormatter should only be used together with FixedLocator
      ax1.set_yticklabels(np.insert(self.groupsunique.astype(str), 0, ''))
    


    
![png](2020-01-01-%EA%B8%B0%EB%B3%B8%ED%86%B5%EA%B3%841_files/2020-01-01-%EA%B8%B0%EB%B3%B8%ED%86%B5%EA%B3%841_32_1.png)
    





<table class="simpletable">
<caption>Multiple Comparison of Means - Tukey HSD, FWER=0.05</caption>
<tr>
   <th>group1</th>   <th>group2</th>  <th>meandiff</th>  <th>p-adj</th>  <th>lower</th>   <th>upper</th> <th>reject</th>
</tr>
<tr>
    <td>asian</td>    <td>black</td>   <td>-1.3353</td> <td>0.5735</td> <td>-3.8242</td> <td>1.1535</td>  <td>False</td>
</tr>
<tr>
    <td>asian</td>  <td>hispanic</td>  <td>-0.7593</td>   <td>0.9</td>  <td>-3.1315</td> <td>1.6129</td>  <td>False</td>
</tr>
<tr>
    <td>asian</td>    <td>other</td>   <td>-0.0264</td>   <td>0.9</td>  <td>-3.0202</td> <td>2.9674</td>  <td>False</td>
</tr>
<tr>
    <td>asian</td>    <td>white</td>   <td>-1.4184</td> <td>0.4336</td> <td>-3.6932</td> <td>0.8564</td>  <td>False</td>
</tr>
<tr>
    <td>black</td>  <td>hispanic</td>   <td>0.576</td>  <td>0.8145</td> <td>-0.9362</td> <td>2.0882</td>  <td>False</td>
</tr>
<tr>
    <td>black</td>    <td>other</td>    <td>1.309</td>  <td>0.5494</td> <td>-1.0622</td> <td>3.6801</td>  <td>False</td>
</tr>
<tr>
    <td>black</td>    <td>white</td>   <td>-0.0831</td>   <td>0.9</td>  <td>-1.4374</td> <td>1.2713</td>  <td>False</td>
</tr>
<tr>
  <td>hispanic</td>   <td>other</td>    <td>0.733</td>  <td>0.8996</td> <td>-1.5154</td> <td>2.9813</td>  <td>False</td>
</tr>
<tr>
  <td>hispanic</td>   <td>white</td>   <td>-0.6591</td> <td>0.4974</td> <td>-1.7847</td> <td>0.4665</td>  <td>False</td>
</tr>
<tr>
    <td>other</td>    <td>white</td>   <td>-1.392</td>  <td>0.3912</td> <td>-3.5374</td> <td>0.7533</td>  <td>False</td>
</tr>
</table>



## Associative Type Test

### Correlation Coefficient
**귀무가설 : X와 Y는 상관이 없다.(상관계수 = 0)**  
**대립가설 : 상관계수가 0이 아니다.**
- X와 Y를 별도로 시각화 해 볼 것


```python

spst.pearsonr(wisconsin_ages,minnesota_ages) # X와 Y의 상관계수와 p-value
```




    (0.7749658250313621, 3.969124248684934e-11)



- 결과는 튜플로 나오는데

1. 튜플의 첫 번째 값 : 상관계수를 뜻한다. 두 데이터의 선형성의 정도를 나타낸다.
2. p-value는 상관계수가 우연에 의해 일어나진 않았는지 판단한다.
    * 귀무가설 : 상관 계수가 0이다.
    * 대립가설 : 상관 계수가 0이 아니다.

pvalue가 0.57로 0.05보다 크기 때문에 귀무가설 받아들임
피어슨상관계쑤는 -0.25이므로 약한 음의 상관관계가 있음

### 교차분석(Chisquare_test)

티셔츠 구매여부와 반바지 구매여부는 관계가.. 있을까?!  
**귀무가설 : 티셔츠 구매와 바지 구매는 별개이다.(독립이다)**  
**대립가설 : 티셔츠를 구매와 바지는 독립이 아니다.관련이 있다..**


```python
# 고객별 셔츠와 바지의 구매 여부 데이터
shirt_raw = {'shirts':[0,0,0,0,0,0,1,1,0,0,1,1,1,1,1,0,0,0,0,0,1],
            'pants':[0,0,1,0,0,0,1,0,1,1,1,1,1,1,1,0,0,0,1,0,1] }
shirt_df = pd.DataFrame(shirt_raw)
print(shirt_df.head(10))
```

       shirts  pants
    0       0      0
    1       0      0
    2       0      1
    3       0      0
    4       0      0
    5       0      0
    6       1      1
    7       1      0
    8       0      1
    9       0      1
    


```python
# 데이터의 Crosstable
contingency = pd.crosstab(shirt_df['shirts'], shirt_df['pants'])
contingency
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
      <th>pants</th>
      <th>0</th>
      <th>1</th>
    </tr>
    <tr>
      <th>shirts</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>9</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>




```python
chiresult = spst.chi2_contingency(contingency) # 카이제곱 검정
```


```python
# 결과 : 튜플로 4개 값 출력됨
print("카이제곱통계량 : {}".format(chiresult[0]))
print("p-value : {:.20f}".format(chiresult[1]))
print("자유도 : {}".format(chiresult[2]))
print("기대 빈도 분할표: \n", chiresult[3] ) #귀무가설에 대한 기대빈도.
```

    카이제곱통계량 : 4.317941433566433
    p-value : 0.03771251880967476516
    자유도 : 1
    기대 빈도 분할표: 
     [[6.19047619 6.80952381]
     [3.80952381 4.19047619]]
    

- 유의수준 0.05 하에 p-value가 매우 낮으므로 두 집단간 차이가 있다(바지 구매는 셔츠 구매와 관련이 있다)
- pants 0과 pants 1 그룹을 비교했을 때 shirts 0, 1의 차이가 있다. pants가 0, 1, 2였다면 0, 1, 2에 따라 차이가 있다라고 해석할 수 있음
