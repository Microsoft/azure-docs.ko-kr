---
title: '빠른 시작: Azure Queue storage 라이브러리 v12 - .NET'
description: Azure Queue .NET v12 라이브러리를 사용하여 큐를 만들고 큐에 메시지를 추가하는 방법을 알아봅니다. 그런 다음, 큐에서 메시지를 읽고 삭제하는 방법을 알아봅니다. 큐를 삭제하는 방법도 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: fd60adf94e57be3892a264c2a8457b839e1c8778
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011296"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>빠른 시작: .NET용 Azure Queue storage 클라이언트 라이브러리 v12

.NET용 Azure Queue storage 클라이언트 라이브러리 버전 12를 시작합니다. Azure Queue storage는 나중에 검색하고 처리할 수 있도록 대량의 메시지를 저장하는 서비스입니다. 다음 단계에 따라 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 보세요.

.NET용 Azure Queue storage 클라이언트 라이브러리 v12를 사용하여 다음을 수행할 수 있습니다.

* 큐 만들기
* 큐에 메시지 추가
* 큐의 메시지 피킹(Peeking)
* 큐의 메시지 업데이트
* 큐에서 메시지 받기
* 큐에서 메시지 삭제
* 큐 삭제

추가 리소스:

* [API 참조 설명서](/dotnet/api/azure.storage.queues)
* [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues)
* [패키지(NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0)
* [샘플](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* Azure Storage 계정 - [스토리지 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 현재 운영 체제의 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)입니다. 런타임이 아니라 SDK가 있어야 합니다.

## <a name="setting-up"></a>설치

이 섹션에서는 .NET용 Azure Queue storage 클라이언트 라이브러리 v12를 사용하는 프로젝트 준비 과정을 안내합니다.

### <a name="create-the-project"></a>프로젝트 만들기

*QueuesQuickstartV12*라는 .NET Core 애플리케이션을 만듭니다.

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 *QueuesQuickstartV12*라는 새 콘솔 앱을 만듭니다. 이 명령은 *Program.cs*라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. 새로 만든 *QueuesQuickstartV12* 디렉터리로 전환합니다.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에서 `dotnet add package` 명령을 사용하여 .NET용 Azure Queue storage 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 편집기에서 *Program.cs* 파일 열기
1. `Console.WriteLine("Hello World!");` 문 제거
1. `using` 지시문 추가
1. [비동기 코드를 지원](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main)하도록 `Main` 메서드 선언 업데이트



코드는 다음과 같습니다.

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>개체 모델

Azure Queue storage는 대량의 메시지를 저장하기 위한 서비스입니다. 큐 메시지의 크기는 최대 64KB입니다. 큐는 스토리지 계정의 용량 제한에 도달할 때까지 수백만 개의 메시지를 포함할 수 있습니다. 큐는 비동기적으로 처리할 작업의 백로그를 만드는 데 일반적으로 사용됩니다. Queue storage는 다음 세 가지 유형의 리소스를 제공합니다.

* 스토리지 계정
* 스토리지 계정의 큐
* 큐 내의 메시지

다음 다이어그램에서는 이러한 리소스 간의 관계를 보여줍니다.

![Queue storage 아키텍처 다이어그램](./media/storage-queues-introduction/queue1.png)

다음 .NET 클래스를 사용하여 이러한 리소스와 상호 작용합니다.

* [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient): `QueueServiceClient`를 사용하면 스토리지 계정의 모든 큐를 관리할 수 있습니다.
* [QueueClient](/dotnet/api/azure.storage.queues.queueclient): `QueueClient` 클래스를 사용하면 개별 큐와 해당 메시지를 관리하고 조작할 수 있습니다.
* [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage): `QueueMessage` 클래스는 큐에서 [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages)를 호출할 때 반환되는 개별 개체를 나타냅니다.

## <a name="code-examples"></a>코드 예제

다음 예제 코드 조각은 .NET용 Azure Queue storage 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [연결 문자열 가져오기](#get-the-connection-string)
* [큐 만들기](#create-a-queue)
* [큐에 메시지 추가](#add-messages-to-a-queue)
* [큐의 메시지 피킹(Peeking)](#peek-at-messages-in-a-queue)
* [큐의 메시지 업데이트](#update-a-message-in-a-queue)
* [큐에서 메시지 받기](#receive-messages-from-a-queue)
* [큐에서 메시지 삭제](#delete-messages-from-a-queue)
* [큐 삭제](#delete-a-queue)

### <a name="get-the-connection-string"></a>연결 문자열 가져오기

아래 코드는 스토리지 계정에 대한 연결 문자열을 검색합니다. 연결 문자열은 [스토리지 연결 문자열 구성](#configure-your-storage-connection-string) 섹션에서 생성된 환경 변수에 저장됩니다.

다음 코드를 `Main` 메서드 내에 추가합니다.

```csharp
Console.WriteLine("Azure Queue storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>큐 만들기

새 큐의 이름을 결정합니다. 아래 코드는 큐 이름의 고유성을 보장하기 위해 이름에 GUID 값을 추가합니다.

> [!IMPORTANT]
> 큐 이름은 소문자, 숫자 및 하이픈만 포함할 수 있으며 문자나 숫자로 시작해야 합니다. 각 하이픈의 앞과 뒤에는 하이픈이 아닌 문자가 있어야 합니다. 이름의 길이는 3~63자 사이여야 합니다. 큐 이름 지정에 대한 자세한 내용은 [큐 및 메타데이터 이름 지정](/rest/api/storageservices/naming-queues-and-metadata)을 참조하세요.


[QueueClient](/dotnet/api/azure.storage.queues.queueclient) 클래스 인스턴스를 만듭니다. 그런 다음, [CreateAsync](/dotnet/api/azure.storage.queues.queueclient.createasync) 메서드를 호출하여 스토리지 계정에 큐를 만듭니다.

이 코드를 `Main` 메서드의 끝에 추가합니다.

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>큐에 메시지 추가

다음 코드 조각은 [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) 메서드를 호출하여 큐에 메시지를 비동기적으로 추가합니다. 또한, `SendMessageAsync` 호출에서 반환된 [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt)를 저장합니다. 영수증은 나중에 프로그램에서 메시지를 업데이트하는 데 사용됩니다.

이 코드를 `Main` 메서드의 끝에 추가합니다.

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>큐의 메시지 피킹(Peeking)

[PeekMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) 메서드를 호출하여 큐의 메시지를 피킹합니다. `PeekMessagesAsync` 메서드는 큐 앞에서 하나 이상의 메시지를 검색하지만 메시지의 표시 유형을 변경하지는 않습니다.

이 코드를 `Main` 메서드의 끝에 추가합니다.

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>큐의 메시지 업데이트

[UpdateMessageAsync](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) 메서드를 호출하여 메시지 콘텐츠를 업데이트합니다. `UpdateMessageAsync` 메서드는 메시지의 표시 시간 제한 및 콘텐츠를 변경할 수 있습니다. 메시지 콘텐츠는 크기가 최대 64KB인 UTF-8 인코딩 문자열이어야 합니다. 메시지의 신규 콘텐츠와 함께 이전에 코드에서 저장된 `SendReceipt`의 값을 전달합니다. `SendReceipt` 값은 업데이트할 메시지를 식별합니다.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기

[ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) 메서드를 호출하여 이전에 추가한 메시지를 다운로드합니다.

이 코드를 `Main` 메서드의 끝에 추가합니다.

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>큐에서 메시지 삭제

메시지를 처리한 후 큐에서 메시지를 삭제합니다. 여기서는 콘솔에 메시지를 표시하는 것 외에는 다른 처리 작업이 없습니다.

앱은 메시지를 처리하고 삭제하기 전에 `Console.ReadLine`을 호출하여 사용자 입력을 위해 일시 중지합니다. 리소스가 삭제되기 전에 [Azure Portal](https://portal.azure.com)에서 리소스가 제대로 생성되었는지 확인합니다. 명시적으로 삭제되지 않은 메시지는 다시 처리할 수 있도록 큐에 다시 표시됩니다.

이 코드를 `Main` 메서드의 끝에 추가합니다.

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>큐 삭제

다음 코드는 [DeleteAsync](/dotnet/api/azure.storage.queues.queueclient.deleteasync) 메서드로 큐를 삭제하여 앱에서 만든 리소스를 정리합니다.

이 코드를 `Main` 메서드의 끝에 추가합니다.

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>코드 실행

이 앱은 메시지 세 개를 만들어서 Azure 큐에 추가합니다. 이 코드는 큐의 메시지를 나열하고, 검색하여 삭제한 후 최종적으로 큐를 삭제합니다.

콘솔 창에서 애플리케이션 디렉터리로 이동한 다음, 애플리케이션을 빌드하고 실행합니다.

```console
dotnet build
```

```console
dotnet run
```

앱의 출력은 다음 예제 출력과 유사합니다.

```output
Azure Queue storage v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

메시지를 받기 전에 앱이 일시 중지되면 [Azure Portal](https://portal.azure.com)에서 스토리지 계정을 확인합니다. 메시지가 큐에 있는지 확인합니다.

**Enter** 키를 눌러서 메시지를 받고 삭제합니다. 메시지가 표시되면 **Enter** 키를 다시 눌러서 큐를 삭제하고 데모를 마칩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 비동기 .NET 코드를 사용하여 큐를 만들고 큐에 메시지를 추가하는 방법을 알아보았습니다. 그런 다음, 메시지를 피킹하고 검색하고 삭제하는 방법을 알아보았습니다. 마지막으로 메시지 큐를 삭제하는 방법을 알아보았습니다.

자습서, 샘플, 빠른 시작 및 기타 설명서는 다음 자료를 참조하세요.

> [!div class="nextstepaction"]
> [.NET 및.NET Core 개발자용 Azure](https://docs.microsoft.com/dotnet/azure/)

* 자세한 내용은 [.NET용 Azure Storage 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage)를 참조하세요.
* 더 많은 Azure Queue storage 샘플 앱을 보려면 [Azure Queue storage v12 .NET 클라이언트 라이브러리 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)을 계속 진행하세요.
* .NET Core에 대해 자세히 알아보려면 [10분 안에 .NET 시작](https://www.microsoft.com/net/learn/get-started/)을 참조하세요.
