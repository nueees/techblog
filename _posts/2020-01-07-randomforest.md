---
toc: true
layout: post
description: 8장
categories: [statistics,python]
title: RandomForest
---

[yg's blog](https://yganalyst.github.io/ml/ML_chap6-3/)

---

# 통계분석_4 - RandomForest

## Ensemble Methods
가장 좋은 모델 하나만 사용하는게 아니라 몇가지 모델을 통한 예측기(분류 또는 회귀)를 연결하여 더 좋은 예측기를 만드는 방법  
일반적으로 앙상블 모형은 하나의 예측기를 훈련시킬 때보다 편향을 비슷하지만 **분산**이 감소한다

1) 투표기반(Voting Classifiers)
2) 배깅(Boostrap AGGregatING, Bagging)  
3) 페이스팅(Pasting)
4) 부스팅(Boosting)  


### 1. Voting

#### 1-1. 직접투표 (Hard voting)
어떤 훈련 데이터셋에 대해 여러 분류기(로지스틱 회귀-A, SVM-A, Randomforest-B)를 훈련시켰을 때
각 분류기의 예측을 모아 다수결 투표(A:2개, B:1개 -> A)로 정하는 것



```python
# 투표기반 voting classifiers VS 개별 분류기 비교
from sklearn.model_selection import train_test_split
from sklearn.datasets import make_moons

from sklearn.ensemble import RandomForestClassifier
from sklearn.ensemble import VotingClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC

X, y = make_moons(n_samples=500, noise=0.30, random_state=42)
X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=42)

log_clf = LogisticRegression(solver = 'liblinear')
rnd_clf = RandomForestClassifier(n_estimators=10)
svm_clf = SVC(gamma='auto')

voting_clf = VotingClassifier(estimators=[('lr',log_clf),
                                         ('rf',rnd_clf),
                                         ('svc',svm_clf)],
                             voting='hard')
voting_clf.fit(X_train, y_train)

```




    VotingClassifier(estimators=[('lr', LogisticRegression(solver='liblinear')),
                                 ('rf', RandomForestClassifier(n_estimators=10)),
                                 ('svc', SVC(gamma='auto'))])




```python
from sklearn.metrics import accuracy_score

for clf in (log_clf, rnd_clf, svm_clf, voting_clf):
    clf.fit(X_train, y_train)
    y_pred = clf.predict(X_test)
    print(clf.__class__.__name__, accuracy_score(y_test,y_pred))

# voting이 0.896으로 개별분류기들 보다 조금 높음
```

    LogisticRegression 0.864
    RandomForestClassifier 0.896
    SVC 0.888
    VotingClassifier 0.896
    

#### 1-2. 간접투표 (soft voting)
hard voting은 개별 분류기마다 다수인 범주가 무엇이냐에 따라 투표로 앙상블의 예측을 결정했다면,  
간접 투표(soft voting)는 각 분류기마다 해당 범주에 속할 확률(0~1값)을 평균을 내어  
(로지스틱 회귀 - A:0.7, B:0.3, SVM - A:0.1, B:0.9, Randomforest - A:0.6, B:0.4)  
평균이 가장 높은 범주로 분류(A: $\frac{0.7+0.1+0.6}{3} = 0.47$ , B: $\frac{0.3+0.9+0.4}{3} = 0.53 $ -> B (hard로하면 A) )   





```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.ensemble import VotingClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC
from sklearn.metrics import accuracy_score

log_clf = LogisticRegression(solver = 'liblinear')
rnd_clf = RandomForestClassifier(n_estimators=10)
svm_clf = SVC(gamma='auto', probability=True) # SVM에서 기본값으로 범주에 속할 확률을 제공하지 않으므로 prob옵션 True로 줘야 간접투표 가능

voting_clf = VotingClassifier(estimators=[('lr',log_clf),
                                         ('rf',rnd_clf),
                                         ('svc',svm_clf)],
                             voting='soft') # voting 옵션에 soft 명시
voting_clf.fit(X_train, y_train)


for clf in (log_clf, rnd_clf, svm_clf, voting_clf):
    clf.fit(X_train, y_train)
    y_pred = clf.predict(X_test)
    print(clf.__class__.__name__, accuracy_score(y_test,y_pred))

# voting이 0.912로 상당히 높아짐
```

    LogisticRegression 0.864
    RandomForestClassifier 0.88
    SVC 0.888
    VotingClassifier 0.912
    

