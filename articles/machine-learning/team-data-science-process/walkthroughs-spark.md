---
title: PySpark, Scala를 사용하는 HDInsight Spark의 분석 - Team Data Science Process
description: 예측 분석을 수행하기 위해 Azure HDInsight Spark에서 PySpark 및 Scala 사용을 보여 주는 Team Data Science Process의 예제입니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ce57c37791f9fcf353d08e177eb243f4939b52a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805521"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Azure에서 PySpark 및 Scala를 사용하여 HDInsight Spark 데이터 과학 연습

이러한 연습에서는 Azure Spark 클러스터에서 PySpark 및 Scala를 사용하여 예측 분석을 수행합니다. Team Data Science Process에 설명된 단계를 따릅니다. Team Data Science Process의 개요는 [데이터 과학 프로세스](overview.md)를 참조하세요. HDInsight의 Spark 개요는 [HDInsight의 Spark 소개](../../hdinsight/spark/apache-spark-overview.md)를 참조하세요.

Team Data Science Process를 실행하는 추가 데이터 과학 연습은 사용하는 **플랫폼**에 따라 그룹화됩니다. 이러한 예제의 항목 목록은 [Team Data Science Process 실행 연습](walkthroughs.md)을 참조하세요.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Azure Spark에서 PySpark를 사용하여 택시 팁 예측

[Azure HDInsight에서 Spark 사용](spark-overview.md) 연습은 뉴욕 택시의 데이터를 사용하여 팁 지불 여부 및 예상되는 지불 금액의 범위를 예측합니다. [Azure HDInsight Spark 클러스터](https://azure.microsoft.com/services/hdinsight/)를 사용하는 시나리오에서 Team Data Science Process를 사용하여 공개적으로 사용 가능한 NYC Taxi Trip 및 요금 데이터 세트에서 데이터를 저장, 탐색 및 기능 엔지니어링합니다. 이 개요 항목은 연습의 나머지 부분에서 사용되는 HDInsight Spark 클러스터와 Jupyter PySpark 노트북을 사용하여 설정합니다. 이러한 노트북은 데이터 탐색 방법을 보여 준 후 모델을 만들고 사용하는 방법을 보여 줍니다. 고급 데이터 탐색 및 모델링 Notebook은 교차 유효성 검사, 하이퍼 매개 변수 비우기 및 모델 평가를 포함하는 방법을 보여 줍니다.

### <a name="data-exploration-and-modeling-with-spark"></a>Spark로 데이터 탐색 및 모델링 
[Spark MLlib 도구 키트를 사용하여 데이터에 대한 이진 분류 및 회귀 모델 만들기](spark-data-exploration-modeling.md) 항목을 수행하여 데이터 세트를 탐색하고 기계 학습 모델 만들기, 점수 매기기 및 평가를 수행합니다.

### <a name="model-consumption"></a>모델 사용
이 항목에서 만든 분류 및 회귀 모델의 점수를 매기는 방법을 알아보려면 [Spark로 빌드된 기계 학습 모델 점수 매기기 및 평가](spark-model-consumption.md)를 참조하세요.

### <a name="cross-validation-and-hyperparameter-sweeping"></a>교차 유효성 검사 및 하이퍼 매개 변수 비우기
교차 유효성 검사 및 하이퍼 매개 변수 비우기를 사용하여 모델을 학습하는 방법은 [Spark로 고급 데이터 탐색 및 모델링](spark-advanced-data-exploration-modeling.md)을 참조하세요.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Azure Spark에서 Scala를 사용하여 택시 팁 예측

[Azure에서 Spark와 함께 Scala 사용](scala-walkthrough.md) 연습은 뉴욕 택시의 데이터를 사용하여 팁 지불 여부 및 예상되는 지불 금액의 범위를 예측합니다. Azure HDInsight Spark 클러스터에서 Spark MLlib(Machine Learning 라이브러리) 및 SparkML 패키지를 사용하여 감독 Machine Learning 작업에 대해 Scala를 사용하는 방법을 보여 줍니다. [데이터 과학 프로세스](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): 데이터 수집 및 탐색, 시각화, 기능 엔지니어링, 모델링, 모델 사용으로 이루어진 작업을 단계별로 안내합니다. 작성된 모델은 로지스틱 및 선형 회귀, 임의 포리스트 및 그라데이션 향상된 트리를 포함합니다.


## <a name="next-steps"></a>다음 단계

Team Data Science Process를 구성하는 주요 구성의 논의는 [Team Data Science Process 개요](overview.md)를 참조하세요.

데이터 과학 프로젝트를 구성하는 데 사용할 수 있는 Team Data Science Process 수명 주기의 논의는 [Team Data Science Process 수명 주기](lifecycle.md)를 참조하세요. 수명 주기는 일반적으로 프로젝트가 실행될 때 시작부터 끝까지 따라야 하는 단계를 간략하게 설명합니다. 

