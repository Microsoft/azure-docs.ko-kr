---
title: .NET 앱에서 로그 이벤트 생성
description: Azure 클러스터 또는 독립 실행형 클러스터에 호스트된 .NET Service Fabric 애플리케이션에 로깅을 추가하는 방법을 알아봅니다.
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: a36425acf42a469c7f48b2e954bdacfdfcce1b10
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011483"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Service Fabric 애플리케이션에 로깅 추가

애플리케이션은 문제가 발생할 경우, 디버그하기에 충분한 정보를 제공해야 합니다. 로깅은 Service Fabric 애플리케이션에 추가할 수 있는 가장 중요한 사항 중 하나입니다. 오류가 발생할 경우, 좋은 로깅이 오류를 조사하는 방법을 제공할 수 있습니다. 로그 패턴을 분석하여 애플리케이션의 성능 또는 디자인을 개선하는 방법을 찾을 수 있습니다. 이 문서에서는 몇 가지 로깅 옵션을 보여 줍니다.

## <a name="eventflow"></a>EventFlow

[EventFlow 라이브러리](https://github.com/Azure/diagnostics-eventflow) 제품군을 사용하면 애플리케이션이 수집할 진단 데이터 및 데이터 출력 위치를 정의할 수 있습니다. 진단 데이터는 성능 카운터에서 애플리케이션 추적까지 모든 항목이 될 수 있습니다. 애플리케이션과 동일한 프로세스에서 실행되므로 통신 오버헤드가 최소화됩니다. EventFlow 및 Service Fabric에 대한 자세한 내용은 [Azure Service Fabric 이벤트 집계 및 EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)를 참조하세요.

### <a name="using-structured-eventsource-events"></a>구조적 EventSource 이벤트 사용

사용 사례로 메시지 이벤트를 정의하면 이벤트 컨텍스트에 이벤트 데이터를 패키징할 수 있습니다. 지정한 이벤트 속성의 이름 또는 값을 기준으로 더 간단하게 검색하고 필터링할 수 있습니다. 계측 출력을 구조화하면 더 쉽게 읽을 수 있지만 각 사용 사례에 대한 이벤트를 정의하는 데 더 많은 생각과 시간이 필요합니다. 

일부 이벤트 정의는 전체 애플리케이션에서 공유할 수 있습니다. 예를 들어 메서드 시작 또는 중지 이벤트는 애플리케이션 내의 많은 서비스에서 다시 사용됩니다. 주문 시스템과 같은 도메인 관련 서비스에는 자체의 고유 이벤트를 포함하는 **CreateOrder** 이벤트가 있을 수 있습니다. 이 방법은 많은 이벤트를 생성하고 잠재적으로 프로젝트 팀 전체에서 식별자를 조정할 필요가 있습니다. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>일반적 EventSource 사용

특정 이벤트를 정의하는 것이 어려울 수 있기 때문에 많은 사람들이 일반적으로 정보를 문자열로 출력하는 공통 매개 변수 집합을 포함하는 몇 가지 이벤트를 정의합니다. 구조적 측면 대부분이 손실되어 결과를 검색하고 필터링하는 것이 더욱 어렵습니다. 이 방법에서는 일반적으로 로깅 수준에 해당하는 몇 가지 이벤트가 정의됩니다. 다음 코드 조각에서는 디버그 및 오류 메시지를 정의합니다.

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

또한 구조적 계측과 일반 계측의 하이브리드를 사용하면 효과적일 수 있습니다. 구조적 계측은 오류 및 메트릭 보고에 사용됩니다. 일반 이벤트는 문제 해결을 위해 엔지니어가 사용하는 자세한 로깅에 사용할 수 있습니다.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

ASP.NET Core 로깅([ Microsoft.Extensions.Logging NuGet 패키지](https://www.nuget.org/packages/Microsoft.Extensions.Logging))은 애플리케이션에 대한 표준 로깅 API를 제공하는 로깅 프레임워크입니다. 다른 로깅 백 엔드에 대한 지원을 ASP.NET Core 로깅에 연결할 수 있습니다. 이렇게 하면 많은 코드를 변경할 필요 없이 애플리케이션의 로깅에 대한 다양한 지원이 처리됩니다.

1. 계측할 프로젝트에 **Microsoft 확장명 로깅** 패키지를 추가 합니다. 또한 공급자 패키지를 추가합니다. 자세한 내용은 [ASP.NET Core 로그인](/aspnet/core/fundamentals/logging)(영문)을 참조하세요.
2. 서비스 파일에 **로깅하** 는 **using** 지시문을 추가 합니다.
3. 서비스 클래스 내에 프라이빗 변수를 정의합니다.

   ```csharp
   private ILogger _logger = null;
   ```

4. 서비스 클래스의 생성자에서 다음 코드를 추가합니다.

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. 메서드에서 코드 계측을 시작합니다. 다음은 몇 가지 샘플입니다.

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>다른 로깅 공급자 사용

타사 공급자 중 일부는 [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/) 및 [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging)를 포함하여 이전 섹션에서 설명한 방법을 사용합니다. 이들 각각을 ASP.NET Core 로깅에 연결하거나 개별적으로 사용할 수 있습니다. Serilog에는 로거에서 보낸 모든 메시지를 보강하는 기능이 있습니다. 이 기능은 서비스 이름, 유형 및 파티션 정보를 출력하는 데 유용할 수 있습니다. ASP.NET Core 인프라에서 이 기능을 사용하려면 다음 단계를 수행합니다.

1. **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate** 및 **Serilog.Sinks.Observable** NuGet 패키지를 프로젝트에 추가합니다. 
2. `LoggerConfiguration` 및 로거 인스턴스를 만듭니다.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. `Serilog.ILogger` 인수를 서비스 생성자에 추가하고 새로 만든 로거를 전달합니다.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. 서비스 생성자에서 **ServiceTypeName**, **ServiceName**, **PartitionId** 및 **InstanceId**에 대한 속성 보강자를 만듭니다.

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Serilog 없이 ASP.NET Core를 사용하는 경우와 동일한 코드를 계측합니다.

   >[!NOTE]
   >앞의 예제와 함께 정적 `Log.Logger`를 사용하지 *않는* 것이 좋습니다. Service Fabric은 단일 프로세스 내에서 동일한 서비스 유형의 여러 인스턴스를 호스팅할 수 있습니다. 정적 `Log.Logger`를 사용하는 경우 속성 보강자의 마지막 기록기는 실행 중인 모든 인스턴스의 값을 표시합니다. _logger 변수가 서비스 클래스의 프라이빗 멤버 변수가 되는 한 가지 이유입니다. 또한 서비스 전체에서 사용할 수 있는 일반 코드에서 `_logger`를 사용할 수 있도록 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Service Fabric의 애플리케이션 모니터링](service-fabric-diagnostics-event-generation-app.md)에 대해 자세히 알아봅니다.
- [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) 및 [Microsoft Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md)를 사용하는 로깅에 대해 알아봅니다.
