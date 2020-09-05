---
title: '빠른 시작: Python에서 Azure Service Bus 토픽 및 구독 사용'
description: 이 문서에서는 Azure Service Bus 항목, 구독, 항목에 구독 보내기, 구독에서 메시지 수신을 만드는 방법을 보여줍니다.
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: f6d1b25cb502b8cb208ba5b59c91667e03c77778
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88064386"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>빠른 시작: Python에서 Service Bus 토픽 및 구독 사용

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

이 문서에서는 Python을 사용하여 Azure Service Bus 토픽과 구독을 사용하는 방법을 보여 줍니다. 이 샘플에서는 [Azure Python SDK][Azure Python package] 패키지를 사용하여 다음을 수행합니다. 

- 토픽 및 토픽에 대한 구독 만들기
- 구독 필터 및 규칙 만들기
- 토픽에 메시지 보내기 
- 구독에서 메시지 받기
- 토픽 및 구독 삭제

## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독 [Visual Studio 또는 MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
- [빠른 시작: Azure Portal을 사용하여 Service Bus 토픽 및 구독 만들기](service-bus-quickstart-topics-subscriptions-portal.md)의 단계에 따라 만든 Service Bus 네임스페이스입니다. **공유 액세스 정책** 화면에서 이 빠른 시작의 뒷부분에서 사용할 수 있도록 네임스페이스 이름, 공유 액세스 키 이름 및 기본 키 값을 복사합니다. 
- [Azure Python SDK][Azure Python package] 패키지가 설치되어 있는 Python 3.4x 이상. 자세한 내용은 [Python 설치 가이드](/azure/developer/python/azure-sdk-install)를 참조하세요.

## <a name="create-a-servicebusservice-object"></a>ServiceBusService 개체 만들기

**ServiceBusService** 개체를 사용하면 토픽 및 토픽에 대한 구독 작업을 수행할 수 있습니다. 프로그래밍 방식으로 Service Bus에 액세스하려면 Python 파일의 맨 위쪽에 다음 줄을 추가합니다.

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

다음 코드를 추가하여 **ServiceBusService** 개체를 만듭니다. `<namespace>`, `<sharedaccesskeyname>` 및 `<sharedaccesskeyvalue>`를 사용자의 Service Bus 네임스페이스, SAS(공유 액세스 서명) 키 이름 및 기본 키 값으로 바꿉니다. [Azure Portal][Azure portal]의 Service Bus 네임스페이스에 있는 **공유 액세스 정책**에서 이러한 값을 찾을 수 있습니다.

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>토픽 만들기

다음 코드는 `create_topic` 메서드를 사용하여 기본 설정이 포함된 `mytopic`이라는 Service Bus 토픽을 만듭니다.

```python
bus_service.create_topic('mytopic')
```

토픽 옵션을 사용하여 메시지 TTL(Time to Live) 또는 최대 토픽 크기와 같은 기본 토픽 설정을 재정의할 수 있습니다. 다음 예제에서는 최대 토픽 크기가 5GB이고 기본 메시지 TTL은 1분인 `mytopic`이라는 토픽을 만듭니다.

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>구독 만들기

토픽에 대한 구독은 **ServiceBusService** 개체로도 만들 수 있습니다. 구독에는 해당 가상 큐에 전달되는 메시지 세트를 제한하는 필터가 있을 수 있습니다. 필터를 지정하지 않으면 새 구독은 기본 **MatchAll** 필터를 사용하여 토픽에 게시된 모든 메시지를 구독의 가상 큐에 배치합니다. 다음 예제에서는 **MatchAll** 필터를 사용하는 `AllMessages`라는 `mytopic`에 대한 구독을 만듭니다.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>구독과 함께 필터 사용

**ServiceBusService** 개체의 `create_rule` 메서드를 사용하여 구독에 표시되는 메시지를 필터링합니다. 구독을 만들 때 규칙을 지정하거나 기존 구독에 규칙을 추가할 수 있습니다.

