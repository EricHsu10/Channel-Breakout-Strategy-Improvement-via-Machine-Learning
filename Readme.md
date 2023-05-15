# Channel Breakout Strategy Improvement via Machine Learning
## 1. Motivation
### 當沖交易優勢：

當沖交易相對波段交易能夠做到的有以下三點:1.避開隔夜的跳空風險 2.交易成本低 3.交易週期短。
然而，當沖交易也有其限制，因為持倉時間有限，如何有效率的選擇進場時機將左右我們的當沖交易績效，因此在有限的時間內建立交易的優勢很重要。
除了找出當日會有波動的標的之外，若是能**預測波動方向較一致的盤勢**，找出比較不會上下沖洗的盤勢，或許能夠讓我們在日內的波段行情中找到好的交易機會。

![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/82b29bd4-0b0d-4396-95ca-d3296ec0c670)


### 實體K介紹:

K線由開盤價、收盤價、最高價、最低價四種價格組成
實體Ｋ即是**Ｋ線的實心部分**，不包含上影線與下影線
實體K佔比＝開盤價和收盤價距離/最高價與最低價的距離

![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/e3c6350b-d72a-4ea8-a6ad-9ee8dfafe616)

我們認為實體Ｋ佔比較大的盤勢，波動的方向會較一致，且不會有較大的日內反轉，適合當沖的突破策略; 相對的，上下影線部分較大的盤勢可能容易被盤勢玩弄。


### 實體K當沖濾網測試 ：
```
Ｍulticharts 回測簡單的當沖策略
* 標的：台積電期貨 5分K
* 策略內容：價格突破＋實體Ｋ濾網 
* 實體Ｋ濾網: 實體Ｋ佔比>0.4 且 實體Ｋ大小超過1塊 (只做大實體K的盤)(修改)

進場方式:
* 向上突破五根K高點做多
* 向下突破五根K低點做空

出場方式:
* 持有到收盤，1%停損
```


實體K濾網績效:上帝視角（預先知道今天實體Ｋ比例，if>0.5才使用突破策略買進）

![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/9868a1f0-c268-4502-b0ab-40e01155d357)


買進持有績效

![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/ca50023e-83ba-4a40-98d9-1b678a8fec7c)


無濾網績效：只用使用突破策略

![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/af78b1b0-2fc6-4c55-a8da-0a08f9570e00)

## 2. Data 

 
 ### 標的:
台積電 2330 
### 資料來源:
Tickdata: 價量資料
TEJ：籌碼資料
### 樣本期間:  
2017/01~2022/06

| training data (7) | testing data (3) |
| -------- | -------- |
| 2017/01~2020/08 | 2020/09~2022/06     |


### Response variable

We calculate $Y_t$ using $X_{t-1}$.
$$Y_t=I_{\{physical > 0.4 ,\ high-low>1\}},$$ 

where $$physical = abs(close-open)/(high-low).$$


### Explainatory variables (at day $t$)

$X_{t,i}$ at day $t$.

| **價量指標**           | 描述                                   |
| ------------------------ | --------------------------------------------- |
| EMA                      | 移動平均線(價格變數)                                       |
| CCI                      | 順勢指標                                              |
| SAR                      | 反轉指標                                 |#Stop And Reverse
| ADX                      | 動向指標                  |#Directional Movement Index
| OBV                      | 能量潮指標                                       |#On Balance Volume
| ATR                      | 價格波動指標                                             |
| AROONOSC                 | Aroon指標                                              |
| RSI                      | 相對強弱指標                                      |
| **三大法人**             |                                               |
| foreign                  | 外資買賣超(張)                                |
| trust                    | 投信買賣超(張)                                |
| dealer                   | 自營買賣超(張)                                |
| day_foreign              | 外資買賣超日數                                |
| day_trust                | 投信買賣超日數                                |
| day_dealer               | 自營買賣超日數                                |
| ratio_foreign            | 外資成交比重                                  |
| ratio_trust              | 投信成交比重                                  |
| ratio_dealer             | 自營成交比重                                  |
| **融資融券**             |                                               |
| ratio_credit             | 信用交易比重                                  |
| increase_margin          | 融資增加(張)                                  |
| decrease_margin          | 融資減少(張)                                  |
| total_margin             | 融資餘額(張)                                  |
| balance_margin           | 融資餘額(千元)                                |
| utilization_margin       | 融資使用率                                    |
| increase_short           | 融券增加(張)                                  |
| decrease_short           | 融券減少(張)                                  |
| total_short              | 融券餘額(張)                                  |
| balance_short            | 融券餘額(千元)                                |
| utilization_short        | 融券使用率                                    |
| ratiotoday_margin        | 融資增減比率                                  |
| ratiotoday_short         | 融券增減比率                                  |
| ratio_st_mg              | 券資比                                        |
| offset                   | 資券互抵(張)                                  |
| ratio_offset             | 資券互抵比例                                  |
| ratio_turnover           | 實際週轉率％                                  |
| **董監持股**             |                                               |
| ratio_friegnShares_noD&S | 外資總持股率_不含董監%                        |
| ratio_trustShares        | 投信持股率％                                  |
| ratio_D&S_Shares         | 自營持股率％                                  |
| ratio_dealerShares       | 董監持股％                                    |
| ratio_D&S_Pledge         | 董監質押％                                    |

