---
title: OpsGenie를 사용하여 Azure 서비스 상태 경고 구성 | Microsoft Docs
description: 서비스 상태 이벤트에 대한 개인 설정 알림을 OpsGenie 인스턴스로 가져옵니다.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.workload: Supportability
ms.date: 11/14/2017
ms.openlocfilehash: 79a77fff206831c0f9b3bb73ad33f951d99e2c81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782211"
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>OpsGenie를 사용하여 서비스 상태 경고 구성

이 문서에서는 웹후크를 사용하여 OpsGenie로 Azure 서비스 상태 경고를 설정하는 방법을 보여 줍니다. [OpsGenie](https://www.opsgenie.com/)의 Azure 서비스 상태 통합을 사용하여 OpsGenie에 Azure 서비스 상태 경고를 전달할 수 있습니다. OpsGenie는 경고를 승인하거나 닫을 때까지 통화 일정과 메일, SMS(문자 메시지), 전화 통화, iOS 및 Android 푸시 알림 사용 및 경고 에스컬레이션을 기반으로 적절한 통보 대상을 결정할 수 있습니다.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>OpsGenie에서 서비스 상태 통합 URL 만들기
1.  [OpsGenie](https://www.opsgenie.com/) 계정에 등록하고 로그인했는지 확인합니다.

1.  OpsGenie에서 **통합** 섹션으로 이동합니다.

    ![OpsGenie에서 "통합" 섹션](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  **Azure 서비스 상태** 통합 단추를 선택합니다.

    ![OpsGenie에서 "Azure 서비스 상태 단추"](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  경고의 **이름**을 지정하고 **팀에 할당됨** 필드를 지정합니다.

1.  **받는 사람**, **사용** 및 **알림 표시 안 함**과 같은 다른 필드를 작성합니다.

1.  끝에 `apiKey`가 이미 추가된 **통합 URL**을 복사 및 저장합니다.

    ![OpsGenie에서 "통합 URL"](./media/webhook-alerts/opsgenie-integration-url.png)

1.  **통합 저장**을 선택합니다.

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Azure Portal에서 OpsGenie를 사용하여 경고 만들기
### <a name="for-a-new-action-group"></a>새 작업 그룹의 경우:
1. [Azure Portal을 사용하여 새 작업 그룹에 대한 서비스 상태 알림의 경고 만들기](../azure-monitor/platform/alerts-activity-log-service-notifications.md)에서 1단계부터 8단계까지 수행합니다.

1. **작업** 목록에서 다음을 정의합니다.

    a. **작업 유형:** *웹후크*

    b. **세부 정보:** 이전에 저장한 OpsGenie **통합 URL**입니다.

    다. **이름:** 웹후크의 이름, 별칭 또는 식별자입니다.

1. 경고 만들기가 완료되면 **저장**을 선택합니다.

### <a name="for-an-existing-action-group"></a>기존 작업 그룹의 경우:
1. [Azure Portal](https://portal.azure.com/)에서 **모니터**를 선택합니다.

1. **설정** 섹션에서 **작업 그룹**을 선택합니다.

1. 편집하려는 작업 그룹을 찾아 선택합니다.

1. **작업** 목록에 다음을 추가합니다.

    a. **작업 유형:** *웹후크*

    b. **세부 정보:** 이전에 저장한 OpsGenie **통합 URL**입니다.

    다. **이름:** 웹후크의 이름, 별칭 또는 식별자입니다.

1. 작업 그룹 업데이트가 완료되면 **저장**을 선택합니다.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 요청을 통해 웹후크 통합 테스트
1. 보낼 서비스 상태 페이로드를 만듭니다. [Azure 활동 로그 경고에 대한 웹후크](../azure-monitor/platform/activity-log-alerts-webhook.md)에서 예제 서비스 상태 웹후크 페이로드를 찾을 수 있습니다.

1. 다음과 같이 HTTP POST 요청을 만듭니다.

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. 상태 메시지 "성공"과 함께 `200 OK` 응답이 표시됩니다.

1. [OpsGenie](https://www.opsgenie.com/)로 이동하여 통합이 성공적으로 설정되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
- [기존 문제 관리 시스템에 대한 웹후크 알림 구성](service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다.
- [활동 로그 경고 웹후크 스키마](../azure-monitor/platform/activity-log-alerts-webhook.md)를 검토하세요. 
- [서비스 상태 알림](../azure-monitor/platform/service-notifications.md)에 대해 자세히 알아보세요.
- [작업 그룹](../azure-monitor/platform/action-groups.md)에 대해 자세히 알아보세요.