가장 유연한 필터 유형은 SQL-92의 하위 집합을 사용하는 **SqlFilter**입니다. SQL 필터는 토픽에 게시된 메시지의 속성에 적용됩니다. SQL 필터와 함께 사용할 수 있는 식에 대한 자세한 내용은 [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 구문을 참조하세요.

**MatchAll** 기본 필터가 새로운 모든 구독에 자동으로 적용되므로 필터링하려는 구독에서 기본 필터를 제거해야 합니다. 그렇지 않으면 **MatchAll**이 사용자가 지정하는 기타 필터를 재정의합니다. **ServiceBusService** 개체의 `delete_rule` 메서드를 사용하여 기본 규칙을 제거할 수 있습니다.

다음 예제에서는 `HighMessageFilter`라는 **SqlFilter** 규칙을 사용하여 이름이 `HighMessages`인 `mytopic`에 대한 구독을 만듭니다. `HighMessageFilter` 규칙은 사용자 지정 `messageposition` 속성이 3보다 큰 메시지만 선택합니다.

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

다음 예제에서는 `LowMessageFilter`라는 **SqlFilter** 규칙을 사용하여 이름이 `LowMessages`인 `mytopic`에 대한 구독을 만듭니다. `LowMessageFilter` 규칙은 `messageposition` 속성이 3보다 작거나 같은 메시지만 선택합니다.

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

`AllMessages`, `HighMessages`및 `LowMessages`가 모두 적용되는 경우 `mytopic`로 전송된 메시지는 항상 `AllMessages` 구독의 수신자에게 배달됩니다. 또한 메시지의 `messageposition` 속성 값에 따라 메시지는 `HighMessages` 또는 `LowMessages` 구독에 선택적으로 배달됩니다. 

## <a name="send-messages-to-a-topic"></a>토픽에 메시지 보내기

애플리케이션에서 **ServiceBusService** 개체의 `send_topic_message` 메서드를 사용하여 Service Bus 토픽에 메시지를 보냅니다.

다음 예제에서는 `mytopic` 토픽에 5개의 테스트 메시지를 보냅니다. 사용자 지정 `messageposition` 속성 값은 루프의 반복에 따라 다르며, 해당 메시지를 받는 구독을 결정합니다. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>메시지 크기 제한 및 할당량

Service Bus 토픽은 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 애플리케이션 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 생성 시 토픽 크기를 정의할 수 있으며, 상한이 5GB입니다. 

할당량에 대한 자세한 내용은 [Service Bus 할당량][Service Bus quotas]을 참조하세요.

## <a name="receive-messages-from-a-subscription"></a>구독에서 메시지 받기

애플리케이션은 **ServiceBusService** 개체의 `receive_subscription_message` 메서드를 사용하여 구독에서 메시지를 받습니다. 다음 예제에서는 `LowMessages` 구독에서 메시지를 수신하고 읽을 때 해당 메시지를 삭제합니다.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

`receive_subscription_message`의 선택적 `peek_lock` 매개 변수는 메시지를 읽을 때 Service Bus가 구독에서 메시지를 삭제할지 여부를 결정합니다. 메시지 수신에 대한 기본 모드는 *PeekLock*이거나, `peek_lock`이 구독에서 메시지를 삭제하지 않고 메시지를 읽고(피킹) 잠그는 **True**로 설정됩니다. 그런 다음, 각 메시지를 명시적으로 완료하여 구독에서 제거해야 합니다.

메시지를 읽을 때 구독에서 메시지를 삭제하려면 앞의 예제와 같이 `peek_lock` 매개 변수를 **False**로 설정하면 됩니다. 수신 작업의 일부로 메시지를 삭제하는 것은 가장 간단한 모델이며, 오류가 발생하는 경우 애플리케이션에서 누락된 메시지를 허용할 수 있는 경우 제대로 작동합니다. 이 동작에 대한 이해를 돕기 위해 애플리케이션이 수신 요청을 실행한 다음, 처리하기 전에 크래시되는 시나리오를 고려해 보세요. 메시지가 수신되는 동안 삭제된 경우 애플리케이션이 다시 시작되고 메시지를 다시 사용하기 시작할 때 크래시 전에 수신된 메시지는 누락됩니다.

애플리케이션에서 누락된 메시지를 허용할 수 없는 경우 수신은 2단계 작업이 됩니다. PeekLock은 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후, 애플리케이션에 반환합니다. 애플리케이션은 메시지를 처리하거나 저장한 후 **Message** 개체에 대해 `complete` 메서드를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다.  `complete` 메서드는 메시지를 사용 중인 것으로 표시하고 구독에서 제거합니다.

다음 예제에서는 피킹 잠금 시나리오를 보여 줍니다.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>애플리케이션 크래시 및 읽을 수 없는 메시지 처리

Service Bus는 애플리케이션 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 애플리케이션이 메시지를 처리할 수 없는 경우 **Message** 개체의 `unlock` 메서드를 호출할 수 있습니다. Service Bus에서 구독 내 메시지의 잠금을 해제하므로 동일하거나 다른 소비 애플리케이션에서 메시지를 다시 받을 수 있습니다.

구독 내에서 잠긴 메시지에 대한 시간 제한도 있습니다. 애플리케이션에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 애플리케이션이 크래시되는 경우) Service Bus가 메시지의 잠금을 자동으로 해제하여 다시 받을 수 있게 합니다.

애플리케이션이 메시지를 처리한 후 `complete` 메서드가 호출되기 전에 크래시되는 경우 다시 시작될 때 메시지가 애플리케이션에 다시 배달됩니다. 이 동작은 일반적으로 *최소 1회 처리*라고 합니다. 즉, 각 메시지가 최소 1회 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 시나리오에서 중복 처리를 허용할 수 없는 경우 배달 시도 간에 일정하게 유지되는 메시지의 **MessageId** 속성을 사용하여 중복 메시지 배달을 처리할 수 있습니다. 

## <a name="delete-topics-and-subscriptions"></a>토픽 및 구독 삭제

토픽 및 구독을 삭제하려면 [Azure Portal][Azure portal] 또는 `delete_topic` 메서드를 사용합니다. 다음 코드는 `mytopic`이라는 토픽을 삭제합니다.

```python
bus_service.delete_topic('mytopic')
```

토픽을 삭제하면 토픽에 대한 모든 구독도 삭제됩니다. 구독을 개별적으로 삭제할 수도 있습니다. 다음 코드는 `mytopic` 토픽에서 `HighMessages`라는 구독을 삭제합니다.

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

기본적으로 토픽 및 구독은 영구적이며 사용자가 삭제할 때까지 존재합니다. 구독이 특정 기간이 경과한 후 자동으로 삭제되도록 하려면 구독에서 [auto_delete_on_idle](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) 매개 변수를 설정하면 됩니다. 

> [!TIP]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하여 손쉽게 메시징 엔터티를 관리할 수 있습니다. 이 도구를 통해 가져오기/내보내기 기능 또는 토픽, 큐, 구독, 릴레이 서비스, Notification Hubs 및 Event Hubs를 테스트하는 기능과 같은 고급 기능을 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이제 Service Bus 토픽의 기본 사항을 익혔으므로 다음 링크를 따라 이동하여 자세한 내용을 확인할 수 있습니다.

* [큐, 토픽 및 구독][Queues, topics, and subscriptions]
* [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 참조

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md