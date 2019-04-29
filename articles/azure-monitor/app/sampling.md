---
title: Azure Application Insights의 원격 분석 샘플링 | Microsoft Docs
description: 제어에서 원격 분석의 양을 유지하는 방법입니다.
services: application-insights
documentationcenter: windows
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.author: cithomas
ms.openlocfilehash: b35b0c66c29805d9cd7ecd00ffaad4fc1cfe253b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653814"
---
# <a name="sampling-in-application-insights"></a>Application Insights의 샘플링

샘플링은 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)의 기능입니다. 애플리케이션 데이터의 올바른 분석을 통계적으로 유지하는 동안 원격 분석 트래픽 및 저장소를 줄일 수 있는 좋은 방법입니다. 필터는 관련된 항목을 선택하여 진단 조사를 수행할 때 항목 간을 탐색할 수 있도록 합니다.
포털에서 메트릭 개수가 표시 됩니다 하는 경우 계정 샘플링 야 정상화 됩니다. 이렇게 하면 통계에 영향을 주지 최소화 됩니다.

샘플링은 트래픽 및 데이터 비용을 줄여주며 제한을 피하는 데 도움이 됩니다.

## <a name="in-brief"></a>개요:

* 샘플링은 *n* 레코드에 1을 유지하면서 나머지는 무시합니다. 예를 들어, 1에서 5 개 이벤트를 20%의 샘플링 비율을 유지할 수 있습니다. 
* 적응 샘플링은 모든 최신 버전의 ASP.NET 및 ASP.NET Core 소프트웨어 개발 키트 (Sdk)에 기본으로 사용 됩니다.
* 샘플링을 수동으로 설정할 수 있습니다. 이에 포털에서 구성할 수 있습니다 합니다 *사용량 및 예상된 비용 페이지*, ApplicationInsights.config 파일에서 ASP.NET SDK에서 코드를 통해 ASP.NET Core SDK 또는 Java SDK의 ApplicationInsights.xml 파일입니다.
* 사용자 지정 이벤트 및 이벤트 집합을 보존 되었거나 함께 삭제 되었음을 확인 해야를 기록 하는 경우 동일한 OperationId 값 이벤트가 있어야 합니다.
* 샘플링 약수 *n*은 `itemCount` 속성의 각 레코드에서 보고되며 이는 검색의 이름 "요청 개수" 또는 "이벤트 개수"에 나타납니다. `itemCount==1`경우 샘플링 작업에서 아닙니다.
* 분석 쿼리를 작성하는 경우 [샘플링을 고려](../../azure-monitor/log-query/aggregations.md)해야 합니다. 특히, 레코드를 단순히 세는 대신 `summarize sum(itemCount)`를 사용해야 합니다.

## <a name="types-of-sampling"></a>샘플링 유형

다음은 세 가지 대체 샘플링 방법입니다.

* **적응 샘플링** ASP.NET/ASP.NET Core 앱의 SDK에서 보낸 원격 분석의 볼륨을 자동으로 조정 합니다. 이 ASP.NET 웹 SDK v 2.0.0-beta3 이상 및 Microsoft.ApplicationInsights.AspNetCore SDK v 2.2.0-beta1 이상에서 기본 샘플링입니다.  적응 샘플링은 현재 ASP.NET 서버 쪽 원격 분석에만 사용할 수 있습니다.

* **고정 비율 샘플링** ASP.NET 또는 ASP.NET Core 또는 Java 서버 모두에서 사용자의 브라우저에서 보낸 원격 분석의 양을 줄입니다. 비율은 사용자가 설정합니다. 클라이언트와 서버는 샘플링을 동기화하므로 검색에서 관련된 페이지 보기 및 요청 사이를 이동할 수 있습니다.

* **수집 샘플링** Azure portal에서 작동 합니다. 설정한 샘플링 주기에 따라 앱에서 보낸 원격 분석 중 일부를 삭제합니다. 앱에서 보낸 원격 분석 트래픽을 줄이지는 않지만 월별 할당량 내로 유지하는 데 도움이 됩니다. 수집 샘플링의 주요 이점은 앱을 다시 배포 하지 않고 샘플링 비율을 설정할 수 있습니다. 수집 샘플링은 모든 서버와 클라이언트에 균일 하 게 작동 합니다.

