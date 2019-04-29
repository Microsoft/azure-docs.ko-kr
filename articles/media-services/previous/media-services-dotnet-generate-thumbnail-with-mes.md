---
title: .NET과 함께 미디어 인코더 표준을 사용하여 미리 보기를 생성하는 방법
description: 이 항목에서는 .NET과 함께 Media Encoder Standard를 사용하여 동시에 자산을 인코드하고 미리 보기를 생성하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bc29c098bcf7ef1d1a2e2532a00c95f0ec7e927
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61244232"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>.NET과 함께 미디어 인코더 표준을 사용하여 미리 보기를 생성하는 방법 

Media Encoder Standard를 사용하여 입력 동영상에서 하나 이상의 미리 보기를 [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 또는 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 이미지 파일 형식으로 생성할 수 있습니다. 이미지를 생성하는 작업을 제출하거나 미리 보기 생성을 인코딩과 결합할 수 있습니다. 이 문서에서는 이러한 시나리오를 위해 몇 가지 샘플 XML 및 JSON 미리 보기 기본 설정을 제공합니다. 문서 끝에는 Media Services .NET SDK를 사용하여 인코딩 작업을 완료하는 방법을 보여 주는 [예제 코드](#code_sample)가 나와 있습니다.

예제 사전 설정에 사용되는 요소에 대한 자세한 내용은 [Media Encoder Standard 스키마](media-services-mes-schema.md)를 참조하세요.

[고려 사항](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) 섹션을 검토해야 합니다.
    
## <a name="example-of-a-single-png-file-preset"></a>"단일 PNG 파일" 사전 설정의 예

다음 JSON 및 XML 기본 설정은 입력 동영상의 첫 몇 초 분량에서 단일 출력 PNG 파일을 생성하는 데 사용할 수 있습니다. 여기서 인코더는 “흥미로운” 프레임을 찾기 위해 최상의 노력을 합니다. 출력 이미지 크기는 100%로 설정되어 있습니다. 다시 말해서, 입력 동영상과 크기와 일치합니다. 또한 "Outputs"의 “Format” 설정이 “Codecs” 섹션의 "PngLayers" 사용과 일치해야 합니다. 

### <a name="json-preset"></a>JSON 사전 설정

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>XML 사전 설정

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>"일련의 JPEG 이미지" 사전 설정의 예

다음 JSON 및 XML 기본 설정은 입력 타임라인의 타임스탬프 5%, 15%, …, 95%에서 일련의 10개의 이미지를 생성하는 데 사용할 수 있습니다. 여기서 이미지 크기는 입력 동영상의 1/4로 지정되어 있습니다.

### <a name="json-preset"></a>JSON 사전 설정

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML 사전 설정
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>"특정 타임스탬프의 1개 이미지" 사전 설정의 예

다음 JSON 및 XML 기본 설정은 입력 동영상의 30초 마크에 단일 JPEG 이미지를 생성하는 데 사용할 수 있습니다. 이 기본 설정에서는 입력 동영상이 30초를 넘을 것으로 예상합니다(그렇지 않으면 작업이 실패함).

### <a name="json-preset"></a>JSON 사전 설정

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML 사전 설정
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>"다른 해상도의 미리 보기" 사전 설정의 예

다음 사전 설정은 하나의 작업의 다른 해상도에서 미리 보기를 생성하는 데 사용될 수 있습니다. 예제에서는 입력 타임라인의 위치 5%, 15%, …, 95%에서 인코더가 입력 동영상 해상도 100%에서 하나 그리고 50%에서 다른 하나로, 두 개의 이미지를 생성합니다.

FileName에서 {해상도} 매크로를 사용하는 것은 출력 이미지의 파일 이름을 생성하는 동안 사전 설정의 인코딩 섹션에 지정한 너비 및 높이를 사용하는 인코더를 나타냅니다. 또한 서로 다른 이미지를 쉽게 구분할 수 있습니다.

### <a name="json-preset"></a>JSON 사전 설정

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML 사전 설정
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>인코딩하는 동안 미리 보기 생성의 예

위의 모든 예제는 이미지만을 생성하는 인코딩 작업을 전송하는 방법을 설명했지만 미리 보기 생성으로 동영상/오디오 인코딩을 결합할 수도 있습니다. 다음 JSON 및 XML 사전 설정은 **Media Encoder Standard**에서 인코딩하는 동안 미리 보기를 생성하도록 합니다.

### <a id="json"></a>JSON 사전 설정
스키마에 대한 자세한 내용은 [이 문서](https://msdn.microsoft.com/library/mt269962.aspx)를 참조하세요.

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a id="xml"></a>XML 사전 설정
스키마에 대한 자세한 내용은 [이 문서](https://msdn.microsoft.com/library/mt269962.aspx)를 참조하세요.

```csharp
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a id="code_sample"></a>.NET을 사용하여 동영상 인코드 및 미리 보기 생성

다음 코드 예제에서는 Media Services .NET SDK를 사용하여 다음 작업을 수행합니다.

* 인코딩 작업을 만듭니다.
* 미디어 인코더 표준 인코더에 대한 참조를 가져옵니다.
* 미리 보기를 생성하는 데 필요한 정보 및 Encoding 기본 설정이 포함된 기본 설정 [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) 또는 [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json)을 로드합니다. 이 [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) 또는 [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json)을 파일에 저장하고 다음 코드를 사용하여 파일을 로드할 수 있습니다.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* 작업에 단일 인코딩을 추가합니다. 
* 인코딩할 입력 자산을 지정합니다.
* 인코딩된 자산을 포함하는 출력 자산을 만듭니다.
* 작업 진행 상태를 확인할 이벤트 처리기를 추가합니다.
* 작업을 제출합니다.

개발 환경을 설정하는 방법에 대한 지침은 [.NET을 사용한 Media Services 개발](media-services-dotnet-how-to-use.md) 문서를 참조하세요.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
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

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
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

## <a name="considerations"></a>고려 사항
고려 사항은 다음과 같습니다.

* 시작/단계/범위에 대한 명시적 타임스탬프 사용 시 입력 소스의 길이가 1분 이상이라고 가정합니다.
* Jpg/Png/BmpImage 요소에는 Start, Step 및 Range 문자열 특성이 있으며, 이러한 특성은 다음과 같이 해석될 수 있습니다.
  
  * 음수가 아닌 정수인 경우 프레임 번호(예: "Start": "120")
  * % 접미사로 표시된 경우 소스 기간 기준(예: "Start": "15%") 또는
  * HH:MM:SS... 형식. 예: “Start”: "00:01:00"
    
    표기법을 원하는 대로 혼용하거나 일치시킬 수 있습니다.
    
    또한 Start는 콘텐츠의 첫 번째 "흥미로운" 프레임의 결정을 시도하는 특수 Macro:{Best}를 지원합니다. 참고: (Step 및 Range는 Start를 {Best}로 설정하면 무시됨)
  * 기본값: Start:{Best}
* 각 이미지 형식에 대해 출력 형식을 명시적으로 제공해야 합니다. Jpg/Png/BmpFormat. 출력 형식이 있는 경우 MES는 JpgVideo를 JpgFormat에 일치시키는 식으로 진행합니다. OutputFormat은 새 이미지 코덱 특유의 Macro: {Index}를 도입하며, 이는 이미지 출력 형식에 대해 존재해야(한 번만) 합니다.

## <a name="next-steps"></a>다음 단계

인코딩 작업이 보류 중인 동안 [작업 진행 상태](media-services-check-job-progress.md)를 확인할 수 있습니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>관련 항목
[Media Services Encoding 개요](media-services-encode-asset.md)