### EDA:
#### 1. Distribution Plot
<!--實體K分布-->
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/4e781fef-947c-4bdd-9eb7-6991e5aac647)

##### 結果:訓練集和測試集的分配大抵相同，不會有樣本分布不均問題

#### 2. Pair Plot
<!--Y與X關係-->
##### 價量指標
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/9d4936a1-9be9-48cf-9ce3-3ff1bd0680ce)


##### 三大法人
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/90df4207-8938-42ea-8f9d-df4936a63f72)


###### 三大法人的解釋變數大多相關性小(點分布對稱)
##### 融資券
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/3a00e183-090b-446d-a4f4-350328e46220)


###### 相較上者，融資劵的解釋變數相關性較大且為正相關
##### 董監持股
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/2b27632c-6930-404d-82cb-2f6b5bc3836b)


###### 結果:
###### 1.前一日是較有趨勢的盤，隔日傾向較沒有趨勢。
###### 2.有些變數有正相關，但大部分非線性關係。

#### 3. Heat map
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/8396af43-910b-45ea-9b66-7b55bc86e27e)


###### 結果:
###### 1.發現有些價量變數與信用交易類的變數有相關
###### 2.變數間有高度相關，須注意有可能是因為變數是從配對的變數從公式衍生出來的

## 3. Formulation
Y: Trend 實體K
X: 價量變數與籌碼變數
目標:用X預測Y
We calculate $Y_t$ using $X_{t-1}$.
$$Y_t=I_{\{physical > 0.4 ,\ high-low>1\}},$$ 

where $$physical = abs(close-open)/(high-low).$$

Benchmark: Logistic Regression
Method: DecisionTree, XGBoost

## 4. Analysis

### 4.1 Confunsion matrix



#### 4.1.A: Confusion matrix with static models
| training data (7) | testing data (3) |
| -------- | -------- |
| 2017/01~2020/08 | 2020/09~2022/06     |

Original method (static parameters:使用訓練集中的一組參數去預測第二天(隔天)的$y$，且在測試期間具有相同的參數)


| 績效 |Logistic|DecisionTree|XGBoost|
| ------ |:------ | --- | --- | 
| Accuracy  | 0.5 | 0.54| <font color="#f00">**0.59**</font> |  |  
| F1 Score  | 0.59| 0.67 | <font color="#f00">**0.71**</font> |  |     
| Recall    | 0.58| 0.59 | <font color="#f00">**0.61**</font> |  |     
Precision | 0.6| 0.79 | <font color="#f00">**0.85**</font> |  |  

Note:模型中參數已經過GridSearchCV最佳化
#### 4.1.B: Confusion matrix with dynamic-adjusted models
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/563631aa-3ee2-43f3-91b0-65004c9c1f30)

#### 我們想要從靜態的模型中再更進步我們的參數，理由是靜態切割的方式可能沒有辦法考慮到市場的趨勢(ex.2020年全球遇上新冠肺炎，疫情衝擊經濟)，所以我們使用動態的時間序列推進訓練集。
|data period| window |shift|
|---|----|---|
|2017/01~2022/06|250 days|30 days|


Sliding window train test split prediciton (Use 250-day historical to obtain a model and parameters to predict $y$ one-days ahead,finally we re-estimate the model and predict with the new parameters) 


| 績效 |Logistic|DecisionTree|XGBoost|
| ------ |:------ | --- | --- | 
| Accuracy  | 0.5 | 0.57 $\uparrow$ | <font color="#f00">**0.6 $\uparrow$**</font> |  |   
| F1 Score  | 0.59| 0.68 $\uparrow$ | <font color="#f00">**0.73 $\uparrow$**</font> | |    
| Recall    | 0.58| 0.61 $\uparrow$ | <font color="#f00">**0.61**</font>  |  |    
| Precision | 0.6 | 0.78 | <font color="#f00">**0.9 $\uparrow$$\uparrow$**</font> | |   

