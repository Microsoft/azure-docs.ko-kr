---
title: Azure 미디어 분석으로 얼굴 편집 | Microsoft Docs
description: 이 항목에서는 Azure Media Analytics로 얼굴을 편집하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;
ms.openlocfilehash: 1fe003ae13bc5f195932f4f140e17c4dc2791959
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247403"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Azure 미디어 분석으로 얼굴 편집 
## <a name="overview"></a>개요
**Azure Media Redactor**는 클라우드에서 확장성 있는 얼굴 편집 기능을 제공하는 [Azure Media Analytics](media-services-analytics-overview.md) MP(미디어 프로세서)입니다. 얼굴 편집을 사용하면 선택한 개인의 얼굴을 흐리게 표시하기 위해 동영상을 수정할 수 있습니다. 공공 안전과 새 미디어 시나리오를 위해 얼굴 편집 서비스를 사용할 수 있습니다. 짧은 장면이라도 여러 명의 얼굴이 포함된 경우 수동으로 편집하려면 많은 시간이 걸릴 수 있지만 이 서비스를 사용하면 몇 번의 간단한 단계를 통해 얼굴을 편집할 수 있습니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-redactor/) 를 참조하세요.

이 문서에서는 **Azure Media Redactor**에 대한 세부 정보를 제공하고 .NET용 Media Services SDK와 함께 사용하는 방법을 보여 줍니다.

## <a name="face-redaction-modes"></a>얼굴 편집 모드
얼굴 편집은 동일한 개인이 다른 각도에서도 흐리게 표시될 수 있도록 동영상의 모든 프레임에서 얼굴을 감지하고 앞뒤 시간의 얼굴 개체를 추적합니다. 자동 편집 프로세스는 복잡하여 항상 원하는 결과가 100% 생성되지는 않습니다. 따라서 미디어 분석은 최종 결과를 수정하기 위한 몇 가지 방법을 제공합니다.

완전 자동 모드 외에, ID 목록을 통해 검색한 얼굴을 선택/선택 취소할 수 있는 2단계 워크플로가 있습니다. 또한 MP는 프레임별 임의 조정을 위해 JSON 형식의 메타데이터 파일을 사용합니다. 이 워크플로는 **분석** 및 **편집** 모드로 분할됩니다. 두 모드를 하나의 작업에서 두 작업을 실행하는 단일 단계로 결합할 수 있습니다. 이러한 모드를 **결합된** 모드라고 합니다.

### <a name="combined-mode"></a>결합된 모드
이 모드는 수동 입력 없이 자동으로 편집된 mp4를 생성합니다.

| 단계 | 파일 이름 | 메모 |
| --- | --- | --- |
| 입력 자산 |foo.bar |WMV, MOV 또는 MP4 형식의 동영상 |
| 입력 구성 |작업 구성 사전 설정 |{'version':'1.0', 'options': {'mode':'combined'}} |
| 출력 자산 |foo_redacted.mp4 |흐리게 표시하기가 적용된 동영상 |

#### <a name="input-example"></a>입력 예:
[이 동영상을 보세요.](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>출력 예제:
[이 동영상을 보세요.](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>분석 모드
2단계 워크플로의 **분석** 단계는 동영상 입력을 사용하여 얼굴 위치의 JSON 파일과 검색된 각 얼굴의 jpg 이미지를 생성합니다.

| 단계 | 파일 이름 | 메모 |
| --- | --- | --- |
| 입력 자산 |foo.bar |WMV, MPV 또는 MP4 형식의 동영상 |
| 입력 구성 |작업 구성 사전 설정 |{'version':'1.0', 'options': {'mode':'analyze'}} |
| 출력 자산 |foo_annotations.json |얼굴 위치의 주석 데이터(JSON 형식). 사용자가 흐리게 표시하는 테두리 상자를 수정하기 위해 편집할 수 있습니다. 아래 샘플을 참조하세요. |
| 출력 자산 |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |검색된 각 얼굴을 잘라낸 jpg(숫자는 얼굴의 레이블 ID) |

#### <a name="output-example"></a>출력 예제:

```json
    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated
```

### <a name="redact-mode"></a>편집 모드
워크플로의 두 번째 단계에서는 하나의 자산으로 결합해야 하는 여러 입력을 사용합니다.

여기에는 흐리게 표시할 ID 목록, 원본 동영상 및 주석 JSON이 포함됩니다. 이 모드에서는 주석을 사용하여 입력 동영상에 흐리게 표시를 적용합니다.

분석 단계의 출력에는 원본 동영상이 포함되지 않습니다. 동영상을 편집 모드 작업의 입력 자산으로 업로드하고 기본 파일로 선택해야 합니다.

| 단계 | 파일 이름 | 메모 |
| --- | --- | --- |
| 입력 자산 |foo.bar |WMV, MPV 또는 MP4 형식의 동영상. 1단계와 동일한 동영상입니다. |
| 입력 자산 |foo_annotations.json |1단계의 주석 메타데이터 파일 및 수정 사항(선택 사항) |
| 입력 자산 |foo_IDList.txt(선택 사항) |줄로 구분된 편집할 얼굴 ID의 새 목록(선택 사항). 지정하지 않으면 모든 얼굴이 흐리게 표시됩니다. |
| 입력 구성 |작업 구성 사전 설정 |{'version':'1.0', 'options': {'mode':'redact'}} |
| 출력 자산 |foo_redacted.mp4 |주석을 기반으로 흐리게 표시하기가 적용된 동영상 |

#### <a name="example-output"></a>예제 출력
IDList에서 하나의 ID가 선택된 출력입니다.

[이 동영상을 보세요.](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

예제 foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>흐리게 형식

**결합** 또는 **편집** 모드에는 JSON 입력 구성을 통해 선택할 수 있는 5가지 흐리게 모드가 있습니다( **낮음**, **중간**, **높음**, **상자** 및 **검정**). 기본적으로 **중간**이 사용됩니다.

아래에 흐리게 형식의 샘플을 확인할 수 있습니다.

### <a name="example-json"></a>예제 JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>낮음

![낮음](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>중간

![중간](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>높음

![높음](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>검정

![검정](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>출력 JSON 파일의 요소

편집 MP는 한 동영상 프레임 내에서 최대 64명의 얼굴을 검색할 수 있는 고정밀도 얼굴 위치 검색 및 추적을 제공합니다. 정면이 최상의 결과를 제공하며 측면 또는 작은 얼굴(24x24 픽셀보다 작거나 같음)의 경우에는 어려울 수 있습니다.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET 샘플 코드

다음 프로그램은 방법을 보여 줍니다.

1. 자산을 만들고 미디어 파일을 자산에 업로드합니다.
2. 다음 JSON 기본 설정을 포함하는 구성 파일을 기반으로 얼굴 편집 작업을 만듭니다. 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
                }
            }
    ```

3. 출력 JSON 파일을 다운로드합니다. 

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

namespace FaceRedaction
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

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

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

## <a name="next-steps"></a>다음 단계

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>관련 링크
[Azure Media Services 분석 개요](media-services-analytics-overview.md)

[Azure 미디어 분석 데모](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

