---
title: Azure Front 도어 서비스에서 메트릭 및 로그 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure Front 도어 서비스에서 지 원하는 다양 한 메트릭 및 액세스 로그에 대해 설명 합니다.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: e379e67fb733c968a755afd245d079239f559c89
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751407"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Azure Front 도어 서비스에서 메트릭 및 로그 모니터링

Azure Front 도어 서비스를 사용 하 여 다음과 같은 방법으로 리소스를 모니터링할 수 있습니다.

- **메트릭**. Azure 전면 도어에는 현재 성능 카운터를 볼 수 있는 7 개의 메트릭이 있습니다.
- **로그**. 활동 및 진단 로그를 사용 하면 모니터링 목적으로 리소스에서 성능, 액세스 및 기타 데이터를 저장 하거나 사용할 수 있습니다.

### <a name="metrics"></a>메트릭

메트릭은 포털에서 성능 카운터를 볼 수 있는 특정 Azure 리소스에 대한 기능입니다. 다음은 사용 가능한 전방 도어 메트릭입니다.

| 메트릭 | 메트릭 표시 이름 | 단위 | 차원 | Description |
| --- | --- | --- | --- | --- |
| RequestCount | 요청 수 | 카운트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 제공하는 클라이언트 요청 수  |
| RequestSize | 요청 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 클라이언트에서 Front Door로, 요청으로 전송된 바이트 수 |
| ResponseSize | 응답 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 클라이언트로, 응답으로 전송된 바이트 수 |
| TotalLatency | 총 대기 시간 | 밀리초 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 프런트 도어에서 클라이언트가 마지막 응답 바이트를 승인할 때까지 Front 도어가 받은 클라이언트 요청에서 계산 된 시간입니다. |
| BackendRequestCount | 백 엔드 요청 수 | 카운트 | HttpStatus</br>HttpStatusGroup</br>백 엔드 | Front Door에서 백 엔드로 전송된 요청 수 |
| BackendRequestLatency | 백 엔드 요청 대기 시간 | 밀리초 | 백 엔드 | Front Door에서 백 엔드의 마지막 응답 바이트를 받을 때까지 Front Door에서 백 엔드로 요청이 전송될 때 계산된 시간 |
| BackendHealthPercentage | 백 엔드 상태 비율 | 백분율 | 백 엔드</br>BackendPool | Front Door에서 백 엔드로 성공한 상태 프로브의 비율 |
| WebApplicationFirewallRequestCount | 웹 애플리케이션 방화벽 요청 수 | 카운트 | PolicyName</br>RuleName</br>실행력 | Front Door의 애플리케이션 계층 보안에 의해 처리된 클라이언트 요청 수 |

## <a name="activity-log"></a>활동 로그

활동 로그는 프런트 도어 서비스에서 수행 된 작업에 대한 정보를 제공 합니다. 또한 Front 도어 서비스에서 수행 되는 모든 쓰기 작업 (put, post 또는 delete)을 결정 합니다.

>[!NOTE]
>활동 로그에는 읽기 (get) 작업이 포함 되지 않습니다. 또한 Azure Portal 또는 원래 관리 API를 사용 하 여 수행 하는 작업을 포함 하지 않습니다.

Azure Monitor에서 Azure 리소스의 모든 로그 또는 Front 도어 서비스의 활동 로그에 액세스 합니다. 활동 로그를 보려면

1. 프런트 도어 인스턴스를 선택 합니다.
2. **활동 로그**를 선택 합니다.

    ![활동 로그](./media/front-door-diagnostics/activity-log.png)

3. 필터링 범위를 선택한 다음 **적용**을 선택 합니다.

## <a name="diagnostic-logging"></a>진단 로그
진단 로그는 감사 및 문제 해결에 중요 한 작업 및 오류에 대한 다양 한 정보를 제공 합니다. 진단 로그는 활동 로그와 다릅니다.

활동 로그는 Azure 리소스에서 수행 된 작업에 대한 통찰력을 제공 합니다. 진단 로그는 리소스에서 수행한 작업에 대한 통찰력을 제공 합니다. 자세한 내용은 [Azure Monitor 진단 로그](../azure-monitor/platform/platform-logs-overview.md)를 참조 하세요.

![진단 로그](./media/front-door-diagnostics/diagnostic-log.png)

프런트 도어 서비스에 대한 진단 로그를 구성 하려면:

1. Azure Front 도어 서비스를 선택 합니다.

2. **진단 설정**을 선택 합니다.

3. **진단 켜기**를 선택합니다. 진단 로그를 메트릭과 함께 저장소 계정에 보관 하거나, 이벤트 허브로 스트림 하거나 Azure Monitor 로그에 보냅니다.

현재 Front 도어 서비스는 진단 로그 (일괄 처리 시간)를 제공 합니다. 진단 로그는 각 항목에 대해 다음 스키마를 갖는 개별 API 요청을 제공 합니다.

| 속성  | Description |
| ------------- | ------------- |
| ClientIp | 요청한 클라이언트의 IP 주소입니다. |
| ClientPort | 요청을 수행한 클라이언트의 IP 포트입니다. |
| HttpMethod | 요청에서 사용된 HTTP 메서드 |
| HttpStatusCode | 프록시에서 반환 된 HTTP 상태 코드입니다. |
| HttpStatusDetails | 요청에 대한 결과 상태입니다. 이 문자열 값의 의미는 상태 참조 테이블에서 찾을 수 있습니다. |
| HttpVersion | 요청 또는 연결의 유형입니다. |
| RequestBytes | 요청 헤더 및 요청 본문을 포함 하 여 HTTP 요청 메시지의 크기 (바이트)입니다. |
| RequestUri | 받은 요청의 URI |
| ResponseBytes | 백 엔드 서버에서 응답으로 보낸 바이트 수입니다.  |
| RoutingRuleName | 요청이 일치 하는 라우팅 규칙의 이름입니다. |
| SecurityProtocol | 요청에 사용 되는 TLS/SSL 프로토콜 버전 이거나, 암호화가 없는 경우 null입니다. |
| TimeTaken | 작업에 걸린 시간 (밀리초)입니다. |
| UserAgent | 클라이언트에서 사용 하는 브라우저 유형입니다. |
| TrackingReference | 프런트 도어에서 제공 하는 요청을 식별 하는 고유한 참조 문자열로, 클라이언트에 대한 X-y 헤더로도 전송 됩니다. 특정 요청에 대한 액세스 로그에서 세부 정보를 검색 하는 데 필요 합니다. |

## <a name="next-steps"></a>다음 단계

- [프런트 도어 프로필 만들기](quickstart-create-front-door.md)
- [프런트 도어 작동 방법](front-door-routing-architecture.md)
