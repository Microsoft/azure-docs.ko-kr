---
title: Azure Data Factory를 사용하여 데이터 변환 | Microsoft Docs
description: Hadoop, Machine Learning 또는 Azure Data Lake Analytics를 사용하여 Azure Data Factory에서 데이터를 변환 또는 처리하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 2c674f77ef0f779c9764771e2e0ae7a4aea47548
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622110"
---
# <a name="transform-data-in-azure-data-factory"></a>Azure Data Factory의 데이터 변환
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight 스트리밍](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [저장 프로시저](transform-data-using-stored-procedure.md)
> * [데이터 레이크 분석 U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks Notebook](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET 사용자 지정](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>개요
이 문서에서는 원시 데이터를 예측 가능하고 통찰력 있는 정보로 변환하고 처리하는 데 사용할 수 있는 Azure Data Factory의 데이터 변환 작업을 설명합니다. 변환 작업은 Azure HDInsight 클러스터나 Azure Batch와 같은 컴퓨팅 환경에서 실행됩니다. 각 변환 작업에 대한 자세한 정보가 있는 문서에 대한 링크를 제공합니다.

Data Factory는 개별적 또는 다른 작업과 연계하여 [파이프라인](concepts-pipelines-activities.md)에 추가할 수 있는 다음 데이터 변환 작업을 지원합니다.

## <a name="hdinsight-hive-activity"></a>HDInsight Hive 작업
Data Factory 파이프라인에서 HDInsight Hive 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터의 Hive 쿼리를 실행합니다. 이 작업에 대한 자세한 내용은 [Hive 작업](transform-data-using-hadoop-hive.md) 문서를 참조하세요. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig 작업
Data Factory 파이프라인에서 HDInsight Pig 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터의 Pig 쿼리를 실행합니다. 이 작업에 대한 자세한 내용은 [Pig 작업](transform-data-using-hadoop-pig.md) 문서를 참조하세요. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 작업
Data Factory 파이프라인의 HDInsight MapReduce 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 MapReduce 프로그램을 실행합니다. 이 작업에 대한 자세한 내용은 [MapReduce 작업](transform-data-using-hadoop-map-reduce.md) 문서를 참조하세요.

## <a name="hdinsight-streaming-activity"></a>HDInsight 스트리밍 작업
Data Factory 파이프라인의 HDInsight 스트리밍 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행합니다. 이 작업에 대한 자세한 내용은 [HDInsight 스트리밍 작업](transform-data-using-hadoop-streaming.md)을 참조하세요.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark 작업
Data Factory 파이프라인에서 HDInsight Spark 작업은 사용자 고유 HDInsight 클러스터에서 Spark 프로그램을 실행합니다. 자세한 내용은 [Azure Data Factory에서 Spark 프로그램 호출](transform-data-using-spark.md) 을 참조하세요. 

## <a name="machine-learning-activities"></a>Machine Learning 작업
Azure Data Factory를 사용하면 예측 분석을 위해 게시된 Azure Machine Learning 웹 서비스를 사용하는 파이프라인을 쉽게 만들 수 있습니다. Azure Data Factory 파이프라인에서 [배치 실행 작업](transform-data-using-machine-learning.md)을 사용하여 Machine Learning 웹 서비스를 호출하고 배치에 있는 데이터에 대한 예측을 할 수 있습니다.

시간이 지남에 따라 Machine Learning 점수 매기기 실험의 예측 모델은 새 입력 데이터 세트를 사용하여 다시 학습되어야 합니다. 재학습으로 완료한 후에는 재학습한 Machine Learning 모델로 점수 매기기 웹 서비스를 업데이트하려고 합니다. [업데이트 리소스 작업](update-machine-learning-models.md)을 사용하여 새로 학습된 모델로 웹 서비스를 업데이트합니다.  

이러한 Machine Learning 작업에 대한 자세한 내용은 [Machine Learning 작업 사용](transform-data-using-machine-learning.md)을 참조하세요. 

## <a name="stored-procedure-activity"></a>저장 프로시저 작업
Data Factory 파이프라인에서 SQL Server 저장 프로시저 작업을 사용하여 기업의 Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database 또는 Azure VM. 자세한 내용은 [저장 프로시저 작업](transform-data-using-stored-procedure.md) 문서를 참조하세요.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 작업
Data Lake Analytics U-SQL 작업은 Azure Data Lake Analytics 클러스터에 대해 U-SQL 스크립트를 실행합니다. 자세한 내용은 [Data Analytics U-SQL 작업](transform-data-using-data-lake-analytics.md) 문서를 참조하세요. 

## <a name="databricks-notebook-activity"></a>Databricks Notebook 활동

Data Factory 파이프라인의 Azure Databricks Notebook 활동은 Azure Databricks 작업 영역에서 Databricks Notebook을 실행합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다. [Databricks Notebook을 실행하여 데이터 변환](transform-data-databricks-notebook.md)을 참조하세요.

## <a name="databricks-jar-activity"></a>Databricks Jar 활동

Data Factory 파이프라인의 Azure Databricks Jar 활동은 Azure Databricks 클러스터에서 Spark Jar를 실행합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다. [Azure Databricks에서 Jar 활동을 실행하여 데이터 변환](transform-data-databricks-jar.md)을 참조하세요.

## <a name="databricks-python-activity"></a>Databricks Python 활동

Data Factory 파이프라인의 Azure Databricks Python 활동은 Azure Databricks 클러스터에서 Python 파일을 실행합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다. [Azure Databricks에서 Python 활동을 실행하여 데이터 변환](transform-data-databricks-python.md)을 참조하세요.

## <a name="custom-activity"></a>사용자 지정 작업
Data Factory에서 지원되지 않는 방식으로 데이터를 변환해야 하는 경우 고유의 데이터 이동 논리가 포함된 사용자 지정 작업을 만들어서 파이프라인에 해당 작업을 사용할 수 있습니다. Azure Batch 서비스 또는 Azure HDInsight 클러스터를 사용하여 실행되도록 사용자 지정 .NET 작업을 구성할 수 있습니다. 자세한 내용은 [사용자 지정 작업 사용](transform-data-using-dotnet-custom-activity.md) 문서를 참조하세요. 

R이 설치된 HDInsight 클러스터에서 R 스크립트를 실행하는 사용자 지정 작업을 만들 수 있습니다. [Azure Data Factory를 사용하여 R 스크립트 실행](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)을 참조하세요. 

## <a name="compute-environments"></a>컴퓨팅 환경
컴퓨팅 환경을 위한 연결된 서비스를 만들고 변환 작업을 정의할 때 이 연결된 서비스를 사용합니다. 데이터 팩터리에서 지원하는 컴퓨팅 환경은 두 가지 유형이 있습니다. 

- **주문형**:  이 경우 Data Factory에서 완전히 컴퓨팅 환경을 관리합니다. 데이터를 처리하기 위한 작업을 제출하기 전에 데이터 팩터리 서비스에서 자동으로 컴퓨팅 환경을 만들고 작업이 완료되면 제거합니다. 작업 실행, 클러스터 관리, 부트스트래핑 작업에 대한 주문형 컴퓨팅 환경의 세부적인 설정을 구성 및 제어할 수 있습니다. 
- **자체 환경 사용**: 이 경우 사용자 고유의 컴퓨팅 환경(예: HDInsight 클러스터)을 Data Factory에 연결된 서비스로 등록할 수 있습니다. 컴퓨팅 환경은 이를 사용하여 작업을 실행하는 데이터 팩터리 서비스와 사용자에 의해 관리됩니다. 

데이터 팩터리에서 지원하는 컴퓨팅 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](compute-linked-services.md) 문서를 참조하세요. 

## <a name="next-steps"></a>다음 단계
변환 작업 사용 예제에 대해서는 [자습서: Spark를 사용하여 데이터 변환](tutorial-transform-data-spark-powershell.md)을 참조하세요.
