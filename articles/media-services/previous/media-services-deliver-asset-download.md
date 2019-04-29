---
title: 컴퓨터에 Media Services 자산 다운로드 - Azure | Microsoft Docs
description: 컴퓨터로 자산을 다운로드하는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 21fcc6ae09718ffbb22e1d438926586dd3cde71d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465663"
---
# <a name="how-to-deliver-an-asset-by-download"></a>방법: 다운로드를 통해 자산 제공  
이 문서에서는 Media Services에 업로드된 미디어 자산을 제공하는 옵션에 대해 설명합니다. 다양한 애플리케이션 시나리오에서 Media Services 콘텐츠를 제공할 수 있습니다. 인코딩한 다음, 생성된 미디어 자산을 다운로드하거나 스트리밍 로케이터를 사용하여 액세스합니다. 향상된 성능과 확장성을 위해 Content Delivery Network(CDN)를 사용하여 콘텐츠를 배달할 수도 있습니다.

이 예제에는 Media Services에서 로컬 컴퓨터로 미디어 자산을 다운로드하는 방법을 보여줍니다. 이 코드에서는 작업 ID를 사용하여 Media Services 계정에 연결된 작업을 쿼리하고 **OutputMediaAssets** 컬렉션(작업 실행의 결과로 반환되는 하나 이상의 출력 미디어 자산 집합)에 액세스합니다. 이 예제에서는 작업에서 출력 미디어 자산을 다운로드하는 방법을 보여 주지만, 동일한 방법을 사용하여 다른 자산을 다운로드할 수도 있습니다.

>[!NOTE]
>다른 AMS 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)은 1,000,000개의 정책으로 제한됩니다. 항상 같은 날짜/액세스 권한을 사용하는 경우(예: 비 업로드 정책처럼 오랫동안 유지되는 로케이터에 대한 정책) 동일한 정책 ID를 사용합니다. 자세한 내용은 [이](media-services-dotnet-manage-entities.md#limit-access-policies) 문서를 참조하세요.

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>관련 항목
[스트리밍 콘텐츠 제공](media-services-deliver-streaming-content.md)

