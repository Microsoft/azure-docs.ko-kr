---
title: .NET-Azure Storage를 사용 하 여 blob 컨테이너 만들기 또는 삭제
description: .NET 클라이언트 라이브러리를 사용 하 여 Azure Storage 계정에서 blob 컨테이너를 만들거나 삭제 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/22/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a17ab97dbfa1819154695f4c287b59db90f34334
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018997"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>.NET을 사용 하 여 Azure Storage에서 컨테이너 만들기 또는 삭제

Azure Storage blob은 컨테이너로 구성 됩니다. Blob을 업로드 하려면 먼저 컨테이너를 만들어야 합니다. 이 문서에서는 [.net 용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)를 사용 하 여 컨테이너를 만들고 삭제 하는 방법을 보여 줍니다.

## <a name="name-a-container"></a>컨테이너 이름

컨테이너 이름은 컨테이너 또는 해당 blob의 주소를 지정할 때 사용 되는 고유 URI의 일부를 형성 하므로 올바른 DNS 이름 이어야 합니다. 컨테이너의 이름을 지정할 때 다음 규칙을 따릅니다.

- 컨테이너 이름은 3 ~ 007e; 63 자까지 가능 합니다.
- 컨테이너 이름은 문자 또는 숫자로 시작 해야 하며 소문자, 숫자 및 대시 (-) 문자만 포함할 수 있습니다.
- 컨테이너 이름에 연속 된 대시 문자를 두 개 이상 사용할 수 없습니다.

컨테이너의 URI는 다음 형식으로 되어 있습니다.

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너를 만들려면 다음 메서드 중 하나를 호출 합니다.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [만들기](/dotnet/api/azure.storage.blobs.blobcontainerclient.create)
- [CreateAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.createasync)
- [CreateIfNotExists](/dotnet/api/azure.storage.blobs.blobcontainerclient.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.createifnotexistsasync)

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [만들기](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)
---

같은 이름을 가진 컨테이너가 이미 있는 경우 **Create** 및 **createasync** 메서드는 예외를 throw 합니다.

**Createifnotexists** 및 **CreateIfNotExistsAsync** 메서드는 컨테이너를 만들었는지 여부를 나타내는 부울 값을 반환 합니다. 이름이 같은 컨테이너가 이미 있으면이 메서드는 **False** 를 반환 하 여 새 컨테이너가 만들어지지 않았음을 표시 합니다.

컨테이너는 저장소 계정 아래에 즉시 생성됩니다. 컨테이너를 서로 중첩할 수는 없습니다.

다음 예제에서는 컨테이너를 비동기식으로 만듭니다.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```
---

## <a name="create-the-root-container"></a>루트 컨테이너 만들기

루트 컨테이너는 저장소 계정에 대한 기본 컨테이너 역할을 합니다. 각 저장소 계정에는 *$root*이름 이어야 하는 하나의 루트 컨테이너가 있을 수 있습니다. 루트 컨테이너를 명시적으로 만들거나 삭제 해야 합니다.

루트 컨테이너 이름을 포함 하지 않고 루트 컨테이너에 저장 된 blob을 참조할 수 있습니다. 루트 컨테이너를 사용 하면 저장소 계정 계층의 최상위 수준에서 blob을 참조할 수 있습니다. 예를 들어 다음과 같은 방식으로 루트 컨테이너에 있는 blob을 참조할 수 있습니다.

`https://myaccount.blob.core.windows.net/default.html`

다음 예제에서는 루트 컨테이너를 동기적으로 만듭니다.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateRootContainer":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```
---

## <a name="delete-a-container"></a>컨테이너 삭제

.NET에서 컨테이너를 삭제 하려면 다음 방법 중 하나를 사용 합니다.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [Delete](/dotnet/api/azure.storage.blobs.blobcontainerclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexistsasync)

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Delete](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)
---

**Delete** 및 **DeleteAsync** 메서드는 컨테이너가 존재 하지 않는 경우 예외를 throw 합니다.

**Deleteifexists** 및 **DeleteIfExistsAsync** 메서드는 컨테이너가 삭제 되었는지 여부를 나타내는 부울 값을 반환 합니다. 지정 된 컨테이너가 존재 하지 않는 경우 이러한 메서드는 컨테이너를 삭제 하지 않았음을 나타내기 위해 **False** 를 반환 합니다.

컨테이너를 삭제 한 후에는 *적어도* 30 초 동안 동일한 이름의 컨테이너를 만들 수 없습니다. 동일한 이름의 컨테이너를 만들려고 하면 HTTP 오류 코드 409 (충돌)이 발생 합니다. 컨테이너 또는 컨테이너에 포함 된 blob에 대 한 다른 모든 작업은 HTTP 오류 코드 404 (찾을 수 없음)와 함께 실패 합니다.

다음 예제에서는 지정 된 컨테이너를 삭제 하 고 컨테이너가 존재 하지 않는 경우 예외를 처리 합니다.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

다음 예제에서는 지정 된 접두사로 시작 하는 모든 컨테이너를 삭제 하는 방법을 보여 줍니다.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteContainersWithPrefixAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>참고 항목

- [컨테이너 작업 만들기](/rest/api/storageservices/create-container)
- [컨테이너 삭제 작업](/rest/api/storageservices/delete-container)
