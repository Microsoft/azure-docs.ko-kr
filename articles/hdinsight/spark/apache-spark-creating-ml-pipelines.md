---
title: Apache Spark Machine Learning 학습 파이프라인 만들기 - Azure HDInsight
description: Apache Spark 기계 학습 라이브러리를 사용하여 데이터 파이프라인을 만듭니다.
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: c539460177a0a85938b886d161803e1fdf0e9e68
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124461"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Apache Spark 기계 학습 파이프라인 만들기

Apache Spark의 확장 가능한 MLlib(기계 학습 라이브러리)는 모델링 기능을 분산 환경에 제공합니다. [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) Spark 패키지는 데이터 프레임에 작성된 고급 수준의 API 집합입니다. 이러한 API를 사용하면 실용적인 기계 학습 파이프라인을 만들고 튜닝할 수 있습니다.  *Spark 기계 학습*은 이전의 RDD 기반 파이프라인 API가 아니라 이 MLlib 데이터 프레임 기반 API를 참조합니다.

ML(기계 학습) 파이프라인은 여러 기계 학습 알고리즘을 결합한 완벽한 워크플로입니다. 데이터를 처리하고 학습하는 데 필요한 여러 단계가 있을 수 있으며 일련의 알고리즘이 필요합니다. 파이프라인은 기계 학습 프로세스의 단계와 순서를 정의합니다. MLlib에서 파이프라인의 단계는 변환기와 평가기에서 각각 작업을 수행하는 PipelineStages의 특정 시퀀스로 표시됩니다.

변환기는 `transform()` 메서드를 사용하여 한 데이터 프레임을 다른 데이터 프레임으로 변환하는 알고리즘입니다. 예를 들어 기능 변환기는 데이터 프레임의 한 열을 읽고, 다른 열에 매핑한 다음, 매핑된 열이 추가된 새 데이터 프레임을 출력할 수 있습니다.

평가기는 학습 알고리즘의 추상화이며, 변환기를 생성하기 위해 데이터 세트에 맞추거나 학습해야 합니다. 평가기는 `fit()`이라는 메서드를 구현합니다. 이 메서드는 데이터 프레임을 수락하고 변환기인 데이터 프레임을 생성합니다.

변환기 또는 평가기의 각 상태 비저장 인스턴스에는 매개 변수를 지정할 때 사용되는 자체의 고유 식별자가 있습니다. 둘 다 이러한 매개 변수를 지정하는 균일한 API를 사용합니다.

## <a name="pipeline-example"></a>파이프라인 예제

ML 파이프라인을 실제로 사용하기 위해 이 예제에서는 HDInsight 클러스터에 대한 기본 스토리지(Azure Storage 또는 Data Lake Storage)에 미리 로드된 `HVAC.csv` 데이터 파일 샘플을 사용합니다. 파일의 내용을 보려면 `/HdiSamples/HdiSamples/SensorSampleData/hvac` 디렉터리로 이동합니다. `HVAC.csv`에는 여러 건물의 HVAC(*난방, 환기 및 공기 조절*) 시스템의 목표 온도와 실제 온도가 모두 포함된 일단의 시간이 포함되어 있습니다. 목표는 모델에서 데이터를 학습하고 지정된 건물에 대한 예측 온도를 생성하는 것입니다.

코드는 다음과 같습니다.

1. `BuildingID`, `SystemInfo`(시스템의 식별자 및 보존 기간) 및 `label`(너무 더운 건물인 경우 1.0, 그렇지 않은 경우 0.0)를 저장하는 `LabeledDocument`를 정의합니다.
2. 데이터의 줄(행)을 사용하여 목표 온도와 실제 온도를 비교하여 건물이 "더운지" 여부를 결정하는 `parseDocument` 사용자 지정 구문 분석기 함수를 만듭니다.
3. 원본 데이터를 추출할 때 구문 분석기를 적용합니다.
4. 학습 데이터를 만듭니다.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

LabeledDocument = Row("BuildingID", "SystemInfo", "label")

# Define a function that parses the raw CSV file and returns an object of type LabeledDocument
def parseDocument(line):
    values = [str(x) for x in line.split(',')]
    if (values[3] > values[2]):
        hot = 1.0
    else:
        hot = 0.0        

    textValue = str(values[4]) + " " + str(values[5])

    return LabeledDocument((values[6]), textValue, hot)

# Load the raw HVAC.csv file, parse it using the function
data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

이 예제 파이프라인에는 `Tokenizer`, `HashingTF`(둘 다 변환기) 및 `Logistic Regression`(평가기)의 세 단계가 있습니다.  `pipeline.fit(training)`가 호출되면 `training` 데이터 프레임에서 추출되어 구문 분석된 데이터가 파이프라인을 통해 흐릅니다.

1. 첫 번째 `Tokenizer` 단계에서는 `SystemInfo` 입력 열(시스템 식별자와 보존 기간 값으로 구성)을 `words` 출력 열로 분할합니다. 새로운 이 `words` 열이 데이터 프레임에 추가됩니다. 
2. 두 번째 `HashingTF` 단계에서는 새 `words` 열을 기능 벡터로 변환합니다. 새로운 이 `features` 열이 데이터 프레임에 추가됩니다. 이러한 처음 두 단계는 변환기입니다. 
3. 세 번째 `LogisticRegression` 단계는 평가기이므로 파이프라인에서 `LogisticRegression.fit()` 메서드를 호출하여 `LogisticRegressionModel`을 생성합니다. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

`Tokenizer` 및 `HashingTF` 변환기에서 추가된 새 `words` 및 `features` 열과 `LogisticRegression` 평가기의 샘플을 보려면 원본 데이터 프레임에서 `PipelineModel.transform()` 메서드를 실행합니다. 프로덕션 코드에서 다음 단계는 테스트 데이터 프레임에 전달하여 학습의 유효성을 검사하는 것입니다.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

이제 `model` 개체를 사용하여 예측을 만들 수 있습니다. 이 기계 학습 애플리케이션의 전체 샘플과 단계별 실행 지침은 [Azure HDInsight에서 Apache Spark 기계 학습 애플리케이션 빌드](apache-spark-ipython-notebook-machine-learning.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

* [Azure에서 Scala 및 Apache Spark를 사용한 데이터 과학](../../machine-learning/team-data-science-process/scala-walkthrough.md)
