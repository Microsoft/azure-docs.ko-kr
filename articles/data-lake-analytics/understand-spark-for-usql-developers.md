---
title: Azure Data Lake Analytics U SQL 개발자를 위한 Apache Spark를 이해 합니다.
description: 이 문서에서는 T-SQL 개발자 간의 차이점에 대한 Apache Spark 개념을 설명 합니다.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648429"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>U SQL 개발자를 위한 Apache Spark 이해

Microsoft는 [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) 및 [Azure HDInsight](../hdinsight/hdinsight-overview.md) 와 같은 여러 분석 서비스와 Azure Data Lake Analytics를 지원 합니다. 개발자는 분석 파이프라인을 빌드할 때 오픈 소스 솔루션에 대한 명확한 기본 설정을가지고 있습니다. T-SQL 개발자가 Apache Spark를 이해 하 고 U-SQL 스크립트를 Apache Spark으로 변환 하는 방법에 대한 자세한 내용은이 지침을 만들었습니다.

여기에는 몇 가지 단계를 수행 하 고 여러 가지 방법을 사용할 수 있습니다.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Apache Spark에서 U-SQL로 변환 하는 단계

1. 작업 오케스트레이션 파이프라인을 변환 합니다.

   [Azure Data Factory](../data-factory/introduction.md) 를 사용 하 여 Azure Data Lake Analytics 스크립트를 오케스트레이션 하는 경우 새 Spark 프로그램을 오케스트레이션 하도록 조정 해야 합니다.
2. U-SQL 및 Spark 데이터 관리 방법의 차이점 이해

   [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 에서 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)로 데이터를 이동 하려는 경우 파일 데이터와 카탈로그에서 유지 관리 되는 데이터를 모두 복사 해야 합니다. Azure Data Lake Analytics은 Azure Data Lake Storage Gen1만 지원 합니다. [Spark 데이터 형식 이해](understand-spark-data-formats.md) 를 참조 하세요.
3. U-SQL 스크립트를 Spark로 변환

   U-SQL 스크립트를 변환 하기 전에 분석 서비스를 선택 해야 합니다. 사용할 수 있는 계산 서비스의 일부는 다음과 같습니다.
      - [Azure Data Factory 데이터 흐름](../data-factory/concepts-data-flow-overview.md) 데이터 흐름 매핑은 데이터 엔지니어가 코드를 작성 하지 않고도 그래픽 데이터 변환 논리를 개발할 수 있도록 시각적으로 디자인 된 데이터 변환입니다. 복잡 한 사용자 코드를 실행 하는 데 적합 하지는 않지만 기존의 SQL 유사 데이터 흐름 변환을 쉽게 나타낼 수 있습니다.
      - [Azure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) HDInsight의 Apache Hive는 ETL (추출, 변환 및 로드) 작업에 적합 합니다. 즉, U-SQL 스크립트를 Apache Hive으로 변환 합니다.
      - [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) 또는 [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) 같은 Apache Spark 엔진은 U-SQL 스크립트를 Spark로 변환 하려고 합니다. 자세한 내용은 [Spark 데이터 형식 이해](understand-spark-data-formats.md) 를 참조 하세요.

> [!CAUTION]
> [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) 및 [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) 는 모두 클러스터 서비스 이며 Azure Data Lake Analytics와 같은 서버를 사용 하지 않는 작업입니다. 적절 한 비용/성능 비율을 얻으려면 클러스터를 프로 비전 하는 방법과 비용을 최소화 하기 위해 수명을 관리 하는 방법을 고려해 야 합니다. 이러한 서비스는 .NET으로 작성 된 사용자 코드와 다른 성능 특성을 가지 므로 래퍼를 작성 하거나 지원 되는 언어로 코드를 다시 작성 해야 합니다. 자세한 내용은 [Spark 데이터 형식 이해](understand-spark-data-formats.md), [U SQL 개발자를 위한 Apache Spark 코드 개념 이해](understand-spark-code-concepts.md)및 [Apache Spark에](https://dotnet.microsoft.com/apps/data/spark) 대한 .net을 참조 하십시오.

## <a name="next-steps"></a>다음 단계

- [U SQL 개발자를 위한 Spark 데이터 형식 이해](understand-spark-data-formats.md)
- [U SQL 개발자를 위한 Spark 코드 개념 이해](understand-spark-code-concepts.md)
- [Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 빅 데이터 분석 솔루션 업그레이드](../storage/blobs/data-lake-storage-upgrade.md)
- [Apache Spark 용 .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Azure Data Factory Hadoop Hive 작업을 사용 하 여 데이터 변환](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure Data Factory에서 Spark 작업을 사용 하 여 데이터 변환](../data-factory/transform-data-using-spark.md)
- [Azure HDInsight의 Apache Spark 정의](../hdinsight/spark/apache-spark-overview.md)
