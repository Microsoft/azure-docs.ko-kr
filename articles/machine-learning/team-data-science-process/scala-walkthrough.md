---
title: Azure에서 Scala 및 Spark를 사용하는 데이터 과학 - Team Data Science Process
description: Azure HDInsight Spark 클러스터에서 Spark 확장형 MLlib 및 Spark ML 패키지를 사용하여 감독 기계 학습 작업에 대해 Scala를 사용하는 방법을 설명합니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: cdc37ace4687fe978030f528dcd5cbc87da596f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589485"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Azure에서 Scala 및 Spark를 사용하는 데이터 과학
이 문서에서는 Azure HDInsight Spark 클러스터에서 Spark 확장형 MLlib 및 SparkML 패키지를 사용하여 감독 기계 학습 작업에 대해 Scala를 사용하는 방법을 설명합니다. 또한 [데이터 과학 프로세스](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): 데이터 수집 및 탐색, 시각화, 기능 엔지니어링, 모델링 및 모델 사용으로 이루어진 작업을 단계별로 안내합니다. 이 문서의 모델에는 두 가지 일반적인 감독 기계 학습 작업 외에 로지스틱 및 선형 회귀, 임의 포리스트 및 GBT(그라데이션 향상 트리)가 포함됩니다.

* 회귀 문제: 택시 운행에 대한 팁 액수 예측($)
* 이진 분류: 택시 운행에 대한 팁 또는 노팁(1/0) 예측

모델링 프로세스에는 관련 정확도 메트릭을 통한 테스트 데이터 집합의 학습 및 평가가 필요합니다. 이 문서에서는 이러한 모델을 Azure Blob Storage에 저장하고 예측 성능의 점수를 매기며 평가하는 방법을 설명합니다. 그뿐 아니라 교차 유효성 검사 및 하이퍼 매개 변수 스윕 작업을 사용하여 모델을 최적화하는 방법의 고급 항목도 포함되어 있습니다. 사용되는 데이터는 GitHub에서 사용할 수 있는 2013 NYC 택시 여정 및 요금 데이터 집합의 샘플입니다.

