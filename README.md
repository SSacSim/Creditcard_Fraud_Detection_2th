## 신용카드 사기 거래 탐지 AI 경진대회
대회 URL: https://dacon.io/competitions/official/235930/overview/description

주최/주관 : 데이콘

진행 기간 : 2022.07.01 ~ 2022.08.05

대회설명:
  * 방대한 신용카드 사용 데이터에서 극히 일부인 사기 거래를 탐지할 수 있는 AI 모델 개발(Anomaly Detection)

대회 목표:
  * 30개 feature, 비식별화된 데이터를 이용하여 사기 거래 여부를 판단하는 모델 개발
  * macro-f1 score 평가지표를 이용하여 불균형 데이터에서의 모델 성능 평가 및 모델 성능 개선

대회 결과 :
  * 이상치에 Robust한 MCD(Minimum Covariance Determinant)모델을 사용하여 Unsupervised task를 Supervised task로 변경하여 추가적으로 머신러닝을 이용하여 Classification 진행
  * 🏆대회 최종 2등 달성(Private 3등)
 * * *
 ## 대회 진행 환경
 프로젝트 진행 환경 : Google Colab
 
 팀구성 : 2명
 
 역할 :
  * 데이터 분석
    * feature별 IQR값을 살펴보고 이상치 확인 → 단, task의 특성상 삭제 X
  * 모델 개발
    * AutoEncoder , Iforest , mcd 등 다양한 이상치 탐지 모델 시도
    * mcd 모델을 이용하여 Unsupervised task → Supervised task로 변경 ( 데이터 레이블링 )
    * Supervised task로 변경 후, Logistic Regression, CatBoost 모델 앙상블 진행
* * *
## 진행 상세 과정
1. 데이터 분석
 * 모든 데이터와 feature명이 개인정보 문제로 비식별화 되어있어 파생변수 생성 x.
 * Feature별  IQR을 벗어나는 이상치가 다수 존재하였으나, 대회 특성상 중요한 역할을 할 수 있다는 판단하에 제거하지 않음.
 * Validation에서 사기 비율 체크, 약 0.001055%

2. 문제 접근 방식
 * Validation data set의 통계치 & 평가 데이터로 활용하여 이상치 탐지 모델 학습
 * 임의의 레이블을 생성 후 Classification 진행
  
3. 모델링
 * Validation 데이터를 평가지표 삼아 가장 성능이 좋은 이상치 탐지 모델 선택(MCD).
 * Validation 이상치 통계치를 이용하여 Train 데이터에서 이상치 값 N개 임의 설정
 * MCD를 이용하여 마할라노비스 거리 중 가장 멀다고 판단된 N개를 이상치로 임의 레이블링
 * 레이블링된 Train 데이터 셋을 이용하여 Classification task 진행

4. 성능 향상 방법
 * Validation 통계치를 이용하여 Train Data Set에 이상치가 120개라 판단. 
    하지만, 120개로 고정하지 않고, ±5 범위로 폭넓게 실험하여 최적의 값 118 적용.
 * Test Set 평가에 Validation의 이상치 비율을 2~2.5배를 적용
    * macro-f1 score를 평가지표로 사용하기때문에, 기존 통계치보다 범위 확장
 * 마할라노비스 거리가 가장 먼 K개의 데이터만 이상치로 판단
 * lr과 catboost 두 모델을 하드보팅 앙상블하여, 비정상 거래 판단 
