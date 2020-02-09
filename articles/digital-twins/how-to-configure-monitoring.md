---
title: 모니터링을 구성 하는 방법-Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins의 모니터링 및 로깅 옵션을 구성 하는 방법에 대해 알아봅니다.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: e35e18be20af3bd9f6fdc9541f9abfe857a6b87c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511861"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Azure Digital Twins에서 모니터링을 구성하는 방법

Azure Digital Twins는 강력한 로깅, 모니터링 및 분석을 지원합니다. 솔루션 개발자는 Azure Monitor 로그, 진단 로그, 활동 로깅 및 기타 서비스를 사용 하 여 IoT 앱의 복잡 한 모니터링 요구를 지원할 수 있습니다. 로깅 옵션을 결합하여 여러 서비스의 레코드를 쿼리 또는 표시하고 여러 서비스에 대한 세밀한 로깅 범위를 제공할 수 있습니다.

이 문서에서는 로깅 및 모니터링 옵션을 요약하고 Azure Digital Twins에 한정된 방식으로 로깅 및 모니터링 옵션을 결합하는 방법을 설명합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>활동 로그 검토

Azure [활동 로그](../azure-monitor/platform/platform-logs-overview.md)는 각 Azure 서비스 인스턴스의 구독 수준 이벤트 및 작업 기록에 대한 인사이트를 신속하게 제공합니다.

구독 수준 이벤트는 다음과 같습니다.

* 리소스 만들기
* 리소스 제거
* 앱 비밀 만들기
* 다른 서비스와 통합

Azure Digital Twins에 대한 활동 로깅이 기본적으로 사용되며 다음 방법을 사용하여 Azure Portal을 통해 확인할 수 있습니다.

1. Azure Digital Twins 인스턴스를 선택합니다.
1. **활동 로그**를 선택하여 디스플레이 패널을 불러옵니다.

    [활동 로그 ![](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

고급 활동 로깅의 경우:

1. **로그** 옵션을 선택하여 **활동 로그 분석 개요**를 선택합니다.

    [![선택](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. **활동 로그 분석 개요**는 필수 활동 로그 데이터를 요약합니다.

    [![활동 로그 분석 개요]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>**활동 로그**를 사용하여 구독 수준 이벤트에 대한 인사이트를 신속하게 얻을 수 있습니다.

## <a name="enable-customer-diagnostic-logs"></a>고객 진단 로그 사용

각 Azure 인스턴스에 대한 [진단 설정](../azure-monitor/platform/platform-logs-overview.md)을 설정하여 작업 로깅을 보완할 수 있습니다. 활동 로그는 구독 수준 이벤트와 관련이 있지만, 진단 로깅은 리소스 자체의 작업 기록에 대한 인사이트를 제공합니다.

다음은 진단 로깅의 예입니다.

* 사용자 정의 함수의 실행 시간
* 성공적인 API 요청의 응답 상태 코드
* 키 자격 증명 모음에서 앱 키 검색

인스턴스에 진단 로그를 사용하려면:

1. Azure Portal에서 리소스를 불러옵니다.
1. **진단 설정**선택:

    [![진단 설정 1](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. **진단 켜기** 를 선택 하 여 데이터를 수집 합니다 (이전에는 사용 하도록 설정 하지 않은 경우).
1. 요청된 필드에 정보를 입력하고 데이터를 저장할 방법 위치를 선택합니다.

    [진단 설정 2 ![](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    진단 로그는 [Azure File Storage](../storage/files/storage-files-deployment-guide.md) 를 사용 하 여 저장 되 고 [Azure Monitor 로그](../azure-monitor/log-query/get-started-portal.md)와 공유 되는 경우가 많습니다. 두 옵션 모두 선택할 수 있습니다.

>[!TIP]
>**진단 로그**를 사용하여 리소스 작업에 대한 인사이트를 살펴봅니다.

## <a name="azure-monitor-and-log-analytics"></a>Azure 모니터 및 로그 분석

IoT 애플리케이션은 서로 다른 리소스, 디바이스, 위치 및 데이터를 하나로 통합합니다. 세밀한 로깅은 전체 애플리케이션 아키텍처의 각 부분, 서비스 또는 구성 요소에 대한 자세한 정보를 제공하지만, 유지 관리 및 디버깅을 위해 통합 개요가 필요한 경우가 많습니다.

Azure Monitor에는 한 위치에서 로깅 원본을 보고 분석할 수 있도록 하는 강력한 log analytics 서비스가 포함 되어 있습니다. 따라서 Azure Monitor는 복잡한 IoT 앱 내에서 로그를 분석하는 데 매우 유용합니다.

다음은 사용 사례입니다.

* 여러 진단 로그 기록 쿼리
* 여러 사용자 정의 함수의 로그 검토
* 특정 시간 프레임 내에서 두 개 이상의 서비스에 대한 로그 표시

전체 로그 쿼리는 [Azure Monitor 로그](../azure-monitor/log-query/log-query-overview.md)를 통해 제공 됩니다. 이러한 강력한 기능을 설정하려면:

1. Azure Portal에서 **Log Analytics**를 검색합니다.
1. 사용 가능한 **Log Analytics 작업 영역** 인스턴스가 표시 됩니다. 그 중 하나를 선택하고 쿼리할 **로그**를 선택합니다.

    [Log analytics ![](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. **Log Analytics 작업 영역** 인스턴스가 아직 없는 경우 **추가** 단추를 선택 하 여 작업 영역을 만들 수 있습니다.

    [OMS ![만들기](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

**Log Analytics 작업 영역** 인스턴스가 프로 비전 되 면 강력한 쿼리를 사용 하 여 여러 로그에서 항목을 찾거나 **로그 관리**를 사용 하 여 특정 기준을 사용 하 여 검색할 수 있습니다.

   [![로그 관리](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

강력한 쿼리 작업에 대한 자세한 내용은 [쿼리 시작](../azure-monitor/log-query/get-started-queries.md)하기를 참조 하세요.

> [!NOTE]
> **Log Analytics 작업 영역** 에 처음으로 이벤트를 보낼 때 5 분의 지연이 발생할 수 있습니다.

또한 Azure Monitor 로그는 **진단 및 문제 해결**을 선택 하 여 볼 수 있는 강력한 오류 및 경고 알림 서비스를 제공 합니다.

   [![경고 및 오류 알림](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>**Log Analytics 작업 영역** 을 사용 하 여 여러 앱 기능, 구독 또는 서비스에 대한 로그 기록을 쿼리할 수 있습니다.

## <a name="other-options"></a>기타 옵션

Azure Digital Twins는 애플리케이션별 로깅 및 보안 감사도 지원합니다. Azure Digital Twins 인스턴스에 사용할 수 있는 모든 Azure 로깅 옵션에 대한 자세한 개요는 [azure 로그 감사](../security/fundamentals/log-audit.md) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- Azure [활동 로그](../azure-monitor/platform/platform-logs-overview.md)에 대해 자세히 알아보세요.

- [진단 로그 개요](../azure-monitor/platform/platform-logs-overview.md)를 읽고 Azure 진단 설정에 대해 자세히 알아보세요.

- [Azure Monitor 로그](../azure-monitor/log-query/get-started-portal.md)에 대해 자세히 알아보세요.
