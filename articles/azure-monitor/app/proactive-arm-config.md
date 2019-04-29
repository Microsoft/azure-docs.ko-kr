---
title: Azure Resource Manager 템플릿을 사용하여 Azure Application Insights 스마트 검색 규칙 설정 구성 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 Azure Application Insights 스마트 검색 규칙의 관리 및 구성 자동화
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 3ab50c92543615488d9ced599df433bf7e1e4061
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461564"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 Application Insights 스마트 검색 규칙 관리

[Azure Resource Manager 템플릿](../../azure-resource-manager/resource-group-authoring-templates.md)을 사용하여 Application Insights의 스마트 검색 규칙을 관리하고 구성할 수 있습니다.
이 방법은 Azure Resource Manager 자동화를 사용하여 새 Application Insights 리소스를 배포하거나 기존 리소스의 설정을 수정할 때 사용할 수 있습니다.

## <a name="smart-detection-rule-configuration"></a>스마트 검색 규칙 구성

스마트 검색 규칙에 대한 다음 설정을 구성할 수 있습니다.
- 규칙을 사용하도록 설정된 경우(기본값은 **true**.)
- 항목이 검색되었을 때 구독 소유자, 참가자 및 읽기 권한자에게 이메일을 보내야 하는 경우(기본값은 **true**.)
- 항목이 검색되었을 때 알림을 받아야 하는 추가 메일 수신인
- * _미리 보기_로 표시된 스마트 검색 규칙에는 이메일 구성을 사용할 수 없습니다.

Azure Resource Manager를 통해 규칙 설정을 구성할 수 있도록, 이제 스마트 검색 규칙 구성은 Application Insights 리소스 내부에서 **ProactiveDetectionConfigs**라고 하는 내부 리소스로 제공됩니다.
유연성을 극대화하기 위해 고유한 알림 설정을 사용하여 각 스마트 검색 규칙을 구성할 수 있습니다.

## <a name="examples"></a>예

아래는 Azure Resource Manager 템플릿을 사용하여 스마트 검색 규칙 설정을 구성하는 방법을 보여주는 몇 가지 예입니다.
모든 샘플은 _“myApplication”_ 이라는 Application Insights 리소스 및 내부에서 _“longdependencyduration”_ 이라고 부르는 "긴 종속성 기간 스마트 검색 규칙"을 참조합니다.
잊지 말고 Application Insights 리소스 이름을 바꾸고, 관련 스마트 검색 규칙 내부 이름을 지정하세요. 아래 표에서 각 스마트 검색 규칙에 해당하는 내부 Azure Resource Manager 이름 목록을 확인하세요.

### <a name="disable-a-smart-detection-rule"></a>스마트 검색 규칙 사용 안 함

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>스마트 검색 규칙에 이메일 전송 알림 사용 안 함

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>스마트 검색 규칙에 대한 추가 이메일 수신인 추가

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

## <a name="smart-detection-rule-names"></a>스마트 검색 규칙 이름

아래 표에는 포털에 나타나는 스마트 검색 규칙 이름과 Azure Resource Manager 템플릿에서 사용해야 하는 내부 이름이 정리되어 있습니다.

> [!NOTE]
> 미리 보기로 표시된 스마트 검색 규칙은 이메일 알림을 지원하지 않습니다. 따라서 이러한 규칙에 대해 활성화된 속성만 설정할 수 있습니다. 

| Azure Portal 규칙 이름 | 내부 이름
|:---|:---|
| 느린 페이지 로드 시간 | slowpageloadtime |
| 느린 서버 응답 시간 | slowserverresponsetime |
| 긴 종속성 기간 | longdependencyduration |
| 서버 응답 시간 저하 | degradationinserverresponsetime |
| 종속성 기간 저하 | degradationindependencyduration |
| 추적 심각도 비율의 저하(미리 보기) | extension_traceseveritydetector |
| 예외 볼륨의 비정상적인 증가(미리 보기) | extension_exceptionchangeextension |
| 잠재적인 메모리 누수 검색됨(미리 보기) | extension_memoryleakextension |
| 잠재적인 보안 문제 검색됨(미리 보기) | extension_securityextensionspackage |
| 리소스 사용률 문제 검색됨(미리 보기) | extension_resourceutilizationextensionspackage |

## <a name="who-receives-the-classic-alert-notifications"></a>누가 (클래식) 경고 알림을 받나요?

이 섹션은 스마트 검색 클래식 경고에만 적용되며, 원하는 수신자만 알림을 받도록 경고 알림을 최적화하는 데 도움이 됩니다. [클래식 경고](../platform/alerts-classic.overview.md)와 새 경고 환경 간의 차이점에 대해 자세히 알아보려면 [경고 개요 문서](../platform/alerts-overview.md)를 참조하세요. 현재 스마트 검색 경고는 클래식 경고 환경만 지원합니다. 한 가지 예외는 [Azure 클라우드 서비스의 스마트 감지 경고](./proactive-cloud-services.md)입니다. Azure 클라우드 서비스의 스마트 감지 경고에 대한 경고 알림을 제어하려면 [작업 그룹](../platform/action-groups.md)을 사용합니다.

* 스마트 검색/클래식 경고 알림에는 특정 수신자를 사용하는 것이 좋습니다.

* 스마트 검색 경고의 경우 **대량/그룹** 확인란 옵션을 사용하도록 설정하면 구독에서 소유자, 기여자 또는 읽기 권한자 역할의 사용자에게 경고가 전송됩니다. 실제로 구독의 Application Insights 리소스에 대한 액세스 권한이 있는 _모든_ 사용자가 범위에 포함되며 알림을 받습니다. 

> [!NOTE]
> 현재 **대량/그룹** 확인란 옵션을 사용하고 있고 사용 안 함으로 설정하는 경우에는 변경 내용을 되돌릴 수 없습니다.

## <a name="next-steps"></a>다음 단계

자동으로 감지하는 방법에 대해 자세히 알아봅니다.

- [오류 잘못된 부분](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [메모리 누수](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [성능 이상](../../azure-monitor/app/proactive-performance-diagnostics.md)