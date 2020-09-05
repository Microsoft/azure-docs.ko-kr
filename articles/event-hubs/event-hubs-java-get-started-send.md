---
title: Java(최신)를 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기
description: 이 문서에서는 최신 azure-messaging-eventhubs 패키지를 사용하여 Azure Event Hubs와 이벤트를 주고 받는 Java 애플리케이션을 만드는 과정을 연습할 수 있습니다.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5ca03883cf7daa66e94fd78df9e03535fe6f51e6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934007"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Java를 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기(azure-messaging-eventhubs)
이 빠른 시작에서는 **azure-messaging-eventhubs** Java 패키지를 사용하여 이벤트 허브와 이벤트를 주고 받는 방법을 보여줍니다.

> [!IMPORTANT]
> 이 빠른 시작은 새로운 **azure-messaging-eventhubs** 패키지를 사용합니다. 이전 **azure-eventhubs** 및 **azure-eventhubs-eph** 패키지를 사용하는 빠른 시작은 [azure-eventhubs 및 azure-eventhubs-eph를 사용하여 이벤트 보내기 및 받기](event-hubs-java-get-started-send-legacy.md)를 참조하세요. 


## <a name="prerequisites"></a>사전 요구 사항
Azure Event Hubs를 처음 사용하는 경우 이 빠른 시작을 수행하기 전에 [Event Hubs 개요](event-hubs-about.md)를 참조하세요. 

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.

