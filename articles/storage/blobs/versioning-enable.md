---
title: Blob 버전 관리 설정 및 관리
titleSuffix: Azure Storage
description: Azure Portal 또는 Azure Resource Manager 템플릿을 사용 하 여 blob 버전 관리를 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 1df7afb5a029ff7770a64d6bf698a462c8ab9735
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230673"
---
# <a name="enable-and-manage-blob-versioning"></a>Blob 버전 관리 설정 및 관리

Blob 저장소 버전 관리를 사용 하도록 설정 하 여 이전 버전의 개체를 자동으로 유지 관리할 수 있습니다.  Blob 버전 관리를 사용 하는 경우 데이터를 잘못 수정 하거나 삭제 한 경우 이전 버전의 blob을 복원 하 여 데이터를 복구할 수 있습니다.

이 문서에서는 Azure Portal 또는 Azure Resource Manager 템플릿을 사용 하 여 저장소 계정에 대 한 blob 버전 관리를 사용 하거나 사용 하지 않도록 설정 하는 방법을 보여 줍니다. Blob 버전 관리에 대해 자세히 알아보려면 [blob 버전 관리](versioning-overview.md)를 참조 하세요.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>BLOB 버전 관리 사용

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 blob 버전 관리를 사용 하도록 설정 하려면

1. 포털에서 저장소 계정으로 이동 합니다.
1. **Blob service**에서 **데이터 보호**를 선택 합니다.
1. **버전 관리** 섹션에서 **사용**을 선택 합니다.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Azure Portal에서 blob 버전 관리를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

# <a name="template"></a>[템플릿](#tab/template)

템플릿에서 blob 버전 관리를 사용 하도록 설정 하려면 **Isversioningenabled** 속성을 **true**로 설정 하 여 템플릿을 만듭니다. 다음 단계에서는 Azure Portal에서 템플릿을 만드는 방법을 설명 합니다.

1. Azure Portal에서 **리소스 만들기**를 선택 합니다.
1. **Marketplace 검색**에서 **템플릿 배포**를 입력 하 고 **enter**키를 누릅니다.
1. **템플릿 배포**를 선택 하 고 **만들기**를 선택한 다음 **편집기에서 사용자 고유의 템플릿 빌드**를 선택 합니다.
1. 템플릿 편집기에서 다음 JSON을 붙여넣습니다. `<accountName>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.
1. 템플릿을 저장하는 경우
1. 계정에 대 한 리소스 그룹을 지정한 다음 **구매** 단추를 선택 하 여 템플릿을 배포 하 고 blob 버전 관리를 사용 하도록 설정 합니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Azure Portal 템플릿을 사용 하 여 리소스를 배포 하는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 리소스 배포](../../azure-resource-manager/templates/deploy-portal.md)를 참조 하세요.

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>새 버전을 트리거하기 위해 blob 수정

다음 코드 예제에서는 .NET 버전 [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) 이상 버전의 Azure Storage 클라이언트 라이브러리를 사용 하 여 새 버전 만들기를 트리거하는 방법을 보여 줍니다. 이 예를 실행 하기 전에 저장소 계정에 대 한 버전 관리를 사용 하도록 설정 했는지 확인 합니다.

이 예에서는 블록 blob을 만든 다음 blob의 메타 데이터를 업데이트 합니다. Blob의 메타 데이터를 업데이트 하면 새 버전이 생성 됩니다. 이 예에서는 초기 버전 및 현재 버전을 검색 하 고 현재 버전에만 메타 데이터가 포함 되어 있음을 보여 줍니다.

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>다음 단계

- [Blob 버전 관리](versioning-overview.md)
- [Azure Storage Blob에 대한 일시 삭제](soft-delete-overview.md)
