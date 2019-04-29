---
title: Azure Application Insights 데이터를 사용하여 사용자 지정 보고서 자동화
description: Azure Application Insights 데이터를 사용하여 사용자 지정 일간/주간/월간 보고서 자동화
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 37de2de18656f0f8ba85495b3fd72315e0bd885b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60903991"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Azure Application Insights 데이터를 사용하여 사용자 지정 보고서 자동화

정기 간행물 보고서는 팀이 중요한 비즈니스 서비스가 어떻게 진행되고 있는지 정보를 받을 수 있도록 도와줍니다. 개발자, DevOps/SRE 팀 및 관리자는 모두가 포털에 로그인할 필요 없이 안정적으로 인사이트를 제공하는 자동화된 보고서로 생산적일 수 있습니다. 이런 보고서는 어떤 경고 규칙도 트리거하지 않을 수 있는 대기 시간, 부하 또는 실패율의 점진적인 증가를 식별하는 데 도움이 될 수 있습니다.

각 엔터프라이즈에는 다음과 같은 고유의 보고 필요성이 있습니다. 

* 보고서에서 사용자 지정 메트릭 또는 메트릭의 특정 백분위수 집계입니다.
* 다양한 대상을 위한 데이터의 일간, 주간 및 월간 롤업에 대한 다양한 보고서가 있습니다.
* 영역 또는 환경 같은 사용자 지정 특성에 의한 구분입니다. 
* 서로 다른 구독 또는 리소스 그룹 등에 있을 수 있는 경우라 해도 단일 보고서에서 일부 AI 리소스를 그룹화합니다.
* 선택적 대상에게 보내는 중요한 메트릭이 포함된 별도 보고서입니다.
* 포털 리소스에 액세스할 수 없는 관련자에게 보고서를 제공합니다.

> [!NOTE] 
> 주간 Application Insights 다이제스트 이메일은 모든 사용자 지정을 허용하지 않으며 아래 나열된 사용자 지정 옵션을 위해 중단됩니다. 마지막 주간 다이제스트 이메일은 2018년 6월 11일에 전송됩니다. 유사한 사용자 지정 보고서를 가져오기 위한 다음 옵션 중 하나를 구성하세요(아래 제시된 쿼리 사용).

## <a name="to-automate-custom-report-emails"></a>사용자 지정 보고서 이메일을 자동화하려면

