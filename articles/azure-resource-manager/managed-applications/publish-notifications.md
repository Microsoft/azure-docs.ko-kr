---
title: 알림이 포함 된 관리 되는 앱
description: 관리 되는 응용 프로그램 인스턴스의 생성, 업데이트, 삭제 및 오류에 대 한 알림을 수신 하도록 webhook 엔드포인트을 사용 하 여 관리 되는 응용 프로그램을 구성 합니다.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715675"
---
# <a name="azure-managed-applications-with-notifications"></a>알림을 사용 하는 Azure 관리 되는 응용 프로그램

Azure 관리 되는 응용 프로그램 알림을 통해 게시자는 관리 되는 응용 프로그램 인스턴스의 수명 주기 이벤트에 따라 작업을 자동화할 수 있습니다. 게시자는 사용자 지정 알림 웹 후크 엔드포인트을 지정 하 여 신규 및 기존 관리 되는 응용 프로그램 인스턴스에 대 한 이벤트 알림을 받을 수 있습니다. 게시자는 응용 프로그램 프로 비전, 업데이트 및 삭제 시 사용자 지정 워크플로를 설정할 수 있습니다.

## <a name="getting-started"></a>시작
관리 되는 응용 프로그램 수신을 시작 하려면 공용 HTTPS 엔드포인트을 실행 하 고 서비스 카탈로그 응용 프로그램 정의 또는 Azure Marketplace 제안을 게시할 때 지정 합니다.

신속 하 게 시작 하는 데 권장 되는 단계는 다음과 같습니다.
1. 들어오는 POST 요청을 기록 하 고 `200 OK`을 반환 하는 공용 HTTPS 엔드포인트을 실행 합니다.
2. 이 문서의 뒷부분에 설명 된 대로 서비스 카탈로그 응용 프로그램 정의 또는 Azure Marketplace 제품에 엔드포인트을 추가 합니다.
3. 응용 프로그램 정의 또는 Azure Marketplace 제품을 참조 하는 관리 되는 응용 프로그램 인스턴스를 만듭니다.
4. 알림이 수신 중인지 확인 합니다.
5. 이 문서의 **엔드포인트 인증** 섹션에 설명 된 대로 권한 부여를 사용 하도록 설정 합니다.
6. 이 문서의 **알림 스키마** 섹션에 설명 된 지침에 따라 알림 요청을 구문 분석 하 고 알림을 바탕으로 비즈니스 논리를 구현 합니다.

## <a name="add-service-catalog-application-definition-notifications"></a>서비스 카탈로그 응용 프로그램 정의 알림 추가
#### <a name="azure-portal"></a>Azure portal
시작 하려면 [Azure Portal을 통해 서비스 카탈로그 응용 프로그램 게시](./publish-portal.md)를 참조 하세요.

