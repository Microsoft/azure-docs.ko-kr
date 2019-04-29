---
title: Azure Monitor 로그-Azure Logic Apps를 사용 하 여 B2B 메시지 모니터링 | Microsoft Docs
description: AS2, x12 및 EDIFACT 메시지 통합 계정 및 Azure Logic Apps에 대 한 모니터링 및 Azure Monitor 로그를 사용 하 여 진단 로깅 설정
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: 12799a308157c3c0e19de1f82c0fe3df44fad37e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106303"
---
# <a name="monitor-b2b-messages-with-azure-monitor-logs-in-azure-logic-apps"></a>Azure Logic Apps에서 Azure Monitor 로그를 사용 하 여 B2B 메시지 모니터링

통합 계정에서 거래 업체 간에 B2B 통신을 설정한 후 해당 업체는 서로 메시지를 교환할 수 있습니다. 이 통신이 예상 대로 작동, AS2, X12를 모니터링할 수 있습니다 및 EDIFACT 메시지를 사용 하 여 통합 계정에 대해 진단 로깅을 설정 검사할 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md)합니다. 이 서비스는 클라우드 및 온-프레미스 환경을 모니터링하고 해당 가용성 및 성능을 유지할 수 있고 런타임 세부 정보 및 보다 다양한 디버깅에 대한 이벤트를 수집합니다. 또한 Azure Storage 및 Azure Event Hub와 같은 기타 서비스를 통해 이 데이터를 사용할 수도 있습니다.

> [!NOTE]
> 이 페이지는 [2019년 1월에 사용 중지](../azure-monitor/platform/oms-portal-transition.md)되지만 가능한 경우 Azure Log Analytics로 이러한 단계를 대체하는 Microsoft OMS(Operations Management Suite)를 아직 참조할 수 있습니다. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>필수 조건

* 진단 로깅과 함께 설정된 논리 앱. [논리 앱을 만드는 방법](quickstart-create-first-logic-app-workflow.md) 및 [해당 논리 앱에 대한 로깅을 설정하는 방법](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)을 알아봅니다.

* 이전 요구 사항을 충족 하면, Log Analytics 작업 영역을 모니터링 하 고 Azure Monitor 로그를 통해 B2B 통신 추적에 사용할 수 있는 해야 합니다. Log Analytics 작업 영역이 없는 경우 [Log Analytics 작업 영역을 만드는 방법](../azure-monitor/learn/quick-create-workspace.md)을 알아봅니다.

* 논리 앱에 연결된 통합 계정. [논리 앱에 대한 링크와 함께 통합 계정을 만드는 방법](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)을 알아봅니다.

## <a name="turn-on-diagnostics-logging"></a>진단 로깅 켜기

