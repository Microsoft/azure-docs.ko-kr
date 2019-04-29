---
title: 인증 및 보안 모델 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs의 인증 및 보안 모델을 설명합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 19b347423c28b4c615f90f325ead462b9d3e8e9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822595"
---
# <a name="azure-event-hubs---authentication-and-security-model"></a>Azure Event Hubs - 인증 및 보안 모델

Azure Event Hubs 보안 모델은 다음 요구 사항을 만족합니다.

* 유효한 자격 증명을 제공하는 클라이언트만 이벤트 허브로 데이터를 보낼 수 있습니다.
* 클라이언트는 다른 클라이언트를 가장할 수 없습니다.
* 악성 클라이언트는 이벤트 허브로 데이터를 보내지 못하도록 차단할 수 있습니다.

## <a name="client-authentication"></a>클라이언트 인증

Event Hubs 보안 모델은 [공유 액세스 서명(SAS)](../service-bus-messaging/service-bus-sas.md) 토큰 및 *이벤트 게시자*의 조합을 기반으로 합니다. 이벤트 게시자는 이벤트 허브에 대한 가상 엔드포인트를 정의합니다. 게시자는 이벤트 허브에 메시지를 보내는 데만 사용할 수 있습니다. 게시자에서 메시지를 받을 수 없습니다.

일반적으로 이벤트 허브에서는 클라이언트당 하나의 게시자를 사용합니다. 이벤트 허브의 게시자에게 전달되는 모든 메시지는 해당 이벤트 허브 내의 큐에 삽입됩니다. 게시자는 세분화된 액세스 제어 및 제한을 사용하도록 설정합니다.

각 Event Hubs 클라이언트는 클라이언트에 업로드되는 고유 토큰을 할당받습니다. 각 고유 토큰이 고유한 다른 게시자에 대한 액세스가 허용되도록 토큰이 생성됩니다. 토큰을 소유하는 클라이언트는 하나의 게시자에게만 보낼 수 있으며 다른 게시자에게는 보낼 수 없습니다. 여러 클라이언트가 동일한 토큰을 공유하는 경우 각 클라이언트가 게시자를 공유합니다.

추천되지는 않지만 이벤트 허브에 대한 직접 액세스 권한을 부여하는 토큰을 가진 디바이스를 장착할 수 있습니다. 이 토큰을 보유하는 모든 디바이스는 해당 이벤트 허브에 직접 메시지를 보낼 수 있습니다. 이러한 디바이스는 제한의 대상이 되지 않습니다. 또한 디바이스에서 해당 이벤트 허브로 보내는 것을 차단할 수 없습니다.

모든 토큰은 SAS 키로 서명됩니다. 일반적으로 모든 토큰은 동일한 키로 서명됩니다. 클라이언트는 키를 인식하지 않기 때문에 다른 클라이언트가 토큰을 제조하지 못합니다.

### <a name="create-the-sas-key"></a>SAS 키 만들기

Event Hubs 네임스페이스를 만들면, 서비스는 자동으로 **RootManageSharedAccessKey**라는 256비트 SAS 키를 생성합니다. 이 규칙에는 네임스페이스에 대해 전송, 수신 대기 및 관리 권한을 부여하는 연결된 기본 및 보조 키 쌍이 있습니다. 추가 키를 만들 수도 있습니다. 특정 이벤트 허브에 대한 전송 권한을 부여하는 키를 생성하는 것이 좋습니다. 이 항목의 나머지 부분에서는 이 키의 이름을 **EventHubSendKey**로 지정했다고 가정합니다.

다음 예제에서는 이벤트 허브를 만들 때 전송 전용 키를 만듭니다.

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create event hub with a SAS rule that enables sending to that event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>토큰 생성