![Azure Portal의 서비스 카탈로그 응용 프로그램 정의 알림](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> 현재 응용 프로그램 정의 속성의 `notificationEndpoints`에는 엔드포인트을 하나만 제공할 수 있습니다.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Azure Marketplace 관리 되는 응용 프로그램 알림 추가
자세한 내용은 [Azure 응용 프로그램 제품 만들기](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)를 참조 하세요.

![Azure Portal에서 관리 되는 응용 프로그램 알림 Azure Marketplace](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>이벤트 트리거
다음 표에서는 EventType 및 ProvisioningState와 해당 트리거의 가능한 모든 조합을 설명 합니다.

EventType | ProvisioningState | 알림에 대 한 트리거
---|---|---
PUT | 수락됨 | 관리 되는 리소스 그룹을 만들고 응용 프로그램을 넣은 후 (관리 되는 리소스 그룹 내 배포를 시작 하기 전에) 성공적으로 프로젝션 했습니다.
PUT | 성공 | PUT 후에 관리 되는 응용 프로그램의 전체 프로 비전이 성공 했습니다.
PUT | 실패 | 어떤 지점에서 든 응용 프로그램 인스턴스 프로 비전의 실패.
패치 | 성공 | 관리 되는 응용 프로그램 인스턴스에 성공적으로 패치 한 후 태그, JIT 액세스 정책 또는 관리 id를 업데이트 합니다.
Delete | 삭제 중 | 사용자가 관리 되는 앱 인스턴스의 삭제를 시작 하는 즉시
Delete | Deleted | 관리 되는 응용 프로그램을 모두 삭제 하 고 성공적으로 삭제 한 후
Delete | 실패 | 프로 비전 해제 프로세스 중에 삭제를 차단 하는 오류가 발생 한 후
## <a name="notification-schema"></a>알림 스키마
알림을 처리 하기 위해 webhook 엔드포인트을 실행 하는 경우 페이로드를 구문 분석 하 여 중요 한 속성을 가져온 다음 알림에 대 한 동작을 수행 해야 합니다. 서비스 카탈로그와 Azure Marketplace 관리 되는 응용 프로그램 알림은 동일한 속성을 많이 제공 합니다. 샘플 다음에 나오는 표에는 두 가지 작은 차이점이 설명 되어 있습니다.

#### <a name="service-catalog-application-notification-schema"></a>서비스 카탈로그 응용 프로그램 알림 스키마
관리 되는 응용 프로그램 인스턴스를 성공적으로 프로 비전 한 후의 샘플 서비스 카탈로그 알림은 다음과 같습니다.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

프로 비전이 실패 하면 오류 세부 정보가 포함 된 알림이 지정 된 엔드포인트으로 전송 됩니다.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Azure Marketplace 응용 프로그램 알림 스키마

관리 되는 응용 프로그램 인스턴스를 성공적으로 프로 비전 한 후의 샘플 서비스 카탈로그 알림은 다음과 같습니다.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

프로 비전이 실패 하면 오류 세부 정보가 포함 된 알림이 지정 된 엔드포인트으로 전송 됩니다.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

매개 변수 | 설명
---|---
eventType | 알림을 트리거한 이벤트의 유형입니다. (예: PUT, PATCH, DELETE)
applicationId | 알림이 트리거된 관리 되는 응용 프로그램의 정규화 된 리소스 식별자입니다.
eventTime | 알림을 트리거한 이벤트의 타임 스탬프입니다. (UTC ISO 8601 형식의 날짜 및 시간)
provisioningState | 관리 되는 응용 프로그램 인스턴스의 프로 비전 상태입니다. (예: 성공, 실패, 삭제, 삭제)
error | *ProvisioningState가 실패 한 경우에만 지정*됩니다. 오류 코드, 메시지 및 오류의 원인이 되는 문제에 대 한 세부 정보를 포함 합니다.
applicationDefinitionId | *서비스 카탈로그 관리 되는 응용 프로그램에만 지정*됩니다. 관리 되는 응용 프로그램 인스턴스가 프로 비전 된 응용 프로그램 정의의 정규화 된 리소스 식별자를 나타냅니다.
계획 | *관리 되는 응용 프로그램 Azure Marketplace에만 지정*됩니다. 관리 되는 응용 프로그램 인스턴스의 게시자, 제품, SKU 및 버전을 나타냅니다.
billingDetails | *관리 되는 응용 프로그램 Azure Marketplace에만 지정 됩니다.* 관리 되는 응용 프로그램 인스턴스의 청구 정보입니다. 사용 세부 정보에 대 한 Azure Marketplace를 쿼리 하는 데 사용할 수 있는 resourceUsageId을 포함 합니다.

## <a name="endpoint-authentication"></a>엔드포인트 인증
Webhook 엔드포인트의 보안을 유지 하 고 알림의 신뢰성을 확인 하려면 다음을 수행 합니다.
1. 웹 후크 URI 위에 다음과 같이 쿼리 매개 변수를 제공 합니다 (예: https\:. 각 알림을 사용 하 여 쿼리 매개 변수 `sig`에 필요한 값 `Guid`있는지 확인 합니다.
2. ApplicationId를 사용 하 여 관리 되는 응용 프로그램 인스턴스에서 GET을 실행 합니다. 일관성을 유지 하기 위해 provisioningState이 알림의 provisioningState 일치 하는지 확인 합니다.

## <a name="notification-retries"></a>알림 다시 시도

관리 되는 응용 프로그램 알림 서비스에는 웹 후크 엔드포인트에서 알림에 대 한 `200 OK` 응답이 필요 합니다. 웹 후크 엔드포인트이 500 보다 크거나 같은 HTTP 오류 코드를 반환 하거나, 429 오류 코드를 반환 하는 경우 또는 엔드포인트에 일시적으로 연결할 수 없는 경우 알림 서비스가 다시 시도 됩니다. 웹 후크 엔드포인트을 10 시간 이내에 사용할 수 없게 되 면 알림 메시지가 삭제 되 고 다시 시도가 중지 됩니다.
