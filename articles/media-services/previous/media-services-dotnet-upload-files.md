---
title: .NET을 사용하여 Media Services 계정에 파일 업로드 | Microsoft 문서
description: 자산을 만들고 업로드하여 Media Services에 미디어 콘텐츠를 가져오는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 4098d55a0b7505b2178c95d612c078a427adc9a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61464218"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>.NET을 사용하여 Media Services 계정에 파일 업로드 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST (영문)](media-services-rest-upload-files.md)
> * [포털](media-services-portal-upload-files.md)
> 
> 

Media Services에서 자산에 디지털 파일을 업로드(수집)합니다. **자산** 엔터티에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 자막 파일(및 이러한 파일에 대한 메타데이터)이 포함될 수 있습니다.  파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.

자산에 포함된 파일을 **자산 파일**이라고 합니다. **AssetFile** 인스턴스 및 실제 미디어 파일은 별개의 두 개체입니다. AssetFile 인스턴스는 미디어 파일에 대한 메타데이터를 포함하는 반면 미디어 파일은 실제 미디어 콘텐츠를 포함합니다.

> [!NOTE]
> 고려 사항은 다음과 같습니다.
> 
> * Media Services는 스트리밍 콘텐츠(예: http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.)를 위해 URL을 작성할 때 IAssetFile.Name 속성 값을 사용합니다. 이러한 이유로 퍼센트 인코딩은 허용되지 않습니다. **Name** 속성 값에는 !*'();:@&=+$,/?%#[]"와 같은 [퍼센트 인코딩 예약 문자](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)를 사용할 수 없습니다. 또한 파일 이름 확장명에는 ‘.’ 하나만 사용할 수 있습니다.
> * 이름 길이는 260자보다 클 수 없습니다.
> * Media Services에서 처리를 위해 지원되는 최대 파일 크기에 제한이 있습니다. 파일 크기 제한에 대한 세부 정보는 [이](media-services-quotas-and-limitations.md) 문서를 참조하세요.
> * 다른 AMS 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)은 1,000,000개의 정책으로 제한됩니다. 항상 같은 날짜/액세스 권한을 사용하는 경우(예: 비 업로드 정책처럼 오랫동안 배치되는 로케이터에 대한 정책) 동일한 정책 ID를 사용해야 합니다. 자세한 내용은 [이](media-services-dotnet-manage-entities.md#limit-access-policies) 문서를 참조하세요.
> 

자산을 만들 때 다음 암호화 옵션을 지정할 수 있습니다.

* **없음** - 암호화가 사용되지 않습니다. 기본값입니다. 이 옵션을 사용하면 콘텐츠가 전송 중인 상태이거나 저장소에 저장된 상태일 때 보호되지 않습니다.
  점진적 다운로드를 사용하여 MP4를 배달하려는 경우 이 옵션을 사용하세요. 
* **CommonEncryption** - 일반적인 암호화 또는 PlayReady DRM(예: PlayReady DRM으로 보호되는 부드러운 스트리밍)으로 이미 보호된 콘텐츠를 업로드하는 경우 이 옵션을 사용합니다.
* **EnvelopeEncrypted** – AES로 암호화된 HLS를 업로드하는 경우 이 옵션을 사용합니다. 파일을 Transform Manager로 인코딩 및 암호화해야 합니다.
* **StorageEncrypted** - AES-256 비트 암호화를 통해 보호되지 않은 콘텐츠를 로컬로 암호화한 후에 Azure Storage에 업로드하고 사용하지 않을 때 암호화하여 저장합니다. 저장소 암호화로 보호된 자산은 자동으로 암호 해제되어 인코딩되기 전에 암호화된 파일 시스템에 배치됩니다. 그리고 필요에 따라 새 출력 자산으로 다시 업로드되기 전에 다시 암호화됩니다. 저장소 암호화를 사용하는 기본적인 사례는 디스크에 저장된 상태일 때 강력한 암호화로 고품질의 입력 미디어 파일을 보호하려는 경우입니다.
  
    Media Services는 DRM(Digital Rights Manager)처럼 네트워크상이 아니라 자산에 대해 디스크상의 저장소 암호화 기능을 제공합니다.
  
    자산이 암호화된 저장소인 경우 자산 배달 정책을 구성해야 합니다. 자세한 내용은 [자산 배달 정책 구성](media-services-dotnet-configure-asset-delivery-policy.md)을 참조하세요.

**CommonEncrypted** 옵션 또는 **EnvelopeEncrypted** 옵션으로 암호화할 자산을 지정하는 경우 **ContentKey**로 해당 자산을 연결해야 합니다. 자세한 내용은 [ContentKey를 만드는 방법](media-services-dotnet-create-contentkey.md)을 참조하세요 

**StorageEncrypted** 옵션으로 암호화할 자산을 지정하는 경우 .NET용 Media Services SDK에서 자산에 대한 **StorageEncrypted** **ContentKey**를 만듭니다.

이 아티클에서는 Media Services .NET SDK와 Media Services .NET SDK 확장을 사용하여 Media Services 자산으로 파일을 업로드하는 방법을 설명합니다.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Media Services .NET SDK를 사용하여 단일 파일 업로드
다음 코드는 .NET을 사용하여 단일 파일을 업로드합니다. AccessPolicy와 로케이터는 업로드 함수에 의해 생성되고 제거됩니다. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Media Services .NET SDK를 사용하여 여러 파일 업로드
다음 코드는 자산을 만들고 여러 파일을 업로드하는 방법을 보여 줍니다.

코드는 다음을 수행합니다.

* 이전 단계에서 정의된 CreateEmptyAsset 메서드를 사용하여 빈 자산을 만듭니다.
* 자산에 액세스할 수 있는 권한 및 기간을 정의하는 **AccessPolicy** 인스턴스를 만듭니다.
* 자산에 액세스를 제공하는 **Locator** 인스턴스를 만듭니다.
* **BlobTransferClient** 인스턴스를 만듭니다. 이 유형은 Azure blob에서 작동하는 클라이언트를 나타냅니다. 이 예제에서 클라이언트는 업로드 진행 상태를 모니터링합니다. 
* 지정된 디렉터리에서 파일을 열거하고 각각의 파일에 **AssetFile** 인스턴스를 만듭니다.
* **UploadAsync** 메서드를 사용하여 파일을 Media Services에 업로드합니다. 

> [!NOTE]
> 호출을 차단하지 않고 파일을 동시에 업로드하려면 UploadAsync 메서드를 사용합니다.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


많은 수의 자산을 업로드할 때에는 다음 사항을 고려합니다.

* 스레드마다 새 **CloudMediaContext** 개체를 만듭니다. **CloudMediaContext** 클래스는 스레드로부터 안전하지 않습니다.
* 기본 값 2에서 5와 같이 더 높은 값으로 NumberOfConcurrentTransfers를 늘립니다. 이 자산을 설정하면 **CloudMediaContext**의 모든 인스턴스에 영향을 미칩니다. 
* 기본 값 10으로 ParallelTransferThreadCount를 유지합니다.

## <a id="ingest_in_bulk"></a>Media Services .NET SDK를 사용하여 대량으로 자산 수집
큰 자산 파일을 업로드하면 자산을 만드는 동안 병목 상태가 될 수 있습니다. 대량 또는 “대량 수집”으로 자산을 수집하면 업로드 과정에서 자산 만들기를 분리하는 작업이 포함됩니다. 대량 수집 방식을 사용하려면 자산 및 연결된 파일을 설명하는 매니페스트(IngestManifest)를 만듭니다. 그런 다음 매니페스트의 blob 컨테이너에 연결된 파일을 업로드하기 위해 선택한 업로드 방식을 사용합니다. Microsoft Azure Media Services는 매니페스트와 연결된 blob 컨테이너를 감시합니다. blob 컨테이너에 파일을 업로드하면, Microsoft Azure Media Services가 매니페스트(IngestManifestAsset)의 자산 구성에 기반하여 자산 만들기를 완료합니다.

새 IngestManifest를 만들려면 CloudMediaContext의 IngestManifests 컬렉션에서 Create 메서드를 호출합니다. 이 메서드는 입력한 매니페스트 이름으로 새 IngestManifest를 만듭니다.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

대량 IngestManifest와 연결되는 자산을 만듭니다. 대량 수집을 위해 자산에 원하는 암호화 옵션을 구성합니다.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

IngestManifestAsset는 대량 수집을 위한 대량 IngestManifest와 자산을 연결합니다. 각각의 자산을 구성하는 AssetFiles도 연결합니다. IngestManifestAsset를 만들려면 서버 컨텍스트에서 만들기 메서드를 사용합니다.

다음 예제에서는 대량 수집 매니페스트에 이전에 만든 두 자산을 연결하는 두 개의 새 IngestManifestAssets를 추가하는 과정을 설명합니다. 각각의 IngestManifestAsset는 대량 수집 중에 각각의 자산에 업로드된 파일 집합도 연결합니다.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

IngestManifest의 **IIngestManifest.BlobStorageUriForUpload** 속성에서 제공하는 Blob 스토리지 컨테이너 URI에 자산 파일을 업로드할 수 있는 고속 클라이언트 응용 프로그램을 사용할 수 있습니다. 

다음 코드에서는 .NET SDK를 사용하여 자산 파일을 업로드하는 방법을 보여 줍니다.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

이 아티클에서 사용하는 샘플의 자산 파일을 업로드하는 코드는 다음 코드 예제에 표시되어 있습니다.

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

**IngestManifest**의 Statistics 속성을 폴링하여 **IngestManifest**에 연결된 모든 자산의 대량 수집 과정을 결정할 수 있습니다. 과정 정보를 업데이트하려면 통계 속성을 폴링할 때마다 새 **CloudMediaContext** 를 사용해야 합니다.

다음 예제는 해당 **Id**로 IngestManifest를 폴링하는 방법을 설명합니다.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>.NET SDK Extensions를 사용하여 파일 업로드
다음 예제에서는 .NET SDK Extensions를 사용하여 단일 파일을 업로드하는 방법을 보여줍니다. 이 경우 **CreateFromFile** 메서드가 사용되지만 비동기 버전(**CreateFromFileAsync**)도 사용됩니다. **CreateFromFile** 메서드를 사용하면 파일 이름, 암호화 옵션 및 파일의 업로드 과정을 보고하기 위한 콜백을 지정할 수 있습니다.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

다음 예제는 UploadFile 함수를 호출하며 자산 만들기 옵션으로 저장소 암호화를 지정합니다.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>다음 단계

이제 업로드된 자산을 인코딩할 수 있습니다. 자세한 내용은 [자산 인코딩](media-services-portal-encode.md)을 참조하세요.

또한 Azure Functions를 사용하여 구성된 컨테이너에 도착하는 파일에 따라 인코딩 작업을 트리거할 수도 있습니다. 자세한 내용은 [이 샘플](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )을 참조하세요.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>다음 단계
이제 Media Services에 자산을 업로드했으므로 [미디어 프로세서를 가져오는 방법][How to Get a Media Processor] 아티클로 이동하세요.

[How to Get a Media Processor]: media-services-get-media-processor.md

