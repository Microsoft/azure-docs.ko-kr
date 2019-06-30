---
title: Python에서 Azure Service Bus 큐를 사용하는 방법 | Microsoft Docs
description: Python에서 Azure Service Bus 큐를 사용하는 방법에 대해 알아봅니다.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: b74238ee49fe0d96d218f1800a33a9d60badc6d5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341704"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Python에서 Service Bus 큐를 사용하는 방법

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

이 자습서에서는 메시지를 보내고 Service Bus 큐에서 메시지를 수신 하는 Python 응용 프로그램을 만드는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 조건
1. Azure 구독. 이 자습서를 완료하려면 Azure 계정이 필요합니다. 활성화할 수 있습니다 하 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) 에 등록 또는 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)합니다.
2. 단계를 수행 합니다 [Service Bus 큐를 사용 하 여 Azure portal](service-bus-quickstart-portal.md) 문서.
    1. 빠른 읽을 **개요** Service bus **큐**합니다. 
    2. Service Bus를 만듭니다 **네임 스페이스**합니다. 
    3. 가져오기의 **연결 문자열**합니다. 

        > [!NOTE]
        > 만들려는 **큐** 이 자습서에서는 Python을 사용 하 여 Service Bus 네임 스페이스에서입니다. 
1. Python을 설치 또는 [Python Azure Service Bus 패키지][Python Azure Service Bus package]를 참조 합니다 [Python 설치 가이드](../python-how-to-install.md)합니다. Service Bus Python SDK의 전체 설명서를 참조 하세요 [여기](/python/api/overview/azure/servicebus?view=azure-python)합니다.

## <a name="create-a-queue"></a>큐 만들기
합니다 **ServiceBusClient** 개체를 사용 하면 큐로 작업할 수 있습니다. 프로그래밍 방식으로 Service Bus에 액세스하려는 Python 파일의 맨 위쪽에 다음 코드를 추가합니다.

```python
from azure.servicebus import ServiceBusClient
```

다음 코드를 **ServiceBusClient** 개체입니다. 대체 `<CONNECTION STRING>` 에 service bus 연결 문자열을 사용 하 여 합니다.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

SAS 키 이름 및 값에 대 한 값에서 찾을 수 있습니다는 [Azure portal][Azure portal] 연결 정보 또는 Visual Studio **속성** 창 (서버 탐색기에서 Service Bus 네임 스페이스를 선택 하는 경우 표시 된 이전 섹션에서).

```python
sb_client.create_queue("taskqueue")
```

`create_queue`는 추가 옵션도 지원합니다. 이러한 옵션을 통해 메시지 TTL(Time to Live)이나 최대 큐 크기 등 기본 큐 설정을 재정의할 수 있습니다. 다음은 최대 큐 크기를 5GB로, TTL 값을 1분으로 설정하는 예제입니다.

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120, default_message_time_to_live=datetime.timedelta(minutes=1))
```

자세한 내용은 [Azure Service Bus Python 설명서](/python/api/overview/azure/servicebus?view=azure-python)합니다.

## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기
응용 프로그램 호출 Service Bus 큐에 메시지를 보내려고 합니다 `send` 메서드는 `ServiceBusClient` 개체입니다.

다음 예제에서는 `send_queue_message`를 사용하여 `taskqueue`라는 큐에 테스트 메시지를 보내는 방법을 보여줍니다.

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

Service Bus 큐는 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 애플리케이션 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다. 할당량에 대한 자세한 내용은 [Service Bus 할당량][Service Bus quotas]을 참조하세요.

자세한 내용은 [Azure Service Bus Python 설명서](/python/api/overview/azure/servicebus?view=azure-python)합니다.

## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기
사용 하 여 큐에서 메시지를 수신 합니다 `get_receiver` 메서드는 `ServiceBusService` 개체:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

## Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

자세한 내용은 [Azure Service Bus Python 설명서](/python/api/overview/azure/servicebus?view=azure-python)합니다.


`peek_lock` 매개 변수가 **False**로 설정된 경우 메시지를 읽으면 큐에서 해당 메시지가 삭제됩니다. `peek_lock` 매개 변수를 **True**로 설정하여 큐에서 삭제되지 않도록 메시지를 읽은(peek) 후 잠글 수 있습니다.

받기 작업의 일부로 메시지를 읽고 삭제하는 동작은 가장 단순한 모델이며, 실패할 경우 애플리케이션이 메시지를 처리하지 않아도 되는 시나리오에서 가장 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. Service Bus는 메시지를 이용되는 것으로 표시하기 때문에 애플리케이션이 다시 시작되고 메시지 소비를 다시 시작할 경우 크래시 전에 소비된 메시지가 누락됩니다.

`peek_lock` 매개 변수를 **True**로 설정하면 수신은 2단계 작업이 되므로, 메시지 누락을 허용하지 않는 애플리케이션을 지원할 수 있습니다. Service Bus는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 애플리케이션에 반환합니다. 애플리케이션은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 **Message** 개체에 대해 **delete** 메서드를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. **delete** 메서드는 메시지를 이용되는 것으로 표시하고 큐에서 제거합니다.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>애플리케이션 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법
Service Bus는 애플리케이션 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 애플리케이션이 메시지를 처리할 수 없는 경우 **Message** 개체의 **unlock** 메서드를 호출할 수 있습니다. 그러면 Service Bus에서 큐 메시지의 잠금을 해제하므로 동일한 소비 애플리케이션이나 다른 소비 애플리케이션에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 애플리케이션에서 잠금 제한 시간이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 애플리케이션이 크래시되는 경우) Service Bus가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

애플리케이션이 메시지를 처리한 후 **delete** 메서드가 호출되기 전에 크래시되는 경우, 다시 시작될 때 메시지가 애플리케이션에 다시 배달됩니다. 이 종종 이라고 **한 번 이상 처리**, 즉, 각 메시지가 최소 한 번 이상 처리 되지만 특정 상황에서는 동일한 메시지를 다시 배달 될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 애플리케이션 개발자가 중복 메시지 배달을 처리하는 논리를 애플리케이션에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지되는 메시지의 **MessageId** 속성을 사용합니다.

> [!NOTE]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 이 도구는 가져오기/내보내기 기능 또는 항목, 큐, 구독, 릴레이 서비스, Notification Hubs 및 이벤트 허브를 테스트하는 기능과 같은 고급 기능을 제공합니다. 

## <a name="next-steps"></a>다음 단계
이제 Service Bus 큐의 기본 사항을 익혔으므로 다음 문서를 따라 자세히 알아보세요.

* [큐, 토픽 및 구독][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

