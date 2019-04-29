---
title: Azure Media Video Thumbnails를 사용하여 비디오 요약 만들기 | Microsoft Docs
description: 비디오 요약을 사용하면 원본 비디오에서 흥미로운 조각을 자동으로 선택하여 긴 비디오의 요약을 만들 수 있습니다. 이는 긴 비디오에서 예상되는 사항에 대한 빠른 개요를 제공하려는 경우에 유용합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: milanga;juliako;
ms.openlocfilehash: 0fcacf68f4b41ed8945a6a40d7da125aef499947
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825539"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Azure 미디어 비디오 미리 보기를 사용하여 비디오 요약 만들기  
## <a name="overview"></a>개요
**Azure 미디어 비디오 미리 보기** MP(미디어 프로세서)를 사용하여 긴 비디오의 요약만 미리 보려는 고객에게 유용한 비디오 요약을 만들 수 있습니다. 예를 들어 고객은 미리 보기를 가리키면 나타나는 짧은 "요약 비디오"를 원할 수 있습니다. 구성 기본 설정을 통해 **Azure 미디어 비디오 미리 보기** 의 매개 변수를 조정하면 MP의 강력한 장면 감지 및 연결 기술을 사용하여 알고리즘 방식으로 설명이 포함된 하위 클립을 생성할 수 있습니다.  

**Azure 미디어 비디오 미리 보기** MP는 현재 미리 보기 상태입니다.

이 문서에서는 **Azure Media Video 미리 보기**에 대한 세부 정보를 제공하고 .NET용 Media Services SDK와 함께 사용하는 방법을 보여 줍니다.

## <a name="limitations"></a>제한 사항

여러 장면으로 구성되지 않은 비디오의 경우 하나의 장면만 출력됩니다.

## <a name="video-summary-example"></a>비디오 요약 예제
Azure 미디어 비디오 미리 보기 미디어 프로세서에서 수행할 수 있는 작업의 몇 가지 예는 다음과 같습니다.

### <a name="original-video"></a>원본 비디오
[원본 비디오](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>비디오 미리 보기 결과
[비디오 미리 보기 결과](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>작업 구성(기본 설정)
**Azure 미디어 비디오 미리 보기**로 비디오 미리 보기 작업을 만들 때에는 구성 기본 설정을 지정해야 합니다. 위의 미리 보기 샘플은 다음 기본 JSON 구성을 사용하여 만들어졌습니다.

```json
    {
        "version":"1.0"
    }
```

현재 다음 매개 변수를 변경할 수 있습니다.

| 매개 변수 | 설명 |
| --- | --- |
| outputAudio |결과 비디오에 오디오를 포함할지 여부를 지정합니다. <br/>허용되는 값은 다음과 같습니다. True 또는 False입니다. 기본값은 True입니다. |
| fadeInFadeOut |개별 동작 미리 보기 간에 페이드 전환이 사용되는지 여부를 지정합니다.  <br/>허용되는 값은 다음과 같습니다. True 또는 False입니다.  기본값은 True입니다. |
| maxMotionThumbnailDurationInSecs |전체 결과 비디오의 길이를 지정하는 정수입니다.  기본값은 원본 비디오의 지속 시간에 따라 다릅니다. |

다음 표에서는 **maxMotionThumbnailInSecs** 가 사용되지 않은 경우의 기본 지속 시간을 설명합니다.

|  |  |  |
| --- | --- | --- |
| 비디오 지속 시간 |d < 3분 |3분 < d < 15분 |
| 미리 보기 지속 시간 |15초(장면 2~3개) |30초(장면 3~5개) |

다음 JSON은 사용 가능한 매개 변수를 설정합니다.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>.NET 샘플 코드

다음 프로그램은 방법을 보여 줍니다.

1. 자산을 만들고 미디어 파일을 자산에 업로드합니다.
2. 다음 JSON 기본 설정을 포함하는 구성 파일을 기반으로 동영상 미리 보기 작업을 만듭니다. 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. 출력 파일을 다운로드합니다. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기 및 구성

개발 환경을 설정하고 [.NET을 사용한 Media Services 환경](media-services-dotnet-how-to-use.md)에 설명된 대로 연결 정보를 사용하여 app.config 파일을 채웁니다. 

#### <a name="example"></a>예

```csharp
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;

    namespace VideoSummarization
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


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

                // Use the following event handler to check job progress.  
                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

                // Launch the job.
                job.Submit();

                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

                progressJobTask.Wait();

                // If job state is Error, the event handling
                // method for job progress should log errors.  Here we check
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                    Console.WriteLine(string.Format("Error: {0}. {1}",
                                                    error.Code,
                                                    error.Message));
                    return null;
                }

                return job.OutputMediaAssets[0];
            }

            static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
            {
                IAsset asset = _context.Assets.Create(assetName, options);

                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                assetFile.Upload(filePath);

                return asset;
            }

            static void DownloadAsset(IAsset asset, string outputDirectory)
            {
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    file.Download(Path.Combine(outputDirectory, file.Name));
                }
            }

            static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors
                    .Where(p => p.Name == mediaProcessorName)
                    .ToList()
                    .OrderBy(p => new Version(p.Version))
                    .LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor",
                                                               mediaProcessorName));

                return processor;
            }

            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);

                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
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
                        // LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }

        }
    }
```

### <a name="video-thumbnail-output"></a>비디오 미리 보기 출력
[비디오 미리 보기 출력](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>관련 링크
[Azure Media Services 분석 개요](media-services-analytics-overview.md)

[Azure 미디어 분석 데모](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