### 2. Bagging (bootstrap aggregating)
- 같은 모델에서 bootstrap(복원 추출)을 통해 여러개의 분류기를 만들고 결과를 voting하는 방식으로 집계(Aggregating)  
- 중복을 허용하여 샘플링하는 방식으로 각 트리가 사용하는 데이터를 일부 (약 2/3)로 제한(나머지 OOB[^1])  
- 같은 훈련 샘플이 여러 예측기에 사용될 수 있음(데이터 분포 왜곡)  
original: (x1,x2,x3,x4,x5,x6,x7,x8,x9,x10)  
bootstrap1: (x8,x6,x2,x9,x5,x8,x1,x4,x8,x2) / OOB: (x3,x7,x10)  
bootstrap2: (x10,x1,x3,x5,x1,x7,x4,x2,x1,x8) / OOB: (x6, x9)  
bootstrap3: (x6,x5,x4,x1,x2,x4,x2,x6,x9,x2) / OOB: (x3,x7,x8,10)  

[^1]:OOB(Out of bag)이란 training set에서 샘플링되지 않은 데이터를 validate set으로 사용

- Low Bias, High Variance에 효과적  
- ex) Random Forest


#### 2-1. Random Forest (Classifier, Regressor)  
여러개의 decision tree를 이용한 bagging  

- RandomForestClassifier (BaggingClassifier)  
최빈값으로 aggregating  
hard voting과 같으나, predict_proba를 추정할 수 있으면 자동으로 간접 투표 선택  

- RandomForestRegressor (BaggingRegressor)  
 평균으로 aggregating  

##### BaggingClassifier hyper parameter  

- N-estimator : 랜덤포레스트 안에 만들어지는 의사결정나무 개수. 트리가 많아지면 속도가 느려지고 너무 트리가 크면 오히려 정확도가 낮아진다. 그러나 일반적으론 트리가 많아질수록 분류를 잘하게 되므로 적절한 trade-off 필요하다.  

- Max-depth : 랜덤포레스트 안에 있는 각 의사결정나무의 깊이를 설정. 트리가 깊어질수록 더 잘게 분류를 시키므로 일반적으론 정확도가 높아진다.  

- Min-samples-split : 내부 노드에 데이터를 얼마 만큼씩 최소한 넣을 것인가 설정. 10%~100%로 설정. 100%로 갈수록 underffiting이 일어나서 정확도가 낮아진다.  

- Min-samples-leaf : 리프 노드에 데이터를 얼마 만큼씩 최소한 넣을 것인가 설정. 10%~100%로 설정. 100%로 갈수록 underffiting이 일어나서 정확도가 낮아진다.  

- Max-feature : 가장 잘 분류할 feature의 갯수를 설정  

- boot_strap : True(중복허용, 배깅), False(중복허용 X, 페이스팅)  

- n_jobs : 사용할 CPU 수(-1로 지정하면 가용한 모든코어 사용)  

_hyper parameter tuning library: GridSearchCV_  

##### 진행단계  
1) bagging  
1-1) bootstrapping  
1-2) 각 sample로 독립적인 모델 생성  
1-3) 모델 학습 후 집계  

2) 무작위 변수선택  

3) bagging, 변수 랜덤 선택을 통해 다양성 확보  

4) 변수 중요도 산출  






```python
# BaggingClassifier 분류 예제
from sklearn.model_selection import train_test_split
from sklearn.datasets import make_moons
import matplotlib.pyplot as plt
import numpy as np

X, y = make_moons(n_samples=500, noise=0.30, random_state=42)  # noise 키우면 달의 두께가 두꺼워짐
X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=42)

from sklearn.ensemble import BaggingClassifier
from sklearn.tree import DecisionTreeClassifier
from sklearn.metrics import accuracy_score

bag_clf = BaggingClassifier(DecisionTreeClassifier(), # 결정트리 분류기 선택
                           n_estimators=500, # 앙상블에 사용할 분류기 수
                           max_samples=100, # 무작위로 뽑을 샘플 수
                           bootstrap=True, # 중복허용
                           n_jobs=-1) # 사용 cpu 숫자 -1: max 코어 사용

bag_clf.fit(X_train, y_train)
y_pred = bag_clf.predict(X_test)
print(bag_clf.__class__.__name__,accuracy_score(y_test, y_pred))


# 결정트리 분류기 1개 학습
tree_clf = DecisionTreeClassifier(random_state=42)
tree_clf.fit(X_train, y_train)
y_pred_tree = tree_clf.predict(X_test)
print(tree_clf.__class__.__name__,accuracy_score(y_test, y_pred_tree))

# bagging이 0.928로 높게 나옴

```

    BaggingClassifier 0.912
    DecisionTreeClassifier 0.856
    

