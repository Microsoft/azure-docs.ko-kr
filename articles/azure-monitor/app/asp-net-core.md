---
title: ASP.NET Core 응용 프로그램에 대 한 Azure 애플리케이션 정보 | Microsoft Docs
description: ASP.NET Core 웹 애플리케이션의 가용성, 성능 및 사용량을 모니터링합니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/30/2020
ms.openlocfilehash: 719bf997254c98c5790d6d6733982fea08541967
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936523"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core 응용 프로그램에 대 한 Application Insights

이 문서에서는 [ASP.NET Core](/aspnet/core) 응용 프로그램에 대해 Application Insights를 사용 하도록 설정 하는 방법을 설명 합니다. 이 문서의 지침을 완료 하면 Application Insights는 ASP.NET Core 응용 프로그램에서 요청, 종속성, 예외, 성능 카운터, 하트 비트 및 로그를 수집 합니다.

여기서 사용 하는 예제는를 대상으로 하는 [MVC 응용 프로그램](/aspnet/core/tutorials/first-mvc-app) 입니다 `netcoreapp3.0` . 모든 ASP.NET Core 응용 프로그램에 이러한 지침을 적용할 수 있습니다. [Worker 서비스](/aspnet/core/fundamentals/host/hosted-services#worker-service-template)를 사용 하는 경우 [여기](./worker-service.md)에 있는 지침을 사용 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

[ASP.NET Core에 대 한 APPLICATION INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 는 응용 프로그램의 실행 위치에 관계 없이 응용 프로그램을 모니터링할 수 있습니다. 응용 프로그램이 실행 중이 고 Azure에 네트워크로 연결 되어 있는 경우 원격 분석을 수집할 수 있습니다. Application Insights 모니터링은 .NET Core가 지원 되는 모든 위치에서 지원 됩니다. 지원 내용:
* **운영 체제**: Windows, Linux 또는 Mac
* **호스팅 메서드**: In process 또는 out of process
* **배포 방법**: 프레임워크 종속 또는 자체 포함
* **웹 서버**: IIS(Internet Information Server) 또는 Kestrel
* **호스팅 플랫폼**: Azure App Service, Azure VM, Docker, AKS(Azure Kubernetes Service) 등의 Web Apps 기능
* **.Net Core 런타임 버전**: 1xx, 2. xx 또는 3. xx
* **IDE**: Visual Studio, VS Code 또는 명령줄.

> [!NOTE]
> ASP.NET Core 3.x에는 [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) 이상이 필요 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 작동 하는 ASP.NET Core 응용 프로그램입니다. ASP.NET Core 응용 프로그램을 만들어야 하는 경우이 [ASP.NET Core 자습서](/aspnet/core/getting-started/)를 따르세요.
- 유효한 Application Insights 계측 키입니다. Application Insights에 원격 분석을 보내려면이 키가 필요 합니다. 계측 키를 가져오기 위해 새 Application Insights 리소스를 만들어야 하는 경우 [Application Insights 리소스 만들기](./create-new-resource.md)를 참조 하세요.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>서버 쪽 원격 분석 Application Insights 사용 (Visual Studio)

Mac용 Visual Studio [수동 지침](#enable-application-insights-server-side-telemetry-no-visual-studio)을 사용 합니다. Windows 버전의 Visual Studio 에서만이 프로시저를 지원 합니다.

1. Visual Studio에서 프로젝트를 엽니다.

    > [!TIP]
    > 원할 경우 프로젝트에 대 한 소스 제어를 설정 하 여 Application Insights에서 수행 하는 모든 변경 내용을 추적할 수 있습니다. 소스 제어를 사용 하도록 설정 하려면 **파일**  >  **원본 제어에 추가**를 선택 합니다.

2. **프로젝트**  >  **Application Insights 원격 분석 추가**를 선택 합니다.

3. **시작**을 선택합니다. 이 선택의 텍스트는 사용자의 Visual Studio 버전에 따라 다를 수 있습니다. 일부 이전 버전에서는 대신 **시작** 사용 단추를 사용 합니다.

4. 구독을 선택합니다. 그런 다음 **리소스**  >  **레지스터**를 선택 합니다.

5. 프로젝트에 Application Insights를 추가한 후 최신 안정적인 SDK 릴리스를 사용 하 고 있는지 확인 합니다. **Project**  >  **NuGet 패키지 관리**  >  **AspNetCore**로 이동 합니다. 필요한 경우 **업데이트**를 선택 합니다.

     ![업데이트할 Application Insights 패키지를 선택할 수 있는 위치를 보여 주는 스크린샷](./media/asp-net-core/update-nuget-package.png)

6. 선택적 팁을 따르고 프로젝트를 소스 제어에 추가한 경우 **View**  >  **팀 탐색기**  >  **변경 내용**보기로 이동 합니다. 그런 다음 각 파일을 선택 하 Application Insights 원격 분석에서 변경한 내용에 대 한 차이 보기를 확인 합니다.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>서버 쪽 원격 분석 Application Insights 사용 (Visual Studio 없음)

1. [ASP.NET Core에 대 한 APPLICATION INSIGHTS SDK NuGet 패키지](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)를 설치 합니다. 항상 안정적인 최신 버전을 사용 하는 것이 좋습니다. [오픈 소스 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-dotnet/releases)에서 SDK에 대 한 전체 릴리스 정보를 찾습니다.

    다음 코드 샘플에서는 프로젝트의 파일에 추가 되는 변경 내용을 보여 줍니다 `.csproj` .

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. `services.AddApplicationInsightsTelemetry();` `ConfigureServices()` `Startup` 다음 예제와 같이 클래스의 메서드에를 추가 합니다.

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. 계측 키를 설정 합니다.

    계측 키를에 대 한 인수로 제공할 수 있지만 `AddApplicationInsightsTelemetry` 구성에서 계측 키를 지정 하는 것이 좋습니다. 다음 코드 샘플에서는에서 계측 키를 지정 하는 방법을 보여 줍니다 `appsettings.json` . `appsettings.json`게시 하는 동안이 응용 프로그램 루트 폴더에 복사 되었는지 확인 합니다.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    또는 다음 환경 변수 중 하나에서 계측 키를 지정 합니다.

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    예를 들면

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY` 는 일반적으로 [Azure Web Apps](./azure-web-apps.md?tabs=net)에서 사용 되지만이 SDK가 지원 되는 모든 위치에서 사용 될 수도 있습니다. 코드 없는 웹 앱 모니터링을 수행 하는 경우 연결 문자열을 사용 하지 않는 경우이 형식이 필요 합니다.

    계측 키를 설정 하는 대신 [연결 문자열](./sdk-connection-string.md?tabs=net)도 사용할 수 있습니다.

    > [!NOTE]
    > 코드에 지정 된 계측 키가 `APPINSIGHTS_INSTRUMENTATIONKEY` 다른 옵션을 통해 적용 되는 환경 변수를 통해 이깁니다.

### <a name="user-secrets-and-other-configuration-providers"></a>사용자 암호 및 기타 구성 공급자

계측 키를 ASP.NET Core 사용자 암호에 저장 하거나 다른 구성 공급자에서 검색 하려면 매개 변수와 함께 오버 로드를 사용할 수 있습니다 `Microsoft.Extensions.Configuration.IConfiguration` . 예: `services.AddApplicationInsightsTelemetry(Configuration);`

## <a name="run-your-application"></a>애플리케이션 실행

응용 프로그램을 실행 하 고 요청을 만듭니다. 이제 원격 분석이 Application Insights로 흐릅니다. Application Insights SDK는 다음 원격 분석과 함께 응용 프로그램에 대 한 들어오는 웹 요청을 자동으로 수집 합니다.

### <a name="live-metrics"></a>라이브 메트릭

[라이브 메트릭은](./live-stream.md) Application Insights 모니터링이 올바르게 구성 되었는지 여부를 신속 하 게 확인 하는 데 사용할 수 있습니다. 포털이 포털 및 분석에 표시 되기 시작 하는 데 몇 분 정도 걸릴 수 있지만, 라이브 메트릭은 거의 실시간으로 실행 중인 프로세스의 CPU 사용량을 표시 합니다. 요청, 종속성, 추적 등의 다른 원격 분석도 표시할 수 있습니다.

### <a name="ilogger-logs"></a>ILogger 로그

`ILogger`심각도 이상을 통해 내보낸 로그 `Warning` 는 자동으로 캡처됩니다. [ILogger 문서](ilogger.md#control-logging-level) 에 따라 Application Insights에서 캡처할 로그 수준을 사용자 지정할 수 있습니다.

### <a name="dependencies"></a>종속성

종속성 컬렉션은 기본적으로 사용 하도록 설정 되어 있습니다. [이](asp-net-dependencies.md#automatically-tracked-dependencies) 문서에서는 자동으로 수집 되는 종속성에 대해 설명 하 고 수동 추적을 수행 하는 단계를 포함 합니다.

### <a name="performance-counters"></a>성능 카운터

ASP.NET Core의 [성능 카운터](./web-monitor-performance.md) 에 대 한 지원은 제한 되어 있습니다.

* SDK 버전 2.4.1 이상에서는 애플리케이션이 Azure Web Apps(Windows)에서 실행되는 경우 성능 카운터를 수집합니다.
* SDK 버전 2.7.1 이상에서는 애플리케이션이 Windows에서 실행되고 `NETSTANDARD2.0` 이상을 대상으로 하는 경우 성능 카운터를 수집합니다.
* .NET Framework를 대상으로 하는 애플리케이션의 경우 모든 버전의 SDK에서 성능 카운터를 지원합니다.
* SDK 버전 2.8.0 이상에서는 Linux의 cpu/메모리 카운터를 지원합니다. 다른 카운터는 Linux에서 지원되지 않습니다. Linux 및 기타 비 Windows 환경에서 시스템 카운터를 가져오는 권장 방법은 [EventCounters](#eventcounter)를 사용하는 것입니다.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` 는 기본적으로 사용 하도록 설정 되며 .NET Core 2.x 앱에서 기본 카운터 집합을 수집 합니다. [Eventcounter](eventcounters.md) 자습서에는 수집 된 카운터의 기본 집합이 나열 됩니다. 또한 목록을 사용자 지정 하는 방법에 대 한 지침도 있습니다.

## <a name="enable-client-side-telemetry-for-web-applications"></a>웹 응용 프로그램에 대 한 클라이언트 쪽 원격 분석 사용

위의 단계는 서버측 원격 분석 수집을 시작 하는 데 충분 합니다. 응용 프로그램에 클라이언트 쪽 구성 요소가 있는 경우 다음 단계에 따라 [사용 원격 분석](./usage-overview.md)수집을 시작 합니다.

1. 에서 `_ViewImports.cshtml` 삽입을 추가 합니다.

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. 에서 `_Layout.cshtml` `HtmlHelper` 섹션의 끝에, `<head>` 다른 스크립트 앞에를 삽입 합니다. 페이지에서 사용자 지정 JavaScript 원격 분석을 보고 하려는 경우이 코드 조각 뒤에 삽입 합니다.

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
`FullScript`SDK v 2.14부터를 사용 하 여를 사용할 수도 있습니다 `ScriptBody` . 콘텐츠 보안 정책을 설정 하는 태그를 제어 해야 하는 경우 다음을 사용 합니다 `<script>` .

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

`.cshtml`이전에 참조 한 파일 이름은 기본 MVC 응용 프로그램 템플릿에서 가져온 것입니다. 궁극적으로 응용 프로그램에 대 한 클라이언트 쪽 모니터링을 적절 하 게 사용 하려면 `<head>` 모니터링할 응용 프로그램의 각 페이지에 있는 섹션에 JavaScript 코드 조각이 표시 되어야 합니다. 에 JavaScript 코드 조각을 추가 하 여이 응용 프로그램 템플릿에 대 한이 목표를 달성할 수 있습니다 `_Layout.cshtml` . 

프로젝트에가 포함 되지 않은 경우 `_Layout.cshtml` 에도 [클라이언트 쪽 모니터링](./website-monitoring.md)을 추가할 수 있습니다. 이렇게 하려면 `<head>` 응용 프로그램 내 모든 페이지의을 제어 하는 동일한 파일에 JavaScript 코드 조각을 추가 합니다. 또는 여러 페이지에 코드 조각을 추가할 수 있지만이 솔루션은 유지 관리 하기 어렵고 일반적으로 권장 하지 않습니다.

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK 구성

ASP.NET Core에 대 한 Application Insights SDK를 사용자 지정 하 여 기본 구성을 변경할 수 있습니다. Application Insights ASP.NET SDK의 사용자는 또는을 (를) 사용 하 여 구성을 변경 하는 방법을 익힐 수 있습니다 `ApplicationInsights.config` `TelemetryConfiguration.Active` . ASP.NET Core에 대 한 구성을 다르게 변경 합니다. ASP.NET Core SDK를 응용 프로그램에 추가 하 고 ASP.NET Core 기본 제공 [종속성 주입](/aspnet/core/fundamentals/dependency-injection)을 사용 하 여 구성 합니다. `ConfigureServices()`달리 지정 하지 않는 한 클래스의 메서드에서 거의 모든 구성 변경을 수행 `Startup.cs` 합니다. 다음 섹션에서는 자세한 정보를 제공 합니다.

> [!NOTE]
> ASP.NET Core 응용 프로그램에서는를 수정 하 여 구성을 변경 하는 것이 `TelemetryConfiguration.Active` 지원 되지 않습니다.

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions 사용

`ApplicationInsightsServiceOptions`다음 예와 같이에 전달 하 여 몇 가지 일반적인 설정을 수정할 수 있습니다 `AddApplicationInsightsTelemetry` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

의 전체 설정 목록 `ApplicationInsightsServiceOptions`

|설정 | Description | 기본값
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | 사용/사용 안 함 `PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | 사용/사용 안 함 `RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | 사용/사용 안 함 `EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | 사용/사용 안 함 `DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  사용/사용 안 함 `AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  사용/사용 안 함 `AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | LiveMetrics 기능 사용/사용 안 함 | true
|EnableAdaptiveSampling | 적응 샘플링 사용/사용 안 함 | true
|EnableHeartbeat 비트 | 하트 비트 기능 사용/사용 안 함-주기적 (15 분 기본값)은 ' HeartbeatState ' 라는 사용자 지정 메트릭을 .NET 버전, Azure 환경 정보 (해당 하는 경우) 등의 런타임에 대 한 정보로 보냅니다. | true
|AddAutoCollectedMetricExtractor | 샘플링을 수행 하기 전에 요청/종속성에 대 한 미리 집계 된 메트릭을 전송 하는 TelemetryProcessor AutoCollectedMetrics 추출기를 사용/사용 안 함으로 설정 합니다. | true
|RequestCollectionOptions. 사이 예외 | 요청 수집 모듈에서 처리 되지 않은 예외 추적의 보고를 사용 하거나 사용 하지 않도록 설정 합니다. | ApplicationInsightsLoggerProvider를 사용 하 여 예외를 추적 하므로 NETSTANDARD 2.0의 경우 false이 고, 그렇지 않으면 true입니다.

최신 목록에 대해서는 [의 `ApplicationInsightsServiceOptions` 구성 가능한 설정을](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) 참조 하세요.

### <a name="sampling"></a>샘플링

ASP.NET Core에 대 한 Application Insights SDK는 고정 비율과 적응 샘플링을 모두 지원 합니다. 적응 샘플링은 기본적으로 사용 하도록 설정 되어 있습니다. 

자세한 내용은 [ASP.NET Core 응용 프로그램에 대 한 적응 샘플링 구성](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)을 참조 하세요.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers 추가

추가 정보를 사용 하 여 원격 분석을 보강 하려면 [원격 분석 이니셜라이저](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) 를 사용 합니다.

`TelemetryInitializer` `DependencyInjection` 다음 코드에 표시 된 것 처럼 컨테이너에 새를 추가 합니다. SDK는 `TelemetryInitializer` 컨테이너에 추가 된 모든를 자동으로 선택 합니다 `DependencyInjection` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers 제거

원격 분석 이니셜라이저는 기본적으로 제공 됩니다. 모든 또는 특정 원격 분석 이니셜라이저를 제거 하려면를 호출한 *후* 다음 샘플 코드를 사용 `AddApplicationInsightsTelemetry()` 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // Remove a specific built-in telemetry initializer
    var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                        (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
    if (tiToRemove != null)
    {
        services.Remove(tiToRemove);
    }

    // Remove all initializers
    // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
    services.RemoveAll(typeof(ITelemetryInitializer));
}
```

### <a name="adding-telemetry-processors"></a>원격 분석 프로세서 추가

에서 확장 메서드를 사용 하 여 사용자 지정 원격 분석 프로세서를에 추가할 수 있습니다 `TelemetryConfiguration` `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` . [고급 필터링 시나리오](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer)에서 원격 분석 프로세서를 사용 합니다. 다음 예제를 사용 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>기본 TelemetryModules 구성 또는 제거

Application Insights 원격 분석 모듈을 사용 하 여 사용자별 수동 추적을 요구 하지 않고 특정 작업에 대 한 유용한 원격 분석을 자동으로 수집 합니다.

다음 자동 컬렉션 모듈은 기본적으로 사용 하도록 설정 되어 있습니다. 이러한 모듈은 원격 분석을 자동으로 수집 합니다. 기본 동작을 변경 하도록 사용 하지 않도록 설정 하거나 구성할 수 있습니다.

* `RequestTrackingTelemetryModule` -들어오는 웹 요청에서 RequestTelemetry 분석을 수집 합니다.
* `DependencyTrackingTelemetryModule` -나가는 http 호출 및 sql 호출에서 [DependencyTelemetry](./asp-net-dependencies.md) 을 수집 합니다.
* `PerformanceCollectorModule` -Windows PerformanceCounters을 수집 합니다.
* `QuickPulseTelemetryModule` -라이브 메트릭 포털에 표시 하기 위한 원격 분석을 수집 합니다.
* `AppServicesHeartbeatTelemetryModule` -응용 프로그램이 호스트 되는 Azure App Service 환경에 대해 하트 비트 (사용자 지정 메트릭으로 전송 됨)를 수집 합니다.
* `AzureInstanceMetadataTelemetryModule` -응용 프로그램이 호스트 되는 Azure VM 환경에 대해 하트 비트 (사용자 지정 메트릭으로 전송 됨)를 수집 합니다.
* `EventCounterCollectionModule` - [Eventcounters를 수집 합니다.](eventcounters.md) 이 모듈은 새로운 기능이 며 SDK 버전 2.8.0 이상에서 사용할 수 있습니다.

기본값을 구성 하려면 `TelemetryModule` `ConfigureTelemetryModule<T>` `IServiceCollection` 다음 예제와 같이에 확장 메서드를 사용 합니다.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Clear();
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

2.12.2 버전부터는 [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) 기본 모듈을 사용 하지 않도록 설정 하는 간편한 옵션을 포함 합니다.

### <a name="configuring-a-telemetry-channel"></a>원격 분석 채널 구성

기본 [원격 분석 채널](./telemetry-channels.md) 은 `ServerTelemetryChannel` 입니다. 다음 예제와 같이이를 재정의할 수 있습니다.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>동적 원격 분석 사용 안 함

조건부로 또는 동적으로 원격 분석을 사용 하지 않도록 설정 하려는 경우 `TelemetryConfiguration` 코드의 어느 곳에서 나 ASP.NET Core 종속성 주입 컨테이너를 사용 하 여 인스턴스를 확인 하 고 플래그를 설정할 수 있습니다 `DisableTelemetry` .

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

위의 경우 자동 수집 모듈이 원격 분석을 수집 하는 것을 방지할 수 없습니다. Application Insights에 대 한 원격 분석 보내기가 위의 방법으로 사용 하지 않도록 설정 됩니다. 특정 자동 수집 모듈을 원하지 않는 경우 [원격 분석 모듈을 제거](#configuring-or-removing-default-telemetrymodules) 하는 것이 좋습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-application-insights-support-aspnet-core-3x"></a>는 ASP.NET Core 3(sp3)을 지원 Application Insights?

예. ASP.NET Core 버전 2.8.0 이상 [에 대 한 APPLICATION INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 로 업데이트 합니다. 이전 버전의 SDK는 ASP.NET Core 3(sp3)을 지원 하지 않습니다.

또한 [여기](#enable-application-insights-server-side-telemetry-visual-studio)에서 visual studio 기반 지침을 사용 하는 경우 최신 버전의 visual studio 2019 (16.3.0)를 온보드로 업데이트 합니다. 이전 버전의 Visual Studio에서는 ASP.NET Core 2.x 앱에 대 한 자동 등록을 지원 하지 않습니다.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>자동으로 수집 되지 않는 원격 분석을 추적 하려면 어떻게 해야 하나요?

`TelemetryClient`생성자 주입을 사용 하 여의 인스턴스를 가져온 다음 필요한 메서드를 호출 `TrackXXX()` 합니다. `TelemetryClient`ASP.NET Core 응용 프로그램에서는 새 인스턴스를 만들지 않는 것이 좋습니다. 의 단일 인스턴스는 `TelemetryClient` 나머지 원격 분석과 공유 하는 컨테이너에 이미 등록 되어 `DependencyInjection` `TelemetryConfiguration` 있습니다. 새 인스턴스를 만드는 것 `TelemetryClient` 은 원격 분석의 나머지 부분과 분리 된 구성이 필요한 경우에만 권장 됩니다.

다음 예제에서는 컨트롤러에서 추가 원격 분석을 추적 하는 방법을 보여 줍니다.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Application Insights의 사용자 지정 데이터 보고에 대 한 자세한 내용은 [Application Insights 사용자 지정 메트릭 API 참조](./api-custom-events-metrics.md)를 참조 하세요. [Getmetric API](./get-metric.md)를 사용 하 여 Application Insights에 사용자 지정 메트릭을 전송 하는 데 유사한 방법을 사용할 수 있습니다.

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>일부 Visual Studio 템플릿은 IWebHostBuilder에서 UseApplicationInsights () 확장 메서드를 사용 하 여 Application Insights를 사용 하도록 설정 했습니다. 이 사용량이 여전히 유효 한가요?

확장 메서드는 `UseApplicationInsights()` 계속 지원 되지만 APPLICATION INSIGHTS SDK 버전 2.8.0 이상에서 사용 되지 않는 것으로 표시 되었습니다. SDK의 다음 주 버전에서는 제거 됩니다. Application Insights 원격 분석을 사용 하도록 설정 하는 권장 방법은를 사용 하 여 `AddApplicationInsightsTelemetry()` 일부 구성을 제어 하는 오버 로드를 제공 하기 때문입니다. 또한 ASP.NET Core 2.x 앱에서 `services.AddApplicationInsightsTelemetry()` application insights를 사용 하도록 설정 하는 유일한 방법입니다.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Web Apps에 ASP.NET Core 응용 프로그램을 배포 합니다. Web Apps에서 Application Insights 확장을 계속 사용 하도록 설정 해야 하나요?

이 문서에 표시 된 것 처럼 SDK가 빌드 시간에 설치 된 경우 App Service 포털에서 [Application Insights 확장](./azure-web-apps.md) 을 사용 하도록 설정할 필요가 없습니다. 확장이 설치 된 경우에도 SDK가 응용 프로그램에 이미 추가 된 것을 감지 하면 해당 확장이 다시 시작 됩니다. 확장에서 Application Insights를 사용 하도록 설정 하는 경우 SDK를 설치 하 고 업데이트할 필요가 없습니다. 그러나이 문서의 지침에 따라 Application Insights를 사용 하도록 설정 하면 다음과 같은 이유로 유연성이 향상 됩니다.

   * Application Insights 원격 분석은에서 계속 작동 합니다.
       * Windows, Linux, Mac 등의 모든 운영 체제
       * 자체 포함 또는 프레임 워크 종속을 비롯 한 모든 게시 모드
       * 전체 .NET Framework를 포함 하는 모든 대상 프레임 워크입니다.
       * Web Apps, Vm, Linux, 컨테이너, Azure Kubernetes 서비스 및 비 Azure 호스팅을 비롯 한 모든 호스팅 옵션입니다.
       * Preview 버전을 포함 한 모든 .NET Core 버전
   * Visual Studio에서 디버깅 하는 경우 원격 분석을 로컬로 볼 수 있습니다.
   * API를 사용 하 여 추가 사용자 지정 원격 분석을 추적할 수 있습니다 `TrackXXX()` .
   * 구성에 대 한 모든 권한을 가집니다.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>상태 모니터 같은 도구를 사용 하 여 Application Insights 모니터링을 사용 하도록 설정할 수 있나요?

아니요. [상태 모니터](./monitor-performance-live-website-now.md) 및 [상태 모니터 v2](./status-monitor-v2-overview.md) 는 현재 ASP.NET 4.x만 지원 합니다.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights ASP.NET Core 2.0 응용 프로그램에 대해 자동으로 사용 하도록 설정 되어 있습니까?

`Microsoft.AspNetCore.All`2.0 메타 패키지에는 APPLICATION INSIGHTS SDK (버전 2.1.0)가 포함 되어 있습니다. Visual Studio 디버거에서 응용 프로그램을 실행 하는 경우 Visual Studio는 Application Insights을 사용 하도록 설정 하 고 원격 분석을 IDE 자체에서 로컬로 표시 합니다. 계측 키가 지정 되지 않은 경우 원격 분석을 Application Insights 서비스로 보내지 못했습니다. 2.0 앱의 경우에도 Application Insights을 사용 하도록 설정 하려면이 문서의 지침을 따르는 것이 좋습니다.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Linux에서 응용 프로그램을 실행 하는 경우 모든 기능이 지원 되나요?

예. SDK에 대 한 기능 지원은 모든 플랫폼에서 동일 하지만 다음과 같은 경우는 예외입니다.

* [성능 카운터](./performance-counters.md) 는 Windows 에서만 지원 되므로 SDK는 Linux에서 [이벤트 카운터](./eventcounters.md) 를 수집 합니다. 대부분의 메트릭은 동일 합니다.
* `ServerTelemetryChannel`가 기본적으로 사용 되는 경우에도 응용 프로그램이 Linux 또는 MacOS에서 실행 되는 경우, 네트워크 문제가 있는 경우 채널이 일시적으로 원격 분석을 유지 하기 위해 로컬 저장소 폴더를 자동으로 만들지 않습니다. 이러한 제한으로 인해 임시 네트워크 또는 서버 문제가 있으면 원격 분석이 손실 됩니다. 이 문제를 해결 하려면 채널에 대 한 로컬 폴더를 구성 합니다.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>이 SDK는 새 .NET Core 2.x 작업자 서비스 템플릿 응용 프로그램에 대해 지원 되나요?

이 SDK에는가 필요 `HttpContext` 하므로 .Net Core 2.X Worker 서비스 응용 프로그램을 포함 하 여 HTTP가 아닌 응용 프로그램에서 작동 하지 않습니다. 새로 릴리스된 Microsoft ApplicationInsights. r e n a m e 서비스 SDK를 사용 하 여 이러한 응용 프로그램에서 application insights를 사용 하도록 설정 하려면 [이](worker-service.md) 문서를 참조 하세요.

## <a name="open-source-sdk"></a>오픈 소스 SDK

* [코드를 읽고이에 기여](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates)합니다.

최신 업데이트 및 버그 수정에 대해서는 [릴리스 정보를 참조](./release-notes.md)하세요.

## <a name="next-steps"></a>다음 단계

* 사용자 [흐름을 탐색](./usage-flows.md) 하 여 사용자가 앱을 탐색 하는 방법을 파악 합니다.
* 예외가 throw 될 때 소스 코드 및 변수의 상태를 확인 하도록 [스냅숏 컬렉션을 구성](./snapshot-debugger.md) 합니다.
* [API를 사용](./api-custom-events-metrics.md) 하 여 앱의 성능 및 사용 현황에 대 한 자세한 보기에 대 한 사용자 고유의 이벤트 및 메트릭을 보냅니다.
* [가용성 테스트](./monitor-web-app-availability.md)를 사용하여 전 세계에서 사용자 앱을 지속적으로 확인합니다.
* [ASP.NET Core의 종속성 주입](/aspnet/core/fundamentals/dependency-injection)

