---
title: 일관성 수준 및 Azure Cosmos DB API
description: Azure Cosmos DB 및 Apache Cassandra의 다양 한 Api 간 일관성 수준 매핑 이해 MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 819929fb157444ae53df113c0318dd18146156c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442023"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>일관성 수준 및 Azure Cosmos DB API

Azure Cosmos DB는 널리 사용 되는 데이터베이스에 대한 유선 프로토콜 호환 Api를 기본적으로 지원 합니다. 여기에는 MongoDB, Apache Cassandra, Gremlin 및 Azure Table storage가 포함 됩니다. 이러한 데이터베이스는 일관성 수준에 대해 정확 하 게 정의 된 일관성 모델 또는 SLA 지원 보장을 제공 하지 않습니다. 이러한 데이터베이스는 일반적으로 Azure Cosmos DB에서 제공된 5가지 일관성 모델의 하위 집합만 제공합니다. 

SQL API, Gremlin API 및 Table API 사용 하는 경우 Azure Cosmos 계정에 구성 된 기본 일관성 수준이 사용 됩니다. 

MongoDB에 대한 Cassandra API 또는 Azure Cosmos DB의 API를 사용 하는 경우 응용 프로그램은 더 강력한 일관성 및 내구성 보증을 통해 각각 Apache Cassandra 및 MongoDB에서 제공 하는 일관성 수준의 전체 집합을 얻습니다. 이 문서에서는 Apache Cassandra 및 MongoDB 일관성 수준에 대한 해당 Azure Cosmos DB 일관성 수준을 보여 줍니다.

## <a id="cassandra-mapping"></a>Apache Cassandra 및 Azure Cosmos DB 일관성 수준 간 매핑

Azure Cosmos DB와 달리 Apache Cassandra는 기본적으로 정확 하 게 정의 된 일관성 보장을 제공 하지 않습니다.  대신, Apache Cassandra는 높은 가용성, 일관성 및 대기 시간을 절충 하는 쓰기 일관성 수준 및 읽기 일관성 수준을 제공 합니다. Azure Cosmos DB의 Cassandra API를 사용 하는 경우: 

* Apache Cassandra의 쓰기 일관성 수준은 Azure Cosmos 계정에 구성 된 기본 일관성 수준에 매핑됩니다. 

* Azure Cosmos DB은 Cassandra client 드라이버에서 지정 된 읽기 일관성 수준을 읽기 요청에 따라 동적으로 구성 된 Azure Cosmos DB 일관성 수준 중 하나로 동적으로 매핑합니다. 

다음 표에서는 Cassandra API를 사용할 때 기본 Cassandra 일관성 수준이 Azure Cosmos DB의 일관성 수준에 매핑되는 방법을 보여 줍니다.  

[![Cassandra 일관성 모델 매핑](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>MongoDB 및 Azure Cosmos DB 일관성 수준 간 매핑

Azure Cosmos DB와 달리 native MongoDB는 정확 하 게 정의 된 일관성 보장을 제공 하지 않습니다. 대신, 네이티브 MongoDB를 사용 하면 쓰기 우려, 읽기 관심사 및 isMaster 지시어를 사용 하 여 읽기 작업을 주 복제본 또는 보조 복제본으로 전달 하 여 원하는 일관성 수준을 달성할 수 있습니다. 

MongoDB에 대한 Azure Cosmos DB API를 사용 하는 경우 MongoDB 드라이버는 쓰기 지역을 주 복제본으로 처리 하 고 다른 모든 지역은 읽기 복제본입니다. Azure Cosmos 계정에 연결 된 영역을 주 복제본으로 선택할 수 있습니다. 

MongoDB에 Azure Cosmos DB API를 사용 하는 동안:

* 쓰기 문제는 Azure Cosmos 계정에 구성 된 기본 일관성 수준에 매핑됩니다.
 
* Azure Cosmos DB은 MongoDB 클라이언트 드라이버에 지정 된 읽기 문제를 읽기 요청에 대해 동적으로 구성 된 Azure Cosmos DB 일관성 수준 중 하나로 동적으로 매핑합니다. 

* Azure Cosmos 계정에 연결 된 특정 지역에는 해당 지역을 쓰기 가능한 첫 번째 지역으로 만들어 주석을 달 수 있습니다. 

다음 표에서는 MongoDB에 대한 Azure Cosmos DB의 API를 사용 하는 경우 기본 MongoDB 쓰기/읽기 관련 사항을 Azure Cosmos 일관성 수준에 매핑하는 방법을 보여 줍니다.

[![MongoDB 일관성 모델 매핑](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>다음 단계

오픈 소스 API 및 Cosmos DB API 간의 일관성 수준 및 호환성에 대해 자세히 알아봅니다. 다음 문서를 참조하세요.

* [다양한 일관성 수준의 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB의 API for MongoDB에서 지원되는 MongoDB 기능](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API에서 지원된 Apache Cassandra 기능](cassandra-support.md)
