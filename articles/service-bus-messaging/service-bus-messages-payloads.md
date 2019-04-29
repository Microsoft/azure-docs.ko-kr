---
title: Azure Service Bus 메시지, 페이로드 및 serialization | Microsoft Docs
description: Service Bus 메시지 페이로드 개요
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: aschhab
ms.openlocfilehash: 3158f0255810c66605d28856133112181c2916db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315642"
---
# <a name="messages-payloads-and-serialization"></a>메시지, 페이로드 및 serialization

Microsoft Azure Service Bus는 메시지를 처리합니다. 메시지는 키-값 쌍 속성의 형태로 메타데이터 뿐만 아니라 페이로드를 전달하며 페이로드를 설명하고 Service Bus 및 애플리케이션에 처리 지침을 제공합니다. 경우에 따라 메타데이터만으로 보낸 사람이 수신자에게 제공하려는 정보를 전달하는 데 충분하며 페이로드는 빈 상태를 유지합니다.

.NET 및 Java에 대한 공식 Service Bus 클라이언트의 개체 모델은 Service Bus가 지원하는 와이어 프로토콜과 매핑되는 추상 Service Bus 메시지 구조를 반영합니다.
 
Service Bus 메시지는 Service Bus가 서비스 쪽에서 어떤 형식으로든 절대 처리하지 못하는 이진 페이로드 섹션과 2개의 속성 집합으로 이루어져 있습니다. *브로커 속성*은 시스템에서 미리 정의됩니다. 이러한 미리 정의된 속성은 브로커 내의 메시지 수준 기능을 제어하거나 일반적이고 표준화된 메타데이터 항목에 매핑됩니다. *사용자 속성*은 애플리케이션에 의해 정의되고 설정될 수 있는 키-값 쌍의 컬렉션입니다.
 
미리 정의된 브로커 속성은 다음 표에 나와 있습니다. 이름은 모든 공식 클라이언트 API에서 사용되며 HTTP 프로토콜 매핑의 [BrokerProperties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) JSON 개체에도 있습니다.
 
AMQP 프로토콜 수준에서 사용되는 해당 이름은 괄호로 묶어 표시됩니다. 

