---
title: Application Insights를 사용한 Azure Service Fabric 이벤트 분석
description: Azure Service Fabric 클러스터의 모니터링 및 진단을 위해 Application Insights를 사용하여 이벤트를 시각화 및 분석하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 0e84c3c1ab542dc4541ebdcfbc49f45e02458d9c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626217"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Application Insights를 사용하여 이벤트 분석 및 시각화

Azure Monitor의 일부인 Azure Application Insights는 응용 프로그램 모니터링 및 진단을 위한 확장 가능한 플랫폼입니다. 여기에는 강력한 분석 및 쿼리 도구, 사용자 지정 가능한 대시보드 및 시각화, 자동화된 경고 등의 추가 옵션이 포함됩니다. Application Insights와 Service Fabric의 통합에는 Visual Studio 및 Azure Portal에 대한 도구 경험뿐만 아니라, 기본 제공되는 포괄적인 로깅 환경을 제공하는 Service Fabric 관련 메트릭이 포함됩니다. Application Insights를 통해 많은 로그가 자동으로 생성되고 수집되지만 애플리케이션에 사용자 지정 로깅을 추가하여 보다 풍부한 진단 환경을 만드는 것이 좋습니다.

이 문서에서는 다음과 같은 일반적인 질문에 답변합니다.

* 애플리케이션 및 서비스 내부에서 어떤 작업이 진행되는지 파악하고 원격 분석 데이터를 수집하려면 어떻게 해야 하나요?
* 애플리케이션, 특히 서로 통신하는 서비스 문제를 해결하려면 어떻게 해야 하나요?
* 서비스 성능(예: 페이지 로드 시간, HTTP 요청)에 대한 메트릭을 얻으려면 어떻게 해야 하나요?

이 문서의 목적은 Application Insights 내에서 인사이트를 획득하고 문제를 해결하는 방법을 보여주는 것입니다. Service Fabric을 사용하여 Application Insights를 설정 및 구성하는 방법을 알아보려면 [자습서](service-fabric-tutorial-monitoring-aspnet.md)를 살펴보세요.

## <a name="monitoring-in-application-insights"></a>Application Insights에서 모니터링

Application Insights는 Service Fabric을 사용할 때 바로 사용 가능한 환경을 제공합니다. 개요 페이지에서, Application Insights는 응답 시간, 처리된 요청 수 등 서비스에 대한 핵심 정보를 제공합니다. 맨 위에서 '검색' 단추를 클릭하면 애플리케이션의 최근 요청 목록을 볼 수 있습니다. 또한 여기에 실패한 요청을 보고 어떤 오류가 발생했는지 진단할 수 있습니다.

