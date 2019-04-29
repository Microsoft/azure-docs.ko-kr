---
title: Azure Cosmos DB의 분할 및 수평적 크기 조정
description: Azure Cosmos DB, 분할 및 파티션 키를 구성 하는 방법 및 응용 프로그램에 대 한 올바른 파티션 키를 선택 하는 방법에서 분할 작동에 알아봅니다.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: cf454d6504f0433d7ac7ca883982ae317b14f814
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60928866"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB의 분할 및 수평적 크기 조정

이 문서에서는 Azure Cosmos DB에서 물리적 및 논리적 파티션을 설명 합니다. 또한 크기 조정 및 분할에 대 한 모범 사례를 설명 합니다. 

## <a name="logical-partitions"></a>논리 파티션

논리 파티션은 동일한 파티션 키를 가진 항목의 집합으로 구성 됩니다. 예를 들어, 모든 항목이 포함 되어 있는 컨테이너에에서는 `City` 속성을 사용할 수 `City` 컨테이너에 대 한 파티션 키로 합니다. 그룹에 대 한 특정 값이 있는 항목의 `City`와 같은 `London`를 `Paris`, 및 `NYC`, 서로 다른 논리 파티션을 구성 합니다. 기본 데이터가 삭제 된 경우 파티션 삭제에 대해 걱정할 필요가 없습니다.

Azure Cosmos DB에서 컨테이너는 확장성의 기본 단위입니다. 컨테이너에서 프로 비전 된 처리량 및 컨테이너에 추가 되는 데이터는 자동으로 논리 파티션이 여러 분할 된 (가로). 데이터 및 처리량 Azure Cosmos 컨테이너에 대 한 지정한 파티션 키에 따라 분할 됩니다. 자세한 내용은 [Azure Cosmos 컨테이너를 만드는](how-to-create-container.md)합니다.

또한 논리 파티션은 데이터베이스 트랜잭션의 범위를 정의합니다. 사용 하 여 논리 파티션 내에서 항목을 업데이트할 수는 [스냅숏 격리를 사용 하 여 트랜잭션을](database-transactions-optimistic-concurrency.md)합니다. 새 항목 컨테이너에 추가 되 면 시스템에서 새 논리 파티션은 투명 하 게 만들어집니다.

## <a name="physical-partitions"></a>실제 파티션

Azure Cosmos 컨테이너를 데이터와 처리량을 많은 수의 논리 파티션 분산 하 여 크기가 조정 됩니다. 하나 이상의 논리 파티션이 라고도 하는 복제본의 집합으로 구성 하는 물리적 파티션에 매핑되는 내부적으로 [ *복제본 세트*](global-dist-under-the-hood.md)합니다. 각 복제본 Azure Cosmos DB 데이터베이스 엔진 인스턴스의 호스트를 설정 합니다. 복제본 집합에서는 내구성, 고가용성 및 일관성 있는 실제 파티션 내에 저장 된 데이터. 실제 파티션이 저장소 및 요청 단위 (Ru)의 최대 크기를 지원합니다. 실제 파티션을 구성 하는 각 복제본은 파티션 저장소 할당량을 상속 합니다. 실제 파티션의 모든 복제본에는 전체적으로 실제 파티션을 할당 되는 처리량을 지원 합니다. 

다음 이미지에서는 논리 파티션이 전역적으로 배포된 실제 파티션에 매핑되는 방법을 보여 줍니다.

![Azure Cosmos DB에서 분할 하는 방법을 보여 주는 이미지](./media/partition-data/logical-partitions.png)

컨테이너에 대 한 프로 비전 된 처리량은 실제 파티션 간에 균등 하 게 구분 됩니다. 처리량 요청을 균등 하 게 분산 하지 않습니다 하는 파티션 키 디자인에는 "핫" 파티션을 만들 수 있습니다. 속도 제한 및 프로 비전 된 처리량 및 더 높은 비용의 비효율적인 사용 핫 파티션을 발생할 수 있습니다.

논리 파티션과 달리 실제 파티션은 시스템의 내부 구현입니다. 크기, 배치 또는 실제 파티션 수를 제어할 수 없습니다 및 논리 파티션이 물리적 파티션 간의 매핑을 제어할 수 없습니다. 그러나 논리 파티션 및 데이터, 워크 로드 및 처리량을 분포의 수를 제어할 수 있습니다 [올바른 논리 파티션 키 선택](partitioning-overview.md#choose-partitionkey)합니다.

## <a name="next-steps"></a>다음 단계

* 에 대 한 자세한 [파티션 키를 선택](partitioning-overview.md#choose-partitionkey)합니다.
* [Azure Cosmos DB에서 프로비전된 처리량](request-units.md)에 대한 자세한 정보
* [Azure Cosmos DB에서 글로벌 배포](distribute-data-globally.md)에 대한 자세한 정보
* [Azure Cosmos 컨테이너의 처리량을 프로비전](how-to-provision-container-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스의 처리량을 프로비전](how-to-provision-database-throughput.md)하는 방법을 알아봅니다.
