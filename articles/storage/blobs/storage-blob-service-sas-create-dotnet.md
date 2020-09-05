---
title: .NET을 사용 하 여 컨테이너 또는 blob에 대 한 서비스 SAS 만들기
titleSuffix: Azure Storage
description: .NET 클라이언트 라이브러리를 사용 하 여 컨테이너 또는 blob에 대 한 서비스 공유 액세스 서명 (SAS)을 만드는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/07/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 16179ae730e81ff1ff7c107e3af70b5ce24e8813
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001947"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>.NET을 사용 하 여 컨테이너 또는 blob에 대 한 서비스 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 저장소 계정 키를 사용 하 여 [.net 용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)를 통해 컨테이너 또는 blob에 대 한 서비스 SAS를 만드는 방법을 보여 줍니다.

## <a name="create-a-service-sas-for-a-blob-container"></a>Blob 컨테이너에 대 한 서비스 SAS 만들기

다음 코드 예제에서는 컨테이너에 대 한 SAS를 만듭니다. 기존에 저장된 액세스 정책의 이름을 제공하는 경우 해당 정책은 SAS와 연결됩니다. 저장 된 액세스 정책이 제공 되지 않는 경우 코드는 컨테이너에 임시 SAS를 만듭니다.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

새 [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder)을 만듭니다. 그런 다음 [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) 를 호출 하 여 SAS 토큰 문자열을 가져옵니다. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetContainerSasUri":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

컨테이너에 대 한 서비스 SAS를 만들려면 [CloudBlobContainer. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) 메서드를 호출 합니다.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}

```
---

## <a name="create-a-service-sas-for-a-blob"></a>Blob에 대 한 서비스 SAS 만들기

다음 코드 예제에서는 blob에 SAS를 만듭니다. 기존에 저장된 액세스 정책의 이름을 제공하는 경우 해당 정책은 SAS와 연결됩니다. 저장 된 액세스 정책이 제공 되지 않는 경우 코드는 blob에 임시 SAS를 만듭니다.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

새 [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder)을 만듭니다. 그런 다음 [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) 를 호출 하 여 SAS 토큰 문자열을 가져옵니다. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetBlobSasUri":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Blob에 대 한 서비스 SAS를 만들려면 [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) 메서드를 호출 합니다.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>다음 단계

- [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](../common/storage-sas-overview.md)
- [서비스 SAS 만들기](/rest/api/storageservices/create-service-sas)
