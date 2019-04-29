---
title: Blob Storage 및 Visual Studio 연결된 서비스 시작(클라우드 서비스) | Microsoft Docs
description: Visual Studio 연결된 서비스를 사용하여 스토리지 계정에 연결한 후 Visual Studio 클라우드 서비스 프로젝트에서 Azure Blob Storage 사용을 시작하는 방법입니다.
services: storage
author: ghogen
manager: douge
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 9f1ef06e0275954343c548d0f6937b9c6fbcfd18
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122943"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Azure Blob Storage 및 Visual Studio 연결된 서비스 시작(클라우드 서비스 프로젝트)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>개요
이 문서에서는 Visual Studio **연결된 서비스 추가** 대화 상자를 사용하여 Visual Studio 클라우드 서비스 프로젝트에서 Azure Storage 계정을 만들거나 참조한 후 Azure Blob Storage를 시작하는 방법을 설명합니다. Blob 컨테이너에 액세스하고 만드는 방법과 Blob 업로드, 나열 및 다운로드와 같은 일반적인 작업을 수행하는 방법을 살펴보겠습니다. 샘플은 C\#으로 작성되었으며 [.NET용 Microsoft Azure Storage 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/dn261237.aspx)를 사용합니다.

Azure Blob Storage는 HTTP 또는 HTTPS를 통해 전 세계 어디에서나 액세스할 수 있는 다량의 구조화되지 않은 데이터를 저장하기 위한 서비스입니다. 단일 Blob은 임의의 크기일 수 있습니다. Blob은 이미지, 오디오 및 비디오 파일, 원시 데이터, 문서 파일 등일 수 있습니다.

파일이 폴더에 저장되듯이 저장소 Blob은 컨테이너에 저장됩니다. 저장소를 만든 후 해당 저장소에 하나 이상의 컨테이너를 만듭니다. 예를 들어 "Scrapbook"이라는 저장소를 만든 다음 저장소에 사진을 저장할 "images"라는 컨테이너를 만들고 오디오 파일을 저장할 "audio"라는 컨테이너를 만들 수 있습니다. 컨테이너를 만든 후 컨테이너에 개별 Blob 파일을 업로드할 수 있습니다.

