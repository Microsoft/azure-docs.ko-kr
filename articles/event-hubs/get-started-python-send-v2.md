---
title: Python을 사용 하 여 이벤트 보내기 또는 받기-Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에 이벤트를 전송 하는 Python 응용 프로그램을 만들기 위한 연습을 제공 합니다.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: d7ab79d49aade7dd6e98cf33ce538174d176c784
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705346"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Python을 사용 하 여 Event Hubs에서 이벤트 보내기 또는 받기

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. 이벤트 허브로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에서는 이벤트 허브에서 이벤트를 보내거나 받는 Python 응용 프로그램을 만드는 방법을 설명 합니다.

> [!IMPORTANT]
> 이 빠른 시작에서는 Azure Event Hubs Python SDK 버전 5를 사용 합니다. Python SDK의 이전 버전 1을 사용 하는 빠른 시작은 [이 문서](event-hubs-python-get-started-send.md)를 참조 하세요. SDK 버전 1을 사용 하는 경우 코드를 최신 버전으로 마이그레이션하는 것이 좋습니다. 자세한 내용은 [마이그레이션 가이드](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)를 참조 하세요.


## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
- [빠른 시작: Azure Portal를 사용 하 여 이벤트 허브 만들기](event-hubs-create.md)의 지침에 따라 만든 활성 Event Hubs 네임 스페이스 및 이벤트 허브 네임스페이스와 이벤트 허브 이름을 적어 두세요. 이 연습의 뒷부분에서 사용됩니다.
- Event Hubs 네임스페이스의 공유 액세스 키 이름 및 기본 키 값. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)의 지침에 따라 액세스 키 이름과 값을 가져옵니다. 기본 액세스 키 이름은 **RootManageSharedAccessKey**입니다. 키 이름과 기본 키 값을 복사해 두세요. 이 연습의 뒷부분에서 사용됩니다.
- `pip` 설치 및 업데이트 된 Python 2.7 및 3.5 이상
- Event Hubs용 Python 패키지. 이 패키지를 설치하려면 경로에 Python이 있는 명령 프롬프트에서 다음 명령을 실행합니다.

    ```cmd
    pip install azure-eventhub
    ```

    Azure Blob storage를 사용 하 여 검사점 저장소로 이벤트를 수신 하기 위해이 패키지를 설치 합니다.

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>이벤트 보내기
이 섹션에서는 이전에 만든 이벤트 허브에 이벤트를 전송 하는 Python 스크립트를 만듭니다.

1. 선호하는 Python 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))를 엽니다.
2. **send.py**라는 스크립트를 만듭니다. 이 스크립트는 이전에 만든 이벤트 허브에 이벤트 일괄 처리를 보냅니다.
3. Send.py에 다음 코드를 붙여 넣습니다. 자세한 내용은 코드 주석을 참조 하십시오.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()

            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > 매우 유용한 주석이 있는 전체 소스 코드는 [GitHub에서이 파일](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py) 을 참조 하세요.

## <a name="receive-events"></a>이벤트 수신
이 빠른 시작에서는 Azure Blob Storage을 검사점 저장소로 사용 합니다. 검사점 저장소는 검사점을 유지 하는 데 사용 됩니다 (마지막 읽기 위치).  

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage 및 blob 컨테이너 만들기
다음 단계를 수행 하 여 blob 컨테이너에 Azure Storage 계정을 만듭니다.

1. [Azure Storage 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blob 컨테이너 만들기](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [저장소 계정에 대한 연결 문자열을 가져옵니다.](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    연결 문자열과 컨테이너 이름을 적어 둡니다. 수신 코드에서 사용 합니다.


### <a name="create-python-script-to-receive-events"></a>이벤트를 수신 하는 Python 스크립트 만들기

이 섹션에서는 이벤트 허브에서 이벤트를 수신 하는 Python 스크립트를 만듭니다.

1. 선호하는 Python 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))를 엽니다.
2. **recv.py**라는 스크립트를 만듭니다.
3. Recv.py에 다음 코드를 붙여 넣습니다. 자세한 내용은 코드 주석을 참조 하십시오.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # print the event data
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)

    async def main():
        # create an Azure blob checkpoint store to store the checkpoints
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > 매우 유용한 주석이 있는 전체 소스 코드는 [GitHub에서이 파일](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py) 을 참조 하세요.


### <a name="run-the-receiver-app"></a>받는 사람 앱 실행

이 스크립트를 실행하려면 해당 경로에 Python이 있는 명령 프롬프트를 열고 다음 명령을 실행합니다.

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>발신자 앱 실행

이 스크립트를 실행하려면 해당 경로에 Python이 있는 명령 프롬프트를 열고 다음 명령을 실행합니다.

```bash
python send.py
```

받는 사람 창에서 이벤트 허브로 전송 된 메시지가 표시 됩니다.


## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 이벤트를 비동기적으로 보내고 받습니다. 이벤트를 동기적으로 보내고 받는 방법에 대한 자세한 내용은 [이 위치](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples)에 있는 샘플을 참조 하세요.

[여기](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)에서 GitHub의 모든 샘플 (동기화와 비동기 모두)을 찾을 수 있습니다.
