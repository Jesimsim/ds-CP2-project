# ds-CP2-project


![header](https://capsule-render.vercel.app/api?type=rect&color=_4C4C4B&height=200&section=header&text=Churn%20Classification&fontSize=50&fontColor=ECEA6E&desc=%5B%EA%B8%B0%EC%97%85%EC%97%B0%EA%B3%84%5D%201%EC%9D%B8%20%ED%81%AC%EB%A6%AC%EC%97%90%EC%9D%B4%ED%84%B0%20SNS%20%ED%94%8C%EB%9E%AB%ED%8F%BC%20%EC%84%9C%EB%B9%84%EC%8A%A4%EC%9D%98%20%EC%9C%A0%EC%A0%80%20%EC%9D%B4%ED%83%88%EA%B0%90%EC%A7%80%20%EC%8B%9C%EA%B3%84%EC%97%B4%EB%AA%A8%ED%98%95%20%EC%84%A4%EA%B3%84%20%EB%B0%8F%20%EC%84%B8%EA%B7%B8%EB%A9%98%ED%85%8C%EC%9D%B4%EC%85%98&descSize=17&descAlignY=72)


※ 기업연계 MBA 상 코드 기재가 어려움을 양해부탁드립니다


## 프로젝트 배경

### 프로젝트 목적 및 배경
- 이탈감지 시계열모형을 구축하여 셀럽의 활동로그를 통해 이탈자를 사전에 파악하여 선제적으로 대응하고자 함.
- 또한 시계열 모형을 해석(XAI)하여 각 지표에 영향을 미치는 요소를 파악함.
- 더 나아가 이탈확률 * 스티커확률에 대한 시계열 모형을 구축하여 각 확률에 따라 관리우선점수 및 관리우선대상군을 부여하고, 관리우선대상군과 비대상군의 통계량을 검정하여 각 지표와 연관된 요소를 파악함

### 프로젝트 의의
- 로그를 통해 이탈군을 파악하여 이탈군 대상 리텐션 관리방안 모색
     - user-fit 마케팅(이탈군 전용 할인정책), CRM시스템, 새로운 기능 구현(push메시지) 등
     - 최소한의 자원으로 리텐션을 유지할 수 있음.
- 이탈율 및 스티커여부와 상관관계가 높은 요소를 파악하며 내부 지표로 살필 수 있음.
- 서비스에 부가가치를 창출하지만 이탈 가능성이 높은 관리우선대상군의 추가적인 특징을 파악하여 내부 지표로 살필 수 있음.

---


## 프로젝트 프로세스
### 전체 프로세스
```EDA 및 Preprocessing > 모델 설계 및 선정 > 모델 결과 및 해석 > 관리우선대상군 분류 및 특징 파악```

### 팀 구성
- 본인 : 데이터 EDA 및 Preprocessing, 모델 실험 및 튜닝,  XAI, 유저 세그멘테이션 및 t-test를 통한 해석
- 팀원 1, 2 : 데이터 EDA 및 Preprocessing
- 팀원 3 : 데이터 EDA 및 Preprocessing, 모델 튜닝
- 팀원 4 : 데이터 EDA 및 Preprocessing + 모듈화, 모델링 및 XAI 방법론 제안 및 모델 설계


### 수행한 Task

**데이터 EDA 및 Preprocessing**
- 셀럽 비대상(자기방활동이 없거나 셀럽리스트에 존재하지 않는 유저) 제거
- statsmodels.tsa의 분해시계열을 통한 유저의 로그 사용패턴 분석
- 프로세스 마이닝 및 생키다이어그램을 통한 유저의 서비스 이용프로세스 분석

**모델 설계**
- 데이터 스케일링, 레이어에 따른 모델 결과 실험(GRU, BiLSTM, GRU-BiLSTM) 
- 베이지안 서치를 통한 하이퍼파라미터 튜닝 후 모델 성능 개선

**모델 해석**
- 다중공선성 제거 및 Boruta-SHAP 알고리즘을 활용하여 통게적으로 유의하지 않은 변수 제거
- 이탈모형과 스티커모델 각각의 Surrogate Model 구축 후, feature importance 및 SHAP 결과 도출 및 해석

**유저 세그멘테이션(관리우선대상군 및 비대상군)**
- 관리우선점수 부여 : 이탈확률 * 스티커확률에 따른 분포 확인(log를 취해 왜도 완화) 후 임계치 선정
- T-test를 통한 대상군과 비대상군의 각 변수별 표본평균(T통계량) 차이 확인

---

## 데이터셋 

- **제공** : 일리오 
- **데이터 형태 및 집계방식** : 프론트엔드를 통해 트래킹된 셀럽 및 팬의 로그데이터, 땡큐스티커 후원 로그, 셀럽 고유값 리스트
- **데이터 크기** : 20주차(22.11.10~23.03.28), 38,638개(Train 27,536/Val 6,885/Test 4,217) 
- **설명** : Raw 로그 데이터(5,186,966개)에서 비대상(자기방활동이 없거나 셀럽리스트에 존재하지 않는 유저) 제거 후 유저별 일일 집계 및 7일+7일로 슬라이딩하여 총 38,638개 생성

- **피처데이터(X)** : 일일 셀럽의 자기방에서의 각 이벤트별 활동 집계, 팬의 이벤트별 활동 집계, 땡큐스티커 내역 및 합계
- **라벨데이터(y)**  : 이탈율(7일간의 자기방활동여부, 이진분류), 스티커여부(7일간 받는 여부, 이진분류)
- **데이터 비율** : 이탈율 Train 0.36, Val 0.36, Test 0.33 / 스티커여부 Train 0.09, Val 0.10, Test 0.13

---

## 모델 및 기법

![image](https://user-images.githubusercontent.com/114756802/234031949-7ff9bfaa-6c83-466e-8b88-ecb9ac64a2c3.png)


#### Attention-based GRU-BiLSTM
    - GRU-BiLSTM을 조합하여, 빠른 학습속도를 보이는 GRU와 특정 타임스텝에서 이전 이후의 정보를 양방향으로 학습하는 BiLSTM의 장점을 결합함
    - Attention 메커니즘을 활용하여 중요 정보를 선택/강조함
     
     
![image](https://user-images.githubusercontent.com/114756802/234029333-4e62fe68-bbf3-4e28-ab95-ab579960fbca.png)


#### SHAP by Surrogate Model
    - 음과 양 영향력 둘 다를 알 수 있는 SHAP 분석을 활용함
    - 딥러닝 모델의 경우 아주 많은 시간(>5,000h)을 소요하여, 트리 기반의 전역적 대리모델을 활용하여 SHAP 수행함

#### 다중공선성 제거 및 Boruta-SHAP 활용
    - SHAP value의 유용성을 높이고 변수 중요도가 분할되어 under-estimated 되지 않도록 두 가지 방법론을 활용함
    - 설명력이 낮은 변수 및 다중공선성(어떤 변수가 다른 독립변수의 조합으로 표현될 수 있는 경우)가 높은 변수를 제거
    - Boruta-SHAP을 통해 통계적으로 유의한 변수만으로 기존모형 모사
     

---

## 프로젝트 결과

### [ 모델 성능 ]
**이탈모델** : accuracy 0.87, microAP 0.94
- Baseline Model(최빈모델)과 비교했을 때 높은 성능을 보임.
- 타 ML모델과 비교했을 때, LGBM과는 accuracy는 비슷했으나 microAP는 높은 등 이탈을 보수적으로 판단하는 경향을 보임
- 최소한의 자원으로 리텐션을 유지하는데 관심 있는 것을 고려했을 때 보수적인 모델을 채택함

**스티커모델** : microAP 0.94, accuracy 0.88
- 데이터 불균형을 고려해 microAP를 채택했을 때, Baseline model 및 타 ML 모델에 비해 월등한 성능을 보임

### **[ 해석XAI ]**
- Surrogate Model의 유사도는 이탈모델 accuracy 0.96, 스티커모델 microAP 0.94의 정확도를 보임.
- Feature Importance 및 SHAP을 활용하여 이탈율과 스티커여부에 영향을 미치는 로그이벤트와 일일현황이 무엇인지 파악하고, 이탈율과 변수 간의 상관관계(비례, 반비례) 등을 파악함.
- 특히 두 모델에 공통적으로 중요한 로그이벤트가 팬과 관련된 활동임을 확인하고, 리텐션 유지를 위해 팬의 영향력이 큼을 시사함

### **[ 관리우선대상군 부여 및 특징 파악 ]**
- 모델 해석에 도출된 변수(지표) 외에도, 일반이탈군과 비교되는 추가적인 통계적 특징을 포착함

### **[ 피드백 ]**
- 실제 이탈에 영향을 미치는 변수들을 객관적으로 확인하고, 요청 사항(이탈모형) 외 스티커, 세그먼테이션 등의 과제를 수행하여 더 나은 서비스 운영에 도움이 될 듯 함.
- 패키징된 코드를 활용하여 기업 내부에서 더 효율적인 서비스 운영을 위해 활용될 예정 

---

## 한계점 및 해결방안

- 코호트 분석 등 이탈군 분석 고도화 
- 세그멘테이션 고도화 방법론 모색 
- 도메인/마케팅방법론 등 다양한 지식을 바탕으로 EDA 및 모델 결과 해석
- 이상탐지모델로서의 가능성 탐색
- 딥러닝 모델의 장점 상 다중공선성 높은 컬럼을 넣어도 잘 해석하지만, 제거하고 학습했을 때의 결과도 실험해봤으면 좋았을 것  
