---
title: Azure Storage에서 대량의 임의 데이터 다운로드 | Microsoft Docs
description: Azure SDK를 사용하여 Azure Storage 계정에서 대량의 임의 데이터를 다운로드하는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 466a61fd27fd9eeb32d004af1ab6bb43503e6233
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020731"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Azure Storage에서 대량의 임의 데이터 다운로드

이 자습서는 시리즈의 3부입니다. 이 자습서는 Azure Storage에서 대량의 데이터를 다운로드하는 방법을 보여 줍니다.

시리즈 3부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 애플리케이션 업데이트
> * 애플리케이션 실행
> * 연결 수의 유효성 검사

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 이전 스토리지 자습서: [Azure Storage에 대량의 임의 데이터를 병렬로 업로드][previous-tutorial]를 완료해야 합니다.

## <a name="remote-into-your-virtual-machine"></a>가상 머신에 원격으로 연결

 가상 머신과의 원격 데스크톱 세션을 만들려면 로컬 컴퓨터에서 다음 명령을 사용합니다. 해당 IP 주소를 가상 머신의 publicIPAddress로 바꿉니다. 가상 머신을 만들 때 사용되는 자격 증명을 묻는 메시지가 표시되면 입력합니다.

```
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>애플리케이션 업데이트

이전 자습서에서는 스토리지 계정에 파일만 업로드했습니다. 텍스트 편집기에서 `D:\git\storage-dotnet-perf-scale-app\Program.cs` 파일을 엽니다. `Main` 메서드를 다음 샘플로 바꿉니다. 이 예제에서는 업로드 작업을 주석 처리하고, 완료 시 다운로드 작업 및 스토리지 계정의 콘텐츠를 삭제하는 작업의 주석을 제거합니다.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        UploadFilesAsync().GetAwaiter().GetResult();

        // Uncomment the following line to enable downloading of files from the storage account.  This is commented out
        // initially to support the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files.
        // DownloadFilesAsync().GetAwaiter().GetResult();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.  This is commented out initially
        // As the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files has you upload only for one tutorial and download for the other. 
        if (!exception)
        {
            // DeleteExistingContainersAsync().GetAwaiter().GetResult();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

애플리케이션이 업데이트된 후 애플리케이션을 다시 빌드해야 합니다. `Command Prompt`를 열고 `D:\git\storage-dotnet-perf-scale-app`으로 이동합니다. 다음 예제에 표시된 대로 `dotnet build`를 실행하여 애플리케이션을 다시 빌드합니다.

```
dotnet build
```

## <a name="run-the-application"></a>애플리케이션 실행

이제 애플리케이션이 다시 빌드되었으므로 업데이트된 코드로 애플리케이션을 실행할 차례입니다. 아직 열지 않은 경우 `Command Prompt`를 열고 `D:\git\storage-dotnet-perf-scale-app`으로 이동합니다.

`dotnet run`를 입력하여 애플리케이션을 실행합니다.

```
dotnet run
```

애플리케이션은 **storageconnectionstring**에 지정된 스토리지 계정에 있는 컨테이너를 읽습니다. 컨테이너에 있는 [ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) 메서드를 사용하여 한 번에 Blob을 10개 반복하고 [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) 메서드를 사용하여 로컬 컴퓨터로 다운로드합니다.
다음 표에서는 다운로드된 각 Blob에 대해 정의된 [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions)를 보여 줍니다.

|속성|값|Description|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| 이 속성은 업로드된 콘텐츠의 MD5 해시를 검사하지 않도록 설정합니다. MD5 유효성 검사를 사용하지 않으면 더 빠른 전송이 생성됩니다. 그러나 전송 중인 파일의 유효성 또는 무결성을 확인하지 않습니다. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| 이 속성은 MD5 해시를 계산하고 저장할지를 결정합니다.   |

`DownloadFilesAsync` 작업은 다음 예제에 표시됩니다.

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.  Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container lazily 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

### <a name="validate-the-connections"></a>연결 유효성 검사

파일을 다운로드하는 동안 스토리지 계정에 대한 동시 연결 수를 확인할 수 있습니다. `Command Prompt`를 열고 `netstat -a | find /c "blob:https"`를 입력합니다. 이 명령은 `netstat`를 사용하여 현재 열린 연결 수를 표시합니다. 다음 예제는 자습서를 직접 실행할 때 표시되는 것과 유사한 출력을 보여 줍니다. 예제에서 볼 수 있듯이 스토리지 계정에서 무작위 파일을 다운로드할 때 280개가 넘는 연결이 열려 있었습니다.

```
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>다음 단계

시리즈 3부에서는 다음 방법을 통해 스토리지 계정에서 대량의 임의 데이터를 다운로드하는 방법에 대해 배웠습니다.

> [!div class="checklist"]
> * 애플리케이션 실행
> * 연결 수의 유효성 검사

포털에서 처리량 및 대기 시간 메트릭을 확인하려면 시리즈의 4부로 이동하세요.

> [!div class="nextstepaction"]
> [포털에서 처리량 및 대기 시간 메트릭 확인](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
