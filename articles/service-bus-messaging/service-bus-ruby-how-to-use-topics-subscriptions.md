---
title: Service Bus 토픽을 사용하는 방법(Ruby) | Microsoft Docs
description: Azure에서 Service Bus 토픽 및 구독을 사용하는 방법에 대해 알아봅니다. 코드 샘플은 Ruby 애플리케이션용으로 작성되었습니다.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: c2862c3521c05ba81f7a567f7951d22a9ab95c44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472338"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Ruby에서 Service Bus 토픽 및 구독을 사용하는 방법
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

이 문서에서는 Ruby 애플리케이션에서 Service Bus 토픽과 구독을 사용하는 방법을 설명합니다. 다루는 시나리오는 다음과 같습니다.

- 토픽 및 구독 만들기 
- 구독 만들기 필터 
- 토픽에 메시지 보내기 
- 구독에서 메시지 받기
- 토픽 및 구독 삭제


## <a name="prerequisites"></a>필수 조건
1. Azure 구독. 이 자습서를 완료하려면 Azure 계정이 필요합니다. 활성화할 수 있습니다 하 [Visual Studio 또는 MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) 하거나 등록을 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)합니다.
2. 단계를 수행 합니다 [빠른 시작: Service Bus 토픽 및 구독 항목을 만들려면 Azure portal을 사용 하 여](service-bus-quickstart-topics-subscriptions-portal.md) Service Bus를 만들려면 **네임 스페이스** 받고 합니다 **연결 문자열**합니다. 

    > [!NOTE]
    > 만들려는 **항목** 및 **구독** 사용 하 여 토픽에 **Ruby** 이 빠른 시작 합니다. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>토픽 만들기
**Azure::ServiceBusService** 개체를 사용하면 토픽으로 작업할 수 있습니다. 다음 코드는 **Azure::ServiceBusService** 개체를 만듭니다. 토픽을 만들려면 `create_topic()` 메서드를 사용합니다. 다음 예제에서는 토픽을 만들거나 오류를 출력합니다.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

추가 옵션을 사용하여 **Azure::ServiceBus::Topic** 개체를 전달할 수도 있습니다. 그러면 메시지 TTL(Time to Live) 또는 최대 큐 크기와 같은 기본 토픽 설정을 재정의할 수 있습니다. 다음 예제에서는 최대 큐 크기를 5GB로 설정하고 TTL(Time to Live)을 1분으로 설정합니다.

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>구독 만들기
토픽 구독은 **Azure::ServiceBusService** 개체로도 만들 수 있습니다. 구독에는 이름이 지정되며, 구독의 가상 큐에 전달되는 메시지 집합을 제한하는 선택적 필터가 있을 수 있습니다.

구독은 영구 보존됩니다. 즉, 구독 자체나 구독이 연결된 토픽이 삭제될 때까지는 계속 유지됩니다. 애플리케이션에 구독을 만들기 위한 논리가 포함된 경우, getSubscription 메서드를 사용하여 구독이 이미 존재하는지를 먼저 확인해야 합니다.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>기본(MatchAll) 필터를 사용하여 구독 만들기
새 구독을 만들 때 필터를 지정하지 않은 경우 **MatchAll** 필터(기본값)가 사용됩니다. **MatchAll** 필터를 사용하면 토픽에 게시된 모든 메시지가 구독의 가상 큐에 배치됩니다. 다음 예제에서는 "all-messages"라는 구독을 만들고 기본 **MatchAll** 필터를 사용합니다.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>필터를 사용하여 구독 만들기
토픽으로 전송된 메시지 중 특정 구독 내에 표시되어야 하는 메시지의 범위를 지정하는 필터를 정의할 수도 있습니다.

구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는 **Azure::ServiceBus::SqlFilter**입니다. SQL 필터는 토픽에 게시된 메시지의 속성에 적용됩니다. SQL 필터와 함께 사용할 수 있는 식에 대한 자세한 내용은 [SqlFilter](service-bus-messaging-sql-filter.md) 구문을 참조하세요.

**Azure::ServiceBusService** 개체의 `create_rule()` 메서드를 사용하여 구독에 필터를 추가할 수 있습니다. 이 메서드를 사용하면 기존 구독에 새 필터를 추가할 수 있습니다.

기본 필터는 모든 새 구독에 자동 적용되므로 먼저 기본 필터를 제거해야 합니다. 그렇지 않으면 **MatchAll**이 사용자가 지정하는 기타 필터를 재정의합니다. **Azure::ServiceBusService** 개체의 `delete_rule()` 메서드를 사용하여 기본 규칙을 제거할 수 있습니다.

다음 예제에서는 사용자 지정 `message_number` 속성이 3보다 큰 메시지만 선택하는 **Azure::ServiceBus::SqlFilter**를 사용하여 "high-messages"라는 구독을 만듭니다.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

마찬가지로, 다음 예제에서는 `message_number` 속성이 3보다 작거나 같은 메시지만 선택하는 **Azure::ServiceBus::SqlFilter**를 사용하여 `low-messages`라는 구독을 만듭니다.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

이제 `test-topic`으로 메시지를 보내는 경우 `all-messages` 토픽 구독을 구독하는 수신자에게는 항상 배달되고, `high-messages` 및 `low-messages` 토픽 구독을 구독하는 수신자에게는 메시지 내용에 따라 선택적으로 배달됩니다.