Java 가상 머신 기반 언어인 [Scala](https://www.scala-lang.org/)는 개체 지향 개념과 함수 언어 개념을 통합합니다. 클라우드의 분산형 처리 및 Azure Spark 클러스터에 실행하기에 적합한 확장형 언어입니다.

[Spark](https://spark.apache.org/)는 메모리 내 처리를 지원하여 빅 데이터 분석 애플리케이션의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다. 속도, 간편한 사용 및 정교한 분석을 위해 Spark 처리 엔진이 빌드되었습니다. Spark는 메모리 내 분산형 계산 기능을 지원하여 기계 학습 및 그래프 계산의 반복 알고리즘에 적합합니다. [spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) 패키지는 DataFrames 맨 위에 빌드된 균일한 상위 수준 API 집합을 제공하여 사용자가 실제 기계 학습 파이프라인을 만들고 조정할 수 있도록 합니다. [MLlib](https://spark.apache.org/mllib/) 는 Spark의 확장형 기계 학습 라이브러리로, 분산형 환경에서 모델링 기능을 사용할 수 있습니다.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md)는 Azure에서 호스트하는 오픈 소스 Spark의 제품입니다. 또한 Azure Blob Storage에 저장된 데이터를 변환, 필터링 및 시각화하기 위해 Spark SQL 대화형 쿼리를 실행할 수 있는 Spark 클러스터 상의 Jupyter Scala Notebook에 대한 지원도 포함하고 있습니다. 이 문서에서 솔루션을 제공하고 데이터 시각화를 위해 관련 플롯을 보여 주는 Scala 코드 조각은 Spark 클러스터에 설치된 Jupyter Notebook에서 실행됩니다. 이러한 항목의 모델링 단계는 각 모델 형식을 학습, 평가, 저장 및 사용하는 방법을 보여 주는 코드를 포함하고 있습니다.

이 문서의 설정 단계 및 코드는 Azure HDInsight 3.4 Spark 1.6용입니다. 그러나 이 문서에 나오는 코드와 [Scala Jupyter Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) 에 포함된 코드는 일반적이므로 모든 Spark 클러스터에서 작동해야 합니다. HDInsight Spark를 사용하지 않는 경우 클러스터 설치 및 관리 단계가 이 문서에 나오는 내용과 약간 다를 수 있습니다.

> [!NOTE]
> Scala가 아닌 Python을 사용하여 종단 간 데이터 과학 프로세스에 대한 작업을 완료하는 방법을 보여 주는 항목에 대해서는 [Azure HDInsight에서 Spark를 사용하는 데이터 과학](spark-overview.md)을 참조하세요.
> 
> 

## <a name="prerequisites"></a>필수 조건
* Azure 구독이 있어야 합니다. 아직 없으면 [Azure 무료 평가판을 다운로드하세요](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* 다음 절차를 완료하려면 Azure HDInsight 3.4 Spark 1.6 클러스터가 필요합니다. 클러스터를 만들려면 [시작: Azure HDInsight에서 Apache Spark 만들기](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)를 참조하세요. **클러스터 형식 선택** 메뉴에서 클러스터 형식 및 버전을 설정합니다.

![HDInsight 클러스터 형식 구성](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

NYC 택시 여정 데이터에 대한 설명 및 Spark 클러스터의 Jupyter Notebook에서 코드를 실행하는 방법에 관한 지침은 [Azure HDInsight에서 Spark를 사용하는 데이터 과학 개요](spark-overview.md)의 관련 섹션을 참조하세요.  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spark 클러스터의 Jupyter Notebook에서 Scala 코드 실행
Azure 포털에서 Jupyter Notebook을 시작할 수 있습니다. 대시보드에서 Spark 클러스터를 찾아 클릭하여 클러스터에 대한 관리 페이지로 들어갑니다. 다음으로 **클러스터 대시보드**, **Jupyter Notebook**을 차례로 클릭하여 Spark 클러스터와 연결된 Notebook을 엽니다.

![클러스터 대시보드 및 Jupyter Notebook](./media/scala-walkthrough/spark-jupyter-on-portal.png)

https://&lt;clustername&gt;.azurehdinsight.net/jupyter에서 Jupyter Notebook에 액세스할 수도 있습니다. *clustername* 을 사용자의 클러스터 이름으로 바꿉니다. Jupyter Notebook에 액세스하려면 관리자 계정에 대한 암호가 필요합니다.

![클러스터 이름을 사용하여 Jupyter Notebook으로 이동](./media/scala-walkthrough/spark-jupyter-notebook.png)

**Scala** 를 선택하여 PySpark API를 사용하는 미리 패키지된 Notebook의 몇 가지 예제가 있는 디렉터리를 확인합니다. 이 Spark 항목 모음에 대한 코드 샘플이 포함된 Scala.ipynb Notebook을 사용하는 탐색 모델링 및 점수 매기기는 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)에서 사용할 수 있습니다.

Notebook을 Github에서 Spark 클러스터의 Jupyter Notebook 서버에 직접 업로드할 수 있습니다. Jupyter 홈페이지에서 **Upload** 단추를 클릭합니다. 파일 탐색기에서 Scala Notebook의 Github(원시 콘텐츠) URL을 붙여넣고 **열기**를 클릭합니다. Scala Notebook은 다음 URL로 제공됩니다.

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>설정: 사전 설정 Spark 및 Hive 컨텍스트, Spark 매직 및 Spark 라이브러리
### <a name="preset-spark-and-hive-contexts"></a>사전 설정 Spark 및 Hive 컨텍스트
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Jupyter Notebook이 제공되는 Spark 커널에는 사전 설정 컨텍스트가 있습니다. 개발하는 애플리케이션으로 작업을 시작하기 전에 Spark 또는 Hive 컨텍스트를 명시적으로 설정할 필요는 없습니다. 사전 설정 컨텍스트는 다음과 같습니다.

* `sc` 
* `sqlContext` 

### <a name="spark-magics"></a>Spark 매직
Spark 커널은 특수 명령인 일부 미리 정의된 "매직"을 제공하며 이러한 매직은 `%%`기호를 사용하여 호출할 수 있습니다. 이러한 명령 중 두 가지는 다음 코드 샘플에 사용됩니다.

* `%%local` 다음 줄의 코드가 로컬로 실행될 것임을 지정합니다. 코드는 유효한 Scala 코드여야 합니다.
* `%%sql -o <variable name>` `sqlContext`에 대해 Hive 쿼리를 실행합니다. `-o` 매개 변수가 전달된 경우 쿼리 결과가 `%%local` Scala 컨텍스트에서 Spark 데이터 프레임으로 유지됩니다.

Jupyter Notebook의 커널 및 `%%`(예: `%%local`)로 호출할 수 있는 미리 정의된 "매직"에 대한 자세한 내용은 [HDInsight의 HDInsight Spark Linux 클러스터에서 Jupyter Notebook에 대해 사용할 수 있는 커널](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)을 참조하세요.

### <a name="import-libraries"></a>라이브러리 가져오기
다음 코드를 사용하여 Spark, MLlib 및 기타 필요한 라이브러리를 가져옵니다.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>데이터 수집
데이터 과학 프로세스의 첫 단계는 분석하려는 데이터를 수집하는 것입니다. 외부 원본 또는 시스템의 데이터를 데이터 탐색 및 모델링 환경으로 가져옵니다. 이 문서에서 수집하는 데이터는 택시 여정 및 요금 파일(.tsv 파일로 저장됨)의 연결된 0.1% 샘플을 나타냅니다. 데이터 탐색 및 모델링 환경은 Spark입니다. 이 섹션은 다음과 같은 일련의 작업을 완료하는 코드를 포함합니다.

1. 데이터 및 모델 저장소에 대한 디렉터리 경로를 설정합니다.
2. 입력 데이터 집합을 읽습니다(.tsv 파일로 저장됨).
3. 데이터에 대한 스키마를 정의하고 데이터를 정리합니다.
4. 정리된 데이터 프레임을 만들고 메모리에 캐시합니다.
5. 데이터를 SQLContext의 임시 테이블로 등록합니다.
6. 테이블을 쿼리하고 결과를 데이터 프레임으로 가져옵니다.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Azure Blob Storage의 스토리지 위치에 대한 디렉터리 경로 설정
Spark에서는 Azure Blob Storage에서 읽고 쓸 수 있습니다. Spark를 사용하여 기존 데이터를 처리한 다음 결과를 Blob Storage에 다시 저장할 수 있습니다.

Blob Storage에 모델 또는 파일을 저장하려면 경로를 적절히 지정해야 합니다. `wasb:///`로 시작하는 경로를 사용하여 Spark 클러스터에 연결된 기본 컨테이너를 참조합니다. `wasb://`를 사용하여 다른 위치를 참조합니다.

다음 코드 샘플은 읽을 입력 데이터의 위치 및 모델이 저장될 Spark 클러스터에 연결된 Blob Storage에 대한 경로를 지정합니다.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>데이터 가져오기, RDD 만들기 및 스키마에 따라 데이터 프레임 정의
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

셀 실행 시간: 8초

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>테이블을 쿼리하고 결과를 데이터 프레임으로 가져오기
다음으로 요금, 승객 및 팁 데이터에 대한 테이블을 쿼리하고, 손상된 외부 데이터를 필터링하며 여러 행으로 인쇄합니다.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**출력:**

| fare_amount | passenger_count | tip_amount | tipped |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>데이터 탐색 및 시각화
데이터를 Spark로 가져오면 데이터 과학 프로세스의 다음 단계에서 탐색 및 시각화를 통해 데이터를 더 잘 이해할 수 있습니다. 이 섹션에서는 SQL 쿼리를 사용하여 Taxi 데이터를 검사합니다. 그런 후 결과를 데이터 프레임으로 가져와 Jupyter의 자동 시각화 기능을 사용하여 시각적 조사에 대한 대상 변수 및 잠재 기능을 그립니다.

### <a name="use-local-and-sql-magic-to-plot-data"></a>로컬 및 SQL 매직을 사용하여 데이터 그리기
기본적으로 Jupyter Notebook에서 실행하는 코드 조각의 출력은 작업자 노드에서 유지되는 세션 컨텍스트 내에서 사용할 수 있습니다. 모든 계산에 대한 작업자 노드에 여정을 저장하는 경우와 계산에 필요한 모든 데이터를 Jupyter 서버 노드(헤드 노드)에서 로컬로 사용할 수 있는 경우, `%%local` 매직을 사용하여 Jupyter 서버에서 코드 조각을 실행할 수 있습니다.

* **SQL 매직**(`%%sql`). HDInsight Spark 커널은 SQLContext에 대해 간편한 인라인 HiveQL 쿼리를 지원합니다. (`-o VARIABLE_NAME`) 인수는 Jupyter 서버에서 Pandas 데이터 프레임으로 SQL 쿼리의 출력을 유지합니다. 즉, 로컬 모드에서 사용할 수 있습니다.
* `%%local` **매직**. `%%local` 매직은 HDInsight 클러스터의 헤드 노드인 Jupyter 서버에서 코드를 로컬로 실행하는 데 사용됩니다. 일반적으로 `-o` 매개 변수를 사용하여 `%%local` 매직을 `%%sql` 매직과 함께 사용합니다. `-o` 매개 변수는 SQL 쿼리의 출력을 로컬로 유지하고 그 다음 `%%local` 매직은 로컬로 유지되는 SQL 쿼리의 출력에 대해 로컬로 실행할 다음 코드 조각 집합을 트리거합니다.

### <a name="query-the-data-by-using-sql"></a>SQL을 사용하여 데이터 쿼리
이 쿼리는 요금 금액, 승객 수 및 팁 금액에 따라 택시 여정을 검색합니다.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

다음 코드에서 `%%local` 매직은 sqlResults 로컬 데이터 프레임을 만듭니다. sqlResults의 matplotlib를 사용하여 그림을 그립니다.

> [!TIP]
> 로컬 매직은 이 문서에서 여러 번 사용됩니다. 데이터 집합이 큰 경우 로컬 메모리에 맞게 데이터 프레임을 만들도록 샘플링하세요.
> 
> 

### <a name="plot-the-data"></a>데이터 그리기
데이터 프레임이 Pandas 데이터 프레임처럼 로컬 컨텍스트에 있으면 Python 코드를 사용하여 그릴 수 있습니다.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Spark 커널은 코드를 실행한 후 SQL(HiveQL) 쿼리의 출력을 자동으로 시각화합니다. 여러 형식의 시각화 요소 중에서 선택할 수 있습니다.

* 테이블
* 원형
* 꺾은선형
* 영역
* 가로 막대형

다음은 데이터를 그리는 코드입니다.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**출력:**

![팁 금액 히스토그램](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![승객 수에 따른 여정 수](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![금액으로 녀건 금액](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>기능을 만들고 변환한 후 모델링 함수에 입력하기 위한 데이터 준비
Spark ML 및 MLlib의 트리 기반 모델링 기능의 경우 범주화, 인덱싱, 원 핫 인코딩(one hot encoding), 벡터화 등의 다양한 해당 기법을 사용하여 대상 및 기능을 준비해야 합니다. 이 섹션에서 수행하는 절차는 다음과 같습니다.

1. 시간을 트래픽 시간 버킷으로 **범주화** 하여 새로운 기능을 만듭니다.
2. 범주 기능에 **인덱싱 및 원 핫 인코딩(one-hot encoding)** 을 적용합니다.
3. **데이터 집합을 학습 및 테스트 부분으로 샘플링 및 분할** 합니다.
4. **학습 변수 및 기능을 지정**하고, 인덱싱되었거나 원 핫 인코딩된 학습 및 테스팅 입력 레이블이 지정된 지점 RDD(탄력성 분산 데이터 세트) 또는 데이터 프레임을 만듭니다.
5. 기계 학습 모델에 대한 입력으로 사용할 **기능과 대상을 자동으로 범주화 및 벡터화** 합니다.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>시간을 트래픽 시간 버킷으로 범주화하여 새로운 기능 만들기
이 코드는 트래픽 시간 버킷으로 시간을 범주화하여 새로운 기능을 만드는 방법 및 메모리에 결과 데이터 프레임을 캐시하는 방법을 설명합니다. RDD 및 데이터 프레임을 반복해서 사용하는 경우 캐시하면 실행 시간이 향상됩니다. 따라서 RDD 및 데이터 프레임을 다음 절차의 여러 단계에서 캐시합니다.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>범주 기능 인덱싱 및 원 핫 인코딩
MLlib의 모델링 및 예측 함수는 사용하기 전에 범주 입력 데이터로 기능을 인덱싱 또는 인코딩해야 합니다. 이 섹션에는 모델링 기능에 입력에 대한 범주 기능을 인덱싱하거나 인코딩하는 방법을 설명합니다.

모델에 따라 다양한 방식으로 모델을 인덱싱하거나 인코드해야 합니다. 예를 들어 로지스틱 및 선형 회귀 모델에는 원 핫 인코딩이 필요합니다. 예를 들어 세 개의 범주가 있는 기능을 세 개의 기능 열로 확장할 수 있습니다. 관찰 범주에 따라, 각 열에는 0 또는 1이 포함됩니다. MLlib는 원 핫 인코딩을 위한 [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 함수를 제공합니다. 이 인코더는 레이블 인덱스의 열을 단 하나의 값을 가진 이진 벡터의 열에 매핑합니다. 이 인코딩을 사용하여 로지스틱 회귀 분석 등과 같은 숫자 값을 가진 기능을 예상하는 알고리즘을 범주 기능에 적용할 수 있습니다.

여기서는 문자열 예를 보여 주기 위해 4개의 변수만 변환합니다. 평일과 같이 숫자 값으로 표현되는 기타 변수는 범주 변수로 인덱싱할 수도 있습니다.

인덱싱에는 `StringIndexer()` 함수를 사용하고 원 핫 인코딩에는 MLlib의 `OneHotEncoder()` 함수를 사용합니다. 다음은 범주 기능을 인덱스 및 인코딩하는 코드입니다.

    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

셀 실행 시간: 4초

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>데이터 집합을 학습 및 테스트 부분으로 샘플링 및 분할
이 코드는 데이터의 무작위 샘플링을 만듭니다(이 예제에서는 25%). 데이터 집합의 크기로 인해 이 예제에는 샘플링이 필요하지 않지만, 이 문서에서는 필요한 경우 자신의 문제에 사용하는 방식을 알 수 있도록 샘플링 방법을 설명합니다. 샘플이 큰 경우 모델을 학습하는 동안 상당한 시간을 절약할 수 있습니다. 다음으로, 샘플을 교육 부분(이 예제에서는 75%)과 테스트 부분(이 예제에서는 25%)으로 분할하여 분류 및 회귀 모델링에 사용합니다.

학습 중에 교차 유효성 검사 접기를 선택하는 데 사용할 수 있는 행("rand" 열에서)에 도달하기 위해 난수(0과 1 사이)를 추가합니다.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

셀 실행 시간: 2초

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>학습 변수 및 기능 지정, 인덱싱되었거나 원 핫 인코딩된 학습 및 테스팅 입력 레이블이 지정된 지점 RDD 또는 데이터 프레임 만들기
이 섹션은 범주 텍스트 데이터를 레이블이 지정된 지점 데이터 형식으로 인덱싱하고 인코딩하는 방법을 보여주는 코드를 포함하여 MLlib 로지스틱 회귀 및 다른 분류 모델을 학습하고 테스트하는 데 사용될 수 있습니다. 레이블이 지정된 지점 개체는 대부분 MLlib의 기계 학습 알고리즘에서 입력 데이터로 필요한 방식으로 형식이 지정된 RDD입니다. [레이블이 지정된 지점](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) 은 레이블/응답과 연결된 로컬 벡터, 밀도 또는 스파스입니다.

이 코드에서는 모델을 학습하는 데 사용할 대상(종속) 변수 및 기능을 지정합니다. 그런 후 인덱싱되었거나 원 핫 인코딩된 학습 및 테스팅 입력 레이블이 지정된 지점 RDD 또는 데이터 프레임을 만듭니다.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

셀 실행 시간: 4초

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>기계 학습 모델에 대한 입력으로 사용할 기능과 대상을 자동으로 범주화 및 벡터화
Spark ML을 사용하여 트리 기반 모델링 기능에 사용할 대상 및 기능을 범주화합니다. 이 코드는 다음 두 작업을 완료합니다.

* 0.5의 임계값을 사용하는 0과 1 사이의 각 데이터 요소에 0 또는 1 값을 할당하여 분류의 이진 대상을 만듭니다.
* 기능을 자동으로 범주화합니다. 기능에 대한 고유 숫자 값이 32보다 작은 경우 해당 기능은 범주화됩니다.

이러한 두 작업에 대한 코드는 다음과 같습니다.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>이진 분류 모델: 팁을 지불해야 하는지 예측
이 섹션에서는 팁이 지불되었는지 아닌지를 예측하기 위해 이진 분류 모델을 세 가지 형식으로 만듭니다.

* Spark ML `LogisticRegression()` 함수를 사용하는 **로지스틱 회귀 모델**
* Spark ML `RandomForestClassifier()` 함수를 사용하는 **임의 포리스트 분류 모델**
* MLlib `GradientBoostedTrees()` 함수를 사용하는 **그라데이션 향상 트리 분류 모델**

### <a name="create-a-logistic-regression-model"></a>로지스틱 회귀 모델 만들기
다음으로, Spark ML `LogisticRegression()` 함수를 사용하여 로지스틱 회귀 모델을 만듭니다. 다음과 같은 단계를 통해 모델 빌딩 코드를 만듭니다.

1. **모델 학습** 데이터
2. **모델 평가** 
3. **모델 저장** 
4. **모델 점수 매기기** 
5. **결과 그리기** 

이러한 절차에 대한 코드는 다음과 같습니다.

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

결과를 로드하고 점수를 매기고 저장합니다.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**출력:**

ROC on test data = 0.9827381497557599

로컬 Pandas 데이터 프레임에서 Python을 사용하여 ROC 곡선을 그립니다.

    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
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

![팁 여부 ROC 곡선](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>임의 포리스트 분류 모델 만들기
다음으로 Spark ML `RandomForestClassifier()` 함수를 사용하는 임의 포리스트 분류 모델을 만들고 테스트 데이터에 대해 모델을 평가합니다.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**출력:**

ROC on test data = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>GBT 분류 모델 만들기
다음으로 MLlib `GradientBoostedTrees()` 함수를 사용하여 GBT 분류 모델을 만들고 테스트 데이터에 대해 모델을 평가합니다.

    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**출력:**

Area under ROC curve: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>회귀 모델: 팁 액수 예측
이 섹션에서는 팁 금액을 예측하기 위해 두 가지 형식의 회귀 모델을 만듭니다.

* Spark ML `LinearRegression()` 함수를 사용하는 **정칙 선형 회귀 모델** 모델을 저장하고 테스트 데이터에 대해 모델을 평가합니다.
* Spark ML `GBTRegressor()` 함수를 사용하는 **그라데이션 향상 트리 회귀 모델**

### <a name="create-a-regularized-linear-regression-model"></a>정칙 선형 회귀 모델 만들기
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

셀 실행 시간: 13초

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**출력:**

R-sqr on test data = 0.5960320470835743

다음으로, 테스트 결과를 데이터 프레임으로 쿼리하고 AutoVizWidget 및 matplotlib를 사용하여 시각화합니다.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

이 코드는 쿼리 출력에서 로컬 데이터 프레임을 만들고 데이터를 그립니다. `%%local` 매직은 matplotlib로 그리는 데 사용할 수 있는 로컬 데이터 프레임 `sqlResults`를 만듭니다.

> [!NOTE]
> 이 Spark 매직은 이 문서에서 여러 번 사용됩니다. 데이터 양이 많은 경우 로컬 메모리에 맞게 데이터 프레임을 만들도록 샘플링해야 합니다.
> 
> 

Python matplotlib를 사용하여 도표를 만듭니다.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**출력:**

![팁 액수: 실제 및 예측](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>GBT 회귀 모델 만들기
Spark ML `GBTRegressor()` 함수를 사용하여 GBT 회귀 모델을 만들고 테스트 데이터에 대해 모델을 평가합니다.

[GBT](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (그라데이션 향상 트리)는 결정 트리의 결합체입니다. GBT는 기능 손실을 최소화하기 위해 결정 트리를 반복적으로 학습합니다. 회귀 및 분류에 대해 GBT를 사용할 수 있습니다. GBT는 범주 기능을 처리하고 기능 규모 결정을 요구하지 않으며 비선형 기능 상호 작용을 캡처할 수 있습니다. 또한 다중 클래스 분류 설정에도 사용할 수 있습니다.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**출력:**

Test R-sqr is: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>최적화를 위한 고급 모델링 유틸리티
이 섹션에서는 개발자가 모델 최적화를 위해 자주 사용하는 기계 학습 유틸리티를 사용합니다. 특히, 매개 변수 스윕 작업 및 교차 유효성 검사를 사용하여 세 가지 다른 방식으로 기계 학습 모델을 최적화할 수 있습니다.

* 데이터를 학습 및 유효성 검사 집합으로 분할하고, 학습 집합에서 하이퍼 매개 변수 스윕 작업을 사용하여 모델을 최적화하고, 유효성 검사 집합에서 평가를 수행합니다(선형 회귀).
* Spark ML의 CrossValidator 함수(이진 분류)를 사용하여 교차 유효성 검사 및 하이퍼 매개 변수 스윕 작업을 통해 모델을 최적화합니다.
* 사용자 지정 교차 유효성 검사 및 매개 변수 스윕 작업 코드를 사용하여 모든 기계 학습 함수 및 매개 변수 집합을 통해 모델을 최적화합니다(선형 회귀).

**교차 유효성 검사** 는 알려진 데이터 집합에서 학습된 모델이 학습되지 않은 데이터 집합의 기능 예측을 얼마나 잘 일반화하는지 평가하는 기술입니다. 이 기술의 일반 개념은 알려진 데이터의 데이터 집합에서 모델을 학습한 다음 독립된 데이터 집합에 대해 예측 정확도를 테스트하는 것입니다. 일반적인 구현은 데이터 집합을 *k*접기로 나눈 다음 접기 중 하나를 제외한 모든 접기에서 라운드 로빈 방식으로 모델을 학습하는 것입니다.

**하이퍼 매개 변수 최적화** 는 학습 알고리즘에 대한 하이퍼 매개 변수 집합을 선택하는 문제이며, 일반적으로 독립된 데이터 집합에서의 알고리즘 성능 측정값을 최적화하는 것을 목표로 합니다. 하이퍼 매개 변수는 모델 학습 프로시저 외부에서 지정해야 하는 값입니다. 하이퍼 매개 변수 값에 대한 가정은 모델의 유연성 및 정확도에 영향을 줄 수 있습니다. 의사 결정 트리에는 원하는 깊이와 트리의 리프 수와 같은 하이퍼 매개 변수가 있습니다. SVM(지원 벡터 컴퓨터)은 오분류 페널티 조건을 설정해야 합니다.

하이퍼 매개 변수 최적화를 수행하는 일반적인 방법은 **매개 변수 스윕**이라고도 하는 그리드 검색을 사용하는 것입니다. 그리드 검색에서는 학습 알고리즘에 대한 하이퍼 매개 변수 공간의 지정된 하위 집합 값으로 철저하게 검색합니다. 교차 유효성 검사는 그리드 검색 알고리즘에 의해 생성되는 최적의 결과를 분류하는 성능 메트릭을 제공할 수 있습니다. 교차 유효성 검사 하이퍼 매개 변수 스윕 작업을 사용하는 경우 모델 과잉 맞춤과 같은 문제를 학습 데이터로 제한하는 데 도움이 될 수 있습니다. 이러한 방식으로 모델은 학습 데이터가 추출된 일반적인 데이터 집합에 적용할 수 있는 용량을 유지합니다.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>하이퍼 매개 변수 스윕 작업으로 선형 회귀 모델 최적화
다음으로 데이터를 학습 및 유효성 검사 집합으로 분할하고, 학습 집합에서 하이퍼 매개 변수 스윕 작업을 사용하여 모델을 최적화하고, 유효성 검사 집합에서 평가를 수행합니다(선형 회귀).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**출력:**

Test R-sqr is: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>교차 유효성 검사 및 하이퍼 매개 변수 스윕 작업을 사용하여 이진 분류 모델 최적화
이 섹션에서는 교차 유효성 검사 및 하이퍼 매개 변수 스윕 작업을 사용하여 이진 분류 모델을 최적화하는 방법을 설명합니다. 여기서는 Spark ML `CrossValidator` 함수를 사용합니다.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

셀 실행 시간: 33초

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>사용자 지정 교차 유효성 검사 및 매개 변수 스윕 작업 코드를 사용하여 선형 회귀 모델 최적화
다음으로 사용자 지정 코드를 사용하여 모델을 최적화하고 최고 정확도 기준을 사용하여 최적의 모델 매개 변수를 식별합니다. 그런 다음 최종 모델을 만들고 테스트 데이터로 모델을 평가하고 Blob Storage에 모델을 저장합니다. 마지막으로 모델을 로드하고 테스트 데이터의 점수를 매기며 해당 정확도를 평가합니다.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**출력:**

셀 실행 시간: 61초

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Scala를 사용하여 Spark에서 빌드한 기계 학습 모델을 자동으로 사용
Azure에서 데이터 과학 프로세스를 구성하는 작업을 안내하는 항목에 대한 개요는 [팀 데이터 과학 프로세스](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)를 참조하세요.

[팀 데이터 과학 프로세스 연습](walkthroughs.md) 에서는 특정 시나리오에 대한 팀 데이터 과학 프로세스의 단계를 보여 주는 다른 종단 간 연습에 대해 설명합니다. 또한 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 설명합니다.

[Spark에서 빌드한 기계 학습 모델 점수 매기기](spark-model-consumption.md) 에서는 Scala 코드를 사용하여 Spark에서 빌드한 기계 학습 모델과 함께 Azure Blob Storage에 저장된 새 데이터 집합을 자동으로 로드하고 점수를 매기는 방법을 설명합니다. 사용자는 제공된 지침을 따르고 자동 사용을 위해 Python 코드를 이 문서의 Scala 코드로 바꿀 수 있습니다.

