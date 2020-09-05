---
title: Azure Storage에 대량의 임의 데이터를 병렬로 업로드
description: Azure Storage 클라이언트를 사용하여 Azure Storage 계정에 대량의 임의 데이터를 병렬로 업로드하는 방법 알아보기
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: dd87e1a9bcff55813dff420976df58351386fb34
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "75371941"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Azure Storage에 대량의 임의 데이터를 병렬로 업로드

이 자습서는 시리즈의 2부입니다. 이 자습서에서는 Azure Storage 계정에 대량의 임의 데이터를 업로드하는 애플리케이션을 배포하는 방법을 설명합니다.

시리즈 2부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 연결 문자열 구성
> * 애플리케이션 빌드
> * 애플리케이션 실행
> * 연결 수의 유효성 검사

Azure Blob Storage는 데이터를 저장하기 위한 확장 가능한 서비스를 제공합니다. 애플리케이션 성능을 가능한 한 높게 유지하려면 Blob Storage 작동 방식을 이해하는 것이 좋습니다. Azure Blob에 대한 제한을 알고 있어야 합니다. 이러한 제한을 자세히 알아보려면 [Blob 스토리지의 확장성 및 성능 목표](../blobs/scalability-targets.md)를 방문하세요.

[파티션 이름 지정](../blobs/storage-performance-checklist.md#partitioning)은 Blob을 사용하여 고성능 애플리케이션을 설계할 때 고려한 또 다른 잠재적인 중요한 요소입니다. 크기가 4MiB보다 크거나 같은 블록의 경우 [처리량이 높은 블록 Blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)이 사용되고 파티션 이름 지정은 성능에 영향을 미치지 않습니다. 크기가 4MiB 미만인 블록의 경우 Azure Storage는 확장/축소 및 부하 분산에 범위 기준 분할 구성표를 사용합니다. 이 구성은 이름 지정 규칙 또는 접두사가 유사한 파일이 동일한 파티션으로 이동함을 의미합니다. 이 논리에는 파일이 업로드되는 컨테이너의 이름이 포함됩니다. 이 자습서에서는 임의로 생성된 콘텐츠뿐만 아니라 이름에 대한 GUID가 있는 파일을 사용합니다. 그런 다음, 임의 이름을 가진 5개의 다른 컨테이너로 업로드됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 이전 스토리지 자습서: [확장 가능한 애플리케이션에 필요한 가상 머신 및 스토리지 계정 만들기][previous-tutorial]를 완료해야 합니다.

## <a name="remote-into-your-virtual-machine"></a>가상 머신에 원격으로 연결

다음 명령을 사용하여 로컬 컴퓨터에서 가상 머신과의 원격 데스크톱 세션을 만듭니다. 해당 IP 주소를 가상 머신의 publicIPAddress로 바꿉니다. 가상 머신을 만들 때 사용되는 자격 증명을 묻는 메시지가 표시되면 입력합니다.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>연결 문자열 구성

Azure Portal에서 스토리지 계정으로 이동합니다. 스토리지 계정의 **설정** 아래에서 **액세스 키**를 선택합니다. 기본 또는 보조 키에서 **연결 문자열**을 복사합니다. 이전 단계에서 만든 가상 머신에 로그인합니다. 관리자 권한으로 **명령 프롬프트**를 열고 `setx` 명령을 `/m` 스위치와 함께 실행합니다. 이 명령은 시스템 설정 환경 변수를 저장합니다. **명령 프롬프트**를 다시 로드할 때까지 환경 변수를 사용할 수 없습니다. 다음 샘플에서는 **\<storageConnectionString\>** 를 바꿉니다.

```
setx storageconnectionstring "<storageConnectionString>" /m
```

완료되면 다른 **명령 프롬프트**를 열고, `D:\git\storage-dotnet-perf-scale-app`으로 이동한 다음, `dotnet build`를 입력하여 애플리케이션을 빌드합니다.

## <a name="run-the-application"></a>애플리케이션 실행

`D:\git\storage-dotnet-perf-scale-app`로 이동합니다.

`dotnet run`를 입력하여 애플리케이션을 실행합니다. `dotnet`을 처음 실행할 때 이 명령은 로컬 패키지 캐시를 채워 복원 속도가 개선되고 오프라인 액세스를 사용할 수 있습니다. 이 명령은 완료하는 데 1분이 걸리며 한 번만 발생합니다.

```
dotnet run
```

애플리케이션은 임의 이름이 지정된 5개의 컨테이너를 만들고 스테이징 디렉터리에 있는 파일을 스토리지 계정으로 업로드하기 시작합니다. 애플리케이션은 최소 스레드를 100으로 설정하고 [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx)을 100으로 설정하여 애플리케이션을 실행할 때 많은 수의 동시 연결이 허용되도록 합니다.

스레딩 및 연결 제한 설정을 설정하는 것 외에 [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) 메서드에 대한 [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions)는 병렬 처리를 사용하지만 MD5 해시 유효성 검사를 사용하지 않도록 구성됩니다. 파일은100mb 블록으로 업로드됩니다. 이 구성은 향상된 성능을 제공하지만, 전체 100mb 블록이 다시 시도되는 오류가 있는 것처럼 성능이 저하된 네트워크를 사용할 경우 비용이 많이 들 수 있습니다.

|속성|값|Description|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| 설정은 업로드할 때 Blob을 블록으로 나눕니다. 최고 성능을 위해 이 값은 코어 수의 8배여야 합니다. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| 이 속성은 업로드된 콘텐츠의 MD5 해시를 검사하지 않도록 설정합니다. MD5 유효성 검사를 사용하지 않으면 더 빠른 전송이 생성됩니다. 그러나 전송 중인 파일의 유효성 또는 무결성을 확인하지 않습니다.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| 이 속성은 MD5 해시를 계산하고 파일과 함께 저장할지를 결정합니다.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| 최대 10회 재시도를 사용한 2초 백오프 |요청의 재시도 정책을 결정합니다. 연결 실패가 다시 시도됩니다. 이 예제에서 [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) 정책은 2초 백오프 및 최대 10회 재시도를 사용하여 구성됩니다. 이 설정은 애플리케이션이 Blob 스토리지의 확장성 목표에 적중하기 위해 접근할 때 중요합니다. 자세한 내용은 [Azure 스토리지의 확장성 및 성능 목표](../blobs/scalability-targets.md)를 참조하세요.  |

`UploadFilesAsync` 작업은 다음 예제에 표시됩니다.

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

다음 예제는 Windows 시스템에서 실행 중인 잘린 애플리케이션 출력입니다.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>연결 유효성 검사

파일을 업로드하는 동안 스토리지 계정에 대한 동시 연결 수를 확인할 수 있습니다. **명령 프롬프트**를 열고 `netstat -a | find /c "blob:https"`를 입력합니다. 이 명령은 `netstat`를 사용하여 현재 열린 연결 수를 표시합니다. 다음 예제는 자습서를 직접 실행할 때 표시되는 것과 유사한 출력을 보여 줍니다. 예제에서 볼 수 있듯이 스토리지 계정에서 무작위 파일을 업로드할 때 800개의 연결이 열렸습니다. 이 값은 업로드를 실행하는 내내 변경됩니다. 병렬 블록 청크로 업로드하면 콘텐츠를 전송하는 데 필요한 시간이 크게 단축됩니다.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>다음 단계

시리즈 2부에서는 다음 방법을 통해 스토리지 계정에 대량의 임의 데이터를 병렬로 업로드하는 방법에 대해 배웠습니다.

> [!div class="checklist"]
> * 연결 문자열 구성
> * 애플리케이션 빌드
> * 애플리케이션 실행
> * 연결 수의 유효성 검사

스토리지 계정에서 대량의 데이터를 다운로드하는 시리즈 3부 전에 수행합니다.

> [!div class="nextstepaction"]
> [Azure Storage에서 대량의 임의 데이터 다운로드](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
