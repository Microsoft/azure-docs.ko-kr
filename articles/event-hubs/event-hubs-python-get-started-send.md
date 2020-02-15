---
title: Python(이전)을 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기
description: 이 연습에서는 이전 azure-eventhub 버전 1 패키지를 사용하여 이벤트를 Azure Event Hubs로 보내거나 받는 Python 스크립트를 만들고 실행하는 방법을 보여줍니다.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 654ccd6352dc0b671cc3becdafd2f1e1102dd39e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902945"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>빠른 시작: Python(azure-eventhub 버전 1)을 사용하여 Event Hubs와 이벤트 주고 받기

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스의 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 내용은 [Azure Event Hubs](event-hubs-about.md) 및 [Azure Event Hubs의 기능 및 용어](event-hubs-features.md)를 참조하세요.

이 빠른 시작에서는 이벤트 허브와 이벤트를 주고 받는 Python 애플리케이션을 만드는 방법을 보여줍니다. 

> [!WARNING]
> 이 빠른 시작은 Azure Event Hubs Python SDK 버전 1을 위한 것입니다. 코드를 [Python SDK 버전 5](get-started-python-send-v2.md)로 [마이그레이션](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)하는 것이 좋습니다.

 
 

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.

- Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
- [빠른 시작: Azure Portal을 사용하여 이벤트 허브 만들기](event-hubs-create.md)의 지침에 따라 만든 활성 Event Hubs 네임스페이스 및 이벤트 허브. 네임스페이스와 이벤트 허브 이름을 적어 두세요. 이 연습의 뒷부분에서 사용됩니다. 
- Event Hubs 네임스페이스의 공유 액세스 키 이름 및 기본 키 값. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)의 지침에 따라 액세스 키 이름과 값을 가져옵니다. 기본 액세스 키 이름은 **RootManageSharedAccessKey**입니다. 키 이름과 기본 키 값을 복사해 두세요. 이 연습의 뒷부분에서 사용됩니다. 
- `pip`가 설치 및 업데이트된 Python 3.4 이상
- Event Hubs용 Python 패키지. 이 패키지를 설치하려면 경로에 Python이 있는 명령 프롬프트에서 다음 명령을 실행합니다. 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```


## <a name="send-events"></a>이벤트 보내기

이벤트 허브로 이벤트를 보내는 Python 애플리케이션을 만드는 방법은 다음과 같습니다.

> [!NOTE]
> 이 빠른 시작을 진행하는 대신, GitHub에서 [샘플 앱](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)을 다운로드하여 실행해도 됩니다. 이 경우 `EventHubConnectionString` 및 `EventHubName` 문자열을 해당 이벤트 허브 값으로 바꿔야 합니다.

1. 선호하는 Python 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))를 엽니다.
2. *send.py*라는 새 파일을 만듭니다. 이 스크립트는 이벤트 허브에 100개의 이벤트를 전송합니다.
3. 다음 코드를 *send.py* 파일에 붙여넣고, Event Hubs \<네임스페이스>, \<eventhub>, \<AccessKeyName> 및 \<기본 키 값>을 사용자 고유의 값으로 바꿉니다. 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. 파일을 저장합니다. 

스크립트를 실행하려면 *send.py* 파일을 저장한 디렉터리에서 다음 명령을 실행합니다.

```cmd
start python send.py
```

축하합니다! 이제 Event Hub에 메시지를 보냈습니다.

## <a name="receive-events"></a>이벤트 수신

이벤트 허브에서 이벤트를 수신하는 Python 애플리케이션을 만드는 방법은 다음과 같습니다.

1. Python 편집기에서 *recv.py*라는 파일을 만듭니다.
2. 다음 코드를 *recv.py* 파일에 붙여넣고, Event Hubs \<네임스페이스>, \<eventhub>, \<AccessKeyName> 및 \<기본 키 값>을 사용자 고유의 값으로 바꿉니다. 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. 파일을 저장합니다.

스크립트를 실행하려면 *recv.py* 파일을 저장한 디렉터리에서 다음 명령을 실행합니다.

```cmd
start python recv.py
```

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 문서를 참조하세요.

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs의 기능 및 용어](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)

