---
title: 여러 Azure Monitor Application Insights 리소스 통합 | Microsoft Docs
description: 이 문서에서는 Azure Monitor Logs의 함수를 사용해 여러 Application Insights 리소스를 쿼리하고 해당 데이터를 표시하는 방법을 자세히 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: 3f3de81197b05d4f025a3fd8638cffe4b07cecad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61424657"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>여러 Azure Monitor Application Insights 리소스 통합 
이 문서에서는 모든 Application Insights 애플리케이션 로그 데이터가 각기 다른 Azure 구독에 있더라도 한 곳에서 쿼리하고 확인하는 방법을 설명합니다. 사용이 중단된 Application Insights 커넥터 대신 이 방법을 사용할 수 있습니다. 단일 쿼리에 포함할 수 있는 Application Insights 리소스 수는 100개로 제한됩니다.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>여러 Application Insights 리소스를 쿼리하는 권장 방식 
여러 Application Insights 리소스의 목록을 쿼리에 표시하는 방식은 번거로우며 유지 관리하기가 어려울 수 있습니다. 이러한 방식 대신 함수를 사용해 애플리케이션 범위에서 쿼리 논리를 분리할 수 있습니다.  

이 예제에서는 여러 Application Insights 리소스를 모니터링하고 애플리케이션 이름을 기준으로 실패한 요청의 수를 시각화하는 방법을 보여 줍니다. 시작하기 전에 Application Insights 리소스에 연결된 작업 영역에서 이 쿼리를 실행하여 연결된 애플리케이션 목록을 가져옵니다. 

```
ApplicationInsights
| summarize by ApplicationName
```

애플리케이션 목록에 합집합 연산자를 사용하는 함수를 만든 다음, *applicationsScoping*이라는 별칭으로 작업 영역의 쿼리를 함수로 저장합니다.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>언제든지 포털에서 작업 영역의 쿼리 탐색기로 이동한 후 편집할 함수를 선택하고 저장하여, 또는 `SavedSearch` PowerShell cmdlet을 사용하여 나열된 애플리케이션을 수정할 수 있습니다. `withsource= SourceApp` 명령은 로그를 전송한 애플리케이션을 지정하는 열을 결과에 추가합니다. 
>
>ApplicationsScoping 함수는 Application Insights 데이터 구조를 반환하므로 쿼리는 작업 영역에서 실행되지만 Application Insights 스키마를 사용합니다. 
>
>이 예제의 선택적 연산자인 parse는 SourceApp 속성에서 애플리케이션 이름을 추출합니다. 

이제 리소스 간 쿼리에 applicationsScoping 함수를 사용할 수 있습니다.  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

함수 별칭은 정의된 모든 애플리케이션에서 요청의 합집합을 반환합니다. 그런 다음, 쿼리는 실패한 요청에 대해 필터링하고 애플리케이션별로 추세를 시각화합니다.

![쿼리 간 결과 예제](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>여러 Application Insights 리소스 및 작업 영역 데이터 쿼리 
Connector를 중지하고 Application Insights 데이터 보존 기간(90일)에 따라 잘린 시간 범위에 대해 쿼리를 수행해야 하는 경우 작업 영역과 Application Insights 리소스에서 중간 기간에 대해 [리소스 간 쿼리](../../azure-monitor/log-query/cross-workspace-query.md)를 수행해야 합니다. 위에서 설명한 새 Application Insights 데이터 보존 기간에 따라 애플리케이션 데이터가 누적될 때까지 이 쿼리를 수행합니다. Application Insights의 스키마와 작업 영역의 스키마는 서로 다르기 때문에 이 쿼리는 다소 조작해야 합니다. 스키마 차이점이 강조 표시되어 있는 이 섹션 뒷부분의 표를 참조하세요. 

>[!NOTE]
>로그 경고의 [리소스 간 쿼리](../log-query/cross-workspace-query.md)는 새 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)에서 지원됩니다. [레거시 Log Analytics 경고 API](../platform/api-alerts.md)에서 전환하지 않는 한, Azure Monitor는 기본적으로 [레거시 Log Analytics 경고 API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api)를 사용하여 Azure Portal에서 새 로그 경고 규칙을 만듭니다. 전환 후에는 새 API가 Azure Portal에서 새 경고 규칙의 기본값이 되며, 해당 API를 사용하여 리소스 간 쿼리 로그 경고 규칙을 만들 수 있습니다. [scheduledQueryRules API에 대한 ARM 템플릿](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template)을 사용하면 전환하지 않고 [리소스 간 쿼리](../log-query/cross-workspace-query.md) 로그 경고 규칙을 만들 수 있지만, 이 경고 규칙은 Azure Portal이 아닌 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 통해 관리할 수 있습니다.

예를 들어 2018년 11월 1일에 Connector 작동이 중지된 경우 작업 영역의 애플리케이션 데이터와 Application Insights 리소스 간에 로그를 쿼리할 때는 다음 예제와 같이 쿼리를 생성합니다.

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights 및 Log Analytics 작업 영역 스키마의 차이점
다음 표에는 Log Analytics 및 Application Insights의 스키마 간 차이점이 나와 있습니다.  

| Log Analytics 작업 영역 속성| Application Insights 리소스 속성|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Browser | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Country | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | success | 
| ResponseCode | resultCode | 
| Role | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>다음 단계

Application Insights 앱에 대한 자세한 정보를 보려면 [로그 검색](../../azure-monitor/log-query/log-query-overview.md)을 사용합니다.
