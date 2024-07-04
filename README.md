# Oil_Extraction_Anomaly_Detection

## DataSet 개요
### 데이터셋 정보
해양 유정에서 관측된 이상 징후(Anomalies)가 포함된 시계열 데이터로, 한 csv 파일 당 6,000초 ~ 60,000초 정도 측정함.
![image](https://github.com/Felix-Silas/Oil_Extraction_Anomaly_Detection/assets/84503487/a8e7505b-69c2-41eb-9247-c7cc1c7e197b)

### 변수  
1. P-PDG (Unit: Pa): Pressure at permanent downhole gauge  
2. P-TPT (Unit: Pa): Pressure at temperature/pressure transducer  
3. T-TPT (Unit: °C): Temperature at temperature/pressure transducer  
4. P-MON-CKP (Unit: Pa): Pressure upstream of production choke  
5. T-JUS-CKP (Unit: °C): Temperature downstream of production choke  
6. P-JUS-CKGL (Unit: Pa): Pressure upstream of the gas lift choke  
7. T-JUS-CKGL (Unit: °C): Temperature upstream of the gas lift choke  

### 8가지 이상
1. BSW(Basic Sediment & Water): 불순물의 농도가 높아지게 되면 TPT 센서의 압력과 온도가 민감하게 반응한다.  
2. Spurious Closure of DHSV: Oil 추출 밸브 오작동으로 관의 Oil 유량이 줄어들어 온도, 압력이 줄어든다.  
3. Severe Slugging: Oil과 공기가 같이 섞여서 들어오는 상황으로, 모든 센서에서 불안정한 패턴이 관측된다.  
4. Flow instability: 불안정한 흐름에 의한 이상으로, 다양한 원인들이 복합적으로 나타나는 상황이다.  
5. Rapid Productivity Loss: 기술적인 문제로 인해 생산성이 떨어지는 상황으로, 특정 요인을 파악하는 것이 힘들다.  
6. Quick Restriction in PCK: 작업자의 PCK 밸브 조작 미숙으로 인해 발생하는 상황으로, PCK 센서의 압력은 증가, 온도는 감소한다.  
7. Scaling in PCK: PCK 관 안에 침전물이 쌓이기 시작해 관 내부에 있는 센서들이 민감도를 잃고, 센서 값이 요동친다.  
8. Hydrates Formation: 탄소와 얼음이 섞인 수화물이 형성되어 PCK관에 choking이 발생해 압력과 온도가 급격히 감소한다.  

![image](https://github.com/Felix-Silas/Oil_Extraction_Anomaly_Detection/assets/84503487/bb38b38c-b823-4bd1-b127-6bdd2dde136f)
![image](https://github.com/Felix-Silas/Oil_Extraction_Anomaly_Detection/assets/84503487/b27b114d-a062-4bf8-a0a2-8c38b228d39c)

## 모형 계획
### 현 상황
1. 선행 논문 다수 존재: 1개 클래스에 대해 이상 탐지를 하는 경우, 선행 연구가 충분히 많으므로 경쟁력 부족.  
2. 연산량, 복잡도 고려: 해커톤 당일 기관에서 제공하는 제한된 분석플랫폼 상에서 모든 팀이 구현을 진행해야 하므로 연산량과 복잡도를 최소화해야 함.  
3. GPU 사용 불가: 다중 이상 탐지에 적합한 모형들은 대부분 GPU가 필수적으로 필요함. 하지만 해커톤 당일 GPU 사용 불가능.  
--> 이를 해결하기 위해 특정 클래스에 필요한 특성만 고려하여 개별 이상 탐지기 구현한 후, 하나의 다중 클래스 이상 탐지기로 통합 

### 실제 구현 모델
![image](https://github.com/Felix-Silas/Oil_Extraction_Anomaly_Detection/assets/84503487/5e75240a-920a-489c-9401-e53822ef4ccd)

## 모델링
### 실행환경
Google Colab 및 개인 노트북의 CPU 환경에서 실행함

### 사용 라이브러리
matplotlib : 3.5.1  
numpy : 1.21.5  
pandas : 1.4.2  
seaborn : 0.11.2  
sklearn : 1.0.2  		
lightgbm : 4.0.0  
xgboost : 1.7.6   

### 개별 이상 탐지기
#### [1] Class 3
##### 데이터셋 특징 
1. 전이 과정 없음  
2. 패턴이 나타나면 이상, 나타나지 않으면 정상. 
##### 전처리 방식
1. 결측치 포함된 행 제거
2. (10000초)*(4개의 변수)를 flatten해서 40000개의 컬럼으로 변환  
3. 100초 단위로 다운 샘플링  
##### 사용 모형
OCSVM  
##### 모형 선정 이유
1. 다변량 시계열 데이터를 입력으로 받음  
2. 준지도학습이 가능한 모형   
##### 성능
F1-Score: 0.9286  
실행 시간: 2초  

#### [2] Class 1
##### 데이터셋 특징 
1. 압력은 서서히 감소하고 온도는 서서히 올라가기 시작하면 이상으로 판단.  
2. TPT 변수 이외에는 일관성 있는 특징 존재하지 않음.
##### 전처리 방식
1. TPT 이외 컬럼 모두 제거  
2. 전이 과정 대부분을 이상으로 처리 (10%는 정상 클래스, 나머지 90%는 이상으로 전환) --> 조기 이상 탐지 가능  
##### 사용 모형
XGBoost
##### 모형 선정 이유
1. 분류 & 회귀에서 좋은 성능을 발휘  
2. 병렬 처리로 학습하므로 자원 적게 소모  
##### 성능
F1-Score: 0.9836  
실행 시간: 20초  

#### [3] Class 2, Class 6, Class 8
##### 데이터셋 특징 
1. 정상과 이상이 확연히 구분
2. 전이 이상 값이 너무 많음
3. 온도와 압력이 감소되는 것이 특징  
##### 전처리 방식
1. 이전 값으로 결측치 대체  
2. 5초 단위로 다운 샘플링
3. 전이과정은 모두 정상으로 대체
4. RobustScaler 표준화  
##### 사용 모형
LightGBM
##### 모형 선정 이유
1. 대용량 데이터 처리에 용이함  
2. 더 적은 메모리로 바르고 우수한 결과
3. 모형 자체 결측치 처리    
##### 성능
F1-Score: 0.8811  
실행 시간: 13초  

#### [4] Class 7
##### 데이터셋 특징 
1. 일정한 값이 지속되거나 결측값으로 구성된 컬럼 존재  
2. 온도와 압력이 굉장히 진동함  
3. P-JUS-CKGL 변수 선형으로 증가하는 패턴 발견  
##### 전처리 방식
1. 일정한 값이 지속되거나 결측값으로 구성된 컬럼 제거
2. 전이 과정을 정상과 이상 데이터의 비율에 따라 재분배  
3. StandardScaler 표준화  
##### 사용 모형
SGDClassifier
##### 모형 선정 이유
1. Class 7의 데이터 값들은 정상과 이상을 선형적으로 구분 가능하다고 판단
2. 미니 배치 학습을 지원하여 대용량 데이터 학습에 용이함
##### 성능
F1-Score: 0.9217   
실행 시간: 15초 

## 결론
### 성능 평가
![image](https://github.com/Felix-Silas/Oil_Extraction_Anomaly_Detection/assets/84503487/913c1745-e87e-4b39-aa9f-2a037796e78f)

### 기대 효과
1. 동시에 발생한 이상 탐지 가능  
- 1, 2, 3, 6, 7, 8 총 6개의 이상탐지기가 존재한다.  
- 이상탐지기로 한 개의 이상만을 판단하는 것이 아니라, 동시에 발생한 비정상적인 이상을 모두 탐지할 수 있다.  
- 따라서 각 이상에 맞는 조치를 빠르게 취할 수 있다.  

2. 기존 논문보다 향상된 성능 보유  
- 대부분의 논문에서 1가지 이상을 탐지하는 단일 이상 탐지기를 구현하는 것을 중점적으로 다루었다.  
- 그러나 우리가 제안한 모형은 최종적으로 6가지 이상을 분류할 수 있다.  
- 도메인 분석을 기반으로 각 이상에 맞게 모형을 개발하여 기존 연구에서 제시된 f1-score보다 향상된 성능을 보인다.  

3. 대회 주최기관의 실행 환경에서 구현 가능  
- 최대한 컴퓨팅 자원을 사용하지 않기 위해 딥러닝보다 머신러닝 기법에 치중하여 모델을 구현했다.  
- 모형 복잡도를 최소화하여 CPU 환경에서 이용할 수 있도록 구현했다.  
- GPU를 활용하지 않는 Web 기반 플랫폼 환경에서도 정확하고 빠른 이상 탐지가 가능할 것으로 판단된다.   
