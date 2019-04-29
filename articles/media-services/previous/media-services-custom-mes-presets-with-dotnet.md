---
title: Media Encoder Standard 사전 설정 사용자 지정 | Microsoft Docs
description: 이 항목에서는 미디어 인코더 표준 태스크 사전 설정을 사용자 지정하여 고급 인코딩을 수행하는 방법을 설명합니다. 그리고 Media Services .NET SDK를 사용하여 인코딩 태스크와 작업을 만드는 방법을 설명합니다. 또한 인코딩 작업에 사용자 지정 사전 설정을 제공하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: 39a1dd5c3d26eeb6545a96aa35f9457bd9859c21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247246"
---
# <a name="customizing-media-encoder-standard-presets"></a>Media Encoder Standard 사전 설정 사용자 지정  

## <a name="overview"></a>개요

이 문서에서는 사용자 지정 기본 설정을 사용하여 MES(Media Encoder Standard)에서 고급 인코딩을 수행하는 방법에 대해 설명합니다. 여기서는 .NET을 사용하여 인코딩 작업과 인코딩 작업을 실행하는 작업을 만듭니다.  

이 문서에서는 [H264 다중 비트 전송률 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) 기본 설정을 사용하고 레이어 수를 줄여 기본 설정을 사용자 지정하는 방법을 살펴봅니다. [Media Encoder Standard 기본 설정 사용자 지정](media-services-advanced-encoding-with-mes.md) 문서에서는 고급 인코딩 작업을 수행하는 데 사용할 수 있는 사용자 지정 기본 설정을 보여 줍니다.

> [!NOTE]
> 이 문서에 설명 된 사용자 지정 사전 설정을 사용할 수 없습니다 [Media Services V3](https://docs.microsoft.com/azure/media-services/latest/) 변환 또는 CLI 명령입니다. 참조 된 [v2에서 v3 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md) 대 한 자세한 내용은 합니다.

## <a id="customizing_presets"></a> MES 사전 설정 사용자 지정

### <a name="original-preset"></a>원래 사전 설정

[H264 다중 비트 전송률 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) 문서에서 정의한 JSON을 .json 확장명을 사용하는 파일에 저장합니다. 예를 들어 **CustomPreset_JSON.json**과 같습니다.

### <a name="customized-preset"></a>사용자 지정한 사전 설정

**CustomPreset_JSON.json** 파일을 열고 다음과 같이 보이도록 **H264Layers**에서 처음 세 개의 레이어를 제거합니다.

```json 
    {  
      "Version": 1.0,  
      "Codecs": [  
        {  
          "KeyFrameInterval": "00:00:02",  
          "H264Layers": [  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 1000,  
              "MaxBitrate": 1000,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 650,  
              "MaxBitrate": 650,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 400,  
              "MaxBitrate": 400,  
              "BufferWindow": "00:00:05",  
              "Width": 320,  
              "Height": 180,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            }  
          ],  
          "Type": "H264Video"  
        },  
        {  
          "Profile": "AACLC",  
          "Channels": 2,  
          "SamplingRate": 48000,  
          "Bitrate": 128,  
          "Type": "AACAudio"  
        }  
      ],  
      "Outputs": [  
        {  
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
          "Format": {  
            "Type": "MP4Format"  
          }  
        }  
      ]  
    }  
```

## <a id="encoding_with_dotnet"></a>Media Services .NET SDK를 사용하여 Encoding

다음 코드 예제에서는 Media Services .NET SDK를 사용하여 다음 작업을 수행합니다.

- 인코딩 작업을 만듭니다.
- 미디어 인코더 표준 인코더에 대한 참조를 가져옵니다.
- 이전 섹션에서 만든 사용자 지정 JSON 사전 설정을 로드합니다. 
  
        // Load the JSON from the local file.
        string configuration = File.ReadAllText(fileName);  

- 작업에 인코딩 작업을 추가합니다. 
- 인코딩할 입력 자산을 지정합니다.
- 인코딩된 자산을 포함하는 출력 자산을 만듭니다.
- 작업 진행 상태를 확인할 이벤트 처리기를 추가합니다.
- 작업을 제출합니다.
   
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

namespace CustomizeMESPresests
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

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

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

            // Encode and generate the output using custom presets.
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

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

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

## <a name="see-also"></a>참고 항목

- [CLI를 사용 하 여 사용자 지정 변환을 사용 하 여 인코딩하는 방법](../latest/custom-preset-cli-howto.md)
- [Media Services v3를 사용하여 인코딩](../latest/encoding-concept.md)

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
