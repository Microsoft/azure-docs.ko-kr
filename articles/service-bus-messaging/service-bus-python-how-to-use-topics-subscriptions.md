---
title: Python과 함께 Azure Service Bus 토픽을 사용하는 방법 | Microsoft Docs
description: Python에서 Azure Service Bus 토픽 및 구독을 사용하는 방법에 대해 알아봅니다.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 102fe85916194648501be3d2cb39d8bcda9e9f5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811084"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Python에서 Service Bus 토픽 및 구독을 사용하는 방법

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

이 문서에서는 Service Bus 토픽과 구독을 사용하는 방법을 보여 줍니다. 샘플은 Python으로 작성되었으며 [Azure Python SDK 패키지][Azure Python package]를 사용합니다. 다루는 시나리오는 다음과 같습니다.

- 토픽 및 구독 만들기 
- 구독 만들기 필터 
- 토픽에 메시지 보내기 
- 구독에서 메시지 받기
- 토픽 및 구독 삭제

## <a name="prerequisites"></a>필수 조건
1. Azure 구독. 이 자습서를 완료하려면 Azure 계정이 필요합니다. 활성화할 수 있습니다 하 [Visual Studio 또는 MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) 에 등록 또는 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)합니다.
2. 단계를 수행 합니다 [빠른 시작: Service Bus 토픽 및 구독 항목을 만들려면 Azure portal을 사용 하 여](service-bus-quickstart-topics-subscriptions-portal.md) Service Bus를 만들려면 **네임 스페이스** 받고 합니다 **연결 문자열**합니다.

    > [!NOTE]
    > 만들려는 **항목** 및 **구독** 사용 하 여 토픽에 **Python** 이 빠른 시작 합니다. 
3. 설치할 [Python Azure 패키지][Azure Python package]합니다. 참조 된 [Python 설치 가이드](../python-how-to-install.md)합니다.

## <a name="create-a-topic"></a>토픽 만들기

**ServiceBusService** 개체를 사용하면 토픽으로 작업할 수 있습니다. 프로그래밍 방식으로 Service Bus에 액세스하려는 Python 파일의 맨 위쪽에 다음 코드를 추가합니다.

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

다음 코드는 **ServiceBusService** 개체를 만듭니다. `mynamespace`, `sharedaccesskeyname` 및 `sharedaccesskey`를 실제 네임스페이스, SAS(공유 액세스 서명) 키 이름 및 키 값으로 바꿉니다.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

[Azure Portal][Azure portal]에서 SAS 키 이름 값 및 키 값을 가져올 수 있습니다.

```python
bus_service.create_topic('mytopic')
```

`create_topic` 메서드는 추가 옵션도 지원합니다. 이러한 옵션을 통해 메시지 TTL(Time to Live)이나 최대 토픽 크기 등 기본 토픽 설정을 재정의할 수 있습니다. 다음은 최대 토픽 크기를 5GB, TTL(Time to Live)을 1분으로 설정하는 예제입니다.

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>구독 만들기

토픽에 대한 구독은 **ServiceBusService** 개체로도 만들 수 있습니다. 구독에는 이름이 지정되며, 구독의 가상 큐에 전달되는 메시지 집합을 제한하는 선택적 필터가 있을 수 있습니다.

> [!NOTE]
> 구독은 영구적이며, 구독 자체 또는 구독하는 토픽이 삭제될 때까지 계속 유지됩니다.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>기본(MatchAll) 필터를 사용하여 구독 만들기

새 구독을 만들 때 필터를 지정하지 않은 경우 **MatchAll** 필터(기본값)가 사용됩니다. **MatchAll** 필터를 사용하면 토픽에 게시된 모든 메시지가 구독의 가상 큐에 배치됩니다. 다음 예제에서는 `AllMessages`라는 구독을 만들고 기본 **MatchAll** 필터를 사용합니다.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>필터를 사용하여 구독 만들기

토픽으로 전송된 메시지 중 특정 토픽 구독 내에 표시되어야 하는 메시지의 범위를 지정하는 필터를 정의할 수도 있습니다.

