---
title: '빠른 시작: Azure Blob 스토리지 라이브러리 v12 - .NET'
description: 이 빠른 시작에서는 .NET용 Azure Blob 스토리지 클라이언트 라이브러리버전 12를 사용하여 Blob(개체) 스토리지에서 컨테이너 및 Blob을 만드는 방법을 알아봅니다. 그런 다음, Blob을 로컬 컴퓨터로 다운로드하는 방법과 컨테이너의 모든 Blob을 나열하는 방법을 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: fa37db7a5c63f7a5e8a84d98afbb81e007904974
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001436"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>빠른 시작: .NET용 Azure Blob 스토리지 클라이언트 라이브러리 v12

.NET용 Azure Blob 스토리지 클라이언트 라이브러리 v12 시작 Azure Blob Storage는 클라우드를 위한 Microsoft의 개체 스토리지 솔루션입니다. 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Blob Storage는 대량의 비정형 데이터를 저장하도록 최적화되어 있습니다.

.NET용 Azure Blob 스토리지 클라이언트 라이브러리 v12를 사용하여 다음을 수행합니다.

* 컨테이너 만들기
* Azure Storage에 Blob 업로드
* 컨테이너의 모든 blob 나열
* 로컬 컴퓨터에 blob 다운로드
* 컨테이너 삭제

추가 리소스:

* [API 참조 설명서](/dotnet/api/azure.storage.blobs)
* [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [패키지(NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [샘플](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* Azure Storage 계정 - [스토리지 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 현재 운영 체제의 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)입니다. 런타임이 아니라 SDK가 있어야 합니다.

## <a name="setting-up"></a>설치

이 섹션에서는 .NET용 Azure Blob 스토리지 클라이언트 라이브러리 v12를 사용하는 프로젝트를 준비합니다.

### <a name="create-the-project"></a>프로젝트 만들기

*BlobQuickstartV12*라는 .NET Core 애플리케이션을 만듭니다.

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 *BlobQuickstartV12*라는 새 콘솔 앱을 만듭니다. 이 명령은 *Program.cs*라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. 새로 만든 *BlobQuickstartV12* 디렉터리로 전환합니다.

   ```console
   cd BlobQuickstartV12
   ```

1. *BlobQuickstartV12* 디렉터리에서 *data*라는 다른 디렉터리를 만듭니다. 여기에서 Blob 데이터 파일이 생성되고 저장됩니다.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에서 `dotnet add package` 명령을 사용하여 .NET용 Azure Blob 스토리지 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 편집기에서 *Program.cs* 파일 열기
1. `Console.WriteLine("Hello World!");` 문 제거
1. `using` 지시문 추가
1. 비동기 코드를 지원하도록 `Main` 메서드 선언 업데이트

코드는 다음과 같습니다.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>개체 모델

Azure Blob Storage는 대량의 비정형 데이터를 저장하도록 최적화되어 있습니다. 비정형 데이터는 텍스트 또는 이진 데이터와 같은 특정 데이터 모델 또는 정의를 따르지 않는 데이터입니다. Blob Storage는 다음 세 가지 유형의 리소스를 제공합니다.

* 스토리지 계정
* 스토리지 계정의 컨테이너
* 컨테이너의 blob

다음 다이어그램에서는 이러한 리소스 간의 관계를 보여줍니다.

![Blob Storage 아키텍처 다이어그램](./media/storage-blobs-introduction/blob1.png)

다음 .NET 클래스를 사용하여 이러한 리소스와 상호 작용합니다.

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): `BlobServiceClient` 클래스를 사용하여 Azure Storage 리소스 및 blob 컨테이너를 조작할 수 있습니다.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): `BlobContainerClient` 클래스를 사용하여 Azure Storage 컨테이너 및 해당 blob을 조작할 수 있습니다.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): `BlobClient` 클래스를 사용하여 Azure Storage blob을 조작할 수 있습니다.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): `BlobDownloadInfo` 클래스는 blob 다운로드에서 반환된 속성 및 콘텐츠를 나타냅니다.

## <a name="code-examples"></a>코드 예제

이 예제 코드 조각은 .NET용 Azure Blob Storage 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여 줍니다.

