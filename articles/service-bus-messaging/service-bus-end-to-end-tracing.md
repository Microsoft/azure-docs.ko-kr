---
title: Azure Service Bus 엔드투엔드 추적 및 진단 | Microsoft Docs
description: Service Bus 클라이언트 진단 및 종단 간 추적 (처리에 관련 된 모든 서비스를 통해 클라이언트)에 대 한 개요입니다.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9b46f85e16370d15e3a8def98cdcdf8b3878208d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021632"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Service Bus 메시징을 통한 분산 추적 및 상관관계

마이크로 서비스 개발의 일반적인 문제점 중 하나는 처리에 관련된 모든 서비스를 통해 클라이언트의 작업을 추적할 수 있다는 것입니다. 디버깅, 성능 분석, A/B 테스트 및 기타 일반적인 진단 시나리오에 유용합니다.
이 문제점의 한 부분은 논리적 작업 추적입니다. 메시지 처리 결과와 대기 시간 및 외부 종속성 호출이 포함됩니다. 다른 부분은 프로세스 경계를 벗어나는 이러한 진단 이벤트의 상관관계입니다.

생산자가 큐를 통해 메시지를 보내는 경우 일반적으로 다른 클라이언트 또는 서비스에서 시작된 다른 논리적 작업 범위에서 발생합니다. 소비자가 메시지를 수신하면 동일한 작업이 계속됩니다. 생산자와 소비자(작업을 처리하는 다른 서비스)가 둘 다 원격 분석 이벤트를 내보내 작업 흐름과 결과를 추적합니다. 이러한 이벤트와 추적 작업을 엔드투엔드에 상호 연결하려면 원격 분석을 보고하는 각 서비스가 추적 컨텍스트를 사용하여 모든 이벤트에 스탬프를 지정해야 합니다.

Microsoft Azure Service Bus 메시징에는 생산자와 소비자가 이러한 추적 컨텍스트를 전달하는 데 사용하는 페이로드 속성이 정의되어 있습니다.
프로토콜은 [HTTP 상관관계 프로토콜](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)을 기반으로 합니다.

| 속성 이름        | 설명                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | 큐에 대한 생산자의 외부 호출 고유 식별자입니다. 이유, 고려 사항 및 형식은 [HTTP 프로토콜의 Request-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id)를 참조하세요. |
|  Correlation-Context | 작업 처리에 관련된 모든 서비스에 전파되는 작업 컨텍스트입니다. 자세한 내용은 [HTTP 프로토콜의 Correlation-Context](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context)를 참조하세요. |

## <a name="service-bus-net-client-autotracing"></a>Service Bus .NET 클라이언트 자동 추적

버전 3.0.0부터 [Microsoft Azure ServiceBus Client for .NET](/dotnet/api/microsoft.azure.servicebus.queueclient)은 추적 시스템 또는 클라이언트 코드 조각에서 연결할 수 있는 추적 계측 지점을 제공합니다.
계측을 사용하면 클라이언트 쪽에서 모든 Service Bus 메시징 서비스 호출을 추적할 수 있습니다. [메시지 처리기 패턴](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)을 사용하여 메시지 처리를 완료하면 메시지 처리도 계측됩니다.

### <a name="tracking-with-azure-application-insights"></a>Azure Application Insights를 사용하여 추적

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/)는 자동 요청 및 종속성 추적을 포함하여 다양한 성능 모니터링 기능을 제공합니다.

프로젝트 유형에 따라 Application Insights SDK를 설치합니다.
- [ASP.NET](../azure-monitor/app/asp-net.md) - 버전 2.5-beta2 이상 설치
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - 버전 2.2.0-beta2 이상 설치
이러한 링크는 SDK 설치, 리소스 만들기 및 SDK 구성(필요한 경우)에 대한 세부 정보를 제공합니다. 비 ASP.NET 애플리케이션의 경우 [콘솔 애플리케이션용 Azure Application Insights](../azure-monitor/app/console.md) 문서를 참조하세요.

