# :computer: **노트북 가격 예측 분석**
* **진행 기간** : 2022. 03. 17 ~ 2022. 03. 23

### &nbsp;

## :books: 사용 Skill
* **Google Colab**
* **Python 3.7.13**
  * Feature Engineering : pandas, numpy
  * 데이터 시각화 : matplotlib, seaborn
  * 모델링 : category_encoders, scipy, sklearn, xgboost
  * 모델 해석 : eli5, pdpbox, shap
* **ML Models**
  * Decision Tree Regressor
  * Random Forest Regressor
  * XGBoost Regressor

### &nbsp;

## :pushpin: Contents
* :one: **프로젝트 진행 배경**  
* :two: **데이터 선정 & Feature Engineering**  
* :three: **데이터 시각화**  
* :four: **모델링**  
* :five: **모델 해석**  
* :six: **결론**
* :seven: **한계점**

### &nbsp;


## :one: 프로젝트 진행 배경
* 노트북을 구매하는데 있어서 업그레이드할 수 있는 여러가지 스펙(ex. Ram, 메모리, 화면의 크기)이 존재
* 대부분의 사람들이 제한된 예산 내에서 어떤 스펙을 업그레이드해야 합리적인지 알지 못해 어려움을 겪음
* 따라서 노트북에서 선택할 수 있는 옵션별로 가격에 미치는 영향을 파악하면 소비자 입장에서 합리적으로 노트북 스펙을 업그레이드하는데 참고 가능

### &nbsp;

