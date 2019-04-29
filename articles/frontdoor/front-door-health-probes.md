---
title: Azure Front Door Service - 백 엔드 상태 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure Front Door Service가 백엔드 상태를 모니터링하는 방법을 이해할 수 있게 도와드립니다
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 59a3bac39437b91eeee3b005bd23476a34a308b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736584"
---
# <a name="health-probes"></a>상태 프로브

각 백 엔드의 상태를 확인하기 위해 각 Front Door 환경은 각각의 구성된 백 엔드로 가상 HTTP/HTTPS 요청을 주기적으로 보냅니다. 그런 다음, Front Door는 이러한 프로브의 응답을 사용하여 실제 클라이언트 요청을 라우팅해야 하는 “최상”의 백 엔드를 확인합니다.


## <a name="supported-protocols"></a>지원되는 프로토콜

Front Door는 HTTP 또는 HTTPS 프로토콜을 통한 프로브 전송을 지원합니다. 이러한 프로브는 라우팅 클라이언트 요청에 대해 구성된 동일한 TCP 포트를 통해 전송되며, 재정의할 수 없습니다.

## <a name="health-probe-responses"></a>상태 프로브 응답

| 응답  | 설명 | 
| ------------- | ------------- |
| 상태 확인  |  200 정상 상태 코드는 백 엔드가 정상임을 나타냅니다. 그 외 코드는 모두 실패로 간주됩니다. 어떠한 이유로(네트워크 오류를 포함) 유효한 HTTP 응답이 프로브에 대해 수신되지 않으면 프로브가 실패로 계산됩니다.|
| 대기 시간 측정  | 대기 시간은 응답의 마지막 바이트를 수신하는 순간 프로브 요청을 보내기 직전에 측정된 벽시계 시간입니다. 각 요청에 대한 새 TCP 연결을 사용하므로 이 측정값은 기존 웜 연결을 사용하는 백 엔드에 편향되지 않습니다.  |

## <a name="how-front-door-determines-backend-health"></a>Front Door가 백 엔드 상태를 결정하는 방법

Azure Front Door Service는 상태를 확인하기 위해 모든 알고리즘에서 아래와 동일한 3단계 프로세스를 사용합니다.

1. 비활성화된 백 엔드를 제외합니다.

2. 상태 프로브 오류가 있는 백 엔드를 제외합니다.
    * 이 선택은 마지막 _n_ 상태 프로브 응답을 확인하여 완료합니다. 최소한 _x_가 정상적인 경우 백 엔드는 정상 상태로 간주됩니다.

    * _n_은 부하 분산 설정의 SampleSize 속성을 변경하여 구성됩니다.

    * _x_는 부하 분산 설정의 SuccessfulSamplesRequired 속성을 변경하여 구성됩니다.

3. 백 엔드 풀의 정상 백 엔드 외에, Front Door는 각 백 엔드에 대한 대기 시간(왕복 시간)을 추가로 측정하고 유지합니다.


## <a name="complete-health-probe-failure"></a>상태 프로브 실패 완료

상태 프로브가 백 엔드 풀의 모든 백 엔드에 대해 실패한 경우, Front Door는 모든 백 엔드를 정상으로 간주하고 모든 백 엔드에 걸친 라운드 로빈 배포에 트래픽을 라우팅합니다.

백 엔드가 정상 상태를 반환하고 나면, Front Door는 정상 부하 분산 알고리즘을 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
