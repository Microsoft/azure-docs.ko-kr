---
title: Spark로 데이터 탐색 및 모델링 - Team Data Science Process
description: Azure에서 Spark MLlib 도구 키트의 데이터 탐색 및 모델링 기능을 소개합니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: acc701431afa458efd0768fb3d6898fd1920e333
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811167"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Spark로 데이터 탐색 및 모델링

이 연습에서는 HDInsight Spark를 사용하여 NYC Taxi Trip 및 요금 2013 데이터 세트의 샘플에 데이터 탐색과 이진 분류 및 회귀 모델링 작업을 수행합니다.  처리를 위한 HDInsight Spark 클러스터와 데이터 및 모델을 저장하는 Azure Blob을 사용하여 [데이터 과학 프로세스](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)의 단계를 종단 간 안내합니다. 프로세스는 Azure Storage Blob에서 가져온 데이터를 탐색하고 시각화한 다음 데이터를 준비하여 예측 모델을 빌드합니다. 이러한 모델은 Spark MLlib 도구 키트를 사용하여 이진 분류 및 회귀 모델링 작업을 수행합니다.

* **이진 분류** 작업은 여정에 대해 팁이 지불되었는지 여부를 예측합니다. 
* **회귀** 작업은 다른 팁 기능을 기반으로 하는 팁의 금액을 예측합니다. 

사용하는 모델은 로지스틱 및 선형 회귀, 임의 포리스트, 그라데이션 향상된 트리를 포함합니다.

* [SGD로 선형 회귀](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) 는 SGD(Stochastic Gradient Descent) 메서드를 사용하는 선형 회귀 모델이며 최적화 및 기능에 대해 크기를 조정하여 결재된 팁 금액을 예측합니다. 
* [LBFGS로 로지스틱 회귀 분석](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) 또는 "로짓" 회귀는 종속 변수가 데이터 분류를 수행하는 범주인 경우 사용할 수 있는 회귀 모델입니다. LBFGS는 제한된 양의 컴퓨터 메모리를 사용하여 BFGS(Broyden–Fletcher–Goldfarb–Shanno) 알고리즘을 비슷하게 만들고 기계 학습에서 널리 사용되는 준 뉴턴 최적화 알고리즘입니다.
* [임의 포리스트](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) 는 결정 트리의 결합체입니다.  즉, 과잉 맞춤의 위험을 줄이기 위해 많은 결정 트리를 결합합니다. 임의 포리스트는 회귀 및 분류에 사용되며 범주 기능을 처리하고 다중 클래스 분류 설정으로 확장할 수 있습니다. 기능 크기 조정을 필요로 하지 않으며 비선형 및 기능 상호 작용을 캡처할 수 있습니다. 임의 포리스트는 분류 및 회귀를 위해 매우 성공적인 기계 학습 모델 중 하나입니다.
* [GBT](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (그라데이션 승격 트리)는 결정 트리의 결합체입니다. GBT는 기능 손실을 최소화하기 위해 결정 트리를 반복적으로 학습합니다. GBT는 회귀 및 분류에 사용되며 범주 기능을 처리하고 기능 크기 조정을 필요로 하지 않으며 비선형 및 기능 상호 작용을 캡처할 수 있습니다. 또한 다중 클래스 분류 설정에도 사용할 수 있습니다.

또한 모델링 단계에는 각 모델 유형을 학습, 평가 및 저장하는 방법을 보여주는 코드가 포함되어 있습니다. Python은 솔루션을 코딩하고 관련 차트를 표시하는 데 사용합니다.   

> [!NOTE]
> Spark MLlib 도구 키트가 큰 데이터 세트에서 작동하도록 디자인되었지만 여기서는 편의상 비교적 작은 샘플을 사용합니다(30Mb보다 작은 170,000개 행 즉, 원래 NYC 데이터 세트의 약 0.1%를 사용함). 여기에 제공된 연습은 2개의 작업자 노드를 가진 HDInsight 클러스터에서 효율적으로 실행됩니다(약 10분 동안). 동일한 코드를 약간만 수정하면 데이터를 메모리에 캐시하고 클러스터 크기를 변경하도록 적절하게 수정하여 더 큰 데이터 집합을 처리하는 데 사용할 수 있습니다.
> 
> 

## <a name="prerequisites"></a>필수 조건
이 연습을 완료하려면 Azure 계정과 Spark 1.6(또는 Spark 2.0) HDInsight 클러스터가 필요합니다. 이러한 요구 사항을 충족시키는 방법에 대한 자세한 지침은 [Azure HDInsight에서 Spark를 사용하는 데이터 과학 개요](spark-overview.md)를 참조하세요. 이 항목에는 여기에서 사용된 NYC 2013 Taxi 데이터에 대한 설명 및 Spark 클러스터의 Jupyter Notebook에서 코드를 실행하는 방법에 대한 지침이 포함되어 있습니다. 

## <a name="spark-clusters-and-notebooks"></a>Spark 클러스터 및 Notebook
설치 단계와 코드는 HDInsight Spark 1.6을 사용하는 이 연습에 제공됩니다. 하지만 Jupyter Notebook은 HDInsight Spark 1.6과 Spark 2.0 클러스터 둘 다에 제공됩니다. 노트북과 이에 연결된 링크의 설명은 이들을 포함하는 GitHub 리포지토리의 [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md)에 제공됩니다. 그뿐 아니라 여기에 있는 코드와 연결된 Notebook에 있는 코드는 일반적이므로 아무 Spark 클러스터에서나 작동할 것입니다. HDInsight Spark를 사용하지 않는 경우 클러스터 설치 및 관리 단계가 여기에 나오는 내용과 약간 다를 수 있습니다. 편의를 위해, Jupyter Notebook 서버의 pySpark 커널에서 실행되는 Spark 1.6 및 Jupyter Notebook 서버의 pySpark3 커널에서 실행되는 Spark 2.0용 Jupyter Notebook에 연결된 링크는 다음과 같습니다.

### <a name="spark-16-notebooks"></a>Spark 1.6 Notebook

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): 여러 가지 알고리즘을 사용하여 데이터 탐색, 모델링 및 채점을 수행하는 방법에 대한 정보를 제공합니다.