### 4.2 人類視角實體Ｋ濾網績效 (Dynamic-adjusted models)

#####   Logistic Regression:testing set績效2020/09~2022/06
多單權益曲線:
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/939cb8b6-6efe-46c5-95ab-d469228f630f)

空單權益曲線:
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/7310ec1b-93da-43fa-8833-5a52563bd795)

<!--只做多單的績效:
![](https://i.imgur.com/kkfGN8I.png)-->

#####   DecisionTree :testing set績效2020/09~2022/06
多單權益曲線:
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/0c9afaf1-75c1-4df2-ba74-5dfe817d7325)

空單權益曲線:
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/c0c252bd-3cf8-435a-956a-515d5046cc00)

<!--只做多單的績效:
![](https://i.imgur.com/pqgzwwf.png)-->

#####   XGBOOST :testing set績效2020/09~2022/06
多單權益曲線:
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/26444058-9ddc-4c97-bc67-3a52757a07c9)

空單權益曲線:
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/7ecbe657-eaed-4e67-bc9f-3e5ecc9b8bcd)

<!--只做多單的績效:
![](https://i.imgur.com/pYCPymZ.png)-->


##### 結論: 由上圖可看出，多單的價格突破搭配實體K濾網效果不錯，空單則效果不好。股市長期多頭，因此空單突破可能需要更多濾網或更複雜策略才能穩定獲利

### 策略優化
```
價格突破＋實體Ｋ濾網 當沖策略(不做空)
* 標的：台積電期貨 5分K
* 初始資金:100000

進場方式:
* 日K濾網(昨日收盤價>近三日收盤價)
* 向上突破五根K高點做多
* 一天只進場一次

出場方式:
* 持有到收盤，0.5%停損
```
| 交易績效 |Logistic|DecisionTree|XGBoost| No Physical Filter(突破+日K)|
| ------ |:------ | --- | --- | --- | 
| 淨利  | 10200 | 75100 | <font color="#f00">**159000**</font> | 62000 |   
| 最大策略虧損 (%)  | 60.99%| 34.11% | <font color="#f00">**31.35%**</font> |41.75% | 
| 交易次數  | 118 | 159 | 170  | 200 |    
| 勝率 | 38.98% | 38.36% | <font color="#f00">**42.35%**</font> | 38.5%|  
| 年報酬率 | 5.67% | 41.75% | <font color="#f00">**88.39%**</font> | 34.47% | 
* 樣本外買進持有淨利:14484
## 5. Conclusion

1. XGBOOST 比較好
1. Dynamic-adjusted models 比較讚
1. 實體K濾網在多單的簡單突破策略(突破近N根高點做多)較有效果
1. 股市長期多頭，因此空單突破可能需要更多濾網或更複雜策略才能穩定獲利










### Appendix 

#### Confusion matrix with static models: 我們使用不同模型形成的Confusion matrix鑑別靜態模型的好壞
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/2bba4cbb-f140-45b0-9739-cb951d73fdb3)

###### Accuracy:模型預測正確數量所佔整體的比例
###### Precision:陽性的樣本中有幾個是預測正確
###### Recall:事實為真的樣本中有幾個是預測正確
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/cd2ea7db-3dd8-43c3-b775-59fbef040a9e)

###### F1-score:同時考慮Precision & Recall，平衡地反映這個演算法的精確度

![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/a870027e-10c7-4145-b104-7d09e803491b)

![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/d354bf94-b783-4909-96c2-2bc60364487e)

![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/17ffd494-186b-41ec-8234-c35f2f8178aa)

<!--![](https://i.imgur.com/QzBZVeV.png) -->

### Confusion matrix with dynamic-adjusted models:鑑別動態模型的好壞
![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/b9103d74-3aea-4e4b-bd44-d34f2e0088f0)

![image](https://github.com/EricHsu10/Channel-Breakout-Strategy-Improvement-via-Machine-Learning/assets/111495932/558bf73f-2c25-4170-a904-ddfbfd0e9d73)


#### 樣本數

Train   ：label=1 :  501 label=0 :  513
Test    ：label=1 :  259 label=0 :  176
All          ：label=1 :  760 label=0 :  689
