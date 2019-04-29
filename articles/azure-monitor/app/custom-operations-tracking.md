---
title: Azure Application Insights .NET SDK를 통한 사용자 지정 작업 추적 | Microsoft Docs
description: Azure Application Insights .NET SDK를 통한 사용자 지정 작업 추적
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/30/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: ae6e0e186f5cc0c9e3f0cd02d45d57c079eb3539
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900892"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Application Insights .NET SDK를 통한 사용자 지정 작업 추적

Azure Application Insights SDK는 들어오는 HTTP 요청과 종속 서비스에 대한 호출(예:HTTP 요청 및 SQL 쿼리)을 자동으로 추적합니다. 요청 및 종속성의 추적과 상관 관계를 사용하면 애플리케이션을 결합하는 모든 마이크로 서비스에서 전체 애플리케이션의 응답성 및 안정성을 파악할 수 있습니다. 

일반적으로는 지원할 수 없는 애플리케이션 패턴의 클래스가 있습니다. 이러한 패턴의 적절한 모니터링에는 수동 코드 계측이 필요합니다. 이 문서에서는 사용자 지정 큐 처리 및 장기 실행 백그라운드 작업 실행과 같이 수동으로 계측해야 할 수 있는 몇 가지 패턴에 대해 설명합니다.

여기서는 Application Insights SDK를 통해 사용자 지정 작업을 추적하는 방법에 대한 지침을 제공합니다. 다음과 관련이 있습니다.

- .NET용 Application Insights(기본 SDK라고도 함) 버전 2.4+
- 웹 애플리케이션용 Application Insights(ASP.NET 실행) 버전 2.4+
- ASP.NET Core용 Application Insights 버전 2.1+

## <a name="overview"></a>개요
작업은 애플리케이션에서 실행하는 활동의 논리적 부분입니다. 여기에는 이름, 시작 시간, 기간, 결과 및 실행 컨텍스트(예: 사용자 이름, 속성 및 결과)가 있습니다. 작업 B에서 작업 A를 시작한 경우 작업 B는 A의 부모로 설정됩니다. 작업에는 하나의 부모만 있을 수 있지만 많은 자식 작업이 있을 수 있습니다. 작업 및 원격 분석 상관 관계에 대한 자세한 내용은 [Azure Application Insights 원격 분석 상관 관계](correlation.md)를 참조하세요.

Application Insights .NET SDK에서 작업은 [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) 추상 클래스 및 해당하는[RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) 및 [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs) 하위 항목으로 설명됩니다.

## <a name="incoming-operations-tracking"></a>들어오는 작업 추적 
Application Insights 웹 SDK는 IIS 파이프라인과 모든 ASP.NET Core 애플리케이션에서 실행되는 ASP.NET 애플리케이션에 대한 HTTP 요청을 자동으로 수집합니다. 다른 플랫폼과 프레임워크에 대해서 커뮤니티 지원 솔루션이 있습니다. 그러나 애플리케이션에서 표준 또는 커뮤니티 지원 솔루션으로 지원되지 않으면 수동으로 계측할 수 있습니다.

사용자 지정 추적이 필요한 또 다른 예로 큐에서 항목을 받는 작업자가 있습니다. 일부 큐의 경우 이 큐에 메시지를 추가하는 호출이 종속성으로 추적됩니다. 그러나 메시지 처리를 설명하는 상위 수준 작업은 자동으로 수집되지 않습니다.

이러한 작업을 추적할 수 있는 방법을 살펴보겠습니다.

상위 수준에서 작업은 `RequestTelemetry`를 만들고 알려진 속성을 설정하는 것입니다. 작업이 완료되면 원격 분석을 추적합니다. 다음 예제에서는 이 작업을 보여 줍니다.

### <a name="http-request-in-owin-self-hosted-app"></a>Owin 자체 호스팅 앱의 HTTP 요청
이 예제에서 추적 컨텍스트는 [상관 관계에 대한 HTTP 프로토콜](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)에 따라 전파됩니다. 여기서 설명하는 헤더를 받아야 합니다.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

상관 관계에 대한 HTTP 프로토콜도 `Correlation-Context` 헤더를 선언하지만, 여기서는 간소화하기 위해 생략했습니다.