### <a name="spark-20-notebooks"></a>Spark 2.0 노트북
Spark 2.0 클러스터를 사용하여 구현되는 회귀 및 분류 작업은 별도의 Notebook에 위치하며 분류 Notebook은 다른 데이터 집합을 사용합니다.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): 이 파일은 [여기](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data) 설명된 NYC Taxi 운행 및 요금 데이터 세트를 사용하여 Spark 2.0 클러스터에서 데이터 탐색, 모델링, 채점을 수행하는 방법에 대한 정보를 제공합니다. 이 Notebook은 Spark 2.0에 대해 제공했던 코드를 신속하게 탐색하기 위한 좋은 시작점일 수 있습니다. NYC Taxi 데이터를 분석하는 Notebook 상세 정보는 이 목록에서 다음 Notebook을 참조하세요. 이러한 Notebook을 비교하는 목록 다음의 참고 사항을 확인하세요. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): 이 파일은 [여기](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data)에 설명된 NYC Taxi 운행 및 요금 데이터 세트를 사용하여 데이터 랭글링(Spark SQL 및 데이터 프레임 작업), 탐색, 모델링 및 채점을 수행하는 방법을 보여줍니다.
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): 이 파일은 2011년 및 2012년의 유명 항공사 정시 출발 데이터 세트를 사용한 데이터 랭글링(Spark SQL 및 데이터 프레임 작업), 탐색, 모델링 및 채점을 수행하는 방법을 보여줍니다. 날씨 요소를 모델에 포함시킬 수 있도록 모델링하기 전에 항공사 데이터 세트와 공항 날씨 데이터(예: 풍속, 온도, 고도 등)를 통합하였습니다.