![Application Insights 개요](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

이전 이미지의 오른쪽 패널을 보면 목록에 두 가지 주요 유형인 요청과 이벤트가 있습니다. 이 예에서 요청은 HTTP 요청을 통해 앱의 API로 호출되고, 호출은 코드의 아무 위치에나 추가할 수 있는 원격 분석 데이터 역할을 하는 사용자 지정 이벤트입니다. [사용자 지정 이벤트 및 메트릭용 Application Insights API](../azure-monitor/app/api-custom-events-metrics.md)에서 애플리케이션 계측을 추가로 탐색할 수 있습니다. 요청을 클릭하면 다음 이미지와 같이 Application Insights Service Fabric NuGet 패키지에서 수집되는 Service Fabric 관련 데이터를 포함하여 자세한 정보가 표시됩니다. 이 정보는 문제를 해결하고 애플리케이션 상태를 파악하는 데 유용하며, 이 모든 정보는 Application Insights 내에서 검색할 수 있습니다.

![Application Insights Service Fabric NuGet 패키지에서 수집되는 Service Fabric 관련 데이터를 포함하여 자세한 정보가 보여 주는 스크린샷](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights에는 들어오는 모든 데이터를 쿼리하기 위해 지정된 보기가 있습니다. 개요 페이지 맨 위에 있는 "메트릭 탐색기"를 선택하여 Application Insights 포털로 이동합니다. 여기서 Kusto 쿼리 언어를 사용하여 앞에서 언급한 사용자 지정 이벤트, 요청, 예외, 성능 카운터 및 기타 메트릭에 대해 쿼리를 실행할 수 있습니다. 다음 예제는 지난 1시간의 모든 요청을 보여줍니다.

![Application Insights 요청 세부 정보](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Application Insights 포털의 기능을 좀 더 자세히 살펴보려면 [Application Insights 포털 설명서](../azure-monitor/app/overview-dashboard.md)로 이동하세요.

### <a name="configuring-application-insights-with-eventflow"></a>EventFlow로 Application Insights 구성

EventFlow를 사용하여 이벤트를 집계하는 경우 `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`NuGet 패키지를 가져와야 합니다. 다음 코드는 *eventFlowConfig.json* 의 *outputs* 섹션에 필요합니다.

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

필터를 필요한 대로 변경하고 다른 입력(각각의 NuGet 패키지와 함께)도 포함해야 합니다.

## <a name="application-insights-sdk"></a>Application Insights SDK

EventFlow 및 WAD는 진단/모니터링에 좀 더 모듈 방식으로 접근할 수 있으므로 집계 솔루션으로 사용하는 것이 좋습니다. 즉, EventFlow의 출력을 변경하려는 경우 실제 계측을 변경할 필요 없이 구성 파일을 간단히 수정하기만 하면 됩니다. Application Insights를 사용하기로 결정하고 다른 플랫폼으로 변경할 가능성이 없는 경우 이벤트를 집계하여 Application Insights에 보내는 작업에 대해 Application Insights의 새로운 SDK를 사용하는 일을 살펴보아야 합니다. 즉, 데이터를 Application Insights로 보내도록 EventFlow를 구성할 필요가 없으며 대신 ApplicationInsight의 Service Fabric NuGet 패키지를 설치합니다. 패키지에 대한 자세한 내용은 [여기](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)를 참조하세요.

[Application Insights support for Microservices and Containers](https://azure.microsoft.com/blog/app-insights-microservices/)(마이크로 서비스 및 컨테이너에 대한 Application Insights 지원)에서는 작업 중인 새로운 기능 중 일부(현재 베타 버전)를 보여 주며 이를 통해 Application Insights에서 보다 풍부한 기본 모니터링 옵션을 사용할 수 있습니다. 여기에는 종속성 추적(클러스터의 모든 서비스 및 애플리케이션의 AppMap을 빌드하고 그 사이의 통신을 설정하는 데 사용됨) 및 서비스에서 발생한 추적의 상관 관계가 포함됩니다(애플리케이션 또는 서비스의 워크플로 문제를 정확히 발견 가능).

.NET에서 개발 중이고 Service Fabric의 프로그래밍 모델 중 일부를 사용하고 있으며 이벤트 및 로그 데이터를 시각화하고 분석하기 위한 플랫폼으로 Application Insights를 사용하려는 경우 모니터링 및 진단 워크플로인 Application Insights SDK 경로를 통해 이동하는 것이 좋습니다. Application Insights를 사용하여 로그를 수집/표시하는 작업을 시작하려면 [Application Insights 설명서](../azure-monitor/azure-monitor-app-hub.yml) 및 [추적 로그 설명서](../azure-monitor/app/asp-net-trace-logs.md)를 참조하세요.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Azure Portal에서 Application Insights 리소스 이동

Application Insights를 이벤트 및 로그의 출력으로 구성했으면 몇 분 내에 Application Insights 리소스에 정보가 표시되기 시작합니다. Application Insights 리소스로 이동합니다. 그러면 Application Insights 리소스 대시보드가 표시됩니다. Application Insights 작업 표시줄에서 **검색** 을 선택하여 수신한 최신 추적을 보고 필터링할 수 있습니다.

*메트릭 탐색기* 는 애플리케이션, 서비스 및 클러스터가 보고할 수 있는 메트릭을 기반으로 사용자 지정 대시보드를 만드는 데 유용한 도구입니다. 수집 중인 데이터를 기반으로 직접 몇 가지 차트를 설정하려면 [Application Insights에서 메트릭 탐색](../azure-monitor/essentials/metrics-charts.md)을 참조하세요.

**분석** 을 클릭하면 Application Insights 분석 포털로 이동합니다. 이 포털에서는 보다 넓은 범위 및 다양한 옵션으로 이벤트 및 추적을 쿼리할 수 있습니다. 자세한 내용은 [Application Insights의 분석](../azure-monitor/logs/log-query-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [AI에 경고 설정](../azure-monitor/alerts/alerts-log.md) - 성능 또는 사용 변경에 대한 알림 받기
* [Application Insights의 스마트 감지](../azure-monitor/app/proactive-diagnostics.md) - 잠재적인 성능 문제를 경고하기 위해 Application Insights에 전송되는 원격 분석에 대한 사전 분석 수행