### 3. Pasting
중복을 허용하지 않고 샘플링하는 방식  
bagging은 각 예측기가 학습하는 subset에 다양성을 증가시키므로 bagging이 pasting보다 편향이 조금 더 높음  

### 4. Boosting
- 약한 학습기(Weak Learner)들을 결합해서 보다 정확한 강한 학습기(Strong Learner)를 만듦  
- weak learner에서 순서대로 일을하면 앞의 학습기에서 찾지 못한 부분을 추가적으로 찾을 수 있음  

- Low Bias, High Variance에 효과적
- ex) GBM, XGBoost, LightGBM


<br><br>
---

## Random Forest

- 주로 recommand, feature selection, image classification에 쓰임  
- 특정 특성을 선택하는 Decision Tree를 여러개 생성하여 이들을 기반으로 작업을 수행하는 것  
- 기존 앙상블의 bagging과 달리 하나의 변수의 상대적 중요도 측정 가능  
어떤 변수를 사용한 노드가 (전체 트리에 대해) 평균적으로 불순도를 얼마나 감소시키는지 확인하여 중요도 측정(=가중치 평균)  

### Single classification trees(decision tree) vs Random Forest

decision tree: 이미 학습된 데이터(과대적합 쉬움)에 대해 분류를 잘 하지만 새로운 데이터는 분류를 잘 하지 못함  
새로운 데이터는 random forest가 잘 함  
Tree Correlation이라고 하는 특정 feature 하나가 정답에 많은 영향을 주게되면 대부분의 결과치가 유사하게 나타나는 문제점  

random forest: decision tree (의사결정나무)들을 이용해서 가장 좋은 결과를 내는 tree 투표  

기존 rf에서 each tree가 자라는 과정  
1) training set이 N개면 한 나무에도 N개의 sample case가 만들어짐 (복원 추출로 여러 나무를 만들 수 있음(bagging))  
2) 입력변수가 M개가 있다면, 각 노드는 랜덤으로 M보다 작은 m개의 변수가 선택되어 나무가 자라는 동안 m은 변하지 않음  
여기서 문제점은 두 tree간에 correlation이 있을 수 있으므로, 확실한 분류를 하는 낮은 에러율을 가진 tree에 중요도를 높여서 rf에러율을 줄일 수 있음  

trade off: 여기서 m을 줄이면 correlation과 strength 둘다 줄어듦



### importance
- importance = (현재 노드의 샘플 비율 x 불순도) - (왼쪽 자식 노드의 샘플 비율 x 불순도) - (오른쪽 자식 노드의 샘플 비율 x 불순도)  
- 전체 중요도의 합으로 나누어 정규화  
- 샘플 비율 = 해당 노드의 샘플수 / 전체 샘플수  
- Random Forest의 변수 중요도 = 각 결정트리의 변수중요도의 함 / 트리 수  

```python
from sklearn.datasets import load_iris
iris = load_iris()
rnd_clf = RandomForestClassifier(n_estimators=500,n_jobs=-1)
rnd_clf.fit(iris["data"], iris["target"])
for name, score in zip(iris["feature_names"], rnd_clf.feature_importances_):
    print(name, score)
```
- feature_importances_으로 확인 가능  




### 진행 단계

1) training data set에서 무작위로 중복을 허용하여 N개 선택 (bootstrap)  
2) N개의 data sample에서 data의 특성값(iris: petal width, height...) M개 중 중복 없이 m[^2]개 선택  
3) decision tree training  

4) 위 1)~3) tree 생성 과정을 k번 반복(bagging)  

5) 1)~4)단계를 통해 생성된 k(n_estimators)개의 decision tree를 이용해서 가장 좋은 결과를 내는 tree 투표(voting)  

