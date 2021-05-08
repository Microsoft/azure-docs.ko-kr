---
title: Azure Portal을 사용하여 Azure 서비스 알림에 대한 활동 로그 경고 수신하기
description: Azure Portal을 사용하여 서비스 상태 알림에 대한 활동 로그 경고 설정 방법 알아보기.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 48126d923cb0baa33058c6fd55e48f31d793fade
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570179"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 서비스 알림에 대한 활동 로그 경고 만들기
## <a name="overview"></a>개요

이 문서에서는 Azure Portal을 사용하여 서비스 상태 알림에 대한 활동 로그 경고를 설정하는 방법을 설명합니다.  

서비스 상태 알림은 [Azure 활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에 저장됩니다. 활동 로그에 저장된 정보의 양이 많은 점을 고려하여 서비스 상태 알림에 대한 경고를 보다 쉽게 확인하고 설정할 수 있도록 별도의 사용자 인터페이스가 있습니다. 

Azure에서 Azure 구독에 서비스 상태 알림을 전송할 때 경고를 받을 수 있습니다. 다음 항목에 따라 경고를 구성할 수 있습니다.

- 서비스 상태 알림 클래스(서비스 문제, 계획된 유지 보수, 상태 권고, 보안 권고)
- 영향을 받는 구독
- 영향을 받는 서비스
- 영향을 받는 하위 지역

> [!NOTE]
> 서비스 상태 알림은 리소스 상태 이벤트에 대한 경고를 보내지 않습니다.

다음과 같이 경고를 받는 사람도 구성할 수 있습니다.

- 기존 작업 그룹을 선택합니다.
- 새 작업 그룹을 만듭니다(향후 경고에 사용할 수 있음).

작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../azure-monitor/alerts/action-groups.md)를 참조하세요.

Azure 리소스 관리자 템플릿을 사용하여 서비스 상태 알림 경고를 구성하는 방법에 대한 자세한 내용은 [ 템플릿](../azure-monitor/alerts/alerts-activity-log.md)을 참조하세요.

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>첫 Azure Service Health 경고 설정에 관한 영상 보기

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>Azure Portal을 사용하여 Service Health 경고 만들기
1. [포털](https://portal.azure.com)에서 **서비스 상태** 를 선택합니다.

    !["서비스 상태" 서비스](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. **경고** 섹션에서 **상태 경고** 를 선택합니다.

    !["상태 경고" 탭](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. **서비스 상태 경고 만들기** 를 선택하고 필드를 입력합니다.

    !["서비스 상태 경고 만들기" 명령](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. 경고를 표시하려는 **구독**, **서비스** 및 **지역** 을 선택합니다.

    [![“활동 로그 경고 추가” 대화 상자](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>이 구독은 활동 로그 경고를 저장하는 데 사용됩니다. 경고 리소스가 이 구독에 배포되고 이에 대한 활동 로그에서 이벤트를 모니터링합니다.

5. 경고를 받을 **이벤트 형식**(*서비스 문제*, *계획된 유지 관리*, *상태 권고*, *보안 권고*)을 선택합니다.

6. **작업 그룹 선택** 을 클릭하여 기존 작업 그룹을 선택하거나 새 작업 그룹을 만듭니다. 작업 그룹에 대해 자세히 알아보려면 [Azure Portal에서 작업 그룹 만들기 및 관리](../azure-monitor/alerts/action-groups.md)를 참조하세요.


7. **경고 규칙 이름** 및 **설명** 을 입력하여 경고 세부 정보를 정의합니다.

8. 경고를 저장할 **리소스 그룹** 을 선택합니다.



몇 분 이내에 경고가 활성화되고 만들 때 지정한 조건에 따라 트리거를 시작합니다.

[기존 문제 관리 시스템에 대한 웹후크 알림 구성](service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다. 활동 로그 경고에 대한 웹후크 스키마 정보는 [Azure 활동 로그 경고에 대한 웹후크](../azure-monitor/alerts/activity-log-alerts-webhook.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계
- [Azure Service Health 경고 설정 모범 사례](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)에 대해 알아봅니다.
- [Azure Service Health에 대한 모바일 푸시 알림을 설정](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)하는 방법을 알아봅니다.
- [기존 문제 관리 시스템에 대한 웹후크 알림 구성](service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다.
- [서비스 상태 알림](service-notifications.md)에 대해 자세히 알아보세요.
- [알림 속도 제한](../azure-monitor/alerts/alerts-rate-limiting.md)에 대해 자세히 알아보세요.
- [활동 로그 경고 웹후크 스키마](../azure-monitor/alerts/activity-log-alerts-webhook.md)를 검토하세요.
- [활동 로그 경고의 개요](../azure-monitor/alerts/alerts-overview.md)를 확인하고 경고를 받는 방법에 대해 알아보세요.
- [작업 그룹](../azure-monitor/alerts/action-groups.md)에 대해 자세히 알아보세요.
