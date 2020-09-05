---
title: Visual Studio를 사용 하 여 queue storage 시작 (클라우드 서비스)
description: Visual Studio 연결 서비스를 사용하여 스토리지 계정에 연결한 후 Visual Studio 클라우드 서비스 프로젝트에서 Azure Queue Storage 사용을 시작하는 방법입니다.
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9f0a3c3a96a73bd71a9b0c769a3ceff85ae428f1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017620"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Azure Queue Storage 및 Visual Studio 연결된 서비스 시작(클라우드 서비스 프로젝트)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>개요
이 문서에서는 Visual Studio **연결된 서비스 추가** 대화 상자를 사용하여 클라우드 서비스 프로젝트에서 Azure 스토리지 계정을 만들거나 참조한 후 Visual Studio에서 Azure Queue Storage를 사용하는 방법을 설명합니다.

코드에서 큐를 만드는 방법을 살펴보겠습니다. 또한 큐 메시지 추가, 수정, 읽기 및 제거와 같은 기본 큐 작업을 수행하는 방법을 보여 드립니다. 샘플은 c # 코드로 작성 되었으며 [.net 용 Microsoft Azure Storage 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/dn261237.aspx)를 사용 합니다.

**연결된 서비스 추가** 작업은 프로젝트의 Azure Storage에 액세스하는 데 적합한 NuGet 패키지를 설치하고 프로젝트 구성 파일에 스토리지 계정에 대한 연결 문자열을 추가합니다.