## <a name="send-messages-to-a-topic"></a>토픽에 메시지 보내기
Service Bus 토픽에 메시지를 보내려면 애플리케이션에서 **Azure::ServiceBusService** 개체의 `send_topic_message()` 메서드를 사용해야 합니다. Service Bus 토픽으로 전송되는 메시지는 **Azure::ServiceBus::BrokeredMessage** 개체의 인스턴스입니다. **Azure::ServiceBus::BrokeredMessage** 개체에는 표준 속성 세트(예: `label` 및 `time_to_live`), 애플리케이션별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 문자열 데이터의 본문이 있습니다. 응용 프로그램은 문자열 값을 `send_topic_message()` 메서드에 전달하여 메시지의 본문을 설정할 수 있습니다. 그러면 필수 표준 속성이 기본값으로 채워집니다.

다음 예제에서는 5개의 테스트 메시지를 `test-topic`에 보내는 방법을 보여 줍니다. 루프가 반복될 때마다 각 메시지의 `message_number` 사용자 지정 속성 값이 달라집니다. 이 값에 따라 메시지를 수신하는 구독이 결정됩니다.

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus 토픽은 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 애플리케이션 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 이 토픽 크기는 생성 시 정의되며 상한이 5GB입니다.

## <a name="receive-messages-from-a-subscription"></a>구독에서 메시지 받기
**Azure::ServiceBusService** 개체의 `receive_subscription_message()` 메서드를 사용하여 구독에서 메시지를 받습니다. 기본적으로 메시지는 구독에서 삭제하지 않고 읽고(피크) 잠급니다. `peek_lock` 옵션을 **false**로 설정하여 메시지를 구독에서 읽고 삭제할 수 있습니다.

기본 동작은 읽기 및 삭제가 2단계 작업으로 수행되도록 하므로 메시지 누락이 허용되지 않는 애플리케이션도 지원할 수 있습니다. Service Bus는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 애플리케이션에 반환합니다. 애플리케이션은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후, `delete_subscription_message()` 메서드를 호출하고 삭제될 메시지를 매개 변수로 제공하여 수신 프로세스의 두 번째 단계를 완료합니다. `delete_subscription_message()` 메서드는 메시지를 사용 중인 것으로 표시하고 구독에서 제거합니다.

`:peek_lock` 매개 변수를 **false**로 설정하면 메시지 읽기 및 삭제가 가장 간단한 모델이 됩니다. 이러한 모델은 오류 발생 시 애플리케이션이 메시지를 처리하지 않아도 되는 시나리오에 가장 적합합니다. 소비자가 수신 요청을 생성했는데 해당 요청을 처리하기 전에 응용 프로그램 작동이 중단되는 경우를 가정해 보겠습니다. Service Bus가 메시지를 사용되는 것으로 표시했기 때문에 애플리케이션이 다시 시작되고 메시지를 다시 사용하기 시작할 때 충돌 전에 사용한 메시지는 누락됩니다.

다음 예제에서는 `receive_subscription_message()`를 사용하여 메시지를 받고 처리하는 방법을 보여줍니다. 이 예제에서는 먼저 **false**로 설정된 `:peek_lock`을 사용하여 `low-messages` 구독에서 메시지를 받고 삭제한 후 `high-messages`에서 다른 메시지를 받고 `delete_subscription_message()`를 사용하여 메시지를 삭제합니다.

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>애플리케이션 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법
Service Bus는 애플리케이션 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 애플리케이션이 메시지를 처리할 수 없는 경우 **Azure::ServiceBusService** 개체의 `unlock_subscription_message()` 메서드를 호출할 수 있습니다. 그러면 Service Bus에서 구독 내 메시지의 잠금을 해제하고 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

구독 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 응용 프로그램에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램 작동이 중단되는 경우) Service Bus가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

애플리케이션이 메시지를 처리한 후 `delete_subscription_message()` 메서드가 호출되기 전에 충돌하는 경우, 다시 시작될 때 메시지가 애플리케이션에 다시 배달됩니다. 이를 *최소 한 번 이상 처리*라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 애플리케이션 개발자가 중복 메시지 배달을 처리하는 논리를 애플리케이션에 추가해야 합니다. 이 논리는 배달 시도 간에 일정하게 유지되는 메시지의 `message_id` 속성을 사용하여 추가하는 경우가 많습니다.

## <a name="delete-topics-and-subscriptions"></a>토픽 및 구독 삭제
토픽과 구독은 영구적이므로, [Azure Portal][Azure portal] 또는 프로그래밍 방식을 통해 명시적으로 삭제해야 합니다. 다음 예제는 `test-topic` 토픽을 삭제하는 방법을 보여 줍니다.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

토픽을 삭제하면 토픽에 등록된 모든 구독도 삭제됩니다. 구독을 개별적으로 삭제할 수도 있습니다. 다음 코드는 이름이 `high-messages`인 구독을 `test-topic` 토픽에서 삭제하는 방법을 보여줍니다.

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>다음 단계
이제 Service Bus 토픽의 기본 사항을 익혔으므로 다음 링크를 따라 이동하여 자세한 내용을 확인할 수 있습니다.

* [큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)을 참조하세요.
* [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)에 대한 API 참조.
* GitHub에서 [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) 리포지토리를 방문하세요.

[Azure portal]: https://portal.azure.com