* [연결 문자열 가져오기](#get-the-connection-string)
* [컨테이너 만들기](#create-a-container)
* [컨테이너에 Blob 업로드](#upload-blobs-to-a-container)
* [컨테이너의 Blob 나열](#list-the-blobs-in-a-container)
* [Blob 다운로드](#download-blobs)
* [컨테이너 삭제](#delete-a-container)

### <a name="get-the-connection-string"></a>연결 문자열 가져오기

아래 코드에서는 [스토리지 연결 문자열 구성](#configure-your-storage-connection-string) 섹션에서 만든 환경 변수에서 스토리지 계정에 대한 연결 문자열을 검색합니다.

다음 코드를 `Main` 메서드 내에 추가합니다.

```csharp
Console.WriteLine("Azure Blob storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>컨테이너 만들기

새 컨테이너의 이름을 결정합니다. 아래 코드는 컨테이너 이름이 고유해지도록 이름에 GUID 값을 추가합니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름 지정에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) 클래스의 인스턴스를 만듭니다. 그런 다음, [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) 메서드를 호출하여 스토리지 계정에 컨테이너를 만듭니다.

이 코드를 `Main` 메서드의 끝에 추가합니다.

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>컨테이너에 Blob 업로드

다음 코드 조각을 실행합니다.

1. 로컬 *data* 디렉터리에 텍스트 파일을 만듭니다.
1. [컨테이너 만들기](#create-a-container) 섹션에서 컨테이너에 대해 [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) 메서드를 호출하여 [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) 개체에 대한 참조를 가져옵니다.
1. [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) 메서드를 호출하여 로컬 텍스트 파일을 Blob에 업로드합니다. 이 메서드는 Blob이 없는 경우 만들고, Blob이 있는 경우 덮어씁니다.

이 코드를 `Main` 메서드의 끝에 추가합니다.

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream, true);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) 메서드를 호출하여 컨테이너의 blob을 나열합니다. 이 경우 하나의 Blob만 컨테이너에 추가되었으므로 나열된 작업은 하나의 해당 Blob만 반환합니다.

이 코드를 `Main` 메서드의 끝에 추가합니다.

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Blob 다운로드

[DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) 메서드를 호출하여 이전에 만든 blob을 다운로드합니다. 예제 코드는 로컬 파일 시스템에서 두 파일을 볼 수 있도록 파일 이름에 "DOWNLOADED" 접미사를 추가합니다.

이 코드를 `Main` 메서드의 끝에 추가합니다.

```csharp
// Download the blob to a local file
// Append the string "DOWNLOADED" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOADED.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
{
    await download.Content.CopyToAsync(downloadFileStream);
    downloadFileStream.Close();
}
```

### <a name="delete-a-container"></a>컨테이너 삭제

다음 코드는 [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)를 사용하여 전체 컨테이너를 삭제하여 앱이 만든 리소스를 정리합니다. 또한 앱에서 만든 로컬 파일도 삭제합니다.

앱은 blob, 컨테이너 및 로컬 파일을 삭제하기 전에 `Console.ReadLine`을 호출하여 사용자 입력을 일시 중지합니다. 리소스를 삭제하기 전에 실제로 올바르게 만들어졌는지 확인하는 것이 좋습니다.

이 코드를 `Main` 메서드의 끝에 추가합니다.

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>코드 실행

이 앱은 로컬 *data* 폴더에 테스트 파일을 만들고 Blob 스토리지에 업로드합니다. 그런 다음, 컨테이너에 Blob을 나열하고 이전 파일과 새 파일을 비교할 수 있도록 새 이름으로 해당 파일을 다운로드합니다.

애플리케이션 디렉터리로 이동한 다음, 애플리케이션을 빌드하고 실행합니다.

```console
dotnet build
```

```console
dotnet run
```

앱의 출력은 다음 예제 출력과 유사합니다.

```output
Azure Blob storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

정리 프로세스를 시작하기 전에 *data* 폴더에서 두 파일을 확인합니다. 이 파일을 열어 동일한지 확인할 수 있습니다.

파일을 확인한 후에 **Enter** 키를 눌러 테스트 파일을 삭제하고 데모를 완료합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 .NET을 사용하여 Blob을 업로드, 다운로드 및 나열하는 방법을 배웠습니다.

Blob 스토리지 샘플 앱을 보려면 다음을 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Blob 스토리지 SDK v12 .NET 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* 자습서, 샘플, 빠른 시작 및 기타 설명서는 [.NET 및 .NET Core 개발자용 Azure](/dotnet/azure/)를 참조하세요.
* .NET Core에 대해 자세히 알아보려면 [10분 안에 .NET 시작](https://www.microsoft.com/net/learn/get-started/)을 참조하세요.
