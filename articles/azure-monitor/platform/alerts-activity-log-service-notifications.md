---
title: Azure 서비스 알림에 대한 활동 로그 경고 수신
description: Azure 서비스가 발생할 때 SMS, 전자 메일 또는 웹후크를 통해 알림을 받습니다.
author: shawntabrizi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: shtabriz
ms.openlocfilehash: 6b617ff99b9183c6b5008292dd3e5d11f2e69da9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776907"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>서비스 알림에 대한 활동 로그 경고 만들기
## <a name="overview"></a>개요
이 문서에서는 Azure Portal을 사용하여 서비스 상태 알림에 대한 활동 로그 경고를 설정하는 방법을 보여 줍니다.  

Azure에서 Azure 구독에 서비스 상태 알림을 전송할 때 경고를 받을 수 있습니다. 다음 항목에 따라 경고를 구성할 수 있습니다.

- 서비스 상태 알림 클래스(서비스 문제, 계획된 유지 보수, 상태 자문)
- 영향을 받는 구독
- 영향을 받는 서비스
- 영향을 받는 하위 지역

> [!NOTE]
> 서비스 상태 알림은 리소스 상태 이벤트에 대한 경고를 전송하지 않습니다.

다음과 같이 경고를 받는 사람도 구성할 수 있습니다.

- 기존 작업 그룹을 선택합니다.
- 새 작업 그룹을 만듭니다(향후 경고에 사용할 수 있음).

작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../../azure-monitor/platform/action-groups.md)를 참조하세요.

Azure 리소스 관리자 템플릿을 사용하여 서비스 상태 알림 경고를 구성하는 방법에 대한 자세한 내용은 [ 템플릿](alerts-activity-log.md)을 참조하세요.

## <a name="alert-and-new-action-group-using-azure-portal"></a>Azure Portal을 사용하여 경고 및 새 작업 그룹
1. [포털](https://portal.azure.com)에서 **서비스 상태**를 선택합니다.

    !["서비스 상태" 서비스](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. **경고** 섹션에서 **상태 경고**를 선택합니다.

    !["상태 경고" 탭](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. **서비스 상태 경고 만들기**를 선택하고 필드를 입력합니다.

    !["서비스 상태 경고 만들기" 명령](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. 경고를 받을 **구독**, **서비스** 및 **영역**을 선택합니다.

    ![“활동 로그 경고 추가” 대화 상자](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> 이 구독은 활동 로그 경고를 저장하는 데 사용됩니다. 경고 리소스가 이 구독에 배포되고 이에 대한 활동 로그에서 이벤트를 모니터링합니다.

1. 다음 중에 경고를 표시할 **이벤트 유형**을 선택합니다. ‘서비스 문제’, ‘계획된 유지 관리’ 및 ‘상태 공지’ 

1. **경고 규칙 이름** 및 **설명**을 입력하여 경고 세부 정보를 정의합니다.

1. 경고를 저장할 **리소스 그룹**을 선택합니다.

1. **새 작업 그룹**을 선택하여 새 작업 그룹을 만듭니다. **작업 그룹 이름** 상자에 이름을 입력하고 **짧은 이름** 상자에 이름을 입력합니다. 약식 이름은 이 경고가 발생할 때 전송된 알림에서 참조됩니다.

    ![새 작업 그룹 만들기](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. 받는 사람에 대한 다음 항목을 제공하여 받는 사람 목록을 정의합니다.

    a. **이름**: 받는 사람의 이름, 별칭 또는 식별자를 입력합니다.

    b. **작업 유형**: SMS, 메일, 웹후크, Azure 앱 등을 선택합니다.

    다. **세부 정보**: 선택한 작업 유형에 따라 전화 번호, 메일 주소, 웹후크 URI 등을 입력합니다.

1. **확인**을 선택하여 작업 그룹을 만든 다음, **경고 규칙 만들기**를 선택하여 경고를 완성합니다.

몇 분 이내에 경고가 활성화되고 만들 때 지정한 조건에 따라 트리거를 시작합니다.

[기존 문제 관리 시스템에 대한 웹후크 알림 구성](../../service-health/service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다. 활동 로그 경고에 대한 웹후크 스키마 정보는 [Azure 활동 로그 경고에 대한 웹후크](../../azure-monitor/platform/activity-log-alerts-webhook.md)를 참조하세요.

>[!NOTE]
>이러한 단계에서 정의한 작업 그룹은 향후의 모든 경고 정의에 대해 기존 작업 그룹으로 다시 사용할 수 있습니다.
>
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Azure Portal을 사용하여 기존 작업 그룹에 경고

1. 이전 섹션의 1-7단계를 수행하여 서비스 상태 알림을 만듭니다. 

1. **작업 그룹 정의** 아래에서 **작업 그룹 선택** 단추를 클릭합니다. 적절한 작업 그룹을 선택합니다.

1. **추가**를 선택하여 작업 그룹을 추가한 다음, **경고 규칙 만들기**를 선택하여 경고를 완성합니다.

몇 분 이내에 경고가 활성화되고 만들 때 지정한 조건에 따라 트리거를 시작합니다.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 경고 및 새 작업 그룹

다음은 이메일 대상을 사용하여 작업 그룹을 만들고 대상 구독에 모든 서비스 상태 알림을 사용하도록 설정하는 예제입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>경고 관리

경고를 만들면 **모니터**의 **경고** 섹션에 표시됩니다. 관리하려는 경고를 선택합니다.

* 편집합니다.
* 삭제합니다.
* 해당 경고에 대한 알림 수신을 일시적으로 중지하거나 다시 시작하려면 사용 안 함 또는 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계
- [기존 문제 관리 시스템에 대한 웹후크 알림 구성](../../service-health/service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다.
- [서비스 상태 알림](../../azure-monitor/platform/service-notifications.md)에 대해 자세히 알아보세요.
- [알림 속도 제한](../../azure-monitor/platform/alerts-rate-limiting.md)에 대해 자세히 알아보세요.
- [활동 로그 경고 웹후크 스키마](../../azure-monitor/platform/activity-log-alerts-webhook.md)를 검토하세요.
- [활동 로그 경고의 개요](../../azure-monitor/platform/alerts-overview.md)를 확인하고 경고를 받는 방법에 대해 알아보세요. 
- [작업 그룹](../../azure-monitor/platform/action-groups.md)에 대해 자세히 알아보세요.
