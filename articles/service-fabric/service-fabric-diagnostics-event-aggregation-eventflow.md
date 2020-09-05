---
title: EventFlow를 사용 하 여 Azure Service Fabric 이벤트 집계
description: Azure Service Fabric 클러스터 모니터링 및 진단을 위해 EventFlow를 사용하여 이벤트를 집계 및 수집하는 방법에 대해 알아봅니다.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: a80eea5a50aa7b1e441049eeb2cae381994cd3ec
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018657"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>EventFlow를 사용하여 이벤트 집계 및 수집

[Microsoft 진단 EventFlow](https://github.com/Azure/diagnostics-eventflow)(영문)는 노드의 이벤트를 하나 이상의 모니터링 대상으로 라우팅할 수 있습니다. 서비스 프로젝트에 NuGet 패키지로 포함되어 있어 EventFlow 코드와 구성이 서비스와 함께 이동하므로 Azure Diagnostics에 대해 앞에서 언급한 노드별 구성 문제가 제거됩니다. EventFlow는 서비스 프로세스 내에서 실행되며 구성된 출력에 직접 연결됩니다. 이러한 직접 연결로 인해 EventFlow는 Azure, 컨테이너 및 온-프레미스 서비스 배포에서 작동합니다. 각 EventFlow 파이프라인이 외부 연결을 만들기 때문에 컨테이너와 같이 고밀도 시나리오에서 EventFlow를 실행하는 경우 신중해야 합니다. 따라서 여러 프로세스를 호스트하는 경우 몇 가지 아웃바운드 연결이 생성됩니다. 이 경우`ServiceType`의 모든 복제본이 동일한 프로세스에서 실행되어 아웃바운드 연결 수를 제한하므로 Service Fabric 애플리케이션에 별로 문제가 되지 않습니다. EventFlow는 이벤트 필터링도 제공하므로 지정된 필터와 일치하는 이벤트만 보냅니다.

## <a name="set-up-eventflow"></a>EventFlow 설정

EventFlow 이진을 NuGet 패키지 집합으로 사용할 수 있습니다. EventFlow를 Service Fabric 서비스 프로젝트에 추가하려면 Solution Explorer에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 “NuGet 패키지 관리”를 선택합니다. "찾아보기" 탭으로 전환하고 "`Diagnostics.EventFlow`"를 검색합니다.

![Visual Studio NuGet 패키지 관리자 UI에서 EventFlow NuGet 패키지](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

"입력" 및 "출력" 레이블이 지정된 다양한 패키지 목록이 나타납니다. EventFlow는 다양한 로깅 공급자 및 분석기를 지원합니다. EventFlow를 호스팅하는 서비스는 애플리케이션 로그에 대해 원본과 대상에 따라 적합한 패키지를 포함해야 합니다. 핵심 ServiceFabric 패키지 외에도 최소한 하나의 입력 및 출력이 구성되어 있어야 합니다. 예를 들어 다음 패키지를 추가하여 EventSource 이벤트를 Application Insights에 보낼 수 있습니다.

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` 서비스의 EventSource 클래스 및 표준 EventSources (예: *ServiceFabric* 및 *ServiceFabric*)에서 데이터를 캡처하려면
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`(여기서는 Azure Application Insights 리소스에 로그를 보낼 것임)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(Service Fabric 서비스 구성에서 EventFlow 파이프라인의 초기화를 활성화하고 진단 데이터를 Service Fabric 상태 보고서로 보내 모든 문제를 보고)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` 패키지에서는 서비스 프로젝트가 .NET Framework 4.6 이상을 대상으로 해야 합니다. 이 패키지를 설치하기 전에 프로젝트 속성에서 적합한 대상 프레임워크를 설정해야 합니다.

모든 패키지를 설치한 다음에는 서비스에서 EventFlow를 구성하여 활성화합니다.

## <a name="configure-and-enable-log-collection"></a>로그 수집 구성 및 활성화
로그 보내기를 담당하는 EventFlow 파이프라인은 구성 파일에 저장된 사양으로부터 만들어집니다. `Microsoft.Diagnostics.EventFlow.ServiceFabric` 패키지는 시작 EventFlow 구성 파일을 `PackageRoot\Config` 솔루션 폴더에 `eventFlowConfig.json` 이름으로 설치합니다. 기본 서비스 `EventSource` 클래스 및 구성하려는 다른 입력에서 데이터를 수집하고 적절한 위치로 데이터를 보내도록 이 구성 파일을 수정해야 합니다.

>[!NOTE]
>프로젝트 파일에 VisualStudio 2017 형식이 있는 경우 `eventFlowConfig.json` 파일이 자동으로 추가되지 않습니다. 이를 해결하려면 `Config` 폴더에 파일을 만들고 빌드 작업을 `Copy if newer`로 설정합니다. 

다음은 위에서 언급한 NuGet 패키지를 기반으로 하는 샘플 *eventFlowConfig.json*입니다.
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

서비스의 ServiceEventSource 이름은ServiceEventSource 클래스에 적용된 `EventSourceAttribute`의 Name 속성 값입니다. 모두 `ServiceEventSource.cs` 파일에서 지정하며 서비스 코드의 일부입니다. 예를 들어 다음 코드 조각에서 ServiceEventSource의 이름은 *MyCompany-Application1-Stateless1*입니다.

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

`eventFlowConfig.json` 파일은 서비스 구성 패키지의 일부입니다. 이 파일에 대한 변경 사항은 서비스의 전체 또는 구성 전용 업그레이드에 포함될 수 있습니다. 업그레이드 실패 시 Service Fabric 업그레이드 상태 확인과 자동 롤백의 대상이 됩니다. 자세한 내용은 [Service Fabric 애플리케이션 업그레이드](service-fabric-application-upgrade.md)를 참조하세요.

구성의 *filters* 섹션을 사용하면 EventFlow 파이프라인을 통해 출력으로 전송하려는 정보를 추가로 사용자 지정하여 특정 정보를 삭제 또는 포함하거나 이벤트 데이터의 구조를 변경할 수 있습니다. 필터링에 대한 자세한 내용은 [EventFlow 필터](https://github.com/Azure/diagnostics-eventflow#filters)를 참조하세요.

마지막 단계는 `Program.cs` 파일에 있는 서비스 시작 코드에서 EventFlow 파이프라인을 인스턴스화하는 것입니다.

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

`ServiceFabricDiagnosticsPipelineFactory`의 `CreatePipeline` 메서드 매개 변수 형태로 전달되는 이름은 EventFlow 로그 수집 파이프라인을 나타내는 *상태 개체*의 이름입니다. EventFlow에서 오류가 발생하고 Service Fabric 상태 하위 시스템을 통해 보고할 경우 이 이름을 사용합니다.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>eventFlowConfig에서 Service Fabric 설정 및 애플리케이션 매개 변수 사용

EventFlow는 Service Fabric 설정 및 애플리케이션 매개 변수를 사용하여 EventFlow 설정 구성을 지원합니다. 값에 대해 다음 특수 구문을 사용하여 Service Fabric 설정 매개 변수를 참조할 수 있습니다.

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>`은 Service Fabric 구성 섹션의 이름이고 `<setting-name>`은 EventFlow 설정을 구성하는 데 사용할 값을 제공하는 구성 설정입니다. 이 작업을 수행하는 방법에 대한 자세한 내용을 보려면 [Support for Service Fabric settings and application parameters](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters)(Service Fabric 설정 및 애플리케이션 매개 변수 지원)를 참조하세요.

## <a name="verification"></a>확인

서비스를 시작하고 Visual Studio의 출력 창에서 디버그를 관찰합니다. 서비스가 시작되면 구성된 출력으로 서비스가 레코드를 보내는 증거를 확인하기 시작합니다. 이벤트 분석 및 시각화 플랫폼으로 이동하여 로그가 나타나는지 확인합니다(몇 분이 소요될 수 있음).

## <a name="next-steps"></a>다음 단계

* [Application Insights를 사용 하 여 이벤트 분석 및 시각화](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Azure Monitor 로그를 사용 하 여 이벤트 분석 및 시각화](service-fabric-diagnostics-event-analysis-oms.md)
* [EventFlow 설명서](https://github.com/Azure/diagnostics-eventflow)