구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는 **SqlFilter**입니다. SQL 필터는 토픽에 게시된 메시지의 속성에 적용됩니다. SQL 필터와 함께 사용할 수 있는 식에 대한 자세한 내용은 [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 구문을 참조하세요.

**ServiceBusService** 개체의 **create\_rule** 메서드를 사용하여 구독에 필터를 추가할 수 있습니다. 이 메서드를 사용하면 기존 구독에 새 필터를 추가할 수 있습니다.

> [!NOTE]
> 기본 필터는 모든 새로운 구독에 자동으로 적용되므로 먼저 기본 필터를 제거해야 합니다. 그렇지 않으면 **MatchAll**이 사용자가 지정하는 기타 필터를 재정의합니다. **ServiceBusService** 개체의 `delete_rule` 메서드를 사용하여 기본 규칙을 제거할 수 있습니다.
> 
> 

다음 예제에서는 사용자 지정 `messagenumber` 속성이 3보다 큰 메시지만 선택하는 **SqlFilter**를 사용하여 이름이 `HighMessages`인 구독을 만듭니다.

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

마찬가지로, 다음 예제에서는 `messagenumber` 속성이 3 이하인 메시지만 선택하는 **SqlFilter**가 있는 이름이 `LowMessages`인 구독을 만듭니다.

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

이제 `mytopic`으로 메시지를 보내는 경우 **AllMessages** 토픽 구독을 구독하는 수신자에게는 항상 배달되고, **HighMessages** 및 **LowMessages** 토픽 구독을 구독하는 수신자에게는 메시지 콘텐츠에 따라 선택적으로 배달됩니다.

## <a name="send-messages-to-a-topic"></a>토픽에 메시지 보내기

Service Bus 토픽에 메시지를 보내려면 애플리케이션에서 **ServiceBusService** 개체의 `send_topic_message` 메서드를 사용해야 합니다.

다음 예제에서는 5개의 테스트 메시지를 `mytopic`에 보내는 방법을 보여 줍니다. 루프가 반복될 때마다 각 메시지의 `messagenumber` 속성 값이 변경되며 이 값에 따라 해당 메시지를 받는 구독이 결정됩니다.

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Service Bus 토픽은 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 애플리케이션 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 이 토픽 크기는 생성 시 정의되며 상한이 5GB입니다. 할당량에 대한 자세한 내용은 [Service Bus 할당량][Service Bus quotas]을 참조하세요.

## <a name="receive-messages-from-a-subscription"></a>구독에서 메시지 받기

**ServiceBusService** 개체의 `receive_subscription_message` 메서드를 사용하여 구독에서 메시지를 받습니다.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

`peek_lock` 매개 변수가 **False**로 설정된 경우 메시지를 읽으면 구독에서 해당 메시지가 삭제됩니다. `peek_lock` 매개 변수를 **True**로 설정하여 큐에서 삭제되지 않도록 메시지를 읽은(peek) 후 잠글 수 있습니다.

받기 작업의 일부로 메시지를 읽고 삭제하는 동작은 가장 단순한 모델이며, 오류 발생 시 응용 프로그램이 메시지를 처리하지 않아도 되는 시나리오에서 가장 효과적입니다. 이 동작에 대한 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. Service Bus가 메시지를 사용되는 것으로 표시했기 때문에 애플리케이션이 다시 시작되고 메시지를 다시 사용하기 시작할 때 충돌 전에 사용한 메시지는 누락됩니다.

`peek_lock` 매개 변수를 **True**로 설정하면 수신은 2단계 작업이 되므로, 메시지 누락을 허용하지 않는 애플리케이션을 지원할 수 있습니다. Service Bus는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 애플리케이션에 반환합니다. 애플리케이션은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 **Message** 개체에 대해 `delete` 메서드를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. `delete` 메서드는 메시지를 사용 중인 것으로 표시하고 구독에서 제거합니다.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>애플리케이션 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법

Service Bus는 애플리케이션 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 애플리케이션이 메시지를 처리할 수 없는 경우 **Message** 개체의 `unlock` 메서드를 호출할 수 있습니다. 이 메서드를 사용하면 Service Bus가 구독 내 메시지의 잠금을 해제하고 동일한 소비 애플리케이션이나 다른 소비 애플리케이션에서 메시지를 다시 받을 수 있습니다.

구독 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 애플리케이션에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 애플리케이션이 크래시되는 경우) Service Bus가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

애플리케이션이 메시지를 처리한 후 `delete` 메서드가 호출되기 전에 충돌하는 경우, 다시 시작될 때 메시지가 애플리케이션에 다시 배달됩니다. 이 동작은 대개 한 번 이상 처리\*; 즉, 각 메시지가 최소 한 번 처리 됩니다 되지만 특정 상황에서는 동일한 메시지를 다시 배달 될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 애플리케이션 개발자가 중복 메시지 배달을 처리하는 논리를 애플리케이션에 추가해야 합니다. 이렇게 하려면 배달 시도 간에 일정하게 유지하는 메시지의 **MessageId** 속성을 사용하면 됩니다.

## <a name="delete-topics-and-subscriptions"></a>토픽 및 구독 삭제

토픽과 구독은 영구적이므로, [Azure Portal][Azure portal] 또는 프로그래밍 방식을 통해 명시적으로 삭제해야 합니다. 다음 예제에서는 이름이 `mytopic`인 토픽을 삭제하는 방법을 보여 줍니다.

```python
bus_service.delete_topic('mytopic')
```

토픽을 삭제하면 토픽에 등록된 모든 구독도 삭제됩니다. 구독을 개별적으로 삭제할 수도 있습니다. 다음 코드는 이름이 `HighMessages`인 구독을 `mytopic` 토픽에서 삭제하는 방법을 보여 줍니다.

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>다음 단계

이제 Service Bus 토픽의 기본 사항을 익혔으므로 다음 링크를 따라 이동하여 자세한 내용을 확인할 수 있습니다.

* [큐, 토픽 및 구독][Queues, topics, and subscriptions]을 참조하세요.
* [SqlFilter.SqlExpression][SqlFilter.SqlExpression]에 대한 참조입니다.

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
