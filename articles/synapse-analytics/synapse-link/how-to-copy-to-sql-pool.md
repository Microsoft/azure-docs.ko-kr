---
title: Apache Spark를 사용하여 Azure Cosmos DB용 Synapse Link 데이터를 SQL 풀에 복사
description: 데이터를 Spark 데이터 프레임에 로드하고, 데이터를 큐레이션하고, SQL 풀 테이블에 로드합니다.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 88962d63519cfeb78be694c4f702b05ed4e7d3df
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658374"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-sql-pool-using-apache-spark"></a>Apache Spark를 사용하여 Azure Cosmos DB에서 SQL 풀로 데이터 복사

Azure Cosmos DB용 Azure Synapse Link를 사용하면 Azure Cosmos DB의 작동 데이터에 대해 거의 실시간으로 분석을 실행할 수 있습니다. 그러나 데이터 웨어하우스 사용자에게 서비스를 제공하기 위해 일부 데이터를 집계하고 보강해야 하는 경우도 있습니다. Synapse Link 데이터를 큐레이션하고 내보내는 작업은 Notebook에서 단 몇 개의 셀로 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* 다음을 사용하여 [Synapse 작업 영역 프로비전](../quickstart-create-workspace.md):
    * [Spark 풀](../quickstart-create-apache-spark-pool-studio.md)
    * [SQL 풀](../quickstart-create-sql-pool-studio.md)
* [데이터가 포함된 HTAP 컨테이너를 사용하여 Cosmos DB 계정 프로비전](../../cosmos-db/configure-synapse-link.md)
* [Azure Cosmos DB HTAP 컨테이너를 작업 영역에 연결](./how-to-connect-synapse-link-cosmos-db.md)
* [Spark에서 SQL 풀로 데이터를 가져오도록 올바른 설정 수행](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>단계
이 자습서에서는 트랜잭션 저장소에 영향을 주지 않도록 분석 저장소에 연결합니다(요청 단위를 사용하지 않음). 이제부터 다음과 같은 단계를 진행합니다.
1. Cosmos DB HTAP 컨테이너를 Spark 데이터 프레임으로 읽기
2. 새 데이터 프레임에 결과 집계
3. SQL 풀로 데이터 수집

[![Spark에서 SQL 단계로](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>데이터
이 예제에서는 **RetailSales**라는 HTAP 컨테이너를 사용합니다. **ConnectedData**라는 연결된 서비스의 일부이며 다음과 같은 스키마가 있습니다.
* _rid: string(nullable = true)
* _ts: long(nullable = true)
* logQuantity: double(nullable = true)
* productCode: string(nullable = true)
* quantity: long(nullable = true)
* price: long(nullable = true)
* id: string(nullable = true)
* advertising: long(nullable = true)
* storeId: long(nullable = true)
* weekStarting: long(nullable = true)
* _etag: string(nullable = true)

보고 목적으로 *productCode* 및 *weekStarting*별로 판매(*수량*, *수익*(가격 x 수량))를 집계합니다. 마지막으로, 해당 데이터를 **dbo.productsales**라는 SQL 풀 테이블로 내보냅니다.

## <a name="configure-a-spark-notebook"></a>Spark Notebook 구성
Scala as Spark(Scala)를 주 언어로 사용하여 Spark Notebook을 만듭니다. 세션에 Notebook의 기본 설정을 사용합니다.

## <a name="read-the-data-in-spark"></a>Spark에서 데이터 읽기
Spark가 있는 Cosmos DB HTAP 컨테이너를 첫 번째 셀의 데이터 프레임으로 읽습니다.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>새 데이터 프레임에 결과 집계

두 번째 셀에서는 SQL 풀 데이터베이스에 로드하기 전에 새 데이터 프레임에 필요한 변환과 집계를 실행합니다.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-sql-pool"></a>SQL 풀에 결과 로드

세 번째 셀에서는 데이터를 SQL 풀에 로드합니다. 작업이 완료되면 삭제될 임시 외부 테이블, 외부 테이블 소스 및 외부 파일 형식이 자동으로 생성됩니다.

```java
df_olap_aggr.write.sqlanalytics("arnaudpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>SQL로 결과 쿼리

다음 SQL 스크립트와 같은 간단한 SQL 쿼리를 사용하여 결과를 쿼리할 수 있습니다.
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

쿼리는 차트 모드에서 다음과 같은 결과를 제공합니다. [![Spark에서 SQL 단계로](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>다음 단계
* [Apache Spark를 사용하여 Azure Cosmos DB Analytical Store 쿼리](./how-to-query-analytical-store-spark.md)