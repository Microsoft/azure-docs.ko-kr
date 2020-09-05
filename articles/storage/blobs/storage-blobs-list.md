---
title: .NET을 사용하여 Blob 나열 - Azure Storage
description: .NET 클라이언트 라이브러리를 사용하여 Azure Storage 계정의 컨테이너에 있는 Blob을 나열하는 방법을 알아봅니다. 코드 예제에서는 Blob이 디렉터리나 폴더로 구성된 경우에도 Blob을 플랫 목록으로 나열하거나 계층 구조로 나열하는 방법을 보여 줍니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/05/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9295b1d8a54d9c3c1a138a54f4b3706bd39227fd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89009545"
---
# <a name="list-blobs-with-net"></a>.NET을 사용하여 Blob 나열

코드에서 Blob을 나열하는 경우 Azure Storage에서 결과가 반환되는 방식을 관리하는 다양한 옵션을 지정할 수 있습니다. 각 결과 세트에서 반환할 결과 수를 지정하고 후속 세트를 검색할 수 있습니다. 이름이 해당 문자 또는 문자열로 시작하는 Blob을 반환하는 접두사를 지정할 수 있습니다. 플랫 목록 구조나 계층 구조로 Blob을 나열할 수 있습니다. 계층형 목록은 폴더로 구성된 것처럼 Blob을 반환합니다.

이 문서에서는 [.NET용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)를 사용하여 Blob을 나열하는 방법을 보여 줍니다.  

## <a name="understand-blob-listing-options"></a>Blob 목록 옵션 이해

스토리지 계정의 Blob을 나열하려면 다음 메서드 중 하나를 호출합니다.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [BlobContainerClient. GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs?view=azure-dotnet)
- [BlobContainerClient.GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync?view=azure-dotnet)
- [BlobContainerClient. GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy?view=azure-dotnet)
- [BlobContainerClient. Getblobsby3| Async](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync?view=azure-dotnet)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

컨테이너의 Blob을 나열하려면 다음 메서드 중 하나를 호출합니다.

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

이러한 메서드에 대한 오버로드는 목록 작업에서 Blob을 반환하는 방법을 관리하기 위한 추가 옵션을 제공합니다. 다음 섹션에서는 이러한 방법에 대해 설명합니다.

---

### <a name="manage-how-many-results-are-returned"></a>반환되는 결과 수 관리

기본적으로 목록 작업은 한 번에 최대 5000 개의 결과를 반환 하지만 각 나열 작업에서 반환할 결과 수를 지정할 수 있습니다. 이 문서에 제공 된 예제에서는이 작업을 수행 하는 방법을 보여 줍니다.

목록 작업에서 5000 개 이상의 blob을 반환 하거나 사용할 수 있는 blob 수가 지정한 수를 초과 하는 경우 Azure Storage는 blob 목록과 함께 *연속 토큰* 을 반환 합니다. 연속 토큰은 Azure Storage에서 다음 결과 세트를 검색하는 데 사용할 수 있는 불투명 값입니다.

코드에서 연속 토큰의 값을 확인하여 null인지 여부를 확인합니다. 연속 토큰이 null이면 결과 세트가 완료된 것입니다. 연속 토큰이 null이 아닌 경우 목록 작업을 다시 호출하고 연속 토큰을 전달하여 연속 토큰이 null이 될 때까지 다음 결과 세트를 검색합니다.

### <a name="filter-results-with-a-prefix"></a>접두사를 사용하여 결과 필터링

컨테이너 목록을 필터링하려면 `prefix` 매개 변수에 대한 문자열을 지정합니다. 접두사 문자열은 하나 이상의 문자를 포함할 수 있습니다. 그러면 Azure Storage는 이름이 해당 접두사로 시작하는 Blob만 반환합니다.

### <a name="return-metadata"></a>메타데이터 반환

결과를 사용 하 여 blob 메타 데이터를 반환할 수 있습니다. 