[메시지 처리기 패턴](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)을 사용하여 메시지를 처리하는 경우 특별히 수행할 작업이 없습니다. 서비스에서 수행하는 모든 Service Bus 호출이 자동으로 추적되고 다른 원격 분석 항목과 상호 연결됩니다. 또는 수동 메시지 처리 추적은 다음 예제를 참조하세요.

#### <a name="trace-message-processing"></a>추적 메시지 처리

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

이 예제에서는 처리된 각 메시지에 대해 `RequestTelemetry`가 보고되며 타임스탬프, 지속 기간 및 결과(성공)를 포함합니다. 원격 분석에는 상관관계 속성 집합도 있습니다.
또한 메시지 처리 중에 보고된 중첩된 추적 및 예외에 `RequestTelemetry`의 ‘하위’로 표시되는 상관관계 속성을 사용하여 스탬프가 지정됩니다.

메시지 처리 중에 지원되는 외부 구성 요소를 호출하는 경우 해당 구성 요소도 자동으로 추적되고 상호 연결됩니다. 수동 추적 및 상관관계는 [Application Insights .NET SDK를 사용하여 사용자 지정 작업 추적](../azure-monitor/app/custom-operations-tracking.md)을 참조하세요.

Application Insights SDK 외에 외부 코드를 실행 하는 경우 Application Insights 로그를 볼 때 **시간이** 더 오래 걸릴 것입니다. 

![Application Insights 로그의 기간이 깁니다.](./media/service-bus-end-to-end-tracing/longer-duration.png)

메시지를 받는 시간이 지연 된 것은 아닙니다. 이 시나리오에서는 메시지가 SDK 코드에 대 한 매개 변수로 전달 되기 때문에 메시지를 이미 받았습니다. 그리고 App Insights 로그 (**프로세스**)의 **name** 태그는 현재 외부 이벤트 처리 코드에서 메시지를 처리 하 고 있음을 나타냅니다. 이 문제는 Azure와 관련이 없습니다. 대신 이러한 메트릭은 Service Bus에서 메시지를 이미 수신 했을 때 외부 코드의 효율성을 나타냅니다. Service Bus에서 메시지를 받은 후 **프로세스** 태그가 생성 되 고 할당 된 위치를 확인 하려면 [GitHub에서이 파일](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) 을 참조 하세요. 

### <a name="tracking-without-tracing-system"></a>추적 시스템 없이 추적
추적 시스템이 자동 Service Bus 호출 추적을 지원하지 않는 경우 추적 시스템 또는 애플리케이션에 해당 지원을 추가하는 것이 좋습니다. 이 섹션에서는 Service Bus .NET 클라이언트가 보내는 진단 이벤트에 대해 설명합니다.  

Service Bus .NET 클라이언트는 .NET 추적 기본 형식 [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) 및 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)를 사용하여 계측됩니다.

`Activity`는 추적 컨텍스트 역할을 하고, `DiagnosticSource`는 알림 메커니즘입니다. 

DiagnosticSource 이벤트에 대한 수신기가 없는 경우 계측이 해제되어 계층 비용을 0으로 유지합니다. DiagnosticSource는 수신기에 모든 제어를 제공합니다.
- 수신기가 수신 대기할 원본 및 이벤트를 제어함
- 수신기가 이벤트율 및 샘플링을 제어함
- 이벤트는 전체 컨텍스트를 제공하는 페이로드와 함께 전송되므로 이벤트 중 메시지 개체에 액세스하고 수정할 수 있음

구현을 계속 진행하기 전에 [DiagnosticSource 사용자 가이드](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)를 숙지하세요.

ASP.NET Core 앱에서 Microsoft.Extension.Logger로 로그를 기록하는 Service Bus 이벤트 수신기를 만들겠습니다.
이 수신기는 [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) 라이브러리를 사용하여 DiagnosticSource를 구독합니다(라이브러리 없이도 DiagnosticSource를 쉽게 구독할 수 있음).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

이 예제에서 수신기는 각 Service Bus 작업의 지속 기간, 결과, 고유 식별자 및 시작 시간을 기록합니다.

#### <a name="events"></a>이벤트

각 작업에 대해 두 개의 이벤트(‘Start’ 및 ‘Stop’)가 전송됩니다. 대체로 ‘Stop’ 이벤트에만 관심이 있습니다. 작업의 결과 뿐만 아니라 시작 시간 및 기간을 작업 속성으로 제공 합니다.

