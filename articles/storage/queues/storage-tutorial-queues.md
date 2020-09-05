---
title: 자습서 - .NET에서 Azure Storage 큐 작업
description: Azure 큐 서비스를 사용하여 큐를 만들고 .NET 코드를 사용하여 메시지를 삽입하고, 가져오고, 삭제하는 방법에 대한 자습서입니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/09/2020
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: 7474cfbd0182797bd62e97979e83e2aeb5244cbc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008797"
---
# <a name="tutorial-work-with-azure-storage-queues-in-net"></a>자습서: .NET에서 Azure 스토리지 큐 작업

Azure Queue storage는 분산된 애플리케이션의 구성 요소 간에 통신을 가능하게 해주는 클라우드 기반 큐를 구현합니다. 각 큐는 발신자 구성 요소에서 추가하고 수신자 구성 요소에서 처리할 수 있는 메시지 목록을 유지합니다. 큐를 사용하면 수요에 따라 애플리케이션을 즉시 확장할 수 있습니다. 이 문서에서는 Azure 스토리지 큐를 작업하는 기본 단계를 보여줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - Azure Storage 계정 만들기
> - 앱 만들기
> - Azure 클라이언트 라이브러리 추가
> - 비동기 코드에 대한 지원 추가
> - 큐 만들기
> - 큐에 메시지 삽입
> - 큐에서 메시지 제거
> - 빈 큐 삭제
> - 명령줄 인수 확인
> - 앱 빌드 및 실행

## <a name="prerequisites"></a>필수 구성 요소

