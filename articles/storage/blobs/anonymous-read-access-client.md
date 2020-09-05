---
title: .NET을 사용 하 여 공용 컨테이너 및 blob에 익명으로 액세스
titleSuffix: Azure Storage
description: .NET 용 Azure Storage 클라이언트 라이브러리를 사용 하 여 공용 컨테이너 및 blob에 익명으로 액세스할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-csharp
ms.openlocfilehash: 1ba36c7128f72f5181f3fed53f5615ab464e3325
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013540"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>.NET을 사용 하 여 공용 컨테이너 및 blob에 익명으로 액세스

Azure Storage는 컨테이너 및 blob에 대 한 선택적 공용 읽기 액세스를 지원 합니다. 클라이언트는 Azure Storage에 대 한 데이터 액세스를 지 원하는 다른 도구 및 유틸리티를 사용 하 여 Azure Storage 클라이언트 라이브러리를 사용 하 여 공용 컨테이너 및 blob에 익명으로 액세스할 수 있습니다.

이 문서에서는 .NET에서 공용 컨테이너 또는 blob에 액세스 하는 방법을 보여 줍니다. 컨테이너에 대 한 익명 읽기 액세스를 구성 하는 방법에 대 한 자세한 내용은 [컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 구성](anonymous-read-access-configure.md)을 참조 하세요. 저장소 계정에 대 한 모든 익명 액세스를 방지 하는 방법에 대 한 자세한 내용은 [컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 방지](anonymous-read-access-prevent.md)를 참조 하세요.

컨테이너 및 Blob에 익명으로 액세스하는 클라이언트는 자격 증명을 필요로 하지 않는 생성자를 사용할 수 있습니다. 다음 예에서는 컨테이너 및 blob을 익명으로 참조 하는 몇 가지 다른 방법을 보여 줍니다.

## <a name="create-an-anonymous-client-object"></a>익명 클라이언트 개체 만들기

계정에 대 한 Blob 저장소 끝점을 제공 하 여 익명 액세스를 위한 새 서비스 클라이언트 개체를 만들 수 있습니다. 그러나 익명 액세스에 사용할 수 있는 해당 계정의 컨테이너 이름도 알아야 합니다.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

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

---

## <a name="reference-a-container-anonymously"></a>컨테이너를 익명으로 참조

익명으로 사용할 수 있는 컨테이너에 대한 URL이 있는 경우 해당 URL을 사용하여 컨테이너를 직접 참조할 수 있습니다.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

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

---

## <a name="reference-a-blob-anonymously"></a>Blob을 익명으로 참조

익명 액세스에 사용할 수 있는 Blob에 대한 URL이 있는 경우 해당 URL을 사용하여 Blob을 직접 참조할 수 있습니다.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>다음 단계

- [컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 구성](anonymous-read-access-configure.md)
- [컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 차단](anonymous-read-access-prevent.md)
- [Azure Storage에 대한 액세스 권한 부여](../common/storage-auth.md)
