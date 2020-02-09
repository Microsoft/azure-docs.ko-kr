---
title: Azure Cosmos DB에서 처리량 크기 조정
description: 이 문서에서는 Azure Cosmos 계정이 프로 비전 되는 여러 지역에 걸쳐 처리량을 확장 하 Azure Cosmos DB는 방법을 설명 합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873508"
---
# <a name="globally-scale-provisioned-throughput"></a>전역적으로 프로비전된 처리량 크기 조정 

Azure Cosmos DB에서 프로 비전 된 처리량은 요청 단위/초 (r u/초 또는 복수 형식 RUs)로 표시 됩니다. RU는 다음 이미지에서 표시된 대로 Cosmos 컨테이너에 대한 읽기 및 쓰기 작업의 비용을 모두 측정합니다.

![요청 단위](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Cosmos 컨테이너 또는 Cosmos 데이터베이스에서 RU를 프로비전할 수 있습니다. 컨테이너에 프로 비전 된 RUs는 해당 컨테이너에서 수행 되는 작업에만 사용할 수 있습니다. 데이터베이스에서 프로비전된 RU는 데이터베이스 내의 모든 컨테이너에서 공유됩니다(독점적으로 할당된 RU가 있는 모든 컨테이너 제외).

탄력적으로 크기 조정 프로 비전 된 처리량의 경우 언제 든 지 프로 비전 된 r u/초를 늘리거나 줄일 수 있습니다. 자세한 내용은 [How to 프로 비전 하는 방법](set-throughput.md) 및 탄력적으로 scale Cosmos 컨테이너 및 데이터베이스를 참조 하세요. 전체적으로 크기를 조정 하기 위해 언제 든 지 Cosmos 계정에서 지역을 추가 하거나 제거할 수 있습니다. 자세한 내용은 [데이터베이스 계정에서 Azure 지역 추가/제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account)를 참조하세요. 여러 영역을 Cosmos 계정에 연결 하는 것은 전 세계에서 낮은 대기 시간과 [고가용성](high-availability.md) 을 얻기 위해 중요 합니다.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>프로비전된 처리량을 영역에 분산하는 방법

Cosmos 컨테이너 (또는 데이터베이스 Cosmos DB)에 *' r '* rus를 프로 비전 하는 경우 Cosmos 계정에 연결 된 *각* 지역에서 *' r '* rus를 사용할 수 있도록 합니다. 계정에 새 지역을 추가할 때마다 새로 추가 된 지역에서 *' R '* RUs를 자동으로 프로 비전 Cosmos DB. Cosmos 컨테이너에 대해 수행 되는 작업은 각 지역에서 *' R '* RUs를 가져오도록 보장 됩니다. 특정 영역에 RU를 선택적으로 할당할 수 없습니다. Cosmos 컨테이너 (또는 데이터베이스)에 프로 비전 된 RUs는 Cosmos 계정과 연결 된 모든 지역에 프로 비전 됩니다.

Cosmos 컨테이너가 *' R '* RUs로 구성 되어 있고 Cosmos 계정과 연결 된 *' N '* 지역이 있다고 가정 하면 다음을 수행 합니다.

- Cosmos 계정이 단일 쓰기 지역으로 구성 된 경우 전체 RUs는 컨테이너에서 전역적으로 사용할 수 있습니다 = *R* x *N*.

- Cosmos 계정이 여러 쓰기 지역으로 구성 된 경우 컨테이너에서 전역적으로 사용할 수 있는 총 RUs = *R* x (*N*+ 1). 추가 *R* RUs는 지역 전체에서 업데이트 충돌 및 앤티 엔트로피 트래픽을 처리 하도록 자동으로 프로 비전 됩니다.

[일관성 모델](consistency-levels.md) 을 선택 하는 것도 처리량에 영향을 줍니다. 더 강력한 일관성 수준 (예: *제한 된 부실* 또는 *강력한* 일관성)과 비교 하 여 더 완화 된 일관성 수준 (예: *세션*, *일관 된 접두사* 및 *최종* 일관성)에 대해 약 2x 읽기 처리량을 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음으로 컨테이너 또는 데이터베이스에 대한 처리량을 구성 하는 방법을 배울 수 있습니다.

* [컨테이너 및 데이터베이스에 대한 처리량 가져오기 및 설정](set-throughput.md) 