[^2]: 보통 $ m = \sqrt{m} $ (m은 data의 전체 특성 개수)



### RandomForestClassifier Hyper parameter  
BaggingClassifier의 매개변수를 모두 가지고 있음  
- out-of-bag score: 예측이 얼마나 정확한가에 대한 추정을 수치로 나타낸 것

변경 불가 옵션:  
- splitter : 항상 best  
- presort : 항상 False  
- max_samples : 항상 1  
- base_estimator : 항상 지정된 매개변수를 사용한 결정트리 모델  



_코드 예제는 BaggingClassifier에 DecisionTreeClassifier를 넣어 앙상블을 만드는 것과 동일하나, 결정트리에 최적화되어 있는 RandomForestClassifier 사용_

[RandomForest 예제](https://www.blopig.com/blog/2017/07/using-random-forests-in-python-with-scikit-learn/)


```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn import datasets
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score

iris = datasets.load_iris()
df = pd.DataFrame(iris.data, columns=iris.feature_names)
# sklearn provides the iris species as integer values since this is required for classification
# here we're just adding a column with the species names to the dataframe for visualisation
df['species'] = np.array([iris.target_names[i] for i in iris.target])

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(df[iris.feature_names], iris.target, test_size=0.5, stratify=iris.target, random_state=123456)
```


```python

rf = RandomForestClassifier(n_estimators=100, oob_score=True, random_state=123456)
rf.fit(X_train, y_train)

predicted = rf.predict(X_test)
accuracy = accuracy_score(y_test, predicted)
print(f'Out-of-bag score estimate: {rf.oob_score_:.3}')
print(f'Mean accuracy score: {accuracy:.3}')
# 분류모델 성능 측정: Accuracy(정확도), Precision(정밀도), Recall(재현율), F1 Score, Fall-out
```

    Out-of-bag score estimate: 0.96
    Mean accuracy score: 0.933
    


```python
# RandomForestRegressor 회귀 예제

from sklearn.datasets import load_boston
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import r2_score
from scipy.stats import spearmanr, pearsonr

boston = datasets.load_boston()
features = pd.DataFrame(boston.data, columns=boston.feature_names)
targets = boston.target

X_train, X_test, y_train, y_test = train_test_split(features, targets, train_size=0.8, random_state=42)
# scaler = StandardScaler().fit(X_train)
# X_train_scaled = pd.DataFrame(scaler.transform(X_train), index=X_train.index.values, columns=X_train.columns.values)
# X_test_scaled = pd.DataFrame(scaler.transform(X_test), index=X_test.index.values, columns=X_test.columns.values)
```


```python
rf = RandomForestRegressor(n_estimators=500, oob_score=True, random_state=0)
rf.fit(X_train, y_train)

predicted_train = rf.predict(X_train)
predicted_test = rf.predict(X_test)
test_score = r2_score(y_test, predicted_test)
spearman = spearmanr(y_test, predicted_test)
pearson = pearsonr(y_test, predicted_test)
print(f'Out-of-bag R-2 score estimate: {rf.oob_score_:>5.3}')
print(f'Test data R-2 score: {test_score:>5.3}')
print(f'Test data Spearman correlation: {spearman[0]:.3}')
print(f'Test data Pearson correlation: {pearson[0]:.3}')
# 회귀모델 성능 측정: MAE(Mean Absolute Error), MSE(Mean Squared Error), RMSE(Root Mean Squared Error), MAPE(Mean Absolute Percentage Error), R2 Score(Coefficient of Determination, 결정계수)
```

    Out-of-bag R-2 score estimate: 0.841
    Test data R-2 score: 0.883
    Test data Spearman correlation: 0.903
    Test data Pearson correlation: 0.94
    

#### Random Forest의 Feature Importance 시각화


```python
import seaborn as sns
%matplotlib inline

rf_reg = RandomForestRegressor(n_estimators=1000)

# 앞 예제에서 만들어진 X_data, y_target 데이터 셋을 적용하여 학습합니다.   
rf_reg.fit(features, targets)

feature_series = pd.Series(data=rf_reg.feature_importances_, index=features.columns )
feature_series = feature_series.sort_values(ascending=False)
sns.barplot(x= feature_series, y=feature_series.index)
```

<br><br>
---
    



```python

```
