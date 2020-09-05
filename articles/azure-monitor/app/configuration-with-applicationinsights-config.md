---
title: ApplicationInsights.config 참조 - Azure | Microsoft Docs
description: 데이터 수집 모듈을 사용하거나 사용하지 않도록 설정하고 성능 카운터 및 기타 매개 변수를 추가합니다.
ms.topic: conceptual
ms.date: 05/22/2019
ms.custom: devx-track-csharp
ms.reviewer: olegan
ms.openlocfilehash: 7c0759e78b1adc1704acb602daa12cf9cabbe153
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934806"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>ApplicationInsights.config 또는 .xml로 Application Insights SDK 구성
Application Insights .NET SDK는 NuGet 패키지의 숫자로 구성됩니다. [코어 패키지](https://www.nuget.org/packages/Microsoft.ApplicationInsights) Application Insights에 원격 분석을 보내는 경우에 API를 제공합니다. [추가 패키지](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights)는 해당 컨텍스트 및 애플리케이션에서 원격 분석을 자동으로 추적하기 위해 원격 분석 *모듈* 및 *이니셜라이저*를 제공합니다. 구성 파일을 조정 하 여 원격 분석 모듈 및 이니셜라이저를 사용 하거나 사용 하지 않도록 설정 하 고 그 중 일부에 대 한 매개 변수를 설정할 수 있습니다.

구성 파일의 이름은 애플리케이션 유형에 따라 `ApplicationInsights.config` 또는 `ApplicationInsights.xml`입니다. [대부분 버전의 SDK는 설치][start]할 때 프로젝트에 자동으로 추가됩니다. 기본적으로 **> Application Insights 원격 분석 추가**를 지 원하는 Visual Studio 템플릿 프로젝트에서 자동화 된 환경을 사용 하는 경우 ApplicationInsights.config 파일은 프로젝트 루트 폴더에 만들어지고 컴파일 시 bin 폴더에 복사 됩니다. 또한 [IIS 서버에서 상태 모니터][redfield]하 여 웹 앱에 추가 됩니다. Azure [웹 사이트](azure-web-apps.md) 또는 [azure VM 및 가상 머신 확장 집합에](azure-vm-vmss-apps.md) 대 한 확장이 사용 되는 경우 구성 파일은 무시 됩니다.

[웹 페이지에서 SDK][client]를 제어할 동급의 파일은 없습니다.

이 문서는 구성 파일에서 참조하는 섹션, SDK의 구성 요소를 제어하는 방법 및 해당 구성 요소를 로드하는 NuGet 패키지를 설명합니다.

> [!NOTE]
> ApplicationInsights.config 및 .xml 지침은 .NET Core SDK에 적용되지 않습니다. .NET Core 응용 프로그램을 구성 하려면 [이](./asp-net-core.md) 가이드를 따르세요.

## <a name="telemetry-modules-aspnet"></a>원격 분석 모듈(ASP.NET)
각 원격 분석 모듈은 특정 유형의 데이터를 수집 하 고 핵심 API를 사용 하 여 데이터를 보냅니다. 모듈은 다른 NuGet 패키지에 의해 설치되며 이는 .config 파일에 필요한 줄을 추가합니다.

각 모듈에 대해 구성 파일에 노드가 있습니다. 모듈을 사용하지 않으려면 노드를 삭제하거나 주석으로 처리합니다.

### <a name="dependency-tracking"></a>종속성 추적
[종속성 추적](./asp-net-dependencies.md) 은 앱이 데이터베이스 및 외부 서비스와 데이터베이스에 수행하는 호출에 대한 원격 분석을 수집합니다. 이 모듈이 IIS 서버에서 작동하도록 하려면 [상태 모니터를 설치][redfield]해야 합니다.

[TrackDependency API](./api-custom-events-metrics.md#trackdependency)를 사용하여 종속성 추적 코드를 작성할 수 있습니다.

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet 패키지.

에이전트 기반 (코드 없는) 연결을 사용 하 여 코드를 수정 하지 않고 종속성을 자동으로 수집할 수 있습니다. Azure 웹 앱에서 사용 하려면 [Application Insights 확장](azure-web-apps.md)을 사용 하도록 설정 합니다. Azure VM 또는 Azure virtual machine scale set에서 사용 하려면 [VM 및 가상 머신 확장 집합에 대 한 응용 프로그램 모니터링 확장](azure-vm-vmss-apps.md)을 사용 하도록 설정 합니다.

### <a name="performance-collector"></a>성능 수집기
IIS 설치에서 CPU, 메모리, 네트워크 로드 등의 [시스템 성능 카운터를 수집](./performance-counters.md) 합니다. 사용자가 직접 설정한 성능 카운터를 포함하여 어떤 카운터를 수집할지 지정할 수 있습니다.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet 패키지.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights 진단 원격 분석
`DiagnosticsTelemetryModule`은 Application Insights instrumenation 코드 자체에 오류를 보고합니다. 예를 들어 코드가 성능 카운터에 액세스할 수 없는 경우 또는 `ITelemetryInitializer` 를 throw하는 경우는 예외입니다. 이 모듈에서 추적된 원격 분석 추적은 [진단 검색][diagnostic]에 표시됩니다.

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

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
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -관찰 되지 않은 작업 예외를 추적 합니다.
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - 작업자 역할, Windows 서비스 및 콘솔 애플리케이션에 대한 처리되지 않은 예외를 추적합니다.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet 패키지.

### <a name="eventsource-tracking"></a>EventSource 추적
`EventSourceTelemetryModule`을 사용하여 Application Insights에 전송될 EventSource 이벤트를 구성할 수 있습니다. EventSource 이벤트 추적에 대한 자세한 내용은 [EventSource 이벤트 사용](./asp-net-trace-logs.md#use-eventsource-events)을 참조하세요.

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW 이벤트 추적
`EtwCollectorTelemetryModule`을 사용하여 Application Insights에 추적으로 전송할 ETW 공급자의 이벤트를 구성할 수 있습니다. ETW 이벤트 추적에 대한 자세한 내용은 [ETW 이벤트 사용](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events)을 참조하세요.

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Microsoft.ApplicationInsights 패키지는 SDK의 [코어 API](/dotnet/api/microsoft.applicationinsights?view=azure-dotnet) 를 제공합니다. 다른 원격 분석 모듈은이를 사용 하 고 사용자 [고유의 원격 분석을 정의 하](./api-custom-events-metrics.md)는 데 사용할 수도 있습니다.

* ApplicationInsights.config에 항목이 없습니다.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet 패키지. 이 NuGet을 설치하면 .config 파일이 생성되지 않습니다.

## <a name="telemetry-channel"></a>원격 분석 채널
[원격 분석 채널](telemetry-channels.md) 은 Application Insights 서비스에 대 한 원격 분석의 버퍼링 및 전송을 관리 합니다.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` 는 웹 응용 프로그램의 기본 채널입니다. 데이터를 메모리에 버퍼링 하 고 재시도 메커니즘과 로컬 디스크 저장소를 활용 하 여 보다 안정적인 원격 분석 배달을 제공 합니다.
* `Microsoft.ApplicationInsights.InMemoryChannel` 는 다른 채널이 구성 되지 않은 경우에 사용 되는 경량 원격 분석 채널입니다. 

## <a name="telemetry-initializers-aspnet"></a>원격 분석 이니셜라이저(ASP.NET)
원격 분석 이니셜라이저는 원격 분석의 모든 항목과 함께 전송 되는 컨텍스트 속성을 설정 합니다.

[고유한 이니셜라이저를 작성](./api-filtering-sampling.md#add-properties) 하여 컨텍스트 속성을 설정할 수 있습니다.

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
* `SyntheticTelemetryInitializer` 또는 `SyntheticUserAgentTelemetryInitializer`는 가용성 테스트 또는 검색 엔진 봇과 같은 가상 원본에서 요청을 처리하는 경우, 모든 원격 분석 항목의 `User`, `Session` 및 `Operation` 컨텍스트 속성을 업데이트합니다. 기본적으로 [메트릭 탐색기](../platform/metrics-charts.md) 는 가상 원격 분석을 표시하지 않습니다.

    `<Filters>` 는 요청의 식별 속성을 설정합니다.
* `UserTelemetryInitializer`은 사용자의 브라우저에서 실행되는 Application insights JavaScript 계측 코드에 의해 제공된 `ai_user` 쿠키의 추출된 값을 사용하여 모든 원격 분석 항목에 대한 `User` 컨텍스트의 `Id` 및 `AcquisitionDate`속성을 업데이트합니다.
* `WebTestTelemetryInitializer`[가용성 테스트](./monitor-web-app-availability.md)에서 제공 하는 HTTP 요청에 대 한 사용자 ID, 세션 ID 및 가상 원본 속성을 설정 합니다.
  `<Filters>` 는 요청의 식별 속성을 설정합니다.

Service Fabric에서 실행되는 .NET 애플리케이션에 대해 `Microsoft.ApplicationInsights.ServiceFabric` NuGet 패키지를 포함할 수 있습니다. 이 패키지에는 Service Fabric 속성을 원격 분석 항목에 추가하는 `FabricTelemetryInitializer`가 포함되어 있습니다. 자세한 내용은 이 NuGet 패키지에 의해 추가된 속성에 대한 [GitHub 페이지](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md)를 참조하세요.

## <a name="telemetry-processors-aspnet"></a>원격 분석 프로세서(ASP.NET)
원격 분석 프로세서는 SDK에서 포털로 전송 되기 직전에 각 원격 분석 항목을 필터링 하 고 수정할 수 있습니다.

사용자 [고유의 원격 분석 프로세서를 작성할](./api-filtering-sampling.md#filtering)수 있습니다.

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>적응 샘플링 원격 분석 프로세서 (2.0.0-beta3)
이 옵션은 기본적으로 사용하도록 설정되어 있습니다. 앱에서 다양한 원격 분석을 보내는 경우 이 프로세서는 일부 정보를 제거합니다.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

매개 변수는 알고리즘을 달성하려고 하는 대상을 제공합니다. SDK의 각 인스턴스가 독립적으로 작동하므로 서버가 여러 컴퓨터의 클러스터인 경우 원격 분석의 실제 볼륨을 적절하게 곱합니다.

[샘플링에 대해 자세히 알아보세요](./sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>고정 률 샘플링 원격 분석 프로세서 (2.0.0-beta1)
또한 표준 [샘플링 원격 분석 프로세서](./api-filtering-sampling.md) (2.0.1에서)가 있습니다.

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

## <a name="instrumentationkey"></a>InstrumentationKey
데이터가 표시되는 Application Insights 리소스를 결정합니다. 일반적으로 각 애플리케이션에 대해 별도 키를 사용하여 별도 리소스를 만듭니다.

키를 동적으로 설정하려는 경우, 예를 들어 애플리케이션의 결과를 다른 리소스로 보내려는 경우 구성 파일에서 키를 생략하고 대신 코드에 설정할 수 있습니다.

표준 원격 분석 모듈을 포함 하 여 TelemetryClient의 모든 인스턴스에 대 한 키를 설정 합니다. ASP.NET 서비스의 global.aspx.cs와 같은 초기화 메서드에 키를 설정합니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
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

[api]: ./api-custom-events-metrics.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: ./asp-net-exceptions.md
[netlogs]: ./asp-net-trace-logs.md
[new]: ./create-new-resource.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

