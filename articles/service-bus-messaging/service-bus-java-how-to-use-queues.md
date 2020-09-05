---
title: Java에서 Azure Service Bus 큐 사용
description: 이 자습서에서는 Azure Service Bus 큐와 메시지를 주고 받는 Java 애플리케이션을 만드는 방법에 대해 알아봅니다.
ms.devlang: Java
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5447bea686db48157c721978f5510cd80e0924c6
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88065797"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>빠른 시작: Java에서 Azure Service Bus 큐를 사용하여 메시지 보내기 및 받기

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
이 자습서에서는 Azure Service Bus 큐와 메시지를 주고 받는 Java 애플리케이션을 만드는 방법에 대해 알아봅니다. 

> [!NOTE]
> Java 샘플은 GitHub의 [azure-service-bus 리포지토리](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)에 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
1. Azure 구독 이 자습서를 완료하려면 Azure 계정이 필요합니다. [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
2. 작업할 큐가 없는 경우 [Azure Portal을 사용하여 Service Bus 큐 만들기](service-bus-quickstart-portal.md) 문서의 단계에 따라 큐를 만듭니다.
    1. Service Bus **큐**의 빠른 **개요**를 읽습니다. 
    2. Service Bus **네임스페이스**를 만듭니다. 
    3. **연결 문자열**을 가져옵니다.
    4. Service Bus **큐**를 만듭니다.
3. [Java용 Azure SDK][Azure SDK for Java]를 설치합니다. 


## <a name="configure-your-application-to-use-service-bus"></a>Service Bus를 사용하도록 애플리케이션 구성
이 샘플을 빌드하기 전에 [Java용 Azure SDK][Azure SDK for Java]를 설치했는지 확인하세요. 

Eclipse를 사용하는 경우 Azure SDK for Java를 포함하고 있는 [Eclipse용 Azure 도구 키트][Azure Toolkit for Eclipse]를 설치할 수 있습니다. 그런 다음, **Java용 Microsoft Azure 라이브러리**를 프로젝트에 추가할 수 있습니다. IntelliJ를 사용하는 경우 [IntelliJ용 Azure 도구 키트 설치](/azure/developer/java/toolkit-for-intellij/installation)를 참조하세요. 

![Java용 Microsoft Azure 라이브러리를 Eclipse 프로젝트에 추가합니다.](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Java 파일 맨 위에 다음 `import` 문을 추가합니다.

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기
Service Bus 큐에 메시지를 보내기 위해 애플리케이션은 **QueueClient** 개체를 인스턴스화하고 메시지를 비동기식으로 전송합니다. 다음 코드는 포털을 통해 만든 큐에 메시지를 보내는 방법을 보여 줍니다.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

Service Bus 큐로 보내고 받은 메시지는 [Message](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) 클래스 인스턴스입니다. Message 개체에는 표준 속성 집합(예: Label 및 TimeToLive), 애플리케이션별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 임의 애플리케이션 데이터 본문이 있습니다. 애플리케이션은 Message의 생성자에 직렬화 가능 개체를 전달하여 메시지 본문을 설정할 수 있으며, 그러면 적절한 직렬 변환기가 개체를 직렬화하는 데 사용됩니다. 또는 **java.IO.InputStream** 개체를 제공할 수 있습니다.


Service Bus 큐는 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 애플리케이션 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다.

## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기
큐에서 메시지를 받는 기본 방법은 **ServiceBusContract** 개체를 사용하는 것입니다. 받은 메시지는 **ReceiveAndDelete** 및 **PeekLock**의 두 가지 모드에서 작동할 수 있습니다.

**ReceiveAndDelete** 모드를 사용하는 경우 수신은 1단계 작업입니다. 즉, Service Bus가 큐 메시지에 대한 읽기 요청을 받으면 메시지를 이용되는 것으로 표시하고 애플리케이션에 반환합니다. **ReceiveAndDelete** 모드(기본 모드임)는 가장 단순한 모델이며, 애플리케이션이 실패 이벤트 시 메시지를 처리하지 않아도 안전한 시나리오에서 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요.
Service Bus가 메시지를 사용되는 것으로 표시했기 때문에 애플리케이션이 다시 시작되고 메시지를 다시 사용하기 시작할 때 충돌 전에 사용한 메시지는 누락됩니다.

**PeekLock** 모드에서 수신은 2단계 작업이므로 메시지 누락이 허용되지 않는 애플리케이션을 지원할 수 있습니다. Service Bus는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 애플리케이션에 반환합니다. 애플리케이션은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 수신된 메시지에 대해 **complete()** 를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. Service Bus는 **complete()** 호출을 확인한 후 메시지를 사용 중인 항목으로 표시하고 큐에서 제거합니다. 

다음 예제에서는 **PeekLock** 모드(기본 모드가 아님)를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 아래 예제에서는 등록된 메시지 처리기와 함께 콜백 모델을 사용하고 `TestQueue`에 도착할 때 메시지를 처리합니다. 이 모드는 콜백이 정상적으로 반환될 때 자동으로 **complete()** 를 호출하고 콜백이 예외를 throw하면 **abandon()** 을 호출합니다. 

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
        // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>애플리케이션 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법
Service Bus는 애플리케이션 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신기 애플리케이션이 메시지를 처리할 수 없는 경우 **getLockToken()** 을 통해 받은 메시지의 잠금 토큰을 사용하여 클라이언트 개체에서 **abandon()** 메서드를 호출할 수 있습니다. 그러면 Service Bus에서 큐 메시지의 잠금을 해제하므로 동일한 소비 애플리케이션이나 다른 소비 애플리케이션에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 시간 제한도 있으며, 애플리케이션에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 애플리케이션이 크래시되는 경우) Service Bus가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

애플리케이션이 메시지를 처리한 후 **complete()** 요청이 실행되기 전에 크래시되는 경우 다시 시작될 때 메시지가 애플리케이션으로 다시 전달됩니다. 이를 *최소 한 번 이상 처리*라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 애플리케이션 개발자가 중복 메시지 배달을 처리하는 논리를 애플리케이션에 추가해야 합니다. 이 작업은 배달을 여러 번 시도해도 일정하게 유지되는 메시지의 **getMessageId** 메서드를 사용하여 수행하는 경우가 많습니다.

> [!NOTE]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 이 도구는 가져오기/내보내기 기능 또는 항목, 큐, 구독, 릴레이 서비스, Notification Hubs 및 이벤트 허브를 테스트하는 기능과 같은 고급 기능을 제공합니다. 

## <a name="next-steps"></a>다음 단계
지금까지 Service Bus 큐의 기본 사항에 대해 알아보았습니다. 자세한 내용은 [큐, 토픽 및 구독][Queues, topics, and subscriptions]을 참조하세요.

자세한 내용은 [Java개발자 센터](https://azure.microsoft.com/develop/java/)를 참조하세요.

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage