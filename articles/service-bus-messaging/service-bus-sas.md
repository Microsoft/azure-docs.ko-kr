---
title: 공유 액세스 서명을 사용한 Azure Service Bus 액세스 제어 | Microsoft Docs
description: 공유 액세스 서명을 사용한 Azure Service Bus 액세스 제어 개요, Azure Service Bus를 사용한 SAS 권한 부여 상세 정보
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 8f5c1755462d2bbd28dd7f8db427cda141817588
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472238"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>공유 액세스 서명을 사용한 Service Bus 액세스 제어

*공유 액세스 서명*(SAS)은 Service Bus 메시징의 기본 보안 메커니즘입니다. 이 문서에서는 SAS, 그 작동 방법 및 플랫폼과 상관 없는 방식으로 사용하는 방법에 대해 설명합니다.

SAS는 권한 부여 규칙에 따라 Service Bus에 대한 액세스를 보호합니다. 네임스페이스 또는 메시징 엔터티(릴레이, 큐 또는 항목)에서 구성됩니다. 권한 부여 규칙에는 특정 권한과 연결된 이름이 있으며 암호화 키 쌍을 전달합니다. Service Bus SDK를 통해 또는 사용자 고유의 코드에서 규칙의 이름 및 키를 사용하여 SAS 토큰을 생성합니다. 그런 다음, 클라이언트는 토큰을 Service Bus에 전달하여 요청된 작업에 대한 권한 부여를 증명할 수 있습니다.

## <a name="overview-of-sas"></a>SAS 개요

공유 액세스 서명은 간단한 토큰을 사용하는 클레임 기반 권한 부여 메커니즘입니다. SAS를 사용하여 키는 연결 중에 전달되지 않습니다. 키는 서비스에 의해 나중에 확인될 수 있는 정보를 암호화하여 서명하는 데 사용됩니다. SAS는 클라이언트가 권한 부여 규칙 이름 및 일치 키를 즉시 소유하는 사용자 이름 및 암호 구성표와 유사하게 사용될 수 있습니다. SAS는 또한 클라이언트가 서명 키의 소유 없이 보안 토큰 서비스에서 시간 제한 및 서명된 액세스 토큰을 받는 페더레이션된 보안 모델과 유사하게 사용될 수도 있습니다.