- .NET v12 SDK를 사용 하는 경우 [Blobtraits](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.models.blobtraits?view=azure-dotnet) 열거에 대 한 **메타 데이터** 값을 지정 합니다.

- .NET v11 SDK를 사용 하는 경우 [Bloblistingdetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) 열거에 대 한 **메타 데이터** 값을 지정 합니다. Azure Storage에는 반환된 각 Blob이 있는 메타데이터가 포함되어 있으므로 Blob 메타데이터를 검색하기 위해 이 컨텍스트에서 **FetchAttributes** 메서드 중 하나를 호출할 필요가 없습니다.

### <a name="flat-listing-versus-hierarchical-listing"></a>단순 목록 및 계층 구조 목록

Azure Storage Blob은 계층형 패러다임(예: 클래식 파일 시스템)이 아닌 플랫 패러다임으로 구성됩니다. 그러나 폴더 구조를 모방하기 위해 Blob을 *가상 디렉터리*로 구성할 수 있습니다. 가상 디렉터리는 Blob 이름의 일부를 형성하며 구분 기호 문자로 표시됩니다.

Blob을 가상 디렉터리로 구성하려면 Blob 이름에 구분 기호 문자를 사용합니다. 기본 구분 기호 문자는 정방향 슬래시(/)이지만 어떤 문자도 구분 기호로 지정할 수 있습니다.

구분 기호를 사용하여 Blob의 이름을 지정할 경우 Blob을 계층형으로 나열하도록 선택할 수 있습니다. 계층 구조 목록 작업의 경우 Azure Storage는 부모 개체 아래에 가상 디렉터리와 Blob을 반환합니다. 클래식 파일 시스템을 프로그래밍 방식으로 트래버스하는 방법과 비슷하게 목록 작업을 재귀적으로 호출하여 계층 구조를 트래버스할 수 있습니다.

## <a name="use-a-flat-listing"></a>플랫 목록 사용

기본적으로 목록 작업은 Blob을 플랫 목록으로 반환합니다. 플랫 목록에서 Blob은 가상 디렉터리로 구성되지 않습니다.

다음 예제에서는 지정된 선택적 세그먼트 크기를 사용하여 지정된 컨테이너에 있는 Blob을 나열하고 Blob 이름을 콘솔 창에 씁니다.

계정에서 계층 구조 네임 스페이스 기능을 사용 하도록 설정한 경우 디렉터리는 가상이 아닙니다. 대신, 구체적이 고 독립적인 개체입니다. 따라서 디렉터리가 길이가 0 인 blob으로 목록에 표시 됩니다.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsFlatListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
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

샘플 출력은 다음과 유사합니다.

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>계층형 목록 사용

목록 작업을 계층형으로 호출하는 경우 Azure Storage는 계층의 첫 번째 수준에서 가상 디렉터리 및 Blob을 반환합니다. 다음 디렉터리를 검색하기 위해 재귀 호출에 접두사를 전달할 수 있도록 각 가상 디렉터리의 [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) 속성이 설정됩니다.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Blob을 계층적으로 나열 하려면 [BlobContainerClient. GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy?view=azure-dotnet)또는 [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync?view=azure-dotnet) 을 호출 합니다.

다음 예에서는 지정 된 세그먼트 크기 (선택 사항)를 사용 하 여 지정 된 컨테이너의 blob을 나열 하 고 blob 이름을 콘솔 창에 씁니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsHierarchicalListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Blob을 계층형으로 나열하려면 목록 메서드의 `useFlatBlobListing` 매개 변수를 **false**로 설정합니다.

다음 예제에서는 지정된 선택적 세그먼트 크기를 사용하여 지정된 컨테이너에 있는 Blob을 나열하고 Blob 이름을 콘솔 창에 씁니다.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
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

샘플 출력은 다음과 유사합니다.

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Blob 스냅샷은 계층형 목록 작업에 나열할 수 없습니다.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>다음 단계

- [Blob 나열](/rest/api/storageservices/list-blobs)
- [Blob 리소스 열거](/rest/api/storageservices/enumerating-blob-resources)
