---
title: Java(최신)를 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기
description: 이 문서에서는 최신 azure-messaging-eventhubs 패키지를 사용하여 Azure Event Hubs와 이벤트를 주고 받는 Java 애플리케이션을 만드는 과정을 연습할 수 있습니다.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: d9d22374868f3befd659918c532f339d49ba1642
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77032051"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Java를 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기(azure-messaging-eventhubs)
이 빠른 시작에서는 Azure Event Hubs와 이벤트를 주고 받는 Java 애플리케이션을 만드는 방법을 보여줍니다. 

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

> [!IMPORTANT]
> 이 빠른 시작은 새로운 **azure-messaging-eventhubs** 패키지를 사용합니다. 이전 **azure-eventhubs** 및 **azure-eventhubs-eph** 패키지를 사용하는 빠른 시작은 [이 문서](event-hubs-java-get-started-send.md)를 참조하세요. 


## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Java 개발 환경. 이 자습서에서는 [Eclipse](https://www.eclipse.org/)를 사용합니다. JDK(Java Development Kit) 버전 8 이상이 필요합니다. 
- **Event Hubs 네임스페이스 및 이벤트 허브 만들기** 첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 애플리케이션에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스 및 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 따릅니다. 그런 다음, 다음 문서의 지침에 따라 이벤트 허브에 대한 액세스 키의 값을 가져옵니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 액세스 키는 이 자습서의 뒷부분에서 작성하는 코드에 사용합니다. 기본 키 이름은 **RootManageSharedAccessKey**입니다.

## <a name="send-events"></a>이벤트 보내기 
이 섹션에서는 이벤트 허브로 이벤트를 전송하는 Java 애플리케이션을 만드는 방법을 보여줍니다. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Azure Event Hubs 라이브러리에 대한 참조 추가

Event Hubs에 대한 Java 클라이언트 라이브러리는 [Maven 중앙 리포지토리](https://search.maven.org/search?q=a:azure-messaging-eventhubs)에서 Maven 프로젝트에 사용할 수 있습니다. Maven 프로젝트 파일 안에 다음 종속성 선언을 사용하여 이 라이브러리를 참조할 수 있습니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>이벤트 허브에 메시지를 전송하는 코드 작성

다음 샘플에서는 먼저 즐겨 찾는 Java 개발 환경에서 콘솔/셸 애플리케이션에 대한 새 Maven 프로젝트를 만듭니다. `SimpleSend` 클래스를 추가하고 해당 클래스에 다음 코드를 추가합니다.

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
    .buildProducer();
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
            .buildProducer();

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
이 자습서의 코드는 전체 작동 중인 애플리케이션을 검사할 수 있는 [GitHub의 EventProcessorClient 샘플](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java)을 기반으로 합니다.

### <a name="create-a-java-project"></a>Java 프로젝트 만들기

Event Hubs에 대한 Java 클라이언트 라이브러리는 [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)의 Maven 프로젝트에 사용할 수 있으며 Maven 프로젝트 파일 내의 다음 종속성 선언을 사용하여 참조할 수 있습니다. 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. 다음 코드를 사용하여 `Receiver`(이)라는 클래스를 새로 만듭니다. 자리 표시자를 이벤트 허브 및 스토리지 계정을 만들 때 사용한 값으로 바꿉니다.
   
   ```java
    import com.azure.messaging.eventhubs.*;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;

    public class Receiver {

        private static final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        private static final String eventHubName = "EVENT HUB NAME";
    
        public static void main(String[] args) throws Exception {

            // function to process events
            Consumer<EventContext> processEvent = eventContext  -> {
                System.out.print("Received event: ");
                // print the body of the event
                System.out.println(eventContext.getEventData().getBodyAsString());
                eventContext.updateCheckpoint();
            };

            // function to process errors
            Consumer<ErrorContext> processError = errorContext -> {
                // print the error message
                System.out.println(errorContext.getThrowable().getMessage());
            };

            EventProcessorBuilder eventProcessorBuilder = new EventProcessorBuilder()
                .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                .connectionString(connectionString, eventHubName)
                .processEvent(processEvent)
                .processError(processError)
                .checkpointStore(new InMemoryCheckpointStore());
        
            EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();
            System.out.println("Starting event processor");
            eventProcessorClient.start();

            System.out.println("Press enter to stop.");
            System.in.read();

            System.out.println("Stopping event processor");
            eventProcessor.stop();
            System.out.println("Event processor stopped.");
    
            System.out.println("Exiting process");
        }
    }
    ```
    
2. [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)에서 **InMemoryCheckpointStore.java** 파일을 다운로드하여 프로젝트에 추가합니다. 
3. 프로그램을 빌드하고 오류가 없는지 확인합니다. 

## <a name="run-the-applications"></a>애플리케이션 실행
1. **받는 사람** 애플리케이션 목록을 실행합니다.
1. 그런 다음, **보낸 사람** 애플리케이션을 실행합니다. 
1. **받는 사람** 애플리케이션 창에서 보낸 사람 애플리케이션에 의해 게시된 이벤트를 확인합니다.
1. 받는 사람 애플리케이션 창에서 **ENTER** 키를 눌러 애플리케이션을 중지합니다. 

## <a name="next-steps"></a>다음 단계
[GitHub의 Java SDK 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)을 확인합니다.

