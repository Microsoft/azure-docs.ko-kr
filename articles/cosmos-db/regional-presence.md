---
title: Azure Cosmos DB의 지역 현재 상태
description: 이 문서에서는 Azure Cosmos DB 및 여러 클라우드 환경의 지역 현재 상태에 대해 설명합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753220"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Azure Cosmos DB의 지역 현재 상태

Azure Cosmos DB는 Azure의 기본 서비스 이며 기본적으로 Azure를 사용할 수 있는 모든 지역에서 항상 사용할 수 있습니다. 현재, Azure는 [54개 지역](https://azure.microsoft.com/global-infrastructure/regions/)에서 사용할 수 있습니다. 

[![Azure Cosmos DB를 사용할 수 있는 지역](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB는 고객이 사용할 수 있는 5개의 고유한 Azure 클라우드 환경에서 사용할 수 있습니다.

* **Azure 공용** 클라우드: 전역적으로 사용할 수 있습니다.

* **Azure 중국 21vianet** 은 중국의 가장 큰 인터넷 공급자 중 하나인 Microsoft와 21vianet 간의 고유한 파트너 관계를 통해 사용할 수 있습니다.

* **Azure Germany**: 데이터 트러스티 모델에 따라 서비스를 제공하며 고객 데이터는 Deutsche Telekom 자회사 T-Systems International GmbH가 독일 데이터 신탁 기관으로서 독일에서 관리합니다.

* **Azure Government**: 미국 4개 지역에서 미국 정부 기관 및 해당 파트너가 사용할 수 있습니다. 

* **DoD (방어 부서)에 대한 Azure Government** 미국의 방어 부서와 미국의 두 지역에서 사용할 수 있습니다.

## <a name="regional-presence-with-global-distribution"></a>전역 배포가 지원되는 지역 현재 상태

Azure Cosmos DB (SQL, MongoDB, Cassandra, Gremlin 및 Table 포함)에서 제공 하는 모든 Api는 기본적으로 모든 Azure 지역에서 사용할 수 있습니다. 예를 들어 MongoDB 및 Cassandra Api는 모든 글로벌 Azure 지역 뿐만 아니라 중국, 독일, 정부 및 DoD (학과 (학과) 지역 등의 소 버린 클라우드에서도 Azure Cosmos DB으로 노출 될 수 있습니다.

Azure Cosmos DB는 [전역적으로 분산](distribute-data-globally.md) 된 데이터베이스 서비스입니다. 제한 없는 수의 Azure 지역을 Azure Cosmos 계정과 연결할 수 있으며 데이터는 자동으로 투명하게 복제됩니다. 언제든지 Azure Cosmos 계정에서 지역을 추가 또는 제거할 수 있습니다. 턴키 전역 배포 기능 및 다중 마스터 복제 프로토콜을 사용하여 Azure Cosmos DB는 99번째 백분위수에서 10밀리초 미만의 읽기 및 쓰기 대기 시간, 99.999의 읽기 및 쓰기 가용성, Azure Cosmos 계정과 연결된 모든 지역에서 읽기 및 쓰기의 프로비전된 처리량을 탄력적으로 조정하는 기능을 제공합니다. 또한 Azure Cosmos DB는 5개의 잘 정의된 일관성 모델을 제공하고 특정 일관성 모델을 데이터에 적용하도록 선택할 수 있습니다. 마지막으로 Azure Cosmos DB는 프로 비전 된 처리량, 99 번째 백분위 수의 대기 시간, 고가용성 및 일관성을 포괄 하는 포괄적인 [Service Level Agreement(서비스 수준 약정) (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) 를 제공 하는 업계의 유일한 데이터베이스 서비스입니다. 위의 기능은 모든 Azure 클라우드에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 통해 Azure Cosmos DB의 핵심 개념에 대해 알아볼 수 있습니다.

* [글로벌 데이터 배포](distribute-data-globally.md)
* [Azure Cosmos DB 계정을 관리하는 방법](manage-account.md)
* [Cosmos DB 컨테이너 및 데이터베이스에 대한 처리량 프로비전](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
