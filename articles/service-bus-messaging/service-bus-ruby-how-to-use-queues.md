---
title: Ruby를 통해 Azure Service Bus 큐를 사용하는 방법 | Microsoft Docs
description: Azure에서 Service Bus 큐를 사용하는 방법에 대해 알아봅니다. 코드 샘플은 Ruby로 작성되었습니다.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 6c42fbffd0b4569a9b04dede94061e716c48ecf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474592"
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Ruby에서 Service Bus 큐를 사용하는 방법

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

이 자습서에서는 메시지를 보내고 Service Bus 큐에서 메시지를 수신 하는 Ruby 응용 프로그램을 만드는 방법을 알아봅니다. 샘플은 Ruby로 작성되었으며 Azure gem을 사용합니다.

## <a name="prerequisites"></a>필수 조건
1. Azure 구독. 이 자습서를 완료하려면 Azure 계정이 필요합니다. 활성화할 수 있습니다 하 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) 에 등록 또는 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)합니다.
2. 단계를 수행 합니다 [Service Bus 큐를 사용 하 여 Azure portal](service-bus-quickstart-portal.md) 문서.
    1. 빠른 읽을 **개요** Service bus **큐**합니다. 
    2. Service Bus를 만듭니다 **네임 스페이스**합니다. 
    3. 가져오기의 **연결 문자열**합니다. 

        > [!NOTE]
        > 만들려는 **큐** 이 자습서에서는 Ruby를 사용 하 여 Service Bus 네임 스페이스에서입니다. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>큐를 만드는 방법
**Azure::ServiceBusService** 개체를 사용하면 큐로 작업할 수 있습니다. 큐를 만들려면 `create_queue()` 메서드를 사용합니다. 다음 예제에서는 큐를 만들거나 오류를 출력합니다.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

추가 옵션을 사용하여 **Azure::ServiceBus::Queue** 개체를 전달할 수도 있습니다. 추가 옵션을 사용하면 메시지 TTL(Time to Live) 또는 최대 큐 크기와 같은 기본 큐 설정을 재정의할 수 있습니다. 다음 예제에서는 최대 큐 크기를 5GB로 설정하고 TTL(Time to Live)을 1분으로 설정하는 방법을 보여줍니다.

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>큐에 메시지를 보내는 방법
Service Bus 큐에 메시지를 보내기 위해 애플리케이션은 **Azure::ServiceBusService** 개체에 대해 `send_queue_message()` 메서드를 호출합니다. Service Bus 큐로 보내고 받는 메시지는 **Azure::ServiceBus::BrokeredMessage** 개체이며 표준 속성 세트(예: `label` 및 `time_to_live`), 애플리케이션별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 임의 애플리케이션 데이터 본문을 포함합니다. 애플리케이션은 문자열 값을 메시지로 전달하여 메시지의 본문을 설정할 수 있습니다. 그러면 필수 표준 속성이 기본값으로 채워집니다.

다음 예제에서는 `send_queue_message()`를 사용하여 `test-queue`라는 큐에 테스트 메시지를 보내는 방법을 보여줍니다.

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus 큐는 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 애플리케이션 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다.

## <a name="how-to-receive-messages-from-a-queue"></a>큐에서 메시지를 받는 방법
**Azure::ServiceBusService** 개체의 `receive_queue_message()` 메서드를 사용하여 큐에서 메시지를 받습니다. 기본적으로 메시지는 큐에서 삭제하지 않고 읽고 잠급니다. 그러나 `:peek_lock` 옵션을 **false**로 설정하여 메시지를 읽고 큐에서 삭제할 수 있습니다.

기본 동작은 읽기 및 삭제가 2단계 작업으로 수행되도록 하므로 메시지 누락이 허용되지 않는 애플리케이션도 지원할 수 있습니다. Service Bus는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 애플리케이션에 반환합니다. 애플리케이션은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후, `delete_queue_message()` 메서드를 호출하고 삭제될 메시지를 매개 변수로 제공하여 수신 프로세스의 두 번째 단계를 완료합니다. `delete_queue_message()` 메서드는 메시지를 이용되는 것으로 표시하고 큐에서 제거합니다.

경우는 `:peek_lock` 매개 변수는 설정 **false**, 읽기 및 메시지를 삭제는 가장 간단한 모델이 되며는 응용 프로그램 오류 발생 시 메시지를 처리 하지 허용할 수 없는 시나리오에 가장 적합 합니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. Service Bus가 메시지를 사용되는 것으로 표시했기 때문에 애플리케이션이 다시 시작되고 메시지를 다시 사용하기 시작할 때 충돌 전에 사용한 메시지는 누락됩니다.

다음 예제에서는 `receive_queue_message()`를 사용하여 메시지를 받고 처리하는 방법을 보여줍니다. 이 예제에서는 먼저 **false**로 설정된 `:peek_lock`을 사용하여 메시지를 받고 삭제한 후 다른 메시지를 받고 그런 다음 `delete_queue_message()`를 사용하여 메시지를 삭제합니다.

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>애플리케이션 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법
Service Bus는 애플리케이션 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 애플리케이션이 메시지를 처리할 수 없는 경우 **Azure::ServiceBusService** 개체의 `unlock_queue_message()` 메서드를 호출할 수 있습니다. 이 호출은 Service Bus에서 큐 메시지의 잠금을 해제하므로 동일한 소비 애플리케이션이나 다른 소비 애플리케이션에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 시간 제한도 있으며, 애플리케이션에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 애플리케이션이 크래시되는 경우) Service Bus가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

애플리케이션이 메시지를 처리한 후 `delete_queue_message()` 메서드가 호출되기 전에 충돌하는 경우, 다시 시작될 때 메시지가 애플리케이션에 다시 배달됩니다. 이 프로세스를 *최소 한 번 이상 처리*라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 애플리케이션 개발자가 중복 메시지 배달을 처리하는 논리를 애플리케이션에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지되는 메시지의 `message_id` 속성을 사용합니다.

## <a name="next-steps"></a>다음 단계
이제 Service Bus 큐의 기본 사항을 익혔으므로 다음 링크를 따라 자세히 알아보세요.

* [큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md) 개요.
* GitHub에서 [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) 리포지토리를 방문하세요.

이 문서에서 설명한 Azure Service Bus 큐와 [Ruby에서 큐 Storage를 사용하는 방법](../storage/queues/storage-ruby-how-to-use-queue-storage.md) 문서에서 설명한 Azure 큐를 비교하려면 [Azure 큐 및 Azure Service Bus 큐 - 비교 및 대조](service-bus-azure-and-service-bus-queues-compared-contrasted.md)를 참조하세요.

