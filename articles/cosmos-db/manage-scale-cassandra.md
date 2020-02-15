---
title: Azure Cosmos DB에서 Cassandra API 탄력적으로 크기 조정
description: Azure Cosmos DB Cassandra API 계정의 크기를 조정 하는 데 사용할 수 있는 옵션과 해당 장점/단점에 대해 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 668e9ddadf151a86be0d8c09fc91b4c70db12f3a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210789"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Azure Cosmos DB Cassandra API 계정 크기 조정 탄력적으로

Cassandra에 대 한 Azure Cosmos DB API의 탄력적 특성을 탐색 하는 다양 한 옵션이 있습니다. Azure Cosmos DB에서 효과적으로 크기를 조정 하는 방법을 이해 하려면 시스템의 성능 요구 사항을 고려 하 여 적절 한 양의 요청 단위 (r u/초)를 프로 비전 하는 방법을 이해 하는 것이 중요 합니다. 요청 단위에 대 한 자세한 내용은 [요청 단위](request-units.md) 문서를 참조 하세요. 

![데이터베이스 작업은 요청 단위를 사용함](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>처리 률 제한 (429 오류)

클라이언트에서 프로 비전 한 용량 보다 많은 리소스 (r u/초)를 사용 하는 경우 Azure Cosmos DB에서 요금 제한 (429) 오류가 반환 됩니다. Azure Cosmos DB의 Cassandra API는 Cassandra 네이티브 프로토콜에서 이러한 예외를 오버로드된 오류로 변환합니다. 

시스템이 대기 시간을 인식 하지 못하는 경우 재시도를 사용 하 여 처리량 속도 제한을 처리 하는 것이 충분할 수 있습니다. Java의 [Cassandra 재시도 정책](https://docs.datastax.com/drivers/java/2.0/com/datastax/driver/core/policies/RetryPolicy.html) 에 대 한 [Azure Cosmos DB 확장](https://github.com/Azure/azure-cosmos-cassandra-extensions) 을 사용 하 여 투명 하 게 rate 제한을 처리 하는 방법에 대해서는 [java 코드 샘플](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) 을 참조 하세요. 또한 [Spark 확장](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) 을 사용 하 여 요율 제한을 처리할 수 있습니다.

## <a name="manage-scaling"></a>크기 조정 관리

대기 시간을 최소화 해야 하는 경우 Cassandra API의 RUs (크기 조정 및 프로 비전 처리량)를 관리 하는 옵션이 있습니다.

* [Azure Portal를 사용 하 여 수동으로](#use-azure-portal)
* [제어 평면 기능을 사용 하 여 프로그래밍 방식으로](#use-control-plane)
* [특정 SDK로 CQL 명령을 사용 하 여 프로그래밍 방식으로](#use-cql-queries)
* [Autopilot를 사용 하 여 동적으로](#use-autopilot)

다음 섹션에서는 각 방법의 장점과 단점을 설명 합니다. 그런 다음, 시스템의 크기 조정 요구, 전반적인 비용 및 솔루션의 효율성 요구를 균형 있게 조정 하기 위한 최상의 전략을 결정할 수 있습니다.

## <a id="use-azure-portal"></a>Azure Portal 사용

Azure Portal를 사용 하 여 Azure Cosmos DB Cassandra API 계정에서 리소스의 크기를 조정할 수 있습니다. 자세히 알아보려면 [컨테이너 및 데이터베이스에 대 한 처리량 프로 비전](set-throughput.md)문서를 참조 하세요. 이 문서에서는 Azure Portal의 [데이터베이스](set-throughput.md#set-throughput-on-a-database) 또는 [컨테이너](set-throughput.md#set-throughput-on-a-container) 수준에서 처리량을 설정 하는 경우의 상대적인 이점에 대해 설명 합니다. 이 문서에 언급 된 "데이터베이스" 및 "컨테이너" 라는 용어는 Cassandra API에 대해 각각 "keyspace" 및 "table"에 매핑됩니다.

이 방법의 장점은 데이터베이스에서 처리량 용량을 관리 하는 간단한 턴키 방법 이라는 것입니다. 그러나 대부분의 경우에는 크기를 조정 하는 방법을 사용 하 여 비용 효율적이 고 높은 성능을 위해 특정 수준의 자동화가 필요할 수 있습니다. 다음 섹션에서는 관련 시나리오와 방법을 설명 합니다.

## <a id="use-control-plane"></a>제어 평면 사용

Cassandra에 대 한 Azure Cosmos DB API는 다양 한 제어 평면 기능을 사용 하 여 프로그래밍 방식으로 처리량을 조정 하는 기능을 제공 합니다. 지침 및 샘플은 [Azure Resource Manager](manage-cassandra-with-resource-manager.md), [Powershell](powershell-samples-cassandra.md)및 [Azure CLI](cli-samples-cassandra.md) 문서를 참조 하세요.

이 방법의 장점은 타이머를 기반으로 하는 리소스의 확장 또는 축소를 자동화 하 여 사용량이 많은 활동 또는 활동 기간을 고려 하는 것입니다. Azure Functions 및 Powershell을 사용 하 여이를 수행 하는 방법 [에 대 한](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) 샘플을 살펴보세요.

이 방법의 단점은 예기치 않은 변경 크기 조정 요구 사항에 실시간으로 응답할 수 없다는 것입니다. 대신 시스템, 클라이언트/SDK 수준에서 또는 [Autopilot](provision-throughput-autopilot.md)를 사용 하 여 응용 프로그램 컨텍스트를 활용 해야 할 수 있습니다.

## <a id="use-cql-queries"></a>특정 SDK를 사용 하 여 CQL 쿼리 사용

지정 된 데이터베이스 또는 컨테이너에 대해 [CQL ALTER 명령을](cassandra-support.md#keyspace-and-table-options) 실행 하 여 코드를 사용 하 여 시스템을 동적으로 확장할 수 있습니다.

이 방법의 장점은 응용 프로그램에 적합 한 사용자 지정 방식으로 확장 요구에 대응할 수 있다는 점입니다. 이 접근 방식에서는 표준 r u/초 요금 및 요금을 계속 활용할 수 있습니다. 시스템의 규모 요구 사항이 대체로 예측 가능한 경우 (70% 이상) CQL에서 SDK를 사용 하면 Autopilot를 사용 하는 것 보다 비용 효율적인 자동 크기 조정 방법이 될 수 있습니다. 이 방법의 단점은 속도로 인해 재시도를 구현 하는 것이 매우 복잡할 수 있으며,이 경우에는 대기 시간이 길어질 수 있습니다.

## <a id="use-autopilot"></a>Autopilot 사용

수동 또는 프로그래밍 방식으로 처리량을 프로 비전 하는 방법 외에도 Autopilot 모드에서 Azure cosmos 컨테이너를 구성할 수 있습니다. Autopilot 모드는 Sla를 손상 시 키 지 않고 지정 된 r u 범위 내에서 사용량 요구에 맞게 자동으로 확장 됩니다. 자세히 알아보려면 [autopilot 모드에서 Azure Cosmos 컨테이너 및 데이터베이스 만들기](provision-throughput-autopilot.md) 문서를 참조 하세요.

이 방법의 장점은 시스템에서 크기 조정 요구를 관리 하는 가장 쉬운 방법 이라는 점입니다. 구성 된 기능 **범위 내에서**요율 제한을 적용 하지 않도록 보장 합니다. 이는 시스템의 크기 조정 요구 사항을 예측할 수 있는 경우에는 위에서 언급 한 맞춤식 제어 평면이 나 SDK 수준 방법을 사용 하는 것 보다 Autopilot 크기 조정 요구를 처리 하는 방법이 저렴 하다는 단점이 있습니다.

## <a name="next-steps"></a>다음 단계

- Java 애플리케이션을 사용하여 [Cassandra API 계정, 데이터베이스 및 테이블 만들기](create-cassandra-api-account-java.md)를 시작합니다.
