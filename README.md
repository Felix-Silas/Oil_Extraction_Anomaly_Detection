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