SAS 키를 사용하여 토큰을 생성할 수 있습니다. 클라이언트당 하나의 토큰만 생성해야 합니다. 다음 메서드를 사용하여 토큰을 생성할 수 있습니다. 모든 토큰은 **EventHubSendKey** 키를 사용하여 생성됩니다. 각 토큰에는 고유한 URI가 할당됩니다. 'Resource' 매개 변수 (이 경우 이벤트 허브) 서비스의 URI 끝점에 해당합니다.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

이 메서드를 호출할 때 URI는 `https://<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`로 지정해야 합니다. 모든 토큰에 대해 `PUBLISHER_NAME`는 제외하고 URI는 동일하며, 각 토큰에 대해서는 달라야 합니다. `PUBLISHER_NAME`은 해당 토큰을 수신하는 클라이언트의 ID를 나타내는 것이 좋습니다.

이 메서드는 다음 구조를 사용하여 토큰을 생성합니다.

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

토큰 만료 시간(초)은 1970년 1월 1일에서부터 지정됩니다. 다음은 토큰 예제입니다.

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

일반적으로 토큰의 수명은 클라이언트와 유사하거나 더 깁니다. 클라이언트에 새 토큰을 가져오는 기능이 있는 경우 짧은 수명의 토큰을 사용할 수 있습니다.

### <a name="sending-data"></a>데이터 전송

토큰이 생성된 후에 각 클라이언트는 자체의 고유한 토큰과 함께 프로비전됩니다.

클라이언트가 이벤트 허브로 데이터를 전송할 경우 토큰을 사용해서 보내기 요청에 태그를 지정합니다. 공격자가 도청 및 토큰 가로채기를 하지 못하도록 방지하려면 클라이언트와 이벤트 허브 간의 통신은 암호화된 채널을 통해 이루어져야 합니다.

### <a name="blacklisting-clients"></a>클라이언트 차단 목록

토큰이 공격자에 의해 도난당한 경우 공격자가 토큰을 도난당한 클라이언트로 가장할 수 있습니다. 클라이언트를 차단할 경우 해당 클라이언트는 다른 게시자를 사용하는 새 토큰을 수신할 때까지 사용할 수 없게 됩니다.

## <a name="authentication-of-back-end-applications"></a>백 엔드 애플리케이션의 인증

Event Hubs는 이 이벤트 허브 클라이언트에서 생성된 데이터를 사용하는 백 엔드 애플리케이션을 인증하기 위해 Service Bus 항목에 사용된 모델과 유사한 보안 모델을 사용합니다. Event Hubs 소비자 그룹은 Service Bus 토픽에 대한 구독과 같습니다. 소비자 그룹 생성 요청에 이벤트 허브 또는 이벤트 허브가 속한 네임스페이스에 대한 관리 권한을 부여하는 토큰이 함께 지정된 경우 클라이언트는 소비자 그룹을 만들 수 있습니다. 수신 요청이 소비자 그룹, 이벤트 허브 또는 이벤트 허브가 속한 네임스페이스에서의 수신 권한을 부여하는 토큰과 함께 지정된 경우 클라이언트는 해당 소비자 그룹의 데이터를 사용할 수 있습니다.

Service Bus의 현재 버전은 개별 구독에 대한 SAS 규칙을 지원하지 않습니다. Event Hubs 소비자 그룹에 대해서도 마찬가지입니다. SAS 지원은 나중에 두 기능에 대해 추가됩니다.

개별 소비자 그룹에 대한 SAS 인증이 없는 경우, 공용 키가 있는 모든 소비자 그룹을 보호하기 위해 SAS 키를 사용할 수 있습니다. 이 방식에서는 애플리케이션이 이벤트 허브의 임의의 소비자 그룹에서 데이터를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 항목을 방문하세요.

* [Event Hubs 개요]
* [공유 액세스 서명 개요]
* [Event Hubs를 사용하는 샘플 애플리케이션]

[Event Hubs 개요]: event-hubs-what-is-event-hubs.md
[Event Hubs를 사용하는 샘플 애플리케이션]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[공유 액세스 서명 개요]: ../service-bus-messaging/service-bus-sas.md

