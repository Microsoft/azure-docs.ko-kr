---
title: Azure 미디어 분석으로 동작 검색 | Microsoft 문서
description: Azure 미디어 동작 탐지기 MP(미디어 프로세서)를 사용하여 길고 특별하지 않은 동영상 중에서 원하는 섹션을 효율적으로 식별합니다.
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
ms.date: 03/19/2019
ms.author: milanga;juliako;
ms.openlocfilehash: e0b083cba575f4d1c0eb19afb76fca29431ae75e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463534"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Azure 미디어 검색으로 동작 검색
## <a name="overview"></a>개요
**Azure 미디어 동작 탐지기** 의 MP(미디어 프로세서)를 사용하여 길고 특별하지 않은 동영상 중에서 원하는 섹션을 효율적으로 식별합니다. 동작 검색은 고정된 카메라 장면에서 동작이 발생한 동영상 섹션을 식별하는 데 사용할 수 있습니다. 이벤트가 발생한 경계 영역 및 타임스탬프가 있는 메타데이터를 포함하는 JSON 파일을 생성합니다.

보안 동영상 피드를 대상으로 하는 이 기술은 동작을 관련 이벤트와 그림자 및 조명 변화와 같은 가양성으로 분류할 수 있습니다. 이를 통해 수많은 관련 없는 이벤트로 인해 스팸 처리되지 않고 카메라 피드로부터 보안 경고를 생성할 수 있으며, 긴 감시 동영상으로부터 필요한 순간을 추출할 수 있습니다.

**Azure 미디어 동작 탐지기** MP는 현재 미리 보기 상태입니다.

이 문서에서는 **Azure Media Motion Detector**에 대한 세부 정보를 제공하고 .NET용 Media Services SDK와 함께 사용하는 방법을 보여 줍니다.

## <a name="motion-detector-input-files"></a>동작 검색기 입력 파일
동영상 파일입니다. 현재 다음 형식이 지원됩니다. MP4, MOV 및 WMV.

## <a name="task-configuration-preset"></a>작업 구성(기본 설정)
**Azure 미디어 동작 탐지기**로 작업을 만들 때에는 구성 기본 설정을 지정해야 합니다. 

### <a name="parameters"></a>매개 변수
다음 매개 변수를 사용할 수 있습니다.

| 이름 | 옵션 | 설명 | 기본값 |
| --- | --- | --- | --- |
| sensitivityLevel |문자열:'low', 'medium', 'high' |동작이 보고되는 민감도 수준을 설정합니다. 가양성 수를 조정하려면 이 값을 조정합니다. |'medium' |
| frameSamplingValue |양의 정수 |알고리즘이 실행되는 빈도를 설정합니다. 1은 프레임마다, 2는 두 개의 프레임마다 등을 의미합니다. |1 |
| detectLightChange |부울:'true', 'false' |결과에 간단한 변경 내용이 보고되는지 여부를 설정합니다. |'False' |
| mergeTimeThreshold |Xs-time: Hh:mm:ss<br/>예제: 00:00:03 |2개의 이벤트가 결합되어 1로 보고되는 동작 이벤트 간의 기간을 지정합니다. |00:00:00 |
| detectionZones |검색 영역 배열:<br/>- 검색 영역은 3개 이상의 지점 배열<br/>- 지점은 0부터 1까지의 x 및 y 좌표입니다. |사용할 다각형의 검색 영역 목록을 설명합니다.<br/>결과는 영역과 함께 ID로 보고되며 첫 번째 항목은 ‘id’:0입니다. |전체 프레임에 걸쳐 있는 단일 영역입니다. |

### <a name="json-example"></a>JSON 예제

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>동작 검색기 출력 파일
동작 검색 작업은 동작 경고 및 동영상 내에서의 해당 범주를 설명하는 출력 자산으로 JSON 파일을 반환합니다. 파일에는 동영상에서 검색된 동작 시간 및 기간에 대한 정보가 포함됩니다.

동작 탐지기 API는 고정된 배경의 동영상에 움직이는 개체가 있는 경우(예: 감시 동영상) 표시기를 제공합니다. 동작 탐지기는 조명 및 그림자 변화와 같은 가양성을 줄이기 위해 학습됩니다. 현재 알고리즘의 한계로 야간 투시 동영상, 반투명 개체 및 작은 개체가 있습니다.