- **Microsoft Azure 구독**. Azure Event Hubs를 비롯한 Azure 서비스를 사용하려면 구독이 필요합니다.  기존 Azure 계정이 없는 경우 [평가판](https://azure.microsoft.com/free/)에 가입하거나 [계정을 만들 때](https://azure.microsoft.com) MSDN 구독자 혜택을 사용할 수 있습니다.
- Java 개발 환경. 이 빠른 시작에서는 [Eclipse](https://www.eclipse.org/)를 사용합니다. JDK(Java Development Kit) 버전 8 이상이 필요합니다. 
- **Event Hubs 네임스페이스 및 이벤트 허브 만들기** 첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 애플리케이션에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스 및 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 따릅니다. 그리고 다음 문서의 지침에 따라 **Event Hubs 네임스페이스에 대한 연결 문자열**을 가져옵니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 이 빠른 시작의 뒷부분에서 연결 문자열을 사용합니다.

## <a name="send-events"></a>이벤트 보내기 
이 섹션에서는 이벤트 허브로 이벤트를 전송하는 Java 애플리케이션을 만드는 방법을 보여줍니다. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Azure Event Hubs 라이브러리에 대한 참조 추가

Event Hubs용 Java 클라이언트 라이브러리는 [Maven Central 리포지토리](https://search.maven.org/search?q=a:azure-messaging-eventhubs)에서 사용할 수 있습니다. Maven 프로젝트 파일 안에 다음 종속성 선언을 사용하여 이 라이브러리를 참조할 수 있습니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>이벤트 허브에 메시지를 전송하는 코드 작성

다음 샘플에서는 먼저 즐겨 찾는 Java 개발 환경에서 콘솔/셸 애플리케이션에 대한 새 Maven 프로젝트를 만듭니다. `Sender` 클래스를 추가하고 해당 클래스에 다음 코드를 추가합니다.

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>연결 문자열 및 이벤트 허브
이 코드는 Event Hub 네임스페이스에 대한 연결 문자열과 이벤트 허브의 이름을 사용하여 Event Hubs 클라이언트를 빌드합니다. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Event Hubs 생산자 클라이언트 만들기 
이 코드는 이벤트 허브로 이벤트를 생성/전송하는 데 사용되는 생산자 클라이언트 개체를 만듭니다. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>이벤트의 일괄 처리 준비
이 코드는 이벤트의 일괄 처리를 준비합니다. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>이벤트 허브로 이벤트 일괄 처리를 보냅니다.
이 코드는 이전 단계에서 준비한 이벤트 일괄 처리를 이벤트 허브로 보냅니다. 다음 코드는 보내기 작업에서 차단됩니다. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>닫기 및 정리
이 코드는 생산자를 닫습니다. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>이벤트를 보내기 위한 전체 코드
이벤트 허브에 이벤트를 전송하는 전체 코드는 다음과 같습니다. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

프로그램을 빌드하고 오류가 없는지 확인합니다. 이 프로그램은 수신자 프로그램을 실행한 후에 실행됩니다. 

## <a name="receive-events"></a>이벤트 수신
이 자습서의 코드는 전체 작동 중인 애플리케이션을 검사할 수 있는 [GitHub의 EventProcessorClient 샘플](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java)을 기반으로 합니다.

> [!NOTE]
> Azure Stack Hub에서 실행 중인 경우 해당 플랫폼은 Azure에서 일반적으로 사용할 수 있는 것과 다른 버전의 Storage Blob SDK를 지원할 수 있습니다. 예를 들어 [Azure Stack Hub 버전 2002](/azure-stack/user/event-hubs-overview)에서 실행 중인 경우 스토리지 서비스에 사용할 수 있는 가장 높은 버전은 2017-11-09입니다. 이 경우 이 섹션의 다음 단계 외에도 스토리지 서비스 API 버전 2017-11-09를 대상으로 하는 코드를 추가해야 합니다. 특정 Storage API 버전을 대상으로 지정하는 방법에 대한 예제는 [GitHub에 대한 이 샘플](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)을 참조하세요. Azure Stack Hub에서 지원되는 Azure Storage 서비스 버전에 대한 자세한 내용은 [Azure Stack Hub스토리지: 차이점 및 고려 사항](/azure-stack/user/azure-stack-acs-differences)을 참조하세요.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage 및 BLOB 컨테이너 만들기
이 빠른 시작에서는 Azure Storage(특히 Blob Storage)를 검사점 저장소로 사용합니다. 검사점은 이벤트 프로세서가 파티션 내에서 마지막으로 처리된 이벤트의 위치를 표시하거나 커밋하는 프로세스입니다. 검사점 표시는 일반적으로 이벤트를 처리하는 함수 내에서 수행됩니다. 검사점에 대한 자세한 내용은 [이벤트 프로세서](event-processor-balance-partition-load.md)를 참조하세요.

다음 단계에 따라 Azure Storage 계정을 만듭니다. 

1. [Azure Storage 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blob 컨테이너 만들기](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [스토리지 계정에 대한 연결 문자열 가져오기](../storage/common/storage-configure-connection-string.md)

    **연결 문자열**과 **컨테이너 이름**을 적어 둡니다. 수신 코드에서 사용합니다. 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Java 프로젝트에 Event Hubs 라이브러리 추가
pom.xml 파일에 다음 종속성을 추가합니다. 

- [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.1.1</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.1.1</version>
    </dependency>
</dependencies>
```

1. Java 파일 맨 위에 다음 **import** 문을 추가합니다. 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    import java.util.concurrent.TimeUnit;
    ```
2. `Receiver`라는 클래스를 만들고 다음 문자열 변수를 클래스에 추가합니다. 자리 표시자를 올바른 값으로 바꿉니다. 
    ```java
    private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
    private static final String eventHubName = "<EVENT HUB NAME>";
    private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
    private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    ```
3. 다음 `main` 메서드를 클래스에 추가합니다. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(STORAGE_CONNECTION_STRING) 
            .containerName(STORAGE_CONTAINER_NAME) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. 이벤트 및 오류를 처리하는 두 개의 도우미 메서드(`PARTITION_PROCESSOR` 및 `ERROR_HANDLER`)를 `Receiver` 클래스에 추가합니다. 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. 전체 코드는 다음과 같아야 합니다. 

    ```java

    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    import java.util.concurrent.TimeUnit;
    
    public class Receiver {
        
        private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
        private static final String eventHubName = "<EVENT HUB NAME>";
        private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
        private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    
        public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

            if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
                eventContext.updateCheckpoint();
            }
        };
        
        public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
            System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
                errorContext.getPartitionContext().getPartitionId(),
                errorContext.getThrowable());
        };
        
        public static void main(String[] args) throws Exception {
            BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
                .connectionString(STORAGE_CONNECTION_STRING)
                .containerName(STORAGE_CONTAINER_NAME)
                .buildAsyncClient();
    
            EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
                .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName)
                .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                .processEvent(PARTITION_PROCESSOR)
                .processError(ERROR_HANDLER)
                .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient));
    
            EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

            System.out.println("Starting event processor");
            eventProcessorClient.start();
        
            System.out.println("Press enter to stop.");
            System.in.read();
        
            System.out.println("Stopping event processor");
            eventProcessorClient.stop();
            System.out.println("Event processor stopped.");
        
            System.out.println("Exiting process");
        }
        
    }
    ```
3. 프로그램을 빌드하고 오류가 없는지 확인합니다. 

## <a name="run-the-applications"></a>애플리케이션 실행
1. **받는 사람** 애플리케이션 목록을 실행합니다.
1. 그런 다음, **보낸 사람** 애플리케이션을 실행합니다. 
1. **받는 사람** 애플리케이션 창에서 보낸 사람 애플리케이션에 의해 게시된 이벤트를 확인합니다.
1. 받는 사람 애플리케이션 창에서 **ENTER** 키를 눌러 애플리케이션을 중지합니다. 

## <a name="next-steps"></a>다음 단계
GitHub에서 다음 샘플을 참조하세요.

- [azure-messaging-eventhubs samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).  