<!-- -->

> [!NOTE]
> 항공사 데이터 세트는 분류 알고리즘의 사용 이해를 돕기 위해 Spark 2.0 Notebook에 추가되었습니다. 항공사 정시 출발 데이터 세트 및 날씨 데이터 세트에 대한 내용은 다음 링크를 참조하세요.
> 
> - 항공사 정시 출발 데이터: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - 공항 날씨 데이터: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 

<!-- -->

<!-- -->

> [!NOTE]
> NYC taxi의 Spark 2.0 Notebook 및 항공사 비행 지연 데이터 집합은 실행하는 데 10분 이상이 소요될 수 있습니다(HDI 클러스터의 크기에 따라 다름). 위의 목록에 있는 첫 번째 노트북은 택시 및 요금 파일이 미리 조인된 다운 샘플링된 NYC 데이터 세트로 실행하면 시간이 더 적게 걸리는 노트북에서 데이터 탐색, 시각화 및 ML 모델 학습의 다양한 측면을 보여줍니다. [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) 이 노트북은 완료하는 데 훨씬 짧은 시간(2~3분)이 소요되며 Spark 2.0에 제공된 코드를 신속하게 살펴보기에 좋은 출발점이 될 수 있습니다. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
> 아래 설명은 Spark 1.6 사용에 대한 내용입니다. Spark 2.0 버전의 경우 위에 설명 및 링크된 Notebook을 사용합니다. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>설정: 저장소 위치, 라이브러리 및 사전 설정 Spark 컨텍스트
Spark는 Azure Storage Blob(WASB라고도 함)를 읽고 쓸 수 있습니다. 따라서 Spark 및 WASB에 다시 저장된 결과를 사용하여 해당 저장소에 저장된 기존 데이터를 처리할 수 있습니다.

모델 또는 파일을 WASB에 저장하려면 경로를 올바르게 지정해야 합니다. "wasb///"로 시작하는 경로를 사용하여 Spark 클러스터에 연결된 기본 컨테이너를 참조할 수 있습니다. 다른 위치를 “wasb://”에서 참조합니다.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>WASB의 저장소 위치에 대 한 디렉터리 경로를 설정합니다.
다음 코드 샘플은 읽을 데이터의 위치 및 모델 출력을 저장할 모델 저장소 디렉터리에 대한 경로를 지정합니다.

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>라이브러리 가져오기
또한 필요한 라이브러리를 가져와야 합니다. Spark 컨텍스트를 설정하고 다음 코드를 사용하여 필요한 라이브러리를 가져옵니다.

    # IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>미리 설정된 Spark 컨텍스트 및 PySpark 매직
Jupyter Notebook과 함께 제공되는 PySpark 커널에는 사전 설정 컨텍스트가 있습니다. 따라서 개발 중인 애플리케이션으로 작업을 시작하기 전에 Spark 또는 Hive 컨텍스트를 명시적으로 설정할 필요는 없습니다. 이러한 컨텍스트는 기본적으로 사용할 수 있습니다. 이러한 컨텍스트는 다음과 같습니다.

* sc - Spark용 
* sqlContext - Hive용

PySpark 커널은 특수 명령인 일부 미리 정의된 "매직"을 제공하며 이러한 매직은 %%를 사용하여 호출할 수 있습니다. 이러한 코드 샘플에 사용되는 다음과 같은 두 가지 명령이 있습니다.

* **%%local** 다음 줄의 코드는 로컬로 실행됩니다. 코드는 유효한 Python 코드여야 합니다.
* **%%sql-o \<변수 이름 >** sqlContext에 대해 Hive 쿼리를 실행 합니다. -o 매개 변수가 전달된 경우 쿼리 결과가 %%local Python 컨텍스트에서 Pandas 데이터 프레임으로 유지됩니다.

