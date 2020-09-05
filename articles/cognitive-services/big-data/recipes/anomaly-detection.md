---
title: 레 레 레 레 레 레 레 레 레 레 레 레 레 레 레 레 레 레 Cognitive Services 피가
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 빅 데이터에 대 한 Cognitive Services에서 분산 변칙 검색을 수행 하는 방법을 보여 줍니다.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 4f6fa73130e3e78b573a866dbb6524acbc88c50c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691465"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>레 레 레 레 레 레 레 레 레 레 레 레 레 레 레 레 레 레 Cognitive Services 피가

이 작성법은 IoT 장치의 예측 유지 관리를 위해 Spark에서 Azure Synapse Analytics 및 Cognitive Services를 사용 하는 방법을 보여 줍니다. [CosmosDB 및 Synapse 링크](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) 샘플을 따릅니다. 이러한 작업을 단순하게 유지 하기 위해 CosmosDB 및 Synapse 링크를 통해 스트리밍된 데이터를 가져오는 대신 CSV 파일에서 직접 데이터를 읽습니다. Synapse Link 샘플을 살펴보는 것이 좋습니다.

## <a name="hypothetical-scenario"></a>가상 시나리오

가상 시나리오는 IoT 장치가 [스트림 터빈](https://en.wikipedia.org/wiki/Steam_turbine)를 모니터링 하는 전원 공장입니다. IoTSignals 컬렉션은 각 터빈에 대해 분당 회전 (RPM) 및 Megawatts (MW) 데이터를 포함 합니다. 스트림 터빈의 신호를 분석 하 고 있으며 비정상 신호를 검색 했습니다.

임의 빈도의 데이터에는 이상 값이 있을 수 있습니다. 이러한 상황에서 RPM 값은 위로 이동 하 고 MW 출력은 회로 보호에 대해 다운 됩니다. 이 개념은 데이터를 동시에 다른 신호로 표시 하는 것을 확인 하는 것입니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Spark 풀](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool) 로 구성 된 [Azure Synapse 작업 영역](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace)

## <a name="setup"></a>설치 프로그램

### <a name="create-an-anomaly-detector-resource"></a>Anomaly Detector 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용 하 여 번역기에 대 한 리소스를 만듭니다. 다음도 가능합니다.

- [Azure Portal](https://portal.azure.com/)에서 기존 리소스를 봅니다.

이 리소스에 대 한 끝점 및 키를 적어 두세요 .이 가이드에서 필요 합니다.

## <a name="enter-your-service-keys"></a>서비스 키 입력

키와 위치를 추가 하 여 시작 해 보겠습니다.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>데이터 프레임 데이터 읽기

다음으로, I데이터 프레임 신호 파일을 읽어 보겠습니다. Synapse 작업 영역에서 새 노트북을 열고 파일에서 데이터 프레임를 만듭니다.

```python
df_device_info = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Spark에서 Cognitive Services를 사용 하 여 변칙 검색 실행

목표는 IoT 장치의 신호가 비정상 값을 출력 하는 인스턴스를 찾는 것입니다 .이를 통해 무언가 잘못 된 경우를 확인 하 고 예측 유지 관리를 수행할 수 있습니다. 이렇게 하려면 Spark에서 변칙 탐지기를 사용 하겠습니다.

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

데이터를 살펴보겠습니다.

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

이 셀의 결과는 다음과 같습니다.

| timestamp           |   값 | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | 개발-7      | 거짓       |
| 2020-05-01 18:33:52 |    2976 | 개발-7      | 거짓       |
| 2020-05-01 18:33:53 |    2714 | 개발-7      | 거짓       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>장치 중 하나에 대 한 변칙 시각화

IoTSignals.csv에는 여러 IoT 장치의 신호가 있습니다. 특정 장치에 집중 하 고 장치에서 비정상적인 출력을 시각화 합니다.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

이제 특정 장치에 대 한 변칙을 나타내는 데이터 프레임를 만들었으므로 다음과 같은 비정상 상황을 시각화할 수 있습니다.

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

성공 하면 출력이 다음과 같이 표시 됩니다.

![변칙 탐지기 플롯](../media/anomaly-output.png)

## <a name="next-steps"></a>다음 단계

Azure Cognitive Services, Azure Synapse Analytics 및 Azure CosmosDB를 사용 하 여 대규모 예측 유지 관리를 수행 하는 방법에 대해 알아봅니다. 자세한 내용은 [GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples)에서 전체 샘플을 참조 하세요.
