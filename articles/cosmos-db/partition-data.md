---
title: Azure Cosmos DB의 분할 및 수평적 크기 조정
description: Azure Cosmos DB에서 분할이 작동 하는 방식, 분할 및 파티션 키를 구성 하는 방법 및 응용 프로그램에 적합 한 파티션 키를 선택 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754916"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB의 분할 및 수평적 크기 조정

이 문서에서는 Azure Cosmos DB의 물리적 및 논리적 파티션을 설명 합니다. 또한 크기 조정 및 분할에 대한 모범 사례를 설명 합니다. 

## <a name="logical-partitions"></a>논리 파티션

논리적 파티션은 동일한 파티션 키를 가진 항목 집합으로 구성 됩니다. 예를 들어 모든 항목에 `City` 속성이 포함 된 컨테이너에서 `City`를 컨테이너에 대한 파티션 키로 사용할 수 있습니다. @No__t_1, `Paris` 및 `NYC`와 같이 `City`에 대한 특정 값을 가진 항목의 그룹은 고유한 논리 파티션을 형성 합니다. 기본 데이터를 삭제할 때 파티션을 삭제 하는 것에 대해 걱정할 필요가 없습니다.

Azure Cosmos DB에서 컨테이너는 확장성의 기본 단위입니다. 컨테이너에 추가 되는 데이터와 컨테이너에서 프로 비전 하는 처리량은 논리적 파티션 집합에서 자동으로 (가로) 분할 됩니다. 데이터와 처리량은 Azure Cosmos 컨테이너에 대해 지정 하는 파티션 키에 따라 분할 됩니다. 자세한 내용은 [Azure Cosmos 컨테이너 만들기](how-to-create-container.md)를 참조 하세요.

또한 논리적 파티션은 데이터베이스 트랜잭션의 범위를 정의 합니다. [스냅숏 격리로 트랜잭션을](database-transactions-optimistic-concurrency.md)사용 하 여 논리적 파티션 내에서 항목을 업데이트할 수 있습니다. 새 항목이 컨테이너에 추가 되 면 시스템에서 새 논리적 파티션을 투명 하 게 만듭니다.

## <a name="physical-partitions"></a>실제 파티션

Azure Cosmos 컨테이너는 많은 수의 논리적 파티션에서 데이터와 처리량을 분산 하 여 크기를 조정 합니다. 내부적으로 하나 이상의 논리 [*파티션은 복제본 집합*](global-dist-under-the-hood.md)으로도 불리는 복제본 집합으로 구성 되는 실제 파티션에 매핑됩니다. 각 복제본 집합은 Azure Cosmos 데이터베이스 엔진의 인스턴스를 호스팅합니다. 복제본 세트를 사용 하면 실제 파티션 내에 저장 된 데이터를 지 속성, 고가용성 및 일관성을 유지할 수 있습니다. 실제 파티션은 저장소 및 요청 단위 (RUs)의 최대 크기를 지원 합니다. 실제 파티션을 구성 하는 각 복제본은 파티션의 저장소 할당량을 상속 합니다. 실제 파티션의 모든 복제본은 실제 파티션에 할당 된 처리량을 전체적으로 지원 합니다. 

다음 이미지에서는 논리 파티션이 전역적으로 배포된 실제 파티션에 매핑되는 방법을 보여 줍니다.

![Azure Cosmos DB 분할을 보여 주는 이미지](./media/partition-data/logical-partitions.png)

컨테이너에 대해 프로 비전 된 처리량은 실제 파티션 간에 균등 하 게 분할 됩니다. 처리량 요청을 균등 하 게 배포 하지 않는 파티션 키 디자인은 "핫" 파티션을 만들 수 있습니다. 핫 파티션은 속도 제한 및 프로 비전 된 처리량을 비효율적으로 사용 하 고 비용이 더 많이 들 수 있습니다.

논리 파티션과 달리 실제 파티션은 시스템의 내부 구현입니다. 실제 파티션의 크기, 배치 또는 개수를 제어할 수 없으며, 논리적 파티션과 실제 파티션 간의 매핑을 제어할 수 없습니다. 그러나 [올바른 논리적 파티션 키를 선택](partitioning-overview.md#choose-partitionkey)하 여 논리적 파티션 수와 데이터, 워크 로드 및 처리량의 분포를 제어할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [파티션 키를 선택 하는](partitioning-overview.md#choose-partitionkey)방법에 대해 알아봅니다.
* [Azure Cosmos DB에서 프로비전된 처리량](request-units.md)에 대한 자세한 정보
* [Azure Cosmos DB에서 글로벌 배포](distribute-data-globally.md)에 대한 자세한 정보
* [Azure Cosmos 컨테이너의 처리량을 프로비전](how-to-provision-container-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스의 처리량을 프로비전](how-to-provision-database-throughput.md)하는 방법을 알아봅니다.
