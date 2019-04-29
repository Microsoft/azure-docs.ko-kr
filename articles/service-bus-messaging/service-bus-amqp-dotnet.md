---
title: .NET 및 AMQP 1.0을 사용한 Azure Service Bus | Microsoft Docs
description: AMQP를 사용하여 .NET에서 Azure Service Bus 사용
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 82301a17bb461b6d8733d5f046fe791ffbcf3ecb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749260"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>AMQP 1.0을 사용하여 .NET에서 Service Bus 사용

AMQP 1.0 지원은 Service Bus 패키지 버전 2.1 이상에서 이용할 수 있습니다. [NuGet][NuGet]에서 Service Bus 비트를 다운로드하여 최신 버전이 있는지 확인할 수 있습니다.

## <a name="configure-net-applications-to-use-amqp-10"></a>AMQP 1.0을 사용하여 .NET 애플리케이션 구성

기본적으로 Service Bus .NET 클라이언트 라이브러리는 전용 SOAP 기반 프로토콜을 사용하여 Service Bus 서비스와 통신합니다. 기본 프로토콜 대신 AMQP 1.0을 사용하려면 다음 섹션에서 설명한 대로 Service Bus 연결 문자열에서 이를 명시적으로 구성해야 합니다. AMQP 1.0을 사용하는 경우 이러한 변경 사항 외에는 애플리케이션 코드가 변경되지 않습니다.

