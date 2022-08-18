# H-M-Pernsonalized-Fashion-Recommendations
Kaggle competition

- 본 프로젝트는 kaggle competition 연관 프로젝트로, fast fashion 브랜드인 H&M의 데이터를 이용하여 고객들에게 상품을 추천해주는 것이 목표이다.
- 주어진 데이터는 다음과 같다. 
> ```articles.csv```: fashion item에 대한 정보(article은 각 상품을 의미)  
```customers.csv```: 고객에 대한 정보  
```transactions_train.csv```: 고객들의 구매 log  
```images folder```: 각 article에 대한 사진  
    
    하지만 ```articles.csv```에 각 article의 모양과 색깔 정보 등의 상세한 메타데이터가 담겨있기 때문에, ```images folder```의 이미지 데이터를 처리해서 추가적으로 얻어낼 정보가 없다고 판단하여 사용하지 않을 예정이다.
- ```transactions_train.csv```에는 2018년 9월 20일부터 2020년 9월 22일까지, 약 2년간의 구매정보가 있다. 해당 데이터의 마지막 log날짜인 2020년 9월 22일 이후로 일주일동안 고객들이 구매할 상품을 12개까지 추천하는 것이 최종적으로 해야 할 일이다.
- ```transactions_train.csv``` 데이터에 제시된 customer 모두에게 12개씩의 article을 추천해야 하고, 성능평가는 **MAP@12**로 진행한다.([MAP: Mean Average Precision](https://danthetech.netlify.app/DataScience/evaluation-metrics-for-recommendation-system), 간단히 설명하자면 MAP는 precision의 응용 metric으로, 상품에 대한 추천 정확도와 추천 시 우선순위까지 반영하는 성능평가지표이다.)

- 본 프로젝트는 총 3개의 notebook에 걸쳐 기술을 진행할 예정이다.  
> ```1st notebook: EDA(Exploratory Data Analysis)```  
&nbsp;&nbsp;&nbsp;&nbsp;- 주어진 데이터들에 대해 결측치와 이상값 등이 있는지 살펴보고, 각종 시각화와 통계량 분석 등을 통해 여러 방향에서 데이터를 분석해보는 EDA를 진행한다.  
```2nd notebook: Candidate generate```  
&nbsp;&nbsp;&nbsp;- 주어진 데이터를 활용하여 고객마다 수백 개의 article들로 구성된 후보군을 생성하는 작업을 진행한다.  
```3rd notebook: Ranking model```  
&nbsp;&nbsp;&nbsp;- 두번째 notebook에서 만들어진 article 후보군에 대해 ranking을 산정함으로써 고객마다 최종적인 추천을 진행한다.
