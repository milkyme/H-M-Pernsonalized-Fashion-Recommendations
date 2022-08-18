# HnM-Pernsonalized-Fashion-Recommendations
Kaggle competition

- 본 프로젝트는 kaggle competition 연관 프로젝트로, fast fashion 브랜드인 H&M의 데이터를 이용하여 고객들에게 상품을 추천해주는 것이 목표이다.
- 주어진 데이터는 다음과 같다. 
    > ```articles.csv```: fashion item에 대한 정보(article은 각 상품을 의미)  
    ```customers.csv```: 고객에 대한 정보  
    ```transactions_train.csv```: 고객들의 구매 log  
    ```images folder```: 각 article에 대한 사진  
    
    하지만 ```articles.csv```에 각 article의 모양과 색깔 정보 등의 상세한 메타데이터가 담겨있기 때문에, ```images folder```의 이미지 데이터를 처리해서 추가적으로 얻어낼 정보가 없다고 판단하여 사용하지 않을 예정이다.
- ```transactions_train.csv```에는 2018년 9월 20일부터 2020년 9월 22일까지, 약 2년간의 구매정보가 있다. 해당 데이터의 마지막 log날짜인 2020년 9월 22일 이후로 일주일동안 고객들이 구매할 상품을 12개까지 추천하는 것이 최종적으로 해야 할 일이다. ```transactions_train.csv``` 데이터에 제시된 customer 모두에게 12개씩의 article을 추천해야 하고, 성능평가는 **MAP@12**로 진행한다.([MAP: Mean Average Precision](https://danthetech.netlify.app/DataScience/evaluation-metrics-for-recommendation-system), 간단히 설명하자면 MAP는 precision의 응용 metric으로, 상품에 대한 추천 정확도와 추천 시 우선순위까지 반영하는 성능평가지표이다.)

- 본 프로젝트는 총 3개의 notebook에 걸쳐 기술을 진행할 예정이다.  

> -  ```1st notebook: EDA(Exploratory Data Analysis)```  
>    - 주어진 데이터들에 대해 결측치와 이상값 등이 있는지 살펴보고, 각종 시각화와 통계량 분석 등을 통해 여러 방향에서 데이터를 분석해보는 EDA를 진행한다.  
> -  ```2nd notebook: Candidate generate```  
>    - 주어진 데이터를 활용하여 고객마다 수백 개의 article들로 구성된 후보군을 생성하는 작업을 진행한다.  
>    - Graph Neural Network을 사용하여 후보군을 추천하는 내용은 상당히 길어져서 ```2-1.Graph embedding```이라는 별도의 notebook에 기술하였다.
> -  ```3rd notebook: Ranking model```  
>    - 두번째 notebook에서 만들어진 article 후보군에 대해 ranking을 산정함으로써 고객마다 최종적인 추천을 진행한다.

- 1. EDA에선 데이터를 살펴보면서, feature의 내용을 이해하고 친숙해지는 것에 중점을 두고 진행하였다. 해당 notebook을 굳이 보지 않더라도 위의 내용들을 숙지한다면 추천과 관련된 모델의 설명이 적혀있는 ```2. Candidate generate```이나 ```3. Ranking model```을 살펴보는 데에 지장이 없을 것이다.
- 2. Candidate generate
    - 고객에게 추천할 상품들을 여러가지 방식으로 선택하는 과정이다.  1) 최근에 많이 판매됐던 상품들과 연령대별로 많이 팔렸던 상품들을 선택하는 ‘Best item’ 2) 최근 구매했던 상품과 상품코드가 같은 상품을 선택하는 ‘Same product code item’  3) 상품의 메타데이터들을 활용해 최근 구매 상품과 비슷한 상품을 추천하는 ‘Item-based collaborative filtering’ 4) 구매 이력을 바탕으로 같은 상품을 구매했던 고객들의 다른 상품 구매를 반영하여 추천을 진행하는 ‘Graph Embedding(User-based collaborative filtering)’ 등 총 4가지 방법으로 고객별 후보군을 선정하였고, validation 데이터를 통해 검증해본 결과 고객별 recall의 평균이 0.0945 정도로 나왔다.
    - 평균 Recall이 0.0945가 나왔다는 말은 어떤 고객이 상품을 구매했을 때 평균적으로 약 9.45% 확률로 해당 상품이 candidate에 포함되어 있다는 것을 의미한다. 이게 어느 정도 수치인지는 당연히 상대적일 수밖에 없어서 설명을 덧붙이자면, 해당 Kaggle competition의 최종 우승자가 제출한 모델의 test 데이터에 대한 MAP@12가 0.037 정도였다. 
    - 물론 1) MAP@12는 12개만 추천하여 산출한 수치이고 candidate에는 수백개의 article 후보군이 있다. 그리고 2) MAP는 Mean Average Precision으로 어디까지나 기본적인 계산방식은 precision이라 recall과는 직접적인 비교가 불가능하긴 하다.  
      하지만 1) 12개와 수백개의 간극은 ranking 모델에서 한번 더 정교화된 추천을 진행할 것이므로  극복할 여지가 다분하다. 2) precision과 recall은 분명 다르긴 하지만, metric을 적용할 때 분모에 포함되는 요소는 달라도 분자에 포함되는 요소는 같다. 따라서 산출한 평균 recall의 수치도  최종적으로 사용해야 할 metric인 MAP와 충분한 상관관계가 존재한다.
    - 결론적으로 candiate 후보군이 얼마나 잘 선정되었는지 직접적인 비교는 힘들지만, ranking 모델까지 훈련을 잘 시킨다면 충분히 괜찮은 결과를 기대해볼 수 있다고 생각한다. 
- 3. Ranking model
    - 위에서 고객별로 선정한 candidate에 대해 가장 구매할 가능성이 높은 12개를 추려서 추천을 하는 과정이다. Training 데이터로 130만이 넘는 고객들이 주어졌고 그에 따라 수백개씩의 상품 후보군들이 존재하여, 상당히 큰 데이터를 학습해야 한다. 또한 메타데이터가 tabular 데이터로 주어졌다. 위의 두가지를 고려했을 때, 효율적이고 빠르며, tabular데이터에 별다른 전처리에 대한 고민없이 사용할 수 있는 LightGBM 모델을 사용하였다.
    - 이전에 언급했던 것처럼 해당 Kaggle competition의 최종 우승자가 제출한 모델의 test 데이터에 대한 MAP@12가 0.037 정도였다. 하지만 validation 데이터에 적용한 필자의 모델은 MAP@12가 0.060정도이다.
    - Test 데이터와 Validation 데이터는 분명 차이가 있을 것이기에 단순히 필자의 모델이 competition 우승자 모델보다 0.060/0.037 = 1.62배 좋다고는 당연히 말할 수 없다. 하지만 valdation 데이터가 training을 하는 데에 사용되는 Data leakage가 일어나지 않도록 매우 유의하며 작업을 진행했고, validation 데이터를 최대한 test 데이터와 비슷하도록 설정해주었기에(같은 1주일 기간, 1주일밖에 차이나지 않는 시간 등) 충분히 괜찮은 성능이 나왔다고 생각한다.
