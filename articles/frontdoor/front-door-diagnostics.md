---
title: 메트릭 및 Azure 프런트 도어 서비스에서 로그를 모니터링 합니다. | Microsoft Docs
description: 이 문서에서는 다양 한 메트릭과 Azure 프런트 도어 서비스가 지 원하는 액세스 로그에 설명
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
ms.openlocfilehash: 16770ea0a320b3d9f081cc21a102ab050a6467f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736811"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>메트릭 및 Azure 프런트 도어 서비스에서 로그를 모니터링합니다.

Azure 프런트 도어 서비스를 사용 하면 다음과 같은 방법으로 리소스를 모니터링할 수 있습니다.

- **메트릭**. 현재 Application Gateway는 성능 카운터를 보여주는 7개 메트릭을 제공합니다.
- **로그**합니다. 활동 및 진단 로그는 성능, 액세스 및 기타 데이터를 저장 하거나 모니터링을 위해 리소스에서 사용할 수 있습니다.

### <a name="metrics"></a>메트릭

메트릭은 포털에서 성능 카운터를 볼 수 있도록 하는 특정 Azure 리소스에 대 한 기능입니다. 다음은 첫 번째 관문 메트릭을 사용할 수 있습니다.

| 메트릭 | 메트릭 표시 이름 | 단위 | 차원 | 설명 |
| --- | --- | --- | --- | --- |
| RequestCount | 요청 수 | 카운트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 제공하는 클라이언트 요청 수  |
| RequestSize | 요청 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 클라이언트에서 Front Door로, 요청으로 전송된 바이트 수 |
| ResponseSize | 응답 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 클라이언트로, 응답으로 전송된 바이트 수 |
| TotalLatency | 총 대기 시간 | 밀리초 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 클라이언트는 첫 번째 관문에서 마지막 응답 바이트를 승인할 때까지 첫 번째 관문에서 받은 클라이언트 요청의 시간 계산 합니다. |
| BackendRequestCount | 백 엔드 요청 수 | 카운트 | HttpStatus</br>HttpStatusGroup</br>백 엔드 | Front Door에서 백 엔드로 전송된 요청 수 |
| BackendRequestLatency | 백 엔드 요청 대기 시간 | 밀리초 | 백 엔드 | Front Door에서 백 엔드의 마지막 응답 바이트를 받을 때까지 Front Door에서 백 엔드로 요청이 전송될 때 계산된 시간 |
| BackendHealthPercentage | 백 엔드 상태 비율 | 백분율 | 백 엔드</br>BackendPool | Front Door에서 백 엔드로 성공한 상태 프로브의 비율 |
| WebApplicationFirewallRequestCount | 웹 애플리케이션 방화벽 요청 수 | 카운트 | PolicyName</br>RuleName</br>액션(Action) | Front Door의 애플리케이션 계층 보안에 의해 처리된 클라이언트 요청 수 |

## <a name="activity-log"></a>활동 로그

활동 로그 프런트 도어 서비스에서 수행 된 작업에 대 한 정보를 제공 합니다. 또한 결정 합니다 누가, 및에 대 한 쓰기 작업 (put, post 또는 delete) 프런트 도어 서비스에서 수행 된 시기입니다.

>[!NOTE]
>활동 로그는 읽기 (get) 작업을 포함 하지 않습니다. 또한 이러한 Azure portal 또는 원래 관리 API를 사용 하 여 수행할 작업을 포함 하지 않습니다.

액세스 활동 프런트 도어 서비스 또는 Azure Monitor에서 Azure 리소스의 모든 로그를 기록합니다. 활동 로그를 보려면

1. 첫 번째 관문 인스턴스를 선택 합니다.
2. 선택 **활동 로그**합니다.

    ![활동 로그](./media/front-door-diagnostics/activity-log.png)

3. 필터링 된 범위를 선택 하 고 선택한 **적용**합니다.

## <a name="diagnostic-logging"></a>진단 로그
진단 로그는 작업 및 감사 및 문제해결에 대 한 중요 한 오류에 대 한 풍부한 정보를 제공 합니다. 진단 로그는 활동 로그에서 다릅니다.

활동 로그는 Azure 리소스에서 수행 된 작업에 대 한 정보를 제공 합니다. 진단 로그는 리소스에서 수행 하는 작업에 대 한 정보를 제공 합니다. 자세한 내용은 [Azure Monitor 진단 로그](../azure-monitor/platform/diagnostic-logs-overview.md)합니다.

![진단 로그](./media/front-door-diagnostics/diagnostic-log.png)

첫 번째 관문 서비스에 대 한 진단 로그를 구성:

1. Azure 프런트 도어 서비스를 선택 합니다.

2. 선택할 **진단 설정**합니다.

3. **진단 켜기**를 선택합니다. 저장소 계정에 대 한 메트릭과 함께 진단 로그 보관 하 고, 이벤트 허브로 스트림 또는 Azure Monitor 로그로 보낼 합니다.

첫 번째 관문 서비스는 현재 진단 로그 (시간 단위로 일괄 처리)를 제공 합니다. 진단 로그는 다음 스키마를 갖는 각 항목으로 개별 API 요청을 제공 합니다.

| 자산  | 설명 |
| ------------- | ------------- |
| ClientIp | 요청한 클라이언트의 IP 주소입니다. |
| ClientPort | 요청한 클라이언트의 IP 포트입니다. |
| HttpMethod | 요청에서 사용된 HTTP 메서드 |
| HttpStatusCode | 프록시에서 반환 된 HTTP 상태 코드입니다. |
| HttpStatusDetails | 요청에 대 한 상태 결과입니다. 상태 참조 테이블에서이 문자열 값의 의미를 찾을 수 있습니다. |
| HttpVersion | 요청 또는 연결의 형식입니다. |
| RequestBytes | 크기 (바이트)을 요청 헤더 및 요청 본문을 포함 하 여 HTTP 요청 메시지입니다. |
| RequestUri | 받은 요청의 URI |
| ResponseBytes | 응답으로 백 엔드 서버에서 보낸 바이트 수입니다.  |
| RoutingRuleName | 요청 일치 하는 라우팅 규칙의 이름입니다. |
| SecurityProtocol | 암호화 되지 않은 경우 null을 요청에서 사용 하는 TLS/SSL 프로토콜 버전입니다. |
| TimeTaken | 시간 (밀리초)에 해당 작업에 걸린 시간의 길이입니다. |
| UserAgent | 클라이언트에서 사용한 브라우저 종류 |
| TrackingReference | 첫 번째 관문에서 제공한 요청을 식별 하는 고유한 참조 문자열 Azure Xref 헤더로 클라이언트로 전송 합니다. 특정 요청에 대 한 액세스 로그에 정보를 검색 하는 데 필요 합니다. |

## <a name="next-steps"></a>다음 단계

- [첫 번째 관문 프로필 만들기](quickstart-create-front-door.md)
- [첫 번째 관문의 작동 원리](front-door-routing-architecture.md)
