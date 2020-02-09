---
title: Java를 사용 하 여 Azure Event Hubs에서 데이터 전송
description: 이 문서에서는 Azure Event Hubs에 이벤트를 전송 하는 Java 응용 프로그램을 만드는 연습을 제공 합니다.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18, seo-java-august2019, seo-java-september2019
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: be9919950f24dbee7fb8a3f901767c298105bf53
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72325464"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs"></a>Java를 사용 하 여 Azure에서 이벤트를 보내거나 이벤트를 수신 Event Hubs

이 자습서에서는 Azure Event Hubs에 이벤트를 보내거나 이벤트를 수신 하는 Java 응용 프로그램을 만드는 방법을 보여 줍니다.

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. 이벤트 허브로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 Event Hubs 개요 및 Event Hubs 기능을 참조 하세요.

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName` 문자열을 이벤트 허브 값으로 대체하고, 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

## <a name="prerequisites"></a>전제 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- Java 개발 환경. 이 자습서에서는 [Eclipse](https://www.eclipse.org/)를 사용합니다.
- **Event Hubs 네임 스페이스 및 이벤트 허브를 만듭니다**. 첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 애플리케이션에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스 및 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 따릅니다. 그런 다음 문서: [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)의 지침에 따라 이벤트 허브에 대한 액세스 키의 값을 가져옵니다. 액세스 키는 이 자습서의 뒷부분에서 작성하는 코드에 사용합니다. 기본 키 이름은 **RootManageSharedAccessKey**입니다.

## <a name="send-events"></a>이벤트 보내기 
이 섹션에서는 이벤트 허브로 이벤트를 전송 하는 Java 응용 프로그램을 만드는 방법을 보여 줍니다. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Azure Event Hubs 라이브러리에 대한 참조 추가

Event Hubs에 대한 Java 클라이언트 라이브러리는 [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)에서 Maven 프로젝트에 사용할 수 있습니다. Maven 프로젝트 파일 안에 다음 종속성 선언을 사용하여 이 라이브러리를 참조할 수 있습니다.

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

다양한 형식의 빌드 환경을 위해, [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)에서 최근에 릴리스된 JAR 파일을 명시적으로 가져올 수 있습니다.  

단순 이벤트 게시자의 경우 Event Hubs 클라이언트 클래스에 대한 *com.microsoft.azure.eventhubs* 패키지와 유틸리티 클래스(예: Azure Service Bus 메시징 클라이언트와 공유되는 일반적인 예외)에 대한 *com.microsoft.azure.servicebus* 패키지를 가져옵니다. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>이벤트 허브에 메시지를 전송하는 코드 작성

다음 샘플에서는 먼저 즐겨 찾는 Java 개발 환경에서 콘솔/셸 애플리케이션에 대한 새 Maven 프로젝트를 만듭니다. `SimpleSend` 클래스를 추가하고 해당 클래스에 다음 코드를 추가합니다.

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>연결 문자열 생성

ConnectionStringBuilder 클래스를 사용하여 Event Hubs 클라이언트 인스턴스로 전달하는 연결 문자열 값을 생성합니다. 자리 표시자를 네임스페이스 및 이벤트 허브를 만들 때 얻은 값으로 바꿉니다.

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>이벤트를 전송 하는 코드 작성

문자열을 UTF-8 바이트 인코딩으로 전환하여 단일 이벤트를 만듭니다. 그런 다음, 연결 문자열에서 새 Event Hubs 클라이언트 인스턴스를 만들고 메시지를 보냅니다.   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/SSL connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

프로그램을 빌드 및 실행하고, 오류가 없는지 확인합니다.

축하합니다. 이제 Event Hub에 메시지를 보냈습니다.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>부록: 메시지가 EventHub 파티션으로 라우팅되는 방식

소비자가 메시지를 검색할 수 있으려면 먼저 게시자가 파티션에 메시지를 게시해야 합니다. com.microsoft.azure.eventhubs.EventHubClient 개체에서 sendSync() 메서드를 사용하여 메시지를 이벤트 허브에 동기적으로 게시하면 파티션 키의 지정 여부에 따라 라운드 로빈 방식으로 메시지를 특정 파티션으로 보내거나 사용 가능한 모든 파티션에 분산할 수 있습니다.

파티션 키를 나타내는 문자열을 지정하면 이벤트로 보낼 파티션을 결정하기 위해 키가 해시됩니다.

파티션 키를 설정하지 않으면 사용 가능한 모든 파티션으로 메시지가 라운드 로빈됩니다.

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>이벤트 수신
이 자습서의 코드는 전체 작동 중인 애플리케이션을 확인하기 위해 검사할 수 있는 [GitHub의 EventProcessorSample 코드](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample)를 기반으로 합니다.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Java에서 EventProcessorHost를 사용하여 메시지 수신

**EventProcessorHost**는 영구적 검사점을 관리하여 Event Hubs의 이벤트 수신을 간소화하고 이러한 Event Hubs에서 병렬 수신하는 Java 클래스입니다. EventProcessorHost를 사용하면 다른 노드에 호스트된 경우라도 여러 수신기 간에 이벤트를 분할할 수 있습니다. 이 예제에서는 단일 수신기에 대해 EventProcessorHost를 사용하는 방법을 보여 줍니다.

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

EventProcessorHost를 사용 하려면 [Azure Storage 계정] [Azure Storage 계정]이 있어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 화면의 왼쪽에서 **리소스 만들기** 를 선택 합니다.
2. **저장소**를 선택 하 고 **저장소 계정**을 선택 합니다. **스토리지 계정 만들기** 창에서 스토리지 계정에 사용할 이름을 입력합니다. 나머지 필드를 완료 하 고 원하는 지역을 선택한 후 **만들기**를 선택 합니다.
   
    ![Azure Portal에서 저장소 계정 만들기](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. 새로 만든 저장소 계정을 선택 하 고 **액세스 키**를 선택 합니다.
   
    ![Azure Portal에서 액세스 키 가져오기](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Key1 값을 임시 위치로 복사합니다. 이 자습서의 뒷부분에서 사용합니다.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>EventProcessor 호스트를 사용하여 Java 프로젝트 만들기

이벤트 허브에 대한 Java 클라이언트 라이브러리는 [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)의 Maven 프로젝트에 사용할 수 있으며 Maven 프로젝트 파일 내의 다음 종속성 선언을 사용하여 참조할 수 있습니다. 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

다양한 형식의 빌드 환경을 위해, [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)에서 최근에 릴리스된 JAR 파일을 명시적으로 가져올 수 있습니다.

1. 다음 샘플에서는 먼저 즐겨 찾는 Java 개발 환경에서 콘솔/셸 애플리케이션에 대한 새 Maven 프로젝트를 만듭니다. 클래스는 `ErrorNotificationHandler`라고 합니다.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. 다음 코드를 사용하여 `EventProcessorSample`(이)라는 클래스를 새로 만듭니다. 자리 표시자를 이벤트 허브 및 스토리지 계정을 만들 때 사용한 값으로 바꿉니다.
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. 다음 코드를 사용하여 `EventProcessor`라는 클래스를 하나 이상 만듭니다.
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

이 자습서에서는 EventProcessorHost의 단일 인스턴스를 사용합니다. 처리량을 늘리려면 EventProcessorHost의 여러 인스턴스를 개별 머신에서 실행하는 것이 좋습니다.  그러면 중복성도 함께 제공됩니다. 이러한 경우 다양한 인스턴스가 자동으로 서로 조정하여 수신된 이벤트의 부하를 분산합니다. 여러 수신기가 각각 이벤트를 *모두* 처리하도록 하려면 **ConsumerGroup** 개념을 사용해야 합니다. 서로 다른 컴퓨터에서 이벤트를 수신하는 경우 EventProcessorHost 인스턴스의 이름을 해당 인스턴스가 배포된 컴퓨터 또는 역할을 기준으로 지정하면 유용할 수 있습니다.

### <a name="publishing-messages-to-eventhub"></a>EventHub에 메시지 게시

소비자가 메시지를 검색할 수 있으려면 먼저 게시자가 파티션에 메시지를 게시해야 합니다. com.microsoft.azure.eventhubs.EventHubClient 개체에서 sendSync() 메서드를 사용하여 메시지를 이벤트 허브에 동기적으로 게시하면 파티션 키의 지정 여부에 따라 라운드 로빈 방식으로 메시지를 특정 파티션으로 보내거나 사용 가능한 모든 파티션에 분산할 수 있다는 점이 중요합니다.

파티션 키를 나타내는 문자열이 지정되면 이벤트로 보낼 파티션을 결정하기 위해 키가 해시됩니다.

파티션 키를 설정하지 않으면 사용 가능한 모든 파티션으로 메시지가 라운드 로빈됩니다.

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>EventProcessorHost(EPH)에 대한 사용자 지정 CheckpointManager 구현

API는 기본 구현이 사용 사례와 호환되지 않는 시나리오를 위해 사용자 지정 검사점 관리자를 구현하는 메커니즘을 제공합니다.

기본 검사점 관리자는 Blob Storage를 사용하지만, EPH에서 사용하는 검사점 관리자를 사용자 고유의 구현으로 재정의하는 경우 원하는 스토리지를 사용하여 검사점 관리자 구현을 지원할 수 있습니다.

com.microsoft.azure.eventprocessorhost.ICheckpointManager 인터페이스를 구현하는 클래스를 만듭니다.

사용자 지정 검사점 관리자(com.microsoft.azure.eventprocessorhost.ICheckpointManager) 구현 사용

구현 내에서 기본 검사점 메커니즘을 재정의 하 고 사용자 고유의 데이터 저장소 (예: SQL Server, CosmosDB 및 Redis 용 Azure Cache)를 기반으로 사용자 고유의 검사점을 구현할 수 있습니다. 소비자 그룹의 이벤트를 처리하는 모든 EPH 인스턴스가 검사점 관리자 구현을 지원하는 데 사용된 저장소에 액세스할 수 있도록 하는 것이 좋습니다.

사용자 환경에 제공되는 모든 데이터 저장소를 사용할 수 있습니다.

com.microsoft.azure.eventprocessorhost.EventProcessorHost 클래스는 EventProcessorHost의 검사점 관리자를 재정의할 수 있는 2가지 생성자를 제공합니다.


## <a name="next-steps"></a>다음 단계
다음 문서를 읽어보세요. 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs의 기능 및 용어](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)

