---
title: Azure Databricks에서 Azure Cosmos DB Cassandra API 액세스
description: 이 문서에서는 Azure Databricks에서 Azure Cosmos DB Cassandra API를 사용하는 방법을 설명합니다.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 37a06b19285c1196b5d87830ea176d4bd0d4eade
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894014"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Azure Databricks에서 Azure Cosmos DB Cassandra API 데이터 액세스

이 문서에서는 [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)의 Spark에서 Azure Cosmos DB Cassandra API를 사용하는 방법을 자세히 설명합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure Cosmos DB Cassandra API 계정 프로비전](create-cassandra-dotnet.md#create-a-database-account)

* [Azure Cosmos DB Cassandra API 연결 관련 기본 사항 검토](cassandra-spark-generic.md)

* [Azure Databricks 클러스터 프로비전](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Cassandra API 사용을 위한 코드 샘플 검토](cassandra-spark-generic.md#next-steps)

* [원하는 경우 cqlsh를 사용하여 유효성 검사](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra 커넥터용 Cassandra API 인스턴스 구성:**

  Cassandra API용 커넥터에서는 Spark 컨텍스트의 일부분으로 Cassandra 연결 세부 정보를 초기화해야 합니다. Databricks Notebook을 시작하면 Spark 컨텍스트는 이미 초기화되어 있으므로 Notebook을 중지하고 컨텍스트를 다시 초기화해서는 안 됩니다. 이러한 경우에 사용할 수 있는 방법 중 하나는 클러스터 Spark 구성에서 클러스터 수준에 Cassandra API 인스턴스 구성을 추가하는 것입니다. 이 작업은 클러스터당 한 번씩만 수행하면 됩니다. 다음 코드를 공백으로 구분된 키-값 쌍으로 Spark 구성에 추가합니다.
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

* **Cassandra Spark 커넥터:** - Azure Cosmos DB Cassandra API를 Spark와 통합하려면 Cassandra 커넥터를 Azure Databricks 클러스터에 연결해야 합니다. 클러스터를 연결하려면 다음 작업을 수행합니다.

  * Databricks 런타임 버전과 Spark 버전을 검토합니다. 그런 다음, Cassandra Spark 커넥터와 호환되는 [Maven 좌표](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector)를 찾아서 클러스터에 연결합니다. 클러스터에 커넥터 라이브러리를 연결하려는 경우 ["Maven 패키지 또는 Spark 패키지 업로드"](https://docs.databricks.com/user-guide/libraries.html) 문서를 참조하세요. 예를 들어 "Databricks 런타임 버전 4.3", "Spark 2.3.1" 및 "Scala 2.11"의 Maven 좌표는 `spark-cassandra-connector_2.11-2.3.1`입니다.

* **Azure Cosmos DB Cassandra API 관련 라이브러리:** - Cassandra Spark 커넥터에서 Azure Cosmos DB Cassandra API로의 재시도 정책을 구성하려면 사용자 지정 연결 팩터리가 필요합니다. `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0`[Maven 좌표](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar)를 추가하여 클러스터에 라이브러리를 연결합니다.

## <a name="sample-notebooks"></a>샘플 Notebook

Azure Databricks [샘플 Notebook](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) 목록은 GitHub 리포지토리에서 다운로드할 수 있습니다. 이러한 샘플에는 Spark에서 Azure Cosmos DB Cassandra API에 연결하여 데이터에 대해 다양한 CRUD 작업을 수행하는 방법이 포함되어 있습니다. Databricks 클러스터 작업 영역으로 [모든 Notebook을 가져와서](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) 실행할 수도 있습니다. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Spark Scala 프로그램에서 Azure Cosmos DB Cassandra API 액세스

Azure Databricks에서 자동화된 프로세스로 실행할 Spark 프로그램은 [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html))를 사용하여 클러스터에 제출되며, Azure Databricks 작업을 통해 실행되도록 예약됩니다.

아래에는 Azure Cosmos DB Cassandra API와 상호 작용하도록 할 Spark Scala 프로그램 빌드를 시작하는 데 필요한 정보를 제공하는 문서의 링크가 나와 있습니다.
* [Spark Scala 프로그램에서 Azure Cosmos DB Cassandra API에 연결하는 방법](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Azure Databricks에서 Spark Scala 프로그램을 자동화된 작업으로 실행하는 방법](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Cassandra API 사용을 위한 코드 샘플의 전체 목록](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>다음 단계

Java 애플리케이션을 사용하여 [Cassandra API 계정, 데이터베이스 및 테이블 만들기](create-cassandra-api-account-java.md)를 시작합니다.