### <a id="output_elements"></a>출력 JSON 파일의 요소
> [!NOTE]
> 최신 릴리스에서는 출력 JSON 형식이 변경되었으며 일부 고객에게는 큰 변화로 나타날 수 있습니다.
> 
> 

다음 표는 출력 JSON 파일의 요소에 대해 설명합니다.

| 요소 | 설명 |
| --- | --- |
| Version |동영상 API의 버전을 나타냅니다. 현재 버전은 2입니다. |
| 시간 간격 |동영상의 초당 "틱"입니다. |
| Offset |"틱" 단위의 타임스탬프에 대한 시간 오프셋입니다. 동영상 API 버전 1.0에서는 항상 0입니다. 향후 지원하는 시나리오에서는 이 값이 변경될 수 있습니다. |
| 프레임 속도 |동영상의 초당 프레임 수입니다. |
| 너비, 높이 |픽셀 단위의 동영상 너비와 높이를 참조합니다. |
| 시작 |"틱" 단위의 시작 타임스탬프입니다. |
| 기간 |"틱" 단위의 이벤트 길이입니다. |
| Interval |"틱" 단위의 이벤트에 있는 각 항목의 간격입니다. |
| 이벤트 |각 이벤트 조각에는 해당 기간 내에 검색된 동작이 포함됩니다. |
| Type |현재 버전에서 이 값은 일반 동작에 대해 항상 '2'입니다. 이 레이블은 향후 버전에서 동영상 API에 동작 분류에 대한 유연성을 제공합니다. |
| RegionID |위에서 설명했듯이 이 버전에서 이 값은 항상 0입니다. 이 레이블은 향후 버전에서 동영상 API에 다양한 영역에서 동작을 찾는 유연성을 제공합니다. |
| 영역 |동작에 관심이 있는 동영상 영역을 참조합니다. <br/><br/>-"id"는 지역 영역을 나타내는데, 이 버전에는 ID 0밖에 없습니다. <br/>-"type"은 동작에 대한 중요한 영역 모양을 나타냅니다. 현재 "rectangle" 및 "polygon"이 지원됩니다.<br/> "rectangle"이 지정된 경우 영역은 X, Y, 너비 및 높이 치수를 가집니다. X 및 Y 좌표는 0.0 ~ 1.0의 정규화된 배율 단위로 영역의 왼쪽 상단 XY 좌표를 나타냅니다. 너비와 높이는 0.0 ~ 1.0의 정규화된 배율 단위로 영역의 크기를 나타냅니다. 현재 버전에서, X, Y, 너비 및 높이는 0, 0 및 1, 1로 항상 고정됩니다. <br/>"polygon"이 지정된 경우 영역은 지점 단위의 치수를 가집니다. <br/> |
| 조각 |메타데이터는 조각이라고 하는 다른 세그먼트로 청크 분할됩니다. 각 조각에는 시작, 기간, 간격 번호 및 이벤트가 포함됩니다. 이벤트가 없는 조각은 해당 시작 시간 및 기간 동안에 검색된 동작이 없음을 의미합니다. |
| 대괄호 [] |각 대괄호는 이벤트에서 하나의 간격을 나타냅니다. 해당 간격에 대한 빈 대괄호는 검색된 동작이 없음을 의미합니다. |
| 위치 |이벤트 아래의 이 새 항목은 동작이 발생한 위치를 나열합니다. 검색 영역보다 더 구체적입니다. |

다음 JSON 예제에서는 결과를 보여줍니다.

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>제한 사항
* 지원되는 입력 동영상 형식에는 MP4, MOV 및 WMV가 있습니다.
* 동작 검색은 고정 배경 동영상에 최적화되어 있습니다. 알고리즘은 조명 변화, 그림자와 같은 거짓 경보를 줄이는 데 중점을 둡니다.
* 일부 동작은 기술적인 문제(예: 야간 투시 동영상, 반투명 개체 및 작은 개체)로 인해 검색되지 않을 수도 있습니다.

## <a name="net-sample-code"></a>.NET 샘플 코드

다음 프로그램은 방법을 보여 줍니다.

1. 자산을 만들고 미디어 파일을 자산에 업로드합니다.
2. 다음 JSON 기본 설정을 포함하는 구성 파일을 기반으로 동영상 동작 검색 작업을 만듭니다. 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
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

namespace VideoMotionDetection
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

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

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
[Azure Media Services 동작 탐지기 블로그](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure Media Services 분석 개요](media-services-analytics-overview.md)

[Azure 미디어 분석 데모](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

