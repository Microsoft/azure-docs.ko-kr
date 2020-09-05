---
title: .NET을 사용 하 여 blob 컨테이너에 대 한 속성 및 메타 데이터 관리
titleSuffix: Azure Storage
description: .NET 클라이언트 라이브러리를 사용 하 여 시스템 속성을 설정 및 검색 하 고 Azure Storage 계정의 blob 컨테이너에 사용자 지정 메타 데이터를 저장 하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/01/2020
ms.author: tamram
ms.custom: devx-track-csharp
ms.openlocfilehash: 115ab9767c23afd5876eb23821bd1283777cdb5e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018929"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>.NET을 사용 하 여 컨테이너 속성 및 메타 데이터 관리

Blob 컨테이너는 시스템 속성 및 사용자 정의 메타 데이터와 포함 된 데이터를 지원 합니다. 이 문서에서는 [.net 용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)를 사용 하 여 시스템 속성 및 사용자 정의 메타 데이터를 관리 하는 방법을 보여 줍니다.

## <a name="about-properties-and-metadata"></a>속성 및 메타 데이터 정보

- **시스템 속성**: 각 Blob 저장소 리소스에 시스템 속성이 있습니다. 그 중 일부를 읽거나 설정할 수 있지만 나머지는 읽기 전용입니다. 일부 시스템 속성은 내부적으로 특정 표준 HTTP 헤더에 해당합니다. .NET 용 Azure Storage 클라이언트 라이브러리는 이러한 속성을 유지 관리 합니다.

- **사용자 정의 메타 데이터**: 사용자 정의 메타 데이터는 Blob 저장소 리소스에 대해 지정 하는 하나 이상의 이름-값 쌍으로 구성 됩니다. 메타 데이터를 사용 하 여 리소스와 함께 추가 값을 저장할 수 있습니다. 메타데이터 값은 고유한 목적으로만 사용되며 리소스의 동작 방식에 영향을 주지 않습니다.

메타 데이터 이름/값 쌍은 유효한 HTTP 헤더 이므로 HTTP 헤더를 관리 하는 모든 제한 사항을 준수 해야 합니다. 메타 데이터 이름은 유효한 HTTP 헤더 이름 및 유효한 c # 식별자 여야 하며, ASCII 문자만 포함할 수 있고 대/소문자를 구분 하지 않는 것으로 처리 되어야 합니다. ASCII가 아닌 문자를 포함 하는 메타 데이터 값은 b a s e 64로 인코딩 또는 URL 인코딩해야 합니다.

## <a name="retrieve-container-properties"></a>컨테이너 속성 검색

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

컨테이너 속성을 검색 하려면 다음 메서드 중 하나를 호출 합니다.

- [GetProperties](/dotnet/api/azure.storage.blobs.blobcontainerclient.getproperties)
- [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getpropertiesasync)

다음 코드 예제에서는 컨테이너의 시스템 속성을 인출 하 고 일부 속성 값을 콘솔 창에 씁니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadContainerProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Blob storage 리소스에 대 한 속성 및 메타 데이터 값 검색은 2 단계 프로세스입니다. 이러한 값을 읽으려면 먼저 **FetchAttributes** 또는 **FetchAttributesAsync** 메서드를 호출하여 명시적으로 가져와야 합니다. 이 규칙의 예외는 **Exists** 및 **ExistsAsync** 메서드가 커버에서 적절 한 **fetchattributes** 메서드를 호출 하는 것입니다. 이러한 메서드 중 하나를 호출할 때 **Fetchattributes**호출할 필요가 없습니다.

> [!IMPORTANT]
> 스토리지 리소스의 속성 또는 메타데이터 값이 채워지지 않은 경우에는 코드가 **FetchAttributes** 또는 **FetchAttributesAsync** 메서드를 호출하는지 확인합니다.

컨테이너 속성을 검색 하려면 다음 메서드 중 하나를 호출 합니다.

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

다음 코드 예제에서는 컨테이너의 시스템 속성을 인출 하 고 일부 속성 값을 콘솔 창에 씁니다.

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Blob 또는 컨테이너 리소스에 하나 이상의 이름-값 쌍으로 메타 데이터를 지정할 수 있습니다. 메타 데이터를 설정 하려면 [IDictionary](/dotnet/api/system.collections.idictionary) 개체에 이름-값 쌍을 추가 하 고 다음 메서드 중 하나를 호출 하 여 값을 씁니다.

- [SetMetadata](/dotnet/api/azure.storage.blobs.blobcontainerclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setmetadataasync)

메타데이터의 이름은 C# 식별자에 대한 명명 규칙을 준수해야 합니다. 메타 데이터 이름은 생성 된 대/소문자를 유지 하지만 설정 하거나 읽을 때 대/소문자를 구분 하지 않습니다. 리소스에 대해 동일한 이름의 메타 데이터 헤더가 두 개 이상 제출 된 경우 Blob storage는 두 값을 구분 하 여 연결 하 고 HTTP 응답 코드 200 (OK)을 반환 합니다.

다음 코드 예제에서는 컨테이너에서 메타데이터를 설정합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddContainerMetadata":::

메타 데이터를 검색 하려면 다음 메서드 중 하나를 호출 합니다.

- [GetProperties](/dotnet/api/azure.storage.blobs.blobcontainerclient.getproperties)
- [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getpropertiesasync).

그런 다음 아래 예제와 같이 값을 읽습니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadContainerMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Blob 또는 컨테이너 리소스에 하나 이상의 이름-값 쌍으로 메타 데이터를 지정할 수 있습니다. 메타 데이터를 설정 하려면 리소스의 **메타 데이터** 컬렉션에 이름-값 쌍을 추가 하 고 다음 메서드 중 하나를 호출 하 여 값을 작성 합니다.

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

메타데이터의 이름은 C# 식별자에 대한 명명 규칙을 준수해야 합니다. 메타 데이터 이름은 생성 된 대/소문자를 유지 하지만 설정 하거나 읽을 때 대/소문자를 구분 하지 않습니다. 리소스에 대해 동일한 이름의 메타 데이터 헤더가 두 개 이상 제출 된 경우 Blob storage는 두 값을 구분 하 여 연결 하 고 HTTP 응답 코드 200 (OK)을 반환 합니다.

다음 코드 예제에서는 컨테이너에서 메타데이터를 설정합니다. 하나의 값은 컬렉션의 **추가** 메서드를 사용하여 설정됩니다. 다른 값은 암시적 키/값 구문을 사용하여 설정됩니다. 둘 다 모두 유효합니다.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

메타데이터를 검색하려면 아래 예제에 나온 것처럼 Blob 또는 컨테이너에서 **FetchAttributes** 또는 **FetchAttributesAsync** 메서드를 호출하여 **Metadata** 컬렉션을 채운 후에 값을 읽습니다.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
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

- [컨테이너 속성 가져오기 작업](/rest/api/storageservices/get-container-properties)
- [컨테이너 메타 데이터 설정 작업](/rest/api/storageservices/set-container-metadata)
- [컨테이너 메타 데이터 가져오기 작업](/rest/api/storageservices/get-container-metadata)
