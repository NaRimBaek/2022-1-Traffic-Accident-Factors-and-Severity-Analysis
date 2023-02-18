# Traffic-Accident-Factors-and-Severity-Analysis
교통사고 발생 요인 및 심각도 분석 


# 목차
- 주제선정 배경
- 분석데이터 EDA
- 데이터 전처리 및 분석 방안
- 분석 결과
- 의의 및 한계점


# 주제선정배경

2021년 10월 기준 우리나라 인구 10만명당 교통사고 사망 자는 OECD 국가 평균인 5.6명(18년 기준)을 상회하는 6.0 명에 달하며, 
특히 "보행자 보호 측면"에서는 2018년 기준 인구 10만명당 보행 중 사망자수는 2.9명으로 OECD 회원 28개국 가운데 27위를 기록해 최하위권이다.

도로교통안전백서에 따르면 교통사고는 운전자, 차량, 도로 및 환경의 복합요인에 의하여 발생한다. 
이중 날씨와 기상요인은 일차적으로 도로 환경에 큰 영향을 준다
따라서 기상 요인과 교통사고 발생 간의 연관성을 분석하고, 그 피해를 정량화하여 정책적으로 반영할 필요성이 매우 높다.


## 기존 연구의 한계점
국내에서는 경찰청에서 발간하는 “교통사고 통계” 자료가 기상에 따른 교통사고를 원인 별로 제공하지만, 
변수가 단순하여 위험의 상관성을 분석하고, 예측에 사용하기에는 어려움을 따른다. 

더불어 많은 교통 사고 연구들은 통계적으로 단순한 모형(단순회귀분석)을 사용하거나, 딥러닝 모형(LSTM, DNN등)을 사용하여 결과를 보였는데, 
이는 변수에 대한 정확한 해석을 방해하게 된다.


## 분석 목적

따라서 본 연구에서는 국내 교통사고 자료를 이용해서 교통사고 정보, 그리고 다양한 기상요인, 교통정책의 변화가 
교통사고 발생과 사망, 부상에 미치는 영향을 변수에 대한 정확한 해석이 가능한 통계적 기법들을 위주로 분석하여 정량적인 평가를 시도하였다.


## 분석데이터 EDA
1. 도로교통공단의 전국 개별 교통사고 정보(2015~2018)
https://www.data.go.kr/data/15094176/fileData.do?recommendDataYn=Y 
2. 기상청의 종관기상관측자료(ASOS)
https://data.kma.go.kr/data/grnd/selectAsosRltmList.do?pgmNo=36 


## 파생 변수 생성
- 발생시간분류 : Rush hour를 반영한 파생변수
- 난폭운전 : 난폭운전 개정안 시행 여부(0: 시행 전,1:시행후)
- 보복운전 : 보복운전 개정안 시행 여부(0: 시행 전,1:시행후)
- 단속카메라 : 단속카메라 개정안 시행 여부(0: 시 행전,1:시행후)


# 데이터 전처리 및 분석 방안 

## 데이터 전처리
기상 데이터는 EDA 결과, 시계열 추세를 보이기 때문에 cubic spline 보간법으로 결측치를 대체하였다. 

## 분석방향

교통사고의 인명피해 및 물질적 피해 규모를 나타내는 EPDO 변수의 예측을 위해 6개의 regression model을 생성, 비교하였다. 
- Multiple Linear Regression
- Poisson GLM
- NB GLM
- XGBOOST
- CATBOOST
- LGBM

그리고 경찰청 교통사고통계를 바탕으로 Target(사고 규모) 분류를 위해 3가지 classification model을 생성, 비교하였다. 
- XGBOOST
- CATBOOST
- LGBM

사용한 모델들은 glm, boosting 계열 모델들로, 변수들의 해석이 가능하다.


# 분석결과

- REGRESSION 

• 피해자 및 가해자의 차량 종류, 도로종류 등이 boosting 모델에서 공통적으로 중요하다

• 기상관련변수보다 교통관련변수가 중요한 변수로 추출되었다.



- Target Classification

• Imbalanced data에서 majority class로 예측을 하는 경향이 있듯이 분석결과 0 label로 대다수가 분류하였다.

• 피해자과 가해자의 차량 종류가 중요한 변수이고, 날씨 변수 중에서는 풍속이 공통적으로 중요하다고 나타났다. 


# 의의 및 한계점 

## 의의
• 교통사고의 인명피해 및 물질적 피해 규모를 나타내는 EPDO EPDO가 높은 곳을 예측하여 사고 발생 사전에 예방

• 교통사고 예방에 필요한 정책 제안 및 개정안의 타당성에 대한 통계적 검정이 가능


## 한계점 & 개선점
• 심각한 사고를 발생시키는 요인 (음주 여부, 무면허) 에 대한 데이터의 부재

• 사고발생 확률에 기반하여 보험액을 산정할 때,연령 등 인구통계정보가 유의미하다고 알려져 있지만, 가해자에 대한 정보부족으로 어려움이 존재

        -> 가해자에 대한 정보가 좀 더 추가된다면, 예측모델의 성능 향상을 기대해 볼 수 있다.
  
• classification의 경우, 사망자가 없는 사고, 사망사고(사망자 1-2명), 대형사고(사망자 3명이상, 사상자 20명 이상)로 분류하다보니, 대부분 majority로 예측해버리는 데이터 불균형 문제가 심각했다. 
  
        -> classification target을 sampling 기법을 통해, 어느정도 데이터 불균형을 해소한 후, model을 생성하면 성능 향상을 기대해 볼 수 있다. 


## 내가 기여한 점

- regression과 classification에 대한 Model들을 구현하고, 비교 분석한 부분을 담당하였다.  