| 속성 이름                         | 설명                                                                                                                                                                                                                                                                                                                                                                                                                               |
|---------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|  [ContentType](/dotnet/api/microsoft.azure.servicebus.message.contenttype)(content-type)           | 필요에 따라 RFC2045, 섹션 5의 형식(예: `application/json`)에 따라 설명자를 사용하여 메시지의 페이로드를 설명합니다.                                                                                                                                                                                                                                                                                             |
|  [CorrelationId](/dotnet/api/microsoft.azure.servicebus.message.correlationid#Microsoft_Azure_ServiceBus_Message_CorrelationId)(correlation-id)       | 애플리케이션이 상관 관계(예: 회신되는 메시지의 **MessageId** 반영)를 위해 메시지에 대한 컨텍스트를 지정할 수 있도록 합니다.                                                                                                                                                                                                                                                                  |
| [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource)                      | 배달 못 한 메시지로 처리되고 이후에 배달 못 한 메시지 큐에서 다른 엔터티로 자동 전달된 메시지에만 설정됩니다. 메시지가 배달 못 한 메시지로 처리된 엔터티를 나타냅니다. 이 속성은 읽기 전용입니다.                                                                                                                                                                                                                                  |
| [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deliverycount)                         | 이 메시지에 대해 시도된 배달 횟수입니다. 메시지 잠금이 만료되거나 메시지가 수신자에 의해 명시적으로 중단될 때 이 횟수가 증가합니다. 이 속성은 읽기 전용입니다.                                                                                                                                                                                                                                                  |
| [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber)                | 자동 전달된 메시지의 경우, 이 속성은 원래 전송 지점에서 메시지에 처음 할당된 시퀀스 번호를 반영합니다. 이 속성은 읽기 전용입니다.                                                                                                                                                                                                                                                                |
| [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc)                       | 메시지가 수락되고 엔터티에 저장된 UTC 인스턴트입니다. 이 값은 수신자가 보낸 사람의 클럭을 신뢰하지 않으려고 할 때 신뢰할 수 있는 중립 도착 시간 표시로 사용할 수 있습니다. 이 속성은 읽기 전용입니다.                                                                                                                                                                                                   |
|  [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc)(absolute-expiry-time) | 메시지가 제거용으로 표시되고 만료로 인해 엔터티에서 더 이상 검색할 수 없는 UTC 인스턴트입니다. 만료는 **TimeToLive** 속성에 의해 제어되며 이 속성은 EnqueuedTimeUtc+TimeToLive에서 계산됩니다. 이 속성은 읽기 전용입니다.                                                                                                                                                                           |
| [ForcePersistence](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence)                      | [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress) 플래그가 설정된 큐 또는 토픽의 경우 메시지가 승인되기 전에 디스크에 유지되어야 함을 나타내기 위해 이 속성을 설정할 수 있습니다. 이것은 모든 Express 이외 엔터티에 대한 표준 동작입니다.                                                                                                                                                                                                         |
| [Label](/dotnet/api/microsoft.azure.servicebus.message.label)(subject)                       | 이 속성을 사용하여 애플리케이션은 메시지 용도를 전자 메일 제목 줄과 비슷하게 표준화된 방식으로 나타낼 수 있습니다.                                                                                                                                                                                                                                                                                  |
| [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc)                        | 잠금 상태로 검색된 메시지의 경우(보기-잠금 수신 모드, 미리 확인되지 않음) 이 속성은 메시지가 큐/구독에서 잠금 상태를 유지하는 UTC 인스턴스를 반영합니다. 잠금이 만료되면 [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deliverycount)는 증분되며 메시지는 다시 검색할 수 있게 됩니다. 이 속성은 읽기 전용입니다.                                                                                                                         |
| [LockToken](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.locktoken)                             | lock 토큰은 *보기-잠금* 수신 모드에서 브로커가 보유하는 잠금에 대한 참조입니다. 이 토큰은 [연기](message-deferral.md) API를 통해 잠금을 영구적으로 고정하고, 일반 배달 상태 흐름에서 메시지를 제외하는 데 사용할 수 있습니다. 이 속성은 읽기 전용입니다.                                                                                                                                                               |
| [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)(message-id)                | 메시지 식별자는 메시지 및 해당 페이로드를 고유하게 식별하는 애플리케이션 정의 값입니다. 식별자는 자유 형식 문자열이고 애플리케이션 컨텍스트에서 파생된 GUID 또는 식별자를 반영할 수 있습니다. 사용하도록 설정되면 [중복 검색](duplicate-detection.md) 기능은 같은 **MessageId**를 갖는 메시지의 두 번째 및 추가 전송을 식별한 후 제거합니다.                                                                |
| [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)                          | [분할된 엔터티](service-bus-partitioning.md)의 경우 이 값을 설정하면 관련 메시지가 동일한 내부 파티션에 할당되므로 전송 시퀀스 순서가 올바르게 기록됩니다. 파티션은 이 값에 대한 해시 함수에 의해 선택되며 직접 선택할 수 없습니다. 세션 인식 엔터티의 경우 **SessionId** 속성이 이 값을 재정의합니다.                                                                                                       |
| [ReplyTo](/dotnet/api/microsoft.azure.servicebus.message.replyto)(reply-to)                    | 이 선택적인 애플리케이션 정의 값은 메시지의 수신자에 대한 회신 경로를 나타내는 표준 방법입니다. 보낸 사람이 회신을 기대할 경우 해당 값을 회신이 전송될 큐 또는 토픽의 절대 또는 상대 경로로 설정합니다.                                                                                                                                           |
| [ReplyToSessionId](/dotnet/api/microsoft.azure.servicebus.message.replytosessionid)(reply-to-group-id)  | 이 값은 **ReplyTo** 정보를 확대하며 회신 엔터티로 전송될 때 회신에 대해 설정해야 하는 **SessionId**를 지정합니다.                                                                                                                                                                                                                                                                            |
| [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc)               | 연기 후에만 검색할 수 있는 메시지의 경우, 이 속성은 메시지가 논리적으로 큐에 추가되고, 시퀀싱되고, 검색에 사용할 수 있게 되는 UTC 인스턴스를 정의합니다.                                                                                                                                                                                                                 |
| [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber)                        | 시퀀스 번호는 Broker에서 수락하여 저장할 때 메시지에 할당되는 고유의 64비트 정수이며 실제 식별자로 작동합니다. 분할된 엔터티의 경우 최상위 16비트는 파티션 식별자를 나타냅니다. 시퀀스 번호는 일정하게 커지며 간격이 없습니다. 48-64비트 범위가 고갈되면 다시 0에서 시작됩니다. 이 속성은 읽기 전용입니다.                                                                |
| [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)(group-id)                  | 세션 인식 엔터티의 경우 이 애플리케이션 정의 값은 메시지의 세션 정보를 지정합니다. 동일한 세션 식별자를 갖는 메시지는 요약 잠금이 적용되며 정확한 순차적 처리 및 역 멀티플렉싱이 지원됩니다. 세션을 인식하지 않는 엔터티의 경우 이 값이 무시됩니다.                                                                                                                                     |
| [크기](/dotnet/api/microsoft.azure.servicebus.message.size)                                  | 이 속성은 저장소 할당량을 기준으로 하므로 브로커 로그에 포함된 메시지의 저장된 크기를 바이트 수로 반영합니다. 이 속성은 읽기 전용입니다.                                                                                                                                                                                                                                                                                                       |
| [State](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.state)                                 | 로그에 메시지 상태를 나타냅니다. 이 속성은 메시지 검색("보기") 동안 메시지가 "활성" 상태인지와 큐 맨 위에 도달할 때 검색 가능한지, 연기되었는지 또는 예약되기 위해 대기 중인지를 결정하는 데만 적합합니다. 이 속성은 읽기 전용입니다.                                                                                                                                           |
| [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive)                            | 이 값은 브로커가 메시지를 수락 및 저장한 순간부터 메시지가 만료되기까지의 상대적 기간으로, **EnqueueTimeUtc**에 캡처됩니다. 명시적으로 설정되지 않은 경우 예상되는 값은 해당 큐 또는 토픽에 대해 **DefaultTimeToLive**입니다. 메시지 수준 **TimeToLive** 값은 엔터티의 **DefaultTimeToLive** 설정보다 길 수 없습니다. 더 길면 자동으로 조정됩니다. |
| [To](/dotnet/api/microsoft.azure.servicebus.message.to)(to)                               | 이 속성은 라우팅 시나리오에서 나중에 사용하기 위해 예약되어 있으며 현재 broker 자체에서 무시됩니다. 애플리케이션은 메시지의 의도한 논리 대상을 나타내기 위해 규칙 기반 자동 전달 연결 시나리오에서 이 값을 사용할 수 있습니다.                                                                                                                                                                                   |
| [ViaPartitionKey](/dotnet/api/microsoft.azure.servicebus.message.viapartitionkey)                       | 메시지가 트랜잭션 범위에서 전송 큐를 통해 전송될 경우 이 값은 전송 큐 파티션을 선택합니다.                                                                                                                                                                                                                                                                                                                 |

추상 메시지 모델은 메시지가 HTTP(실제로는 항상 HTTPS)를 통해 큐에 게시될 수 있도록 하며 AMQP를 통해 검색할 수 있습니다. 두 경우 모두 메시지는 해당 프로토콜의 컨텍스트에서 정상적으로 나타납니다. 브로커 속성은 필요에 따라 변환되며 사용자 속성은 해당 프로토콜 메시지 모델에서 가장 적절한 위치에 매핑됩니다. HTTP에서 사용자 속성은 HTTP 헤더와 직접 상호 매핑되고, AMQP에서는 **애플리케이션-속성** 맵과 상호 매핑됩니다.

## <a name="message-routing-and-correlation"></a>메시지 라우팅 및 상관 관계

앞서 설명된 브로커 속성의 하위 집합인 [To](/dotnet/api/microsoft.azure.servicebus.message.to), [ReplyTo](/dotnet/api/microsoft.azure.servicebus.message.replyto), [ReplyToSessionId](/dotnet/api/microsoft.azure.servicebus.message.replytosessionid), [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid), [CorrelationId](/dotnet/api/microsoft.azure.servicebus.message.correlationid) 및 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)는 애플리케이션이 특정 대상으로 메시지를 라우팅하도록 지원하는 데 사용됩니다. 이를 이해하기 위해 다음과 같은 몇 가지 패턴을 고려해보세요.

