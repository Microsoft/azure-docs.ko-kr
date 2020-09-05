---
title: 서비스를 사용할 수 없는 예외 Azure Cosmos DB 문제 해결
description: Azure Cosmos DB 서비스를 사용할 수 없는 예외를 진단 하 고 해결 하는 방법을 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 749592b778612c6903c9c15e336de3fb00978199
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870870"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>서비스를 사용할 수 없는 예외 Azure Cosmos DB 진단 및 문제 해결
SDK가 Azure Cosmos DB에 연결할 수 없습니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 서비스를 사용할 수 없는 예외에 대 한 알려진 원인과 해결 방법이 나와 있습니다.

### <a name="the-required-ports-are-being-blocked"></a>필요한 포트가 차단 되 고 있습니다.
[필요한 포트](performance-tips-dotnet-sdk-v3-sql.md#networking) 를 모두 사용 하도록 설정 했는지 확인 합니다.

### <a name="client-side-transient-connectivity-issues"></a>클라이언트 쪽 임시 연결 문제
서비스를 사용할 수 없는 예외는 시간 초과를 유발 하는 일시적인 연결 문제가 있는 경우에 발생할 수 있습니다. 일반적으로이 시나리오와 관련 된 스택 추적에는 오류가 포함 됩니다 `TransportException` . 예를 들면

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

[요청 시간 제한 문제 해결 단계](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) 에 따라 문제를 해결 합니다.

### <a name="service-outage"></a>서비스 중단
[Azure 상태](https://status.azure.com/status) 를 확인 하 여 진행 중인 문제가 있는지 확인 합니다.


## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용 하는 경우 문제를 [진단 하 고 해결](troubleshoot-dot-net-sdk.md) 합니다.
* [.Net v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.net v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.