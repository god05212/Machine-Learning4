# Titanic
## 타이타닉 생존 예측 분석하기
프로젝트 기간: 2024/07/03(수) ~ 2024/07/08(월)  
<br/>
## 개요
해당 프로젝트는 타이타닉 데이터셋을 활용하여 탑승객의 생존 여부를 분석하고 예측하는 것을 목표로 합니다. 타이타닉호는 역사상 가장 잘 알려진 해상 재난 사건 중 하나로, 이 데이터셋은 타이타닉호의 승객 정보를 담고 있습니다.  

프로젝트의 목표는 다음과 같습니다.
1. 타이타닉 데이터를 기반으로 생존 여부를 예측하는 모델을 개발합니다.
2. 생존에 영향을 미치는 요인들을 분석하고 이해합니다.
3. 데이터 시각화와 기계학습 기법을 활용하여 탑승객 데이터를 탐색합니다.
4. 예측 모델을 통해 다양한 분석과 시뮬레이션을 수행하여 타이타닉 재난에서의 생존 가능성을 평가합니다.

이 프로젝트를 통해 타이타닉호의 데이터를 분석함으로써 역사적 사건을 이해하고, 기계학습 기법을 적용하여 예측 모델을 구축하는 경험을 쌓고자 프로젝트를 진행하게 되었습니다.
<br/>
## 사용한 데이터셋
Titanic.csv
<br/>

## 프로젝트 목록
1. Visualization and Analysis
2. Data Cleaning and Preprocessing
3. Feature Engineering
4. Modeling
5. Stacking
6. Final Model Evaluation
  
<br/>

## 프로젝트 수행 과정
1. 데이터 준비 및 초기 분석  
    - 데이터 셋을 로드하고 시각화를 사용하여 초기 탐색 데이터 분석(EDA)을 수행하여 생존여부, 승선항, 성별 및 객실 등급별 생존여부, 나이와 같은 특징의 분포와 관계를 파악했습니다.
    - missingno를 통해 결측치의 위치를 대략적으로 확인해보았습니다.
  
2. 피처 엔지니어링 및 선택
    - 이름에서 호칭을 추출하여 개인을 Officer, Royalty, Mrs, Miss, Mr, Master와 같은 범주로 그룹화합니다.
    - SibSp와 Parch를 결합한 Family Size 기능을 만들었습니다.
    - 티켓 기능에서 숫자 부분을 추출하고, 분석에 필요한 정보를 찾지 못해서 제거하였습니다.
    - 인터넷으로 타이타닉 구조를 검색한 후 pclass와 타이타닉의 층수를 기준으로 Cabin을 나누고, 결측치는 N으로 처리하였습니다.
    - 데이터프레임의 특성들 간의 상관 관계를 히트맵으로 시각화하여 각 특성이 다른 특성들과 어떤 관계를 가지는지 확인하였습니다.
  
3. 데이터 전처리
    - 나이(평균값), cabin(N), embarked(최빈값) 결측치를 처리하였습니다.
    - Pipeline 및 ColumnTransformer로 수치형 변수는 이상치에 민감하지 않도록 MinMaxScaler를 사용하여 정규화하고, 범주형 피쳐는 OneHotEncoder를 사용하여 데이터를 일관되게 전처리하였습니다.
  
4. 모델 선택 및 학습
    - 여러 분류기(RandomForest, XGBoost, LogisticRegression, 'GradientBoosting)를 선택하고 models로 묶어 for문을 통해 성능을 한번에 평가했습니다.
    - 성능을 시각적으로 표현할 수 있는 ROC-AUC Score 및 classification_report를 사용하여 평가했습니다.
    - 기본 성능이 가장 좋았던 GradientBoostingClassifier의 하이퍼파라미터 튜닝을 위해 RandomizedSearchCV를 적용했습니다.(그리드서치를 사용해보았으나 느려서 랜덤 서치 사용함.)
  
5. Stacking
    - vecstack 라이브러리의 Stacking Transformer를 사용하여 구현했습니다.
    - ExtraTreeClassifier, Random ForestClassifier 및 XGBClassifier로 X_train, y_train에 대해 3가지 모델들을 학습시켰습니다.
    - 2단계 모델 GradientBoostingClassifier를 학습시키고 예측한 후 성능을 확인해 보았습니다.
<br/>

## 모델의 test dataset에 대한 ROC-AUC Score (소수점 다섯째 자리에서 반올림) 
| Model | ROC-AUC Score |
|:--------------------------------------------|:-------|
| RandomForestClassifier                      | 0.8779 |
| XGBClassifier                               | 0.8701 |
| LogisticRegression                          | 0.8718 |
| GradientBoostingClassifier                  | 0.8814 |
| Gradient Boosting with Hyperparameter Tuned | 0.8680 |
| Stacking                                    | 0.8175 |
<br/>

## 최종 모델
GradientBoostingClassifier
- test dataset에 대한 결과
  - ROC-AUC Score: 약 0.8814
<br/>

## 결론
스태킹 적용 결과 모델 성능이 더 낮아짐을 알 수 있다. (데이터의 수가 적어서 그럴 가능성 있음.)

현재 우리가 사용했던 모델 중에서는 그래디언트부스팅이 가장 성능이 높음을 알 수 있다.

## 향후 보안할 점
age열의 결측치를 랜던포레스트와 그리드서치를 통해 채우려 하였으나 성능이 안좋았고, 다른 열들을 수정해보았으나 성능은 변함이 없었다. 마지막으로 age 열의 결측치를 평균값으로 채웠더니 성능이 좋아졌다.

시간이 부족해서 더 찾아보지 못했지만 추후 결측치를 모델을 통해 채우면서 성능을 높일 수 있는 방법을 찾아봐야겠다.

![image](https://github.com/god05212/Machine-Learning4/assets/83646397/0c4f1414-d4db-4538-9979-7491f2ade067)
![image](https://github.com/god05212/Machine-Learning4/assets/83646397/982f1065-f55b-4c99-88a5-0af92d06f65f)
![image](https://github.com/god05212/Machine-Learning4/assets/83646397/c750f7bf-cea3-4149-b60c-46de0ee4e6a4)
![image](https://github.com/god05212/Machine-Learning4/assets/83646397/e32fe206-bdd3-4acc-8318-7747d9eaba83)
![image](https://github.com/god05212/Machine-Learning4/assets/83646397/125fa66c-97db-45f5-81bb-825a22a035a0)
![image](https://github.com/god05212/Machine-Learning4/assets/83646397/f35d8c7a-0cac-4eac-be24-7b7eb5cf94cb)
