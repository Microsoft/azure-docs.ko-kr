---
title: JavaScript(최신)를 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기
description: 이 문서에서는 최신 azure/event-hubs 버전 5 패키지를 사용하여 Azure Event Hubs와 이벤트를 주고 받는 JavaScript 애플리케이션을 만드는 과정을 연습할 수 있습니다.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 6f1fe30cad3a1bc0d5bce7a885650dd84fcb2215
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933990"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs-version-5"></a>JavaScript(azure/event-hubs 버전 5)를 사용하여 이벤트 허브에서 이벤트 보내기 또는 받기
이 빠른 시작에서는 **azure/event-hubs 버전 5** JavaScript 패키지를 사용하여 이벤트 허브와 이벤트를 주고 받는 방법을 보여줍니다. 


## <a name="prerequisites"></a>사전 요구 사항
Azure Event Hubs를 처음 사용하는 경우 이 빠른 시작을 수행하기 전에 [Event Hubs 개요](event-hubs-about.md)를 참조하세요. 

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.

- **Microsoft Azure 구독**. Azure Event Hubs를 비롯한 Azure 서비스를 사용하려면 구독이 필요합니다.  기존 Azure 계정이 없는 경우 [평가판](https://azure.microsoft.com/free/)에 가입하거나 [계정을 만들 때](https://azure.microsoft.com) MSDN 구독자 혜택을 사용할 수 있습니다.
- Node.js 버전 8.x 이상. 최신 [LTS(장기 지원) 버전](https://nodejs.org)을 다운로드하세요.  
- Visual Studio Code(권장) 또는 다른 IDE(통합 개발 환경).  
- 활성 Event Hubs 네임스페이스 및 이벤트 허브. 만드는 방법은 다음과 같습니다. 

   1. [Azure Portal](https://portal.azure.com)에서 *Event Hubs* 형식의 네임스페이스를 만들고, 애플리케이션에서 이벤트 허브와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 
   1. 네임스페이스 및 이벤트 허브를 만들려면 [빠른 시작: Azure Portal을 사용하여 이벤트 허브 만들기](event-hubs-create.md)의 지침을 수행합니다.
   1. 이 빠른 시작의 지침에 따라 계속 진행합니다. 
   1. 이벤트 허브 네임스페이스에 대한 연결 문자열을 가져오려면 [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)의 지침을 따릅니다. 이 빠른 시작의 뒷부분에서 사용할 수 있도록 연결 문자열을 기록해 둡니다.
- **Event Hubs 네임스페이스 및 이벤트 허브 만들기** 첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 애플리케이션에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스 및 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 따릅니다. 그리고 다음 문서의 지침에 따라 **Event Hubs 네임스페이스에 대한 연결 문자열**을 가져옵니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 이 빠른 시작의 뒷부분에서 연결 문자열을 사용합니다.

### <a name="install-the-npm-package"></a>npm 패키지 설치
[Event Hubs용 npm(Node 패키지 관리자) 패키지](https://www.npmjs.com/package/@azure/event-hubs)를 설치하려면 경로에 *npm*이 있는 명령 프롬프트를 열고, 샘플을 저장하려는 폴더로 디렉터리를 변경하고, 다음 명령을 실행합니다.

```shell
npm install @azure/event-hubs
```

받는 쪽의 경우 패키지 두 개를 더 설치해야 합니다. 이 빠른 시작에서는 프로그램이 이미 읽은 이벤트를 다시 읽지 않도록 Azure Blob 스토리지를 사용하여 검사점을 유지합니다. 이 스토리지는 일정한 간격으로 BLOB에서 수신된 메시지의 메타데이터에 검사점을 적용합니다. 이러한 방식이 사용되므로 메시지 수신이 중지된 이후 중지된 시점부터 계속해서 쉽게 메시지를 수신할 수 있습니다.

다음 명령을 실행합니다.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>이벤트 보내기

이 섹션에서는 이벤트 허브로 이벤트를 보내는 JavaScript 애플리케이션을 만듭니다.

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com))를 엽니다.
1. *send.js*라는 파일을 만들고, 그 안에 다음 코드를 붙여넣습니다.

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. 코드에서 다음 항목을 실제 값으로 바꿉니다.
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. `node send.js`를 실행하여 이 파일을 실행합니다. 이 명령은 세 개 이벤트의 일괄 처리를 이벤트 허브에 보냅니다.
1. Azure Portal에서 이벤트 허브가 메시지를 받았는지 확인합니다. **메트릭** 섹션에서 **메시지** 보기로 전환합니다. 페이지를 새로 고쳐 차트를 업데이트합니다. 메시지가 수신되었다는 내용이 표시될 때까지 몇 초 정도 걸릴 수 있습니다.

    [![이벤트 허브에서 메시지를 수신했는지 확인](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > 정보 제공을 위한 주석을 비롯한 전체 소스 코드는 [GitHub sendEvents.js 페이지](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)로 이동하세요.

축하합니다! 이벤트 허브에 메시지를 보냈습니다.


## <a name="receive-events"></a>이벤트 수신
이 섹션에서는 JavaScript 애플리케이션에서 Azure Blob 스토리지 검사점 저장소를 사용하여 이벤트 허브에서 이벤트를 수신합니다. 이 스토리지는 Azure Storage BLOB에서 일정한 간격으로 수신된 메시지의 메타데이터에 검사점을 적용합니다. 이러한 방식이 사용되므로 메시지 수신이 중지된 이후 중지된 시점부터 계속해서 쉽게 메시지를 수신할 수 있습니다.

> [!NOTE]
> Azure Stack Hub에서 실행 중인 경우 해당 플랫폼은 Azure에서 일반적으로 사용할 수 있는 것과 다른 버전의 Storage Blob SDK를 지원할 수 있습니다. 예를 들어 [Azure Stack Hub 버전 2002](/azure-stack/user/event-hubs-overview)에서 실행 중인 경우 스토리지 서비스에 사용할 수 있는 가장 높은 버전은 2017-11-09입니다. 이 경우 이 섹션의 다음 단계 외에도 스토리지 서비스 API 버전 2017-11-09를 대상으로 하는 코드를 추가해야 합니다. 특정 Storage API 버전을 대상으로 지정하는 방법에 대한 예제는 GitHub의 [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) 및 [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 샘플을 참조하세요. Azure Stack Hub에서 지원되는 Azure Storage 서비스 버전에 대한 자세한 내용은 [Azure Stack Hub스토리지: 차이점 및 고려 사항](/azure-stack/user/azure-stack-acs-differences)을 참조하세요.


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Azure 스토리지 계정 및 BLOB 컨테이너 만들기
Azure 스토리지 계정 및 BLOB 컨테이너를 만들려면 다음 단계를 수행합니다.

1. [Azure 스토리지 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [스토리지 계정에서 BLOB 컨테이너 만들기](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [스토리지 계정에 대한 연결 문자열 가져오기](../storage/common/storage-configure-connection-string.md)

나중에 수신 코드에 사용할 수 있도록 연결 문자열과 컨테이너 이름을 기록해 두어야 합니다.

### <a name="write-code-to-receive-events"></a>이벤트를 받는 코드 작성

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com))를 엽니다.
1. *receive.js*라는 파일을 만들고, 그 안에 다음 코드를 붙여넣습니다.

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
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
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
1. 이 코드에서 다음 값을 실제 값으로 바꿉니다.
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. 명령 프롬프트에서 `node receive.js` 명령을 실행하여 이 파일을 실행합니다. 창에는 수신된 이벤트에 대한 메시지가 표시됩니다.

    > [!NOTE]
    > 정보 제공을 위한 주석을 비롯한 전체 소스 코드는 [GitHub receiveEventsUsingCheckpointStore.js page 페이지](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsUsingCheckpointStore.js)로 이동하세요.

축하합니다! 이벤트 허브에서 이벤트를 받았습니다. 이제 수신기 프로그램은 이벤트 허브에 있는 기본 소비자 그룹의 모든 파티션에서 이벤트를 수신합니다.

## <a name="next-steps"></a>다음 단계
GitHub에서 다음 샘플을 확인합니다.

- [JavaScript 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
