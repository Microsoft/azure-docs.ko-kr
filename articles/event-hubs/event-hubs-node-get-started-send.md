---
title: Node.js(레거시)를 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기
description: 이 문서에서는 이전 azure/event-hubs 버전 2 패키지를 사용하여 Azure Event Hubs와 이벤트를 주고 받는 Node.js 애플리케이션을 만드는 과정을 연습할 수 있습니다.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 9aa2418657c2d3bcab9ef8883e5bd57422ce5e29
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899900"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs-azureevent-hubs-version-2"></a>빠른 시작: Node.js(@azure/event-hubs 버전 2)를 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에서는 이벤트 허브와 이벤트를 주고 받는 Node.js 애플리케이션을 작성하는 방법을 설명합니다.

> [!WARNING]
> 이 빠른 시작은 Azure Event Hubs Java Script SDK 버전 2를 위한 것입니다. 코드를 [Java Script SDK 버전 5](get-started-node-send-v2.md)로 [마이그레이션](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)하는 것이 좋습니다. 



## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- Node.js 버전 8.x 이상. [https://nodejs.org](https://nodejs.org)에서 최신 LTS 버전을 다운로드하세요.
- Visual Studio Code(권장) 또는 다른 IDE
- **Event Hubs 네임스페이스 및 이벤트 허브 만들기** 첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 애플리케이션에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스와 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 수행한 다음, 이 자습서의 다음 단계를 계속 진행하세요. 그리고 다음 문서의 지침에 따라 이벤트 허브 네임스페이스에 대한 연결 문자열을 가져옵니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 해당 연결 문자열은 이 자습서의 뒷부분에서 사용합니다.


### <a name="install-npm-package"></a>npm 패키지 설치
[Event Hubs용 npm 패키지](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)를 설치하려면 경로에 `npm`이 있는 명령 프롬프트를 열고, 샘플을 저장하고 이 명령을 실행할 폴더로 디렉터리를 변경합니다.

```shell
npm install @azure/event-hubs@2
```

[이벤트 프로세서 호스트용 npm 패키지](https://www.npmjs.com/package/@azure/event-processor-host)를 설치하려면 아래 명령을 대신 실행합니다.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>이벤트 보내기

이 섹션에서는 이벤트 허브로 이벤트를 전송하는 Node.js 애플리케이션을 만드는 방법을 보여줍니다. 

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName` 문자열을 이벤트 허브 값으로 대체하고, 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com))를 엽니다. 
2. `send.js`라는 파일을 만들고 아래 코드를 이 파일에 붙여넣습니다. 문서의 지침에 따라 이벤트 허브 네임스페이스에 대한 연결 문자열을 가져옵니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 위의 코드에 있는 연결 문자열 및 이벤트 허브 이름을 입력합니다.
4. 그런 다음, 명령 프롬프트에서 `node send.js` 명령을 실행하여 이 파일을 실행합니다. 그러면 이벤트 허브에 100개 이벤트가 전송됩니다.

축하합니다! 이벤트 허브에 메시지를 보냈습니다.


## <a name="receive-events"></a>이벤트 수신

이 섹션에서는 이벤트 허브에 있는 기본 소비자 그룹의 단일 파티션에서 이벤트를 수신하는 Node.js 애플리케이션을 만드는 방법을 보여줍니다. 

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com))를 엽니다. 
2. `receive.js`라는 파일을 만들고 아래 코드를 이 파일에 붙여넣습니다.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 위의 코드에 있는 연결 문자열 및 이벤트 허브 이름을 입력합니다.
4. 그런 다음, 명령 프롬프트에서 `node receive.js` 명령을 실행하여 이 파일을 실행합니다. 그러면 이벤트 허브에 있는 기본 소비자 그룹의 파티션 중 하나에서 이벤트가 수신됩니다.

축하합니다! 이벤트 허브에서 이벤트를 받았습니다.

## <a name="receive-events-using-event-processor-host"></a>이벤트 프로세서 호스트를 사용하여 이벤트 수신

이 섹션에서는 Node.js 애플리케이션에서 Azure [EventProcessorHost](event-hubs-event-processor-host.md)를 사용하여 이벤트 허브에서 이벤트를 수신하는 방법을 설명합니다. EPH(EventProcessorHost)를 사용하면 이벤트 허브의 소비자 그룹 내 모든 파티션에서 수신기를 만들어 이벤트 허브에서 이벤트를 효율적으로 수신할 수 있습니다. EPH는 Azure Storage Blob에서 일정한 간격으로 수신된 메시지의 메타데이터에 검사점을 적용합니다. 이러한 방식이 사용되므로 메시지 수신이 중지된 이후 중지된 시점부터 계속해서 쉽게 메시지를 수신할 수 있습니다.

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com))를 엽니다. 
2. `receiveAll.js`라는 파일을 만들고 아래 코드를 이 파일에 붙여넣습니다.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Azure Blob Storage에 대한 연결 문자열과 함께 위의 코드에 있는 연결 문자열 및 이벤트 허브 이름을 입력합니다.
4. 그런 다음, 명령 프롬프트에서 `node receiveAll.js` 명령을 실행하여 이 파일을 실행합니다.

축하합니다! 이벤트 프로세서 호스트를 사용하여 이벤트 허브에서 메시지를 받았습니다. 이제 이벤트 허브에 있는 기본 소비자 그룹의 모든 파티션에서 이벤트가 수신됩니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 읽어보세요.

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs의 기능 및 용어](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)
- GitHub에서 [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) 및 [이벤트 프로세서 호스트](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples)에 대한 다른 Node.js 샘플을 확인하세요.