통합 계정에서 직접 또는 [Azure Monitor 서비스를 통해](#azure-monitor-service) 로깅을 설정할 수 있습니다. Azure Monitor는 인프라 수준의 데이터와 함께 기본 모니터링을 제공합니다. [Azure Monitor](../azure-monitor/overview.md)에 대해 자세히 알아봅니다.

### <a name="turn-on-logging-from-integration-account"></a>통합 계정에서 로깅 켜기

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다. **모니터링** 아래에서 **진단 설정**을 선택합니다.

   ![통합 계정을 찾아 선택하고 "진단 설정" 선택](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. 이제 통합 계정을 찾아 선택합니다. 필터 목록에서 통합 계정에 적용되는 값을 선택합니다.
완료되면 **진단 설정 추가**를 선택합니다.

   | 자산 | 값 | 설명 | 
   |----------|-------|-------------|
   | **구독** | <*Azure-subscription-name*> | 사용자 통합 계정과 연결된 Azure 구독 | 
   | **리소스 그룹** | <*Azure-resource-group-name*> | 통합 계정에 대한 Azure 리소스 그룹 | 
   | **리소스 종류** | **통합 계정** | 로깅을 켜려는 Azure 리소스의 유형 | 
   | **리소스** | <*integration-account-name*> | 로깅을 켜려는 Azure 리소스의 이름 | 
   ||||  

   예를 들면 다음과 같습니다.

   ![통합 계정에 대한 진단 설정](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. 새 진단 설정에 대한 이름을 제공하고 Log Analytics 작업 영역 및 로그하려는 데이터를 선택합니다.

   1. **Log Analytics에 보내기**를 선택합니다. 

   1. **Log Analytics** 아래에서 **구성**을 선택합니다. 

   1. **OMS 작업 영역**에서 로깅에 사용하려는 Log Analytics 작업 영역을 선택합니다. 

      > [!NOTE]
      > OMS 작업 영역을 Log Analytics 작업 영역으로 대체합니다. 

   1. **로그** 아래에서 **IntegrationAccountTrackingEvents** 범주 및 **저장**을 차례로 선택합니다.

   예를 들면 다음과 같습니다. 

   ![진단 데이터 로그를 보낼 수 있도록 Azure Monitor 로그 설정](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. 이제 [Azure Monitor 로그에서 B2B 메시지에 대 한 추적 설정](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)합니다.

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Azure Monitor를 통해 로깅 켜기

1. [Azure Portal](https://portal.azure.com)의 기본 Azure 메뉴에서 **모니터링**을 선택합니다. **설정** 아래에서 **진단 설정**을 선택합니다. 

   !["모니터" > "진단 설정" > 통합 계정 선택](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. 이제 통합 계정을 찾아 선택합니다. 필터 목록에서 통합 계정에 적용되는 값을 선택합니다.
완료되면 **진단 설정 추가**를 선택합니다.

   | 자산 | 값 | 설명 | 
   |----------|-------|-------------|
   | **구독** | <*Azure-subscription-name*> | 사용자 통합 계정과 연결된 Azure 구독 | 
   | **리소스 그룹** | <*Azure-resource-group-name*> | 통합 계정에 대한 Azure 리소스 그룹 | 
   | **리소스 종류** | **통합 계정** | 로깅을 켜려는 Azure 리소스의 유형 | 
   | **리소스** | <*integration-account-name*> | 로깅을 켜려는 Azure 리소스의 이름 | 
   ||||  

   예를 들면 다음과 같습니다.

   ![통합 계정에 대한 진단 설정](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. 새 진단 설정에 대한 이름을 제공하고 Log Analytics 작업 영역 및 로그하려는 데이터를 선택합니다.

   1. **Log Analytics에 보내기**를 선택합니다. 

   1. **Log Analytics** 아래에서 **구성**을 선택합니다. 

   1. **OMS 작업 영역**에서 로깅에 사용하려는 Log Analytics 작업 영역을 선택합니다. 

      > [!NOTE]
      > OMS 작업 영역을 Log Analytics 작업 영역으로 대체합니다. 

   1. **로그** 아래에서 **IntegrationAccountTrackingEvents** 범주 및 **저장**을 차례로 선택합니다.

   예를 들면 다음과 같습니다. 

   ![진단 데이터 로그를 보낼 수 있도록 Azure Monitor 로그 설정](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. 이제 [Azure Monitor 로그에서 B2B 메시지에 대 한 추적 설정](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)합니다.

## <a name="use-diagnostic-data-with-other-services"></a>다른 서비스를 통해 진단 데이터 사용

Azure Monitor 로그와 함께 사용법 논리 앱의 진단 데이터의 다른 Azure 서비스를 사용 하 여 예를 들어 확장할 수 있습니다. 

* [Azure Storage에 Azure 진단 로그 보관](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Azure Event Hubs로 Azure 진단 로그 스트림](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

그런 다음 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 및 [Power BI](../azure-monitor/platform/powerbi.md)와 같은 다른 서비스의 원격 분석 및 분석을 사용하여 실시간으로 모니터링할 수 있습니다. 예를 들면 다음과 같습니다.

* [Event Hub에서 Stream Analytics로 데이터 스트림](../stream-analytics/stream-analytics-define-inputs.md)
* [Stream Analytics를 사용하여 스트리밍 데이터 분석 및 Power BI에서 실시간 분석 대시보드 만들기](../stream-analytics/stream-analytics-power-bi-dashboard.md)

설정하려는 옵션에 따라 먼저 [Azure 저장소 계정을 만들](../storage/common/storage-create-storage-account.md)거나 [Azure 이벤트 허브를 만들](../event-hubs/event-hubs-create.md)어야 합니다. 그런 다음, 진단 데이터를 전송하려는 대상을 선택할 수 있습니다.
보존 기간은 저장소 계정을 사용하도록 선택한 경우에만 적용됩니다.

![Azure 저장소 계정 또는 이벤트 허브로 데이터 보내기](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>지원되는 추적 스키마

Azure는 이러한 추적 스키마 형식을 지원하며 사용자 지정 유형을 제외한 고정된 스키마를 포함합니다.

* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 추적 스키마](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그에서 B2B 메시지 추적](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Azure Monitor 로그에서 B2B 메시지 추적")
* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")

