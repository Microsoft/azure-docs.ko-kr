---
title: Azure Stream Analytics를 사용하여 자주 발생하는 거래 시뮬레이션
description: Azure Stream Analytics 작업에서 선형 회귀 모델 학습 및 평가를 수행하는 방법입니다.
services: stream-analytics
author: rockboyfor
ms.author: v-yeche
manager: digimobile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
origin.date: 11/05/2017
ms.date: 09/17/2018
ms.openlocfilehash: 33a7b27d065fc0383e4693053f7bfb6d56e2d33b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61480071"
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Stream Analytics에서 자주 발생하는 거래 시뮬레이션
SQL 언어와 JavaScript UDF(사용자 정의 함수) 및 UDA(사용자 정의 집계)를 Azure Stream Analytics에 결합하면 사용자가 고급 분석을 수행할 수 있습니다. 고급 분석에는 온라인 기계 학습 교육 및 점수 매기기와 상태 저장 프로세스 시뮬레이션이 포함될 수 있습니다. 이 문서는 자주 발생하는 거래 시나리오에서 연속 학습 및 평가를 수행하는 Azure Stream Analytics 작업에서 선형 회귀를 수행하는 방법을 설명합니다.

## <a name="high-frequency-trading"></a>자주 발생하는 거래
자주 발생하는 거래의 논리 흐름은 다음에 대한 것입니다.
1. 보안 교환에서 실시간 견적 가져오기.
2. 가격 변동을 예상할 수 있도록 견적에 대한 예측 모델 빌드.
3. 가격 변동을 성공적으로 예측한 경우 수익을 올릴 수 있도록 매수 또는 매도 주문. 

결과적으로 다음과 같은 항목이 필요합니다.
* 실시간 견적 피드.
* 실시간 견적에서 작동할 수 있는 예측 모델.
* 거래 알고리즘의 손익을 보여주는 거래 시뮬레이션.