적응 또는 고정 비율 샘플링이 작업 중인 경우 수집 샘플링은 비활성화됩니다.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>ASP.NET/ASP.NET Core 웹 응용 프로그램의 적응 샘플링

적응 샘플링은 Application Insights SDK ASP.NET v 2.0.0-beta3 이상에서는 Microsoft.ApplicationInsights.AspNetCore SDK v 2.2.0-beta1에 사용할 수 있는 이상에서는 기본적으로 사용 됩니다.

적응 샘플링은 웹 서버 앱에서 Application Insights 서비스 엔드포인트로 보내는 원격 분석의 양에 영향을 줍니다. 볼륨의 트래픽 지정 된 최대 속도로 내로 유지 하도록 자동으로 조정 됩니다 및 설정을 통해 제어 됩니다 `MaxTelemetryItemsPerSecond`합니다. 항목 볼륨은 아래와 샘플링 하지 낮은 사용량으로 인 한 또는 디버깅할 때와 같은 응용 프로그램 원격 분석의 양이 생성 `MaxTelemetryItemsPerSecond`합니다. 원격 분석 증가 된 볼륨으로 샘플링 주기는 대상 볼륨을 달성 하도록 조정 됩니다.

목표량을 달성하기 위해 생성된 원격 분석 중 일부가 삭제됩니다. 그러나 다른 샘플링 유형과 마찬가지로 알고리즘에 관련 원격 분석 항목이 유지됩니다. 예를 들어 검색에서 원격 분석을 검사하는 경우 특정 예외와 관련된 요청을 찾을 수 있습니다.

요청 빈도 및 예외 처리 빈도와 같은 메트릭 수는 샘플링 주기에 맞게 보정되도록 조정되므로 메트릭 탐색기에서 거의 정확한 값으로 표시됩니다.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>ASP.NET 응용 프로그램에 대 한 적응 샘플링 구성

[에 대해 알아봅니다](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) 적응 샘플링에 대 한 ASP.NET Core 응용 프로그램을 구성 하는 방법에 대 한 합니다. 

[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)의 `AdaptiveSamplingTelemetryProcessor` 노드에서 여러 매개 변수를 조정할 수 있습니다. 표시된 수치는 기본값입니다.

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    대상 비율은 **각 서버 호스트에**대한 것을 목표로 하는 적응 알고리즘입니다. 여러 호스트에서 웹앱을 실행하는 경우 Application Insights 포털에서 트래픽을 대상 비율 범위 내에서 유지하기 위해 이 값을 줄입니다.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    현재 비율의 원격 분석은 다시 평가 하는 간격입니다. 평가는 이동 평균으로 수행됩니다. 원격 분석이 급격히 증가하는 경우 이 간격을 줄일 수 있습니다.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    샘플링 비율 값을 변경한 후 더 적은 데이터를 캡처하기 위해 샘플링 비율을 다시 낮출 수 있는 시간 제한입니다.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    샘플링 비율 값을 변경한 후 더 많은 데이터를 캡처하기 위해 샘플링 비율을 다시 높일 수 있는 시간 제한입니다.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    샘플링 비율이 변경됨에 따라 사용자가 설정할 수 있는 최소값입니다.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    샘플링 비율이 변경됨에 따라 사용자가 설정할 수 있는 최대값입니다.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    이동 평균 계산에서 가중치는 가장 최근의 값에 할당됩니다. 1보다 작거나 같은 값을 사용합니다. 값이 작을수록 알고리즘은 갑작스런 변화에 덜 반응합니다.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    앱을 막 시작했을 때 할당된 값입니다. 디버깅할 때 값을 줄입니다 하지 마십시오.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    샘플링하지 않으려는 형식의 세미콜론으로 구분된 목록 인식할 수 있는 형식은 다음과 같습니다. 종속성, 이벤트, 예외, 페이지 보기, 요청, 추적 지정된 형식의 모든 인스턴스가 전송되고 지정되지 않은 형식은 샘플링됩니다.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    샘플링하려는 형식의 세미콜론으로 구분된 목록 인식할 수 있는 형식은 다음과 같습니다. 종속성, 이벤트, 예외, 페이지 보기, 요청, 추적 지정된 형식이 샘플링되고 다른 형식의 모든 인스턴스가 항상 전송됩니다.


