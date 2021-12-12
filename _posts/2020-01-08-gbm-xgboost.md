---
toc: true
layout: post
description: 9장,10장
categories: [statistics,python]
title: GBM, XGBoost
---

[yngie blog](https://yngie-c.github.io/machine%20learning/2021/03/21/gbm/)  
[creatorjo blog](https://creatorjo.tistory.com/143)  

---

# 통계분석_5 - GBM(Gradient Boosting Machine)

## 3-1) 사전 정리
### 부스팅(Boosting) : 앙상블 방법 중 하나
  - 약한 모델을 만든 후에 학습 결과를 바탕으로 이를 보완해줄 수 있는 모델을 반복(**순차**)해서 생성하고, 최종적으로 모두 결합하여 강한 모델을 만듦
  - 약한 모델을 결합하면 임의의 강한 모델의 성능 이상으로 끌어올릴(Boosting)수 있다는 개념

### Adaboost(Adaptive Boosting)
  - 여러 부스팅 알고리즘 중에서 초기에 사용된 알고리즘 중 하나
  - 이전 **약한 모델의 오답에 가중치를 부여**하여 새로운 모델을 학습하는 방법
  - 틀리게 예측한 인스턴스에 가중치(Weight)를 부여한다는 다소 간단한 아이디어를 적용했음에도 딥러닝 기법이 나오기 전까지 실시간 얼굴 추적(Real-time Face detection) 등 다양한 곳에 사용된 뛰어난 기법
  - 이진 분류시에 y 의 레이블은 −1,1

### 약한 모델(Weak Model)
  - 성능이 낮은 모델을 가리킴. 약한 모델의 성능은 랜덤 추측(Random gussing)보다 약간 더 나은 수준

## 3-2) GBM(Gradient Boosting Machine)
  - **회귀분석 또는 분류분석을 수행**할 수 있는 예측모형이며, 예측모형의 앙상블 방법론 중 부스팅 계열에 속하는 알고리즘임
  - **경사 하강법(Gradient descent)을 결합**한 새로운 **부스팅(Boosting)** 알고리즘으로 **잔차(Residual, 정답과 예측값의 차이)**를 기준으로 함  
  - 반복하여 잔차를 줄여나가는(최소화하는) 과정에서 경사 하강법(Gradient descent)을 사용함<br><br>
  
  - 손실 함수(Loss function)<br>
    손실 함수의 기울기를 최소화하는 방향으로 추가적인 몯레이 순차적으로 생성되기 때문에 어떤 손실함수를 사용하는지에 따라 최종 생성되는 함수가 달라지며, 회귀냐 분류냐에 따라 사용하는 손실함수가 달라짐<br>
    Regression을 위한 손실 함수 : 제곱 오차(L2, 일반적으로 많이 사용함), 절대 오차(L1), Huber 오차, Quantile 오차 등<br>
    Classification을 위한 손실 함수 : 베르누이 오차, Adaboost 오차 등(이진 분류시에 y 의 레이블은 −1,1)<br><br>
    
  - 과적합(overfitting)<br>
    GBM은 과적합이 발생할 수 있음<br>
    y=F(x)+ϵ <- 정답 레이블을 y, 우리가 최종적으로 근사하고자 하는 함수를 F(x), 소음으로 인해 발생하는 오차를 ϵ<br>
    근사하는 목적은 F(x) 인데 반복수가 많아지다 보면 ϵ 까지 잔차로 인식해서 근사해버리는 과적합 문제가 발생함<br><br>
  
  - 정칙화(Regularization) 방법<br>
    GBM의 과적합을 방지하기 위해 적용하는 방법<br>
    서브샘플링(Subsampling), 수축법(Shrinkage), 조기 종료(Early stopping, 일반적으로 많이 사용됨)가 있음<br><br>
    
  - Variable importance<br>
    GBM에서 변수의 중요도 : 각 트리마다 변수 j의 중요도를 평균내어 구함<br>
    하나의 의사 결정 나무 T에서 변수 j의 중요도 : 해당 변수를 사용했을 때 얻어지는 정보 획득량(Information gain, IG)을 모두 더하여 측정<br><br>
    
  - GBM은 보다 좋은 성능을 보이지만 계속해서 근사 함수를 만들어 내야 하기 때문에 **실행 시간이 오래 걸리고 많은 컴퓨팅 자원이 필요**한 단점이 있음
  - 이를 **개선한 알고리즘으로 XGBoost, LightGBM, CatBoost** 등이 있음

## 3-3) sklearn의 ensemble class - GradientBoostingRegressor

- from sklearn.ensemble import GradientBoostingRegressor<br><br>
- model = GradientBoostingRegressor(loss='ls', learning_rate=0.1, n_estimators=100, subsample=1.0, criterion='friedman_mse', min_samples_split=2, min_samples_leaf=1, min_weight_fraction_leaf=0.0, max_depth=3, min_impurity_decrease=0.0, min_impurity_split=None, init=None, random_state=None, max_features=None, alpha=0.9, verbose=0, max_leaf_nodes=None, warm_start=False, presort='deprecated', validation_fraction=0.1, n_iter_no_change=None, tol=0.0001, ccp_alpha=0.0)<br><br>

- 트리의 수와 학습률이 중요함
- loss : 최적화할 손실함수를 선택(default ls)<br>
    ls - 최소 제곱(Least Square) 회귀<br>
    lad - 최소 절대 편차로, 입력 변수의 순서 정보만을 기반으로하는 매우 강력한 손실 함수<br>
    huber - 위 둘의 조합<br>
    quantile - 분위수 회귀 허용<br>
- **learning_rate** : 학습률. 각 트리의 기여도(default 0.1)<br>
    오차를 얼마나 강하게 보정할것인지를 제어함<br>
- **n_estimators** : 트리의 수(default 100)<br>
    이 값을 키우면 앙상블에 트리가 더 많이 추가되어 모델의 복잡도가 커지고, 훈련세트에서의 실수를 바로잡을 기회가 많아지지만<br>
    너무 크면 모델이 복잡해지고 과적합될 수 있음<br>
- max_depth(max_leaf_nodes) : 트리의 깊이. 복잡도를 너무 높이지 말고 트리의 깊이가 5보다 깊어지지 않도록 함<br>
- subsample: 샘플 비율. 1보다 작으면 확률적 그래디언트 부스팅 발생(default 1.0)<br>
- **criterion** : 분할 기준 선택<br>
    friedman_mse(일반적으로 기능이 좋음), mse, mae 등이 있음(defalut friedman_mse)<br>


## Gradient Boost Machine(GBM)

> **GBM의 학습 방식**

Gradient Boosting Algorithm (GBM)은 회귀분석 또는 분류 분석을 수행할 수 있는 예측모형이며 예측모형의 앙상블 방법론 중 부스팅 계열에 속하는 알고리즘입니다. Gradient Boosting Algorithm은 Tabular format 데이터 (엑셀형태와 같이 X-Y Grid로 되어있는 데이터)에 대한 예측에서 엄청난 성능을 보여주고, 머신러닝 알고리즘 중에서도 가장 예측 성능이 높다고 알려진 알고리즘입니다. 그렇기 때문에 Gradient Boosting Algorithm을 구현한 패키지들이 많습니다. LightGBM, CatBoost, XGBoost 같은 파이썬 패키지들이 모두 Gradient Boosting Algorithm을 구현한 패키지들입니다. GBM은 계산량이 상당히 많이 필요한 알고리즘이기 때문에, 이를 하드웨어 효율적으로 구현하는 것이 필요한데, 위 패키지들은 모두 GBM을 효율적으로 구현하려고한 패키지들이라고 볼 수 있습니다.

출처: https://3months.tistory.com/368 [Deep Play]

**GBM(Gradient Boosting Machine)**은 AdaBoost(에이다부스트)와 유사하지만 가중치를 업데이트 할때 경사 하강법(Gradient Descent)을 이용하는 것이 큰! 차이 입니다.

 

경사 하강법(Gradient Descent): 분류의 실제 결과값을 Y, 피처를 X1, X2, ... , Xn 그리고 이 피처들에 기반한 예측 함수를 F(x) 함수라고 하면 오류식은 h(x) = y - F(x)라고 할 수 있습니다. 이 오류식을 최소화하는 방향성을 가지고 반복적으로 가중치의 값을 업데이터 해나가는 방식이 경사 하강법입니다.

 
사이킷런에서는 GradientBoostingClassifier클래스를 제공하여 GBM 기반의 분류를 실행할 수 있게 해줍니다. 마찬가지로 **사용자 행동 데이터 세트를 통해 예측 분류**를 해보도록 하겠습니다. 



```python
import os
os.chdir('C:/Users/Administrator/Documents/Python/211120_ADP스터디/ADP/')
print(os.getcwd())

```

    C:\Users\Administrator\Documents\Python\211120_ADP스터디\ADP
    


```python
import pandas as pd

def get_new_feature_name_df(old_feature_name_df):
    feature_dup_df = pd.DataFrame(data=old_feature_name_df.groupby('column_name').cumcount(),
                                  columns=['dup_cnt'])
    feature_dup_df = feature_dup_df.reset_index()
    new_feature_name_df = pd.merge(old_feature_name_df.reset_index(), feature_dup_df, how='outer')
    new_feature_name_df['column_name'] = new_feature_name_df[['column_name', 'dup_cnt']].apply(lambda x : x[0]+'_'+str(x[1]) 
                                                                                         if x[1] >0 else x[0] ,  axis=1)
    new_feature_name_df = new_feature_name_df.drop(['index'], axis=1)
    return new_feature_name_df

def get_human_dataset( ):
    
    # 각 데이터 파일들은 공백으로 분리되어 있으므로 read_csv에서 공백 문자를 sep으로 할당.
    feature_name_df = pd.read_csv('./data/UCI HAR Dataset/features.txt',sep='\s+',
                        header=None,names=['column_index','column_name'])
    
    # 중복된 피처명을 수정하는 get_new_feature_name_df()를 이용, 신규 피처명 DataFrame생성. 
    new_feature_name_df = get_new_feature_name_df(feature_name_df)
    
    # DataFrame에 피처명을 컬럼으로 부여하기 위해 리스트 객체로 다시 변환
    feature_name = new_feature_name_df.iloc[:, 1].values.tolist()
    
    # 학습 피처 데이터 셋과 테스트 피처 데이터을 DataFrame으로 로딩. 컬럼명은 feature_name 적용
    X_train = pd.read_csv('./data/UCI HAR Dataset/train/X_train.txt',sep='\s+', names=feature_name )
    X_test = pd.read_csv('./data/UCI HAR Dataset/test/X_test.txt',sep='\s+', names=feature_name)
    
    # 학습 레이블과 테스트 레이블 데이터을 DataFrame으로 로딩하고 컬럼명은 action으로 부여
    y_train = pd.read_csv('./data/UCI HAR Dataset/train/y_train.txt',sep='\s+',header=None,names=['action'])
    y_test = pd.read_csv('./data/UCI HAR Dataset/test/y_test.txt',sep='\s+',header=None,names=['action'])
    
    # 로드된 학습/테스트용 DataFrame을 모두 반환 
    return X_train, X_test, y_train, y_test
```


```python
from sklearn.ensemble import GradientBoostingClassifier
from sklearn.metrics import accuracy_score
import pandas as pd
import time
import warnings
warnings.filterwarnings('ignore')

# 결정 트리에서 사용한 get_human_dataset( )을 이용해 학습/테스트용 DataFrame 반환
X_train, X_test, y_train, y_test = get_human_dataset()

print("## 학습 피처 데이터 정보 ##")
print(X_train.info())
```

    ## 학습 피처 데이터 정보 ##
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 7352 entries, 0 to 7351
    Columns: 561 entries, tBodyAcc-mean()-X to angle(Z,gravityMean)
    dtypes: float64(561)
    memory usage: 31.5 MB
    None
    


```python
# GBM 수행 시간 측정을 위함. 시작 시간 설정.
start_time = time.time()

gb_clf = GradientBoostingClassifier(n_estimators=10, # 기본값은 100
                                    validation_fraction=0.2,
                                    n_iter_no_change=5, tol=0.01, verbose = 1,
                                    random_state=0)
    
gb_clf.fit(X_train , y_train)
gb_pred = gb_clf.predict(X_test)
gb_accuracy = accuracy_score(y_test, gb_pred)

print('GBM 정확도: {0:.4f}'.format(gb_accuracy))
print("GBM 수행 시간: {0:.1f} 초 ".format(time.time() - start_time))
```

          Iter       Train Loss   Remaining Time 
             1           1.4069           48.19s
             2           1.1662           45.24s
             3           0.9894           40.88s
             4           0.8471           35.55s
             5           0.7366           29.59s
             6           0.6445           23.66s
             7           0.5673           17.69s
             8           0.5033           11.76s
             9           0.4466            5.89s
            10           0.3981            0.00s
    GBM 정확도: 0.8907
    GBM 수행 시간: 58.9 초 
    

정확도는 89.07%이다.

시간이 오래 걸리기 때문에 간략하게 n_estimators를 10, 20으로 learning_rate를 0.05, 0.1로 설정하도록 하겠습니다.


```python
from sklearn.model_selection import GridSearchCV

start_time = time.time()

params = {
    'n_estimators':[10, 20], # 원 코드 100, 500
    'learning_rate' : [ 0.05, 0.1]
}
grid_cv = GridSearchCV(gb_clf , param_grid=params , cv=2 ,verbose=1, n_jobs = -1)
grid_cv.fit(X_train , y_train)
print('최적 하이퍼 파라미터:\n', grid_cv.best_params_)
print('최고 예측 정확도: {0:.4f}'.format(grid_cv.best_score_))

print("GBM 수행 시간: {0:.1f} 초 ".format(time.time() - start_time))
```

    Fitting 2 folds for each of 4 candidates, totalling 8 fits
          Iter       Train Loss   Remaining Time 
             1           1.4069            1.61m
             2           1.1662            1.62m
             3           0.9894            1.59m
             4           0.8471            1.52m
             5           0.7366            1.43m
             6           0.6445            1.34m
             7           0.5673            1.25m
             8           0.5033            1.15m
             9           0.4466            1.06m
            10           0.3981           57.65s
            20           0.1600            0.00s
    최적 하이퍼 파라미터:
     {'learning_rate': 0.1, 'n_estimators': 20}
    최고 예측 정확도: 0.8921
    GBM 수행 시간: 348.3 초 
    

최적 하이퍼 파리미터는 n_estimators가 10, learning_rate가 0.1일때로 나왔습니다. 최고 예측 정확도는 약 89.21%정도 나왔습니다. 이 파라미터를 가지고 테스트 데이터 세트에 적용을 하여 예측 정확도를 알아보겠습니다.

## 하이퍼 파라미터 및 튜닝
튜닝이란, 모형이 가지고 있는 여러 조건들을 변형 시키는 것

#### 대표적인 파라미터 소개
- n_estimators: weak learner의 개수 지정, 디폴트는 100개이며, 많이 설정할수록 좋은 성능 기대 가능, 그러나 시간도 오래 소요됨
- max_features: 최적의 분할을 위해 고려할 피처의 개수
int 형으로 지정 시, 대상 피처의 개수, float형으로 지정 시, 대상 피처의 퍼센트
만약 전체 피처가 16개라면 분할 위해 4개 참조
- max_depth: 트리의 최대 깊이 규정
- min_samples_split: 자식 규칙 노드를 분할해 만들기 위한 최소한의 샘플 데이터 개수
- min_samples_leaf: 말단 노드(Leaf)가 되기 위한 최소한의 샘플 데이터 수
- loss: 경사 하강법에서 사용할 비용 함수 지정
-learning_rate: GBM이 학습 진행 시 마다의 학습률. Weak Learner가 순차적으로
- subsample: weak learner가 학습에 사용하는 데이터의 샘플링 비율, 기본값은 1이며, 이는 전체학습 데이터를 기반으로 학습한다는 의미.
### 그리드 서치
GridSearchCV API를 활용하여 모형에 사용되는 하이퍼 파라미터를 순차적으로 입력하며서 편리하게 최적의 파라미터를 도출할 수 있는 방안 제공
즉, 랜덤포레스트의 파라미터를 순차적으로 변경 및 조정하면서 모형을 학습시키는 방법
이를 통해, 머신러닝 모형 개발자의 코드량을 줄여주는 매우 편리한 기법
- estimator: 머신러닝 모형의 객체가 온다.
- param_grid: 딕셔너리 형태로 조정하며, estimator의 튜닝을 위해 파라미터명과 사용될 여러 파라미터 값 지정, 이 부분은 각 머신러닝 모형의 Manual을 참조한다.
- scoring: 예측 성능을 측정할 평가 방법 지정하지만, 대개 별도의 성능 평가 지표 함수 활용
- cv: 교차 검증을 위해 분할되는 학습/테스트 세트의 개수 지정
이 때에는 시간이 다소 소요될 수 있다. (5-10분)


```python
# GridSearchCV를 이용하여 최적으로 학습된 estimator로 predict 수행. 
gb_pred = grid_cv.best_estimator_.predict(X_test)
gb_accuracy = accuracy_score(y_test, gb_pred)
print('GBM 정확도: {0:.4f}'.format(gb_accuracy))
```

    GBM 정확도: 0.9077
    

테스트 데이터 세트에서는 약 90%정도가 나온것을 확인하였습니다. GBM은 과적합에 뛰어난 예측 성능을 가지지만 수행 시간이 오래 걸리는 단점이 있습니다. 

<br><br>
---

# 통계분석_6 - XGBoost (for Regression)

reference: 
- [Machine Learning Mastery - XGBoost for Regression](https://machinelearningmastery.com/xgboost-for-regression/)
- [GBM vs XGBoost 차이](https://m.blog.naver.com/nicolechae0627/221811579005)
- [XGBoost Reg에 사용된 feature의 중요도 산정](https://mljar.com/blog/feature-importance-xgboost/)


Gradient Boosting
- 약한 학습기를 순차적으로 학습시켜 잘못된 예측값의 오류를 개선해 나가는 방법
- 일반적으로 약한학습기로 Tree 모델을 사용함

XGBoost 무엇이 다른가?
1. Regularized boosting  
일반적인 GBM은 과적합을 방지하는 기능이 없지만 XGBoost는 모델 자체에 과적합을 방지하는 기능이 존재
2. 결측값을 자동으로 처리  
XGBoost에 결측값이 있는 데이터를 넣으면 그 결측값을 하나의 값으로 인식하는 듯함. 결측값이 존재할 때의 최적 결과값도 파악
3. Tree Pruning  
GBM은 노드를 나눌 때 negative loss를 만나면 멈추도록 되어있는데, XGBoost는 하이퍼파라미터로 설정한 max_depth까지만 수행하고 Tree의 깊이를 완성시키면 다시 노드 가지치기를 수행한다. 이런 방법으로도 과적합 방지에 기여한다
4. 내장된 Cross Validation
5. Tree building 단계에서 병렬처리를 통해 더 빠른 결과처리 가능
Tree building 단계의 pseudo code가 아래와 같다면, XGBoost는 생성된 노드 아래에 each feature에 해당되는 부분에서 병렬처리를 해서 모델 생성 단계에 걸리는 시간이 단축된다.
```
For each leaf node:
    For each feature:
        Sort the instances in the node by the feature value
        Linear scan to decide the best split on the feature
    Take the best split and do it
```

참고) 파이썬 래퍼 기반의 XGB와 사이킷런 래퍼 기반의 XGB
xgboost 패키지를 불러와서 그 자체를 모델로 사용하는 경우 ( model = xgboost() ) 가 파이썬 래퍼에 해당하고, xgboost 안에서 XGBClassifier나 XGBRegressor를 꺼내 사용하는 게 사이킷런 래퍼에 해당. 솔직히 사용하는 입장에선 별 차이 없고 파라미터의 __default 값이나 메소드__가 달라진다. 

###XGBoost Regression 모델링 (사이킷런 래퍼 이용)


```python
## xgboost 패키지는 adp 제공 패키지에 포함
## 이번에 보는 건 xgboost regression이라서 분류 부분은 없습니다! 머신러닝 완벽가이드에는 분류 예시로 나와있으니 참고해주세요!
```


```python
import xgboost
from xgboost import XGBRegressor
from xgboost import plot_importance ##분류와 회귀에 사용된 변수들의 기여도를 그래프로 그려줌
import pandas as pd
import numpy as np
from sklearn.model_selection import RepeatedKFold
from sklearn.model_selection import cross_val_score
import warnings
warnings.filterwarnings('ignore')
```


```python
## 학습 데이터 : Housing price 데이터
from pandas import read_csv
from matplotlib import pyplot
url = 'https://raw.githubusercontent.com/jbrownlee/Datasets/master/housing.csv'
dataframe = read_csv(url, header=None)
### 데이터 탐색
print(dataframe.shape) ## 13개 feature와 504개 observation
print(dataframe.head())
```

    (506, 14)
            0     1     2   3      4      5   ...  8      9     10      11    12    13
    0  0.00632  18.0  2.31   0  0.538  6.575  ...   1  296.0  15.3  396.90  4.98  24.0
    1  0.02731   0.0  7.07   0  0.469  6.421  ...   2  242.0  17.8  396.90  9.14  21.6
    2  0.02729   0.0  7.07   0  0.469  7.185  ...   2  242.0  17.8  392.83  4.03  34.7
    3  0.03237   0.0  2.18   0  0.458  6.998  ...   3  222.0  18.7  394.63  2.94  33.4
    4  0.06905   0.0  2.18   0  0.458  7.147  ...   3  222.0  18.7  396.90  5.33  36.2
    
    [5 rows x 14 columns]
    


```python
dataframe.head()
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
      <th>0</th>
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.00632</td>
      <td>18.0</td>
      <td>2.31</td>
      <td>0</td>
      <td>0.538</td>
      <td>6.575</td>
      <td>65.2</td>
      <td>4.0900</td>
      <td>1</td>
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
      <td>0</td>
      <td>0.469</td>
      <td>6.421</td>
      <td>78.9</td>
      <td>4.9671</td>
      <td>2</td>
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
      <td>0</td>
      <td>0.469</td>
      <td>7.185</td>
      <td>61.1</td>
      <td>4.9671</td>
      <td>2</td>
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
      <td>0</td>
      <td>0.458</td>
      <td>6.998</td>
      <td>45.8</td>
      <td>6.0622</td>
      <td>3</td>
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
      <td>0</td>
      <td>0.458</td>
      <td>7.147</td>
      <td>54.2</td>
      <td>6.0622</td>
      <td>3</td>
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
## X,Y값 나누기
X = dataframe.iloc[:, :-1]
y = dataframe.iloc[:,-1]
```


```python
## 가장 기본적인 XGB Regressor를 만들고 평가하기
model = XGBRegressor() ## 하이퍼파라미터를 설정하려면 XGBRegreesor()의 괄호 안에 넣으면 됨
cv = RepeatedKFold(n_splits=10, n_repeats=3, random_state=1)
scores = cross_val_score(model, X, y, scoring='neg_mean_absolute_error', cv=cv, n_jobs=-1)
scores = abs(scores)
print('Mean MAE: %.3f (%.3f)' % (scores.mean(), scores.std()) )
```

    Mean MAE: 2.146 (0.328)
    

참고) 모델 평가 스코어에서 쓴 'neg_mean_absolute_error'는 우리가 배운 정확성 측정 방법 중 'MAE(Mean Absolute Error)'에 해당한다. 그러나 scikit-learn에서는 내부 작동 방식 때문에 MAE를 구하면 음수값으로 도출된다고 하고, 그래서 scikitlearn 상에서 MAE 앞에 negative를 붙이는 것 같다. [이 링크에서 더 많은 정보를 확인](https://www.kaggle.com/questions-and-answers/154600)  
이 예제에서는 사이킷런으로 구한 MAE에 다시 절대값을 씌워주는 방식을 사용함

### XGBoost의 하이퍼파라미터
머신러닝 완벽가이드 p.231 참고
- eta
GBM의 학습률에 해당하는 파라미터로 0과 1 사이의 값을 지정. 파이썬 기반의 xgboost에서 default 값은 0.3이고, 사이킷런 래퍼에서는 디폴트가 0.1. 보통은 0.01과 0.2 사이의 값을 선호
- num_boost_rounds: GBM의 n_estimator와 동일, weak learner의 개수에 해당
- min_child_weight: 트리에서 추가로 가지를 나눌지 결정하기 위해 필요한 데이터 weight의 총합(default=1) weight가 클수록 분할을 자제함
- gamma: 트리 리프노드를 추가로 나눌지 결정하는 최소 손실 감소 값. 값이 클수록 과적합 감소 (default = 0)
- max_depth: 트리 깊이 (defulat = 6) 0을 지정하면 깊이에 제한이 없음
- sub_sample: 전체 데이터에서 어느정도의 비율을 트리를 생성하는 데이터로 사용할 것인지 결정. (default = 1)
- colsample_bytree: Tree 생성에 필요한 피처를 임의로 샘플링하는 데 사용. random forest의 원리와 동일 (default = 1)
- lambda: L2 Regularization 적용 값으로 피처 개수가 많을 때 과적합을 감소시키기 위해 사용 (default =1)
- alpha: L1 Regularization 적용값. 설명은 위와 동일
- scale_pos_weight: 특정 값으로 치우친 클래스로 구성된 데이터의 균형을 유지하기 위한 파라미터 (default = 1)

### feature중요도산정


```python
## node에 사용된 feature들의 중요도 계산하기
model.fit(X,y)
model.feature_importances_
```

    [23:36:41] WARNING: /workspace/src/objective/regression_obj.cu:152: reg:linear is now deprecated in favor of reg:squarederror.
    


    array([0.0189575 , 0.00462131, 0.00827119, 0.008848  , 0.04185903,
           0.22672848, 0.01063313, 0.03859667, 0.01044884, 0.02304217,
           0.04435536, 0.01205312, 0.5515853 ], dtype=float32)



```python
import matplotlib.pyplot as plt
plt.barh(dataframe.columns[:-1], model.feature_importances_)
```


    <BarContainer object of 13 artists>



    
![png](2020-01-08-%ED%86%B5%EA%B3%84%EB%B6%84%EC%84%9D5_files/2020-01-08-%ED%86%B5%EA%B3%84%EB%B6%84%EC%84%9D5_33_1.png)
    