[Application Insights 데이터를 프로그래밍 방식으로 쿼리](https://dev.applicationinsights.io/)하여 일정에 따라 사용자 지정 보고서를 생성할 수 있습니다. 다음 옵션은 빠르게 시작할 수 있게 돕습니다.

* [Microsoft Flow를 사용하여 보고서 자동화](automate-with-flow.md)
* [논리 앱을 사용하여 보고서 자동화](automate-with-logic-apps.md)
* 모니터링 시나리오에서 "Application Insights 예약된 다이제스트" [Azure 함수](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) 템플릿을 사용합니다. 이 함수는 SendGrid를 사용하여 이메일을 배달합니다. 

    ![Azure 함수 템플릿](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>주간 다이제스트 이메일에 대한 샘플 쿼리
다음 쿼리는 보고서처럼 주간 다이제스트 이메일에 대한 여러 데이터 세트 가입을 표시합니다. 주간 보고서를 자동화하려면 위에 나열된 옵션 중 하나를 사용하고 필요에 따라 사용자 지정합니다.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Application Insights 예약된 다이제스트 보고서

1. Azure Portal에서 **리소스 만들기** > **Compute** > **Function App**을 선택합니다.

   ![Azure Resource Function App 만들기 스크린샷](./media/automate-custom-reports/function-app-01.png)

2. 앱에 대한 적절한 정보를 입력하고 _만들기_를 선택합니다. (Application Insights를 사용하여 새 Function App을 모니터링하려는 경우에만 Application Insights _On_이 필요합니다.)

   ![Azure Resource Function App 설정 만들기 스크린샷](./media/automate-custom-reports/function-app-02.png)

3. 새 Function App에서 배포를 완료하면 **리소스로 이동**을 선택합니다.

4. **새 함수**를 선택합니다.

   ![새 함수 만들기 스크린샷](./media/automate-custom-reports/function-app-03.png)

5. **_Application Insights 예약된 다이제스트 템플릿_** 을 선택합니다.

   ![새 함수 Application Insights 템플릿 스크린샷](./media/automate-custom-reports/function-app-04.png)

6. 보고서에 대한 적절한 받는 사람 이메일 주소를 입력하고 **만들기**를 선택합니다.

   ![함수 설정 스크린샷](./media/automate-custom-reports/function-app-05.png)

7. **Function App** > **플랫폼 기능** > **애플리케이션 설정**을 선택합니다.

    ![Azure 함수 애플리케이션 설정 스크린샷](./media/automate-custom-reports/function-app-07.png)

8. 적절한 해당 값 ``AI_APP_ID``, ``AI_APP_KEY`` 및 ``SendGridAPI``로 3개의 새 애플리케이션 설정을 만듭니다. **저장**을 선택합니다.

     ![함수 통합 인터페이스 스크린샷](./media/automate-custom-reports/function-app-08.png)
    
    (AI_ 값은 보고하려는 Application Insights 리소스에 대한 API 액세스에서 찾을 수 있습니다. Application Insights API 키가 없는 경우 **API 키 만들기**에 대한 옵션이 있습니다.)
    
   * AI_APP_ID = 애플리케이션 ID
   * AI_APP_KEY = API 키
   * SendGridAPI =SendGrid API 키

     > [!NOTE]
     > SendGrid 계정이 없는 경우 새로 만들 수 있습니다. Azure Functions에 대한 SendGrid의 설명서는 [여기](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid)에 있습니다. SendGrid를 설정하고 이 문서의 끝에 제공되는 API 키를 생성하는 방법에 대한 최소한의 설명을 원하는 경우입니다. 

9. **통합**을 선택하고 출력 아래에서 **SendGrid($return)** 를 선택합니다.

     ![출력 스크린샷](./media/automate-custom-reports/function-app-09.png)

10. **SendGridAPI 키 앱 설정** 아래에서 **SendGridAPI**에 대한 새로 만든 앱 설정을 선택합니다.

     ![Function App 실행 스크린샷](./media/automate-custom-reports/function-app-010.png)

11. Function App을 실행하고 테스트합니다.

     ![테스트 스크린샷](./media/automate-custom-reports/function-app-11.png)

12. 이메일을 확인하여 메시지가 성공적으로 전송/수신되었는지 확인합니다.

     ![이메일 제목 줄 스크린샷](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>Azure로 SendGrid

이러한 단계는 구성된 SendGrid 계정이 아직 없는 경우에만 적용됩니다.

1. Azure Portal에서 **리소스 만들기**를 선택하고 **SendGrid 이메일 전송**을 검색하고 **만들기**를 클릭하고 SendGrid 특정 만들기 지침을 채웁니다. 

     ![SendGrid 리소스 만들기 스크린샷](./media/automate-custom-reports/function-app-13.png)

2. 만들어지면 SendGrid 계정에서 **관리**를 선택합니다.

     ![API 키 설정 스크린샷](./media/automate-custom-reports/function-app-14.png)

3. SendGrid의 사이트가 시작됩니다. **설정** > **API 키**를 선택합니다.

     ![API 키 앱 만들기 및 보기 스크린샷](./media/automate-custom-reports/function-app-15.png)

4. API 키를 만들고 **만들기 및 보기**를 선택합니다. (API 키에 대한 적절한 사용 권한 수준을 확인하려면 제한된 액세스에 대한 SendGrid의 설명서를 검토하세요. 여기에서 예제 목적으로만 모든 권한이 선택됩니다.)

   ![모든 권한 스크린샷](./media/automate-custom-reports/function-app-16.png)

5. 전체 키를 복사합니다. 이 값은 SendGridAPI에 대한 값으로 Function App 설정에서 필요한 값입니다.

   ![API 키 복사 스크린샷](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>다음 단계

* [Analytics 쿼리](../../azure-monitor/log-query/get-started-queries.md) 만들기에 대해 자세히 알아봅니다.
* [Application Insights 데이터를 프로그래밍 방식으로 쿼리](https://dev.applicationinsights.io/)하는 방법에 대해 자세히 알아보기
* [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)에 대해 자세히 알아봅니다.
* [Microsoft Flow](https://ms.flow.microsoft.com)에 대해 자세히 알아봅니다.
