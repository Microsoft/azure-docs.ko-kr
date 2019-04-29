---
title: 일관성 수준 및 Azure Cosmos DB API
description: Azure Cosmos DB에서 API에 대한 일관성 수준 이해
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a8617ae2b01fc89a4c957b8610164a2b53a16f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892487"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>일관성 수준 및 Azure Cosmos DB API

Azure Cosmos DB는 통신에 대 한 기본 지원을 제공 인기 있는 데이터베이스에 대 한 프로토콜-호환 되는 Api입니다. MongoDB, Apache Cassandra, Gremlin을 및 Azure Table storage를 포함 됩니다. 이러한 데이터베이스에 정의 된 일관성 모델이 나 일관성 수준에 대 한 보장 SLA 지원 정확 하 게 제공 하지 않습니다. 이러한 데이터베이스는 일반적으로 Azure Cosmos DB에서 제공된 5가지 일관성 모델의 하위 집합만 제공합니다. 

SQL API, Gremlin API 및 Table API를 사용 하는 경우 Azure Cosmos 계정에 구성 된 기본 일관성 수준을 사용 됩니다. 

Cassandra API 또는 Azure Cosmos DB API에 MongoDB를 사용 하는 경우 응용 프로그램 일관성 수준을 제공 하는 Apache Cassandra 및 MongoDB, 각각의 훨씬 강력한 일관성 및 영속성 보장을 사용 하 여 전체 집합을 가져옵니다. 이 문서에서는 Apache Cassandra 및 MongoDB 일관성 수준에 대 한 해당 Azure Cosmos DB 일관성 수준을 보여줍니다.


## <a id="cassandra-mapping"></a>Apache Cassandra 및 Azure Cosmos DB 일관성 수준 간 매핑

달리 AzureCosmos DB Apache Cassandra 기본적으로 정확 하 게 정의 된 일관성 보장을 보여주는 다루지 않습니다.  대신 Apache Cassandra 쓰기 일관성 수준 및 높은 가용성, 일관성 및 대기 시간 절충 작업을 사용 하도록 설정 하는 읽기 일관성 수준을 제공 합니다. Azure Cosmos DB의 Cassandra API를 사용 하 여 하는 경우: 

* Apache Cassandra 쓰기 일관성 수준은 Azure Cosmos 계정에 구성 된 기본 일관성 수준에 매핑됩니다. 

* Azure Cosmos DB는 읽기 일관성 수준은 읽기 요청에 동적으로 구성 된 Azure Cosmos DB 일관성 수준 중 하나에 Cassandra 클라이언트 드라이버에서 지정 된 동적으로 매핑됩니다. 

다음 표에서 네이티브 Cassandra 일관성 수준에 매핑되는 방법을 Azure Cosmos DB의 일관성 수준 Cassandra API를 사용 하는 경우를 보여 줍니다.  

[![Cassandra 일관성 모델 매핑](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>MongoDB 및 Azure Cosmos DB 일관성 수준 간의 매핑

Azure Cosmos DB와는 달리 네이티브 MongoDB는 정확 하 게 정의 된 일관성 보장을 제공 하지 않습니다. 대신 네이티브 MongoDB를 사용 하면 다음 일관성 보장을 보여주는 구성 하려면: 쓰기 문제가, 읽기 문제가 및 직접 원하는 일관성 수준을 달성 하기 위해 기본 또는 보조 복제본에 읽기 작업에는 isMaster 지시문입니다. 

MongoDB 드라이버는 주 복제본으로 쓰기 지역의 처리 하는 MongoDB에 대 한 Azure Cosmos DB API를 사용 하는 경우 및 다른 모든 지역의 복제본 읽혀집니다. 주 복제본으로 Azure Cosmos 계정과 연결 된 지역을 선택할 수 있습니다. 

MongoDB에 대 한 Azure Cosmos DB API를 사용 하는 동안:

* 쓰기 문제는 Azure Cosmos 계정에 구성 된 기본 일관성 수준에 매핑됩니다.
 
* Azure Cosmos DB는 MongoDB 클라이언트 드라이버에서 읽기 요청을 동적으로 구성 된 Azure Cosmos DB 일관성 수준 중 하나에 의해 지정 된 읽기 문제를 동적으로 매핑됩니다. 

* 계정에 연결 된 Azure Cosmos "마스터"으로 쓰기 가능한 첫 번째 지역으로 지역 하 여 특정 지역에 주석을 달 수 있습니다. 

다음 표에서 설명 하는 방법을 네이티브 MongoDB 쓰기/읽기 문제 MongoDB에 대 한 Azure Cosmos DB API를 사용 하는 경우 Azure Cosmos 일관성 수준에 매핑됩니다.

[![MongoDB 일관성 모델 매핑](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>다음 단계

오픈 소스 API 및 Cosmos DB API 간의 일관성 수준 및 호환성에 대해 자세히 알아봅니다. 다음 문서를 참조하세요.

* [다양한 일관성 수준의 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB의 API for MongoDB에서 지원되는 MongoDB 기능](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API에서 지원된 Apache Cassandra 기능](cassandra-support.md)