* 코드에서 큐를 조작하는 방법에 대한 자세한 내용은 [.NET을 사용하여 Azure Queue Storage 시작](../storage/queues/storage-dotnet-how-to-use-queues.md) 을 참조하세요.
* Azure Storage에 대한 일반적인 내용은 [스토리지 설명서](https://azure.microsoft.com/documentation/services/storage/)를 참조하세요.
* Azure Cloud Services에 대한 일반적인 내용은 [Cloud Services 설명서](https://azure.microsoft.com/documentation/services/cloud-services/) 를 참조하세요.
* ASP.NET 애플리케이션을 프로그래밍하는 방법에 대한 자세한 내용은 [ASP.NET](https://www.asp.net) 을 참조하세요.

Azure Queue Storage는 HTTP 또는 HTTPS를 사용하여 인증된 호출을 통해 전 세계 어디에서나 액세스할 수 있는 다수의 메시지를 저장하기 위한 서비스입니다. 단일 큐 메시지의 크기는 최대 64KB일 수 있으며, 하나의 큐에 스토리지 계정의 총 용량 제한까지 수백만 개의 메시지가 포함될 수 있습니다.

## <a name="access-queues-in-code"></a>코드에서 큐 액세스
Visual Studio Cloud Services 프로젝트의 큐에 액세스하려면 Azure Queue Storage에 액세스하는 C# 소스 파일에 다음 항목을 포함해야 합니다.

1. C# 파일 맨 위의 네임스페이스 선언에 이러한 **using** 문이 포함되어 있는지 확인합니다.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
2. 스토리지 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 스토리지 연결 문자열 및 스토리지 계정 정보를 가져오려면 다음 코드를 사용합니다.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
3. 스토리지 계정의 큐 개체를 참조하려면 **CloudQueueClient** 개체를 가져옵니다.  
   
    ```csharp
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
4. 특정 큐를 참조하려면 **CloudQueue** 개체를 가져옵니다.
   
    ```csharp
    // Get a reference to a queue named "messageQueue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");
    ```

**참고:** 다음 샘플의 코드 앞에 위의 코드를 모두 사용 합니다.

## <a name="create-a-queue-in-code"></a>코드에서 큐 만들기
코드에서 큐를 만들려면 **CreateIfNotExists**에 대한 호출을 추가합니다.

```csharp
// Create the CloudQueue if it does not exist
messageQueue.CreateIfNotExists();
```

## <a name="add-a-message-to-a-queue"></a>큐에 메시지 추가
기존 큐에 메시지를 삽입하려면 새 **CloudQueueMessage** 개체를 만든 다음 **AddMessage** 메서드를 호출합니다.

**CloudQueueMessage** 개체는 문자열(UTF-8 형식) 또는 바이트 배열에서 만들 수 있습니다.

다음은 'Hello, World' 메시지를 삽입하는 예입니다.

```csharp
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
messageQueue.AddMessage(message);
```

## <a name="read-a-message-in-a-queue"></a>큐의 메시지 읽기
큐에서 메시지를 제거 하지 않고도 **PeekMessage** 메서드를 호출 하 여 큐의 맨 앞에 있는 메시지를 볼 수 있습니다.

```csharp
// Peek at the next message
CloudQueueMessage peekedMessage = messageQueue.PeekMessage();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>큐의 메시지 읽기 및 제거
이 코드에서는 2단계를 거쳐 큐에서 메시지를 제거할 수 있습니다.

1. **Getmessage** 를 호출 하 여 큐에서 다음 메시지를 가져옵니다. **Getmessage** 에서 반환 된 메시지는이 큐에서 메시지를 읽는 다른 코드에는 표시 되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다.
2. 큐에서 메시지 제거를 완료하려면 **DeleteMessage**를 호출합니다.

메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 다음 코드에서는 메시지가 처리된 직후에 **DeleteMessage** 를 호출합니다.

```csharp
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

// Process the message in less than 30 seconds

// Then delete the message.
await messageQueue.DeleteMessage(retrievedMessage);
```


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>추가 옵션을 사용하여 큐 메시지를 처리 및 제거합니다.
큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다.

* 메시지의 배치(최대 32개)를 가져올 수 있습니다.
* 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다. 다음 코드 예제는 **GetMessages** 메서드를 사용하여 한 번 호출에 20개의 메시지를 가져옵니다. 그런 다음에 **foreach** 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다. 5 분은 모든 메시지에 대해 동시에 시작 되므로 **Getmessages**에 대 한 호출 이후로 5 분이 지난 후에는 삭제 되지 않은 모든 메시지가 다시 표시 됩니다.

예를 들면 다음과 같습니다.

```csharp
foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.

    // Then delete the message after processing
    messageQueue.DeleteMessage(message);

}
```

## <a name="get-the-queue-length"></a>큐 길이 가져오기
큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **FetchAttributes** 메서드는 메시지 수를 포함하여 큐 특성을 검색하도록 큐 서비스에 요청합니다. **ApproximateMethodCount** 속성은 큐 서비스를 호출하지 않고도 **FetchAttributes** 메서드를 통해 검색된 마지막 값을 반환합니다.

```csharp
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>일반적인 Azure 큐 API와 함께 Async-Await 패턴 사용
이 예에서는 일반적인 Azure 큐 API와 함께 Async-Await 패턴을 사용하는 방법을 보여 줍니다. 이 샘플은 지정 된 각 메서드의 비동기 버전을 호출 합니다 .이는 각 메서드의 **async** 사후 수정에서 볼 수 있습니다. 비동기 메서드가 사용되는 경우 호출이 완료될 때까지 Async- Await 패턴이 로컬 실행을 일시 중단합니다. 이 동작은 현재 스레드가 성능 병목 현상을 방지해주는 다른 작업을 수행할 수 있게 해주며, 애플리케이션의 전반적인 응답성을 향상시킵니다. .NET에서 비동기 대기 패턴을 사용 하는 방법에 대 한 자세한 내용은 [async And wait (c # 및 Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) 를 참조 하세요.

```csharp
// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Add the message asynchronously
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Delete the message asynchronously
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>큐 삭제
큐 및 해당 큐의 모든 메시지를 삭제하려면 큐 개체의 **Delete** 메서드를 호출합니다.

```csharp
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

