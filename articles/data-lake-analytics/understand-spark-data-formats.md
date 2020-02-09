---
title: Azure Data Lake Analytics U SQL 개발자를 위한 Apache Spark 데이터 형식을 이해 합니다.
description: 이 문서에서는 U_SQL 개발자가 T-SQL과 Spark 데이터 형식 간의 차이점을 이해 하는 데 도움이 되는 Apache Spark 개념을 설명 합니다.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648442"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>U-SQL 및 Spark 데이터 형식 간의 차이점 이해

[Azure Databricks](../azure-databricks/what-is-azure-databricks.md) 또는 [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)를 사용 하려면 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 에서 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)로 데이터를 마이그레이션하는 것이 좋습니다.

파일을 이동 하는 것 외에도 데이터를 U-SQL 테이블에 저장 하 여 Spark에서 액세스할 수 있도록 합니다.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Azure Data Lake Storage Gen1 파일에 저장 된 데이터 이동

파일에 저장 된 데이터는 여러 가지 방법으로 이동할 수 있습니다.

- [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 계정에서 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 계정으로 데이터를 복사 하는 [Azure Data Factory](../data-factory/introduction.md) 파이프라인을 작성 합니다.
- [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 계정에서 데이터를 읽고 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 계정에 쓰는 Spark 작업을 작성 합니다. 사용 사례에 따라 원래 파일 형식을 유지할 필요가 없는 경우 Parquet와 같은 다른 형식으로 작성할 수 있습니다.

[Azure Data Lake Storage Gen1에서 빅 데이터 분석 솔루션 업그레이드 문서를](../storage/blobs/data-lake-storage-upgrade.md) 검토 하는 것이 좋습니다 Azure Data Lake Storage Gen2

## <a name="move-data-stored-in-u-sql-tables"></a>U-SQL 테이블에 저장 된 데이터 이동

U-SQL 테이블은 Spark에서 인식 되지 않습니다. SQL 테이블에 저장 된 데이터가 있는 경우 테이블 데이터를 추출 하 고 Spark에서 인식 하는 형식으로 저장 하는 U-SQL 작업을 실행 합니다. 가장 적절 한 형식은 Hive metastore의 폴더 레이아웃에 따라 Parquet 파일 집합을 만드는 것입니다.

출력은 기본 제공 Parquet outputter를 사용 하 여 U-SQL에서 수행할 수 있으며 파일 집합을 사용 하 여 동적 출력 분할을 사용 하 여 파티션 폴더를 만듭니다. [그 어느 때 보다 많은 파일을 처리 하 고 Parquet를 사용](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) 하 여 이러한 Spark 사용 가능 데이터를 만드는 방법의 예를 제공 합니다.

이 변환 후에는 [Azure Data Lake Storage Gen1 파일에 저장 된 데이터 이동](#move-data-stored-in-azure-data-lake-storage-gen1-files)장에 설명 된 대로 데이터를 복사 합니다.

## <a name="caveats"></a>주의 사항

- 데이터 의미 체계 파일을 복사할 때 복사본은 바이트 수준에서 수행 됩니다. 따라서 동일한 데이터가 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 계정에 표시 되어야 합니다. 그러나 Spark는 일부 문자를 다르게 해석할 수 있습니다. 예를 들어 CSV 파일의 행 구분 기호에 대해 다른 기본값을 사용할 수 있습니다.
    또한 테이블에서 형식화 된 데이터를 복사 하는 경우 Parquet 및 Spark는 형식화 된 값 (예: float) 중 일부에 대해 다른 전체 자릿수와 소수 자릿수를 가질 수 있으며 null 값을 다르게 처리할 수 있습니다. 예를 들어, U-SQL에 C# 는 null 값에 대한 의미 체계가 있지만 Spark에는 null 값에 대한 3 값 논리가 있습니다.

- 데이터 구성 (분할)-SQL 테이블은 두 가지 수준 분할을 제공 합니다. 외부 수준 (`PARTITIONED BY`)은 값을 기준으로 하며 대부분은 폴더 계층 구조를 사용 하 여 Hive/Spark 파티션 체계로 매핑됩니다. Null 값이 올바른 폴더에 매핑되는지 확인 해야 합니다. U-SQL의 내부 수준 (`DISTRIBUTED BY`)은 라운드 로빈, 범위, 해시 및 직접 해시의 4 가지 배포 스키마를 제공 합니다.
    Hive/Spark 테이블은 U-SQL과 다른 해시 함수를 사용 하 여 값 분할 또는 해시 분할만 지원 합니다. U-SQL 테이블 데이터를 출력 하는 경우 Spark의 값 분할에만 매핑할 수 있으며 최종 Spark 쿼리에 따라 데이터 레이아웃을 추가로 조정 해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [U SQL 개발자를 위한 Spark 코드 개념 이해](understand-spark-code-concepts.md)
- [Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 빅 데이터 분석 솔루션 업그레이드](../storage/blobs/data-lake-storage-upgrade.md)
- [Apache Spark 용 .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Azure Data Factory에서 Spark 작업을 사용 하 여 데이터 변환](../data-factory/transform-data-using-spark.md)
- [Azure Data Factory Hadoop Hive 작업을 사용 하 여 데이터 변환](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight의 Apache Spark 정의](../hdinsight/spark/apache-spark-overview.md)
