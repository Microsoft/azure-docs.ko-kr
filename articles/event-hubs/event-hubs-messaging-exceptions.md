---
title: 문제 해결 가이드-Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs 메시징 예외 및 제안된 작업의 목록을 제공합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: de5b95bd10bf72f60ba5d63c4b3a799556fcce33
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309781"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Azure Event Hubs에 대한 문제 해결 가이드
이 문서에서는 Event Hubs .NET Framework Api에 의해 생성 되는 몇 가지 .NET 예외 및 문제 해결을 위한 기타 팁을 제공 합니다. 

## <a name="event-hubs-messaging-exceptions---net"></a>Event Hubs 메시징 예외-.NET
이 섹션에서는 .NET Framework Api에 의해 생성 된 .NET 예외를 나열 합니다. 

### <a name="exception-categories"></a>예외 범주

Event Hubs .NET Api는 다음 범주에 해당 하는 예외를 생성 하 고,이를 해결 하기 위해 수행할 수 있는 관련 작업을 수행 합니다.

1. 사용자 코딩 오류: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 일반 조치: 코드를 수정한 후 계속합니다.
2. 설치/구성 오류: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) 일반 조치: 구성을 검토하고 필요한 경우 변경합니다.
3. 일시적 예외: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) 일반 조치: 작업을 다시 시도하거나 사용자에게 알립니다.
4. 기타 예외: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). 일반 작업: 예외 형식에 특정됩니다. 다음 섹션의 테이블을 참조하세요. 

### <a name="exception-types"></a>예외 유형
다음 표에서는 메시징 예외 유형과 원인, 사용자가 수행할 수 있는 제안 조치 참고를 열거합니다.

