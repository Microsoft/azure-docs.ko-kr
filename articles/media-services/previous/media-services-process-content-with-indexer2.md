---
title: Azure Media Indexer 2 미리 보기를 사용하여 미디어 파일 인덱싱 | Microsoft 문서
description: Azure Media Indexer를 사용하면 미디어 파일 콘텐츠를 검색 가능하게 만들고 선택 자막 및 키워드용 전체 텍스트 기록을 생성할 수 있습니다. 이 토픽에서는 Media Indexer 2 Preview를 사용하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: adsolank;juliako;
ms.openlocfilehash: 304ecda320e1fdd9573bc961fde74efe03400aa3
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764177"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Azure Media Indexer 2 미리 보기를 사용하여 미디어 파일 인덱싱
## <a name="overview"></a>개요
**Azure Media Indexer 2 미리 보기** MP(미디어 프로세서)를 사용하여 미디어 파일과 콘텐츠를 검색 가능하도록 설정할 수 있으며 선택 캡션 트랙을 생성할 수 있습니다. [Azure Media Indexer 2 미리 보기](media-services-index-content.md)는 이전 버전의 **Azure Media Indexer**에 비해 보다 빠른 인덱싱을 수행하고 더 광범위한 언어 지원을 제공합니다. 지원되는 언어는 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 중국어(북경어, 간체), 포르투갈어, 아랍어, 러시아어, 일본어 등입니다.

**Azure Media Indexer 2 미리 보기** MP는 현재 미리 보기 상태입니다.

이 문서에서는 **Azure Media Indexer 2 미리 보기**를 사용하여 인덱싱 작업을 만드는 방법을 보여 줍니다.

> [!NOTE]
> 고려 사항은 다음과 같습니다.
> 
> 인덱서 2는 Azure China 및 Azure Government에서 지원되지 않습니다.
> 
> 콘텐츠를 인덱싱할 때, 음성이 매우 분명한(배경 음악, 소음, 효과 또는 마이크 소음) 미디어 파일을 사용해야 합니다. 적절한 콘텐츠의 예로는 녹음된 회의, 강의 또는 프레젠테이션이 있습니다. 인덱싱에 적합하지 않을 수 있는 콘텐츠: 영화, TV 프로그램, 오디오 및 사운드 효과가 혼합된 콘텐츠, 배경 소음(기계 소음)이 들어간 녹음 품질이 좋지 않은 콘텐츠.
> 
> 

이 문서에서는 **Azure Media Indexer 2 미리 보기**에 대한 세부 정보를 제공하고 .NET용 Media Services SDK와 함께 사용하는 방법을 보여 줍니다.

## <a name="input-and-output-files"></a>입력 및 출력 파일
### <a name="input-files"></a>입력 파일
오디오 또는 비디오 파일

### <a name="output-files"></a>출력 파일
인덱싱 작업은 다음 형식의 선택 캡션 파일을 생성할 수 있습니다.  

* **SAMI**
* **TTML**
* **WebVTT**

이러한 형식의 CC(선택 캡션)는 청각 장애가 있는 사용자가 액세스할 수 있는 오디오 및 비디오 파일을 만드는 데 사용될 수 있습니다.

## <a name="task-configuration-preset"></a>작업 구성(기본 설정)
**Azure Media Indexer 2 미리 보기**로 인덱싱 작업을 만들 때에는 구성 기본 설정을 지정해야 합니다.

다음 JSON은 사용 가능한 매개 변수를 설정합니다.

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>지원되는 언어
Azure Media Indexer 2 미리 보기는 다음 언어에 대한 음성-텍스트를 지원합니다(작업 구성에서 언어 이름을 지정할 때 아래와 같이 대괄호 안의 4자로 된 코드를 사용할 경우).

* 영어[EnUs]
* 스페인어[EsEs]
* 중국어 (북경어, 간체) [ZhCn]
* 프랑스어[FrFr]
* 독일어[DeDe]
* 이탈리아어[ItIt]
* 포르투갈어[PtBr]
* 아랍어(이집트)[ArEg]
* 일본어 [JaJp]
* 러시아어[RuRu]
* 영국 영어[EnGb]
* 스페인어 (멕시코) [EsMx] 

## <a name="supported-file-types"></a>지원되는 파일 형식

지원되는 파일 형식에 대한 내용은 [지원되는 코덱/형식](media-services-media-encoder-standard-formats.md#input-containerfile-formats) 섹션을 참조하세요.

## <a name="net-sample-code"></a>.NET 샘플 코드

다음 프로그램은 방법을 보여 줍니다.

1. 자산을 만들고 미디어 파일을 자산에 업로드합니다.
2. 다음 JSON 기본 설정을 포함하는 구성 파일을 기반으로 인덱싱 작업을 만듭니다.

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
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

namespace IndexContent
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

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

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

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>관련 링크
[Azure Media Services 분석 개요](media-services-analytics-overview.md)

[Azure 미디어 분석 데모](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

