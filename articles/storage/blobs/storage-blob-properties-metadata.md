---
title: .NET-Azure Storage를 사용 하 여 blob에 대 한 속성 및 메타 데이터 관리
description: .NET 클라이언트 라이브러리를 사용 하 여 Azure Storage 계정의 blob에 시스템 속성을 설정 및 검색 하 고 사용자 지정 메타 데이터를 저장 하는 방법을 알아봅니다.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/12/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: b1501b61e930b7554063356335b967583c0a3ff5
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008440"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>.NET을 사용 하 여 blob 속성 및 메타 데이터 관리

Blob은 포함 된 데이터 외에 시스템 속성과 사용자 정의 메타 데이터를 지원 합니다. 이 문서에서는 [.net 용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)를 사용 하 여 시스템 속성 및 사용자 정의 메타 데이터를 관리 하는 방법을 보여 줍니다.

## <a name="about-properties-and-metadata"></a>속성 및 메타 데이터 정보

- **시스템 속성**: 각 Blob 저장소 리소스에 시스템 속성이 있습니다. 그 중 일부를 읽거나 설정할 수 있지만 나머지는 읽기 전용입니다. 일부 시스템 속성은 내부적으로 특정 표준 HTTP 헤더에 해당합니다. .NET 용 Azure Storage 클라이언트 라이브러리는 이러한 속성을 유지 관리 합니다.

- **사용자 정의 메타 데이터**: 사용자 정의 메타 데이터는 Blob 저장소 리소스에 대해 지정 하는 하나 이상의 이름-값 쌍으로 구성 됩니다. 메타 데이터를 사용 하 여 리소스와 함께 추가 값을 저장할 수 있습니다. 메타 데이터 값은 자신만의 목적 으로만 사용 되며 리소스의 동작 방식에는 영향을 주지 않습니다.

> [!NOTE]
> 또한 blob 인덱스 태그는 Azure Blob storage 리소스와 함께 임의의 사용자 정의 키/값 특성을 저장 하는 기능을 제공 합니다. 메타 데이터와 마찬가지로 blob 인덱스 태그만 자동으로 인덱싱되어 네이티브 blob service에 의해 검색 가능 하 게 됩니다. Azure Search와 같은 별도의 서비스를 사용 하지 않으면 메타 데이터를 인덱싱하고 쿼리할 수 없습니다.
>
> 이 기능에 대해 자세히 알아보려면 [blob 인덱스를 사용 하 여 Azure blob storage에서 데이터 관리 및 찾기 (미리 보기)](storage-manage-find-blobs.md)를 참조 하세요.

## <a name="set-and-retrieve-properties"></a>속성 설정 및 검색

다음 코드 예에서는 `ContentType` `ContentLanguage` blob에 대 한 및 시스템 속성을 설정 합니다.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Blob에 대 한 속성을 설정 하려면 [SetHttpHeaders](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheaders) 또는 [SetHttpHeadersAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheadersasync)를 호출 합니다. 명시적으로 설정 되지 않은 속성은 모두 지워집니다. 다음 코드 예제에서는 먼저 blob에 대 한 기존 속성을 가져온 다음이를 사용 하 여 업데이트 되지 않는 헤더를 채웁니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_SetBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
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

다음 코드 예제에서는 blob의 시스템 속성을 가져오고 일부 값을 표시 합니다.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Blob storage 리소스에 대 한 메타 데이터 및 속성 값 검색은 2 단계 프로세스입니다. 이러한 값을 읽으려면 먼저 또는 메서드를 호출 하 여 명시적으로 인출 해야 `FetchAttributes` 합니다 `FetchAttributesAsync` . 이 규칙의 예외는 `Exists` 및 `ExistsAsync` 메서드가 내부적으로 적절 한 메서드를 호출 하는 것입니다 `FetchAttributes` . 이러한 메서드 중 하나를 호출 하는 경우에도를 호출할 필요가 없습니다 `FetchAttributes` .

> [!IMPORTANT]
> 저장소 리소스에 대 한 속성 또는 메타 데이터 값이 채워지지 않은 경우 코드에서 또는 메서드를 호출 하는지 확인 `FetchAttributes` `FetchAttributesAsync` 합니다.

Blob 속성을 검색 하려면 `FetchAttributes` `FetchAttributesAsync` blob에서 또는 메서드를 호출 하 여 속성을 채웁니다 `Properties` .

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
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

## <a name="set-and-retrieve-metadata"></a>메타 데이터 설정 및 검색

Blob 또는 컨테이너 리소스에 하나 이상의 이름-값 쌍으로 메타 데이터를 지정할 수 있습니다. 메타 데이터를 설정 하려면 리소스의 컬렉션에 이름-값 쌍을 추가 `Metadata` 합니다. 그런 다음, 다음 메서드 중 하나를 호출 하 여 값을 작성 합니다.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [SetMetadata](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadataasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)
---

메타 데이터 이름/값 쌍은 유효한 HTTP 헤더 이며 HTTP 헤더를 관리 하는 모든 제한 사항을 준수 해야 합니다. 메타 데이터 이름은 유효한 HTTP 헤더 이름 및 유효한 c # 식별자 여야 하며, ASCII 문자만 포함할 수 있고 대/소문자를 구분 하지 않는 것으로 처리 되어야 합니다. ASCII가 아닌 문자를 포함 하는 메타 데이터 값을 [Base64 인코딩](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) 또는 [URL 인코딩합니다](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) .

메타데이터의 이름은 C# 식별자에 대한 명명 규칙을 준수해야 합니다. 메타 데이터 이름은 생성 될 때 사용 되는 대/소문자를 유지 하지만 설정 하거나 읽을 때 대/소문자를 구분 하지 않습니다. 동일한 이름을 사용 하는 두 개 이상의 메타 데이터 헤더가 리소스에 대해 제출 되는 경우 Azure Blob storage는 HTTP 오류 코드 400 (잘못 된 요청)을 반환 합니다.

다음 코드 예제에서는 blob에 메타 데이터를 설정 합니다. 컬렉션의 메서드를 사용 하 여 하나의 값을 설정 `Add` 합니다. 다른 값은 암시적 키/값 구문을 사용하여 설정됩니다.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
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

다음 코드 예제에서는 blob에 대 한 메타 데이터를 읽습니다.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

메타 데이터를 검색 하려면 blob 또는 컨테이너에서 [GetProperties](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getproperties) 또는 [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getpropertiesasync) 메서드를 호출 하 여 [메타 데이터](/dotnet/api/azure.storage.blobs.models.blobproperties.metadata) 컬렉션을 채운 다음 아래 예제와 같이 값을 읽습니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

메타 데이터를 검색 하려면 `FetchAttributes` `FetchAttributesAsync` 다음 예제와 같이 blob 또는 컨테이너에서 또는 메서드를 호출 하 여 컬렉션을 채운 `Metadata` 다음 값을 읽습니다.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>참고 항목

- [Blob 속성 설정 작업](/rest/api/storageservices/set-blob-properties)
- [Blob 속성 가져오기 작업](/rest/api/storageservices/get-blob-properties)
- [Blob 메타 데이터 작업 설정](/rest/api/storageservices/set-blob-metadata)
- [Blob 메타 데이터 가져오기 작업](/rest/api/storageservices/get-blob-metadata)