## <a name="queue-instrumentation"></a>큐 계측
HTTP 요청으로 상관 관계 세부 정보를 전달하는 [상관 관계에 대한 HTTP 프로토콜](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)이 있지만 모든 큐 프로토콜은 동일한 세부 정보가 큐 메시지를 따라 전달되는 방식을 정의해야 합니다. 일부 큐 프로토콜(예: AMQP)은 추가 메타데이터 전달을 허용하며 일부 다른 큐 프로토콜(예: Azure Storage 큐)은 메시지 페이로드로 인코딩될 컨텍스트가 필요합니다.

### <a name="service-bus-queue"></a>Service Bus 큐
Application Insights는 새 [.NET용 Microsoft Azure Service Bus 클라이언트](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) 버전 3.0.0 이상을 사용하여 Service Bus 메시징 호출을 추적합니다.
[메시지 처리기 패턴](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)을 사용하여 메시지를 처리하는 경우 특별히 수행할 작업이 없습니다. 서비스에서 수행하는 모든 Service Bus 호출이 자동으로 추적되고 다른 원격 분석 항목과 상호 연결됩니다. 수동으로 메시지를 처리하는 경우 [Microsoft Application Insights를 사용하여 Service Bus 클라이언트 추적](../../service-bus-messaging/service-bus-end-to-end-tracing.md)을 참조하세요.

[WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 패키지를 사용하는 경우 더 참조합니다. Service Bus 큐는 AMQP 프로토콜을 사용하여 Application Insights는 큐 작업을 자동으로 추적하지 않으므로 다음 예제는 Service Bus에 대한 호출을 추적(및 상관 관계 지정)하는 방법을 보여 줍니다.
상관 관계 식별자는 메시지 속성에서 전달됩니다.

#### <a name="enqueue"></a>큐에 넣기

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure Storage 큐
다음 예제에서는 [Azure Storage 큐](../../storage/queues/storage-dotnet-how-to-use-queues.md) 작업을 추적하고 생산자, 소비자 및 Azure Storage 간의 원격 분석 상관 관계를 지정하는 방법을 보여 줍니다. 

Storage 큐에는 HTTP API가 있습니다. 큐에 대한 모든 호출은 HTTP 요청에 대한 Application Insights 종속성 수집기에서 추적됩니다.
`applicationInsights.config`에 `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer`가 있는지 확인합니다. 없는 경우 [Azure Application Insights SDK에서 필터링 및 전처리](../../azure-monitor/app/api-filtering-sampling.md)에서 설명한 대로 프로그래밍 방식으로 추가합니다.

Application Insights를 수동으로 구성하는 경우 다음과 비슷하게 `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`을 만들고 초기화해야 합니다.
 
```csharp
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

또한 Application Insights 작업 ID와 Storage 요청 ID 사이의 상관 관계를 지정할 수도 있습니다. Storage 요청 클라이언트와 서버 요청 ID를 설정하고 가져 오는 방법에 대한 자세한 내용은 [Azure Storage 모니터링, 진단 및 문제 해결](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing)을 참조하세요.

#### <a name="enqueue"></a>큐에 넣기
Storage 큐는 HTTP API를 지원하므로 큐를 통한 모든 작업은 Application Insights에서 자동으로 추적됩니다. 대부분의 경우 이 계측으로 충분합니다. 그러나 생산자 추적과 소비자 쪽 추적 사이의 상관 관계를 지정하려면 상관 관계에 대한 HTTP 프로토콜에서 수행하는 것과 비슷한 일부 상관 관계 컨텍스트를 전달해야 합니다. 

이 예제는 `Enqueue` 작업을 추적하는 방법을 보여 줍니다. 다음을 수행할 수 있습니다.

 - **상관 관계 지정 재시도(있는 경우)**: 모든 작업에는 `Enqueue` 작업인 하나의 공통 부모가 있습니다. 그렇지 않으면 들어오는 요청의 자식으로 추적됩니다. 큐에 대한 논리적 요청이 여러 개 있으면 재시도가 발생한 호출을 찾는 것이 어려울 수 있습니다.
 - **상관 관계 지정 스토리지 로그(필요한 경우)**: Application Insights 원격 분석과 상관 관계가 지정됩니다.

`Enqueue` 작업은 부모 작업의 자식(예 : 들어오는 HTTP 요청)입니다. HTTP 종속성 호출은 `Enqueue` 작업의 자식 및 들어오는 요청의 손자입니다.

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

애플리케이션 보고서에서 원격 분석의 양을 줄이거나 다른 이유로 `Enqueue` 작업을 추적하지 않으려면 `Activity` API를 직접 사용합니다.

- Application Insights 작업을 시작하는 대신 새 `Activity`를 만듭니다(및 시작합니다). 작업 이름을 제외한 모든 속성을 *지정하지 않아도 됩니다*.
- `operation.Telemetry.Id` 대신 메시지 페이로드에 `yourActivity.Id`를 직렬화합니다. 또한 `Activity.Current.Id`도 사용할 수 있습니다.


#### <a name="dequeue"></a>큐에서 제거
`Enqueue`와 비슷하게 Storage 큐에 대한 실제 HTTP 요청은 Application Insights에서 자동으로 추적됩니다. 그러나 `Enqueue` 작업은 아마도 들어오는 요청 컨텍스트와 같은 부모 컨텍스트에서 발생합니다. Application Insights SDK는 이러한 작업(및 해당하는 HTTP 부분)과 부모 요청 및 동일한 범위에서 보고되는 다른 원격 분석 사이의 상관 관계를 자동으로 지정합니다.

`Dequeue` 작업은 까다롭습니다. Application Insights SDK에서 HTTP 요청을 자동으로 추적합니다. 그러나 메시지가 구문 분석될 때까지 상관 관계 컨텍스트를 인식할 수 없습니다. 원격 분석의 나머지 부분과 메시지를 받기 위한 HTTP 요청 사이의 상관 관계는 지정할 수 없습니다.

대부분의 경우 큐에 대한 HTTP 요청과 다른 추적 사이의 상관 관계를 지정하는 것도 유용할 수 있습니다. 다음 예제에서는 이를 수행하는 방법을 보여 줍니다.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

다음 예제에서 들어오는 메시지는 들어오는 HTTP 요청과 비슷한 방식으로 추적됩니다.

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

마찬가지로 다른 큐 작업도 계측할 수 있습니다. 피크 작업은 큐에서 제거 작업과 비슷한 방식으로 계측해야 합니다. 큐 관리 작업 계측은 필요하지 않습니다. Application Insights는 HTTP와 같은 작업을 추적하며, 이는 대부분의 경우에 충분합니다.

메시지 삭제를 계측할 때는 작업(상관 관계) 식별자를 설정해야 합니다. 또는 `Activity` API를 사용할 수 있습니다. 그러면 Application Insights SDK에서 사용자를 대신하여 이 작업을 수행하므로 원격 분석 항목에서 작업 식별자를 설정할 필요가 없습니다.

- 큐에서 항목을 가져온 후 새 `Activity`를 만듭니다.
- `Activity.SetParentId(message.ParentId)`를 사용하여 소비자와 생산자 로그의 상관 관계를 지정합니다.
- `Activity`를 시작합니다.
- `Start/StopOperation` 도우미를 사용하여 큐에서 제거, 처리 및 삭제 작업을 추적합니다. 동일한 비동기 제어 흐름(실행 컨텍스트)에서 수행합니다. 이런 방식으로 상관 관계가 제대로 지정됩니다.
- `Activity`를 중지합니다.
- `Start/StopOperation`을 사용하거나 수동으로 `Track` 원격 분석을 호출합니다.

### <a name="batch-processing"></a>일괄 처리
일부 큐의 경우 하나의 요청으로 여러 메시지를 큐에서 제거할 수 있습니다. 이러한 메시지를 처리하는 것은 아마도 독립적이며 다른 논리 연산에 속합니다. 이 경우 `Dequeue` 작업을 특정 메시지 처리와 상호 연결할 수 없게 됩니다.

각 메시지는 자체 비동기 제어 흐름에서 처리되어야 합니다. 자세한 내용은 [나가는 종속성 추적](#outgoing-dependencies-tracking) 섹션을 참조하세요.

## <a name="long-running-background-tasks"></a>장기 실행 백그라운드 작업 실행 

일부 애플리케이션은 사용자 요청으로 인해 발생할 수 있는 장기 실행 작업을 시작합니다. 추적/계측 관점에서 이는 요청이나 종속성 계측과 다르지 않습니다. 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

이 예제에서 `telemetryClient.StartOperation`은 `DependencyTelemetry`를 만들고 상관 컨텍스트를 채웁니다. 작업을 예약한 들어오는 요청으로 만들어진 부모 작업이 있다고 가정해 보겠습니다. 들어오는 요청과 동일한 비동기 제어 흐름에서 `BackgroundTask`가 시작되는 한 해당 부모 작업과 상관 관계가 지정됩니다. `BackgroundTask` 및 모든 중첩된 원격 분석 항목은 요청이 종료된 후에도 원인이 된 요청과의 상관 관계가 자동으로 지정됩니다.

연결된 작업(`Activity`)이 없는 백그라운드 스레드에서 작업이 시작되면 `BackgroundTask`에는 부모가 없습니다. 그러나 중첩된 작업이 있을 수 있습니다. 이 작업에서 보고되는 모든 원격 분석 항목과 `BackgroundTask`에서 만들어진 `DependencyTelemetry` 사이의 상관 관계가 지정됩니다.

## <a name="outgoing-dependencies-tracking"></a>나가는 종속성 추적
사용자 고유의 종속성 종류 또는 Application Insights에서 지원하지 않는 작업을 추적할 수 있습니다.

Service Bus 큐의 `Enqueue` 메서드 또는 Azure Storage 큐는 이러한 사용자 지정 추적의 예제로 사용할 수 있습니다.

사용자 지정 종속성 추적을 위한 일반적인 방법은 다음과 같습니다.

- 상관 관계 및 일부 다른 속성(시작 타임스탬프, 기간)에 필요한 `DependencyTelemetry` 속성을 채우는 `TelemetryClient.StartOperation`(확장) 메서드를 호출합니다.
- 이름 및 기타 필요한 컨텍스트와 같이 `DependencyTelemetry`에서 다른 사용자 지정 속성을 설정합니다.
- 종속성을 호출하고 기다립니다.
- 완료되면 `StopOperation`을 통해 작업을 중지합니다.
- 예외를 처리합니다.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

작업을 삭제하면 작업이 중지되므로 `StopOperation`을 호출하는 대신 수행할 수 있습니다.

*경고*: 일부 경우에 처리되지 않은 예외는 `finally`가 호출되는 것을 [방지](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally)하므로 작업은 추적되지 않을 수 있습니다.

### <a name="parallel-operations-processing-and-tracking"></a>병렬 작업 처리 및 추적

`StopOperation`은 시작된 작업만 중지합니다. 현재 실행 중인 작업이 중지하려는 작업과 일치하지 않으면 `StopOperation`에서 아무 작업도 수행하지 않습니다. 이 경우 동일한 실행 컨텍스트에서 여러 작업을 동시에 시작하면 발생할 수 있습니다.

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

항상 `StartOperation`을 호출하고 동일한 **async** 메서드에서 작업을 처리하여 병렬로 실행 중인 작업을 격리해야 합니다. 작업이 동기이면(또는 비동기가 아님) 프로세스를 래핑하고 `Task.Run`을 사용하여 추적합니다.

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="next-steps"></a>다음 단계

- Application Insights에서 [원격 분석 상관 관계](correlation.md) 기본 사항을 알아봅니다.
- Application Insights 유형 및 데이터 모델은 [데이터 모델](../../azure-monitor/app/data-model.md)을 참조합니다.
- 사용자 지정 [이벤트 및 메트릭](../../azure-monitor/app/api-custom-events-metrics.md)을 Application Insights에 보고합니다.
- 컨텍스트 속성 컬렉션에 대한 표준 [구성](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)을 확인합니다.
- [System.Diagnostics.Activity 사용자 가이드](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)에서 원격 분석 상관 관계를 지정하는 방법을 확인합니다.
