---
title: 컨테이너 및 blob에 대 한 공용 읽기 액세스 관리
titleSuffix: Azure Storage
description: 컨테이너와 Blob에서 익명 액세스를 사용하도록 설정하는 방법 및 프로그래밍 방식으로 액세스하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4d9a54c220861b19d67b07998e609ee72897446a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892485"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>컨테이너 및 Blob에 대한 익명 읽기 권한 관리

컨테이너 및 Azure Blob Storage의 해당 Blob에 대한 익명의 공용 읽기 권한을 사용하도록 설정할 수 있습니다. 이렇게 하면 계정 키를 공유하지 않고 공유 액세스 서명(SAS)을 요구하지 않고도 이러한 리소스에 대해 읽기 전용 권한을 부여할 수 있습니다.

공용 읽기 권한은 특정 Blob을 항상 익명 읽기 권한에 사용할 수 있게 하려는 경우에 가장 적합합니다. 보다 세부적인 제어를 위해 공유 액세스 서명을 만들 수 있습니다. 공유 액세스 서명을 사용하면 특정 기간 동안 다양한 권한을 사용하여 제한된 액세스를 제공할 수 있습니다. 공유 액세스 서명 만들기에 대한 자세한 내용은 [Azure Storage에서 SAS(공유 액세스 서명) 사용](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>컨테이너 및 Blob에 익명의 사용자 권한 부여

기본적으로, 컨테이너와 컨테이너 내의 모든 Blob은 적절한 권한이 부여된 사용자만 액세스할 수 있습니다. 익명 사용자에게 컨테이너와 해당 Blob에 대한 읽기 권한을 부여하려면 컨테이너 퍼블릭 액세스 수준을 설정할 수 있습니다. 컨테이너에 대 한 공용 액세스 권한을 부여 하면 익명 사용자가 요청을 승인 하지 않고도 공개적으로 액세스할 수 있는 컨테이너 내에서 blob을 읽을 수 있습니다.

다음 권한으로 컨테이너를 구성할 수 있습니다.

- **공용 읽기 액세스 권한 없음:** 스토리지 계정 소유자만 컨테이너 및 해당 Blob에 액세스할 수 있습니다. 이것이 새로운 모든 컨테이너에 대한 기본 설정입니다.
- **Blob 전용 공용 읽기 권한:** 이 컨테이너 내의 Blob은 익명 요청을 통해 읽을 수 있지만 컨테이너 데이터는 읽을 수 없습니다. 익명 클라이언트는 컨테이너 내의 Blob을 열거할 수 없습니다.
- **컨테이너 및 해당 blob에 대 한 공용 읽기 권한:** 익명 요청을 통해 모든 컨테이너 및 blob 데이터를 읽을 수 있습니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 스토리지 계정 내에서 컨테이너를 열거할 수는 없습니다.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Azure Portal에서 컨테이너 공용 액세스 수준을 설정 합니다.

[Azure Portal](https://portal.azure.com)에서 하나 이상의 컨테이너에 대 한 공용 액세스 수준을 업데이트할 수 있습니다.

1. Azure Portal에서 저장소 계정 개요로 이동 합니다.
1. 메뉴 블레이드의 **Blob service** 에서 **blob**을 선택 합니다.
1. 공용 액세스 수준을 설정 하려는 컨테이너를 선택 합니다.
1. **액세스 수준 변경** 단추를 사용 하 여 공용 액세스 설정을 표시 합니다.
1. **공용 액세스 수준** 드롭다운에서 원하는 공용 액세스 수준을 선택 하 고 확인 단추를 클릭 하 여 선택한 컨테이너에 변경 내용을 적용 합니다.

다음 스크린샷은 선택 된 컨테이너에 대 한 공용 액세스 수준을 변경 하는 방법을 보여 줍니다.

![포털에서 공용 액세스 수준을 설정 하는 방법을 보여 주는 스크린샷](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> 개별 blob에 대 한 공용 액세스 수준을 변경할 수 없습니다. 공용 액세스 수준은 컨테이너 수준 에서만 설정 됩니다.

### <a name="set-container-public-access-level-with-net"></a>.NET을 사용 하 여 컨테이너 공용 액세스 수준 설정

.NET 용 Azure Storage 클라이언트 라이브러리를 사용 하 여 컨테이너에 대 한 권한을 설정 하려면 먼저 다음 방법 중 하나를 호출 하 여 컨테이너의 기존 권한을 검색 합니다.

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

다음으로 **Getpermissions** 메서드에서 반환 되는 [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) 개체에 **publicaccess** 속성을 설정 합니다.

마지막으로 다음 메서드 중 하나를 호출 하 여 컨테이너의 사용 권한을 업데이트 합니다.

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

다음 예제에서는 컨테이너의 권한을 전체 공용 읽기 권한으로 설정합니다. 권한을 Blob에 대해서만 공용 읽기 권한으로 설정하려면 **PublicAccess** 속성을 **BlobContainerPublicAccessType.Blob**으로 설정합니다. 익명 사용자에 대한 모든 권한을 제거하려면 속성을 **BlobContainerPublicAccessType.Off**로 설정합니다.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>컨테이너 및 Blob에 익명으로 액세스

컨테이너 및 Blob에 익명으로 액세스하는 클라이언트는 자격 증명을 필요로 하지 않는 생성자를 사용할 수 있습니다. 다음 예에서는 컨테이너 및 blob을 익명으로 참조 하는 몇 가지 다른 방법을 보여 줍니다.

### <a name="create-an-anonymous-client-object"></a>익명 클라이언트 개체 만들기

계정에 대 한 Blob 저장소 엔드포인트을 제공 하 여 익명 액세스를 위한 새 서비스 클라이언트 개체를 만들 수 있습니다. 그러나 익명 액세스에 사용할 수 있는 해당 계정의 컨테이너 이름도 알아야 합니다.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>컨테이너를 익명으로 참조

익명으로 사용할 수 있는 컨테이너에 대한 URL이 있는 경우 해당 URL을 사용하여 컨테이너를 직접 참조할 수 있습니다.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Blob을 익명으로 참조

익명 액세스에 사용할 수 있는 Blob에 대한 URL이 있는 경우 해당 URL을 사용하여 Blob을 직접 참조할 수 있습니다.

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Storage에 대 한 액세스 권한 부여](../common/storage-auth.md)
- [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](../common/storage-sas-overview.md)
- [BLOB 서비스 REST API](/rest/api/storageservices/blob-service-rest-api)
