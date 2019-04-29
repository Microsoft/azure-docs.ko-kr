---
title: ApplicationInsights.config 참조 - Azure | Microsoft Docs
description: 데이터 수집 모듈을 사용하거나 사용하지 않도록 설정하고 성능 카운터 및 기타 매개 변수를 추가합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 1a5b6d435dcc82b59c30302f9cd711975864594c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901913"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>ApplicationInsights.config 또는 .xml로 Application Insights SDK 구성
Application Insights .NET SDK는 NuGet 패키지의 숫자로 구성됩니다. [코어 패키지](https://www.nuget.org/packages/Microsoft.ApplicationInsights) Application Insights에 원격 분석을 보내는 경우에 API를 제공합니다. [추가 패키지](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights)는 해당 컨텍스트 및 애플리케이션에서 원격 분석을 자동으로 추적하기 위해 원격 분석 *모듈* 및 *이니셜라이저*를 제공합니다. 구성 파일을 조정하여 모듈을 활성화하거나 비활성화하고 이 중 일부 모듈의 매개 변수를 설정할 수 있습니다.

구성 파일의 이름은 애플리케이션 유형에 따라 `ApplicationInsights.config` 또는 `ApplicationInsights.xml`입니다. [대부분 버전의 SDK는 설치][start]할 때 프로젝트에 자동으로 추가됩니다. 또한 [IIS 서버의 상태 모니터][redfield]에 의해 또는 [Azure 웹 사이트 또는 VM에 대한 Application Insights 확장](azure-web-apps.md)을 선택하는 경우 웹앱에 추가됩니다.

[웹 페이지에서 SDK][client]를 제어할 동급의 파일은 없습니다.

이 문서는 구성 파일에서 참조하는 섹션, SDK의 구성 요소를 제어하는 방법 및 해당 구성 요소를 로드하는 NuGet 패키지를 설명합니다.

> [!NOTE]
> ApplicationInsights.config 및 .xml 지침은 .NET Core SDK에 적용되지 않습니다. .NET Core 애플리케이션에 대한 변경의 경우 일반적으로 appsettings.json 파일을 사용합니다. 이 예제는 [스냅숏 디버거 설명서](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)에서 찾을 수 있습니다.

## <a name="telemetry-modules-aspnet"></a>원격 분석 모듈(ASP.NET)
각 원격 분석 모듈은 특정 형식의 데이터를 수집하고 코어 API를 사용하여 데이터를 전송합니다. 모듈은 다른 NuGet 패키지에 의해 설치되며 이는 .config 파일에 필요한 줄을 추가합니다.

각 모듈에 대해 구성 파일에 노드가 있습니다. 모듈을 사용하지 않으려면 노드를 삭제하거나 주석으로 처리합니다.

### <a name="dependency-tracking"></a>종속성 추적 
[종속성 추적](../../azure-monitor/app/asp-net-dependencies.md) 은 앱이 데이터베이스 및 외부 서비스와 데이터베이스에 수행하는 호출에 대한 원격 분석을 수집합니다. 이 모듈이 IIS 서버에서 작동하도록 하려면 [상태 모니터를 설치][redfield]해야 합니다. Azure 웹앱 또는 VM에서 사용하려면 [Application Insights 확장을 선택](azure-web-apps.md)합니다.

[TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)를 사용하여 종속성 추적 코드를 작성할 수 있습니다.

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet 패키지.

### <a name="performance-collector"></a>성능 수집기
IIS 설치에서 CPU, 메모리 및 네트워크 부하와 같은 [시스템 성능 카운터를 수집](../../azure-monitor/app/performance-counters.md)합니다. 사용자가 직접 설정한 성능 카운터를 포함하여 어떤 카운터를 수집할지 지정할 수 있습니다.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet 패키지.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights 진단 원격 분석
`DiagnosticsTelemetryModule`은 Application Insights instrumenation 코드 자체에 오류를 보고합니다. 예를 들어 코드가 성능 카운터에 액세스할 수 없는 경우 또는 `ITelemetryInitializer` 를 throw하는 경우는 예외입니다. 이 모듈에서 추적된 원격 분석 추적은 [진단 검색][diagnostic]에 표시됩니다. dc.services.vsallin.net에 진단 데이터를 보냅니다.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet 패키지. 이 패키지를 설치하는 경우 ApplicationInsights.config 파일은 자동으로 만들어지지 않습니다.

### <a name="developer-mode"></a>개발자 모드
`DeveloperModeWithDebuggerAttachedTelemetryModule`이 Application Insights`TelemetryChannel`를 적용하여 디버거가 애플리케이션 프로세스에 연결될 때 한번에 원격 분석 항목을 하나씩 즉시 보냅니다. 애플리케이션이 원격 분석을 추적할 때와 Application insights 포털에 나타날 때의 간격의 시간차를 감소시킵니다. CPU 및 네트워크 대역폭에 상당한 오버 헤드가 발생합니다.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet 패키지

### <a name="web-request-tracking"></a>웹 요청 추적
HTTP 요청의 [응답 시간 및 결과 코드](../../azure-monitor/app/asp-net.md) 를 보고합니다.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 패키지

### <a name="exception-tracking"></a>예외 추적
`ExceptionTrackingTelemetryModule` 는 웹앱에서 처리되지 않은 예외를 추적합니다. [오류 및 예외][exceptions]를 참조하세요.

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 패키지
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` - [관찰되지 않은 작업 예외](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx)를 추적합니다.
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - 작업자 역할, Windows 서비스 및 콘솔 애플리케이션에 대한 처리되지 않은 예외를 추적합니다.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet 패키지.

### <a name="eventsource-tracking"></a>EventSource 추적
`EventSourceTelemetryModule`을 사용하여 Application Insights에 전송될 EventSource 이벤트를 구성할 수 있습니다. EventSource 이벤트 추적에 대한 자세한 내용은 [EventSource 이벤트 사용](../../azure-monitor/app/asp-net-trace-logs.md#using-eventsource-events)을 참조하세요.

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW 이벤트 추적
`EtwCollectorTelemetryModule`을 사용하여 Application Insights에 추적으로 전송할 ETW 공급자의 이벤트를 구성할 수 있습니다. ETW 이벤트 추적에 대한 자세한 내용은 [ETW 이벤트 사용](../../azure-monitor/app/asp-net-trace-logs.md#using-etw-events)을 참조하세요.

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Microsoft.ApplicationInsights 패키지는 SDK의 [코어 API](https://msdn.microsoft.com/library/mt420197.aspx) 를 제공합니다. 다른 원격 분석 모듈은 이를 사용하고 사용자 또한 [사용자 고유의 원격 분석을 정의하는 데 사용](../../azure-monitor/app/api-custom-events-metrics.md)할 수 있습니다.

* ApplicationInsights.config에 항목이 없습니다.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet 패키지. 이 NuGet을 설치하면 .config 파일이 생성되지 않습니다.

## <a name="telemetry-channel"></a>원격 분석 채널
원격 분석 채널은 Application Insights 서비스에 대한 원격 분석의 버퍼링 및 전송을 관리합니다.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` 는 서비스에 대한 기본 채널입니다. 메모리에 데이터를 버퍼링합니다.
* `Microsoft.ApplicationInsights.PersistenceChannel`는 콘솔 애플리케이션에 대한 대안입니다. 앱을 닫을 때 플러시되지 않은 데이터를 영구 저장소로 저장하고 앱이 다시 시작되면 전송합니다.

## <a name="telemetry-initializers-aspnet"></a>원격 분석 이니셜라이저(ASP.NET)
원격 분석 이니셜라이저는 원격 분석의 모든 항목과 함께 전송되는 컨텍스트 속성을 설정합니다.

[고유한 이니셜라이저를 작성](../../azure-monitor/app/api-filtering-sampling.md#add-properties) 하여 컨텍스트 속성을 설정할 수 있습니다.

표준 이니셜라이저는 웹 또는 Windows Server NuGet 패키지에서 모두 설정합니다.

* `AccountIdTelemetryInitializer` 는 AccountId 속성을 설정합니다.
* `AuthenticatedUserIdTelemetryInitializer`는 JavaScript SDK에서 설정한 AuthenticatedUserId 속성을 설정합니다.
* `AzureRoleEnvironmentTelemetryInitializer`은 `RoleName` 및 Azure 런타임 환경에서 추출된 정보를 사용하여 모든 원격 분석 항목에 대해 `Device` 컨텍스트에 대한 `RoleInstance` 속성을 업데이트합니다.
* `BuildInfoConfigComponentVersionTelemetryInitializer`은 MS 빌드에 의해 생성된 `BuildInfo.config` 파일로부터 추출된 값을 사용하여 모든 원격 분석 항목에 대한 `Component` 컨텍스트의 `Version` 속성을 업데이트합니다.
* `ClientIpHeaderTelemetryInitializer`은 `X-Forwarded-For` HTTP 헤더 기반의 모든 원격 분석 항목의 `Location` 컨텍스트의 `Ip` 속성을 업데이트합니다.
* `DeviceTelemetryInitializer`은 모든 원격 분석 항목에 대한 `Device` 컨텍스트의 다음 속성을 업데이트합니다.
  * `Type`은 "PC"로 설정됩니다.
  * `Id`은 웹 애플리케이션이 실행되고 있는 컴퓨터의 도메인 이름으로 설정됩니다.
  * `OemName`은 WMI를 사용하여 `Win32_ComputerSystem.Manufacturer` 필드에서 추출된 값으로 설정됩니다.
  * `Model`은 WMI를 사용하여 `Win32_ComputerSystem.Model` 필드에서 추출된 값으로 설정됩니다.
  * `NetworkType`은 `NetworkInterface`에서 추출된 값으로 설정됩니다.
  * `Language`은 `CurrentCulture`의 이름으로 설정됩니다.
* `DomainNameRoleInstanceTelemetryInitializer`은 웹 애플리케이션이 실행되는 컴퓨터의 도메인 이름을 사용하여 모든 원격 분석 항목에 대해 `Device` 컨텍스트의 `RoleInstance` 속성을 업데이트합니다.
* `OperationNameTelemetryInitializer`은 `RequestTelemetry`의 `Name` 속성과 HTTP 메서드를 기반으로 한 모든 원격 분석 아이템의 `Operation` 컨텍스트의 `Name` 속성을 업데이트뿐만 아니라 ASP.NET MVC 컨트롤러와 요청을 처리하는 데 작업을 불러옵니다.
* `OperationIdTelemetryInitializer` 또는 `OperationCorrelationTelemetryInitializer`는 자동으로 생성된 `RequestTelemetry.Id`를 사용하여 요청을 처리하는 동안 추적된 모든 원격 분석 항목의 `Operation.Id` 컨텍스트 속성을 업데이트합니다.
* `SessionTelemetryInitializer`은 사용자의 브라우저에서 실행되는 ApplicationInsights JavaScript 계측 코드에 의해 제공된 `ai_session` 쿠키의 추출된 값을 사용하여 모든 원격 분석 항목에 대한 `Session` 컨텍스트의 `Id` 속성을 업데이트합니다.
* `SyntheticTelemetryInitializer` 또는 `SyntheticUserAgentTelemetryInitializer`는 가용성 테스트 또는 검색 엔진 봇과 같은 가상 원본에서 요청을 처리하는 경우, 모든 원격 분석 항목의 `User`, `Session` 및 `Operation` 컨텍스트 속성을 업데이트합니다. 기본적으로 [메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md) 는 가상 원격 분석을 표시하지 않습니다.

    `<Filters>` 는 요청의 식별 속성을 설정합니다.
* `UserTelemetryInitializer`은 사용자의 브라우저에서 실행되는 Application insights JavaScript 계측 코드에 의해 제공된 `ai_user` 쿠키의 추출된 값을 사용하여 모든 원격 분석 항목에 대한 `User` 컨텍스트의 `Id` 및 `AcquisitionDate`속성을 업데이트합니다.
* `WebTestTelemetryInitializer` 는 [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md)의 HTTP 요청에 대한 사용자 ID, 세션 ID 및 가상 원본 속성을 설정합니다.
  `<Filters>` 는 요청의 식별 속성을 설정합니다.

Service Fabric에서 실행되는 .NET 애플리케이션에 대해 `Microsoft.ApplicationInsights.ServiceFabric` NuGet 패키지를 포함할 수 있습니다. 이 패키지에는 Service Fabric 속성을 원격 분석 항목에 추가하는 `FabricTelemetryInitializer`가 포함되어 있습니다. 자세한 내용은 이 NuGet 패키지에 의해 추가된 속성에 대한 [GitHub 페이지](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md)를 참조하세요.

## <a name="telemetry-processors-aspnet"></a>원격 분석 프로세서(ASP.NET)
원격 분석 프로세서는 각 원격 분석 항목을 SDK에서 포털에 보내기 전에 필터링하고 수정할 수 있습니다.

[고유한 원격 분석 프로세서를 작성](../../azure-monitor/app/api-filtering-sampling.md#filtering)할 수 있습니다.

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>적응 샘플링 원격 분석 프로세서(2.0.0-beta3부터)
이 옵션은 기본적으로 사용하도록 설정되어 있습니다. 앱에서 다양한 원격 분석을 보내는 경우 이 프로세서는 일부 정보를 제거합니다.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

매개 변수는 알고리즘을 달성하려고 하는 대상을 제공합니다. SDK의 각 인스턴스가 독립적으로 작동하므로 서버가 여러 컴퓨터의 클러스터인 경우 원격 분석의 실제 볼륨을 적절하게 곱합니다.

[샘플링에 대해 자세히 알아봅니다](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>고정 비율 샘플링 원격 분석 프로세서(2.0.0-beta1부터)
또한 표준 [샘플링 원격 분석 프로세서](../../azure-monitor/app/api-filtering-sampling.md) 도 있습니다(2.0.1부터).

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>채널 매개 변수(Java)
이러한 매개 변수는 Java SDK가 수집한 원격 분석 데이터를 저장하고 플러시하는 방법에 영향을 줍니다.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
SDK의 메모리 내 저장소에 저장할 수 있는 원격 분석 항목의 수입니다. 이 수에 도달하면 원격 분석 버퍼가 플러시됩니다. 즉, 원격 분석 항목이 Application Insights 서버로 전송됩니다.

* 최소: 1
* 최대: 1000
* Default: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
메모리 내 스토리지에 저장된 데이터가 플러시될(Application Insights로 보내는) 빈도를 결정합니다.

* 최소: 1
* 최대: 300
* Default: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
로컬 디스크에서 영구 저장소에 할당되는 최대 크기(MB)를 결정합니다. 이 스토리지는 Application Insights 엔드포인트로 전송하지 못한 원격 분석 항목을 유지하는 데 사용됩니다. 저장소 크기를 충족하면 새 원격 분석 항목이 삭제됩니다.

* 최소: 1
* 최대: 100
* Default: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

#### <a name="local-forwarder"></a>로컬 전달자

[로컬 전달자](opencensus-local-forwarder.md)는 다양한 SDK 및 프레임워크에서 Application Insights 또는 [OpenCensus](https://opencensus.io/) 원격 분석을 수집하고 Application Insights로 경로를 지정하는 에이전트입니다. Windows 및 Linux에서 실행할 수 있습니다. 로컬 전달자는 Application Insights Java SDK와 함께 사용하는 경우 [라이브 메트릭](../../azure-monitor/app/live-stream.md) 및 적응 샘플링을 완벽하게 지원합니다.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>

<!-- The properties below are optional. The values shown are the defaults for each property -->

<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

SpringBoot 스타터를 사용하는 경우 구성 파일(application.properties)에 다음을 추가합니다.

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

기본값은 SpringBoot application.properties 및 applicationinsights.xml 구성에 모두 동일합니다.

## <a name="instrumentationkey"></a>InstrumentationKey
데이터가 표시되는 Application Insights 리소스를 결정합니다. 일반적으로 각 애플리케이션에 대해 별도 키를 사용하여 별도 리소스를 만듭니다.

키를 동적으로 설정하려는 경우, 예를 들어 애플리케이션의 결과를 다른 리소스로 보내려는 경우 구성 파일에서 키를 생략하고 대신 코드에 설정할 수 있습니다.

표준 원격 분석 모듈을 비롯한 TelemetryClient의 모든 인스턴스에 대한 키를 설정하려면 TelemetryConfiguration.Active에 키를 설정합니다. ASP.NET 서비스의 global.aspx.cs와 같은 초기화 메서드에 키를 설정합니다.

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      //...
```

특정 이벤트 집합을 다른 리소스로 보내려는 경우 특정 TelemetryClient에 대한 키를 설정할 수 있습니다.

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

새 키를 얻으려면 [Application Insights 포털에서 새 리소스를 만듭니다][new].



## <a name="applicationid-provider"></a>애플리케이션 ID 공급자

_v2.6.0에서 시작 가능_

이 공급자의 목적은 계측 키에 따라 애플리케이션 ID를 조회하는 것입니다. 애플리케이션 ID는 RequestTelemetry 및 DependencyTelemetry에 포함되며 포털에서 상관 관계를 결정하는 데 사용됩니다.

이는 코드 또는 구성에서 `TelemetryConfiguration.ApplicationIdProvider`를 설정하여 사용할 수 있습니다.

### <a name="interface-iapplicationidprovider"></a>인터페이스: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) sdk에서 `ApplicationInsightsApplicationIdProvider` 및 `DictionaryApplicationIdProvider`라는 두 가지 구현을 제공합니다.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

프로필 API에 대한 래퍼입니다. 요청을 스로틀하고 결과를 캐시합니다.

이 공급자는 [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) 또는 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 중 하나를 설치할 때 사용자 구성 파일에 추가됩니다.

이 클래스에는 선택적 속성 `ProfileQueryEndpoint`가 있습니다.
기본적으로 `https://dc.services.visualstudio.com/api/profiles/{0}/appId`로 설정되어 있습니다.
이 구성에 대한 프록시를 구성해야 하는 경우 기준 주소를 프록시하고 "/api/profiles/{0}/appId"를 포함하는 것이 좋습니다. 해당 '{0}'는 요청에 따라 런타임에 계측 키로 대체됩니다.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>ApplicationInsights.config를 통해 구성 예제:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>코드를 통한 구성 예제:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

구성된 계측 키/애플리케이션 ID 쌍을 사용하는 정적 공급자입니다.

이 클래스에는 `Defined` 속성이 있으며, 이는 계측 키/애플리케이션 ID 쌍의 사전&lt;string,string&gt;입니다.

이 클래스에는 선택적 속성 `Next`가 있습니다. 이는 구성에서 존재하지 않는 계측 키가 요청되는 경우 사용할 다른 공급자를 구성하는 데 사용할 수 있습니다.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>ApplicationInsights.config를 통해 구성 예제:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>코드를 통한 구성 예제:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>다음 단계
[API에 대해 자세히 알아보세요][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