Jupyter Notebook의 커널 및 제공되는 미리 정의된 "매직"에 대한 자세한 내용은 [HDInsight의 HDInsight Spark Linux 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)을 참조하세요.

## <a name="data-ingestion-from-public-blob"></a>공용 blob에서 데이터 수집
데이터 과학 프로세스의 첫 번째 단계는 원본에서 분석할 데이터를 수집하여 데이터 탐색 및 모델링 환경에 상주시키는 것입니다. 이 연습에서 이 환경은 Spark입니다. 이 섹션은 일련의 작업을 완료하는 코드를 포함합니다.

* 모델링할 데이터 샘플 수집
* 입력 데이터 세트 읽기(.tsv 파일로 저장됨)
* 데이터 포맷 및 정리
* 메모리에 개체 만들기 및 캐시(RDD 또는 데이터 프레임)
* SQL 컨텍스트에 임시 테이블로 등록

데이터 수집에 대한 코드는 다음과 같습니다.

    # INGEST DATA

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**출력:**

위의 셀을 실행하는 데 걸린 시간: 51.72초

## <a name="data-exploration--visualization"></a>데이터 탐색 및 시각화
데이터를 Spark로 가져오면 데이터 과학 프로세스의 다음 단계에서 탐색 및 시각화를 통해 데이터를 더 잘 이해할 수 있습니다. 이 섹션에서는 SQL 쿼리를 사용하여 Taxi 데이터를 검사하고 시각적 조사에 대한 대상 변수 및 잠재 기능을 그립니다. 특히, Taxi Trip에서 승객 수의 빈도, 팁 금액의 빈도 및 지불 금액 및 형식에 따른 팁의 변화를 그립니다.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Taxi Trip의 샘플에서 승객 수 빈도의 히스토그램을 그립니다.
이 코드와 후속 코드 조각은 샘플을 쿼리하는 데 SQL 매직을 사용하고 데이터를 그리는 데 로컬 매직을 사용합니다.

* **SQL 매직(`%%sql`)** HDInsight PySpark 커널은 sqlContext에 대해 간편한 인라인 HiveQL 쿼리를 지원합니다. (-o VARIABLE_NAME) 인수는 Jupyter 서버에서 Pandas 데이터 프레임으로 SQL 쿼리의 출력을 유지합니다. 즉, 로컬 모드에서 사용할 수 있습니다.
* **`%%local` 매직** 은 HDInsight 클러스터의 헤드 노드인 Jupyter 서버에서 코드를 로컬로 실행하는 데 사용됩니다. 일반적으로 -o 매개 변수를 사용하여 `%%local` 매직을 `%%sql` 매직과 함께 사용합니다. -o 매개 변수는 SQL 쿼리의 출력을 로컬로 유지하고 그 다음 %%local 매직은 로컬로 유지되는 SQL 쿼리의 출력에 대해 로컬로 실행할 다음 코드 조각 집합을 트리거합니다.

코드를 실행한 후 출력이 자동으로 시각화됩니다.

이 쿼리는 승객 수에 따라 여정을 검색합니다. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

이 코드는 쿼리 출력에서 로컬 데이터 프레임을 만들고 데이터를 그립니다. `%%local` 매직은 로컬 데이터 프레임 `sqlResults`를 만드는데, 이것은 matplotlib로 그리는 데 사용할 수 있습니다. 

> [!NOTE]
> 이 PySpark 매직은 이 연습에서 여러 번 사용됩니다. 데이터 양이 많은 경우 로컬 메모리에 맞게 데이터 프레임을 만들도록 샘플링해야 합니다.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

다음은 승객 수에 따라 여정을 그리는 코드입니다.

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**출력:**