- **간단한 요청/회신**: 게시자가 큐에 메시지를 보내고 메시지 소비자의 회신을 기대합니다. 회신을 받기 위해 게시자는 회신이 배달될 큐를 소유합니다. 해당 큐의 주소는 아웃바운드 메시지의 **ReplyTo** 속성에 표시됩니다. 소비자는 응답할 때 처리된 메시지의 **MessageId**를 회신 메시지의 **CorrelationId** 속성으로 복사하고 해당 메시지를 **ReplyTo** 속성이 나타내는 대상으로 전달합니다. 애플리케이션 컨텍스트에 따라 하나의 메시지가 여러 회신을 발생할 수 있습니다.
- **멀티캐스트 요청/회신**: 이전 패턴의 변형으로, 게시자가 메시지를 토픽으로 보내고 여러 구독자가 메시지를 소비할 자격을 얻게 됩니다. 각 구독자는 앞에서 설명한 방식으로 응답할 수 있습니다. 이 패턴은 검색 또는 롤콜(roll call) 시나리오에 사용되며 응답자는 일반적으로 사용자 속성을 사용하여 또는 페이로드 내에서 자신을 식별합니다. **ReplyTo**가 토픽을 가리킬 경우 이러한 검색 응답 집합이 대상 그룹에 배포될 수 있습니다.
- **멀티플렉싱**: 이 세션 기능은 수신자가 세션을 잠금 상태로 보류하는 동안 일치하는 **SessionId** 값으로 식별된 관련 메시지의 각 세션(또는 그룹)이 특정 수신자에게 라우팅되도록 단일 큐 또는 구독을 통해 관련 메시지의 스트림을 멀티플렉싱할 수 있도록 합니다. [여기](message-sessions.md)에서 세션 세부 정보에 대해 자세히 읽어보세요.
- **멀티플렉싱된 요청/회신**: 이 세션 기능은 멀티플렉싱된 회신을 지원하며 여러 게시자가 회신 큐를 공유하도록 허용합니다. **ReplyToSessionId**를 설정하여 게시자는 소비자에게 해당 값을 회신 메시지의 **SessionId**에 복사하도록 지시할 수 있습니다. 게시 큐 또는 토픽은 세션을 인식할 필요가 없습니다. 메시지가 전송될 때 게시자는 세션 수신자를 조건부로 승인하여 지정된 **SessionId**를 갖는 세션이 큐에서 구체화될 때까지 명확히 기다릴 수 있습니다. 

