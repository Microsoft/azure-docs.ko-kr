---
title: 여러 스토리지 계정에서 Media Services 자산 관리 | Microsoft Docs
description: 이 문서에서는 여러 스토리지 계정에서 Media Services 자산을 관리하는 방법에 대한 지침을 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: b5b0b383cba45646a1e5a8f980b3a097767f9979
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89262603"
---
# <a name="managing-media-services-assets-across-multiple-storage-accounts"></a>여러 스토리지 계정에서 Media Services 자산 관리  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

여러 스토리지 계정을 단일 Media Services 계정에 연결할 수 있습니다. 여러 스토리지 계정을 Media Services 계정에 연결하는 기능은 다음과 같은 이점을 제공합니다.

* 자산을 여러 스토리지 계정에서 부하 분산합니다.
* 대량의 콘텐츠 처리를 위한 Media Services 크기 조정(현재 단일 스토리지 계정의 최대 제한은 500TB). 

이 문서에서는 [Azure Resource Manager api](/rest/api/media/operations/azure-media-services-rest-api-reference) 및 [PowerShell](/powershell/module/az.media)을 사용 하 여 여러 저장소 계정을 Media Services 계정에 연결 하는 방법을 보여 줍니다. 또한 Media Services SDK를 사용하여 자산을 만들 때 다른 스토리지 계정을 지정하는 방법을 보여줍니다. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="considerations"></a>고려 사항

여러 스토리지 계정을 Media Services 계정에 연결할 때는 다음과 같은 고려 사항이 있습니다.

* Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. Media Services 계정과 동일한 위치의 스토리지 계정을 사용하는 것이 좋습니다.
* 스토리지 계정이 지정된 Media Services 계정에 연결되면 분리할 수 없습니다.
* 기본 스토리지 계정은 Media Services 계정을 만드는 중에 지정된 계정입니다. 현재는 기본 스토리지 계정을 변경할 수 없습니다. 
* 쿨 스토리지 계정을 AMS 계정에 추가하려면 스토리지 계정이 Blob 유형이고 기본이 아닌 계정으로 설정되어야 합니다.

기타 고려 사항:

Media Services는 스트리밍 콘텐츠의 Url을 빌드할 때 **IAssetFile.Name** 속성의 값을 사용 합니다 (예: http://{WAMSAccount}. windowsazure.mediaservices/{GUID}/{IAssetFile}/streamingParameters). 이러한 이유로 퍼센트 인코딩은 허용 되지 않습니다. Name 속성 값에는 !* '();:@&=+$,/?%#[]"와 같은 [퍼센트 인코딩 예약 문자](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)를 사용할 수 없습니다. 또한 ‘.’ 하나만 사용할 수 있습니다. 또한 파일 이름 확장명에는 ‘.’ 하나만 사용할 수 있습니다.

## <a name="to-attach-storage-accounts"></a>스토리지 계정을 연결하려면  

저장소 계정을 AMS 계정에 연결 하려면 다음 예제와 같이 [Azure Resource Manager api](/rest/api/media/operations/azure-media-services-rest-api-reference) 및 [PowerShell](/powershell/module/az.media)을 사용 합니다.

```azurepowershell
$regionName = "West US"
$subscriptionId = " xxxxxxxx-xxxx-xxxx-xxxx- xxxxxxxxxxxx "
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccount1Name = "skystorage1"
$storageAccount2Name = "skystorage2"
$storageAccount1Id = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccount1Name"
$storageAccount2Id = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccount2Name"
$storageAccount1 = New-AzMediaServiceStorageConfig -StorageAccountId $storageAccount1Id -IsPrimary
$storageAccount2 = New-AzMediaServiceStorageConfig -StorageAccountId $storageAccount2Id
$storageAccounts = @($storageAccount1, $storageAccount2)
    
Set-AzMediaService -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccounts $storageAccounts
```

### <a name="support-for-cool-storage"></a>쿨 스토리지 지원

현재 쿨 Storage 계정을 AMS 계정에 추가하려면 Storage 계정이 Blob 유형이고 주가 아닌 상태로 설정되어야 합니다.

## <a name="to-manage-media-services-assets-across-multiple-storage-accounts"></a>여러 Storage 계정에서 Media Services 자산을 관리하려면
다음 코드에서는 최신 Media Services SDK를 사용하여 다음 작업을 수행합니다.

1. 지정된 Media Services 계정에 연결된 모든 스토리지 계정을 표시합니다.
2. 기본 스토리지 계정의 이름을 검색합니다.
3. 기본 스토리지 계정에서 새 자산을 만듭니다.
4. 지정된 스토리지 계정에서 인코딩 작업의 출력 자산을 만듭니다.
   
```cs
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace MultipleStorageAccounts
{
    class Program
    {
        // Location of the media file that you want to encode. 
        private static readonly string _singleInputFilePath =
            Path.GetFullPath(@"../..\supportFiles\multifile\interview2.wmv");

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Display the storage accounts associated with 
            // the specified Media Services account:
            foreach (var sa in _context.StorageAccounts)
                Console.WriteLine(sa.Name);

            // Retrieve the name of the default storage account.
            var defaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == true).FirstOrDefault();
            Console.WriteLine("Name: {0}", defaultStorageName.Name);
            Console.WriteLine("IsDefault: {0}", defaultStorageName.IsDefault);

            // Retrieve the name of a storage account that is not the default one.
            var notDefaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == false).FirstOrDefault();
            Console.WriteLine("Name: {0}", notDefaultStorageName.Name);
            Console.WriteLine("IsDefault: {0}", notDefaultStorageName.IsDefault);

            // Create the original asset in the default storage account.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None,
                defaultStorageName.Name, _singleInputFilePath);
            Console.WriteLine("Created the asset in the {0} storage account", asset.StorageAccountName);

            // Create an output asset of the encoding job in the other storage account.
            IAsset outputAsset = CreateEncodingJob(asset, notDefaultStorageName.Name, _singleInputFilePath);
            if (outputAsset != null)
                Console.WriteLine("Created the output asset in the {0} storage account", outputAsset.StorageAccountName);

        }

        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string storageName, string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();

            // If you are creating an asset in the default storage account, you can omit the StorageName parameter.
            var asset = _context.Assets.Create(assetName, storageName, assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);

            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static IAsset CreateEncodingJob(IAsset asset, string storageName, string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My encoding job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset", storageName,
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish. 
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // Get an updated job reference.
            job = GetJob(job.Id);

            // If job state is Error the event handling 
            // method for job progress should log errors.  Here we check 
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                Console.WriteLine("\nExiting method due to job error.");
                return null;
            }

            // Get a reference to the output asset from the job.
            IAsset outputAsset = job.OutputMediaAssets[0];

            return outputAsset;
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }

        private static void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("********************");
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine("Please wait while local tasks or downloads complete...");
                    Console.WriteLine("********************");
                    Console.WriteLine();
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    Console.WriteLine("An error occurred in {0}", job.Id);
                    break;
                default:
                    break;
            }
        }

        static IJob GetJob(string jobId)
        {
            // Use a Linq select query to get an updated 
            // reference by Id. 
            var jobInstance =
                from j in _context.Jobs
                where j.Id == jobId
                select j;
            // Return the job reference as an Ijob. 
            IJob job = jobInstance.FirstOrDefault();

            return job;
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

