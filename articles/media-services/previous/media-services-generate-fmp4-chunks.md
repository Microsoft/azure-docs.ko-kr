---
title: fMP4 청크를 생성하는 Azure Media Services 인코딩 작업 만들기 | Microsoft Docs
description: 이 항목에서는 fMP4 청크를 생성하는 인코딩 작업을 만드는 방법을 보여 줍니다. 이 작업을 Media Encoder Standard 또는 Media Encoder Premium 워크플로 인코더와 함께 사용하면 출력 자산에는 ISO MP4 파일 대신 fMP4 청크가 포함됩니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 48c09a6100586c0c99f3d54f9708c770488c681f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463870"
---
#  <a name="create-an-encoding-task-that-generates-fmp4-chunks"></a>fMP4 청크를 생성하는 인코딩 작업 만들기

## <a name="overview"></a>개요

이 문서에서는 ISO MP4 파일 대신 조각화된 MP4(fMP4) 청크를 생성하는 인코딩 작업을 만드는 방법을 보여 줍니다. fMP4 청크를 생성하려면 다음 코드 조각처럼 **Media Encoder Standard** 또는 **Media Encoder Premium 워크플로** 인코더를 사용하여 인코딩 작업을 만들고 **AssetFormatOption.AdaptiveStreaming**을 지정합니다.  
    
```csharp
    task.OutputAssets.AddNew(@"Output Asset containing fMP4 chunks", 
            options: AssetCreationOptions.None, 
            formatOption: AssetFormatOption.AdaptiveStreaming);
```

## <a id="encoding_with_dotnet"></a>Media Services .NET SDK를 사용하여 Encoding

다음 코드 예제에서는 Media Services .NET SDK를 사용하여 다음 작업을 수행합니다.

- 인코딩 작업을 만듭니다.
- **Media Encoder Standard** 인코더에 대한 참조를 가져옵니다.
- 작업에 인코딩 작업(task)을 추가하고 **적응 스트리밍** 사전 설정을 사용하도록 지정합니다. 
- fMP4 청크 및 .ism 파일을 포함할 출력 자산을 만듭니다.
- 작업 진행 상태를 확인할 이벤트 처리기를 추가합니다.
- 작업을 제출합니다.

#### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기 및 구성

개발 환경을 설정하고 [.NET을 사용한 Media Services 환경](media-services-dotnet-how-to-use.md)에 설명된 대로 연결 정보를 사용하여 app.config 파일을 채웁니다. 

#### <a name="example"></a>예

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreaming
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }
        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");

            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job. 

            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            // It is also specified to use AssetFormatOption.AdaptiveStreaming, 
            // which means the output asset will contain fMP4 chunks.

            task.OutputAssets.AddNew(@"Output Asset containing fMP4 chunks",
            options: AssetCreationOptions.None,
            formatOption: AssetFormatOption.AdaptiveStreaming);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                    break;
                default:
                    break;
            }
        }
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>관련 항목
[Media Services Encoding 개요](media-services-encode-asset.md)