이벤트 페이로드는 수신기에 작업 컨텍스트를 제공하며 API 수신 매개 변수 및 반환 값을 복제합니다. ‘Stop’ 이벤트 페이로드에 ‘Start’ 이벤트 페이로드의 모든 속성이 있으므로 ‘Start’ 이벤트는 완전히 무시해도 됩니다.

모든 이벤트에는 ‘Entity’ 및 ‘Endpoint’ 속성도 있으며, 아래 표에서는 생략되었습니다.
  * `string Entity` - 엔터티(큐, 토픽 등) 이름
  * `Uri Endpoint` - Service Bus 엔드포인트 URL

각 ‘Stop’ 이벤트에는 `TaskStatus` 비동기 작업이 완료된 `Status` 속성이 있으며, 다음 표에서는 이 속성도 간단한 설명을 위해 생략되었습니다.

다음은 계측되는 작업의 전체 목록입니다.

| 작업 이름 | 추적된 API | 특정 페이로드 속성|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` - 전송 중인 메시지 목록 |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` - 처리 중인 메시지<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` - 예약된 메시지 오프셋<br/>`long SequenceNumber` - 예약된 메시지의 시퀀스 번호(‘Stop’ 이벤트 페이로드) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` - 취소할 메시지의 시퀀스 번호 | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` - 수신할 수 있는 최대 메시지 수<br/>`IList<Message> Messages` - 수신된 메시지 목록(‘Stop’ 이벤트 페이로드) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` - 메시지 배치를 찾아볼 시작 지점<br/>`int RequestedMessageCount` - 검색할 메시지 수<br/>`IList<Message> Messages` - 수신된 메시지 목록(‘Stop’ 이벤트 페이로드) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` - 수신할 시퀀스 번호가 포함된 목록<br/>`IList<Message> Messages` - 수신된 메시지 목록(‘Stop’ 이벤트 페이로드) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` - 완료할 해당 메시지의 잠금 토큰이 포함된 목록|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` - 중단할 해당 메시지의 잠금 토큰 |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` - 지연할 해당 메시지의 잠금 토큰 | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` - 배달 못한 편지로 처리할 해당 메시지의 잠금 토큰 | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` - 잠금을 갱신할 해당 메시지의 잠금 토큰<br/>`DateTime LockedUntilUtc` - 새 잠금 토큰 만료 날짜 및 시간(UTC 형식) (‘Stop’ 이벤트 페이로드)|
| Microsoft.Azure.ServiceBus.Process | [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler)에 제공되는 메시지 처리기 람다 함수 | `Message Message` - 처리 중인 메시지 |
| Microsoft.Azure.ServiceBus.ProcessSession | [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler)에 제공되는 메시지 세션 처리기 람다 함수 | `Message Message` - 처리 중인 메시지<br/>`IMessageSession Session` - 처리 중인 세션 |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` - 추가할 규칙을 제공하는 규칙 설명 |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` - 제거할 규칙의 이름 |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` - 구독과 연결된 모든 규칙 ('Stop' 페이로드만 해당) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` - 메시지에 있는 세션 ID |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` - 메시지에 있는 세션 ID<br/>`byte [] State` - 세션 상태(‘Stop’ 이벤트 페이로드) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` - 메시지에 있는 세션 ID<br/>`byte [] State` - 세션 상태 |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` - 메시지에 있는 세션 ID |
| Microsoft.Azure.ServiceBus.Exception | 모든 계측된 API| `Exception Exception` - 예외 인스턴스 |

모든 이벤트에서 현재 작업 컨텍스트가 포함된 `Activity.Current`에 액세스할 수 있습니다.

#### <a name="logging-additional-properties"></a>추가 속성 기록

`Activity.Current`는 현재 작업과 해당 부모의 자세한 컨텍스트를 제공합니다. 자세한 내용은 [활동 설명서](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)를 참조하세요.
Service Bus 계측은 `Activity.Current.Tags`에 추가 정보를 제공하며, 사용 가능한 경우 `MessageId` 및 `SessionId`가 포함됩니다.

‘Receive’, ‘Peek’ 및 ‘ReceiveDeferred’ 이벤트를 추적하는 활동에는 `RelatedTo` 태그도 포함될 수 있습니다. 이 태그는 결과로 수신된 메시지의 `Diagnostic-Id` 목록을 포함합니다.
이러한 작업으로 인해 관련 없는 여러 메시지가 수신될 수 있습니다. 또한, 작업을 시작할 때 `Diagnostic-Id`를 알 수 없으므로 이 태그만 사용하여 ‘Receive’ 작업을 ‘Process’ 작업에 상호 연결할 수 있습니다. 이 태그는 메시지를 수신하는 데 걸리는 시간을 확인하기 위해 성능 문제를 분석할 때 유용합니다.

태그를 기록하는 효율적인 방법은 태그를 반복하는 것으로, 앞의 예제에 입니다. 따라서 앞의 예제에 태그를 추가하면 다음과 같이 표시됩니다. 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>필터링 및 샘플링

이벤트의 일부만 기록하여 성능 오버헤드 또는 스토리지 소비량을 줄이는 것이 바람직한 경우도 있습니다. 앞의 예제와 같이 ‘Stop’ 이벤트만 기록하거나 이벤트의 샘플 비율을 기록할 수 있습니다. 
`DiagnosticSource`는 `IsEnabled` 조건자로 이 작업을 수행하는 방법을 제공합니다. 자세한 내용은 [DiagnosticSource의 컨텍스트 기반 필터링](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering)을 참조하세요.

성능 영향을 최소화하기 위해 단일 작업에 대해 `IsEnabled`를 여러 번 호출할 수 있습니다.

`IsEnabled`는 다음 순서로 호출됩니다.

1. `IsEnabled(<OperationName>, string entity, null)`(예: `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`). 끝에 ‘Start’ 또는 ‘Stop’이 없습니다. 특정 작업이나 큐를 필터링하는 데 사용합니다. 콜백이 `false`를 반환하는 경우 작업에 대한 이벤트가 전송되지 않습니다.

   * ‘Process’ 및 ‘ProcessSession’ 작업의 경우 `IsEnabled(<OperationName>, string entity, Activity activity)` 콜백도 수신됩니다. `activity.Id` 또는 태그 속성을 기준으로 이벤트를 필터링하는 데 사용합니다.
  
2. `IsEnabled(<OperationName>.Start)`(예: `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`). ‘Start’ 이벤트를 실행할지 여부를 확인합니다. 결과는 ‘Start’ 이벤트에만 영향을 주지만 추가 계측은 결과에 종속되지 않습니다.

‘Stop’ 이벤트에 대한 `IsEnabled`는 없습니다.

일부 작업 결과가 예외이면 `IsEnabled("Microsoft.Azure.ServiceBus.Exception")`이 호출됩니다. ‘Exception’ 이벤트만 구독하고 나머지 계측을 차단할 수 있습니다. 이 경우에도 해당 예외를 처리해야 합니다. 다른 계측이 해제되었으므로 추적 컨텍스트가 메시지와 함께 소비자에서 생산자로 이동할 것으로 예상하면 안 됩니다.

`IsEnabled`를 사용하여 샘플링 전략을 구현할 수도 있습니다. `Activity.Id` 또는 `Activity.RootId`를 기준으로 샘플링하면 추적 시스템 또는 사용자 고유 코드를 통해 전파되는 한 모든 계층에서 일관된 샘플링이 보장됩니다.

동일한 원본에 대해 여러 개의 `DiagnosticSource` 수신기가 있는 경우 한 수신기만 이벤트를 수락하면 되므로 `IsEnabled` 호출이 보장되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Application Insights 상관관계](../azure-monitor/app/correlation.md)
* [Application Insights 종속성 모니터링](../azure-monitor/app/asp-net-dependencies.md) - REST, SQL 또는 다른 외부 리소스의 속도가 느려지는지 확인합니다.
* [Application Insights .NET SDK를 통한 사용자 지정 작업 추적](../azure-monitor/app/custom-operations-tracking.md)
