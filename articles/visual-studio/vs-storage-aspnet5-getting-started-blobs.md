---
title: Blob Storage 및 Visual Studio 연결된 서비스 시작(ASP.NET Core) | Microsoft Docs
description: Visual Studio 연결된 서비스를 사용하여 스토리지 계정을 만든 후 Visual Studio ASP.NET Core 프로젝트에서 Azure Blob Storage 사용을 시작하는 방법입니다.
services: storage
author: ghogen
manager: douge
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 388c4d5f28e87f5cfe26336771d30fa44c6f9ef0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123011"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Azure Blob Storage 및 Visual Studio 연결된 서비스 시작(ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

이 문서에서는 Visual Studio의 **연결된 서비스** 기능을 사용하여 Azure Storage 계정을 ASP.NET Core 프로젝트에서 생성하거나 참조한 후 Azure Blob Storage를 Visual Studio에서 사용하는 방법을 설명합니다. **연결된 서비스** 작업은 프로젝트의 Azure Storage에 액세스하는 데 적합한 NuGet 패키지를 설치하고 프로젝트 구성 파일에 스토리지 계정에 대한 연결 문자열을 추가합니다. (Azure Storage에 대한 일반적인 내용은 [스토리지 설명서](https://azure.microsoft.com/documentation/services/storage/)를 참조하세요.)

Azure Blob Storage는 HTTP 또는 HTTPS를 통해 전 세계 어디에서나 액세스할 수 있는 다량의 구조화되지 않은 데이터를 저장하기 위한 서비스입니다. 단일 Blob은 임의의 크기일 수 있습니다. Blob은 이미지, 오디오 및 비디오 파일, 원시 데이터, 문서 파일 등일 수 있습니다. 이 문서에서는 ASP.NET Core 프로젝트에서 Visual Studio **연결된 서비스**를 사용하여 Azure Storage 계정을 만든 후 Blob Storage를 시작하는 방법을 설명합니다.

파일이 폴더에 저장되듯이 저장소 Blob은 컨테이너에 저장됩니다. Blob을 만든 후 해당 Blob에 하나 이상의 컨테이너를 만듭니다. 예를 들어 “Scrapbook”이라는 Blob에 사진을 저장할 “images”라는 컨테이너를 만들고 오디오 파일을 저장할 “audio”라는 컨테이너를 만들 수 있습니다. 컨테이너를 만든 후 컨테이너에 개별 파일을 업로드할 수 있습니다. [빠른 시작: .NET을 사용 하 여 목록 blob 업로드, 다운로드 및](../storage/blobs/storage-quickstart-blobs-dotnet.md) 프로그래밍 방식으로 조작 하는 blob에 대 한 자세한 내용은 합니다.

Azure Storage API 중 일부는 비동기적이며 이 문서의 코드는 비동기 메서드를 사용하는 것으로 가정합니다. 자세한 내용은 [비동기 프로그래밍](https://docs.microsoft.com/dotnet/csharp/async)을 참조하세요.

## <a name="access-blob-containers-in-code"></a>코드에서 Blob 컨테이너에 액세스하기

ASP.NET Core 프로젝트에서 Blob에 프로그래밍 방식으로 액세스하려면 다음 코드(아직 없는 경우)를 추가해야 합니다.

1. 필요한 `using` 문을 추가합니다.

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. 저장소 계정 정보를 나타내는 `CloudStorageAccount` 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다.

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. `CloudBlobClient` 개체를 사용하여 저장소 계정의 기존 컨테이너를 참조할 `CloudBlobContainer` 참조를 가져옵니다.

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>코드에서 컨테이너 만들기

`CloudBlobClient`를 사용하면 `CreateIfNotExistsAsync`를 호출하여 저장소 계정에 컨테이너를 만들 수도 있습니다.

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

컨테이너 내의 파일을 모든 사용자가 사용할 수 있도록 하려면 컨테이너를 공용으로 설정합니다.

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>컨테이너에 Blob 업로드

컨테이너에 Blob 파일을 업로드하려면 컨테이너 참조를 가져온 후 이 참조를 사용하여 Blob 참조를 가져옵니다. 그런 다음 `UploadFromStreamAsync` 메서드를 호출하여 참조하는 모든 데이터 스트림을 업로드합니다. 이 작업은 기존 Blob을 덮어쓰고, Blob이 없는 경우에는 새로 만듭니다. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

컨테이너의 Blob을 나열하려면 먼저 컨테이너 참조를 가져온 다음, 해당 `ListBlobsSegmentedAsync` 메서드를 호출하여 컨테이너 내의 Blob 및/또는 디렉터리를 검색합니다. 반환된 `IListBlobItem`에 대한 다양한 속성 및 메서드에 액세스하려면 `CloudBlockBlob`, `CloudPageBlob` 또는 `CloudBlobDirectory` 개체로 캐스팅합니다. Blob 유형을 알 수 없는 경우 유형 검사를 사용하여 캐스트할 유형을 확인합니다.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

[빠른 시작: .NET을 사용 하 여 목록 blob 업로드, 다운로드 및](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) blob 컨테이너의 내용을 나열 하는 다른 방법에 대 한 합니다.

## <a name="download-a-blob"></a>Blob 다운로드

Blob을 다운로드하려면 먼저 Blob에 대한 참조를 가져온 다음 `DownloadToStreamAsync` 메서드를 호출합니다. 다음 예제에서는 `DownloadToStreamAsync` 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로 전송합니다. 그러면 이 개체를 로컬 파일에 저장할 수 있습니다.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

[빠른 시작: .NET을 사용 하 여 목록 blob 업로드, 다운로드 및](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) blob을 파일로 저장 하는 다른 방법에 대 한 합니다.

## <a name="delete-a-blob"></a>Blob 삭제

Blob을 삭제하려면 먼저 Blob에 대한 참조를 가져온 다음 `DeleteAsync` 메서드를 호출합니다.

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
