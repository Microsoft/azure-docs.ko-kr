---
title: 송신 및.NET Core-Azure Event Hubs를 사용 하 여 이벤트 수신 | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에 이벤트를 보내는 .NET Core 애플리케이션을 만드는 연습을 제공합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: e572a9e19da144be61faefacc370cf512f5079ac
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62738408"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core"></a>이벤트를 보내거나.NET Core를 사용 하 여 Azure Event Hubs에서 이벤트 수신
Event Hubs는 연결된 장치 및 애플리케이션에서 많은 양의 이벤트 데이터(원격 분석)를 처리하는 서비스입니다. Event Hubs에 데이터를 수집한 후 저장소 클러스터를 사용하여 데이터를 저장하거나 실시간 분석 공급자를 사용하여 변환할 수 있습니다. 이 대규모 이벤트 수집 및 처리 기능은 IoT(사물 인터넷)를 포함하여 최신 애플리케이션 아키텍처의 핵심 구성 요소입니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에서.NET Core 응용 프로그램을 만드는 방법을 보여 줍니다 C# 이벤트를 보내거나 이벤트 허브에서 이벤트를 수신 합니다. 

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName` 문자열을 이벤트 허브 값으로 대체하고, 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- [Microsoft Visual Studio 2015 또는 2017](https://www.visualstudio.com). 이 자습서의 예제에서는 Visual Studio 2017을 사용하지만 Visual Studio 2015도 지원됩니다.
- [.NET Core Visual Studio 2015 또는 2017 도구](https://www.microsoft.com/net/core). 
- **Event Hubs 네임 스페이스를 만들고 event hub**합니다. 첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 애플리케이션에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스 및 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 따릅니다. 그런 다음, 가져옵니다 합니다 **이벤트 허브 네임 스페이스에 대 한 연결 문자열** 문서에서 지침에 따라: [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 해당 연결 문자열은 이 자습서의 뒷부분에서 사용합니다.

## <a name="send-events"></a>이벤트 보내기 
이 섹션에서는 이벤트 허브로 이벤트를 보내도록.NET Core 콘솔 응용 프로그램을 만드는 방법을 보여 줍니다. 

### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

Visual Studio를 시작합니다. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다. .NET Core 콘솔 애플리케이션을 만듭니다.

![새 프로젝트](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

다음 단계에 따라 [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) .NET Core 라이브러리 NuGet 패키지를 프로젝트에 추가합니다. 

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. **찾아보기** 탭을 클릭한 다음 "Microsoft.Azure.EventHubs"를 검색하고 **Microsoft.Azure.EventHubs** 패키지를 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>이벤트 허브에 메시지를 전송하는 코드 작성

1. Program.cs 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Event Hubs 연결 문자열 및 엔터티 경로(개별 이벤트 허브 이름)에 대한 `Program` 클래스에 상수를 추가합니다. 대괄호 안의 자리 표시자를 이벤트 허브를 만들 때 얻은 적절한 값으로 바꿉니다. `{Event Hubs connection string}`이 네임스페이스 수준 연결 문자열이며 이벤트 허브 문자열이 아님을 확인합니다. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. 다음과 같이 `MainAsync`라는 새 메서드를 `Program` 클래스에 추가합니다.

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. 다음과 같이 `SendMessagesToEventHub`라는 새 메서드를 `Program` 클래스에 추가합니다.

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. `Program` 클래스의 `Main` 메서드에 다음 코드를 추가합니다.

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Program.cs는 다음과 같아야 합니다.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. 프로그램을 실행하고 오류가 없는지 확인합니다.

## <a name="receive-events"></a>이벤트 수신
이 섹션에 사용 하 여 이벤트 허브에서 메시지를 수신 하는.NET Core 콘솔 응용 프로그램을 작성 하는 방법을 보여 줍니다 합니다 [이벤트 프로세서 호스트](event-hubs-event-processor-host.md)합니다. [이벤트 프로세서 호스트](event-hubs-event-processor-host.md)는 영구적 검사점을 관리하여 Event Hubs의 이벤트 수신을 간소화하고 이러한 Event Hubs에서 병렬 수신하는 .NET 클래스입니다. 이벤트 프로세서 호스트를 사용하면 다른 노드에 호스트된 수신기를 비롯한 여러 수신기 간에 이벤트를 분할할 수 있습니다. 이 예제에서는 단일 수신기에 대해 이벤트 프로세서 호스트를 사용하는 방법을 보여 줍니다.
> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName`, `StorageAccountName`, `StorageAccountKey`, `StorageContainerName` 문자열을 이벤트 허브 값으로 대체한 후 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

Visual Studio를 시작합니다. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다. .NET Core 콘솔 애플리케이션을 만듭니다.

![새 프로젝트](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

[**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) 및 [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET 표준 라이브러리 NuGet 패키지를 다음 단계를 따라 사용자 프로젝트에 추가합니다. 

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. **찾아보기** 탭을 클릭하고 **Microsoft.Azure.EventHubs**를 검색한 다음 **Microsoft.Azure.EventHubs** 패키지를 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.
3. 1단계와 2단계를 반복하고 **Microsoft.Azure.EventHubs.Processor** 패키지를 설치합니다.

### <a name="implement-the-ieventprocessor-interface"></a>IEventProcessor 인터페이스 구현

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 차례로 클릭합니다. 새 클래스 이름을 **SimpleEventProcessor**로 지정합니다.

2. SimpleEventProcessor.cs 파일을 열고 다음 `using` 문을 파일의 맨 위에 추가합니다.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. `IEventProcessor` 인터페이스를 구현합니다. `SimpleEventProcessor` 클래스의 전체 내용을 다음 코드로 바꿉니다.

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>SimpleEventProcessor를 사용하도록 Main 메서드 업데이트

1. Program.cs 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. 이벤트 허브 연결 문자열, 이벤트 허브 이름, 저장소 계정 컨테이너 이름, 저장소 계정 이름, 저장소 계정 키의 `Program` 클래스에 상수를 추가합니다. 자리 표시자를 해당 값으로 바꾸어 다음 코드를 추가합니다.

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 다음과 같이 `MainAsync`라는 새 메서드를 `Program` 클래스에 추가합니다.

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. `Main` 메서드에 다음 코드 줄을 추가합니다.

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 파일은 다음과 같아야 합니다.

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. 프로그램을 실행하고 오류가 없는지 확인합니다.


## <a name="next-steps"></a>다음 단계
다음 문서를 읽어보세요.

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [기능 및 Azure Event Hubs의 용어](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)


