---
title: Azure Cosmos DB에서 처리량 크기 조정
description: 이 문서에서는 Azure Cosmos DB에서 처리량을 탄력적으로 크기 조정하는 방법을 설명합니다.
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: b645fe210e7eeb073380dcadefead3e1b4d7ccc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926430"
---
# <a name="globally-scale-provisioned-throughput"></a>전역적으로 프로비전된 처리량 크기 조정 

Azure Cosmos db에서 프로 비전 된 처리량으로 표시 됩니다 요청 단위/초 (RU/s 또는 복수 형태의 Ru)입니다. RU는 다음 이미지에서 표시된 대로 Cosmos 컨테이너에 대한 읽기 및 쓰기 작업의 비용을 모두 측정합니다.

![요청 단위](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Cosmos 컨테이너 또는 Cosmos 데이터베이스에서 RU를 프로비전할 수 있습니다. 컨테이너에서 프로 비전 된 Ru는 해당 컨테이너에서 수행 된 작업에만 사용할 수 있습니다. 데이터베이스에서 프로비전된 RU는 데이터베이스 내의 모든 컨테이너에서 공유됩니다(독점적으로 할당된 RU가 있는 모든 컨테이너 제외).

프로 비전 된 처리량을 탄력적으로 확장에 대 한 수 늘리거나 프로 비전 된 RU/s 언제 든 지 합니다. 자세한 내용은 [처리량 프로 비전 방법](set-throughput.md) Cosmos 컨테이너 및 데이터베이스를 탄력적으로 확장 하 고 있습니다. 전역적으로 크기 조정 처리량에 대 한 추가 하거나 언제 든 지 Cosmos 계정에서 영역을 제거 합니다. 자세한 내용은 [데이터베이스 계정에서 Azure 지역 추가/제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account)를 참조하세요. Cosmos 계정을 사용 하 여 여러 지역에 연결 하는 것은 짧은 대기 시간을 달성 하기 위해 여러 시나리오에서 중요 하 고 [고가용성](high-availability.md) 세계입니다.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>프로비전된 처리량을 영역에 분산하는 방법

프로 비전 하는 경우 *'R'* Ru Cosmos 컨테이너 (또는 데이터베이스)에서 Cosmos DB는 보장 *'R'* Ru에서 사용할 수 있는 *각* Cosmos 계정과 연결 된 지역입니다. 계정에 새 지역을 추가할 때마다 Cosmos DB 자동으로 프로 비전 *'R'* 새로 추가 된 지역의 Ru 합니다. 가져올 Cosmos 컨테이너에 대해 수행 된 작업 보장이 *'R'* 각 지역의 Ru 합니다. 특정 영역에 RU를 선택적으로 할당할 수 없습니다. Cosmos 컨테이너 (또는 데이터베이스)에서 프로 비전 된 Ru Cosmos 계정과 연결 된 모든 지역에서 프로 비전 됩니다.

Cosmos 컨테이너를 사용 하 여 구성 된 가정 *'R'* Ru 되며 *' n '* 다음 Cosmos 계정과 연결 된 지역:

- Cosmos 계정 컨테이너에서 전역적으로 사용할 수 있는 총 Ru 단일 쓰기 지역으로 구성 된 경우 = *R* x *N*합니다.

- Cosmos 계정의 쓰기 지역 여러 컨테이너에서 전역적으로 사용할 수 있는 총 Ru 사용 하 여 구성 된 경우 = *R* x (*N*+ 1). 추가적인 *R* Ru는 자동으로 프로 비전 프로세스 업데이트 충돌을 방지 엔트로피 트래픽을 지역에서.

여러분이 [일관성 모델](consistency-levels.md) 처리량에도 영향을 줍니다. 더 관대 한 일관성 수준에 대 한 약 2 배 읽기 처리량을 얻을 수 있습니다 (예를 들어 *세션*를 *일관 된 접두사* 하 고 *최종* 일관성)에 비해 강력한 일관성 수준 (예를 들어 *제한 된 부실* 하거나 *강력한* 일관성).

## <a name="next-steps"></a>다음 단계

다음 컨테이너 또는 데이터베이스에서 처리량을 구성 하는 방법에 알아볼 수 있습니다.

* [컨테이너 및 데이터베이스에 대한 처리량 가져오기 및 설정](set-throughput.md) 