- 플랫폼 간 [Visual Studio Code](https://code.visualstudio.com/download) 편집기의 무료 복사본을 가져옵니다.
- [.NET Core SDK](https://dotnet.microsoft.com/download) 버전 3.1 이상을 다운로드하여 설치합니다.
- 현재 Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

먼저, Azure 스토리지 계정을 만듭니다. 스토리지 계정을 만드는 방법에 대한 단계별 지침은 [스토리지 계정 만들기](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) 빠른 시작을 참조하세요. 이 단계는 필수 구성 요소에서 무료 Azure 계정을 만든 후에 수행하는 별도의 단계입니다.

## <a name="create-the-app"></a>앱 만들기

**QueueApp**이라는 .NET Core 애플리케이션을 만듭니다. 간략한 설명을 위해, 이 앱에서는 큐를 통해 메시지를 보내고 받는 작업을 모두 처리하겠습니다.

1. CMD, PowerShell 또는 Azure CLI 같은 콘솔 창에서 `dotnet new` 명령을 사용하여 **QueueApp**이라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs**라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

   ```console
   dotnet new console -n QueueApp
   ```

2. 새로 만든 **QueueApp** 폴더로 전환하여 모두 정상인지 확인하는 앱을 빌드합니다.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   다음 출력과 유사한 결과가 표시됩니다.

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-the-azure-client-libraries"></a>Azure 클라이언트 라이브러리 추가

1. `dotnet add package` 명령을 사용하여 Azure Storage 클라이언트 라이브러리를 프로젝트에 추가합니다.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   콘솔 창의 프로젝트 폴더에서 다음 명령을 실행합니다.

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   콘솔 창의 프로젝트 폴더에서 다음 명령을 실행합니다.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>using 문 추가

1. 프로젝트 디렉터리의 명령줄에 `code .`를 입력하여 현재 디렉터리에서 Visual Studio Code를 엽니다. 명령줄 창을 계속 열어 둡니다. 이후에 몇 가지 명령을 더 실행해야 합니다. 빌드 및 디버그에 필요한 C# 자산을 추가하라는 메시지가 표시되면 **예** 단추를 클릭합니다.

1. **Program.cs** 원본 파일을 열고 `using System;` 문 바로 뒤에 다음 네임스페이스를 추가합니다. 이 앱은 이 네임스페이스의 형식을 사용하여 Azure Storage에 연결하고 큐를 사용합니다.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. **Program.cs** 파일을 저장합니다.

## <a name="add-support-for-asynchronous-code"></a>비동기 코드에 대한 지원 추가

앱에서 클라우드 리소스를 사용하므로 코드가 비동기적으로 실행됩니다.

1. 비동기 방식으로 실행되도록 **Main** 메서드를 업데이트합니다. **void**를 **비동기 작업** 반환 값으로 바꿉니다.

   ```csharp
   static async Task Main(string[] args)
   ```

1. **Program.cs** 파일을 저장합니다.

## <a name="create-a-queue"></a>큐 만들기

Azure API를 호출하기 전에 Azure Portal에서 자격 증명을 가져와야 합니다.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>앱에 연결 문자열 추가

앱이 스토리지 계정에 액세스할 수 있도록 앱에 연결 문자열을 추가합니다.

1. Visual Studio Code로 돌아갑니다.

1. **Main** 메서드에서 `Console.WriteLine("Hello World!");` 코드를 환경 변수에서 연결 문자열을 가져오는 다음 줄로 바꿉니다.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. 다음 코드를 큐 개체를 만들도록 **Main**에 추가합니다. 이 개체는 나중에 send 및 receive 메서드에 전달됩니다.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. 파일을 저장합니다.

## <a name="insert-messages-into-the-queue"></a>큐에 메시지 삽입

큐에 메시지를 보내는 새 메서드를 만듭니다.

1. **Program** 클래스에 다음 **InsertMessageAsync** 메서드를 추가합니다.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   이 메서드는 큐 참조에 전달됩니다. 새 큐가 아직 없는 경우 [CreateIfNotExistsAsync](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync)를 호출하여 새로 만들어집니다. 그런 다음, [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync)를 호출하여 *newMessage*를 큐에 추가합니다.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   이 메서드는 큐 참조에 전달됩니다. 새 큐가 아직 없는 경우 [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync)를 호출하여 새로 만들어집니다. 그런 다음, [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync)를 호출하여 *newMessage*를 큐에 추가합니다.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. **선택 사항** 기본적으로 메시지의 TTL(Time-To-Live)은 7일로 설정됩니다. 메시지 TTL(Time-To-Live)에 임의 양수를 지정할 수 있습니다. 다음 코드 조각에서는 만료되지 *않는* 메시지를 추가합니다.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

    만료되지 않는 메시지를 추가하려면 **SendMessageAsync** 호출에 `Timespan.FromSeconds(-1)`를 사용합니다.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

    만료되지 않는 메시지를 추가하려면 **AddMessageAsync** 호출에 `Timespan.FromSeconds(-1)`를 사용합니다.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. 파일을 저장합니다.

큐 메시지는 UTF-8 인코딩을 사용하여 XML 요청과 호환되는 형식이어야 합니다. 메시지의 크기는 최대 64KB일 수 있습니다. 메시지에 이진 데이터가 포함되어 있으면 메시지를 [Base64로 인코딩](/dotnet/api/system.convert.tobase64string)합니다.

## <a name="dequeue-messages"></a>큐에서 메시지 제거

큐에서 메시지를 검색하는 새 메서드를 만듭니다. 메시지가 성공적으로 수신되면 메시지가 여러 번 처리되지 않도록 큐에서 삭제해야 합니다.

1. **RetrieveNextMessageAsync**라는 새 메서드를 **Program** 클래스에 추가합니다.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   이 메서드는 [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync)를 호출하여 큐에서 메시지를 받고, 첫 번째 매개 변수에 1을 전달하여 큐에 있는 다음 메시지만 검색합니다. 메시지가 수신된 후 [DeleteMessageAsync](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync)를 호출하여 큐에서 메시지를 삭제합니다.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   이 메서드는 [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync)를 호출하여 큐에서 메시지를 받습니다. 메시지가 수신된 후 [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync)를 호출하여 큐에서 메시지를 삭제합니다.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. 파일을 저장합니다.

## <a name="delete-an-empty-queue"></a>빈 큐 삭제

프로젝트가 끝나면 여기서 만든 리소스가 계속 필요한지 확인하는 것이 가장 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 큐가 있지만 비어 있는 경우 사용자에게 큐를 삭제할 것인지 물어봅니다.

1. 빈 큐를 삭제하는 프롬프트를 포함하도록 **RetrieveNextMessageAsync** 메서드를 확장합니다.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. 파일을 저장합니다.

## <a name="check-for-command-line-arguments"></a>명령줄 인수 확인

앱에 전달된 명령줄 인수가 있는 경우 이러한 인수가 큐에 추가할 메시지라고 가정합니다. 인수를 조인하여 문자열을 만듭니다. 앞에서 추가한 **InsertMessageAsync** 메서드를 호출하여 이 문자열을 메시지 큐에 추가합니다.

명령줄 인수가 없는 경우 검색 작업을 시도합니다. **RetrieveNextMessageAsync** 메서드를 호출하여 큐의 다음 메시지를 검색합니다.

마지막으로, 사용자 입력을 기다렸다가 **Console.ReadLine**을 호출하여 종료합니다.

1. 명령줄 인수를 확인하고 사용자 입력을 기다리도록 **Main** 메서드를 확장합니다.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. 파일을 저장합니다.

## <a name="complete-code"></a>전체 코드

이 프로젝트의 전체 코드는 다음과 같습니다.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 프로젝트 디렉터리의 명령줄에서 다음 dotnet 명령을 실행하여 프로젝트를 빌드합니다.

   ```console
   dotnet build
   ```

1. 프로젝트가 성공적으로 빌드되면 다음 명령을 실행하여 첫 번째 메시지를 큐에 추가합니다.

   ```console
   dotnet run First queue message
   ```

   다음 출력이 표시됩니다.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

1. 명령줄 인수 없이 앱을 실행하여 큐의 첫 번째 메시지를 검색하고 제거합니다.

   ```console
   dotnet run
   ```

1. 모든 메시지를 제거할 때까지 앱을 계속 실행합니다. 앱을 한 번 더 실행하면 큐가 비어 있으며 큐를 삭제하라는 메시지가 표시됩니다.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

1. 큐 만들기
1. 큐에서 메시지 추가 및 제거
1. Azure 스토리지 큐 삭제

자세한 내용은 Azure 큐 빠른 시작을 확인하세요.

> [!div class="nextstepaction"]
> [포털용 큐 빠른 시작](storage-quickstart-queues-portal.md)

- [.NET용 큐 빠른 시작](storage-quickstart-queues-dotnet.md)
- [Java용 큐 빠른 시작](storage-quickstart-queues-java.md)
- [Python용 큐 빠른 시작](storage-quickstart-queues-python.md)
- [JavaScript용 큐 빠른 시작](storage-quickstart-queues-nodejs.md)
