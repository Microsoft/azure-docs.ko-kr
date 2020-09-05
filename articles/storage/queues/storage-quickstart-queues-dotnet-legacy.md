---
title: '빠른 시작: .NET용 Azure Storage v11을 사용하여 큐 관리'
description: 이 빠른 시작에서는 .NET용 Azure Storage 클라이언트 라이브러리를 사용하여 큐를 만들고 메시지를 큐에 추가하는 방법을 알아봅니다. 그런 다음, 큐에서 메시지를 읽고 처리하는 방법을 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e782dc632fe4100194015c3e0adb4ceb1ac30a6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011466"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>빠른 시작: .NET용 Azure Storage SDK v11을 사용하여 큐 관리

이 빠른 시작에서는 .NET용 Azure Storage 클라이언트 라이브러리 버전 11을 사용하여 큐를 만들고 메시지를 큐에 추가하는 방법을 알아봅니다. 그런 다음, 큐에서 메시지를 읽고 처리하는 방법을 알아봅니다. 

> [!NOTE]
> 이 빠른 시작에서는 Azure Queue storage 클라이언트 라이브러리의 레거시 버전을 사용합니다. 최신 버전을 시작하려면 [빠른 시작: .NET용 Azure Queue storage 클라이언트 라이브러리 v12](storage-quickstart-queues-dotnet.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

다음으로 운영 체제용 .NET Core 2.0을 다운로드 및 설치합니다. Windows를 실행하는 경우 원한다면 Visual Studio를 설치하고 .NET Framework를 사용할 수 있습니다. 또한 운영 체제와 사용할 편집기를 설치하도록 선택할 수도 있습니다.

### <a name="windows"></a>Windows

- [Windows용 .NET Core](https://www.microsoft.com/net/download/windows) 또는 [.NET Framework](https://www.microsoft.com/net/download/windows)(Windows용 Visual Studio에 포함)를 설치합니다.
- [Windows용 Visual Studio](https://www.visualstudio.com/)를 설치합니다. .NET Core를 사용하는 경우 Visual Studio는 선택 사항입니다.  

.NET Core와 .NET Framework 중에서 선택하는 방법에 대한 내용은 [서버 앱에 .NET Core와 .NET Framework 중에서 무엇을 사용할지 선택하는 방법](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server)을 참조하세요.

### <a name="linux"></a>Linux

- [Linux용 .NET Core](https://www.microsoft.com/net/download/linux) 설치
- 필요에 따라 [Visual Studio Code](https://www.visualstudio.com/) 및 [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 설치

### <a name="macos"></a>macOS

- [macOS용 .NET Core](https://www.microsoft.com/net/download/macos)를 설치합니다.
- 필요에 따라 [Mac용 Visual Studio](https://www.visualstudio.com/vs/visual-studio-mac/) 설치

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

이 빠른 시작 가이드에서 사용되는 샘플 애플리케이션은 기본적인 콘솔 애플리케이션입니다. [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart)에서 샘플 애플리케이션을 탐색할 수 있습니다.

[git](https://git-scm.com/)을 사용하여 개발 환경에 애플리케이션 복사본을 다운로드합니다. 

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. Visual Studio 솔루션을 열려면 *storage-queues-dotnet-quickstart* 폴더를 찾아서 열고 *storage-queues-dotnet-quickstart.sln*을 두 번 클릭합니다. 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>스토리지 연결 문자열 구성

애플리케이션을 실행하려면 스토리지 계정에 대한 연결 문자열을 제공해야 합니다. 이 애플리케이션 예제는 환경 변수의 연결 문자열을 읽어서 Azure Storage에 대한 요청 권한을 부여하는 데 사용합니다.

연결 문자열을 복사한 후 애플리케이션을 실행 중인 로컬 컴퓨터의 새 환경 변수에 씁니다. 환경 변수를 설정하려면 콘솔 창을 열고 사용 중인 운영 체제의 지침을 따릅니다. `<yourconnectionstring>`을 실제 연결 문자열로 바꿉니다.

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

환경 변수를 추가한 후에는 콘솔 창을 포함하여 실행 중인 프로그램 중에서 환경 변수를 읽어야 하는 프로그램을 다시 시작해야 할 수도 있습니다. 예를 들어 편집기로 Visual Studio를 사용하는 경우 Visual Studio를 다시 시작한 후 샘플을 실행합니다. 

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.bashrc` 명령을 실행하여 변경 내용을 적용합니다.

### <a name="macos"></a>macOS

.bash_profile을 편집하고, 환경 변수를 추가합니다.

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

환경 변수를 추가한 후에는 콘솔 창에서 `source .bash_profile` 명령을 실행하여 변경 내용을 적용합니다.

## <a name="run-the-sample"></a>샘플 실행

샘플 애플리케이션이 큐를 만들고 여기에 메시지를 추가합니다. 애플리케이션은 먼저 큐에서 메시지를 제거하지 않고 메시지를 피킹한 다음, 메시지를 검색하여 큐에서 삭제합니다.

### <a name="windows"></a>Windows

편집기로 Visual Studio를 사용하는 경우 **F5** 키를 눌러 실행할 수 있습니다. 

그렇지 않으면 애플리케이션 디렉터리로 이동하여 `dotnet run` 명령으로 애플리케이션을 실행합니다.

```
dotnet run
```

### <a name="linux"></a>Linux

애플리케이션 디렉터리로 이동하여 `dotnet run` 명령으로 애플리케이션을 실행합니다.

```
dotnet run
```

### <a name="macos"></a>macOS

애플리케이션 디렉터리로 이동하여 `dotnet run` 명령으로 애플리케이션을 실행합니다.

```
dotnet run
```

애플리케이션 예제의 출력은 다음 예제와 비슷합니다.

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>샘플 코드 이해

다음으로 작동 방식을 이해하도록 샘플 코드를 탐색합니다.

### <a name="try-parsing-the-connection-string"></a>연결 문자열 구문 분석 시도

샘플에서는 먼저 환경 변수에 스토리지 계정을 가리키는 [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) 개체를 만들도록 구문 분석될 수 있는 연결 문자열이 포함되는지 확인합니다. 연결 문자열이 유효한지 확인하기 위해, 샘플은 [TryParse](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) 메서드를 사용합니다. **TryParse**가 성공적인 경우 *storageAccount* 변수를 초기화하고 **true**를 반환합니다.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...    
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>큐 만들기

먼저, 샘플은 큐를 만들고 여기에 메시지를 추가합니다. 

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name 
// is unique in your storage account. 
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>메시지 추가

다음으로, 샘플은 큐의 뒤쪽에 메시지를 추가합니다. 

메시지는 UTF-8 인코딩을 사용하는 XML 요청에 포함될 수 있는 형식이어야 하며, 크기는 최대 64KB까지 가능합니다. 메시지에 이진 데이터가 포함되어 있으면 메시지를 Base64로 인코딩하는 것이 좋습니다.

기본적으로 메시지의 TTL(Time-To-Live)은 7일로 설정됩니다. 메시지 TTL(Time-To-Live)에 임의 양수를 지정할 수 있습니다.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

만기되지 않는 메시지를 추가하려면 [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) 호출에 `Timespan.FromSeconds(-1)`를 사용합니다.

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>큐의 메시지 피킹

이 샘플에서는 큐의 메시지를 피킹하는 방법을 보여줍니다. 메시지를 피킹하면 메시지의 콘텐츠를 읽을 수 있습니다. 하지만 메시지가 다른 클라이언트에게 계속 표시되기 때문에 이후에 다른 클라이언트가 메시지를 검색하고 처리할 수 있습니다.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's 
// visibility, so that another client can still retrieve and process it. 
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>메시지를 큐에서 제거

이 샘플에서는 메시지를 큐에서 제거하는 방법도 보여줍니다. 메시지를 큐에서 제거하는 경우 큐의 앞쪽에서 메시지를 검색하고 일시적으로 다른 클라이언트에 보이지 않게 합니다. 기본적으로,메시지는 30초 동안 보이지 않습니다. 이 시간 동안 코드가 메시지를 처리할 수 있습니다. 메시지를 큐에서 제거하는 작업을 마치려면, 처리 후 메시지를 즉시 삭제하여 다른 클라이언트가 동일한 메시지를 큐에서 제거하지 않도록 합니다.

코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지 처리에 실패하는 경우에는, 보이지 않는 기간이 지나면 메시지가 다시 표시됩니다. 다른 클라이언트가 동일한 메시지를 검색하고 다시 시도할 수 있습니다.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for 
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>리소스 정리

이 샘플에서는 큐를 삭제하여 만들어진 리소스를 정리합니다. 큐를 삭제하면 포함된 메시지도 모두 삭제됩니다.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>큐를 사용하는 .NET 애플리케이션 개발을 위한 리소스

Azure Queues를 사용하는 .NET 개발에 대한 다음 추가 리소스를 참조하세요.

### <a name="binaries-and-source-code"></a>이진 파일 및 소스 코드

- 최신 버전의 [.NET용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)에 대한 NuGet 패키지를 다운로드합니다.
    - [일반](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
    - [큐](https://www.nuget.org/packages/Azure.Storage.Queues/)
- GitHub에서 [.NET 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-storage-net)를 찾아보세요.

### <a name="client-library-reference-and-samples"></a>클라이언트 라이브러리 참조 및 샘플

- .NET 클라이언트 라이브러리에 대한 자세한 내용은 [.NET API 참조](https://docs.microsoft.com/dotnet/api/overview/azure/storage)를 참조하세요.
- .NET 클라이언트 라이브러리를 사용하여 작성된 [큐 스토리지 샘플](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues)을 살펴보세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 .NET을 사용하여 큐에 메시지를 추가하고, 큐의 메시지를 피킹하고, 메시지를 큐에서 제거하고 처리하는 방법을 알아보았습니다. 

> [!div class="nextstepaction"]
> [Azure Queue Storage로 애플리케이션 간 통신](https://docs.microsoft.com/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- .NET Core에 대해 자세히 알아보려면 [10분 안에 .NET 시작](https://www.microsoft.com/net/learn/get-started/)을 참조하세요.
