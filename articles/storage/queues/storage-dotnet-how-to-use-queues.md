---
title: .NET-Azure Storage를 사용 하 여 Azure Queue storage 시작
description: Azure 큐는 애플리케이션 구성 요소 간에 안정적인 비동기 메시징을 제공합니다. 클라우드 메시징을 사용하면 애플리케이션 구성 요소를 독립적으로 조정할 수 있습니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/08/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: e8dadc999f3bd26671b5a8ee4da26f051a822a26
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001113"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>.NET을 사용하여 Azure Queue Storage 시작

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>개요

Azure Queue Storage는 애플리케이션 구성 요소 간에 클라우드 메시징을 제공합니다. 규모에 맞게 응용 프로그램을 디자인 하는 경우 응용 프로그램 구성 요소는 독립적으로 확장 될 수 있도록 분리 되는 경우가 많습니다. 큐 저장소는 클라우드, 데스크톱, 온-프레미스 서버 또는 모바일 장치에서 실행 되는지에 관계 없이 응용 프로그램 구성 요소 간에 비동기 메시징을 제공 합니다. Queue Storage는 또한 비동기 작업 관리와 프로세스 워크플로 작성을 지원합니다.

### <a name="about-this-tutorial"></a>이 자습서 정보

이 자습서에서는 Azure Queue Storage를 사용하여 몇 가지 일반적인 시나리오에 대한 .NET 코드를 작성하는 방법을 보여 줍니다. 여기서 다루는 시나리오에는 큐 만들기 및 삭제, 큐 메시지 추가, 읽기 및 삭제가 포함됩니다.

**예상 완료 시간:** 45분