## :two: 데이터 선정 & Feature Engineering
* **출처** : [Kaggle - Laptop Price DataSet](https://www.kaggle.com/datasets/muhammetvarl/laptop-price)
* **데이터 수** : 1303
### **Feature Engineering 이전 - (1303, 12) / (rows, columns)**

<img width="1101" alt="스크린샷 2022-09-01 오후 2 17 03" src="https://user-images.githubusercontent.com/97662174/187836816-e72ea3c2-4313-44e6-9947-76ec1aea5275.png">

|Feature|Description|Data Type|
|:---:|---|:---:|
|Company|노트북 제조사 (ex. Apple)|Categorical|
|Product|노트북 모델명 (ex. Macbook Pro)|Categorical|
|TypeName|타입 (ex. Ultrabook)|Categorical|
|Inches|스크린 사이즈 - 단위 : inch (ex. 13.3)|Numerical|
|ScreenResolution|스크린 해상도 (ex. IPS Panel Touchscreen 2400x1600)|Categorical|
|Cpu|Cpu 옵션 (ex. Intel Core i7 2.2GHz)|Categorical|
|Ram|Ram 옵션 (ex. 8GB)|Categorical|
|Memory|메모리 옵션 (ex. 256GB SSD + 2TB HDD)|Categorical|
|Gpu|Gpu 옵션 (ex. Intel Iris Plus Graphics 640)|Categorical|
|OpSys|OS 시스템 (ex. MacOS)|Categorical|
|Weight|무게 (ex. 1.37kg)|Categorical|
|**Price**|**판매 가격 - 단위 : € (ex. 1339.69) [Target]**|**Numerical**|

### **Feature Engineering 이후 - (1303, 12) / (rows, columns)**
<img width="1068" alt="스크린샷 2022-09-01 오후 2 44 22" src="https://user-images.githubusercontent.com/97662174/187840241-850d53f4-a27d-4129-a030-2b9fc58c3a10.png">

|Feature|Description|Data Type|
|:---:|---|:---:|
|Company|노트북 제조사 (ex. Apple)|Categorical|
|TypeName|타입 (ex. Ultrabook)|Categorical|
|Inches|스크린 사이즈 - 단위 : inch (ex. 13.3)|Numerical|
|Screen Type|스크린 타입 (ex. IPS Panel Retina Display)|Categorical|
|Touchscreen|터치스크린 유무 (0 or 1)|Categorical|
|Pixel|스크린의 픽셀 수 (ex. 3840000)|Numerical|
|Cpu|Cpu 옵션 (ex. Intel Core i7)|Categorical|
|Cpu GHz|Cpu의 속도 (ex. 2.2)|Numerical|
|Ram|Ram 옵션 - 단위 : GB (ex. 8)|Numerical|
|Memory Size|메모리 용량 - 단위 : GB (ex. 2256)|Numerical|
|Memory Type|메모리 타입 (ex. SSD + HDD)|Categorical|
|Gpu|Gpu 옵션 (ex. Intel Iris Plus Graphics 640)|Categorical|
|OpSys|OS 시스템 (ex. MacOS)|Categorical|
|Weight|무게 - 단위 : kg (ex. 1.37)|Numerical|
|**Price**|**판매 가격 - 단위 : € (ex. 1339.69) [Target]**|**Numerical**|

### &nbsp;

## :three: 데이터 시각화
### **Categorical Data**
* `Company` `Typename` `Screen Type` `Touchscreen` `Cpu` `Memory Type` `Gpu` `OpSys`
* **barplot**을 이용하여 각 category별 **평균 판매 가격**을 비교
* **ex. Cpu**
<img width="1095" alt="스크린샷 2022-09-01 오후 3 04 42" src="https://user-images.githubusercontent.com/97662174/187843170-28673f1e-baa2-4830-96e6-3577568c0805.png">

* **ex. Memory Type**
<img width="924" alt="스크린샷 2022-09-01 오후 3 05 12" src="https://user-images.githubusercontent.com/97662174/187843221-e6aacaff-f989-4324-bd0d-b97d2378c507.png">


### **Numerical Data**
* `Inches` `Pixel` `Cpu GHz` `Ram` `Memory Size` `Weight`
* **Heatmap**을 이용하여 **판매 가격과의 상관관계** 확인
<img width="670" alt="correlation" src="https://user-images.githubusercontent.com/97662174/187880017-4771ff0e-0bb1-4fd2-952f-bd395962aeb9.png">

### &nbsp;

## :four: 모델링
### 하이퍼파라미터 튜닝
* **사용 모델** :`Decision Tree` `Random Forest` `XGBoost`
* 1303개의 데이터를 Train Set : Test Set = 8(1042개) : 2(261개)로 분리
* Train Set에 대해 **RadomizedSearchCV**를 통해 하이퍼 파라미터를 튜닝하며 학습


||Decision Tree|Random Forest|XGBoost|
|:---:|:---:|:---:|:---:|
|1|max_depth|n_estimators|n_estimators|
|2|min_saples_leaf|max_depth|max_depth|
|3|max_features|min_samples_leaf|learning_rate|
|4||max_features||


### 모델 평가 및 선택
* **평가 지표** : `R2 Score` (회귀 모델의 성능을 0과 1 사이의 값으로 평가 가능)
* Test Set에 대해 Score가 가장 높은 모델을 일반화 성능이 가장 우수한 최종 모델로 선정

|Data|Decision Tree|Random Forest|XGBoost|
|:---:|:---:|:---:|:---:|
|Train Set|0.7332|0.7775|0.8233|
|Test Set|0.6819|0.7391|**0.8146**|

-> **XGBoost**를 최종 모델로 선정

### &nbsp;

## :five: 모델 해석
### 특성 중요도
<img width="818" alt="스크린샷 2022-09-01 오후 3 53 28" src="https://user-images.githubusercontent.com/97662174/187850946-e24f5813-79a5-4883-a86a-00bc3a73e3a9.png">

-> 다른 특성들과 비교하여 **Ram**이 **노트북 판매 가격에 영향을 가장 크게 미치는 특성**으로 나타남

### PDP Plot
<img width="801" alt="스크린샷 2022-09-01 오후 4 02 04" src="https://user-images.githubusercontent.com/97662174/187852196-f4460374-fcd4-4bd5-9f7c-d313619b764e.png">

-> **Ram** 용량이 증가함에 따라 노트북의 판매 가격도 증가하는 경향을 보임 

### SHAP Force Plot
<img width="1170" alt="스크린샷 2022-09-01 오후 3 54 02" src="https://user-images.githubusercontent.com/97662174/187851569-030bdfce-d72b-4ad0-9dbe-1e604dcfa68b.png">

-> 빨간색 영역이 가장 두꺼운 **Ram** 이 **노트북 판매 가격 상승에 가장 큰 영향을 미치는 특성**이라는 것을 확인

### &nbsp;

## :six: 결론
* 다른 스펙을 업그레이드하는 것에 비해 **Ram**의 용량을 늘림에 따른 판매 가격의 상승폭이 상대적으로 큼
* Ram보다는 더 큰 화면의 노트북을 구입하거나 메모리 용량을 늘리는 등을 **소비자 입장에서 합리적으로 노트북을 구매하는 방법**으로 제시할 수 있음 

### &nbsp;

## :seven: 한계점
* 총 데이터의 수가 1303개로 모델의 성능을 끌어올리기에는 다소 적음
