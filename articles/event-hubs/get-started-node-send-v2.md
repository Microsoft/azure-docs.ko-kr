---
title: Node.js를 사용하여 이벤트 보내기 및 받기 - Azure Event Hubs
description: 이 문서에서는 Azure Event Hubs에서 이벤트를 보내는 Node.js 애플리케이션을 만드는 연습을 제공합니다.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: d4810c325acc42d5aa665002654cb01154cdc6bb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981625"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Node.js를 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. 이벤트 허브로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에서는 이벤트 허브와 이벤트를 주고 받는 Node.js 애플리케이션을 작성하는 방법을 설명합니다.

> [!IMPORTANT]
> 이 빠른 시작에서는 Azure Event Hubs Java 스크립트 SDK 버전 5를 사용 합니다. Java 스크립트 SDK의 이전 버전 2를 사용 하는 빠른 시작은 [이 문서](event-hubs-node-get-started-send.md)를 참조 하세요. SDK 버전 2를 사용 하는 경우 코드를 최신 버전으로 마이그레이션하는 것이 좋습니다. 자세한 내용은 [마이그레이션 가이드](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- Node.js 버전 8.x 이상. [https://nodejs.org](https://nodejs.org)에서 최신 LTS 버전을 다운로드하세요.
- Visual Studio Code(권장) 또는 다른 IDE
- **Event Hubs 네임스페이스 및 이벤트 허브 만들기** 첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 애플리케이션에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스와 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 수행한 다음, 이 자습서의 다음 단계를 계속 진행하세요. 그런 다음 문서: [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)의 지침에 따라 이벤트 허브 네임 스페이스에 대한 연결 문자열을 가져옵니다. 해당 연결 문자열은 이 자습서의 뒷부분에서 사용합니다.


### <a name="install-npm-packages"></a>npm 패키지 설치
[Event Hubs용 npm 패키지](https://www.npmjs.com/package/@azure/event-hubs)를 설치하려면 경로에 `npm`이 있는 명령 프롬프트를 열고, 샘플을 저장하고 이 명령을 실행할 폴더로 디렉터리를 변경합니다.

```shell
npm install @azure/event-hubs
```

받는 쪽의 경우 두 개 이상의 패키지를 설치 해야 합니다. 이 빠른 시작에서는 프로그램이 이미 읽은 이벤트를 읽지 않도록 검사점을 유지 하기 위해 Azure Blob Storage를 사용 합니다. 받은 메시지의 검사점 메타 데이터는 blob에서 일정 한 간격으로 수신 됩니다. 이러한 방식이 사용되므로 메시지 수신이 중지된 이후 중지된 시점부터 계속해서 쉽게 메시지를 수신할 수 있습니다.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>이벤트 보내기

이 섹션에서는 이벤트 허브로 이벤트를 전송하는 Node.js 애플리케이션을 만드는 방법을 보여줍니다.

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com))를 엽니다.
2. `send.js` 라는 파일을 만들고 다음 코드를 붙여 넣습니다.

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // send the batch to the event hub
      await producer.sendBatch(batch);

      // close the producer client
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 코드에서 **연결 문자열과** **이벤트 허브 이름** 값을 바꾸어야 합니다.
5. 명령 `node send.js`를 실행 하 여이 파일을 실행 합니다. 그러면 이벤트 허브로 세 개의 이벤트 일괄 처리를 전송 합니다.
6. Azure Portal에서 이벤트 허브가 메시지를 받았는지 확인할 수 있습니다. **메트릭** 섹션에서 **메시지** 뷰로 전환 합니다. 페이지를 새로 고쳐 차트를 업데이트 합니다. 메시지가 수신 되었음을 표시 하는 데 몇 초 정도 걸릴 수 있습니다.

    [이벤트 허브에서 메시지를 받았는지 확인 ![](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > 추가 정보 주석을 포함 하는 전체 소스 코드는 [GitHub에서이 파일](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js) 을 참조 하세요.

축하합니다! 이벤트 허브에 메시지를 보냈습니다.


## <a name="receive-events"></a>이벤트 수신
이 섹션에서는 node.js 응용 프로그램에서 Azure Blob checkpoint 저장소를 사용 하 여 이벤트 허브에서 이벤트를 수신 하는 방법을 보여 줍니다. EPH는 Azure Storage Blob에서 일정한 간격으로 수신된 메시지의 메타데이터에 검사점을 적용합니다. 이러한 방식이 사용되므로 메시지 수신이 중지된 이후 중지된 시점부터 계속해서 쉽게 메시지를 수신할 수 있습니다.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage 및 blob 컨테이너 만들기
다음 단계를 수행 하 여 blob 컨테이너에 Azure Storage 계정을 만듭니다.

1. [Azure Storage 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blob 컨테이너 만들기](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [저장소 계정에 대한 연결 문자열을 가져옵니다.](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    연결 문자열과 컨테이너 이름을 적어 둡니다. 수신 코드에서 사용 합니다.

### <a name="write-code-to-receive-events"></a>이벤트를 수신 하는 코드 작성

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com))를 엽니다.
2. `receive.js` 라는 파일을 만들고 다음 코드를 붙여 넣습니다. 자세한 내용은 코드 주석을 참조 하세요.
    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // subscribe for the events and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // after 30 seconds, stop processing
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
3. 코드에서 **다음 값** 을 지정 해야 합니다.
    - Event Hubs 네임 스페이스에 대한 연결 문자열
    - 이벤트 허브의 이름입니다.
    - Azure Storage 계정에 대한 연결 문자열
    - Blob 컨테이너의 이름입니다.
5. 그런 다음, 명령 프롬프트에서 `node receive.js` 명령을 실행하여 이 파일을 실행합니다. 수신 된 이벤트에 대한 메시지가 창에 표시 됩니다.

    > [!NOTE]
    > 추가 정보 주석을 포함 하는 전체 소스 코드는 [GitHub에서이 파일](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js)을 참조 하세요.

축하합니다! 이벤트 허브에서 이벤트를 받았습니다. 수신기 프로그램은 이벤트 허브에 있는 기본 소비자 그룹의 모든 파티션에서 이벤트를 수신 합니다.

## <a name="next-steps"></a>다음 단계
GitHub에서 다음 샘플을 확인 하세요.

- [JavaScript 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
