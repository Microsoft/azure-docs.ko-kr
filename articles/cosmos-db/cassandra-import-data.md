---
title: Azure Cosmos DB의 Cassandra API 계정으로 데이터 마이그레이션 - 자습서
description: 이 자습서에서는 CQL Copy 명령 및 Spark를 사용하여 Apache Cassandra에서 Azure Cosmos DB의 Cassandra API 계정으로 데이터를 복사하는 방법을 알아봅니다.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: b450d8463c09647b8f87547afe9d714c4b8659c4
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "82858567"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>자습서: Azure Cosmos DB에서 Cassandra API 계정으로 데이터 마이그레이션

개발자는 온-프레미스 또는 클라우드에서 실행되는 기존 Cassandra 워크로드를 가질 수 있으며 Azure로 마이그레이션할 수 있습니다. 이러한 워크로드를 Azure Cosmos DB의 Cassandra API 계정으로 마이그레이션할 수 있습니다. 이 자습서는 Apache Cassandra 데이터를 Azure Cosmos DB의 Cassandra API 계정으로 마이그레이션할 수 있는 다양한 옵션에 대한 지침을 제공합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 마이그레이션 계획
> * 마이그레이션을 위한 필수 조건
> * cqlsh COPY 명령을 사용하여 데이터 마이그레이션
> * Spark를 사용하여 데이터 마이그레이션

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites-for-migration"></a>마이그레이션을 위한 필수 조건

* **처리량 요구 사항 예측:** Azure Cosmos DB에서 데이터를 Cassandra API 계정으로 마이그레이션하려면 먼저 워크로드에 대한 처리량 요구 사항을 예측해야 합니다. 일반적으로 CRUD 작업에 필요한 평균 처리량부터 시작한 다음, ETL(추출, 변환 및 로드) 또는 급증하는 작업에 필요한 추가 처리량을 포함하는 것이 좋습니다. 마이그레이션을 계획하려면 다음과 같은 세부 정보가 필요합니다. 

  * **기존 또는 예상 데이터 크기:** 최소 데이터베이스 크기 및 처리량 요구 사항을 정의합니다. 새 애플리케이션의 데이터 크기를 예측할 경우 데이터가 행 전체에 균일하게 분포되어 있다고 가정하고 데이터 크기를 곱하여 값을 추정하면 됩니다. 

  * **필요한 처리량:** 대략적인 읽기(쿼리/가져오기) 및 쓰기(업데이트/삭제/삽입) 처리량 속도입니다. 이 값은 안정적인 상태 데이터 크기와 함께 필요한 요청 단위를 컴퓨팅하는 데 필요합니다.  

  * **스키마:** cqlsh를 통해 기존 Cassandra 클러스터에 연결하고 Cassandra에서 스키마를 내보냅니다. 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    기존 워크로드의 요구 사항을 확인한 후에는 수집한 처리량 요구 사항에 따라 Azure Cosmos 계정, 데이터베이스 및 컨테이너를 만들어야 합니다.  

  * **작업에 대한 RU 요금 결정:** Cassandra API에서 지원되는 SDK 중 하나를 사용하여 RU를 결정할 수 있습니다. 다음 예제는 RU 비용 가져오기의 .NET 버전을 보여 줍니다.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **필요한 처리량 할당:** 요구 사항이 증가함에 따라 Azure Cosmos DB에서 스토리지 및 처리량의 크기를 자동으로 조정할 수 있습니다. [Azure Cosmos DB 요청 단위 계산기](https://www.documentdb.com/capacityplanner)를 사용하여 처리량 수요를 예측할 수 있습니다. 

* **Cassandra API 계정에 테이블 만들기:** 데이터 마이그레이션을 시작하기 전에 Azure Portal 또는 cqlsh에서 모든 테이블을 미리 만듭니다. 데이터베이스 수준 처리량이 있는 Azure Cosmos 계정으로 마이그레이션하는 경우에는 Azure Cosmos 컨테이너를 만들 때 파티션 키를 제공해야 합니다.

* **처리량 증가:** 데이터 마이그레이션 기간은 Azure Cosmos DB의 테이블에 대해 프로비전하는 처리량에 따라 달라집니다. 마이그레이션 기간에 대한 처리량을 늘립니다. 처리량이 높을수록 속도 제한을 피하고 마이그레이션 시간을 단축할 수 있습니다. 마이그레이션을 완료한 후에는 비용을 절약하기 위해 처리량을 줄이세요. Azure Cosmos 계정은 원본 데이터베이스와 같은 지역에 두는 것이 좋습니다. 

* **TLS 사용:** Azure Cosmos DB에는 엄격한 보안 요구 사항과 표준이 있습니다. 계정과 상호 작용하는 경우 TLS를 사용해야 합니다. SSH와 함께 CQL을 사용하는 경우 TLS 정보를 제공할 수 있는 옵션이 있습니다.

## <a name="options-to-migrate-data"></a>데이터 마이그레이션 옵션

다음 옵션을 사용하여 기존 Cassandra 워크로드에서 Azure Cosmos DB로 데이터를 옮길 수 있습니다.

* [cqlsh COPY 명령 사용](#migrate-data-using-cqlsh-copy-command)  
* [Spark 사용](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>cqlsh COPY 명령을 사용하여 데이터 마이그레이션

[CQL COPY 명령](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh)은 로컬 데이터를 Azure Cosmos DB의 Cassandra API 계정에 복사하는 데 사용됩니다. 다음 단계에 따라 데이터를 복사합니다.

1. Cassandra API 계정의 연결 문자열 정보를 확보합니다.

   * [Azure Portal](https://portal.azure.com)에 로그인하고, Azure Cosmos 계정으로 이동합니다.

   * cqlsh를 통해 Cassandra API 계정에 연결하는 데 필요한 모든 정보가 들어있는 **연결 문자열** 창을 엽니다.

2. 포털의 연결 정보를 사용하여 cqhsh에 로그인합니다.

3. CQL COPY 명령을 사용하여 로컬 데이터를 Cassandra API 계정에 복사합니다.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Spark를 사용하여 데이터 마이그레이션 

다음 단계를 따라 Spark를 사용하여 데이터를 Cassandra API 계정으로 마이그레이션합니다.

- [Azure Databricks 클러스터](cassandra-spark-databricks.md) 또는 [HDInsight 클러스터](cassandra-spark-hdinsight.md) 프로비전 

- [테이블 복사 작업](cassandra-spark-table-copy-ops.md)을 사용하여 데이터를 대상 Cassandra API 엔드포인트로 이동합니다. 

Azure 가상 머신이나 다른 클라우드의 기존 클러스터에 데이터가 있는 경우 Spark 작업을 사용하여 데이터를 마이그레이션하는 것이 좋습니다. 이 옵션을 사용하려면 Spark를 한 번 또는 정기적으로 중개자로 설정해야 합니다. 온-프레미스와 Azure 사이에 Azure ExpressRoute 연결을 사용하면 마이그레이션 속도를 높일 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 경우 리소스 그룹, Azure Cosmos 계정 및 모든 관련 리소스를 삭제해도 됩니다. 삭제하려면 가상 머신의 리소스 그룹을 선택하고, **삭제**를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cosmos DB의 Cassandra API 계정으로 데이터를 마이그레이션하는 방법을 알아보았습니다. 이제 다른 Azure Cosmos DB 개념에 대해 자세히 알아보기 위해 다음 문서를 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB의 튜닝 가능한 데이터 일관성 수준](../cosmos-db/consistency-levels.md)