현재 릴리스에는 AMQP 사용 시 지원되지 않는 몇 가지 API 기능이 있습니다. 이러한 지원되지 않는 기능은[동작의 차이](#behavioral-differences) 섹션에 나열되어 있습니다. AMQP를 사용하는 경우 몇 가지 고급 구성 설정도 다른 의미를 가집니다.

### <a name="configuration-using-appconfig"></a>App.config를 사용한 구성

애플리케이션에서는 App.config 구성 파일을 사용하여 설정을 저장하는 것이 바람직합니다. Service Bus 애플리케이션의 경우 App.config를 사용하여 Service Bus 연결 문자열을 저장할 수 있습니다. 샘플 App.config 파일은 다음과 같습니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

`Microsoft.ServiceBus.ConnectionString` 설정의 값은 Service Bus에 대한 연결을 구성하는 데 사용되는 Service Bus 연결 문자열입니다. 형식은 다음과 같습니다.

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

여기서 `namespace` 및 `SAS key`는 Service Bus 네임스페이스를 만들 때 [Azure 포털][Azure portal]에서 가져옵니다. 자세한 내용은 [Azure 포털을 사용하여 Service Bus 네임스페이스 만들기][Create a Service Bus namespace using the Azure portal]를 참조하세요.

AMQP를 사용하는 경우 `;TransportType=Amqp`을(를) 사용하여 연결 문자열을 추가합니다. 이 표기는 클라이언트 라이브러리가 AMQP 1.0을 사용하여 Service Bus에 연결하도록 지시합니다.

## <a name="message-serialization"></a>메시지 직렬화

기본 프로토콜을 사용하는 경우 .NET 클라이언트 라이브러리의 기본 직렬화 동작은 [DataContractSerializer][DataContractSerializer] 형식을 사용하여 클라이언트 라이브러리와 Service Bus 서비스 간의 전송에 대한 [BrokeredMessage][BrokeredMessage] 인스턴스를 직렬화하는 것입니다. AMQP 전송 모드를 사용하는 경우 클라이언트 라이브러리는 [broker 저장 메시지][BrokeredMessage]를 AMQP 메시지로 직렬화하는 데 AMQP 형식 시스템을 사용합니다. 이 직렬화를 통해 Service Bus에 액세스하기 위해 JMS API를 사용하는 Java 애플리케이션과 같은 다른 플랫폼에서 잠재적으로 실행하는 애플리케이션 수신을 통해 메시지를 수신하고 해석합니다.

[BrokeredMessage][BrokeredMessage] 인스턴스를 생성하는 경우 .NET 개체를 매개 변수로 생성자에게 제공하여 메시지의 본문으로 처리할 수 있습니다. AMQP 기본 형식으로 매핑할 수 있는 개체의 경우 본문은 AMQP 데이터 유형으로 직렬화됩니다. 개체를 AMQP 기본 형식으로 직접 매핑할 수 없는 경우 즉, 애플리케이션에서 정의된 사용자 지정 형식이며 개체는 [DataContractSerializer][DataContractSerializer]를 사용하여 직렬화되고, 직렬화된 바이트는 AMQP 데이터 메시지로 전송됩니다.

비 .NET 클라이언트와의 상호 운용성을 용이하게 하려면 메시지의 본문에 대한 AMQP 형식으로 직접 직렬화할 수 있는 .NET 형식만 사용합니다. 다음 표는 이러한 형식 및 AMQP 형식 시스템에 대한 해당 매핑을 자세히 설명합니다.

| .NET 본문 개체 형식 | 매핑된 AMQP 형식 | AMQP 본문 섹션 형식 |
| --- | --- | --- |
| bool |부울 |AMQP 값 |
| byte |ubyte |AMQP 값 |
| ushort |ushort |AMQP 값 |
| uint |uint |AMQP 값 |
| ulong |ulong |AMQP 값 |
| sbyte |byte |AMQP 값 |
| short |short |AMQP 값 |
| int |int |AMQP 값 |
| long |long |AMQP 값 |
| float |float |AMQP 값 |
| double |double |AMQP 값 |
| decimal |decimal128 |AMQP 값 |
| char |char |AMQP 값 |
| DateTime |timestamp |AMQP 값 |
| Guid |uuid |AMQP 값 |
| byte[] |binary |AMQP 값 |
| 문자열 |문자열 |AMQP 값 |
| System.Collections.IList |list |AMQP 값: 컬렉션에 포함된 항목은 이 테이블에서 정의된 것만 가능합니다. |
| System.Array |array |AMQP 값: 컬렉션에 포함된 항목은 이 테이블에서 정의된 것만 가능합니다. |
| System.Collections.IDictionary |map |AMQP 값: 컬렉션에 포함된 항목은 이 테이블에서 정의된 것만 가능합니다.참고: 문자열만 지원됩니다. |
| Uri |설명된 문자열(아래 표 참조) |AMQP 값 |
| DateTimeOffset |설명된 long(아래 표 참조) |AMQP 값 |
| TimeSpan |설명된 long(아래 참조) |AMQP 값 |
| Stream |binary |AMQP 데이터(여러 개가 있을 수 있음). 데이터 섹션에는 스트림 개체에서 읽은 원시 바이트가 포함되어 있습니다. |
| 다른 개체 |binary |AMQP 데이터(여러 개가 있을 수 있음). 애플리케이션에서 제공된 DataContractSerializer 또는 serializer를 사용하는 개체의 직렬화된 이진을 포함합니다. |

| .NET 형식 | 매핑된 AMQP 설명된 형식 | 메모 |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>동작의 차이

AMQP를 사용하는 경우 기본 프로토콜에 비해 Service Bus .NET API의 동작에 몇 가지 작은 차이점이 있습니다.

* [OperationTimeout][OperationTimeout] 속성은 무시됩니다.
* `MessageReceiver.Receive(TimeSpan.Zero)`은(는) `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`(으)로 구현됩니다.
* 잠금 토큰으로 메시지를 완료하는 작업은 처음에 메시지를 받은 메시지 수신기에서만 수행할 수 있습니다.

## <a name="control-amqp-protocol-settings"></a>AMQP 프로토콜 설정 제어

[.NET API](/dotnet/api/)는 AMQP 프로토콜의 동작을 제어하기 위해 여러 설정을 노출합니다.

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: 링크에 적용되는 초기 크레딧을 제어합니다. 기본값은 0입니다.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: 연결 열기 시간에 협상 동안 제공되는 최대 AMQP 프레임 크기를 제어합니다. 기본값은 65,536바이트입니다.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: 전송을 일괄 처리할 수 있는 경우 이 값에 따라 배치 전송의 최대 지연이 결정됩니다. 기본적으로 발신자/수신자를 상속합니다. 개별 발신자/수신자는 기본값 20 밀리초를 재정의할 수 있습니다.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: AMQP 연결이 SSL 연결을 통해 설정되는지 여부를 제어합니다. 기본값은 **true**입니다.

## <a name="next-steps"></a>다음 단계

자세히 알아볼 준비가 되셨습니까? 다음 링크를 방문하세요.

* [Service Bus AMQP 개요]
* [AMQP 1.0 프로토콜 가이드]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Service Bus AMQP 개요]: service-bus-amqp-overview.md
[AMQP 1.0 프로토콜 가이드]: service-bus-amqp-protocol-guide.md

