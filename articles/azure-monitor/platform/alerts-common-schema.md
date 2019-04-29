---
title: Azure monitor 경고에 대 한 일반적인 경고 스키마
description: 하 고 사용 하도록 설정 하는 방법을 사용 해야 하는 이유, 일반적인 경고 스키마 이해
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: c18227a491478d0d8010761440a54fd088344b39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776598"
---
# <a name="common-alert-schema"></a>일반 경고 스키마

이 문서에서는 일반적인 경고 스키마 무엇 인지 설명 하 고 사용 하도록 설정 하는 방법을 사용할 때의 이점입니다.

## <a name="what-is-the-common-alert-schema"></a>일반 경고 스키마 란?

일반 경고 스키마는 현재 Azure에서 경고 알림에 대 한 사용 환경의 표준화합니다. 지금까지 자신의 전자 메일 템플릿, 웹 후크 스키마 등 세 가지 경고 유형 Azure에서 현재 (메트릭, 로그 및 활동 로그) 했습니다. 일반 경고 스키마를 사용 하 여 일관 된 스키마를 사용 하 여 경고 알림을 받을 수 있습니다.

모든 경고 인스턴스를 설명 **영향을 받은 리소스** 및 **경고를 발생 시킨**, 하며 이러한 인스턴스는 다음 섹션에서 일반적인 스키마에 설명 되어 있습니다.
* **Essentials**: 집합이 **필드를 표준화**설명 하는 모든 경고 유형에 대해 공통 **어떤 리소스** 경고는에 경고 추가 메타 데이터 (예: 심각도 또는 설명)이 일반적인와 함께 합니다. 
* **경고 컨텍스트**: 설명 하는 필드의 집합을 **경고의 원인인**, 달라 지는 필드를 사용 하 여 **경고 유형에 따라**합니다. 예를 들어, 있지만 활동 로그 경고는 경고를 생성 하는 이벤트에 대 한 정보는 메트릭 경고 메트릭 이름 및 경고 컨텍스트에 메트릭 값과 같은 필드 해야 합니다. 

고객의 일반적인 통합 시나리오 라우팅을 담당 팀에서 작업 시작 후 일부 피벗 (예: 리소스 그룹)에 따라 관련된 팀 경고 인스턴스가 포함 됩니다. 일반적인 경고 스키마를 사용 하 여 있습니다 수 표준화 라우팅 논리 경고 유형의 필수 필드를 활용 하 여 추가 조사를 위해 관련된 팀은 상황에 맞는 필드를 유지 합니다.

즉,은 수는 잠재적으로 더 적은 수의 통합 관리 및 유지 관리할 수 있으므로 _대부분_ 좀 더 간편 합니다. 또한 향후 경고 페이로드 강화 (예: 사용자 지정, 진단 보강 등)는 공통 스키마에서 화면만 합니다.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>일반 경고 스키마는 어떤 기능 향상이 가져온?

일반 경고 스키마 주로 스스로 매니페스트 경고 알림에 합니다. 표시 되는 향상 된 사항은 다음과 같습니다.

| 액션(Action) | 향상된 기능|
|:---|:---|
| sms | 모든 경고 유형에 대 한 일관 된 SMS 템플릿입니다. |
| Email | 일관적이 고 자세한 전자 메일 템플릿을 쉽게 한눈에 문제를 진단할 수 있습니다. 포털에 영향을 받는 리소스 경고 인스턴스에 포함 된 딥 링크 빠르게 재구성 프로세스로 이동할 수 있는지 확인 합니다. |
| 웹 후크/논리 앱/a z 함수/자동화 Runbook | 일관 된 JSON 구조 모든 경고 유형에 대해 여러 경고 유형의 통합을 쉽게 빌드할 수 있습니다. |

새 스키마 가까운 미래에 다양 한 경고 사용 환경을 Azure portal과 Azure 모바일 앱에서 사용할 수도 됩니다. 

[웹 후크/논리 앱/a z 함수/Automation Runbook에 대 한 스키마 정의 대해 알아봅니다.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> 다음 작업을 공통 경고 스키마를 지원 하지 않습니다. ITSM 커넥터입니다.

## <a name="how-do-i-enable-the-common-alert-schema"></a>일반 경고 스키마 사용 하도록 설정 하는 방법

옵트인 할 수도 있고 REST API 및 포털에서 작업 그룹을 통해 일반적인 경고 스키마를 옵트아웃할 수 있습니다. 새 스키마로 전환 하 여 토글을 작업 수준에서 존재 합니다. 예를 들어, 개별적으로 전자 메일 작업 및 웹 후크 동작을 옵트인 해야 합니다.

> [!NOTE]
> 1. 다음 경고 유형에 공통 스키마는 기본적으로 지원 (없습니다 선택할 필요).
>     * 스마트 감지 경고
> 1. 현재 다음 경고 유형에 공통 스키마를 지원 하지 않습니다.
>     * 생성 된 경고 [Vm에 대 한 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * 생성 된 경고 [Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Azure portal을 통해

![일반 경고 스키마 옵트인](media/alerts-common-schema/portal-opt-in.png)

1. 작업 그룹에서 기존 또는 새 작업을 엽니다. 
1. 표시 된 것 처럼 일반적인 경고 스키마를 사용 하도록 설정 하려면 설정/해제에 대 한 '예'를 선택 합니다.

### <a name="through-the-action-groups-rest-api"></a>REST API 작업 그룹을 통해

사용할 수도 있습니다는 [작업 그룹 API](https://docs.microsoft.com/rest/api/monitor/actiongroups) 일반적인 경고 스키마에 옵트인 합니다. 하면서 합니다 [만들기 또는 업데이트](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) REST API 호출을 플래그 "useCommonAlertSchema" (옵트인)을 ' true ' 또는 'f a l' (opt out) 하는 다음 작업 중 필요한-전자 메일/웹 후크/논리 앱/a z 함수/automation runbook에 대 한 설정할 수 있습니다.

예를 들어 다음 요청 본문에 대 한 합니다 [만들기 또는 업데이트](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) REST API는 다음을 수행 합니다.

* "John Doe의 전자 메일" 전자 메일 작업에 대 한 일반적인 경고 스키마를 사용 하도록 설정
* "Jane Smith의 전자 메일" 전자 메일 작업에 대 한 일반적인 경고 스키마를 사용 하지 않도록 설정
* "샘플 webhook" 웹 후크 작업에 대 한 일반적인 경고 스키마를 사용 하도록 설정

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>다음 단계

- [웹 후크/논리 앱/a z 함수/Automation Runbook에 대 한 공통 경고 스키마 정의입니다.](https://aka.ms/commonAlertSchemaDefinitions)