* 프로그래밍 방식으로 조작하는 blob에 대한 자세한 내용은 [.NET을 사용하여 Azure Blob Storage 시작](../storage/blobs/storage-dotnet-how-to-use-blobs.md)을 참조하세요.
* Azure Storage에 대한 일반 정보는 [스토리지 설명서](https://azure.microsoft.com/documentation/services/storage/)를 참조하세요.
* Azure Cloud Services에 대한 일반적인 내용은 [Cloud Services 설명서](https://azure.microsoft.com/documentation/services/cloud-services/)를 참조하세요.
* ASP.NET 애플리케이션을 프로그래밍하는 방법에 대한 자세한 내용은 [ASP.NET](https://www.asp.net)을 참조하세요.

## <a name="access-blob-containers-in-code"></a>코드에서 Blob 컨테이너에 액세스하기
클라우드 서비스 프로젝트에서 Blob에 프로그래밍 방식으로 액세스하려면 다음 항목(아직 없는 경우)을 추가해야 합니다.

1. 프로그래밍 방식으로 Azure Storage에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. 스토리지 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. 저장소 계정의 기존 컨테이너를 참조할 **CloudBlobClient** 개체를 가져옵니다.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. 특정 Blob 컨테이너를 참조하는 **CloudBlobContainer** 개체를 가져옵니다.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> 다음 섹션의 코드 앞에서 이전 절차의 모든 코드를 사용합니다.
> 
> 

## <a name="create-a-container-in-code"></a>코드에서 컨테이너 만들기
> [!NOTE]
> ASP.NET의 Azure Storage에 대한 호출을 수행하는 일부 API는 비동기적입니다. 자세한 내용은 [Async 및 Await를 사용한 비동기 프로그래밍](https://msdn.microsoft.com/library/hh191443.aspx) 을 참조하세요. 다음 예제의 코드에서는 비동기 프로그래밍 메서드를 사용한다고 가정합니다.
> 
> 

저장소 계정에서 컨테이너를 만들려면 다음 코드와 같이 **CreateIfNotExistsAsync** 호출을 추가하면 됩니다.

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


컨테이너 내의 파일을 모든 사용자가 사용할 수 있도록 하려면 다음 코드를 사용하여 컨테이너를 공용으로 설정할 수 있습니다.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


인터넷상의 누구든지 공용 컨테이너의 Blob을 볼 수 있지만 해당 액세스 키가 있는 경우에만 수정하거나 삭제할 수 있습니다.

## <a name="upload-a-blob-into-a-container"></a>컨테이너에 Blob 업로드
Azure Storage는 블록 Blob 및 페이지 Blob을 지원합니다. 대부분의 경우 블록 Blob을 사용하는 것이 좋습니다.

블록 Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 다음 이 참조를 사용하여 블록 Blob 참조를 가져옵니다. Blob 참조가 있는 경우 **UploadFromStream** 메서드를 호출하여 데이터 스트림을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다. 다음 예제에서는 컨테이너에 Blob을 업로드하는 방법을 보여 주며, 컨테이너가 이미 만들어져 있다고 가정합니다.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열
컨테이너의 Blob을 나열하려면 먼저 컨테이너 참조를 가져옵니다. 컨테이너의 **ListBlobs** 메서드를 사용하여 컨테이너 내의 Blob 및/또는 디렉터리를 검색할 수 있습니다. 반환된 **IListBlobItem**에 대한 풍부한 속성 및 메서드 집합에 액세스하려면 **CloudBlockBlob**, **CloudPageBlob** 또는 **CloudBlobDirectory** 개체로 캐스트해야 합니다. 형식을 알 수 없는 경우 형식 검사를 사용하여 캐스트할 형식을 확인할 수 있습니다. 다음 코드에서는 **photos** 컨테이너에 있는 각 항목의 URI를 검색하고 출력하는 방법을 보여 줍니다.

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
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

이전 코드 예제에 나온 것처럼 Blob 서비스에도 컨테이너 내의 디렉터리 개념이 있습니다. 따라서 폴더와 유사한 구조로 Blob을 구성할 수 있습니다. 예를 들어 **photos**라는 컨테이너에 있는 다음 블록 Blob 집합을 고려합니다.

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

이전 샘플과 같이 컨테이너에서 **ListBlobs**를 호출하면 반환된 컬렉션에 디렉터리를 나타내는 **CloudBlobDirectory** 및 **CloudBlockBlob** 개체와 최상위 수준의 Blob이 포함됩니다. 결과 출력은 다음과 같습니다.

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


선택적으로 **ListBlobs** 메서드의 **UseFlatBlobListing** 매개 변수를 **true**로 설정할 수 있습니다. 이 경우 디렉터리에 관계없이 모든 Blob이 **CloudBlockBlob**으로 반환됩니다. 다음은 **ListBlobs**호출입니다.

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

다음은 결과입니다.

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

자세한 내용은 [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx)를 참조하세요.

## <a name="download-blobs"></a>Blob 다운로드
Blob을 다운로드하려면 먼저 Blob 참조를 검색한 다음 **DownloadToStream** 메서드를 호출합니다. 다음 예제에서는 **DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로 전송한 다음 이 개체를 로컬 파일에 저장할 수 있습니다.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

**DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 텍스트 문자열로 다운로드할 수도 있습니다.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Blob 삭제
Blob을 삭제하려면 먼저 Blob 참조를 가져온 다음 **Delete** 메서드를 호출합니다.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>여러 페이지에서 비동기식으로 Blob 나열
많은 수의 Blob을 나열하거나 한 번의 나열 작업에서 반환되는 결과 수를 제어하려는 경우에는 여러 결과 페이지에 Blob을 나열할 수 있습니다. 이 예제에서는 여러 페이지에서 비동기식으로 결과를 반환하는 방법을 보여 주므로 큰 결과 집합이 반환되도록 기다리는 동안 실행이 차단되지 않습니다.

이 예제에서는 플랫 Blob 나열을 보여 주지만 **ListBlobsSegmentedAsync** 메서드의 **useFlatBlobListing** 매개 변수를 **false**로 설정하여 계층적 나열을 수행할 수도 있습니다.

샘플 메서드는 비동기 메서드를 호출하므로 앞에 **async** 키워드를 추가해야 하며 **Task** 개체를 반환해야 합니다. **ListBlobsSegmentedAsync** 메서드에 대해 지정된 await 키워드는 나열 작업이 완료될 때까지 샘플 메서드의 실행을 일시 중단합니다.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="next-steps"></a>다음 단계
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