Service Bus에서 SAS 인증은 연결된 액세스 권한 및 기본 및 보조 암호화 키 쌍이 있는 [공유 액세스 권한 부여 규칙](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)으로 구성됩니다. 키는 Base64 표현에서 256비트 값입니다. Service Bus [릴레이](../service-bus-relay/relay-what-is-it.md), [큐](service-bus-messaging-overview.md#queues) 및 [항목](service-bus-messaging-overview.md#topics)의 네임스페이스 수준에서 규칙을 구성할 수 있습니다.

[공유 액세스 서명](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) 토큰은 선택한 권한 부여 규칙의 이름, 액세스해야 하는 리소스의 URI, 만료 인스턴트 및 선택한 권한 부여 규칙의 기본 또는 보조 암호화 키를 사용하여 이러한 필드를 통해 계산된 HMAC-SHA256 암호화 서명을 포함합니다.

## <a name="shared-access-authorization-policies"></a>공유 액세스 권한 부여 정책

각 Service Bus 네임스페이스와 각 Service Bus 엔터티에는 규칙으로 이루어진 공유 액세스 권한 부여 정책이 있습니다. 네임스페이스 수준에서 정책은 개별 정책 구성에 관계 없이 네임스페이스 내부의 모든 엔터티에 적용됩니다.

각각의 권한 부여 정책 규칙의 경우 **이름**, **범위** 및 **권한** 등, 3가지 정보를 결정합니다. **이름** 은 해당 범위 내에서 고유한 이름입니다. 범위는 아주 쉬운데, 해당 리소스의 URI를 말합니다. Service Bus 네임스페이스의 경우, 범위는 `https://<yournamespace>.servicebus.windows.net/` 등과 같이 정규화된 도메인 이름(FQDN)입니다.

정책 규칙에 의해 부여되는 권한은 다음의 조합일 수 있습니다.

* 'Send' - 엔터티에 메시지를 보낼 권한을 부여합니다.
* 'Listen' - 수신 대기(릴레이) 또는 수신(큐, 구독) 및 모든 관련된 메시지 처리에 대한 권한을 부여합니다.
* 'Manage' - 엔터티 만들기 및 삭제를 포함하여 네임스페이스의 토폴로지를 관리하는 권한을 부여합니다.

'Manage' 권한은 'Send' 및 'Receive' 권한을 포함합니다.

네임스페이스 또는 엔터티 정책은 각각 기본 권한 및 Send 및 Listen의 조합을 다루는 세 개의 규칙 집합을 제공하여 최대 12개의 공유 액세스 권한 부여 규칙을 포함할 수 있습니다. 이 제한은 SAS 정책 저장소가 사용자 또는 서비스 계정 저장소에 적합하지 않다는 것을 강조합니다. 애플리케이션에서 사용자 또는 서비스 ID에 따라 Service Bus에 대한 액세스 권한을 부여해야 하는 경우 인증 및 액세스 검사 후 SAS 토큰을 발급하는 보안 토큰 서비스를 구현해야 합니다.

권한 부여 규칙은 *기본 키*와 *보조 키*에 할당됩니다. 이들은 강력한 암호화 키입니다. 잃어 버리거나 누출되지 않도록 하세요. 항상 [Azure Portal][Azure portal]에서 사용할 수 있습니다. 생성된 키 중 하나를 사용할 수 있으며 언제든지 다시 생성할 수 있습니다. 정책에서 키를 다시 생성하거나 변경하는 경우 해당 키에 기반한 이전에 발급된 모든 토큰은 즉시 무효화됩니다. 그러나 이러한 토큰에 따라 생성된 진행 중인 연결은 토큰이 만료될 때까지 작업을 계속합니다.

Service Bus 네임스페이스를 만들 때 **RootManageSharedAccessKey**라는 정책 규칙이 네임스페이스에 대해 자동으로 만들어집니다. 이 정책은 전체 네임스페이스에 대한 Manage 사용 권한을 갖습니다. 이 규칙을 관리 **루트** 계정과 같이 처리하고 애플리케이션에서 사용하지 않는 것이 좋습니다. Powershell 또는 Azure CLI를 통해 포털의 해당 네임스페이스에 대한 **구성** 탭에서 추가 정책 규칙을 만들 수 있습니다.

## <a name="configuration-for-shared-access-signature-authentication"></a>공유 액세스 서명 인증을 위한 구성

Service Bus 네임스페이스, 큐 또는 항목에 대한 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 규칙을 구성할 수 있습니다. Service Bus 구독에서 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 의 구성은 현재 지원되지 않지만 네임스페이스 또는 항목에 구성된 규칙을 사용하여 구독에 액세스할 수 있습니다. 이 절차를 설명하는 작업 샘플은 [Service Bus 구독으로 공유 액세스 서명(SAS) 인증 사용](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) 샘플을 참조하세요.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

이 그림에서는 *manageRuleNS*, *sendRuleNS* 및 *listenRuleNS* 권한 부여 규칙이 큐 Q1 및 토픽 topic T1에 적용되는 반면, *listenRuleQ* 및 *sendRuleQ*는 큐 Q1에만 적용되고 *sendRuleT*는 토픽 T1에만 적용됩니다.

## <a name="generate-a-shared-access-signature-token"></a>공유 액세스 서명 토큰 생성

권한 부여 규칙 이름에 대한 액세스 권한이 있는 모든 클라이언트 및 해당 서명 키 중 하나는 SAS 토큰을 생성할 수 있습니다. 토큰은 다음 형식으로 문자열을 선별하여 생성됩니다.

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** - 토큰 만료 인스턴트입니다. 토큰이 만료될 때 1970년 1월 1일(UNIX Epoch)의 Epoch `00:00:00 UTC` 이후의 초를 반영하는 정수
* **`skn`** - 권한 부여 규칙의 이름입니다.
* **`sr`** - 액세스되는 리소스의 URI입니다.
* **`sig`** - 서명입니다.

`signature-string`은 리소스 URI를 통해 계산된 SHA-256 해시(이전 섹션에 설명된 대로 **범위**) 및 CRLF로 구분된 토큰 만료 인스턴스의 문자열 표현입니다.

해시 계산은 다음 의사 코드와 유사하며 256비트/32바이트 해시 값을 반환합니다.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

토큰은 수신자가 동일한 매개 변수를 사용하여 해시를 다시 계산할 수 있도록 발급자가 유효한 서명 키를 소유하는지 확인하여 해시되지 않은 값을 포함합니다.

리소스 URI은 액세스를 하려는 Service Bus 리소스의 전체 URI입니다. 예를 들면 `http://<namespace>.servicebus.windows.net/<entityPath>` 또는 `sb://<namespace>.servicebus.windows.net/<entityPath>`입니다. 즉, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`과 같습니다. URI는 [%로 인코딩](https://msdn.microsoft.com/library/4fkewx0t.aspx)되어야 합니다.

이 URI로 또는 부모 계층 중 하나에 의해 지정된 엔터티에 서명에 사용된 공유 액세스 권한 부여 규칙을 구성해야 합니다. 예를 들어 이전 예에서 `http://contoso.servicebus.windows.net/contosoTopics/T1` 또는 `http://contoso.servicebus.windows.net`입니다.

SAS 토큰은 `signature-string`에서 사용된 `<resourceURI>`를 접두사로 추가하는 모든 리소스에 유효합니다.

## <a name="regenerating-keys"></a>키 다시 생성

[SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 개체에서 정기적으로 사용되는 키를 다시 생성하는 것이 좋습니다. 기본 및 보조 키 슬롯은 키를 점진적으로 회전할 수 있도록 존재합니다. 애플리케이션에서 일반적으로 기본 키를 사용하는 경우 기본 키를 보조 키 슬롯에 복사한 다음, 기본 키를 다시 생성할 수 있습니다. 그런 다음, 보조 슬롯의 이전 기본 키를 사용하여 계속해서 액세스하는 클라이언트 애플리케이션으로 새 기본 키 값을 구성할 수 있습니다. 모든 클라이언트를 업데이트한 후에 보조 키를 다시 생성하여 마지막으로 이전 기본 키를 사용 중지할 수 있습니다.

키가 손상되었고 키를 해제해야 한다는 것을 알거나 의심이 가는 경우 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)의 [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 및 [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 모두를 새 키로 바꿔 다시 생성할 수 있습니다. 이 절차는 이전 키로 서명된 모든 토큰을 무효화합니다.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Service Bus를 사용한 공유 액세스 서명 인증

다음과 같이 설명된 시나리오는 권한 부여 규칙의 구성, SAS 토큰의 생성 및 클라이언트 권한 부여를 포함합니다.

구성을 설명하고 SAS 권한 부여를 사용하는 Service Bus 애플리케이션의 작업 샘플 전체는 [Service Bus를 사용하여 공유 액세스 서명 인증](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8)을 참조하세요. 네임스페이스 또는 토픽에 구성된 SAS 권한 부여 규칙을 사용하여 Service Bus 구독을 보호하는 방법을 설명하는 관련 샘플은 [Service Bus 구독으로 SAS(공유 액세스 서명) 인증 사용](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)에서 확인할 수 있습니다.

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>엔터티에 대한 공유 액세스 권한 부여 규칙 액세스

Service Bus .NET Framework 라이브러리를 사용하여 해당하는 [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) 또는 [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)에서 [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) 컬렉션을 통해 Service Bus 큐나 토픽에 구성된 [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 개체에 액세스할 수 있습니다.

다음 코드는 큐에 대한 권한 부여 규칙을 추가하는 방법을 보여줍니다.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>공유 액세스 서명 권한 부여 사용

Service Bus.NET 라이브러리로 Azure.NET SDK를 사용하는 애플리케이션은 [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) 클래스를 통해 SAS 권한 부여를 사용할 수 있습니다. 다음 코드에서는 토큰 공급자를 사용하여 Service Bus 큐에 메시지를 보냅니다. 여기에 표시된 사용에 대한 대안으로 이전에 발급된 토큰을 토큰 공급자 팩터리 메서드에 전달할 수도 있습니다.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

다른 클라이언트에 전달할 토큰을 발급하는 데 토큰 공급자를 직접 사용할 수도 있습니다.

연결 문자열은 규칙 이름(*SharedAccessKeyName*) 및 규칙 키(*SharedAccessKey*) 또는 이전에 발급된 토큰(*SharedAccessSignature*)을 포함할 수 있습니다. 해당 항목이 생성자 또는 연결 문자열을 수락하는 팩터리 메서드에 전달된 연결 문자열에 있는 경우 SAS 토큰 공급자가 자동으로 만들어지고 채워집니다.

Service Bus 릴레이로 SAS 권한 부여를 사용하면 Service Bus 네임 스페이스에 구성된 SAS 키를 사용할 수 있습니다. 네임스페이스([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager)와 [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) 개체에 명시적으로 릴레이를 만들면 해당 릴레이 대한 SAS 규칙을 설정할 수 있습니다. Service Bus 구독으로 SAS 권한 부여를 사용하려면 Service Bus 네임 스페이스 또는 항목에 구성된 SAS 키를 사용할 수 있습니다.

## <a name="use-the-shared-access-signature-at-http-level"></a>공유 액세스 서명 사용(HTTP 수준에서)

Service Bus의 모든 엔터티에 대해 공유 액세스 서명을 만드는 방법을 알았으므로 HTTP POST를 수행할 준비가 되었습니다.

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

이 방식은 모든 항목에 대해 작동한다는 것을 기억하세요. 큐, 토픽, 구독에 대해 SAS를 만들 수 있습니다.

발신자 또는 클라이언트에게 SAS 토큰을 제공하면 직접 키를 가질 수 없으며 키를 얻기 위해 해시를 되돌릴 수도 없습니다. 예를 들어 사용자가 액세스할 수 있는 항목 및 액세스 기간을 제어할 수 있습니다. 정책에서 기본 키를 다시 생성하거나 변경할 경우, 만든 공유 액세스 서명이 무효화되므로 주의해야 합니다.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>공유 액세스 서명(AMQP 수준) 사용 

이전 섹션에서는 HTTP POST 요청과 함께 SAS 토큰을 사용하여 데이터를 Service Bus에 보내는 방법을 살펴봤습니다. 아시다시피, Service Bus는 많은 시나리오에서 성능상의 이유로 사용하는 기본 설정 프로토콜인 AMQP(고급 메시지 큐 프로토콜)를 사용하여 액세스할 수 있습니다. AMQP와 함께 SAS 토큰을 사용하는 방법은 2013년 이후 초안 상태이지만 현재 Azure에서 충분한 지원을 받고 있는 문서 [AMQP 클레임 기반 보안 버전 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) 에 설명되어 있습니다.

Service Bus에 데이터의 전송을 시작하기 전에 게시자는 AMQP 메시지 안에 있는 SAS 토큰을 **$cbs**(모든 SAS 토큰을 얻고 유효성을 검사하기 위해 서비스에서 사용하는 "특별" 큐)라는 이름의 정의된 AMQP 노드에 전송해야 합니다. 게시자는 AMQP 메시지 내부에 있는 **ReplyTo** 필드를 지정해야 합니다. 이는 서비스가 토큰 유효성 검사 결과와 함께 게시자에게 응답하는 노드입니다(게시자와 서비스 간의 간단한 요청/응답 패턴). 이 회신 노드는 "즉시" 생성되며 AMQP 1.0 사양에 설명된 것처럼 “원격 노드 동적 생성”에 대해 얘기합니다. SAS 토큰이 유효한지 확인한 후 게시자는 이제 데이터를 서비스에 보내기 시작할 수 있습니다.

다음 단계를 사용 하 여 AMQP 프로토콜을 통해 SAS 토큰을 전송 하는 방법을 표시 합니다 [AMQP.NET Lite](https://github.com/Azure/amqpnetlite) 라이브러리입니다. 이 C에서 공식 Service Bus에 대 한 SDK (예를 들어 WinRT,.NET Compact Framework,.NET Micro Framework 및 Mono) 개발을 사용할 수 없는 경우에 유용\#합니다. 물론 이 라이브러리는 클레임 기반 보안이 HTTP 수준에서 작동하는 방식을 볼 때처럼 AMQP 수준에서 작동하는 방식을 이해하는 데 유용합니다("권한 부여" 헤더 내에서 전송되는 HTTP POST 요청 및 SAS 토큰과 함께). AMQP에 대 한 깊은 지식이 필요 하지 않으면, 수 없어도.NET Framework 응용 프로그램과 함께 공식 Service Bus SDK를 사용할 수 있습니다.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

위의 `PutCbsToken()` 메서드는 서비스에 대한 TCP 연결 및 전송할 SAS 토큰인 *sasToken* 매개 변수를 나타내는 *연결*([AMQP .NET Lite 라이브러리](https://github.com/Azure/amqpnetlite)에서 제공하는 대로 AMQP 연결 클래스 인스턴스)을 수신합니다.

> [!NOTE]
> 연결이 **ANONYMOUS로 설정된 SASL 인증 메커니즘**(SAS 토큰을 보낼 필요가 없을 때 사용하는 사용자 이름 및 암호를 가진 기본 PLAIN이 아님)으로 생성된다는 사실이 중요합니다.
>
>

그런 다음 게시자는 SAS 토큰을 보내고 서비스로부터 회신(토큰 유효성 검사 결과)을 받기 위한 2개의 AMQP 링크를 만듭니다.

AMQP 메시지는 간단한 메시지보다 정보가 많고 속성이 많습니다. SAS 토큰은 해당 생성자를 사용하여 메시지의 본문으로 배치됩니다. **"ReplyTo"** 속성은 수신기 링크에 대한 유효성 검사 결과를 받기 위한 노드 이름으로 설정됩니다(원하는 대로 이름을 변경할 수 있으며 서비스에서 동적으로 생성함). 마지막 세 애플리케이션/사용자 지정 속성은 서비스에서 실행하는 작업의 종류를 나타내는 데 사용합니다. CBS 초안 사양에서 설명한 것처럼 이들은 **토큰의 형식**(이 경우 `servicebus.windows.net:sastoken`)인 **작업 이름**("put-token")이 되고 토큰이 적용되는 **청중의 "이름"** 이어야 합니다(전체 엔터티).

보낸 사람 링크에서 SAS 토큰을 보낸 후 게시자는 수신자 링크에서 회신을 읽어야 합니다. 회신은 HTTP 상태 코드와 동일한 값을 포함할 수 있는 **"status-code"** 라는 이름의 애플리케이션 속성을 가진 간단한 AMQP 메시지입니다.

## <a name="rights-required-for-service-bus-operations"></a>Service Bus 작업에 필요한 권한

다음 테이블에서는 Service Bus 리소스의 다양한 작업에 필요한 액세스 권한을 보여줍니다.

| 작업(Operation) | 필요한 클레임 | 클레임 범위 |
| --- | --- | --- |
| **네임스페이스** | | |
| 네임스페이스에서 권한 부여 규칙 구성 |관리 |네임스페이스 주소 |
| **서비스 레지스트리** | | |
| 개인 정책 열거 |관리 |네임스페이스 주소 |
| 네임스페이스에서 수신 시작 |수신 대기 |네임스페이스 주소 |
| 네임스페이스에서 수신기로 메시지 보내기 |보내기 |네임스페이스 주소 |
| **큐** | | |
| 큐 만들기 |관리 |네임스페이스 주소 |
| 큐 삭제 |관리 |유효한 큐 주소 |
| 큐 열거 |관리 |/$Resources/Queues |
| 큐 설명 가져오기 |관리 |유효한 큐 주소 |
| 큐에서 권한 부여 규칙 구성 |관리 |유효한 큐 주소 |
| 큐로 보내기 |보내기 |유효한 큐 주소 |
| 큐에서 메시지 받기 |수신 대기 |유효한 큐 주소 |
| 메시지 보기-잠금 모드에서 메시지를 받은 후에 중단 또는 완료 |수신 대기 |유효한 큐 주소 |
| 나중에 검색에 대한 메시지 연기 |수신 대기 |유효한 큐 주소 |
| 메시지 효력 상실 |수신 대기 |유효한 큐 주소 |
| 메시지 큐 세션을 사용하여 연결된 상태 가져오기 |수신 대기 |유효한 큐 주소 |
| 메시지 큐 세션을 사용하여 연결된 상태 설정 |수신 대기 |유효한 큐 주소 |
| 나중에 배달할 메시지를 예약합니다(예: [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_)). |수신 대기 | 유효한 큐 주소
| **항목** | | |
| 토픽 만들기 |관리 |네임스페이스 주소 |
| 항목 삭제 |관리 |유효한 항목 주소 |
| 항목 열거 |관리 |/$Resources/Topics |
| 항목 설명 가져오기 |관리 |유효한 항목 주소 |
| 항목에서 권한 부여 규칙 구성 |관리 |유효한 항목 주소 |
| 항목으로 보내기 |보내기 |유효한 항목 주소 |
| **구독** | | |
| 구독 만들기 |관리 |네임스페이스 주소 |
| 구독 삭제 |관리 |../myTopic/Subscriptions/mySubscription |
| 구독 열거 |관리 |../myTopic/Subscriptions |
| 구독 설명 가져오기 |관리 |../myTopic/Subscriptions/mySubscription |
| 메시지 보기-잠금 모드에서 메시지를 받은 후에 중단 또는 완료 |수신 대기 |../myTopic/Subscriptions/mySubscription |
| 나중에 검색에 대한 메시지 연기 |수신 대기 |../myTopic/Subscriptions/mySubscription |
| 메시지 효력 상실 |수신 대기 |../myTopic/Subscriptions/mySubscription |
| 항목 세션을 사용하여 연결된 상태 가져오기 |수신 대기 |../myTopic/Subscriptions/mySubscription |
| 항목 세션을 사용하여 연결된 상태 설정 |수신 대기 |../myTopic/Subscriptions/mySubscription |
| **규칙** | | |
| 규칙 만들기 |관리 |../myTopic/Subscriptions/mySubscription |
| 규칙 삭제 |관리 |../myTopic/Subscriptions/mySubscription |
| 규칙 열거 |관리 또는 수신 |../myTopic/Subscriptions/mySubscription/Rules

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐를 사용하는 방법](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
