---
title: Azure Monitor 로그-Azure Logic Apps에서에서 B2B 메시지 추적 쿼리를 만들 | Microsoft Docs
description: Azure Logic Apps용 Azure Log Analytics에서 AS2, X12 및 EDIFACT 메시지를 추적하는 쿼리 만들기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: d4a94e75de34bbafd3bc8f1c1a0d1a6817245e5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846624"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Azure Logic Apps에 대 한 Azure Monitor 로그에서 B2B 메시지 추적 쿼리 만들기

메시지 찾기 위해 AS2, X12 또는 EDIFACT를 사용 하 여 추적 중인 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md), 특정 조건에 따라 작업을 필터링 하는 쿼리를 만들 수 있습니다. 예를 들어 특정 교환 컨트롤 번호에 따라 메시지를 찾을 수 있습니다.

> [!NOTE]
> 이 페이지에서는 [2019년 1월에 사용 중지](../azure-monitor/platform/oms-portal-transition.md)되고 대신 Azure Log Analytics로 해당 단계를 대체하는 Microsoft OMS(Operations Management Suite)를 사용하여 이러한 작업을 수행하는 방법에 대한 단계를 설명했습니다. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>필수 조건

* 진단 로깅과 함께 설정된 논리 앱. [논리 앱을 만드는 방법](quickstart-create-first-logic-app-workflow.md) 및 [해당 논리 앱에 대한 로깅을 설정하는 방법](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)을 알아봅니다.

* 모니터링 및 로깅을 사용하여 설정된 통합 계정. [통합 계정을 만드는 방법](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 및 [해당 계정에 대한 모니터링 및 로깅을 설정하는 방법](../logic-apps/logic-apps-monitor-b2b-message.md)을 알아봅니다.

* 이미 않았다면 [Azure Monitor 로그에 진단 데이터를 게시](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) 하 고 [Azure Monitor 로그에서 메시지 추적을 설정](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)합니다.

## <a name="create-queries-with-filters"></a>필터를 사용하여 쿼리 만들기

특정 속성 또는 값에 따라 메시지를 찾으려면 필터를 사용하는 쿼리를 만들 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 **모든 서비스**를 선택합니다. 검색 상자에서 "로그 분석"을 찾고 **Log Analytics**를 선택합니다.

   ![Log Analytics 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. **Log Analytics** 아래에서 Log Analytics 작업 영역을 찾고 선택합니다. 

   ![Log Analytics 작업 영역 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. 작업 영역 메뉴의 **일반**에서 **로그(클래식)** 또는 **로그**를 선택합니다. 

   이 예제에서는 클래식 로그 보기의 사용 방법을 보여 줍니다. 
   **Log Analytics 환경 최대화** 섹션에서 **로그 보기**를 선택하면 **로그 검색 및 분석**에 **로그(클래식 보기)** 가 표시됩니다. 

   ![클래식 로그 보기](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. 쿼리 편집 상자에 찾으려는 필드 이름을 입력합니다. 입력을 시작하면 쿼리 편집기에 사용할 수 있는 가능한 일치 및 작업이 표시됩니다. 쿼리를 만든 후에 **실행**을 선택하거나 Enter 키를 누릅니다.

   이 예제에서는 **LogicAppB2B**의 일치 항목을 검색합니다. 
   에 대해 자세히 알아보세요 [Azure Monitor 로그에서 데이터를 찾는 방법](../log-analytics/log-analytics-log-searches.md)합니다.

   ![쿼리 문자열 입력 시작](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. 보려는 시간 범위를 변경하려면 왼쪽 창의 기간 목록에서 선택하거나 슬라이더를 끕니다. 

   ![시간 범위 변경](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. 쿼리에 필터를 추가하려면 **추가**를 선택합니다. 

   ![쿼리에 필터 추가](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. **필터 추가** 아래에 찾으려는 필터 이름을 입력합니다. 필터를 찾으려면 해당 필터를 선택합니다. 왼쪽 창에서 **추가**를 다시 선택합니다.

   예를 들어, 다음은 **Type=="AzureDiagnostics"** 이벤트를 검색한 후 **event_record_messageProperties_interchangeControlNumber_s** 필터를 선택하여 교환 컨트롤 번호에 따라 결과를 찾는 다른 쿼리입니다.

   ![필터 값 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   **추가**를 선택하면 선택한 필터 이벤트 및 값으로 쿼리가 업데이트됩니다. 
   이제 이전 결과 또한 필터링되어 있습니다. 

   예를 들어, 이 쿼리는 **Type=="AzureDiagnostics"** 를 검색한 후 **event_record_messageProperties_interchangeControlNumber_s** 필터를 사용하여 교환 컨트롤 번호에 따라 결과를 찾습니다.

   ![필터링된 결과](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>쿼리 저장

**로그(클래식)** 보기에서 쿼리를 저장하려면 다음 단계를 수행합니다.

1. **로그(클래식)** 페이지의 쿼리에서 **분석**을 선택합니다. 

   ![“분석” 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. 쿼리 도구 모음에서 **저장**을 선택합니다.

   !["저장"을 선택합니다.](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. 예를 들어 쿼리에 대한 세부 정보를 제공하고, 쿼리에 이름을 지정하고, **쿼리**를 선택한 후 범주 이름을 제공합니다. 완료하면 **저장**을 선택합니다.

   !["저장"을 선택합니다.](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. 저장된 쿼리를 보려면 쿼리 페이지로 다시 이동합니다. 쿼리 도구 모음에서 **저장된 검색**을 선택합니다.

   ![“저장된 검색” 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. **저장된 검색** 아래에서 결과를 볼 수 있도록 쿼리를 선택합니다. 

   ![쿼리 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   서로 다른 결과를 찾을 수 있도록 쿼리를 업데이트하려면 쿼리를 편집합니다.

## <a name="find-and-run-saved-queries"></a>저장된 쿼리 찾기 및 실행

1. [Azure Portal](https://portal.azure.com)에서 **모든 서비스**를 선택합니다. 검색 상자에서 "로그 분석"을 찾고 **Log Analytics**를 선택합니다.

   ![Log Analytics 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. **Log Analytics** 아래에서 Log Analytics 작업 영역을 찾고 선택합니다. 

   ![Log Analytics 작업 영역 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. 작업 영역 메뉴의 **일반**에서 **로그(클래식)** 또는 **로그**를 선택합니다. 

   이 예제에서는 클래식 로그 보기의 사용 방법을 보여 줍니다. 

1. 쿼리 페이지가 열리면 쿼리 도구 모음에서 **저장된 검색**을 선택합니다.

   ![“저장된 검색” 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. **저장된 검색** 아래에서 결과를 볼 수 있도록 쿼리를 선택합니다. 

   ![쿼리 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   쿼리는 자동으로 실행되지만, 쿼리가 어떤 이유로든 실행되지 않으면 쿼리 편집기에서 **실행**을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 추적 스키마](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)