| 예외 유형 | 설명/원인/예 | 권장 조치 | 자동/즉시 다시 시도 참고 |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |서버가 지정 된 시간 내에 요청 된 작업에 응답 하지 않았습니다 .이 작업은 [Operationtimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)에 의해 제어 됩니다. 서버가 요청된 작업을 완료했을 수도 있습니다. 이 예외는 네트워크 또는 기타 인프라 지연으로 인해 발생할 수 있습니다. |필요한 경우 시스템 상태에서 일관성을 확인하고 다시 시도합니다.<br /> [TimeoutException](#timeoutexception)을 참조하세요. | 일부 경우 다시 시도하면 문제가 해결될 수 있습니다. 코드에 재시도 논리를 추가합니다. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |요청 된 사용자 작업은 서버 또는 서비스 내에서 허용 되지 않습니다. 자세한 내용은 예외 메시지를 참조하세요. 예를 들어 [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 모드에서 메시지를 받은 경우 [Complete](/dotnet/api/microsoft.servicebus.messaging.receivemode) 가 이 예외를 생성합니다. | 코드 및 설명서를 확인합니다. 요청된 작업이 유효한지 확인합니다. | 다시 시도 해도 도움이 되지 않습니다. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | 이미 종료, 중단 또는 삭제된 개체에서 작업을 호출하려 시도합니다. 드문 경우지만 앰비언트 트랜잭션이 이미 삭제되었을 수 있습니다. | 코드를 확인 하 고 삭제 된 개체에 대한 작업을 호출 하지 않는지 확인 합니다. | 다시 시도 해도 도움이 되지 않습니다. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | [Tokenprovider](/dotnet/api/microsoft.servicebus.tokenprovider) 개체가 토큰을 가져올 수 없습니다. 토큰이 잘못 되었거나 토큰에 작업을 수행 하는 데 필요한 클레임이 포함 되어 있지 않습니다. | 올바른 값을 사용하여 토큰 공급자를 만드는지 확인합니다. Access Control Service의 구성을 확인 합니다. | 일부 경우 다시 시도하면 문제가 해결될 수 있습니다. 코드에 재시도 논리를 추가합니다. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | 메서드에 제공된 하나 이상의 인수가 잘못되었습니다. [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 또는 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory)에 제공된 URI에 경로 세그먼트가 포함됩니다. [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 또는 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory)에 제공된 URI 스키마가 올바르지 않습니다. 속성 값이 32KB보다 큽니다. | 호출 코드를 확인하고 인수가 정확한지 확인합니다. | 재시도로 해결되지 않습니다. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | 작업과 연결된 엔터티가 없거나 삭제되었습니다. | 엔터티가 있는지 확인합니다. | 재시도로 해결되지 않습니다. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | 클라이언트가 이벤트 허브로 연결을 설정할 수 없습니다. |제공된 호스트 이름이 정확하며 호스트에 연결할 수 있는지 확인합니다. | 간헐적인 연결 문제라면 재시도로 문제를 해결할 수 있습니다. |
| [ServiceBus ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | 서비스가 지금은 요청을 처리할 수 없습니다. | 클라이언트가 잠시 대기한 후 작업을 다시 시도할 수 있습니다. <br /> [ServerBusyException](#serverbusyexception)을 참조하세요. | 클라이언트가 일정 시간 이후에 다시 시도할 수 있습니다. 재시도에서 다른 예외가 발생한 경우 해당 예외의 재시도 작동을 확인합니다. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | 다음 상황에 발생할 수 있는 일반 메시징 예외: 다른 엔터티 형식에 속하는(예: topic) 이름이나 경로를 사용하여 [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) 를 만들려고 시도합니다. 1MB보다 큰 메시지를 전송하려고 시도합니다. 서버 또는 서비스가 요청을 처리하는 동안 오류가 발생했습니다. 자세한 내용은 예외 메시지를 참조하세요. 이것은 보통 일시적인 예외입니다. | 코드를 확인하고 메시지 본문에서 직렬화 가능 개체(또는 사용자 지정 직렬 변환기 사용)만 사용하는지 확인합니다. 설명서에서 지원되는 속성 값 유형을 확인하고 지원되는 유형만 사용합니다. [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) 속성을 확인합니다. 이 값이 **true**이면 작업을 다시 시도할 수 있습니다. | 재시도 동작이 정의되지 않았으면 도움이 되지 않습니다. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | 해당 서비스 네임스페이스에서 이미 다른 엔터티가 사용하는 이름으로 엔터티를 만들려고 합니다. | 기존 엔터티를 삭제하거나 만들려는 엔터티에 다른 이름을 선택합니다. | 재시도로 해결되지 않습니다. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | 메시징 엔터티의 최대 허용 크기에 도달했습니다. 이 예외는 소비자별 그룹 수준에서 최대 수신자 수(5)가 이미 열려 있는 경우에 발생할 수 있습니다. | 엔터티나 하위 큐에서 메시지를 수신하여 엔터티에 공간을 만듭니다. <br /> [QuotaExceededException](#quotaexceededexception)을 참조하세요. | 그 사이 메시지가 제거되었으면 재시도가 도움이 될 수 있습니다. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | 비활성화된 엔터티의 런타임 작업에 대한 요청입니다. |엔터티를 활성화합니다. | 그 사이에 엔터티가 활성화된 경우 다시 시도하면 문제가 해결될 수 있습니다. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | 메시지 페이로드가 1mb 제한을 초과 합니다. 이 1mb 제한은 시스템 속성 및 모든 .NET 오버 헤드를 포함할 수 있는 총 메시지 수에 대한 것입니다. | 메시지 페이로드의 크기를 줄인 다음 작업을 다시 시도합니다. |재시도로 해결되지 않습니다. |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) 은 특정 엔터티에 대한 할당량이 초과됐음을 나타냅니다.

이 예외는 소비자별 그룹 수준에서 최대 수신자 수(5)가 이미 열려 있는 경우에 발생할 수 있습니다.

#### <a name="event-hubs"></a>Event Hubs
Event Hubs는 이벤트 허브당 20개의 소비자 그룹으로 제한됩니다. 더 만들려고 하면 [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception)을 받습니다. 

### <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) 은 사용자가 시작한 작업이 작업 시간 제한보다 더 오래 걸린다는 것을 나타냅니다. 

Event Hubs의 경우 시간 제한은 연결 문자열의 일부로 또는 [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder)를 통해 지정됩니다. 오류 메시지 자체는 다를 수 있지만 현재 작업에 대해 지정된 시간 제한 값을 항상 포함합니다. 

#### <a name="common-causes"></a>일반적인 원인
이 오류에 대한 일반적인 두 가지 원인은 잘못된 구성 또는 일시적 서비스 오류입니다.

1. **잘못된 구성** 작동 조건에 대한 작업 시간 제한이 너무 작을 수도 있습니다. 클라이언트 SDK에서 작업 시간 제한에 대한 기본값은 60초입니다. 코드에 너무 작은 값으로 설정된 값이 있는지 확인합니다. 네트워크 및 CPU 사용량의 조건은 특정 작업을 완료 하는 데 걸리는 시간에 영향을 줄 수 있으므로 작업 시간 제한을 작은 값으로 설정 하면 안 됩니다.
2. **일시적 서비스 오류** Event Hubs 서비스에서 예를 들어 트래픽이 높은 기간 동안 요청 처리에 지연이 발생할 수 있습니다. 이러한 경우 작업이 성공할 때까지 지연 후 작업을 다시 시도할 수 있습니다. 동일한 작업을 여러 번 시도한 후에도 계속 실패하는 경우 알려진 서비스 중단이 있는지 [Azure 서비스 상태 사이트](https://azure.microsoft.com/status/) 를 방문하세요.

### <a name="serverbusyexception"></a>ServerBusyException

[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) 또는 [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception)은 서버가 오버로드되었음을 나타냅니다. 이 예외에 대한 두 개의 관련 오류 코드가 있습니다.

#### <a name="error-code-50002"></a>오류 코드 50002

이 오류는 다음 두 가지 이유 중 하나로 발생할 수 있습니다.

1. 이벤트 허브의 모든 파티션에서 부하가 균등 하 게 분산 되지 않으며 하나의 파티션이 로컬 처리량 단위 제한에 도달 합니다.
    
    해결 방법: 파티션 분산 전략을 수정하거나 [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient)를 시도하면 도움이 될 수 있습니다.

2. Event Hubs 네임 스페이스에 충분 한 처리량 단위가 없습니다. [Azure Portal](https://portal.azure.com) 의 Event Hubs 네임 스페이스 창에서 **메트릭** 화면을 확인 하 여 확인할 수 있습니다. 포털에는 집계 된 (1 분) 정보가 표시 되지만 처리량은 실시간으로 측정 되므로 예상에 불과합니다.

    해결 방법: 네임스페이스의 처리량 단위를 늘리면 도움이 될 수 있습니다. 포털의 Event Hubs 네임스페이스 화면에 있는 **규모** 창에서 이 작업을 수행할 수 있습니다. 또는 [자동 팽창](event-hubs-auto-inflate.md)을 사용할 수 있습니다.

#### <a name="error-code-50001"></a>오류 코드 50001

이 오류는 드물게 발생합니다. 네임스페이스에 대한 코드를 실행하는 컨테이너의 CPU가 낮아서 Event Hubs 부하 분산 장치가 몇 초 이내에 시작되지 못할 때 이 오류가 발생합니다.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>GetRuntimeInformation 메서드에 대한 호출 제한
Azure Event Hubs는 초당 GetRuntimeInfo에 대한 초당 최대 50 개의 호출을 지원 합니다. 한도에 도달 하면 다음과 유사한 예외가 표시 될 수 있습니다.

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>연결, 인증서 또는 시간 제한 문제
다음 단계는 *. servicebus.windows.net의 모든 서비스에 대한 연결/인증서/시간 제한 문제를 해결 하는 데 도움이 될 수 있습니다. 

- `https://<yournamespacename>.servicebus.windows.net/`또는 [wget](https://www.gnu.org/software/wget/) 으로 이동 합니다. IP 필터링 또는 가상 네트워크 또는 인증서 체인 문제가 있는지 여부를 확인 하는 데 도움이 됩니다 (java SDK를 사용 하는 경우 가장 일반적으로).

    성공적인 메시지의 예:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    오류 메시지의 예는 다음과 같습니다.

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 다음 명령을 실행 하 여 방화벽에서 차단 된 포트가 있는지 확인 합니다. 사용 되는 포트는 443 (HTTPS), 5671 (AMQP) 및 9093 (Kafka)입니다. 사용 하는 라이브러리에 따라 다른 포트도 사용 됩니다. 다음은 5671 포트가 차단 되었는지 여부를 확인 하는 샘플 명령입니다.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linux에서:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 간헐적 연결 문제가 있는 경우 다음 명령을 실행 하 여 삭제 된 패킷이 있는지 확인 합니다. 이 명령은 서비스와 1 초 마다 25 가지 TCP 연결을 설정 하려고 시도 합니다. 그런 다음 성공/실패 횟수를 확인 하 고 TCP 연결 대기 시간도 확인할 수 있습니다. `psping` 도구는 [여기](/sysinternals/downloads/psping)에서 다운로드할 수 있습니다.

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    `tnc`, `ping`등의 다른 도구를 사용 하는 경우에는 동일한 명령을 사용할 수 있습니다. 
- 이전 단계에서 [Wireshark](https://www.wireshark.org/)와 같은 도구를 사용 하 여 도움을 주지 않고 분석 하는 경우 네트워크 추적을 가져옵니다. 필요한 경우 [Microsoft 지원](https://support.microsoft.com/) 에 문의 하세요. 

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)