### <a name="prerequisites"></a>필수 구성 요소

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- [.NET 용 Azure Storage 공용 클라이언트 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
- [.NET 용 Azure Storage 큐 클라이언트 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
- [.NET용 Azure 구성 관리자](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
- [Azure storage 계정](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>개발 환경 설정

다음으로 이 가이드에 코드 예제를 사용할 준비가 되도록 Visual Studio에서 개발 환경을 설정합니다.

### <a name="create-a-windows-console-application-project"></a>Windows 콘솔 애플리케이션 프로젝트 만들기

Visual Studio에서 새로운 Windows 콘솔 애플리케이션을 만듭니다. 다음 단계에서는 Visual Studio 2019에서 콘솔 애플리케이션을 만드는 방법을 보여 줍니다. 이러한 단계는 다른 버전의 Visual Studio에서도 비슷합니다.

1. **파일**  >  **새로 만들기**  >  **프로젝트** 를 선택 합니다.
2. **플랫폼**  >  **창** 선택
3. **콘솔 앱 (.NET Framework)을** 선택 합니다.
4. **다음**을 선택합니다.
5. **프로젝트 이름** 필드에 응용 프로그램의 이름을 입력 합니다.
6. **만들기**를 선택합니다.

이 자습서의 모든 코드 예제는 콘솔 응용 프로그램의 **Program.cs** 파일의 **Main ()** 메서드에 추가 될 수 있습니다.

Azure 클라우드 서비스, 웹 앱, 데스크톱 및 모바일 응용 프로그램을 포함 하 여 모든 유형의 .NET 응용 프로그램에서 Azure Storage 클라이언트 라이브러리를 사용할 수 있습니다. 이 가이드에서는 편의상 콘솔 애플리케이션을 사용합니다.

### <a name="use-nuget-to-install-the-required-packages"></a>NuGet을 사용하여 필요한 패키지 설치

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

이 자습서를 완료 하려면 프로젝트에서 다음 4 개의 패키지를 참조 해야 합니다.

- [.Net 용 Azure 핵심 라이브러리](https://www.nuget.org/packages/Azure.Core/):이 패키지는 최신 .NET Azure SDK 클라이언트 라이브러리에 대 한 공유 기본 형식, 추상화 및 도우미를 제공 합니다.
- [.Net 용 공용 클라이언트 라이브러리 Azure Storage](https://www.nuget.org/packages/Azure.Storage.Common/):이 패키지는 다른 Azure Storage 클라이언트 라이브러리에서 공유 하는 인프라를 제공 합니다.
- [.Net 용 Azure Storage 큐 라이브러리](https://www.nuget.org/packages/Azure.Storage.Queues/):이 패키지를 사용 하면 클라이언트에서 액세스할 수 있는 메시지를 저장 하기 위해 Azure Storage 큐 서비스 작업할 수 있습니다.
- [.Net 용 Configuration Manager 라이브러리](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/):이 패키지는 클라이언트 응용 프로그램의 구성 파일에 대 한 액세스를 제공 합니다.

NuGet을 사용 하 여 이러한 패키지를 가져올 수 있습니다. 다음 단계를 수행합니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **NuGet 패키지 관리**를 선택 합니다.
1. **찾아보기** 선택
1. 온라인에서 "Azure. Queue"를 검색 하 고 **설치** 를 선택 하 여 저장소 클라이언트 라이브러리와 해당 종속성을 설치 합니다. 이렇게 하면 큐 라이브러리의 종속성 인 Azure. Common 및 Azure Core 라이브러리도 설치 됩니다.
1. 온라인에서 "System.Configuration.ConfigurationManager"를 검색 하 고 **설치** 를 선택 하 여 Configuration Manager를 설치 합니다.

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

이 자습서를 완료 하려면 프로젝트에서 다음 세 가지 패키지를 참조 해야 합니다.

- [.Net 용 공용 클라이언트 라이브러리 Microsoft Azure Storage](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/):이 패키지는 저장소 계정의 데이터 리소스에 대 한 프로그래밍 방식의 액세스를 제공 합니다.
- [.Net 용 Microsoft Azure Storage Queue library](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/):이 클라이언트 라이브러리를 사용 하면 클라이언트에서 액세스할 수 있는 메시지를 저장 하기 위해 Microsoft Azure Storage 큐 서비스 작업할 수 있습니다.
- [.NET용 Microsoft Azure 구성 관리자 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): 이 패키지는 애플리케이션을 실행하는 위치에 관계없이 구성 파일에서 연결 문자열을 구문 분석하기 위한 클래스를 제공합니다.

NuGet을 사용 하 여 이러한 패키지를 가져올 수 있습니다. 다음 단계를 수행합니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **NuGet 패키지 관리**를 선택 합니다.
1. **찾아보기** 선택
1. 온라인에서 "Microsoft. Azure. Queue"를 검색 하 고 **설치** 를 선택 하 여 저장소 클라이언트 라이브러리와 해당 종속성을 설치 합니다. 이렇게 하면 큐 라이브러리의 종속성 인 Microsoft Azure. Storage. Common library도 설치 됩니다.
1. 온라인에서 "Microsoft.Azure.ConfigurationManager"를 검색 하 고 **설치** 를 선택 하 여 Azure Configuration Manager를 설치 합니다.

---

> [!NOTE]
> Storage 클라이언트 라이브러리 패키지는 [.net 용 AZURE SDK](https://azure.microsoft.com/downloads/)에도 포함 되어 있습니다. 그러나 NuGet에서 저장소 클라이언트 라이브러리를 설치 하 여 항상 최신 버전이 있는지 확인 하는 것이 좋습니다.
>
> .NET 용 저장소 클라이언트 라이브러리의 ODataLib 종속성은 WCF Data Services 아니라 NuGet에서 사용 가능한 ODataLib 패키지에 의해 확인 됩니다. ODataLib 라이브러리는 직접 다운로드하거나 NuGet을 통해 코드 프로젝트에서 참조할 수 있습니다. 저장소 클라이언트 라이브러리에서 사용 되는 특정 ODataLib 패키지는 [OData](https://nuget.org/packages/Microsoft.Data.OData/), [Edm](https://nuget.org/packages/Microsoft.Data.Edm/)및 [공간](https://nuget.org/packages/System.Spatial/)입니다. 이러한 라이브러리는 Azure Table storage 클래스에서 사용 되지만 저장소 클라이언트 라이브러리를 사용한 프로그래밍에 대 한 종속성이 필요 합니다.

### <a name="determine-your-target-environment"></a>대상 환경 확인

이 가이드의 예제를 실행하기 위해 다음과 같은 두 가지 환경 옵션이 있습니다.

- 클라우드에서 Azure Storage 계정에 대한 코드를 실행할 수 있습니다.
- Azurite 저장소 에뮬레이터에 대해 코드를 실행할 수 있습니다. Azurite는 클라우드의 Azure Storage 계정을 에뮬레이트하는 로컬 환경입니다. Azurite는 응용 프로그램을 개발 하는 동안 코드를 테스트 하 고 디버깅 하는 데 사용할 수 있는 무료 옵션입니다. 에뮬레이터는 잘 알려진 계정 및 키를 사용합니다. 자세한 내용은 [Azurite 에뮬레이터를 사용 하 여 로컬 Azure Storage 개발 및 테스트](../common/storage-use-azurite.md)를 참조 하세요.

> [!NOTE]
> Azure Storage와 연결하여 비용이 초래되지 않도록 스토리지 에뮬레이터를 대상으로 할 수 있습니다. 그러나 클라우드에서 Azure Storage 계정을 대상으로 하도록 선택하는 경우 이 자습서를 수행하는 비용은 무시됩니다.

## <a name="get-your-storage-connection-string"></a>저장소 연결 문자열 가져오기

.NET 용 Azure Storage 클라이언트 라이브러리는 저장소 연결 문자열을 사용 하 여 저장소 서비스에 액세스 하기 위한 끝점 및 자격 증명을 구성 합니다. 자세한 내용은 [스토리지 계정 액세스 키 관리](../common/storage-account-keys-manage.md)를 참조하세요.

### <a name="copy-your-credentials-from-the-azure-portal"></a>Azure Portal에서 자격 증명 복사

샘플 코드에서 스토리지 계정에 대한 액세스 권한을 부여해야 합니다. 권한을 부여하려면 연결 문자열 형태로 스토리지 계정 자격 증명을 애플리케이션에 제공합니다. 스토리지 계정 자격 증명을 보려면:

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 스토리지 계정을 찾습니다.
3. 스토리지 계정 개요의 **설정** 섹션에서 **액세스 키**를 선택합니다. 계정 액세스 키는 물론 각 키의 전체 연결 문자열이 나타납니다.
4. **key1** 아래에서 **연결 문자열** 값을 찾고, **복사** 단추를 클릭하여 연결 문자열을 복사합니다. 다음 단계에서 연결 문자열 값을 환경 변수에 추가합니다.

    ![Azure Portal에서 연결 문자열을 복사하는 방법을 보여주는 스크린샷](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

연결 문자열에 대한 자세한 내용은 [Azure Storage에 연결 문자열 구성](../common/storage-configure-connection-string.md)을 참조하세요.

> [!NOTE]
> 스토리지 계정 키는 스토리지 계정의 루트 암호와 비슷합니다. 항상 스토리지 계정 키를 보호해야 합니다. 다른 사용자에게 배포하거나 하드 코딩하거나 다른 사람이 액세스할 수 있는 일반 텍스트 파일에 저장하지 마십시오. 손상되었다고 생각되면 Azure Portal을 사용하여 키를 다시 생성합니다.

스토리지 연결 문자열을 유지하는 가장 좋은 방법은 구성 파일 안에 있습니다. 연결 문자열을 구성 하려면 Visual Studio의 솔루션 탐색기에서 *app.config* 파일을 엽니다. 아래와 같이 `\<appSettings\>` 요소의 콘텐츠를 추가합니다. *연결 문자열* 을 포털의 저장소 계정에서 복사한 값으로 바꿉니다.

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

예를 들어, 구성 설정은 다음과 유사하게 표시됩니다.

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Azurite 저장소 에뮬레이터를 대상으로 하려면 잘 알려진 계정 이름 및 키에 매핑되는 바로 가기를 사용할 수 있습니다. 이 경우에 연결 문자열 설정은 다음과 같습니다.

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>지시문을 사용하여 추가

다음 `using` 지시문을 `Program.cs` 파일 맨 위에 추가합니다.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-service-client"></a>큐 서비스 클라이언트 만들기

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

[QueueClient](/dotnet/api/azure.storage.queues.queueclient) 클래스를 사용 하면 Queue storage에 저장 된 큐를 검색할 수 있습니다. 서비스 클라이언트를 만드는 한 가지 방법은 다음과 같습니다.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

[CloudQueueClient](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy) 클래스를 사용하면 Queue Storage에 저장된 큐를 검색할 수 있습니다. 서비스 클라이언트를 만드는 한 가지 방법은 다음과 같습니다.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

이제 데이터를 읽어 오고 Queue Storage에 데이터를 기록하는 코드를 작성할 준비가 되었습니다.

## <a name="create-a-queue"></a>큐 만들기

이 예제에서는 큐를 만드는 방법을 보여 줍니다.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>큐에 메시지 삽입

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

기존 큐에 메시지를 삽입 하려면 [SendMessage](/dotnet/api/azure.storage.queues.queueclient.sendmessage) 메서드를 호출 합니다. 메시지는 `string` (utf-8 형식) 또는 배열이 될 수 있습니다 `byte` . 다음 코드는 큐 (존재 하지 않는 경우)를 만들고 메시지를 삽입 합니다.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

기존 큐에 메시지를 삽입하려면 먼저 새 [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy)를 만듭니다. 다음으로 [Addmessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy) 메서드를 호출 합니다. 는 `CloudQueueMessage` `string` (utf-8 형식) 또는 배열에서 만들 수 있습니다 `byte` . 큐를 만들고 (없는 경우) "Hello, 세계" 라는 메시지를 삽입 하는 코드는 다음과 같습니다.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>다음 메시지 보기

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

[PeekMessages](/dotnet/api/azure.storage.queues.queueclient.peekmessages) 메서드를 호출 하 여 큐에서 메시지를 제거 하지 않고 큐에서 메시지를 피킹할 수 있습니다. *Maxmessages* 매개 변수에 대 한 값을 전달 하지 않는 경우 기본값은 하나의 메시지를 피킹 (peeking) 하는 것입니다.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

큐에서 메시지를 제거 하지 않고도 [PeekMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy) 메서드를 호출 하 여 큐의 맨 앞에 있는 메시지를 볼 수 있습니다.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>대기 중인 메시지의 콘텐츠 변경

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 다음 코드는 큐 메시지를 새로운 콘텐츠로 업데이트하고 표시 제한 시간이 60초 더 늘어나도록 설정합니다. 그러면 메시지와 연결된 작업의 상태가 저장되고 클라이언트에서 메시지에 대한 작업을 계속할 수 있는 시간이 1분 더 허용됩니다. 이 기술을 사용하여 처리 단계가 하드웨어 또는 소프트웨어 오류로 인해 실패하는 경우 처음부터 시작하지 않고도 큐 메시지에 대한 여러 단계의 워크플로를 추적할 수 있습니다. 일반적으로 다시 시도 횟수를 유지 하 고, 메시지가 *n* 번 넘게 다시 시도 되는 경우 삭제 합니다. 이 기능은 처리될 때마다 애플리케이션 오류를 트리거하는 메시지를 차단하여 보호해 줍니다.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="de-queue-the-next-message"></a>큐에서 다음 메시지 제거

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

두 단계로 큐에서 메시지를 큐에서 제거 합니다. [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages)를 호출 하면 큐에서 다음 메시지를 가져옵니다. `ReceiveMessages`에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 [DeleteMessage](/dotnet/api/azure.storage.queues.queueclient.deletemessage)도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드 `DeleteMessage` 는 메시지가 처리 된 직후에 호출 됩니다.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. [GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy)를 호출하면 큐에서 다음 메시지를 가져올 수 있습니다. `GetMessage`에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 [DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy)도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드 `DeleteMessage` 는 메시지가 처리 된 직후에 호출 됩니다.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>일반적인 Queue Storage API와 함께 Async-Await 패턴 사용

이 예제에서는 일반적인 Queue Storage API와 함께 Async- Await 패턴을 사용하는 방법을 보여 줍니다. 샘플의 경우 지정된 각 메서드의 비동기 버전을 호출하는데, 이 버전은 각 메서드의 *Async* 접미사로 표시됩니다. 비동기 메서드가 사용되는 경우 호출이 완료될 때까지 Async-Await 패턴이 로컬 실행을 일시 중단합니다. 이 동작은 현재 스레드가 성능 병목 현상을 방지해주는 다른 작업을 수행할 수 있게 해주며, 애플리케이션의 전반적인 응답성을 향상시킵니다. .NET에서 비동기 대기 패턴을 사용 하는 방법에 대 한 자세한 내용은 [async And wait (c # 및 Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) 를 참조 하세요.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="leverage-additional-options-for-de-queuing-messages"></a>큐에서 메시지를 제거하는 추가 옵션 활용

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

다음 코드 예제에서는 [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) 메서드를 사용 하 여 한 번 호출에 20 개의 메시지를 가져옵니다. 그런 다음에 `foreach` 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다. 5 분은 모든 메시지에 대해 동시에 시작 되므로,에 대 한 호출 이후로 5 분이 경과 된 후에는 `ReceiveMessages` 삭제 되지 않은 모든 메시지가 다시 표시 됩니다.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

다음 코드 예제는 [GetMessages](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy) 메서드를 사용하여 한 번 호출에 20개의 메시지를 가져옵니다. 그런 다음에 `foreach` 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다. 5 분은 모든 메시지에 대해 동시에 시작 되므로,에 대 한 호출 이후로 5 분이 경과 된 후에는 `GetMessages` 삭제 되지 않은 모든 메시지가 다시 표시 됩니다.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>큐 길이 가져오기

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. [GetProperties](/dotnet/api/azure.storage.queues.queueclient.getproperties) 메서드는 메시지 수를 포함 하 여 큐 속성을 검색 하도록 큐 서비스에 요청 합니다. [ApproximateMessagesCount](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) 속성은 큐에 있는 메시지의 대략적인 수를 포함 합니다. 이 숫자는 큐에 있는 실제 메시지 수보다 작지 않으며 더 높을 수 있습니다.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. [FetchAttributes](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy) 메서드는 메시지 수를 포함하여 큐 특성을 검색하도록 큐 서비스에 요청합니다. [ApproximateMessageCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy) 속성은 `FetchAttributes` 큐 서비스를 호출 하지 않고 메서드에서 검색 된 마지막 값을 반환 합니다.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>큐 삭제

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

큐 및 해당 큐의 모든 메시지를 삭제하려면 큐 개체의 [Delete](/dotnet/api/azure.storage.queues.queueclient.delete) 메서드를 호출합니다.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

큐 및 해당 큐의 모든 메시지를 삭제하려면 큐 개체의 [Delete](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy) 메서드를 호출합니다.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>다음 단계

이제 Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 스토리지 작업에 대해 알아보세요.

- 사용 가능한 API에 대한 자세한 내용은 큐 서비스 참조 설명서를 참조하십시오.
  - [Storage Client Library for .NET 참조](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  - [REST API 참조](https://msdn.microsoft.com/library/azure/dd179355)
- [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)를 사용하여 Azure Storage 작업을 위해 작성하는 코드를 간소화하는 방법을 알아봅니다.
- Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하십시오.
  - [.NET을 사용하여 Azure Table Storage를 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md) 하여 구조화된 데이터를 저장합니다.
  - [.NET을 사용하여 Azure Blob Storage를 시작](../blobs/storage-dotnet-how-to-use-blobs.md) 하여 구조화되지 않은 데이터를 저장합니다.
  - [.NET(C#)을 사용하여 SQL Database에 연결](../../azure-sql/database/connect-query-dotnet-core.md)하여 관계형 데이터를 저장합니다.

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
