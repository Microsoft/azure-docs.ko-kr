---
title: Azure Cloud Services용 Application Insights | Microsoft Docs
description: Application Insights를 사용하여 웹 및 작업자 역할을 효과적으로 모니터링
services: application-insights
documentationcenter: ''
keywords: WAD2AI, Azure Diagnostics
author: mrbullwinkle
manager: carmonm
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.workload: tbd
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: d27c0e9570959e01267d83a768ead45b48b7cea1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60903273"
---
# <a name="application-insights-for-azure-cloud-services"></a>Azure Cloud Services용 Application Insights
[Application Insights][start]는 Application Insights SDK의 데이터와 Cloud Services의 [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) 데이터를 결합하여 [Azure Cloud Services 앱](https://azure.microsoft.com/services/cloud-services/)의 가용성, 성능, 오류 및 사용량을 모니터링할 수 있습니다. 앱의 성능 및 효과에 대한 생생한 피드백을 통해 충분한 정보를 바탕으로 각 개발 수명 주기의 디자인 방향을 결정할 수 있습니다.

![개요 대시보드](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>필수 조건
이 작업을 시작하려면 다음이 필요합니다.

* [Azure](https://azure.com) 구독. Windows, Xbox Live 또는 기타 Microsoft 클라우드 서비스의 Microsoft 계정으로 로그인합니다. 
* Microsoft Azure 도구 2.9 이상
* 개발자 분석 도구 7.10 이상

## <a name="get-started-quickly"></a>신속하게 시작하기
Application Insights를 사용하여 클라우드 서비스를 모니터링하는 가장 쉽고 빠른 방법은 Azure에 서비스를 게시할 때 이 옵션을 선택하는 것입니다.

![진단 설정 페이지 예시](./media/cloudservices/azure-cloud-application-insights.png)

이 옵션은 런타임에 앱을 계측하여 사용자의 웹 역할에서 요청, 예외 및 종속성을 모니터링하는 데 필요한 모든 원격 분석을 제공합니다. 또한 작업자 역할의 성능 카운터를 모니터링합니다. 앱에서 생성된 모든 진단 추적도 Application Insights로 전송됩니다.

이 옵션만 필요한 경우, 옵션 선택 후 게시를 완료하면 됩니다. 

다음 단계에서는 [앱에서 메트릭을 보고](../../azure-monitor/app/metrics-explorer.md), [분석을 사용하여 데이터를 쿼리하고](../../azure-monitor/app/analytics.md), [대시보드](../../azure-monitor/app/app-insights-dashboards.md)를 설정합니다. 

브라우저에서 성능을 모니터링하려면 [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md)를 설정하고 [웹 페이지에 코드를 추가](../../azure-monitor/app/javascript.md)할 수도 있습니다.

다음 섹션에서는 다음과 같은 추가 옵션을 설명합니다.

* 다양한 구성 요소에서 데이터를 보내고 개별 리소스에 대한 구성을 빌드합니다.
* 앱에서 사용자 지정 원격 분석을 추가합니다.

## <a name="sample-app-instrumented-with-application-insights"></a>Application Insights를 사용하여 계측하는 샘플 앱
이 [샘플 앱](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)에서 Application Insights는 Azure에서 두 작업자 역할이 호스트되는 클라우드 서비스에 추가됩니다. 

다음 섹션에서는 동일한 방식으로 사용자 자신의 클라우드 서비스 프로젝트를 조정하는 방법을 알아봅니다.

## <a name="plan-resources-and-resource-groups"></a>리소스 및 리소스 그룹 계획
앱의 원격 분석은 Application Insights 형식의 Azure 리소스에 저장, 분석 및 표시됩니다. 

각 리소스는 리소스 그룹에 속합니다. 리소스 그룹은 비용을 관리하고, 팀 구성원에게 액세스 권한을 부여하고, 조정된 단일 트랜잭션에서 업데이트를 배포하는 데 사용됩니다. 예를 들어 한 번의 작업으로 Azure 클라우드 서비스와 해당 Application Insights 모니터링 리소스에 [배포할 스크립트를 작성](../../azure-resource-manager/resource-group-template-deploy.md)할 수 있습니다.

### <a name="resources-for-components"></a>구성 요소에 대한 리소스
앱의 각 구성 요소에 대해 별도 리소스를 만드는 것이 좋습니다. 즉, 각 웹 역할 및 작업자 역할에 대해 리소스를 만드는 것입니다. 각 구성 요소를 개별적으로 분석할 수 있지만 모든 구성 요소의 주요 차트를 함께 표시하는 [대시보드](../../azure-monitor/app/app-insights-dashboards.md)를 만들어 단일 보기에서 이러한 차트를 비교하고 함께 모니터링할 수 있습니다. 

또 다른 접근 방법은 둘 이상의 역할에서 동일한 리소스로 원격 분석을 전송하되, [각 원격 분석 항목에 해당 소스 역할을 식별하는 차원 속성을 추가](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer)하는 것입니다. 이 접근 방법에서는 예외와 같은 메트릭 차트에 일반적으로 다양한 역할의 카운트 집계가 표시되지만 필요한 경우 역할 식별자별로 차트를 분할할 수 있습니다. 동일한 차원으로 검색을 필터링할 수도 있습니다. 이 방법을 사용하면 모든 항목을 보다 쉽게 동시에 볼 수 있지만 역할 간에 약간의 혼동이 발생할 수도 있습니다.

브라우저 원격 분석은 일반적으로 해당 서버 쪽 웹 역할과 동일한 리소스에 포함됩니다.

다양한 구성 요소에 대한 Application Insights 리소스를 하나의 리소스 그룹에 두세요. 이 방식을 사용하면 함께 관리하기가 쉬워집니다. 

### <a name="separate-development-test-and-production"></a>개발, 테스트 및 프로덕션 구분
이전 버전을 사용하는 동안 다음 기능에 대한 사용자 지정 이벤트를 개발하는 경우 개발 원격 분석을 별도의 Application Insights 리소스에 전송할 수 있습니다. 그렇지 않으면 라이브 사이트의 모든 트래픽에서 테스트 원격 분석을 찾기가 어려워집니다.

이 상황을 방지하려면 각 빌드 구성 또는 시스템의 “스탬프”(개발, 테스트, 프로덕션 등)에 대한 별도 리소스를 만듭니다. 각 빌드 구성에 대한 리소스를 별도 리소스 그룹에 둡니다. 

원격 분석을 적절한 리소스에 전송하려면 빌드 구성에 따라 서로 다른 계측 키를 선택할 수 있도록 Application Insights SDK를 설정합니다. 

## <a name="create-an-application-insights-resource-for-each-role"></a>각 역할에 대한 Application Insights 리소스 만들기

각 역할에 대한 별도의 리소스(각 빌드 구성에 대한 별도의 집합)를 만들기로 결정한 경우 Application Insights 포털에서 모든 리소스를 만드는 것이 가장 간편합니다. 많은 리소스를 만드는 경우 [프로세스를 자동화](../../azure-monitor/app/powershell.md)할 수 있습니다.

1. [Azure Portal][portal]에서 **새로 만들기** > **개발자 서비스** > **Application Insights**를 선택합니다.  

    ![Application Insights 창](./media/cloudservices/01-new.png)

1. **애플리케이션 유형** 드롭다운 목록에서 **ASP.NET 웹 애플리케이션**을 선택합니다.  
    각 리소스는 계측 키로 식별됩니다. 이 키는 나중에 SDK의 구성을 수동으로 구성하거나 확인하려는 경우에 필요할 수 있습니다.


## <a name="set-up-azure-diagnostics-for-each-role"></a>각 역할에 대한 Azure Diagnostics 설정
Application Insights를 사용하여 앱을 모니터링하려면 이 옵션을 설정합니다. 웹 역할의 경우 이 옵션은 성능 모니터링, 경고, 진단 및 사용 현황 분석을 제공합니다. 다른 역할의 경우 다시 시작, 성능 카운터 및 System.Diagnostics.Trace 호출과 같은 Azure Diagnostics를 검색하고 모니터링할 수 있습니다. 

1. Visual Studio 솔루션 탐색기의 **\<YourCloudService>** > **역할**에서 각 역할의 속성을 엽니다.

1. **구성**에서 **진단 데이터를 Application Insights로 보내기** 확인란을 선택하고 이전에 만든 적절한 Application Insights 리소스를 선택합니다.

각 빌드 구성에 대한 별도의 Application Insights 리소스를 사용하려면 먼저 구성을 선택합니다.

![Application Insights 구성](./media/cloudservices/configure-azure-diagnostics.png)

그러면 Application Insights 계측 키가 *ServiceConfiguration.\*.cscfg* 파일에 삽입됩니다. 다음은 [샘플 코드](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)입니다.

Application Insights로 전송되는 진단 정보의 수준을 변경하려는 경우 [.cscfg 파일을 직접 편집](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)할 수 있습니다.

## <a name="sdk"></a>각 프로젝트에 SDK 설치
이 옵션을 사용하여 임의 역할에 사용자 지정 비즈니스 원격 분석을 추가할 수 있습니다. 이 옵션은 앱이 사용되는 방식과 성능을 좀 더 자세히 분석해서 제공합니다.

Visual Studio에서 각 클라우드 앱 프로젝트에 Application Insights SDK를 구성합니다.

1. **웹 역할**을 구성하려면 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Application Insights 구성** 또는 **추가 > Application Insights 원격 분석**을 선택합니다.

1. **작업자 역할**을 구성하려면 

    a. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

    b. [Windows 서버용 Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)를 추가합니다.

    !["Application Insights" 검색](./media/cloudservices/04-ai-nuget.png)

1. Application Insights 리소스에 데이터를 보내도록 SDK를 구성하려면

    a. 적합한 시작 함수에서 .cscfg 파일의 구성 설정에서 계측 키를 설정합니다.
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. 앱의 각 역할에 대해 "단계 a"를 반복합니다. 예제 참조:
   
    * [웹 역할](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [작업자 역할](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [웹 페이지](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. 항상 출력 디렉터리에 복사되도록 *ApplicationInsights.config* 파일을 설정합니다.  
    *.config* 파일에 계측 키를 배치하도록 요청하는 메시지가 표시됩니다. 그러나 클라우드 앱의 경우에는 *.cscfg* 파일에서 설정하는 것이 좋습니다. 그래야 포털에서 역할이 정확하게 식별됩니다.

#### <a name="run-and-publish-the-app"></a>앱 실행 및 게시

1. 앱을 실행하고 Azure에 로그인합니다. 

1. 만든 Application Insights 리소스를 엽니다.  
    개별 데이터 요소는 [Search](../../azure-monitor/app/diagnostic-search.md)에 표시되고 집계된 데이터는 [메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md)에 표시됩니다. 

1. 원격 분석을 더 추가하고(다음 섹션 참조) 앱을 게시하여 라이브 진단 및 사용 피드백을 가져옵니다. 

데이터가 없는 경우 다음을 수행합니다.
1. 개별 이벤트를 보려면 [Search][diagnostic] 타일을 엽니다.
1. 앱에서 원격 분석이 생성되도록 다양한 페이지를 엽니다.
1. 몇 초 정도 기다렸다가 **새로 고침**을 클릭합니다.  
    자세한 내용은 [문제 해결][qna]을 참조하세요.

## <a name="view-azure-diagnostics-events"></a>Azure Diagnostics 이벤트 보기
다음 위치에서 Application Insights의 [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) 정보를 찾을 수 있습니다.

* 성능 카운터는 사용자 지정 메트릭으로 표시됩니다. 
* Windows 이벤트 로그는 추적 및 사용자 지정 이벤트로 표시됩니다.
* 애플리케이션 로그, ETW 로그 및 진단 인프라 로그는 추적으로 표시됩니다.

성능 카운터 및 이벤트 개수를 보려면 [메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md)를 열고 다음 차트를 추가합니다.

![Azure Diagnostics 데이터](./media/cloudservices/23-wad.png)

Azure Diagnostics에서 보낸 다양한 추적 로그를 검색하려면 [Search](../../azure-monitor/app/diagnostic-search.md) 또는 [분석 쿼리](../../azure-monitor/log-query/get-started-portal.md)를 사용합니다. 예를 들어 역할이 충돌하여 재활용되도록 하는 처리되지 않은 예외가 있다고 가정해 봅시다. 해당 정보는 Windows 이벤트 로그의 애플리케이션 채널에 표시됩니다. Search를 사용하여 Windows 이벤트 로그 오류를 살펴보고 예외에 대한 전체 스택 추적을 가져올 수 있습니다. 이렇게 하면 문제의 근본 원인을 찾는 데 도움이 됩니다.

![Azure Diagnostics 검색](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>추가 원격 분석
다음 섹션에서는 앱의 다양한 측면에서 추가 원격 분석을 가져오는 방법을 설명합니다.

## <a name="track-requests-from-worker-roles"></a>작업자 역할의 요청 추적
웹 역할에서 요청 모듈은 자동으로 HTTP 요청에 대한 데이터를 수집합니다. 기본 컬렉션 동작을 재정의할 수는 방법에 대한 예제는 [샘플 MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)을 참조하세요. 

HTTP 요청과 같은 방법으로 요청을 추적하여 작업자 역할에 대한 호출의 성능을 캡처할 수 있습니다. Application Insights에서 요청 원격 분석 유형은 시간을 제한할 수 있고 독립적으로 성공 또는 실패할 수 있는 명명된 서버 쪽 작업의 단위를 측정합니다. HTTP 요청은 SDK에서 자동으로 캡처하지만 사용자 고유의 코드를 삽입하여 작업자 역할에 대한 요청을 추적할 수 있습니다.

보고서 요청에서 대해 계측된 두 샘플 작업자 역할: 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>예외
다양한 웹앱 유형에서 처리되지 않은 예외를 수집하는 방법에 대한 자세한 내용은 [Application Insights에서 예외 모니터링](../../azure-monitor/app/asp-net-exceptions.md)을 참조하세요.

샘플 웹 역할에는 MVC5 및 Web API 2 컨트롤러에 있습니다. 2에서 처리되지 않은 예외는 다음 처리기를 통해 캡처됩니다.

* [이 예제와 같은](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) MVC5 컨트롤러의 [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) 설정 
* [이 예제와 같은](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) Web API 2 컨트롤러의 [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) 설정 

작업자 역할의 경우 다음 두 가지 방법으로 예외를 추적할 수 있습니다.

* TrackException(ex)을 사용합니다.
* Application Insights 추적 수신기 NuGet 패키지를 추가한 경우 [이 예제와 같이](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107) System.Diagnostics.Trace를 사용하여 예외를 기록합니다.

## <a name="performance-counters"></a>성능 카운터
다음 카운터가 기본적으로 수집됩니다.

    * \Process(??APP_WIN32_PROC??)\% Processor Time
    * \Memory\Available Bytes
    * \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
    * \Process(??APP_WIN32_PROC??)\Private Bytes
    * \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
    * \Processor(_Total)\% 프로세서 시간

웹 역할의 경우 이러한 카운터도 수집됩니다.

    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

[이 예제와 같이](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14) *ApplicationInsights.config*를 편집하여 추가 사용자 지정 카운터 또는 기타 Windows 성능 카운터를 지정할 수 있습니다.

  ![성능 카운터](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>작업자 역할에 대한 상호 관련된 원격 분석
풍부한 진단 경험이 있으면 실패 또는 높은 대기 시간을 초래하는 것이 무엇인지 알 수 있습니다. 웹 역할과 함께 SDK는 관련된 원격 분석 간의 상관 관계를 자동으로 설정합니다. 

작업자 역할의 경우 이를 위해 사용자 지정 원격 분석 이니셜라이저를 사용하여 모든 원격 분석에 대해 공통 Operation.Id 컨텍스트 특성을 설정할 수 있습니다. 이렇게 하면 대기 시간 또는 실패 문제가 종속성으로 야기되는지 또는 코드 문제 때문인지를 한눈에 알아볼 수 있습니다. 

방법은 다음과 같습니다.

* [이 예제와 같이](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36) correlationId를 CallContext로 설정합니다. 이 경우에 요청 ID를 상관 관계 ID로 사용합니다.
* 사용자 지정 TelemetryInitializer 구현을 추가하여 Operation.Id를 이전에 설정한 correlationId로 설정합니다. 그 예로 [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)를 참조하세요.
* 사용자 지정 원격 분석 이니셜라이저를 추가합니다. *ApplicationInsights.config* 파일 또는 [이 예제와 같이](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233) 코드에서 이 작업을 수행할 수 있습니다.

## <a name="client-telemetry"></a>클라이언트 원격 분석
페이지 보기 수, 페이지 로드 시간, 스크립트 예외 사항과 같은 브라우저 기반 원격 분석을 가져오고 페이지 스크립트에서 사용자 지정 원격 분석을 쓰려면 [JavaScript SDK를 웹 페이지에 추가][client]를 참조하세요.

## <a name="availability-tests"></a>가용성 테스트
앱을 라이브 상태로 유지하고 응답하도록 하려면 [웹 테스트를 설정][availability]합니다.

## <a name="display-everything-together"></a>모든 항목을 함께 표시
시스템에 대한 전반적인 정보를 얻기 위해 주요 모니터링 차트를 하나의 [대시보드](../../azure-monitor/app/app-insights-dashboards.md)에 표시할 수 있습니다. 예를 들어 각 역할의 요청 및 실패 수를 고정할 수 있습니다. 

시스템에서 Stream Analytics와 같은 다른 Azure 서비스를 사용하는 경우 해당 모니터링 차트도 포함합니다. 

클라이언트 모바일 앱이 있는 경우 [App Center](../../azure-monitor/learn/mobile-center-quickstart.md)를 사용합니다. [분석](../../azure-monitor/app/analytics.md)에서 이벤트 수를 표시하는 쿼리를 만들어 대시보드에 고정합니다.

## <a name="example"></a>예
[예제](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) 는 웹 역할 및 두 작업자 역할이 포함되는 서비스를 모니터링합니다.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Azure Cloud Services에서 실행할 때의 "메서드를 찾을 수 없음" 예외
.NET 4.6용으로 빌드하셨나요? .NET 4.6은 Azure Cloud Services 역할에서 자동으로 지원되지 않습니다. 앱을 실행하기 전에 [각 역할에 .NET 4.6을 설치](../../cloud-services/cloud-services-dotnet-install-dotnet.md)합니다.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>다음 단계
* [Application Insights에 Azure Diagnostics를 보내도록 구성](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [자동으로 Application Insights 리소스 만들기](../../azure-monitor/app/powershell.md)
* [Azure Diagnostics 자동화](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
