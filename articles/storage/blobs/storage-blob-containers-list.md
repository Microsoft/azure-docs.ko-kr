---
title: .NET-Azure Storage를 사용 하 여 blob 컨테이너 나열
description: .NET 클라이언트 라이브러리를 사용 하 여 Azure Storage 계정에 blob 컨테이너를 나열 하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f443cd5603e6ca0f60dc0e69b734bfa46138d476
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018946"
---
# <a name="list-blob-containers-with-net"></a>.NET을 사용 하 여 blob 컨테이너 나열

코드에서 Azure Storage 계정의 컨테이너를 나열 하는 경우 Azure Storage에서 결과가 반환 되는 방식을 관리 하는 다양 한 옵션을 지정할 수 있습니다. 이 문서에서는 [.net 용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)를 사용 하 여 컨테이너를 나열 하는 방법을 보여 줍니다.  

## <a name="understand-container-listing-options"></a>컨테이너 목록 옵션 이해

저장소 계정에 컨테이너를 나열 하려면 다음 방법 중 하나를 호출 합니다.

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

이러한 메서드에 대 한 오버 로드는 목록 작업에서 컨테이너를 반환 하는 방법을 관리 하기 위한 추가 옵션을 제공 합니다. 다음 섹션에서는 이러한 방법에 대해 설명합니다.

### <a name="manage-how-many-results-are-returned"></a>반환되는 결과 수 관리

기본적으로 목록 작업은 한 번에 최대 5,000개의 결과를 반환합니다. 더 작은 결과 집합을 반환 하려면 `maxresults` **ListContainerSegmented** 메서드 중 하나를 호출할 때 매개 변수에 0이 아닌 값을 제공 합니다.

저장소 계정에 5000 개 이상의 컨테이너가 포함 된 경우 또는 `maxresults` 목록 작업이 저장소 계정에서 컨테이너의 하위 집합을 반환 하도록 값을 지정 하는 경우 Azure Storage는 컨테이너 목록과 함께 *연속 토큰* 을 반환 합니다. 연속 토큰은 Azure Storage에서 다음 결과 세트를 검색하는 데 사용할 수 있는 불투명 값입니다.

코드에서 연속 토큰의 값을 확인하여 null인지 여부를 확인합니다. 연속 토큰이 null이면 결과 세트가 완료된 것입니다. 연속 토큰이 null이 아닌 경우 연속 토큰이 null이 될 때까지 연속 토큰을 전달 하 여 다음 결과 집합을 검색 하는 **ListContainersSegmented** 또는 **ListContainersSegmentedAsync** 를 다시 호출 합니다.

### <a name="filter-results-with-a-prefix"></a>접두사를 사용하여 결과 필터링

컨테이너 목록을 필터링하려면 `prefix` 매개 변수에 대한 문자열을 지정합니다. 접두사 문자열은 하나 이상의 문자를 포함할 수 있습니다. 그런 다음 Azure Storage은 해당 접두사로 시작 하는 이름을 가진 컨테이너만 반환 합니다.

### <a name="return-metadata"></a>메타데이터 반환

결과를 사용 하 여 컨테이너 메타 데이터를 반환 하려면 [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) 열거에 대 한 **메타 데이터** 값을 지정 합니다. Azure Storage에는 각 컨테이너가 반환 된 메타 데이터가 포함 되어 있으므로 **Fetchattributes** 메서드 중 하나를 호출 하 여 컨테이너 메타 데이터를 검색할 필요가 없습니다.

## <a name="example-list-containers"></a>예: 컨테이너 나열

다음 예제에서는 지정 된 접두사로 시작 하는 저장소 계정에서 컨테이너를 비동기적으로 나열 합니다. 이 예제에서는 한 번에 5 개 결과의 증분 컨테이너를 나열 하 고 연속 토큰을 사용 하 여 결과의 다음 세그먼트를 가져옵니다. 또한이 예제에서는 결과와 함께 컨테이너 메타 데이터를 반환 합니다.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>참고 항목

[컨테이너 나열](/rest/api/storageservices/list-containers2) 
 [Blob 리소스 열거](/rest/api/storageservices/enumerating-blob-resources)