Service Bus 네임스페이스 내의 라우팅은 자동 전달 체인 및 토픽 구독 규칙을 사용하여 실현될 수 있습니다. 네임스페이스 간의 라우팅은 [Azure Logic Apps를 사용하여](https://azure.microsoft.com/services/logic-apps/) 실현될 수 있습니다. 위 목록에 표시된 대로 **To** 속성은 나중에 사용하기 위해 예약되고 결과적으로는 브로커에 의해 특별하게 사용 가능한 기능으로 해석될 수 있습니다. 라우팅을 구현하려는 애플리케이션은 사용자 속성을 기준으로 구현해야 하며 **To** 속성에 의존하면 안 됩니다. 그렇지만 이렇게 해도 호환성 문제가 야기되지는 않습니다.

## <a name="payload-serialization"></a>페이로드 serialization

전송 중이거나 Service Bus 내에 저장되어 있을 때 페이로드는 항상 불투명한 이진 블록입니다. [ContentType](/dotnet/api/microsoft.azure.servicebus.message.contenttype) 속성을 사용하면 애플리케이션은 속성 값에 대해 제안되는 형식을 IETF RFC2045에 따라 MIME 콘텐츠 형식 설명으로 지정하여(예: `application/json;charset=utf-8`) 페이로드를 설명할 수 있습니다.

Java 또는 .NET Standard 변형과 달리, Service Bus API의 .NET Framework 버전에서는 임의의 .NET 개체를 생성자에 전달하여 **BrokeredMessage** 인스턴스를 만들도록 지원합니다. 

레거시 SBMP 프로토콜을 사용하는 경우 해당 개체는 기본 이진 serializer로 또는 외부에서 제공되는 serializer를 사용해서 serialize됩니다. AMQP 프로토콜을 사용하는 경우 개체는 AMQP 개체로 serialize됩니다. 수신자는 [GetBody<T>()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) 메서드를 사용하여 해당 개체를 검색하고 예상되는 형식을 제공할 수 있습니다. AMQP를 사용하면 개체가 **ArrayList** 및 **IDictionary<string,object>** 개체의 그래프로 직렬화되고 AMQP 클라이언트가 이것을 디코딩할 수 있습니다. 

이 숨겨진 serialization 기능이 편리하긴 하지만, 애플리케이션은 개체 serialization을 명시적으로 제어하고, 개체 그래프를 스트림으로 전환한 다음, 메시지에 포함해야 하며, 수신자 쪽에서는 이러한 과정을 반대로 진행해야 합니다. 이로 인해 상호 운용 가능한 결과가 생성됩니다. AMQP는 강력한 이진 인코딩 모델을 사용하지만 AMQP 메시징 에코 시스템에 연결되며 HTTP 클라이언트는 이러한 페이로드를 디코딩하는 데 어려울 수 있다는 점에 유의해야 합니다. 

일반적으로 구조화된 데이터에 대한 페이로드 형식으로 JSON 및 Apache Avro가 권장됩니다.

.NET Standard 및 Java API 변형은 바이트 배열만 허용합니다. 즉, 애플리케이션은 개체 serialization 제어를 처리해야 합니다. 

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