### <a name="real-time-quote-feed"></a>실시간 견적 피드
IEX는 socket.io를 사용하여 무료 [실시간 입찰 및 견적 요청](https://iextrading.com/developer/docs/#websockets)을 제공합니다. 실시간 견적을 받아서 Azure Event Hubs에 데이터 원본으로 푸시하는 간단한 콘솔 프로그램을 작성할 수 있습니다. 다음 코드는 프로그램의 기본 구조입니다. 코드를 간결하게 보여주기 위해 오류 처리가 생략되었습니다. 프로젝트에 SocketIoClientDotNet 및 WindowsAzure.ServiceBus NuGet 패키지를 포함해야 합니다.

```csharp
using Quobject.SocketIoClientDotNet.Client;
using Microsoft.ServiceBus.Messaging;
var symbols = "msft,fb,amzn,goog";
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");
socket.On(Socket.EVENT_MESSAGE, (message) =>
{
    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
});
socket.On(Socket.EVENT_CONNECT, () =>
{
    socket.Emit("subscribe", symbols);
});
```

몇 가지 샘플 이벤트를 생성해 보았습니다.

```json
    {"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}
```

>[!NOTE]
>이벤트의 타임스탬프는 epoch 시간으로 **lastUpdated**입니다.

### <a name="predictive-model-for-high-frequency-trading"></a>자주 발생하는 거래에 대한 예측 모델
데모용으로 Darryl Shen이 작성한 [문서](http://eprints.maths.ox.ac.uk/1895/1/Darryl%20Shen%20%28for%20archive%29.pdf)에 설명된 선형 모델을 사용합니다.

VOI(Volume Order Imbalance)는 현재 입찰/요청 가격과 볼륨 및 마지막 틱의 입찰/요청 가격 및 볼륨의 함수입니다. 이 문서는 VOI와 향후 가격 이동 간의 상관 관계를 식별합니다. 이전 VOI 값 5개와 그 다음 10틱의 가격 변경 간에 선형 모델을 빌드합니다. 선형 회귀와 함께 이전 날짜의 데이터를 사용하여 모델을 학습합니다. 

학습된 모델은 실시간으로 당일 거래에서 견적에 대한 가격 변동을 예측하는 데 사용됩니다. 필요한 만큼의 대규모 가격 변동이 예측되면 거래가 실행됩니다. 임계값 설정에 따라 거래 중에 단일 종목에 수많은 거래가 이뤄질 수 있습니다.

![볼륨 주문 불균형 정의](./media/stream-analytics-high-frequency-trading/volume-order-imbalance-formula.png)

이제 Azure Stream Analytics 작업에서 학습 및 예측 작업을 설명하겠습니다.

먼저 입력이 정리됩니다. Epoch 시간은 **DATEADD**를 통해 날짜/시간으로 변환됩니다. 쿼리에 실패하지 않고 데이터 형식을 강제 변환하는 데 **TRY_CAST**를 사용합니다. 필드의 조작 또는 비교와 관련하여 예상치 않은 동작이 발생하지 않도록 입력 필드를 예상된 데이터 형식으로 캐스팅하는 것이 좋습니다.

```SQL
    WITH
    typeconvertedquotes AS (
        /* convert all input fields to proper types */
        SELECT
            System.Timestamp AS lastUpdated,
            symbol,
            DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
            TRY_CAST(bidSize as bigint) AS bidSize,
            TRY_CAST(bidPrice as float) AS bidPrice,
            TRY_CAST(askSize as bigint) AS askSize,
            TRY_CAST(askPrice as float) AS askPrice,
            TRY_CAST(volume as bigint) AS volume,
            TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
            TRY_CAST(lastSalePrice as float) AS lastSalePrice
        FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
    ),
    timefilteredquotes AS (
        /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
        /* clean up invalid data points */
        SELECT * FROM typeconvertedquotes
        WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
    ),
```

다음으로 마지막 틱 값을 가져오는 **LAG** 함수를 사용합니다. **LIMIT DURATION** 값의 1시간을 임의로 선택합니다. 견적 빈도에 따라 1시간을 거슬러서 이전의 틱을 찾을 수 있다고 가정하는 것이 안전합니다.  

```SQL
    shiftedquotes AS (
        /* get previous bid/ask price and size in order to calculate VOI */
        SELECT
            symbol,
            (bidPrice + askPrice)/2 AS midPrice,
            bidPrice,
            bidSize,
            askPrice,
            askSize,
            LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
            LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
            LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
            LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
        FROM timefilteredquotes
    ),
```

그런 다음, VOI 값을 계산할 수 있습니다. 이전 틱이 존재하지 않는 경우 null 값을 필터링합니다.

```SQL
    currentPriceAndVOI AS (
        /* calculate VOI */
        SELECT
            symbol,
            midPrice,
            (CASE WHEN (bidPrice < bidPricePrev) THEN 0
                ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
             END) -
            (CASE WHEN (askPrice < askPricePrev) THEN askSize
                ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
             END) AS VOI
        FROM shiftedquotes
        WHERE
            bidPrice IS NOT NULL AND
            bidSize IS NOT NULL AND
            askPrice IS NOT NULL AND
            askSize IS NOT NULL AND
            bidPricePrev IS NOT NULL AND
            bidSizePrev IS NOT NULL AND
            askPricePrev IS NOT NULL AND
            askSizePrev IS NOT NULL
    ),
```

이제 **LAG**를 다시 사용하여 2개의 연속적인 VOI 값 뒤에 10개의 연속적인 중간 가격 값을 포함하는 시퀀스를 만듭니다.

```SQL
    shiftedPriceAndShiftedVOI AS (
        /* get 10 future prices and 2 previous VOIs */
        SELECT
            symbol,
            midPrice AS midPrice10,
            LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
            LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
            LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
            LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
            LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
            LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
            LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
            LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
            LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
            LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
            LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
            LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),
```

그런 다음 두 개의 변수 선형 모델에서 데이터를 입력으로 변형합니다. 다시 일부 데이터가 없는 이벤트를 필터링합니다.

```SQL
    modelInput AS (
        /* create feature vector, x being VOI, y being delta price */
        SELECT
            symbol,
            (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
            VOI1 AS x1,
            VOI2 AS x2
        FROM shiftedPriceAndShiftedVOI
        WHERE
            midPrice1 IS NOT NULL AND
            midPrice2 IS NOT NULL AND
            midPrice3 IS NOT NULL AND
            midPrice4 IS NOT NULL AND
            midPrice5 IS NOT NULL AND
            midPrice6 IS NOT NULL AND
            midPrice7 IS NOT NULL AND
            midPrice8 IS NOT NULL AND
            midPrice9 IS NOT NULL AND
            midPrice10 IS NOT NULL AND
            midPrice IS NOT NULL AND
            VOI1 IS NOT NULL AND
            VOI2 IS NOT NULL
    ),
```

Azure Stream Analytics에 기본 제공 선형 회귀 함수가 없기 때문에 **SUM** 및 **AVG** 집계를 사용하여 선형 모델에 대한 계수를 계산합니다.

![선형 회귀 수학 수식](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

```SQL
    modelagg AS (
        /* get aggregates for linear regression calculation,
         http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
        SELECT
            symbol,
            SUM(x1 * x1) AS x1x1,
            SUM(x2 * x2) AS x2x2,
            SUM(x1 * y) AS x1y,
            SUM(x2 * y) AS x2y,
            SUM(x1 * x2) AS x1x2,
            AVG(y) AS avgy,
            AVG(x1) AS avgx1,
            AVG(x2) AS avgx2
        FROM modelInput
        GROUP BY symbol, TumblingWindow(hour, 24, -4)
    ),
    modelparambs AS (
        /* calculate b1 and b2 for the linear model */
        SELECT
            symbol,
            (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
            (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
            avgy,
            avgx1,
            avgx2
        FROM modelagg
    ),
    model AS (
        /* calculate a for the linear model */
        SELECT
            symbol,
            avgy - b1 * avgx1 - b2 * avgx2 AS a,
            b1,
            b2
        FROM modelparambs
    ),
```

현재 이벤트의 점수를 매기기 위해 이전 날짜의 모델을 사용하도록 모델과 견적을 조인하려고 합니다. 하지만 **JOIN**을 사용하는 대신, 모델 이벤트와 견적 이벤트를 **UNION**합니다. 그런 다음 하나의 일치 항목 얻을 수 있도록 **LAG**을 사용하여 이벤트를 이전 날짜의 모델과 쌍으로 연결합니다. 주말이기 때문에 3일을 거슬러 올라가야 합니다. 간단하게 **JOIN**을 사용하는 경우 모든 견적 이벤트의 세 가지 모델을 가져옵니다.

```SQL
    shiftedVOI AS (
        /* get two consecutive VOIs */
        SELECT
            symbol,
            midPrice,
            VOI AS VOI1,        
            LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),
    VOIAndModel AS (
        /* combine VOIs and models */
        SELECT
            'voi' AS type,
            symbol,
            midPrice,
            VOI1,
            VOI2,
            0.0 AS a,
            0.0 AS b1,
            0.0 AS b2
        FROM shiftedVOI
        UNION
        SELECT
            'model' AS type,
            symbol,
            0.0 AS midPrice,
            0 AS VOI1,
            0 AS VOI2,
            a,
            b1,
            b2
        FROM model
    ),
    VOIANDModelJoined AS (
        /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
        SELECT
            symbol,
            midPrice,
            VOI1 as x1,
            VOI2 as x2,
            LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
            LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
            LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
        FROM VOIAndModel
        WHERE type = 'voi'
    ),
```

이제 임계값 값이 0.02인 모델을 기반으로 예측하고 구입/판매 신호를 생성할 수 있습니다. 거래 값 10은 매수입니다. 거래 값 -10은 매도입니다.

``` SQL
    prediction AS (
        /* make prediction if there is a model */
        SELECT
            symbol,
            midPrice,
            a + b1 * x1 + b2 * x2 AS efpc
        FROM VOIANDModelJoined
        WHERE
            a IS NOT NULL AND
            b1 IS NOT NULL AND
            b2 IS NOT NULL AND
            x1 IS NOT NULL AND
            x2 IS NOT NULL
    ),
    tradeSignal AS (
        /* generate buy/sell signals */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,     
            midPrice,
            efpc,
            CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
            DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
        FROM prediction
    ),
```

### <a name="trading-simulation"></a>거래 시뮬레이션
거래 신호가 있으면 거래 전략이 얼마나 효과적인지 실제 거래 없이 테스트해봅니다. 

UDA를 사용하여 이 테스트를 수행할 것이며, 도약 창은 1분 간격으로 도약합니다. 날짜에 따른 추가 그룹화 및 절을 사용하면 창에서 동일한 날에 속한 이벤트만을 표시할 수 있습니다. 이틀에 걸쳐 있는 도약 창의 경우 **GROUP BY** 날짜가 그룹화를 이전 날짜와 현재 날짜로 분리합니다. **HAVING** 절은 현재 날짜에서 끝나지만 이전 날짜에서 그룹화하는 창을 필터링합니다.

```SQL
    simulation AS
    (
        /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,
            date,
            uda.TradeSimulation(tradeSignal) AS s
        FROM tradeSignal
        GROUP BY HoppingWindow(minute, 420, 1), symbol, date
        Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
    )
```

JavaScript UDA는 `init` 함수에서 모든 누적기를 초기화하고, 모든 이벤트를 창에 추가한 상태 전환을 계산하고, 창의 끝 부분에서 시뮬레이션 결과를 반환합니다. 일반 거래 프로세스는 다음과 같습니다.

- 매수 신호가 발생하고 보유 중인 주식이 없으면 매수합니다.
- 매도 신호가 발생하고 보유 중인 주식이 있으면 매도합니다.
- 보유 중인 주식이 없으면 숏 포지션으로 갑니다. 

현재 숏 포지션이고 매수 신호가 발생하면 숏 커버링합니다. 이 시뮬레이션에서는 어떤 경우에도 주식 10주를 보유하거나 공매도하지 않습니다. 거래 비용은 균일하게 $8입니다.

```javascript
    function main() {
        var TRADE_COST = 8.0;
        var SHARES = 10;
        this.init = function () {
            this.own = false;
            this.pos = 0;
            this.pnl = 0.0;
            this.tradeCosts = 0.0;
            this.buyPrice = 0.0;
            this.sellPrice = 0.0;
            this.buySize = 0;
            this.sellSize = 0;
            this.buyTotal = 0.0;
            this.sellTotal = 0.0;
        }
        this.accumulate = function (tradeSignal, timestamp) {
            if(!this.own && tradeSignal.trade == 10) {
              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;
            } else if(!this.own && tradeSignal.trade == -10) {
              // Sell to open
              this.own = true;
              this.pos = -1
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
              // Sell to close
              this.own = false;
              this.pos = 0;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;
              // Sell to open
              this.own = true;
              this.pos = -1;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;        
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
              // Buy to close
              this.own = false;
              this.pos = 0;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;
              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;         
              this.buyTotal += SHARES * tradeSignal.midprice;
            }
        }

        this.computeResult = function () {
            var result = {
                "pnl": this.pnl,
                "buySize": this.buySize,
                "sellSize": this.sellSize,
                "buyTotal": this.buyTotal,
                "sellTotal": this.sellTotal,
                "tradeCost": this.tradeCost
                };
            return result;
        }
    }
```

마지막으로 시각화를 위해 Power BI 대시보드에 출력합니다.

```SQL
    SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
    SELECT
        symbol,
        time,
        date,
        TRY_CAST(s.pnl as float) AS pnl,
        TRY_CAST(s.buySize as bigint) AS buySize,
        TRY_CAST(s.sellSize as bigint) AS sellSize,
        TRY_CAST(s.buyTotal as float) AS buyTotal,
        TRY_CAST(s.sellTotal as float) AS sellTotal
        INTO pnlDashboard
    FROM simulation /* output trade simulation to PBI */
```

![Trades Power BI 차트 시각적 개체](./media/stream-analytics-high-frequency-trading/trades-power-bi-chart.png)

![PNL Power BI 차트 시각적 개체](./media/stream-analytics-high-frequency-trading/pnl-power-bi-chart.png)

## <a name="summary"></a>요약
Azure Stream Analytics에서 다소 복잡한 쿼리를 사용하여 매우 현실적인 자주 발생하는 거래 모델을 구현할 수 있습니다. 기본 제공 선형 회귀 함수가 없기 때문에 모델을 5개에서 2개의 입력 변수로 간소화해야 합니다. 하지만 꼭 원하신다면 차원이 높고 복잡한 알고리즘을 JavaScript UDA로 구현할 수 있습니다. 

JavaScript UDA를 제외한 대부분의 쿼리는 [Azure Stream Analytics Tool for Visual Studio](stream-analytics-tools-for-visual-studio-install.md)를 통해 Visual Studio에서 테스트하고 디버깅할 수 있습니다. 작성자가 초기 쿼리를 작성한 후에 Visual Studio에서 쿼리를 테스트하고 디버깅하는 데 30분 미만이 소요되었습니다. 

현재 Visual Studio에서 UDA를 디버깅할 수 없습니다. JavaScript 코드를 단계별로 실행하는 기능에서 이 기능을 사용하도록 하고 있습니다. 또한 UDA에 도달하는 필드의 이름은 소문자입니다. 쿼리 테스트 중에 명백한 동작이 아닙니다. 하지만 Azure Stream Analytics 호환성 수준 1.1에서는 필드 이름 대소문자를 유지하므로 동작이 보다 자연스럽습니다.

이 문서를 통해 모든 Azure Stream Analytics 사용자가 서비스를 사용하여 거의 실시간으로 계속해서 고급 분석을 수행할 수 있기를 바랍니다. 고급 분석 시나리오에 대한 쿼리를 쉽게 구현할 수 있도록 하는 피드백이 있다면 알려주세요.
<!-- Update_Description: update meta properties -->