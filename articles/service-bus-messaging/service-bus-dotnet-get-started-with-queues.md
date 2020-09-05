---
title: Azure Service Bus 큐 시작 | Microsoft Docs
description: 이 자습서에서는 Service Bus 큐에 메시지를 보내고 메시지를 수신 하는 .NET Core 콘솔 응용 프로그램을 만듭니다.
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: cff2b8a8a0f6aefad43737aeb6fe63d40facac05
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021666"
---
# <a name="get-started-with-service-bus-queues"></a>Service Bus 큐 시작
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
이 자습서에서는 Service Bus 큐에 메시지를 보내고 메시지를 수신 하는 .NET Core 콘솔 응용 프로그램을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Core SDK](https://www.microsoft.com/net/download/windows) 버전 2.0 이상
- Azure 구독 이 자습서를 완료하려면 Azure 계정이 필요합니다. [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
- 작업할 큐가 없는 경우 [Azure Portal을 사용하여 Service Bus 큐 만들기](service-bus-quickstart-portal.md) 문서의 단계에 따라 큐를 만듭니다.

  - Service Bus 큐의 빠른 개요를 읽습니다.
  - Service Bus 네임 스페이스를 만듭니다.
  - 연결 문자열을 가져옵니다.
  - Service Bus 큐를 만듭니다.

## <a name="send-messages-to-the-queue"></a>큐에 메시지 보내기

큐에 메시지를 보내려면 Visual Studio를 사용하여 C# 콘솔 애플리케이션을 작성합니다.

### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

Visual Studio를 시작 하 고 c #에 대 한 새 **콘솔 앱 (.Net Core)** 프로젝트를 만듭니다. 이 예제에서는 *CoreSenderApp*앱의 이름을로 합니다.

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.
1. **찾아보기**를 선택합니다. **[ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** 를 검색 하 고 선택 합니다.
1. 설치 **를 선택 하** 여 설치를 완료 한 다음 NuGet 패키지 관리자를 닫습니다.

    ![NuGet 패키지 선택][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>코드를 작성하여 큐에 메시지 보내기

1. *Program.cs*에서 `using` 네임 스페이스 정의의 맨 위에 클래스 선언 앞에 다음 문을 추가 합니다.

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. 클래스에서 `Program` 다음 변수를 선언 합니다.

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    네임 스페이스에 대 한 연결 문자열을 `ServiceBusConnectionString` 변수로 입력 합니다. 큐 이름을 입력 합니다.

1. 메서드를 `Main()` 다음 **비동기** `Main` 메서드로 바꿉니다. `SendMessagesAsync()`큐에 메시지를 전송 하기 위해 다음 단계에서 추가 하는 메서드를 호출 합니다. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```
1. 메서드 바로 다음에 `MainAsync()` `SendMessagesAsync()` 지정 된 메시지 수를 전송 하는 작업을 수행 하는 다음 메서드를 추가 합니다 `numberOfMessagesToSend` (현재 10으로 설정 됨).

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```

*Program.cs* 파일은 다음과 같이 표시 됩니다.

```csharp
namespace CoreSenderApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        public static async Task Main(string[] args)
        {    
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);
    
            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");
    
            // Send messages.
            await SendMessagesAsync(numberOfMessages);
    
            Console.ReadKey();
    
            await queueClient.CloseAsync();
        }

        static async Task SendMessagesAsync(int numberOfMessagesToSend)
        {
            try
            {
                for (var i = 0; i < numberOfMessagesToSend; i++)
                {
                    // Create a new message to send to the queue
                    string messageBody = $"Message {i}";
                    var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                    // Write the body of the message to the console
                    Console.WriteLine($"Sending message: {messageBody}");

                    // Send the message to the queue
                    await queueClient.SendAsync(message);
                }
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
            }
        }
    }
}
```

프로그램을 실행 하 고 Azure Portal 확인 합니다.

Queue **Essentials**를 표시 하려면 네임 스페이스 **개요** 창에서 큐의 이름을 선택 합니다.

![개수 및 크기와 함께 수신 된 메시지][queue-message]

이제 큐에 대 한 **활성 메시지 수** 값이 **10**입니다. 메시지를 검색 하지 않고이 발신자 앱을 실행할 때마다이 값이 10 씩 늘어납니다.

앱이 큐에 메시지를 추가할 때마다 큐의 현재 크기는 **Essentials** 의 **현재** 값을 증가 시킵니다.

다음 섹션에서는 이러한 메시지를 검색 하는 방법을 설명 합니다.

## <a name="receive-messages-from-the-queue"></a>큐에서 메시지 받기

보낸 메시지를 수신 하려면 다른 **콘솔 앱 (.Net Core)** 응용 프로그램을 만듭니다. 보낸 사람 응용 프로그램의 경우와 마찬가지로 **ServiceBus** NuGet 패키지를 설치 합니다.

### <a name="write-code-to-receive-messages-from-the-queue"></a>코드를 작성하여 큐에서 메시지 받기

1. *Program.cs*에서 `using` 네임 스페이스 정의의 맨 위에 클래스 선언 앞에 다음 문을 추가 합니다.

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. 클래스에서 `Program` 다음 변수를 선언 합니다.

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    네임 스페이스에 대 한 연결 문자열을 `ServiceBusConnectionString` 변수로 입력 합니다. 큐 이름을 입력 합니다.

1. `Main()` 메서드를 다음 코드로 바꿉니다.

    ```csharp
    static void Main(string[] args)
    {
        MainAsync().GetAwaiter().GetResult();
    }

    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register QueueClient's MessageHandler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. 메서드 바로 뒤에 `MainAsync()` 다음 메서드를 추가 합니다 .이 메서드는 메시지 처리기를 등록 하 고 발신자 응용 프로그램에서 보낸 메시지를 받습니다.

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

1. 이전 메서드의 바로 뒤에 수신된 메시지를 처리하는 다음 `ProcessMessagesAsync()` 메서드를 추가합니다.

    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

1. 마지막으로 발생할 수 있는 모든 예외를 처리하는 다음 메서드를 추가합니다.

    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }
    ```

*Program.cs* 파일은 다음과 같습니다.

```csharp
namespace CoreReceiverApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
            Console.WriteLine("======================================================");

            // Register QueueClient's MessageHandler and receive messages in a loop
            RegisterOnMessageHandlerAndReceiveMessages();
 
            Console.ReadKey();

            await queueClient.CloseAsync();
        }

        static void RegisterOnMessageHandlerAndReceiveMessages()
        {
            // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
            var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
            {
                // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                // Set it according to how many messages the application wants to process in parallel.
                MaxConcurrentCalls = 1,

                // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                AutoComplete = false
            };

            // Register the function that will process messages
            queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
        }

        static async Task ProcessMessagesAsync(Message message, CancellationToken token)
        {
            // Process the message
            Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

            // Complete the message so that it is not received again.
            // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
            await queueClient.CompleteAsync(message.SystemProperties.LockToken);

            // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
            // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
            // to avoid unnecessary exceptions.
        }

        static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
        {
            Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
            var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
            Console.WriteLine("Exception context for troubleshooting:");
            Console.WriteLine($"- Endpoint: {context.Endpoint}");
            Console.WriteLine($"- Entity Path: {context.EntityPath}");
            Console.WriteLine($"- Executing Action: {context.Action}");
            return Task.CompletedTask;
        }
    }
}
```

프로그램을 실행하고 포털을 다시 확인합니다. **활성 메시지 수** 와 **현재** 값이 이제 **0**입니다.

![메시지를 받은 후 큐][queue-message-receive]

축하합니다! 이제 큐를 만들고, 큐에 메시지 집합을 보내고, 동일한 큐에서 해당 메시지를 수신 했습니다.

> [!NOTE]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. 사용자는 Service Bus 탐색기를 사용 하 여 Service Bus 네임 스페이스에 쉽게 연결 하 고 메시징 엔터티를 관리할 수 있습니다. 이 도구는 가져오기/내보내기 기능 또는 토픽, 큐, 구독, 릴레이 서비스, notification hubs 및 event hubs를 테스트 하는 기능과 같은 고급 기능을 제공 합니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징의 많은 고급 기능 중 일부를 보여 주는 [샘플이 있는 GitHub 리포지토리](https://github.com/Azure/azure-service-bus/tree/master/samples)를 확인합니다.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive-in-essentials.png

