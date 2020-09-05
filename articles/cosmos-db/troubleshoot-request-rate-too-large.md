---
title: Azure Cosmos DB 요청 속도로 너무 많은 예외 문제 해결
description: 요청 률을 진단 하 고 수정 하는 방법에 대해 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f8b1129c94ecf80efb60a13a0b80b1cc1817ff3f
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871091"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>진단 및 문제 해결 Azure Cosmos DB 요청 비율이 너무 큼 예외
"요청 비율이 너무 큼" 메시지 또는 오류 코드 429은 요청을 제한 하 고 있음을 나타냅니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 섹션에는 너무 많은 요청에 대 한 알려진 원인 및 솔루션이 포함 되어 있습니다.

### <a name="check-the-metrics"></a>메트릭 확인
[Azure Cosmos DB 모니터링](monitor-cosmos-db.md) 을 확인 하 여 429 예외의 수를 확인 합니다.

#### <a name="cause"></a>원인:
사용 된 처리량 (초당 요청 단위)이 [프로 비전 된 처리량](set-throughput.md)을 초과 했습니다. SDK는 지정 된 다시 시도 정책에 따라 요청을 자동으로 다시 시도 합니다. 이 오류가 자주 발생 하는 경우 컬렉션에 대 한 처리량을 높이는 것이 좋습니다. 포털의 메트릭을 확인 하 여 429 오류가 발생 하 고 있는지 확인 합니다. 파티션 키를 검토 하 여 [저장소 및 요청 볼륨의 균등 한 배포가](partition-data.md)발생 하는지 확인 합니다.

#### <a name="solution"></a>해결 방법:
1. 프로 비전 된 처리량을 늘리려면 [포털 또는 SDK](set-throughput.md) 를 사용 합니다.
1. [자동 크기 조정을](provision-throughput-autoscale.md)위해 데이터베이스 또는 컨테이너를 전환 합니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용 하는 경우 문제를 [진단 하 고 해결](troubleshoot-dot-net-sdk.md) 합니다.
* [.Net v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.net v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.