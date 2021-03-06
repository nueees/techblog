---
toc: true
layout: post
description: 6장
categories: [statistics,python]
title: Ridge, Lasso, Elastic Net
---

[ICHI.PRO](https://ichi.pro/ko/seonhyeong-hoegwieseo-lisji-hoegwi-lasso-mich-elastic-net-eulo-86512636393446)
[쿠의블로그](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=hsj2864&logNo=221382076374)

---

# 06_통계분석_2 (Ridge, Lasso, Elastic Net)

1. Ridge  
2. Lasso  
3. Elastic Net  

- 이번 세 규제모델은 머신러닝 파트에서 모델 고르고 튜닝하라는 문제에 사용하면 됨  
- ADP에서 선형회귀 모형을 모델링할 일은 거의 없겠지만, 만약 이전 기출처럼 3개의 모델을 만들고 각각 튜닝해보라는 문제가 나온다면 첫번째 모델로 머신러닝의 기본of기본인 선형회귀 모형을 쓸 수 있을 것 같고, 이때 릿지라쏘를 활용해서 모델을 튜닝했다는 걸 보여주면 될 것 같음.  

- 릿지, 라쏘, 엘라스틱넷 모형은 회귀모형 잔차합을 최소화시키는 기존의 회귀계수 산출 공식에 비용함수를 추가한 것임  
- 회귀모형의 잔차합을 최소화시키는 방식으로 회귀계수를 구하면, 모델을 과적합시키는 방향으로 나아가기 때문에(회귀계수 w의 값이 커짐) 회귀계수벡터 w를 줄여주는 규제 항이 필요함  
  - 릿지회귀: 회귀계수의 제곱을 규제 항으로 사용  
  - 라쏘회귀: 회귀계수의 절대값을 규제 항으로 사용 (특정 w는 0이 됨)  
  - 엘라스틱넷: 제곱과 절대값 모두를 규제항으로 사용  


### When to use?
  - __변수개수 > 데이터의 양__ : 라쏘 이용  
    라쏘는 주로 변수들이 많은 경우에 변수 선택을 하기 위해 사용  
  - __데이터의 양>변수개수__ : 릿지 이용  
    릿지는 변수들 간 다중공선성이 발견될 때 x의 분산을 줄이기 위해 사용 (정보 압축)  
  - 엘라스틱넷은 교란변수 A가 있을 때, 실제 영향을 주는 변수인 B만 사라지고 A만 남아있는 상황(라쏘 이용 시)이나 A와 B의 회귀계수가 크게 줄어서 A의 영향력이 과소평가되는 상황(릿지 이용 시)을 피하기 위해 사용  

```python
import pandas as pd
import numpy as np
from scipy import stats
from sklearn.datasets import load_boston
import matplotlib.pyplot as plt
import seaborn as sns
```


```python
## 예제 데이터 load
boston = load_boston()
df_boston = pd.DataFrame(boston.data, columns = boston.feature_names)
df_boston['PRICE'] = boston.target
```


```python
df_boston.head()
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
      <th>CRIM</th>
      <th>ZN</th>
      <th>INDUS</th>
      <th>CHAS</th>
      <th>NOX</th>
      <th>RM</th>
      <th>AGE</th>
      <th>DIS</th>
      <th>RAD</th>
      <th>TAX</th>
      <th>PTRATIO</th>
      <th>B</th>
      <th>LSTAT</th>
      <th>PRICE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.00632</td>
      <td>18.0</td>
      <td>2.31</td>
      <td>0.0</td>
      <td>0.538</td>
      <td>6.575</td>
      <td>65.2</td>
      <td>4.0900</td>
      <td>1.0</td>
      <td>296.0</td>
      <td>15.3</td>
      <td>396.90</td>
      <td>4.98</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.02731</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>6.421</td>
      <td>78.9</td>
      <td>4.9671</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>396.90</td>
      <td>9.14</td>
      <td>21.6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.02729</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>7.185</td>
      <td>61.1</td>
      <td>4.9671</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>392.83</td>
      <td>4.03</td>
      <td>34.7</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.03237</td>
      <td>0.0</td>
      <td>2.18</td>
      <td>0.0</td>
      <td>0.458</td>
      <td>6.998</td>
      <td>45.8</td>
      <td>6.0622</td>
      <td>3.0</td>
      <td>222.0</td>
      <td>18.7</td>
      <td>394.63</td>
      <td>2.94</td>
      <td>33.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.06905</td>
      <td>0.0</td>
      <td>2.18</td>
      <td>0.0</td>
      <td>0.458</td>
      <td>7.147</td>
      <td>54.2</td>
      <td>6.0622</td>
      <td>3.0</td>
      <td>222.0</td>
      <td>18.7</td>
      <td>396.90</td>
      <td>5.33</td>
      <td>36.2</td>
    </tr>
  </tbody>
</table>
</div>




```python
X_data = df_boston.drop(['PRICE'], axis = 1)
y_target = df_boston['PRICE']
```

<br><br>
---


## 릿지회귀(L2규제)


```python
from sklearn.linear_model import Ridge
from sklearn.model_selection import cross_val_score

ridge = Ridge(alpha = 10) ## alpha는 규제항에 곱해지는 것으로 alpha가 커질수록 회귀계수는 작아짐 (alpha = 회귀식의 람다)
neg_mse_scores = cross_val_score(ridge, X_data, y_target, scoring = "neg_mean_squared_error", cv = 5) 
rmse_scores = np.sqrt(-1 * neg_mse_scores)
avg_rmse = np.mean(rmse_scores)
```


```python
## 규제항에 곱해지는 alpha(=람다) 값의 변화에 따른 정확도, 회귀계수의 크기 변화 측정
alphas = [0, 0.1, 1, 10, 100] ## alpha = 0일때는 규제가 없는 경우와 동일

for alpha in alphas:
  ridge = Ridge(alpha = alpha)

  neg_mse_scores = cross_val_score(ridge, X_data, y_target, scoring = "neg_mean_squared_error", cv = 5)
  avg_rmse = np.mean(np.sqrt(-1 * neg_mse_scores))
  print('alpha {0} 일 때 5 folds의 평균 RMSE: {1:.3f}'.format(alpha, avg_rmse))
```

    alpha 0 일 때 5 folds의 평균 RMSE: 5.829
    alpha 0.1 일 때 5 folds의 평균 RMSE: 5.788
    alpha 1 일 때 5 folds의 평균 RMSE: 5.653
    alpha 10 일 때 5 folds의 평균 RMSE: 5.518
    alpha 100 일 때 5 folds의 평균 RMSE: 5.330
    


```python
coeff_df = pd.DataFrame()

for pos, alpha in enumerate(alphas):
  ridge = Ridge(alpha = alpha)
  ridge.fit(X_data, y_target)

  coeff = pd.Series(data = ridge.coef_, index = X_data.columns)
  colname = 'alpha:' + str(alpha)
  coeff_df[colname] = coeff

print(coeff_df)
```

               alpha:0  alpha:0.1    alpha:1  alpha:10  alpha:100
    CRIM     -0.108011  -0.107474  -0.104595 -0.101435  -0.102202
    ZN        0.046420   0.046572   0.047443  0.049579   0.054496
    INDUS     0.020559   0.015999  -0.008805 -0.042962  -0.052826
    CHAS      2.686734   2.670019   2.552393  1.952021   0.638335
    NOX     -17.766611 -16.684645 -10.777015 -2.371619  -0.262847
    RM        3.809865   3.818233   3.854000  3.702272   2.334536
    AGE       0.000692  -0.000269  -0.005415 -0.010707   0.001212
    DIS      -1.475567  -1.459626  -1.372654 -1.248808  -1.153390
    RAD       0.306049   0.303515   0.290142  0.279596   0.315358
    TAX      -0.012335  -0.012421  -0.012912 -0.013993  -0.015856
    PTRATIO  -0.952747  -0.940759  -0.876074 -0.797945  -0.829218
    B         0.009312   0.009368   0.009673  0.010037   0.009393
    LSTAT    -0.524758  -0.525966  -0.533343 -0.559366  -0.660764
    

위의 릿지회귀모델에서는 alpha가 100일 때 CV를 이용한 모델평가 정확도가 가장 높게 나왔다. 또한 규제항의 alpha가 높아지면 회귀계수의 값은 점점 더 작아짐을 확인할 수 있는데, 0이 되는 항은 하나도 없는 것에서 릿지 모델의 특징을 알 수 있다.

<br><br>
---

## 라쏘회귀(L1규제)
- 회귀계수 w의 절대값을 비용함수로 사용하여 w가 0이 되는 경우가 쉽게 생김


```python
## Lasso는 Ridge회귀에서 사용했던 식 그대로에서 함수만 바꿔주면 된다.

from sklearn.linear_model import Lasso
alphas = [0, 0.1, 1, 10, 100]

for alpha in alphas:
  lasso = Lasso(alpha = alpha)

  neg_mse_scores = cross_val_score(lasso, X_data, y_target, scoring = "neg_mean_squared_error", cv = 5)
  avg_rmse = np.mean(np.sqrt(-1 * neg_mse_scores))
  print('Lasso 회귀 alpha {0} 일 때 5 folds의 평균 RMSE: {1:.3f}'.format(alpha, avg_rmse))


coeff_df = pd.DataFrame()

for pos, alpha in enumerate(alphas):
  lasso = Lasso(alpha = alpha)
  lasso.fit(X_data, y_target)

  coeff = pd.Series(data = lasso.coef_, index = X_data.columns)
  colname = 'alpha:' + str(alpha)
  coeff_df[colname] = coeff

print(coeff_df)
```

    Lasso 회귀 alpha 0 일 때 5 folds의 평균 RMSE: 5.829
    Lasso 회귀 alpha 0.1 일 때 5 folds의 평균 RMSE: 5.615
    Lasso 회귀 alpha 1 일 때 5 folds의 평균 RMSE: 5.776
    Lasso 회귀 alpha 10 일 때 5 folds의 평균 RMSE: 6.586
    Lasso 회귀 alpha 100 일 때 5 folds의 평균 RMSE: 8.393
               alpha:0  alpha:0.1   alpha:1  alpha:10  alpha:100
    CRIM     -0.108011  -0.097894 -0.063437 -0.000000  -0.000000
    ZN        0.046420   0.049211  0.049165  0.026146   0.000000
    INDUS     0.020559  -0.036619 -0.000000 -0.000000  -0.000000
    CHAS      2.686734   0.955190  0.000000  0.000000   0.000000
    NOX     -17.766611  -0.000000 -0.000000  0.000000  -0.000000
    RM        3.809865   3.703202  0.949811  0.000000   0.000000
    AGE       0.000692  -0.010037  0.020910  0.000000  -0.000000
    DIS      -1.475567  -1.160538 -0.668790 -0.000000   0.000000
    RAD       0.306049   0.274707  0.264206  0.000000  -0.000000
    TAX      -0.012335  -0.014570 -0.015212 -0.009282  -0.020972
    PTRATIO  -0.952747  -0.770654 -0.722966 -0.000000  -0.000000
    B         0.009312   0.010249  0.008247  0.007496   0.004466
    LSTAT    -0.524758  -0.568769 -0.761115 -0.564038  -0.000000
    

라쏘 회귀는 ALPHA가 0.1일 때 가장 좋은 성능을 보였고, ALPHA값이 증가함에 따라 다수의 회귀계수가 0이 되는 모습을 보임.
릿지 회귀는 alpha를 높여도 w가 작아질지언정 0이 되지는 않았으나, lasso는 급격하게 0을 만들어나감. 따라서 feature selection에 적합한 모델이라 볼 수 있음. 라쏘와 릿지의 비교에서, 이 boston 집값 데이터는 row 수는 충분하기 때문에 정보량을 줄이는 쪽으로 모델링해야 한다고 말할 수 있을 것 같음

<br><br>
---

## 엘라스틱넷


```python
## 다 똑같다.. 그냥 함수만 계속 바꿔주면 된다

from sklearn.linear_model import ElasticNet
alphas = [0, 0.1, 1, 10, 100]

for alpha in alphas:
  elasticnet = ElasticNet(alpha = alpha)

  neg_mse_scores = cross_val_score(elasticnet, X_data, y_target, scoring = "neg_mean_squared_error", cv = 5)
  avg_rmse = np.mean(np.sqrt(-1 * neg_mse_scores))
  print('ElasticNet 회귀 alpha {0} 일 때 5 folds의 평균 RMSE: {1:.3f}'.format(alpha, avg_rmse))


coeff_df = pd.DataFrame()

for pos, alpha in enumerate(alphas):
  elasticnet = ElasticNet(alpha = alpha)
  elasticnet.fit(X_data, y_target)

  coeff = pd.Series(data = elasticnet.coef_, index = X_data.columns)
  colname = 'alpha:' + str(alpha)
  coeff_df[colname] = coeff

print(coeff_df)
```

    ElasticNet 회귀 alpha 0 일 때 5 folds의 평균 RMSE: 5.829
    ElasticNet 회귀 alpha 0.1 일 때 5 folds의 평균 RMSE: 5.478
    ElasticNet 회귀 alpha 1 일 때 5 folds의 평균 RMSE: 5.522
    ElasticNet 회귀 alpha 10 일 때 5 folds의 평균 RMSE: 6.472
    ElasticNet 회귀 alpha 100 일 때 5 folds의 평균 RMSE: 8.312
               alpha:0  alpha:0.1   alpha:1  alpha:10  alpha:100
    CRIM     -0.108011  -0.100079 -0.080371 -0.000000  -0.000000
    ZN        0.046420   0.051377  0.053240  0.040935   0.000000
    INDUS     0.020559  -0.045901 -0.012657 -0.000000  -0.000000
    CHAS      2.686734   0.987970  0.000000  0.000000   0.000000
    NOX     -17.766611  -0.059533 -0.000000  0.000000  -0.000000
    RM        3.809865   3.252662  0.933936  0.000000   0.000000
    AGE       0.000692  -0.007219  0.020579  0.020067  -0.000000
    DIS      -1.475567  -1.181402 -0.762044 -0.000000   0.000000
    RAD       0.306049   0.288726  0.301569  0.000000   0.000000
    TAX      -0.012335  -0.014952 -0.016439 -0.008950  -0.021347
    PTRATIO  -0.952747  -0.793502 -0.748046 -0.000000  -0.000000
    B         0.009312   0.009963  0.008339  0.007436   0.010110
    LSTAT    -0.524758  -0.598184 -0.758426 -0.632398  -0.000000
    

- alpha의 값을 range 함수를 이용해 더 촘촘히 설정하면, 분산과 편향을 가장 적게 만드는 골디락스 지점에 근접하게 찾을 수 있을 것으로 보임.
- Ridge가 현재 가장 좋은 성능으로 나오는 이유는 parameter 개수보다 데이터의 수가 많기 때문에 굳이 feature를 쳐내는 것 보다는 개별 변수의 영향력을 줄이는 게 전체적으로 오류율이 떨어지는 것 같음
