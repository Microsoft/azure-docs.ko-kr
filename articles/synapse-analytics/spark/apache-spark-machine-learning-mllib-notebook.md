---
title: Apache Spark MLlib를 사용 하 여 machine learning 앱 빌드
description: 로지스틱 회귀를 통한 분류를 사용하여 데이터 세트를 분석하는 기계 학습 앱을 만드는 데 Apache Spark MLlib을 사용하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: e1ece0add7b0749cfd808b0a3ec7962dd43a302d
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719345"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Apache Spark MLlib 및 Azure Synapse Analytics를 사용하여 기계 학습 앱 빌드

이 문서에서는 Apache Spark [MLlib](https://spark.apache.org/mllib/)을 사용하여 Azure 공개 데이터 세트에서 간단한 예측 분석을 수행하는 기계 학습 애플리케이션을 만드는 방법을 알아봅니다. Spark는 기본 제공 기계 학습 라이브러리를 제공합니다. 이 예제에서는 로지스틱 회귀를 통한 *분류*를 사용합니다.

MLlib은 다음 작업에 적합한 유틸리티를 비롯하여 기계 학습 태스크에 유용한 여러 유틸리티를 제공하는 핵심 Spark 라이브러리입니다.

- 분류
- 회귀
- Clustering
- 항목 모델링
- SVD(특이값 분해) 및 PCA(주성분 분석)
- 가설 테스트 및 샘플 통계 계산

## <a name="understand-classification-and-logistic-regression"></a>분류 및 로지스틱 회귀의 이해

널리 사용되는 Machine Learning 작업인 *분류*는 입력 데이터를 범주로 정렬하는 프로세스입니다. 사용자가 제공하는 입력 데이터에 *레이블*을 할당하는 방법을 파악하는 분류 알고리즘 작업입니다. 예를 들어 입력으로 주식 정보를 받아서 판매할 주식과 보유할 주식의 두 가지 범주로 주식을 나누는 기계 학습 알고리즘을 생각할 수 있습니다.

*로지스틱 회귀*는 분류에 사용할 수 있는 알고리즘입니다. Spark의 로지스틱 회귀 API는 *이진 분류*또는 입력 데이터를 두 그룹 중 하나로 분류하는 데 유용합니다. 로지스틱 회귀에 대한 자세한 내용은 [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression)를 참조하세요.

요약하자면, 로지스틱 회귀 프로세스는 입력 벡터가 한 그룹 또는 다른 그룹에 속할 확률을 예측할 수 있는 *로지스틱 함수* 를 만듭니다.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>NYC 택시 데이터에 대한 예측 분석 예제

이 예제에서는 Spark를 사용하여 뉴욕의 택시 이동 팁 데이터에 대한 예측 분석을 수행합니다. 데이터는 [Azure 공개 데이터 세트](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)를 통해 사용할 수 있습니다. 이 데이터 세트의 하위 집합에는 각 이동에 대한 정보, 시작 및 종료 시간과 위치, 비용 및 기타 흥미로운 특성을 포함한 노랑 택시 이동에 대한 정보가 포함되어 있습니다.

> [!IMPORTANT]
> 스토리지 위치에서 이 데이터를 풀하는 데 추가 요금이 있을 수 있습니다.

다음 단계에서는 특정 이동에 팁이 포함되어 있는지 여부를 예측하는 모델을 개발합니다.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Apache Spark MLlib 기계 학습 앱 만들기

1. PySpark 커널을 사용하여 Notebook을 만듭니다. 자세한 지침은 [Notebook 만들기](../quickstart-apache-spark-notebook.md#create-a-notebook)를 참조하세요.
2. 이 애플리케이션에 필요한 형식을 가져옵니다. 다음 코드를 복사하여 빈 셀에 붙여넣은 다음, **SHIFT + ENTER** 키를 누르거나 코드 왼쪽의 파란색 재생 아이콘을 사용하여 셀을 실행합니다.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    PySpark 커널로 인해 컨텍스트를 명시적으로 만들 필요가 없습니다. 첫 번째 코드 셀을 실행하면 Spark 컨텍스트가 자동으로 만들어집니다.

## <a name="construct-the-input-dataframe"></a>입력 데이터 프레임 구축

원시 데이터는 Parquet 형식이기 때문에 Spark 컨텍스트를 사용하여 직접 데이터 프레임으로 파일을 메모리로 풀할 수 있습니다. 아래 코드에서는 기본 옵션을 사용하지만 필요에 따라 데이터 형식 및 기타 스키마 특성을 강제로 매핑할 수도 있습니다.

1. 다음 줄을 실행하여 새 셀에 코드를 붙여넣어 Spark 데이터 프레임을 만듭니다. 그러면 개방형 데이터 집합 API를 통해 데이터를 검색 합니다. 이 데이터를 모두 풀하면 약 15억 행이 생성됩니다. Spark 풀(미리 보기)의 크기에 따라 원시 데이터가 너무 크거나 작업하는 데 너무 많은 시간이 걸릴 수 있습니다. 이 데이터를 더 작은 값으로 필터링할 수 있습니다. 다음 코드 예제에서는 start_date 및 end_date를 사용 하 여 한 달 동안의 데이터를 반환 하는 필터를 적용 합니다.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. 단순 필터링의 단점은 통계 관점에서 데이터에 대한 바이어스를 도입할 수 있다는 것입니다. 또 다른 방법은 Spark에 기본 제공되는 샘플링을 사용하는 것입니다. 다음 코드는 위의 코드 뒤에 적용되는 경우 데이터 세트를 약 2000 행으로 줄입니다. 이 샘플링 단계는 단순 필터 대신 사용하거나 단순 필터와 함께 사용할 수 있습니다.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. 이제 데이터를 확인하여 읽은 내용을 확인할 수 있습니다. 일반적으로 데이터 세트의 크기에 따라 전체 세트가 아닌 하위 집합을 사용하여 데이터를 검토하는 것이 좋습니다. 다음 코드는 두 가지 방법으로 데이터를 볼 수 있습니다. 하나는 기본 방법이고, 다른 하나는 데이터를 그래픽으로 시각화하는 기능뿐만 아니라 훨씬 풍부한 그리드 환경을 제공합니다.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. 생성된 데이터 세트 크기와 Notebook을 여러 번 실험하거나 실행해야 하는 크기에 따라 작업 영역에서 로컬로 데이터 세트를 캐시하는 것이 좋습니다. 다음 세 가지 방법으로 명시적 캐싱을 수행할 수 있습니다.

   - 로컬로 데이터 프레임을 파일로 저장
   - 데이터 프레임을 임시 테이블 또는 보기로 저장
   - 데이터 프레임을 영구 테이블로 저장

이러한 방법 중 처음 두 가지는 다음 코드 예제에 포함되어 있습니다.

임시 테이블 또는 보기를 만들면 데이터에 대해 서로 다른 액세스 경로가 제공되지만, Spark 인스턴스 세션의 기간 동안만 지속됩니다.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>데이터 이해

일반적으로 이 시점에서 데이터 이해를 높이기 위해 *EDA(예비 데이터 분석)* 의 단계를 진행합니다. 다음 코드는 데이터의 상태 및 품질에 대한 결론을 이끌어내는 팁과 관련된 데이터의 세 가지 시각화를 보여 줍니다.

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tipped by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![히스토그램](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![상자 수염 그림](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![산점도](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="prepare-the-data"></a>데이터 준비

원시 형식의 데이터는 모델에 직접 전달하는 데 적합하지 않은 경우가 많습니다. 데이터에 대해 일련의 작업을 수행하여 모델에서 사용할 수 있는 상태로 가져와야 합니다.

아래 코드에서는 네 가지 작업 클래스가 수행됩니다.

- 필터링을 통해 이상값/잘못된 값을 제거
- 필요하지 않은 열을 제거
- 모델이 더 효율적으로 작동하도록 원시 데이터에서 파생된 새 열을 생성(기능화라고도 함)
- 레이블 지정-이진 분류를 수행 하 고 있기 때문에 (지정 된 여행에 대 한 팁이 있거나 그렇지 않음) tip amount를 0 또는 1 값으로 변환 해야 합니다.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

그런 다음, 데이터에 대한 두 번째 단계를 수행하여 최종 기능을 추가합니다.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>로지스틱 회귀 모델 만들기

마지막 작업은 레이블이 지정된 데이터를 로지스틱 회귀로 분석할 수 있는 형식으로 변환하는 것입니다. 로지스틱 회귀 알고리즘에 대한 입력은 *레이블-기능 벡터 쌍* 세트여야 하며, 여기서 *기능 벡터*는 입력 지점을 나타내는 숫자의 벡터입니다. 따라서 범주 열을 숫자로 변환해야 합니다. `trafficTimeBins` 및 `weekdayString` 열을 정수 표현으로 변환해야 합니다. 변환을 수행하는 방법에는 여러 가지가 있지만, 이 예제에서 사용되는 방법은 일반적인 방법인 *OneHotEncoding*입니다.

```python
# Since the sample uses an algorithm that only works with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

이로 인해 모든 열이 모델을 학습하기 위한 올바른 형식인 새로운 데이터 프레임이 만들어집니다.

## <a name="train-a-logistic-regression-model"></a>로지스틱 회귀 모델 학습

첫 번째 작업은 데이터 세트를 학습 세트와 테스트 또는 유효성 검사 세트로 분할하는 것입니다. 여기서 분할은 임의적이며, 여러 분할 설정을 사용해보면서 모델에 영향을 미치는지 확인해야 합니다.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

이제 두 개의 DataFrame이 있으므로 다음 작업은 모델 수식을 만들고 학습 DataFrame에 대해 실행한 다음, DataFrame 테스트에 대해 유효성을 검사하는 것입니다. 모델 수식의 여러 버전을 실험하여 다양한 조합의 영향을 확인해야 합니다.

> [!Note]
> 모델을 저장 하려면 저장소 Blob 데이터 참가자 Azure 역할이 필요 합니다. 저장소 계정에서 Access Control (IAM)로 이동 하 여 **역할 할당 추가**를 선택 합니다. 저장소 Blob 데이터 참가자 Azure 역할을 SQL Database 서버에 할당 합니다. 소유자 권한이 있는 멤버만 이 단계를 수행할 수 있습니다. Azure 기본 제공 역할에 대한 자세한 내용은 이 [가이드](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조하세요.

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

이 셀의 출력은 다음과 같습니다.

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>예측의 시각적 표현 만들기

이제 이 테스트 결과의 이유를 파악하는 데 도움이 되는 최종 시각화를 만들 수 있습니다. [ROC Curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)는 결과를 검토하는 한 가지 방법입니다.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![로지스틱 회귀 팁 모델에 대한 ROC Curve](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "로지스틱 회귀 팁 모델에 대한 ROC Curve")

## <a name="shut-down-the-spark-instance"></a>Spark 인스턴스 종료

애플리케이션 실행을 마친 후에는 탭을 닫아 리소스를 해제하도록 Notebook을 종료하거나, Notebook 아래쪽에 있는 상태 패널에서 **세션 종료**를 선택합니다.

## <a name="see-also"></a>참고 항목

- [개요: Azure Synapse Analytics의 Apache Spark](apache-spark-overview.md)

## <a name="next-steps"></a>다음 단계

- [Apache Spark용 .NET 설명서](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 공식 설명서](https://spark.apache.org/docs/latest/)

>[!NOTE]
> 일부 공식 Apache Spark 설명서에서는 Azure Synapse Spark에서 사용할 수 없는 Spark 콘솔을 사용합니다. [Notebook](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 또는 [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 환경을 대신 사용하세요.