![승객 수에 따른 여정 빈도](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Notebook의 **형식** 메뉴 버튼을 사용하여 다양한 시각화 형식(테이블, 원형, 꺾은선형, 영역 또는 막대) 중에서 선택할 수 있습니다. 막대 그리기는 다음과 같습니다.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>승객 수 및 요금 금액에 따라 팁 금액이 어떻게 달라지는지와 팁 금액에 대한 히스토그램을 그립니다.
SQL 쿼리를 사용하여 데이터를 샘플링합니다.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped 
    FROM taxi_train 
    WHERE passenger_count > 0 
    AND passenger_count < 7 
    AND fare_amount > 0 
    AND fare_amount < 200 
    AND payment_type in ('CSH', 'CRD') 
    AND tip_amount > 0 
    AND tip_amount < 25


이 코드 셀에서는 SQL 쿼리를 사용하여 3가지로 데이터 그리기를 만듭니다.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 100, -2, 20])
    plt.show()


**출력:** 

![팁 금액 분포](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![승객 수에 따른 여정 수](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![금액으로 녀건 금액](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>모델링에 대한 기능 엔지니어링, 변환 및 데이터 준비
이 섹션에서는 기계 학습 모델링에 사용할 데이터를 준비하는 데 사용되는 프로시저에 대한 코드를 설명하고 제공합니다. 다음 작업을 수행하는 방법을 보여줍니다.

* 시간을 트래픽 시간 버킷으로 범주화하여 새로운 기능 만들기
* 범주 기능 인덱스 및 인코딩
* 기계 학습 함수에 입력에 대한 레이블이 지정된 지점 개체 만들기
* 데이터의 하위 무작위 샘플링을 만들고 학습 및 테스트 집합으로 분할합니다
* 기능 크기 조정
* 메모리에서 개체 캐시

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>시간을 트래픽 시간 버킷으로 범주화하여 새로운 기능 만들기
이 코드는 트래픽 시간 버킷으로 시간을 범주화하여 새로운 기능을 만드는 방법 및 메모리에 결과 데이터 프레임을 캐시하는 방법을 보여줍니다. RDD(복원력 있는 분산된 데이터 세트) 및 데이터 프레임을 반복해서 사용하는 경우 캐시하면 실행 시간이 향상됩니다. 따라서 RDD 및 데이터 프레임을 연습의 여러 단계에서 캐시합니다. 

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**출력:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>모델링 기능에 입력에 대한 범주 기능 인덱스 및 인코딩
이 섹션에는 모델링 기능에 입력에 대한 범주 기능을 인덱싱하거나 인코딩하는 방법을 보여줍니다. MLlib의 모델링 및 예측 함수는 사용하기 전에 범주 입력 데이터로 기능을 인덱싱 또는 인코딩해야 합니다. 모델에 따라 다양한 방식에서 인덱싱하거나 인코딩해야 합니다.  

* **트리 기반 모델링**은 범주를 숫자 값(예: 3개 범주가 있는 기능은 0, 1, 2로 인코딩 가능)으로 인코딩해야 합니다. MLlib의 [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) 함수에서 제공됩니다. 이 함수는 레이블의 문자열 열을 레이블 주파수에서 정렬한 레이블 인덱스의 열로 인코딩합니다. 입력 및 데이터 처리를 위해 숫자 값으로 인덱스되더라도 범주로 처리되도록 트리 기반 알고리즘을 적절히 지정할 수 있습니다. 
* **로지스틱 및 선형 회귀 모델**은 원 핫 인코딩(one-hot encoding)이 필요하며 이런 경우, 예를 들어 3개의 범주가 있는 기능이 관찰 범주에 따라 각각 0 또는 1을 포함하는 3개의 기능 열로 확장될 수 있습니다. MLlib는 원 핫 인코딩 작업을 수행하는 [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 함수를 제공합니다. 이 인코더는 레이블 인덱스의 열을 단 하나의 값을 가진 이진 벡터의 열에 매핑합니다. 이 인코딩을 사용하여 로지스틱 회귀 분석 등과 같은 숫자 값을 가진 기능을 예상하는 알고리즘을 범주 기능에 적용할 수 있습니다.

다음은 범주 기능을 인덱스 및 인코딩하는 코드입니다.

    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

위의 셀을 실행하는 데 걸린 시간: 1.28초

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>기계 학습 함수에 입력에 대한 레이블이 지정된 지점 개체 만들기
이 섹션은 범주 텍스트 데이터를 레이블이 지정된 지점 데이터 형식으로 인덱싱하고 인코딩하는 방법을 보여주는 코드를 포함하여 MLlib 로지스틱 회귀 및 다른 분류 모델을 학습하고 테스트하는 데 사용될 수 있습니다. 레이블이 지정된 지점 개체는 대부분 MLlib의 기계 학습 알고리즘에서 입력 데이터로 필요한 방식으로 형식이 지정된 RDD(Resilient Distributed Datasets)입니다. [레이블이 지정된 지점](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) 은 레이블/응답과 연결된 로컬 벡터, 밀도 또는 스파스입니다.  

이 섹션은 범주 텍스트 데이터를 [레이블이 지정된 지점](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) 데이터 형식으로 인덱싱하고 인코딩하는 방법을 보여주는 코드를 포함하여 MLlib 로지스틱 회귀 및 다른 분류 모델을 학습하고 테스트하는 데 사용될 수 있습니다. 레이블이 지정된 지점 개체는 레이블(대상/응답 변수) 및 기능 벡터로 구성된 RDD(Resilient Distributed Datasets)입니다. 이 서식은 MLlib의 많은 기계 학습 알고리즘에서 입력으로 필요합니다.

다음은 이진 분류를 위해 텍스트 기능을 인덱싱 및 인코딩하는 코드입니다.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


다음은 선형 회귀 분석을 위해 범주 텍스트 기능을 인코딩 및 인덱싱하는 코드입니다.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])

        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>데이터의 하위 무작위 샘플링을 만들고 학습 및 테스트 집합으로 분할합니다
이 코드는 데이터의 무작위 샘플링을 만듭니다(25%가 여기에서 사용됨). 데이터 세트의 크기로 인해 이 예제에 필요하지 않지만 필요한 경우 자신의 문제에 사용하는 방식을 알 수 있도록 여기서 샘플링할 수 있는 방법을 설명합니다. 샘플이 큰 경우 모델을 학습하는 동안 상당한 시간을 절약할 수 있습니다. 다음 샘플을 교육 부분(여기서 75%)와 테스트 부분(여기서 25%)로 분할하여 분류 및 회귀 모델링에 사용합니다.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

위의 셀을 실행하는 데 걸린 시간: 0.24초

### <a name="feature-scaling"></a>기능 크기 조정
데이터 정규화라고도 하는 기능 크기 조정은 폭 넓게 분배된 값을 가진 기능이 목적 함수에서 과도한 가중치를 부여하지 않도록 합니다. 기능 크기 조정에 대한 코드는 단위 분산에 대한 기능의 크기를 조정하는 [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) 를 사용합니다. 정칙 회귀 또는 SVM(support vector machine)과 같은 광범위한 다른 기계 학습 모델을 학습하기 위한 인기 있는 알고리즘인 SGD(Stochastic Gradient Descent)와 함께 선형 회귀에 사용하기 위해 MLlib에서 제공합니다.

> [!NOTE]
> LinearRegressionWithSGD 알고리즘이 기능 크기 조정에 민감하다는 점을 발견했습니다.
> 
> 

여기에 정칙 선형 SGD 알고리즘에 사용할 변수의 크기를 조정하는 코드가 있습니다.

    # FEATURE SCALING

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils

    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

위의 셀을 실행하는 데 걸린 시간: 13.17초

### <a name="cache-objects-in-memory"></a>메모리에서 개체 캐시
분류, 회귀 및 확장된 기능에 사용되는 입력 데이터 프레임 개체를 캐시하여 ML(기계 학습) 알고리즘의 학습 및 테스트에 소요된 시간을 줄일 수 있습니다.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()

    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:** 

위의 셀을 실행하는 데 걸린 시간: 0.15초

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>팁이 지불되었는지 여부를 이진 분류 모델로 예측합니다.
이 섹션에서는 Taxi Trip에 팁을 지불했는지 여부를 예측하는 이진 분류 작업에 대한 세 개의 모델을 사용하는 방법을 보여줍니다. 제공된 모델은 다음과 같습니다.

* 정칙 로지스틱 회귀 
* 임의 포리스트 모델
* 그라데이션 향상 트리

코드 섹션을 빌드하는 각 모델은 다음과 같은 단계로 분할됩니다. 

1. **모델 교육** 데이터
2. **모델 평가** 
3. **모델 저장** 

### <a name="classification-using-logistic-regression"></a>로지스틱 회귀를 사용하는 분류
이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 세트에서 팁이 여정에 지불되었는지를 예측하는 [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) 로 로지스틱 회귀 분석 모델을 학습하고 평가하며 저장하는 방법을 보여 줍니다.

**CV 및 하이퍼 매개 변수 비우기를 사용하여 로지스틱 회귀 모델 학습**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics


    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**출력:** 

Coefficients: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Intercept: -0.0111216486893

위의 셀을 실행하는 데 걸린 시간: 14.43초

**표준 메트릭을 사용한 이진 분류 모델 평가**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**출력:** 

Area under PR = 0.985297691373

Area under ROC = 0.983714670256

Summary Stats

Precision = 0.984304060189

Recall = 0.984304060189

F1 Score = 0.984304060189

위의 셀을 실행하는 데 걸린 시간: 57.61초

**ROC 곡선을 그립니다.**

*predictionAndLabelsDF*는 이전 셀에서 테이블 *tmp_results*로 등록되었습니다. *tmp_results*를 사용하면 쿼리를 수행하고 결과를 sqlResults 데이터 프레임으로 출력하여 그래프에 표시할 수 있습니다. 코드는 다음과 같습니다.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


ROC 곡선을 그리고 예측을 수행하는 코드는 다음과 같습니다.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**출력:**

![로지스틱 회귀 ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>임의 포리스트 분류
이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 세트에서 팁이 여정에 지불되었는지 여부를 예측하는 임의 포리스트 모델을 학습, 평가, 저장하는 방법을 보여줍니다.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

Area under ROC = 0.985297691373

위의 셀을 실행하는 데 걸린 시간: 31.09초

### <a name="gradient-boosting-trees-classification"></a>그라데이션 향상 트리 분류
이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 세트에서 팁이 여정에 지불되었는지 여부를 예측하는 그라데이션 향상 트리 모델을 학습, 평가, 저장하는 방법을 보여줍니다.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;

    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**출력:**

Area under ROC = 0.985297691373

위의 셀을 실행하는 데 걸린 시간: 19.76초

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>회귀 모델로 Taxi Trip에 대한 팁 금액을 예측합니다.
이 섹션에서는 다른 팁 기능에 따라 지불한 팁의 금액을 예측하는 회귀 작업에 대한 세 가지 모델을 사용하는 방법을 보여줍니다. 제공된 모델은 다음과 같습니다.

* 정칙 선형 회귀
* 임의 포리스트
* 그라데이션 향상 트리

이러한 모델은 소개에서 설명했습니다. 코드 섹션을 빌드하는 각 모델은 다음과 같은 단계로 분할됩니다. 

1. **모델 교육** 데이터
2. **모델 평가** 
3. **모델 저장** 

### <a name="linear-regression-with-sgd"></a>SGD로 선형 회귀
이 섹션의 코드는 크기 조정된 기능을 사용하여 최적화를 위해 SGD(Stochastic Gradient Descent)를 사용하는 선형 회귀를 학습하는 방법 및 WASB(Azure Blob Storage)에서 모델의 점수를 매기고 평가하며 저장하는 방법을 보여줍니다.

> [!TIP]
> 경험에 따르면 LinearRegressionWithSGD 모델의 수렴과 관련된 문제가 발생할 수 있으며 매개 변수는 유효한 모델을 얻기 위해 신중하게 변경/최적화되어야 합니다. 변수의 크기를 조정하면 수렴에 큰 도움이 됩니다. 
> 
> 

    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats

    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))

    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)

    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

