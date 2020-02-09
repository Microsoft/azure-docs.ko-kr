---
title: Azure Cosmos DB 다중 마스터 혜택
description: Azure Cosmos DB에서 다중 마스터의 이점, 단일 및 여러 쓰기 위치의 대기 시간 및 SLA 요구 사항 비교를 이해 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872012"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Azure Cosmos DB의 다중 마스터 이점 이해

Cosmos DB account operators는 응용 프로그램에 대한 대기 시간, 가용성 및 RTO 요구 사항을 보장 하기 위해 적절 한 전역 배포 구성을 선택 해야 합니다. 여러 쓰기 위치를 사용 하 여 구성 된 Azure Cosmos 계정은 단일 쓰기 위치 (99.999% 쓰기 가용성 < SLA)를 포함 하는 계정에 비해 많은 이점을 제공 합니다.

## <a name="comparison-of-features"></a>기능 비교

|애플리케이션 요구 사항|여러 쓰기 위치|단일 쓰기 위치|참고|
|---|---|---|---|
|P 99에서 < 10ms의 쓰기 대기 시간 SLA|**예**|아닙니다.|쓰기 위치가 하나인 계정은 각 쓰기에 대해 지역 간 네트워크 대기 시간이 추가로 발생 합니다.|
|P 99의 < 10ms에 대한 읽기 대기 시간 SLA|**예**|yes| |
|99.999%의 쓰기 SLA|**예**|아닙니다.|단일 쓰기 위치의 계정에 99.99%의 SLA가 보장 됩니다.|
|RTO = 0|**예**|아닙니다.|지역 재해가 발생 한 경우 쓰기에 대한 제로 다운 시간입니다. 단일 쓰기 위치의 계정에는 15 분의 RTO가 있습니다.|

## <a name="next-steps"></a>다음 단계

Azure Cosmos 계정에서 EnableMultipleWriteLocations를 사용 하지 않도록 설정 하려면 [지원 티켓을 여세요](https://azure.microsoft.com/support/create-ticket/).
