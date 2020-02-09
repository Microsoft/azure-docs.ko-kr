---
title: Azure 응용 프로그램 게이트웨이 리소스 상태 개요
description: 이 문서는 Azure Application Gateway에 대한 리소스 상태 기능의 개요
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659503"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Azure 응용 프로그램 게이트웨이 리소스 상태 개요

[Azure Resource Health](../service-health/resource-health-overview.md) 진단 및 Azure 서비스 문제가 리소스에 영향을 줍니다 때 지원을 받을 수 있습니다. 리소스의 현재 및 과거 상태를 알려줍니다. 또한 문제를 완화하는 데 도움이 되는 기술 지원을 제공합니다.

Application Gateway에 대한 Resource Health는 것이 정상 상태 인지 여부를 평가 하는 게이트웨이의에서 내보낸 신호에 의존 합니다. 게이트웨이가 정상이 아니면 경우 Resource Health는 문제의 원인을 파악 하기 추가 정보를 분석 합니다. 또한 Microsoft에서 수행 되는 작업이 나 수행할 수 있는 문제를 해결 하려면 식별 합니다.

상태가 평가되는 방식에 대한 자세한 내용은 [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)에서 리소스 유형 및 상태 검사에 대한 전체 목록을 확인하세요.


Application Gateway에 대한 상태는 다음 상태 중 하나로 표시 됩니다.

## <a name="available"></a>사용 가능

**사용 가능한** 상태 서비스는 리소스의 상태에 영향을 주는 어떠한 이벤트도 감지 되지 않은 것을 의미 합니다. 표시는 **최근에 해결 됨** 게이트웨이 마지막 24 시간 동안 계획 되지 않은 가동 중지 시간에서 복구 된 경우에는 알림입니다.

![사용 가능한 상태입니다.](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>사용할 수 없음

**사용할 수 없습니다** 상태 서비스는 진행 중인 플랫폼 또는 게이트웨이의 상태에 영향을 주는 비-플랫폼 이벤트를 감지 합니다.을 의미 합니다.

### <a name="platform-events"></a>플랫폼 이벤트

플랫폼 이벤트는 Azure 인프라의 여러 구성 요소에 의해 트리거됩니다. 여기에는 예정된 작업(예: 계획된 유지 관리)과 예기치 않은 인시던트(예: 계획에 없는 호스트 재부팅)가 모두 포함됩니다.

Resource Health에는 이벤트 및 복구 프로세스에 대한 추가 세부 정보가 제공됩니다. 또한 Microsoft 지원 계약이 유효하지 않은 경우에도 지원 담당자에게 문의할 수 있습니다.

![사용할 수 없는 상태](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>알 수 없음

합니다 **알 수 없는** 상태가 리소스 상태를 나타내며 게이트웨이에 대한 정보를 10 분 넘게 수신 하지. 이 상태는 게이트웨이의 상태 선언적 표시가 없습니다. 하지만 문제 해결 프로세스의 중요 한 데이터 요소입니다.

상태 변경 게이트웨이가 예상 대로 실행 중이면 **사용 가능한** 몇 분 후입니다.

문제가 발생 하는 경우는 **알 수 없는** 상태는 플랫폼에서 이벤트에 영향을 주지 게이트웨이 제안 수 있습니다.

![알 수 없는 상태](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>성능 저하됨

합니다 **Degraded** 상태 게이트웨이 성능에서 저하가 감지 되었음을 사용량에 대한 계속 사용할 수 있지만 나타냅니다.

![Degrated 상태](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>다음 단계

Application Gateway 웹 응용 프로그램 방화벽 (WAF) 문제를 해결 하는 방법에 대한 자세한 참조 [문제 해결 웹 응용 프로그램 방화벽 (WAF) Azure Application Gateway에 대한](web-application-firewall-troubleshoot.md)합니다.