Coefficients: [0.00457675809917, -0.0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981, -0.000987181489428, -0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995, -0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

Intercept: 0.853872718283

RMSE = 1.24190115863

R-sqr = 0.608017146081

위의 셀을 실행하는 데 걸린 시간: 58.42초

### <a name="random-forest-regression"></a>임의 포리스트 회귀
이 섹션의 코드에서는 NYC Taxi Trip 데이터에서 팁 금액을 예측하는 임의 포리스트 회귀를 학습, 평가, 저장하는 방법을 보여줍니다.

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

RMSE = 0.891209218139

R-sqr = 0.759661334921

위의 셀을 실행하는 데 걸린 시간: 49.21초

### <a name="gradient-boosting-trees-regression"></a>그라데이션 향상 트리 회귀
이 섹션의 코드에서는 NYC Taxi Trip 데이터에서 팁 금액을 예측하는 그라데이션 향상 트리 모델을 학습, 평가, 저장하는 방법을 보여줍니다.

**학습 및 평가**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

RMSE = 0.908473148639

R-sqr = 0.753835096681

위의 셀을 실행하는 데 걸린 시간: 34.52초

**그림**

*tmp_results*는 이전 셀에서 Hive 테이블로 등록되어 있습니다. 테이블의 결과는 그래프로 나타내기 위해 *sqlResults* 데이터 프레임에 출력됩니다. 코드는 다음과 같습니다.

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

다음은 Jupyter 서버를 사용하여 데이터를 그리는 코드입니다.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)