**해제 하려면** 적응 샘플링, applicationinsights-config에서 AdaptiveSamplingTelemetryProcessor 노드를 제거 합니다.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>대체: 코드에서 적응 샘플링 구성

.config 파일에서 샘플링 매개 변수를 설정하는 대신 프로그래밍 방식으로 이러한 값을 설정할 수 있습니다.

1. 모두 제거 된 `AdaptiveSamplingTelemetryProcessor` .config 파일에서 노드.
2. 적응 샘플링을 구성 하려면 다음 코드 조각을 사용 합니다.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([원격 분석 프로세서에 대해 알아봅니다](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

특정 형식에서 전혀 샘플링 되 고 않도록 제외할 수 있습니다 하거나 각 원격 분석 형식에 대 한 샘플링 주기를 개별적으로 조정할 수도 있습니다. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>ASP.NET Core 응용 프로그램에 대 한 적응 샘플링을 구성 합니다.

방법이 없는 `ApplicationInsights.Config` ASP.NET Core 응용 프로그램 이므로 모든 구성이 완료 되 코드를 통해.
적응 샘플링은 모든 ASP.NET Core 애플리케이션에 기본적으로 사용됩니다. 샘플링 동작을 사용하지 않도록 설정하거나 사용자 지정할 수 있습니다.

### <a name="turning-off-adaptive-sampling"></a>적응 샘플링 끄기

Application Insights 서비스에 메서드에 추가 하는 동안 기본 샘플링 기능을 비활성화할 수 있습니다 ```ConfigureServices```를 사용 하 여 ```ApplicationInsightsServiceOptions``` 내에서 `Startup.cs` 파일:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
    //...
}
```

위의 코드는 샘플링 기능을 비활성화합니다. 더 많은 사용자 지정 옵션을 사용하여 샘플링을 추가하려면 다음 단계를 수행합니다.

### <a name="configure-sampling-settings"></a>샘플링 설정 구성

아래와 같이 ```TelemetryProcessorChainBuilder```의 확장 메서드를 사용하여 샘플링 동작을 사용자 지정합니다.

> [!IMPORTANT]
> 이 메서드를 사용하여 샘플링을 구성하는 경우 AddApplicationInsightsTelemetry()와 함께 aiOptions.EnableAdaptiveSampling = false; 설정을 사용하십시오.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

    var builder = configuration .TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**위의 메서드를 사용 하 여 샘플링을 구성 하려면 사용 해야 하는 경우 ```aiOptions.EnableAdaptiveSampling = false;``` AddApplicationInsightsTelemetry() 사용 하 여 설정 합니다.**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites"></a>ASP.NET, ASP.NET Core 및 Java 웹 사이트에 대 한 고정 비율 샘플링

고정 비율 샘플링은 웹 서버와 웹 브라우저에서 전송되는 트래픽을 줄입니다. 적응 샘플링과 달리 사용자가 결정한 고정 비율로 원격 분석을 줄어듭니다. 또한 클라이언트와 서버 샘플링을 동기화하여 관련 항목이 유지되도록 합니다. 예를 들어 Search의 페이지 보기에서 관련 요청을 찾을 수 있습니다.

다른 샘플링 기술이 등도 관련된 항목을 보관합니다. 각 HTTP 요청 이벤트에 대해 요청 및 관련 이벤트가 함께 삭제되거나 전송됩니다.

메트릭 탐색기에서 요청 및 예외 수와 같은 빈도는 거의 정확한 값이 되도록 계수가 곱해져 샘플링 주기에 맞게 보정됩니다.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>ASP.NET에서 고정 비율 샘플링 구성

1. **적응 샘플링을 사용하지 않도록 설정**: [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)에서 `AdaptiveSamplingTelemetryProcessor` 노드를 제거하거나 주석으로 처리합니다.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **고정 비율 샘플링 모듈을 사용하도록 설정합니다.** 다음 코드 조각을 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)에 추가합니다.
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>대체: 서버 코드에서 고정 비율 샘플링을 사용하도록 설정
    
    .config 파일에서 샘플링 매개 변수를 설정하는 대신 프로그래밍 방식으로 이러한 값을 설정할 수 있습니다. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([원격 분석 프로세서에 대해 알아봅니다](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>ASP.NET Core에서 고정 비율 샘플링 구성

1. **적응 샘플링을 사용하지 않도록 설정**:  메서드에서 변경할 수 있습니다 ```ConfigureServices```를 사용 하 여 ```ApplicationInsightsServiceOptions```:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    //...
    }
    ```

2. **고정 비율 샘플링 모듈을 사용하도록 설정합니다.** 메서드에서 변경할 수 있습니다 ```Configure``` 조각을 아래에 표시 된 대로:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>JAVA에서 고정 비율 샘플링 구성 ###

1. 최신 [Application Insights Java SDK](../../azure-monitor/app/java-get-started.md)를 사용하여 웹 애플리케이션 다운로드 및 구성

2. ApplicationInsights.xml 파일에 다음 코드 조각을 추가하여 **고정 비율 샘플링 모듈을 사용하도록 설정**합니다.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        <TelemetryProcessors/>
    ```

3. 프로세서 태그 “FixedRateSamplingTelemetryProcessor” 내부에 다음 태그를 사용하여 샘플링에서 특정 유형의 원격 분석을 포함하거나 제외할 수 있습니다.
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

원격 분석 샘플링에서 제외 되거나 포함 되는 형식은 다음과 같습니다. 종속성, 이벤트, 예외, PageView, 요청 및 추적 합니다.

> [!NOTE]
> 샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다.  현재 샘플링은 다른 값을 지원하지 않습니다.
> 
> 

<a name="other-web-pages"></a>



## <a name="ingestion-sampling"></a>수집 샘플링

이 샘플링 형식은 웹 서버, 브라우저 및 디바이스의 원격 분석이 Application Insights 서비스 엔드포인트에 도달하는 지점에서 작동합니다. 앱에서 전송되는 원격 분석 트래픽을 줄이지는 않지만 Application Insights에서 처리 및 보존(및 청구)되는 양을 줄입니다.

앱이 월간 할당량을 자주 초과하지만 SDK 기반의 샘플링 유형 중 하나를 사용할 옵션이 없는 경우 이 샘플링 유형을 사용합니다. 

사용량 및 예상 비용 페이지에서 샘플링 주기를 설정합니다.

![애플리케이션 개요 블레이드에서 설정, 할당량, 샘플을 차례로 클릭한 다음, 샘플링 주기를 선택하고 업데이트를 클릭합니다.](./media/sampling/04.png)

다른 샘플링 유형과 마찬가지로 알고리즘에 관련 원격 분석 항목이 유지됩니다. 예를 들어 검색에서 원격 분석을 검사하는 경우 특정 예외와 관련된 요청을 찾을 수 있습니다. 요청 빈도 및 예외 처리 빈도와 같은 메트릭 수는 올바르게 유지됩니다.

샘플링에서 무시된 데이터 요소는 [연속 내보내기](../../azure-monitor/app/export-telemetry.md)와 같은 Application Insights 기능에서 사용할 수 없습니다.

SDK 기반 적응 또는 고정 비율 샘플링이 작동되는 동안에는 수집 샘플링이 작동하지 않습니다. 적응 샘플링은 수집 샘플링은 비활성화 및 ASP.NET/ASP.NET Core SDK는 Visual Studio에서 사용 하도록 설정 하거나 상태 모니터를 사용 하 여 Azure 웹 앱 확장에서 사용 하는 경우 기본적으로 사용 됩니다. SDK의 샘플링 비율이 100% (즉, 보다 작은 경우 항목을 샘플링 되는) 수집 샘플링 속도 설정 하면 무시 됩니다.

> [!WARNING]
> 타일에 표시된 값은 수집 샘플링에 대해 설정한 값을 나타냅니다. SDK 샘플링이 작업 중인 경우 실제 샘플링 속도를 나타내지 않습니다.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>JavaScript를 사용하는 웹 페이지에 대한 샘플링
서버에서 고정 비율 샘플링에 대한 웹 페이지를 구성할 수 있습니다. 

[Application Insights에 대한 웹 페이지를 구성](../../azure-monitor/app/javascript.md)할 때 Application Insights 포털에서 얻은 JavaScript 코드 조각을 수정합니다. (ASP.NET 앱에서 코드 조각은 일반적으로 _Layout.cshtml로 이동합니다.)  계측 키 앞에 `samplingPercentage: 10,`과 같은 줄을 삽입합니다.

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다.  현재 샘플링은 다른 값을 지원하지 않습니다.

서버에서도 고정 비율 샘플링을 사용하도록 설정하는 경우 클라이언트와 서버가 동기화되므로 검색에서 관련된 페이지 보기 및 요청 사이를 이동할 수 있습니다.

## <a name="when-to-use-sampling"></a>언제 샘플링을 사용합니까?

적응 샘플링은 최신.NET 및.NET Core Sdk에서 자동으로 활성화 됩니다. 사용하는 SDK 버전에 관계 없이 수집 샘플링을 사용하면 Application Insights에서 수집된 데이터를 샘플링할 수 있습니다.

기본적으로 Java SDK에서는 샘플링을 사용할 수 없습니다. 현재 고정 비율 샘플링만 지원합니다. 적응 샘플링은 Java SDK에서 지원되지 않습니다.

일반적으로 중소 규모의 애플리케이션 대부분에서는 샘플링이 필요하지 않습니다. 가장 유용한 진단 정보 및 가장 정확한 통계는 모든 사용자 활동에서 데이터를 수집하여 구합니다. 

샘플링의 주요 장점은 다음과 같습니다.

* 앱이 짧은 시간 간격으로 매우 높은 비율의 원격 분석을 전송할 때 Application Insights 서비스는 ("제한") 데이터 요소를 삭제합니다. 
* 가격 책정 계층에 대한 데이터 요소의 [할당량](../../azure-monitor/app/pricing.md) 을 유지합니다. 
* 원격 분석의 컬렉션에서 네트워크 트래픽을 줄입니다. 

### <a name="which-type-of-sampling-should-i-use"></a>어떤 유형의 샘플링을 사용해야 합니까?

**다음과 같은 경우 수집 샘플링을 사용합니다.**

* 원격 분석의 월간 할당량을 자주 초과하는 경우
* 샘플링을 지원하지 않는 SDK 버전(예: ASP.NET 버전 2 이전)을 사용하는 경우
* 사용자의 웹 브라우저에서 너무 많은 원격 분석을 보시기 합니다.

**다음과 같은 경우 고정 비율 샘플링을 사용합니다.**

* ASP.NET 웹 서비스용 Application Insights SDK 버전 2.0.0 이상 또는 Java SDK v2.0.1 이상을 사용하는 경우
* 사용자가 클라이언트 및 서버 간의 동기화된 샘플링을 원하는 경우 [검색](../../azure-monitor/app/diagnostic-search.md)에서 이벤트를 조사하고 있을 때 클라이언트 및 서버에서 페이지 보기 및 http 요청과 같은 관련 이벤트 사이를 탐색할 수 있습니다.
* 사용자가 앱에 대한 적절한 샘플링 비율을 확신하는 경우입니다. 샘플링 비율은 정확한 메트릭을 가져오기 위해 충분히 높아야 하지만 가격 책정 할당량 및 조정 제한을 초과하는 비율보다 낮아야 합니다. 

**다음과 같은 경우 적응 샘플링을 사용합니다.**

다른 형태의 샘플링을 사용하는 조건이 적용되지 않으면 적응 샘플링을 사용하는 것이 좋습니다. ASP.NET/ASP.NET 핵심 서버 SDK에서에서 기본적으로이 설정을 사용 합니다. 특정 최소 주기에 도달할 때까지 트래픽이 감소되지 않으므로 사용 빈도가 낮은 사이트는 영향을 받지 않습니다.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>샘플링이 작업 중인지 어떻게 알 수 있나요?

적용된 위치에 관계 없이 실제 샘플링 주기를 검색하려면 다음과 같은 [분석 쿼리](../../azure-monitor/app/analytics.md) 를 사용합니다.

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

모든 형식에 대 한 RetainedPercentage 100 보다 작은 경우 해당 항목 샘플링 되 고 됩니다.

**Application Insights 세션 메트릭이 샘플링 하지 않습니다 하 고 성능 카운터 앞에서 설명한 모든 샘플링 기술에서 원격 분석 유형입니다. 이러한 형식은 전체 자릿수가 감소를 이러한 원격 분석 유형에 매우 바람직하지 않을 수 있습니다 하는 대로 항상 샘플링에서 제외 됩니다.**

## <a name="how-does-sampling-work"></a>샘플링은 어떻게 작동되나요?

ASP.NET 버전 2.0.0 및 Java SDK 버전 2.0.1 이상에서 SDK의 고정 비율 샘플링 기능입니다. 적응 샘플링은 ASP.NET 버전 2.0.0 이상에서 SDK의 기능입니다. 수집 샘플링은 Application Insights 서비스의 기능이며 SDK가 샘플링을 수행하지 않는 경우 작동될 수 있습니다.

샘플링 알고리즘은 어떤 원격 분석 항목을 삭제할 것인지 및 유지(SDK 또는 Application Insights 서비스에)할 것인지를 결정합니다. 샘플링 의사 결정은 상호 관련된 데이터 요소를 그대로 유지하려는 목표의 여러 규칙에 기반하며 이는 감소된 데이터 집합을 사용하더라도 실행할 수 있고 신뢰할 수 있는 Application Insights에서 진단 환경을 유지 관리합니다. 예를 들어 실패한 요청의 경우 앱이 추가 원격 분석 항목을 전송하면(예: 해당 요청에서 기록된 예외 및 추적) 샘플링은 해당 요청 및 기타 원격 분석을 분할하지 않습니다. 전체를 유지하거나 삭제합니다. 결과적으로 Application Insights에서 요청 세부 정보를 볼 때 항상 연결된 원격 분석 항목과 함께 요청을 확인할 수 있습니다. 

샘플링 의사 결정 되는 특정 작업에 속하는 모든 원격 분석 항목은 보존 또는 삭제 요청의 작업 ID를 기반으로 합니다. 작업에 있지 않은 원격 분석 항목에 대 한 ID 집합 (예: http 컨텍스트가 없는 비동기 스레드에서 보고 된 예에서는 원격 분석 항목) 샘플링 단순히 각 형식의 원격 분석 항목의 백분율을 캡처합니다. .NET SDK의 2.5.0-beta2 및 ASP.NET Core SDK의 2.2.0-beta3 이전의 샘플링 의사 결정 기반 "user"를 정의 하는 응용 프로그램에 대 한 사용자 ID의 해시 (즉, 가장 일반적인 웹 응용 프로그램). 사용자 (예: 웹 서비스)를 정의 하지 않은 하는 응용 프로그램 유형에 대 한 샘플링 의사 결정 된 요청의 작업 ID를 기반으로 합니다.

원격 분석을 다시 표시하는 경우 Application Insights 서비스는 누락된 데이터 요소를 보완하기 위해 컬렉션의 시간에 사용된 동일한 샘플링 백분율로 메트릭을 조정합니다. 따라서 Application Insights에서 원격 분석을 보면 사용자는 실제 수에 가까운 통계적으로 올바른 근사치를 확인할 수 있습니다.

근사치의 정확도는 주로 구성된 샘플링 비율에 따라 다릅니다. 또한 아주 많은 사용자에게서 많은 양의 일반적으로 비슷한 요청을 처리하는 애플리케이션에 대해 정확도가 증가합니다. 반면에 상당한 부하가 있을 때 작동하지 않는 애플리케이션의 경우 이러한 애플리케이션이 일반적으로 할당량 내에 있으면서 제한에서 데이터 손실이 발생하지 않고 해당 원격 분석을 모두 보낼 수 있기에 샘플링은 필요하지 않습니다. 

> [!WARNING]
> Application Insights는 메트릭 및 세션 원격 분석 유형을 샘플링하지 않습니다. 정밀도 하락은 이러한 원격 분석 유형에 매우 좋지 못할 수 있습니다.
> 

### <a name="adaptive-sampling"></a>적응 샘플링

적응 샘플링은 SDK에서 현재의 전송 속도를 모니터링하는 구성 요소를 추가하여 샘플링 비율이 대상 최대 비율 안에서 유지되도록 조정합니다. 조정은 정기적으로 다시 계산되고 발신 전송 속도의 이동 평균에 기반합니다.

## <a name="sampling-and-the-javascript-sdk"></a>샘플링 및 JavaScript SDK

클라이언트 측(JavaScript) SDK는 서버 측 SDK과 함께 고정 비율 샘플링에 사용됩니다. 계측된 페이지는 서버 측이 "샘플"로 결정한 동일한 사용자의 클라이언트 측 원격 분석만을 보냅니다. 이 논리는 전체 클라이언트 및 서버 측에 대한 사용자 세션의 무결성을 유지하도록 설계되었습니다. 결과적으로 Application Insights의 특정 원격 분석 항목에서 해당 사용자 또는 세션에 대한 다른 모든 원격 분석 항목을 찾을 수 있습니다. 

*위에 설명한 대로 내 클라이언트 및 서버 측 원격 분석은 조정된 샘플을 표시하지 않습니다.*

* 서버 및 클라이언트 모두에서 고정 비율 샘플링을 사용할 수 있는지 확인합니다.
* SDK 버전이 2.0 이상이어야 합니다.
* 클라이언트와 서버 모두에서 동일한 샘플링 비율을 설정하도록 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

*ASP.NET 및 ASP.NET Core SDK의 기본 샘플링 동작 이란?*

* 위의 SDK의 최신 버전 중 하나를 사용 하는 경우 적응 샘플링은 초당 5 개의 원격 분석 항목에 대해 기본적으로 사용 됩니다.
  기본적으로 추가 하는 2 AdaptiveSamplingTelemetryProcessors 가지 하 고 샘플링 이벤트 형식에 하나 이벤트 형식은 샘플링에서 제외 다른 합니다. 이 구성은 SDK 이벤트 유형의 원격 분석 항목 5에 원격 분석 항목과 다른 모든 형식의 결합 되므로 다른 원격 분석 형식에서 이벤트 개별적으로 샘플링 되는 5 개의 원격 분석 항목을 제한 하는 함을 의미 합니다. 이벤트는 일반적으로 비즈니스 원격 분석을 위한 사용 및 가능성이 영향을 받지 않는 진단 원격 분석 볼륨입니다.
  
  다음 생성 되는 기본 ApplicationInsights.Config 파일을 보여 줍니다. 에서 설명한 대로 가지 하나씩 두 개의 별도 AdaptiveSamplingTelemetryProcessor 노드 추가 이벤트 형식 및 포함 하 여 다른 제외 합니다. ASP.NET Core, 코드에서 정확히 동일한 기본 동작이 사용 됩니다. 이 기본 동작을 변경 하려면 문서의 이전 섹션에서 예제를 사용 합니다.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*원격 분석을 한 번만 샘플링할 수 있습니까?*

* 아니요. SamplingTelemetryProcessors 항목은 이미 샘플링 하는 경우 고려 사항 샘플링에서 항목을 무시 합니다. 샘플링 SDK 자체에서 이미 샘플링 된 항목에 적용 되지 않습니다는 물론로 수집 샘플링에 대해 마찬가지입니다.'

*샘플링은 왜 간단히 "각 원격 분석 형식의 X 퍼센트를 수집"하지 않습니까?*

* 이 샘플링 방법이 메트릭 근사치의 정확도 높은 수준이 제공 하는 동안 중단 시키며이 사용자, 세션 및 진단에 대 한 중요 한 요청 단위 당 진단 데이터를 연결 하는 기능입니다. 따라서 샘플링은 "앱 사용자의 X 퍼센트에 대해 모든 원격 분석 항목 수집" 또는 "앱 요청의 X퍼센트에 대한 모든 원격 분석 수집" 논리를 사용하여 더 잘 작동합니다. 요청 (예: 배경 비동기 처리)를 사용 하 여 연결 되지 않은 원격 분석 항목을 대체 하는 "각 원격 분석 형식에 대 한 모든 항목의 X 퍼센트 수집 합니다." 

*샘플링 비율은 시간이 지나면서 달라질 수 있습니까?*

* 예, 적응 샘플링은 원격 분석의 현재 관찰된 양에 따라 샘플링 비율을 점차적으로 변경합니다.

*고정 비율 샘플링을 사용하는 경우 내 앱에 가장 적합한 샘플링 비율을 어떻게 알 수 있습니까?*

* 한 가지 방법은 적응 샘플링으로 시작하고 안정적인 비율(위 질문 참조)을 찾은 다음 해당 비율을 사용하여 고정 비율 샘플링으로 전환하는 것입니다. 
  
    그렇지 않으면 추측해야 합니다. Application Insights에서 현재 원격 분석 사용을 분석하고, 발생하는 모든 제한을 관찰하며, 수집된 원격 분석의 양을 추정합니다. 선택된 가격 책정 계층과 함께 이러한 세 가지 입력은 수집된 원격 분석의 볼륨을 얼마나 줄여야 할지 제안합니다. 그러나 사용자 수가 증가하거나 원격 분석의 양이 약간 다르게 변화되면 추정치가 무효화될 수도 있습니다.

*샘플링 비율을 너무 낮게 구성하는 경우 어떻게 됩니까?*

* 샘플링 비율이 지나치게 낮으면(지나친 샘플링) Application Insights가 데이터의 볼륨을 축소하기 위해 데이터의 시각화를 보완하려고 할 때 근사치의 정확도가 줄어듭니다. 또한 종종 실패하거나 요청이 느린 경우 샘플링되기에 진단 환경은 부정적으로 영향을 받습니다.

*샘플링 비율을 너무 높게 구성하는 경우 어떻게 됩니까?*

* 샘플링 비율을 너무 높게 구성하면(충분히 공격적이지 않음) 수집된 원격 분석의 볼륨이 충분히 감소되지 않습니다. 여전히 제한에 관련된 원격 분석 데이터 손실이 발생할 수 있고 Application Insights를 사용하는 비용은 초과 요금때문에 계획된 것 보다 클 수 있습니다.

*샘플링을 어떤 플랫폼에서 사용할 수 있습니까?*

* SDK가 샘플링을 수행하지 않는 경우 특정 볼륨을 초과하는 모든 원격 분석에 대해 자동으로 수집 샘플링이 발생할 수 있습니다. 예를 들어, 이전 버전의 ASP.NET SDK 또는 이전 버전의 Java SDK(1.0.10 or before)를 사용 하는 경우이 구성은 작동 됩니다.
* 사용 중인 ASP.NET SDK 버전 2.0.0 이상 또는 ASP.NET CORE SDK 버전 2.2.0 이상 (Azure 또는 고유한 서버에서 호스팅), 적응 샘플링 기본적으로 표시 하지만 위에서 설명한 것 처럼 고정 비율을 전환할 수 있습니다. 고정 비율 샘플링을 사용하면 SDK 브라우저는 자동으로 관련 이벤트를 샘플링하도록 동기화합니다. 
* Java SDK 버전 2.0.1 사용 하는 경우, 위의 고정 비율 샘플링을 켜도록 ApplicationInsights.xml을 구성할 수 있습니다. 샘플링은 기본적으로 꺼져 있습니다. 고정 비율 샘플링을 사용하면 SDK 브라우저는 자동으로 관련 이벤트를 샘플링하도록 동기화합니다.

*항상 보고 싶은 확실히 드문 이벤트가 있습니다. 이전의 샘플링 모듈에서 그 이벤트를 어떻게 가져올 수 있습니까?*

* 이 작업을 수행 하는 가장 좋은 방법은 사용자 지정을 작성 하는 것 [TelemetryProcessor](../../azure-monitor/app/api-filtering-sampling.md#filtering), 집합은 `SamplingPercentage` 원격 분석 항목에는 100 하려면 보존, 아래와 같이 합니다. 이렇게 하면 모든 샘플링 기술이 모든 샘플링 고려 사항에서이 항목 무시 됩니다.

```csharp
    if(somecondition)
    {
        ((ISupportSampling)item).SamplingPercentage = 100;
    }
```

## <a name="next-steps"></a>다음 단계

* [필터링](../../azure-monitor/app/api-filtering-sampling.md) 은 SDK에서 보내는 항목을 더 엄격하게 제어할 수 있습니다.