---
title: Azure 미디어 분석 OCR로 텍스트 디지털화 | Microsoft Docs
description: Azure 미디어 분석 OCR(광학 문자 인식)을 사용하면 비디오 파일의 텍스트 콘텐츠를 편집 및 검색 가능한 디지털 텍스트로 변환할 수 있습니다.  이 방법을 사용하면 미디어의 비디오 신호에서 의미 있는 메타데이터를 자동으로 추출할 수 있습니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 91fad34073d7505c596bedfb6c93946ee7393dd7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825611"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Azure 미디어 분석을 사용하여 비디오 파일의 텍스트 콘텐츠를 디지털 텍스트로 변환  
## <a name="overview"></a>개요
비디오 파일에서 텍스트 콘텐츠를 추출하고 편집 및 검색 가능한 디지털 텍스트를 생성해야 할 경우 Azure 미디어 분석 OCR(광학 문자 인식)을 사용하는 것이 좋습니다. 이 Azure 미디어 프로세서는 비디오 파일의 텍스트 콘텐츠를 검색하고 사용할 수 있는 텍스트 파일을 생성합니다. OCR을 사용하면 미디어의 비디오 신호에서 의미 있는 메타데이터를 자동으로 추출할 수 있습니다.

검색 엔진과 함께 사용할 경우 텍스트에 따라 미디어를 쉽게 인덱싱하고 콘텐츠 검색 기능을 향상할 수 있습니다. 이러한 방식은 비디오 녹화 또는 슬라이드 쇼 프레젠테이션의 화면 캡처와 같이 텍스트가 풍부한 비디오에서 특히 유용합니다. Azure OCR 미디어 프로세서는 디지털 텍스트에 맞게 최적화됩니다.

**Azure 미디어 OCR** 미디어 프로세서는 현재 미리 보기로 제공됩니다.

이 문서에서는 **Azure Media OCR**에 대한 세부 정보를 제공하고 .NET용 Media Services SDK와 함께 사용하는 방법을 보여 줍니다. 자세한 내용과 예는 [이 블로그](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/)를 참조하세요.

## <a name="ocr-input-files"></a>OCR 입력 파일
동영상 파일입니다. 현재 다음 형식이 지원됩니다. MP4, MOV 및 WMV.

## <a name="task-configuration"></a>작업 구성
작업 구성(사전 설정) **Azure 미디어 OCR**로 작업을 만들 때에는 JSON 또는 XML을 사용하여 구성 사전 설정을 지정해야 합니다. 

>[!NOTE]
>OCR 엔진은 높이/너비의 유효한 입력으로 최소 40픽셀에서 최대 32000픽셀의 이미지 영역만 차지합니다.
>

### <a name="attribute-descriptions"></a>특성 설명
| 특성 이름 | 설명 |
| --- | --- |
|AdvancedOutput| AdvancedOutput을 true로 설정하면 JSON 출력에는 모든 단일 단어(구 및 지역 외에)에 대해 위치 데이터가 포함됩니다. 이러한 세부 정보를 표시하지 않으려면 flag를 false로 설정합니다. 기본값은 False입니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/)를 참조하세요.|
| 언어 |(선택 사항) 검색할 텍스트의 언어에 대해 설명합니다. 다음 중 하나 AutoDetect(기본값), Arabic, ChineseSimplified, ChineseTraditional, Czech Danish, Dutch, English, Finnish, French, German, Greek, Hungarian, Italian, Japanese, Korean, Norwegian, Polish, Portuguese, Romanian, Russian, SerbianCyrillic, SerbianLatin, Slovak, Spanish, Swedish, Turkish |
| TextOrientation |(선택 사항) 검색할 텍스트의 방향에 대해 설명합니다.  "Left"는 모든 문자의 위쪽이 왼쪽을 향함을 나타냅니다.  기본 텍스트(예: 책에서 사용되는 텍스트)를 "위쪽" 방향으로 호출할 수 있습니다.  다음 중 하나 AutoDetect(기본값), Up, Right, Down, Left |
| TimeInterval |(선택 사항) 샘플링 속도를 설명합니다.  기본값은 1/2초 간격입니다.<br/>JSON 형식 – HH:mm:ss.SSS(기본값 00:00:00.500)<br/>XML 형식 – W3C XSD 기간 기본 형식(기본 PT0.5) |
| DetectRegions |(선택 사항) 텍스트를 검색할 비디오 프레임 내의 영역을 지정하는 DetectRegion 개체의 배열입니다.<br/>DetectRegion 개체는 다음 4개 정수 값으로 구성됩니다.<br/>Left - 왼쪽 여백에서 픽셀<br/>Top - 위쪽 여백에서 픽셀<br/>Width – 영역 너비(픽셀)<br/>Height – 영역 높이(픽셀) |

#### <a name="json-preset-example"></a>JSON 사전 설정 예제

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>XML 사전 설정 예제

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""https://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""https://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""https://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>OCR 출력 파일
OCR 미디어 프로세서의 출력은 JSON 파일입니다.

### <a name="elements-of-the-output-json-file"></a>출력 JSON 파일의 요소
비디오 OCR 출력은 비디오에서 찾을 수 있는 문자에 대한 시분할 데이터를 제공합니다.  분석하려는 정확한 단어 쪽을 향하는 방향 또는 언어와 같은 특성을 사용할 수 있습니다. 

출력은 다음 특성을 포함합니다.

| 요소 | 설명 |
| --- | --- |
| 시간 간격 |동영상의 초당 "틱" |
| Offset |타임스탬프의 시간 오프셋 동영상 API 버전 1.0에서는 항상 0입니다. |
| 프레임 속도 |동영상의 초당 프레임 수 |
| width |픽셀 단위의 동영상 너비 |
| height |픽셀 단위의 동영상 높이 |
| 조각 |메타데이터가 청크되는 시간 기반 비디오 청크 배열 |
| start |"틱" 단위의 조각 시작 시간 |
| duration |"틱" 단위의 조각 길이 |
| interval |지정된 조각 내의 각 이벤트 간격 |
| events |영역을 포함하는 배열 |
| region |검색된 단어 또는 구를 나타내는 개체 |
| 언어 |지역 내에서 검색된 텍스트의 언어 |
| orientation |지역 내에서 검색된 텍스트의 방향 |
| lines |지역 내에서 검색된 텍스트의 줄 배열 |
| text |실제 텍스트 |

### <a name="json-output-example"></a>JSON 출력 예제
다음 출력 예제는 일반 동영상 정보 및 몇 가지 동영상 조각을 포함합니다. 모든 동영상 조각에는 OCR MP에 의해 언어 및 텍스트 방향에 따라 검색되는 모든 영역이 포함됩니다. 또한 이러한 영역에는 이 영역의 모든 단어 줄과 이 줄에 포함된 줄의 텍스트, 줄의 위치 및 모든 단어 정보(단어 내용, 위치 및 신뢰도)가 들어 있습니다. 다음 예제에서는 줄 내에 주석을 추가합니다.

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>.NET 샘플 코드

다음 프로그램은 방법을 보여 줍니다.

1. 자산을 만들고 미디어 파일을 자산에 업로드합니다.
2. OCR 구성/사전 설정 파일을 사용하여 작업을 만듭니다.
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

namespace OCR
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

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

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