**출력:**

![Actual-vs-predicted-tip-amounts](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>메모리에서 개체 정리
`unpersist()` 를 사용하여 메모리에 캐시된 개체를 삭제합니다.

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()

    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>소비와 점수 매기기에 대한 모델의 저장소 위치 레코드
[Spark 빌드된 기계 학습 모델 점수 매기기 및 평가](spark-model-consumption.md) 항목에서 설명한 독립적인 데이터 세트를 사용하고 점수를 매기려면 여기서 Consumption Jupyter Notebook에 만든 저장된 모델을 포함하는 이러한 파일 이름을 복사하고 붙여넣어야 합니다. 필요한 모델 파일에 대한 경로를 출력하는 코드는 다음과 같습니다.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**출력**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>다음 작업
Spark MlLib로 회귀 및 분류 모델을 만든 경우 이러한 모델의점수를  매기고 평가하는 방법을 배울 수 있습니다. 고급 데이터 탐색 및 모델링 Notebook은 교차 유효성 검사, 하이퍼 매개 변수 비우기 및 모델 평가 등을 포함한 상세 영역으로 나뉩니다. 

**모델 사용:** 이 항목에서 만든 분류 및 회귀 모델의 점수를 매기고 평가하는 방법을 알아보려면 [Spark로 빌드된 기계 학습 모델 점수 매기기 및 평가](spark-model-consumption.md)를 참조하세요.

**교차 유효성 검사 및 하이퍼 매개 변수 비우기**: 교차 유효성 검사 및 하이퍼 매개 변수 비우기를 사용하여 모델을 학습하는 방법은 [Spark로 고급 데이터 탐색 및 모델링](spark-advanced-data-exploration-modeling.md)을 참조하세요.

