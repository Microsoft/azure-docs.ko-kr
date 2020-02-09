---
title: Azure Cosmos DB NoSQL 및 관계형 데이터베이스 간의 차이점 이해
description: 이 문서에서는 NoSQL과 관계형 데이터베이스의 차이점을 열거 합니다.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896624"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>NoSQL 및 관계형 데이터베이스의 차이점 이해

이 문서에서는 관계형 데이터베이스에 대한 NoSQL 데이터베이스의 주요 이점 중 일부를 열거 합니다. NoSQL에서 작업 하는 몇 가지 문제에 대해서도 설명 합니다. 존재 하는 다른 데이터 저장소를 자세히 살펴보기 위해 [올바른 데이터 저장소를 선택](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)하는 문서를 살펴보세요.

## <a name="high-throughput"></a>높은 처리량

관계형 데이터베이스 시스템을 유지 관리할 때 가장 명백한 문제 중 하나는 대부분의 관계형 엔진이 엄격한 [ACID 의미 체계](https://en.wikipedia.org/wiki/ACID)를 적용 하기 위해 잠금 및 래치를 적용 한다는 것입니다. 이 방법은 데이터베이스 내에서 일관 된 데이터 상태를 보장 하는 측면에서 이점을 제공 합니다. 그러나 동시성, 대기 시간 및 가용성에 대한 상당한 절충은 있습니다. 이러한 기본 아키텍처 제한으로 인해 트랜잭션 볼륨이 높으면 데이터를 수동으로 분할 해야 할 수 있습니다. 수동 분할을 구현 하는 것은 시간이 많이 걸리고 번거로운 연습이 될 수 있습니다.

이러한 시나리오에서 [분산 데이터베이스](https://en.wikipedia.org/wiki/Distributed_database) 는 확장 가능한 솔루션을 제공할 수 있습니다. 그러나 유지 관리는 여전히 비용이 많이 들고 시간이 많이 걸리는 연습이 될 수 있습니다. 관리자는 시스템의 분산 특성을 투명 하 게 유지 하기 위해 추가 작업을 수행 해야 할 수 있습니다. 데이터베이스의 "연결 끊김" 특성을 고려해 야 할 수도 있습니다.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) 은 모든 Azure 지역에서 전 세계적으로 배포 하 여 이러한 문제를 단순화 합니다. 파티션 범위는 응용 프로그램에서 데이터베이스를 원활 하 게 확장 하는 동시에 고가용성을 유지 하기 위해 동적으로 세분화할 수 있습니다. 세분화 된 다중 테넌트와 긴밀 하 게 제어 되는 클라우드 네이티브 리소스 관리는 [서비스가 놀라운 속도로 대기 시간을 보장](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) 하 고 예측 가능한 성능을 용이 하 게 합니다. 분할은 완전히 관리 되므로 관리자가 코드를 작성 하거나 파티션을 관리할 필요가 없습니다.

트랜잭션 볼륨이 초당 수천 개의 트랜잭션과 같이 극단적인 수준에 도달 하는 경우 분산 된 NoSQL 데이터베이스를 고려해 야 합니다. 효율성을 극대화 하 고, 유지 관리 편의성을 높이고, 총 소유 비용을 줄이는 Azure Cosmos DB을 고려 합니다.

![백 엔드](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>계층적 데이터

데이터베이스의 트랜잭션에 많은 부모-자식 관계가 포함 될 수 있는 많은 수의 사용 사례가 있습니다. 이러한 관계는 시간이 지남에 따라 현저 하 게 증가 하 고 관리 하기 어려울 수 있습니다. [계층적 데이터베이스](https://en.wikipedia.org/wiki/Hierarchical_database_model) 의 형식은 1980 년대 중에 발생 했지만 저장소의 비효율성 때문에 널리 사용 되지 않았습니다. 또한 [Ted codd의 관계형 모델이](https://en.wikipedia.org/wiki/Relational_model) 거의 모든 주요 데이터베이스 관리 시스템에서 사용 하는 사실상 표준으로 사용 되기 때문에 트랙 션이 손실 됩니다.

그러나 현재는 문서 스타일 데이터베이스의 인기도가 크게 증가 했습니다. 이러한 데이터베이스는 계층적 데이터베이스 패러다임의 reinventing로 간주할 수 있으며, 이제는 디스크에 데이터를 저장 하는 비용과 관련 하 여 걱정 되지 않습니다. 결과적으로 관계형 데이터베이스에서 많은 복합 부모-자식 엔터티 관계를 유지 관리 하는 것은 최신 문서 지향 방법과 비교 하 여 앤티 패턴으로 간주 될 수 있습니다.

[개체 지향 디자인](https://en.wikipedia.org/wiki/Object-oriented_design)의 등장 및 관계형 모델과 결합할 때 발생 하는 [임피던스 불일치](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) 는 특정 사용 사례에 대한 관계형 데이터베이스의 앤티앨리어싱을 강조 표시 합니다. 숨겨진 경우에도 종종 상당한 유지 관리 비용이 발생할 수 있습니다. [ORM 방식은](https://en.wikipedia.org/wiki/Object-relational_mapping) 이를 부분적으로 완화 하기 위해 발전 했지만 문서 지향 데이터베이스는 개체 지향 접근 방식으로 훨씬 더 잘 병합 됩니다. 이 접근 방식을 사용 하 여 개발자는 ORM 드라이버 또는 맞춤식 언어별 [데이터베이스 엔진](https://en.wikipedia.org/wiki/Object_database)에 적용 되지 않습니다. 데이터에 부모-자식 관계와 계층의 수준 수준이 많은 경우 [AZURE COSMOS DB SQL API](https://docs.microsoft.com/azure/cosmos-db/introduction)와 같은 nosql 문서 데이터베이스를 사용 하는 것을 고려해 볼 수 있습니다.

![OrderDetails](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>복합 네트워크 및 관계

Ironically의 이름을 지정 하면 관계형 데이터베이스는 심층적이 고 복잡 한 관계를 모델링 하기 위한 최적의 솔루션을 제공 합니다. 그 이유는 엔터티 간의 관계가 관계형 데이터베이스에 실제로 존재 하지 않기 때문입니다. 쿼리를 사용 하 여 매핑을 허용 하기 위해 직교 조인이 필요한 복잡 한 관계를 사용 하 여 런타임에 계산 해야 합니다. 따라서 관계가 증가 함에 따라 연산이 계산 측면에서 비용이 더 많이 듭니다. 경우에 따라 이러한 엔터티를 관리 하려는 관계형 데이터베이스를 사용할 수 없게 됩니다.

관계형 데이터베이스가 등장 하는 동안 다양 한 형태의 "네트워크" 데이터베이스가 발생 했지만 계층적 데이터베이스의 경우에는 이러한 시스템이 인기를 얻기 노력해. 시간이 오래 걸리는 경우는 사용 사례가 부족 하 여 저장소 비효율성이 발생 했습니다. 현재 graph 데이터베이스 엔진은 네트워크 데이터베이스 패러다임의 등장로 간주할 수 있습니다. 이러한 시스템의 주요 혜택은 관계가 데이터베이스 내에서 "first class 시민이"로 저장 된다는 것입니다. 따라서 각 새 조인이 나 교차곱을 사용 하 여 시간 복잡성이 증가 하는 것이 아니라 일정 시간 내에 트래버스 관계를 수행할 수 있습니다.

데이터베이스에 복잡 한 관계 네트워크를 유지 관리 하는 경우이 데이터를 관리 하기 위한 [Azure Cosmos DB GREMLIN API](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) 와 같은 그래프 데이터베이스를 고려해 야 할 수 있습니다.

![그래프](./media/relational-or-nosql/graph.png)

Azure Cosmos DB은 모든 주요 NoSQL 모델 형식에 대한 API 프로젝션을 제공 하는 다중 모델 데이터베이스 서비스입니다. 열 패밀리, 문서, 그래프 및 키-값입니다. [Gremlin (그래프)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) 및 SQL (Core) 문서 API 계층은 완전히 상호 운용 가능 합니다. 여기에는 프로그래밍 기능 수준에서 서로 다른 모델 간을 전환할 때의 이점이 있습니다. 그래프 저장소는 복잡 한 네트워크 순회와 동일한 저장소의 문서 레코드로 모델링 된 트랜잭션의 측면에서 쿼리할 수 있습니다.

## <a name="fluid-schema"></a>유체 스키마

관계형 데이터베이스의 또 다른 특징은 디자인 타임에 스키마를 정의 해야 한다는 것입니다. 여기에는 데이터의 참조 무결성 및 준수 측면에서의 이점이 있습니다. 그러나 응용 프로그램이 증가 함에 따라 제한 될 수도 있습니다. 동일한 테이블 또는 데이터베이스 정의를 공유 하는 논리적으로 분리 된 모델에서 스키마 변경에 대한 응답은 시간이 지남에 따라 복잡할 수 있습니다. 이러한 사용 사례는 스키마를 사용 하 여 응용 프로그램에서 레코드 별로 관리 하는 데 유용한 경우가 많습니다. 이렇게 하려면 데이터베이스를 "스키마에 관계 없이" 해야 하며, 레코드 내에 포함 된 데이터를 기준으로 레코드를 "자체 설명" 할 수 있도록 해야 합니다.

구조가 지속적으로 높은 속도로 변화 하는 데이터를 관리 하는 경우, 특히 데이터베이스 전체에서 준수를 적용 하기 어려운 외부 원본에서 트랜잭션을 가져올 수 있는 경우에는 더 많은 스키마를 사용할 수 없는 접근 방식을 고려할 수 있습니다. Azure Cosmos DB와 같은 관리 되는 NoSQL 데이터베이스 서비스를 사용 합니다.

## <a name="microservices"></a>마이크로 서비스

[마이크로 서비스](https://en.wikipedia.org/wiki/Microservices) 패턴이 최근 몇 년 동안 크게 증가 했습니다. 이 패턴은 [서비스 지향 아키텍처](https://en.wikipedia.org/wiki/Service-oriented_architecture)의 루트를 포함 합니다. 이러한 최신 마이크로 서비스 아키텍처에서의 데이터 전송에 대한 사실상의 표준은 [JSON](https://en.wikipedia.org/wiki/JSON)이며, 대부분의 문서 지향 Nosql 데이터베이스에 대한 저장 미디어 이기도 합니다. 이렇게 하면 NoSQL 문서는 복잡 한 마이크로 서비스 구현에서 지 속성 및 동기화 ( [이벤트 소싱 패턴](https://en.wikipedia.org/wiki/Event-driven_architecture)사용) 둘 다에 더욱 원활 하 게 적합 합니다. 기존 관계형 데이터베이스는 이러한 아키텍처에서 유지 관리 하기가 훨씬 더 복잡할 수 있습니다. 이는 Api 간의 상태와 동기화에 필요한 변환의 양이 더 많기 때문입니다. 특히 Azure Cosmos DB 많은 NoSQL 데이터베이스 보다 훨씬 더 원활 하 게 JSON 기반 마이크로 서비스 아키텍처를 사용할 수 있도록 하는 다양 한 기능이 있습니다.

* 순수 JSON 데이터 형식 선택
* 데이터베이스에 기본 제공 되는 JavaScript 엔진과 [쿼리 API](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) 입니다.
* 클라이언트에서 컨테이너에 대한 수정 알림을 받기 위해 구독할 수 있는 최신 [변경 피드](https://docs.microsoft.com/azure/cosmos-db/change-feed) 입니다.

## <a name="some-challenges-with-nosql-databases"></a>NoSQL 데이터베이스를 사용 하는 몇 가지 문제

NoSQL 데이터베이스를 구현할 때 약간의 이점이 있지만 고려해 야 할 몇 가지 과제가 있습니다. 이러한 작업은 관계형 모델을 사용할 때와 동일한 수준에 있지 않을 수 있습니다.

* 동일한 엔터티를 가리키는 여러 관계가 있는 트랜잭션입니다.
* 전체 데이터 집합에서 강력한 일관성이 필요한 트랜잭션입니다.

첫 번째 과제를 살펴보면 NoSQL 데이터베이스의 규칙에 대한 규칙은 일반적으로 비 정규화 되어 있습니다 .이는 앞에서 언급 한 것 처럼 분산 시스템에서 보다 효율적인 읽기를 생성 하는 것입니다. 그러나이 방법을 사용 하면 몇 가지 디자인 문제를 해결할 수 있습니다. 하나의 범주 및 여러 태그와 관련 된 제품의 예를 살펴보겠습니다.

![조인](./media/relational-or-nosql/many-joins.png)

NoSQL 문서 데이터베이스에서 가장 좋은 방법은 범주 이름 및 태그 이름을 "제품 문서"로 직접 비 정규화 하는 것입니다. 그러나 범주, 태그 및 제품을 동기화 상태로 유지 하기 위해이를 용이 하 게 하는 디자인 옵션을 선택 하면 데이터는 "일 대 다"로 단순 업데이트 되는 것이 아니라 제품의 여러 레코드 간에 중복 되기 때문에 유지 관리 복잡성이 추가 됩니다. 관계 및 데이터를 검색 하는 조인입니다. 

절충은 정규화 되지 않은 레코드에서 읽기가 더 효율적 이며 개념적으로 조인 된 엔터티 수가 증가 함에 따라 점점 더 효율적입니다. 그러나 비 정규화 레코드에서 조인 된 엔터티 수가 증가 함에 따라 읽기 효율성이 증가 하는 것 처럼, 엔터티를 동기화 된 상태로 유지 하는 경우에도 유지 관리가 복잡해 집니다. 이러한 절충을 완화 하는 한 가지 방법은 [하이브리드 데이터 모델](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)을 만드는 것입니다.

NoSQL 데이터베이스에서 더 많은 유연성을 제공 하 여 이러한 장단점을 처리할 수 있지만 유연성이 증가 하면 더 많은 디자인 결정이 발생할 수도 있습니다. [실제 예를 사용 하 여 Azure Cosmos DB에서 데이터를 모델링 하 고 분할 하는 방법](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)문서를 참조 하세요. 여기에는 사용자가 서로 다른 파티션에 앉아 있지만 다른 컨테이너에 있는 경우를 제외 하 고는 [비 정규화 된 사용자 데이터를 동기화](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) 된 상태로 유지 하는 방법이 포함 됩니다.

강력한 일관성을 고려 하 여 전체 데이터 집합에 대해이를 반드시 수행 해야 하는 것은 드뭅니다. 그러나이 작업이 필요한 경우에는 분산 데이터베이스에서 어려울 수 있습니다. 강력한 일관성을 유지 하려면 모든 복제본 및 지역에서 데이터를 동기화 해야 클라이언트에서 데이터를 읽을 수 있습니다. 이로 인해 읽기의 대기 시간이 길어질 수 있습니다.

여기서는 여기에 관련 된 다양 한 장단점에 대해 관계형 데이터베이스 보다 더 많은 유연성을 제공 하지만, 작은 규모의 구현을 위해이 방법을 사용 하면 디자인 고려 사항을 더 추가할 수 있습니다. Azure Cosmos DB 이 항목에 대한 자세한 내용은 [일관성, 가용성 및 성능 장단점](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs) 에 대한 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Cosmos 계정 및 기타 개념을 관리 하는 방법을 알아봅니다.

* [Azure Cosmos 계정을 관리하는 방법](how-to-manage-database-account.md)
* [글로벌 분포](distribute-data-globally.md)
* [일관성 수준](consistency-levels.md)
* [Azure Cosmos 컨테이너와 항목으로 작업](databases-containers-items.md)
* [Azure Cosmos 계정에 대한 VNET 서비스 엔드포인트](vnet-service-endpoint.md)
* [Azure Cosmos 계정에 대한 IP 방화벽](firewall-support.md)
* [Azure Cosmos 계정에서 Azure 지역을 추가하고 제거하는 방법](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
