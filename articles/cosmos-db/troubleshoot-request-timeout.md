---
title: Azure Cosmos DB 서비스 요청 시간 제한 예외 문제 해결
description: Azure Cosmos DB 서비스 요청 시간 제한 예외를 진단 하 고 해결 하는 방법을 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 76a1558534728613dcdedc78b64a0366f2bd643d
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871074"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Azure Cosmos DB 요청 시간 제한 예외 진단 및 문제 해결
Azure Cosmos DB HTTP 408 요청 시간 제한을 반환 했습니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 요청 시간 제한 예외에 대 한 알려진 원인과 해결 방법이 나와 있습니다.

### <a name="check-the-sla"></a>SLA 확인
[Azure Cosmos DB 모니터링](monitor-cosmos-db.md) 을 확인 하 여 408 예외의 수가 Azure Cosmos DB SLA를 위반 하는지 확인 합니다.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>해결 방법 1: Azure Cosmos DB SLA를 위반 하지 않음
응용 프로그램은이 시나리오를 처리 하 고 일시적인 오류를 다시 시도해 야 합니다.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>해결 방법 2: Azure Cosmos DB SLA를 위반 했습니다.
[Azure 지원](https://aka.ms/azure-support)에 문의 하세요.
 
### <a name="hot-partition-key"></a>핫 파티션 키
Azure Cosmos DB는 전체 프로 비전 된 처리량을 실제 파티션에 균등 하 게 분산 합니다. 핫 파티션이 있으면 실제 파티션에 있는 하나 이상의 논리 파티션 키가 모든 물리적 파티션의 요청 단위 (/초)를 사용 하는 것입니다. 동시에 다른 실제 파티션에 대 한 o s/s가 사용 되지 않습니다. 증상으로, 사용 되는 총 r u/s는 데이터베이스 또는 컨테이너에서 프로 비전 된 전체 r u/초 보다 낮습니다. 핫 논리 파티션 키에 대 한 요청에 대 한 제한 (429s)이 계속 표시 됩니다. 정규화 된 작업 부하 [메트릭을](monitor-normalized-request-units.md) 사용 하 여 작업에 핫 파티션이 발생 하는지 확인 합니다. 

#### <a name="solution"></a>해결 방법:
요청 볼륨과 저장소를 균등 하 게 분산 하는 좋은 파티션 키를 선택 합니다. [파티션 키를 변경](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)하는 방법에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용 하는 경우 문제를 [진단 하 고 해결](troubleshoot-dot-net-sdk.md) 합니다.
* [.Net v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.net v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.