---
title: Azure Service Bus 큐 시작 | Microsoft Docs
description: Service Bus 메시징 큐를 사용하는 C# 콘솔 애플리케이션을 작성합니다.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 22996b277aba96cbbfedbb3e9cc67644d1a535ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714278"
---
# <a name="get-started-with-service-bus-queues"></a>Service Bus 큐 시작
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
이 자습서에서는.NET Core 콘솔 응용 프로그램 메시지를 보내고 Service Bus 큐에서 메시지를 받을을 만들 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

1. [Visual Studio 2017 업데이트 3(버전 15.3, 26730.01)](https://www.visualstudio.com/vs) 이상
2. [NET Core SDK](https://www.microsoft.com/net/download/windows) 버전 2.0 이상
2. Azure 구독. 이 자습서를 완료하려면 Azure 계정이 필요합니다. 활성화할 수 있습니다 하 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) 에 등록 또는 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)합니다.
3. 다음 단계를 사용 하려면 큐가 없는 경우는 [Service Bus 큐를 사용 하 여 Azure portal](service-bus-quickstart-portal.md) 큐를 만드는 문서입니다.
    1. 빠른 읽을 **개요** Service bus **큐**합니다. 
    2. Service Bus를 만듭니다 **네임 스페이스**합니다. 
    3. 가져오기의 **연결 문자열**합니다. 
    4. Service Bus를 만듭니다 **큐**합니다. 

## <a name="send-messages-to-the-queue"></a>큐에 메시지 보내기

큐에 메시지를 보내려면 Visual Studio를 사용하여 C# 콘솔 애플리케이션을 작성합니다.

### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

Visual Studio를 시작하고 새로운 **콘솔 앱(.NET Core)** 프로젝트를 만듭니다.

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. **찾아보기** 탭을 클릭하고 **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** 를 검색한 다음 **Microsoft.Azure.ServiceBus** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.
   
    ![NuGet 패키지 선택][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>코드를 작성하여 큐에 메시지 보내기

1. 클래스 선언 전에 Program.cs의 네임스페이스 정의 위쪽에서 다음 `using` 문을 추가합니다.
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. `Program` 클래스 내에서 다음 변수를 선언합니다. `ServiceBusConnectionString` 변수는 네임스페이스를 만들 때 가져온 연결 문자열에 설정하고, `QueueName`은 큐를 만들 때 사용된 이름에 설정합니다.
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ``` 

3. `Main()`이라는 기본 내용을 다음 코드줄로 바꿉니다.

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. `Main()`의 바로 뒤에 메시지 전송 메서드를 호출하는 다음 비동기 `MainAsync()` 메서드를 추가합니다.

    ```csharp
    static async Task MainAsync()
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

5. `MainAsync()` 메서드의 바로 뒤에 `numberOfMessagesToSend`(현재 10으로 설정됨)으로 지정된 메시지 수를 전송하는 작업을 수행하는 다음 `SendMessagesAsync()` 메서드를 추가합니다.

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
   
6. Program.cs 파일은 다음과 같아야 합니다.
   
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

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send Messages
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

7. 프로그램을 실행하고 Azure Portal을 확인합니다. 네임스페이스 **개요** 창에서 큐 이름을 클릭합니다. 큐 **Essentials** 화면이 표시됩니다. 이제 큐에 대한 **활성 메시지 수** 값이 **10**입니다. 메시지를 검색하지 않고 보낸 사람 애플리케이션을 실행할 때마다(다음 섹션에서 설명) 이 값이 10만큼 증가합니다. 또한 앱이 큐에 메시지를 추가할 때마다 큐의 현재 크기는 **Essentials** 창에 있는 **현재** 값을 증가시킵니다.
   
      ![메시지 크기][queue-message]

## <a name="receive-messages-from-the-queue"></a>큐에서 메시지 받기

보낸 메시지를 받으려면 다른.NET Core 콘솔 응용 프로그램 만들기 및 설치 합니다 **Microsoft.Azure.ServiceBus** NuGet 패키지를 이전의 보낸 사람 응용 프로그램과 유사 합니다.

### <a name="write-code-to-receive-messages-from-the-queue"></a>코드를 작성하여 큐에서 메시지 받기

1. 클래스 선언 전에 Program.cs의 네임스페이스 정의 위쪽에서 다음 `using` 문을 추가합니다.
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. `Program` 클래스 내에서 다음 변수를 선언합니다. `ServiceBusConnectionString` 변수는 네임스페이스를 만들 때 가져온 연결 문자열에 설정하고, `QueueName`은 큐를 만들 때 사용된 이름에 설정합니다.
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

3. `Main()`이라는 기본 내용을 다음 코드줄로 바꿉니다.

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. `Main()`의 바로 뒤에 `RegisterOnMessageHandlerAndReceiveMessages()` 메서드를 호출하는 다음 비동기 `MainAsync()` 메서드를 추가합니다.

    ```csharp
    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register the queue message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

5. `MainAsync()` 메서드의 바로 뒤에 메시지 처리기를 등록하고 보낸 사람 애플리케이션에서 보낸 메시지를 수신하는 다음 메서드를 추가합니다.

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

6. 이전 메서드의 바로 뒤에 수신된 메시지를 처리하는 다음 `ProcessMessagesAsync()` 메서드를 추가합니다.
 
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

7. 마지막으로 발생할 수 있는 모든 예외를 처리하는 다음 메서드를 추가합니다.
 
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
   
8. Program.cs 파일은 다음과 같아야 합니다.
   
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
4. 프로그램을 실행하고 포털을 다시 확인합니다. **활성 메시지 수**와 **현재** 값이 이제 **0**이 됩니다.
   
    ![큐 길이][queue-message-receive]

축하합니다! 이제 큐를 만들고 해당 큐로 일련의 메시지를 보내고 동일한 큐에서 해당 메시지를 받았습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징의 많은 고급 기능 중 일부를 보여 주는 [샘플이 있는 GitHub 리포지토리](https://github.com/Azure/azure-service-bus/tree/master/samples)를 확인합니다.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png

