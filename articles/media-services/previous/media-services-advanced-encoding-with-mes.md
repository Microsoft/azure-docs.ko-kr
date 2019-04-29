---
title: MES 사전 설정을 사용자 지정하여 고급 인코딩 수행 | Microsoft Docs
description: 이 항목에서는 미디어 인코더 표준 태스크 사전 설정을 사용자 지정하여 고급 인코딩을 수행하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: fadf1aa54f525fb3d4c414161583f8a89f2e4c05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230256"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>MES 사전 설정을 사용자 지정하여 고급 인코딩 수행 

## <a name="overview"></a>개요

이 항목에서는 MES(Media Encoder Standard) 사전 설정을 사용자 지정하는 방법을 보여 줍니다. [사전 설정을 사용자 지정하는 MES를 사용한 Encoding](media-services-custom-mes-presets-with-dotnet.md) 항목에서는 .NET을 사용하여 인코딩 태스크와 이 태스크를 실행하는 작업을 만드는 방법을 보여 줍니다. 사전 설정을 사용자 지정한 후에는 이 사용자 지정 사전 설정을 인코딩 작업에 제공해야 합니다. 

XML 사전 설정을 사용하는 경우 아래 XML 예제에 표시된 것처럼 요소 순서를 유지해야 합니다(예를 들어, KeyFrameInterval은 SceneChangeDetection 앞에 와야 함).

> [!NOTE] 
> 다양 한 Media Encoder Standard의 고급 Media Services v2 기능 하지 않습니다 v3에서 사용할 수 있습니다. 자세한 내용은 [간격이 기능](https://docs.microsoft.com/azure/media-services/latest/migrate-from-v2-to-v3#feature-gaps-with-respect-to-v2-apis)합니다.

## <a name="support-for-relative-sizes"></a>상대적 크기에 대한 지원

미리 보기를 생성하는 경우 출력 너비와 높이를 픽셀 단위로 반드시 지정할 필요는 없습니다. [1%, …, 100%] 범위에서 백분율로 지정할 수 있습니다.

### <a name="json-preset"></a>JSON 사전 설정
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>XML 사전 설정
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>미리 보기 생성

이 섹션에서는 미리 보기를 생성하는 기본 설정을 사용자 지정하는 방법을 보여줍니다. 아래에 정의된 사전 설정은 미리 보기를 생성하는 데 필요한 정보 뿐만 아니라 파일을 인코딩하는 방법에 대한 정보를 포함합니다. [이 섹션](media-services-mes-presets-overview.md)에 문서화된 MES 사전 설정 중 하나를 가져와서 미리 보기를 생성하는 코드를 추가할 수 있습니다.  

> [!NOTE]
> 다음 기본 설정에서 **SceneChangeDetection** 설정은 단일 비트 전송률 비디오로 Encoding하는 경우에만 true로 설정할 수 있습니다. 다중 비트 전송률 비디오로 인코딩하고 **SceneChangeDetection** 을 true로 설정하면 인코더에서 오류를 반환합니다.  
>
>

스키마에 대한 자세한 내용은 [이 항목](media-services-mes-schema.md) 을 참조하세요.

[고려 사항](#considerations) 섹션을 검토해야 합니다.

### <a id="json"></a>JSON 사전 설정
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
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
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
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a id="xml"></a>XML 사전 설정
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
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

### <a name="considerations"></a>고려 사항

고려 사항은 다음과 같습니다.

* 시작/단계/범위에 대한 명시적 타임스탬프 사용 시 입력 소스의 길이가 1분 이상이라고 가정합니다.
* Jpg/Png/BmpImage 요소에는 Start, Step 및 Range 문자열 특성이 있으며, 이러한 특성은 다음과 같이 해석될 수 있습니다.

  * 음수가 아닌 정수인 경우 프레임 번호(예: "Start": "120")
  * % 접미사로 표시된 경우 소스 기간 기준(예: "Start": "15%") 또는
  * HH:MM:SS... 형식으로 표현되는 경우 타임스탬프(예: "Start": "00:01:00"

    표기법을 원하는 대로 혼용하거나 일치시킬 수 있습니다.

    또한 Start는 콘텐츠의 첫 번째 "흥미로운" 프레임의 결정을 시도하는 특수 Macro:{Best}를 지원합니다. 참고: (Step 및 Range는 Start를 {Best}로 설정하면 무시됨)
  * 기본값: Start:{Best}
* 각 이미지 형식에 대해 출력 형식을 명시적으로 제공해야 합니다. Jpg/Png/BmpFormat. 출력 형식이 있는 경우 MES는 JpgVideo를 JpgFormat에 일치시키는 식으로 진행합니다. OutputFormat은 새 이미지 코덱 특유의 Macro: {Index}를 도입하며, 이는 이미지 출력 형식에 대해 존재해야(한 번만) 합니다.

## <a id="trim_video"></a>비디오 자르기(클리핑)
이 섹션에서는 입력이 소위 중 2층 파일이나 주문형 파일인 경우 입력 비디오를 클립하거나 자르는 인코더 사전 설정을 수정하는 방법을 설명합니다. 인코더는 라이브 스트림에서 캡처되거나 보관된 자산을 클립하거나 자르는 데 사용할 수도 있습니다. 이에 대한 세부 정보는 [이 블로그](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)에서 확인할 수 있습니다.

비디오를 자르려면 [이 섹션](media-services-mes-presets-overview.md)에 문서화된 MES 사전 설정 중 하나를 수행하고 **Sources** 요소를 아래와 같이 수정할 수 있습니다. StartTime 값이 입력 비디오의 절대 타임스탬프와 일치해야 합니다. 예를 들어 입력 비디오의 첫 번째 프레임에 12:00:10.000 타임스탬프가 있으면 StartTime은 12:00:10.000 이상이어야 합니다. 아래 예에서는 입력 비디오의 시작 타임스탬프가 0인 것으로 가정합니다. **Sources** 는 사전 설정의 맨 앞에 있어야 합니다.

### <a id="json"></a>JSON 사전 설정
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
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

### <a name="xml-preset"></a>XML 사전 설정
비디오를 자르려면 [여기](media-services-mes-presets-overview.md) 에서 문서화된 MES 사전 설정 중 하나를 수행하고 **원본** 요소를 아래와 같이 수정할 수 있습니다.

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
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
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

## <a id="overlay"></a>오버레이 만들기

미디어 인코더 표준을 사용하면 이미지를 기존 비디오에 오버레이할 수 있습니다. 현재 png, jpg, gif 및 bmp 형식이 지원됩니다. 아래에 정의된 사전 설정은 비디오 오버레이의 기본적인 예입니다.

또한 사전 설정 파일을 정의하는 것 외에도 Media Services를 통해 오버레이 이미지에 해당하는 자산의 파일 및 이미지를 오버레이하려는 원본 비디오에 해당하는 파일을 알 수 있습니다. 비디오 파일은 **기본** 파일이어야 합니다.

.NET을 사용하는 경우 [이 항목](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet)에 정의된 .NET 예제에 다음 두 함수를 추가합니다. **UploadMediaFilesFromFolder** 함수는 폴더에서 파일(예: BigBuckBunny.mp4 및 Image001.png)을 업로드하고 mp4 파일을 자산의 기본 파일로 설정합니다. **EncodeWithOverlay** 함수는 전달된 사용자 지정 사전 설정 파일(예: 다음에 나오는 사전 설정)을 사용하여 인코딩 태스크를 만듭니다.


    static public IAsset UploadMediaFilesFromFolder(string folderPath)
    {
        IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
    
        foreach (var af in asset.AssetFiles)
        {
            // The following code assumes 
            // you have an input folder with one MP4 and one overlay image file.
            if (af.Name.Contains(".mp4"))
                af.IsPrimary = true;
            else
                af.IsPrimary = false;
    
            af.Update();
        }
    
        return asset;
    }

    static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("Media Encoder Standard Job");
        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(customPresetFileName);

        // Create a task
        ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input assets to be encoded.
        // This asset contains a source file and an overlay file.
        task.InputAssets.Add(assetSource);

        // Add an output asset to contain the results of the job. 
        task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }


> [!NOTE]
> 현재 제한 사항:
>
> 오버레이 불투명도 설정이 지원되지 않습니다.
>
> 원본 비디오 파일과 오버레이 이미지 파일은 같은 자산에 있어야 하며 이 자산에서 비디오 파일을 기본 파일로 설정해야 합니다.
>
>

### <a name="json-preset"></a>JSON 사전 설정
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a name="xml-preset"></a>XML 사전 설정
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


## <a id="silent_audio"></a>입력에 오디오가 없을 때 조용한 오디오 트랙 삽입
기본적으로 비디오만 포함하며 오디오는 없는 입력을 인코더로 보내면 출력 자산에는 비디오 데이터만 들어 있는 파일이 포함됩니다. 일부 플레이어는 해당 출력 스트림을 처리할 수 없습니다. 이 시나리오에서는 이 설정을 사용하여 출력에 조용한 오디오 트랙을 추가하는 인코더를 강제할 수 있습니다.

입력에 오디오가 없을 때 조용한 오디오 트랙을 포함하는 자산을 생성하도록 인코더를 적용하려면 "InsertSilenceIfNoAudio" 값을 지정합니다.

[이 섹션](media-services-mes-presets-overview.md)에 문서화된 MES 사전 설정 중 하나를 가져온 후에 다음과 같이 수정할 수 있습니다.

### <a name="json-preset"></a>JSON 사전 설정
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>XML 사전 설정
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a id="deinterlacing"></a>자동 디인터레이스 사용 안 함
인터레이스 콘텐츠가 자동으로 디인터레이스되도록 원하는 고객은 아무 작업도 수행할 필요가 없습니다. 자동 디인터레이스가 설정(기본값)된 경우 MES에서는 인터레이스 프레임 및 인터레이스로 표시된 디인터레이스 프레임만 자동으로 검색합니다.

자동 디인터레이스를 해제할 수 있습니다. 이 옵션은 권장되지 않습니다.

### <a name="json-preset"></a>JSON 사전 설정
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>XML 사전 설정
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a id="audio_only"></a>오디오 전용 사전 설정
이 섹션에서는 두 개의 오디오 전용 MES 사전 설정인 AAC 오디오 및 AAC 고급 음질 오디오를 보여 줍니다.

### <a name="aac-audio"></a>AAC 오디오
    {
      "Version": 1.0,
      "Codecs": [
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
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="aac-good-quality-audio"></a>AAC 고급 음질 오디오
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="concatenate"></a>둘 이상의 비디오 파일 연결

다음 예제에서는 사전 설정을 생성하여 둘 이상의 비디오 파일을 연결하는 방법을 보여줍니다. 가장 일반적인 시나리오는 기본 비디오에 헤더나 트레일러를 추가하려는 경우입니다. 편집 중인 비디오 파일이 속성(비디오 해상도, 프레임 속도, 오디오 트랙 수 등)을 공유하는 경우에 적합합니다. 프레임 속도나 오디오 트랙 수가 서로 다른 비디오를 섞지 않게 주의가 필요합니다.

>[!NOTE]
>연결 기능의 현재 설계에서는 입력된 비디오 클립이 해상도, 프레임 속도 측면에서 일관적이라고 예상합니다. 

### <a name="requirements-and-considerations"></a>요구 사항 및 고려 사항

* 입력 비디오에는 오디오 트랙이 하나만 있어야 합니다.
* 입력 비디오는 모두 프레임 속도가 같아야 합니다.
* 비디오는 별도의 자산에 업로드하고 비디오를 각 자산의 기본 파일로 설정해야 합니다.
* 비디오의 기간을 알아야 합니다.
* 아래의 사전 설정 예제에서는 모든 입력 비디오가 타임스탬프 0에서 시작한다고 가정합니다. 시작 타임스탬프가 다르면 StartTime 값을 수정해야 합니다. 라이브 보관에서는 이런 경우가 일반적입니다.
* JSON 사전 설정은 입력 자산의 AssetID 값에 대한 명시적 참조를 만듭니다.
* 샘플 코드에서는 JSON 사전 설정이 로컬 파일(예: "C:\supportFiles\preset.json")에 저장되었다고 가정합니다. 또한 두 비디오 파일을 업로드하여 두 자산을 만들었으며 사용자가 그에 따른 AssetID 값을 알고 있다고 가정합니다.
* 코드 조각 및 JSON 사전 설정은 두 비디오 파일을 연결하는 예제를 보여줍니다. 이 예를 다음을 통해 3개 이상의 비디오로 확장할 수 있습니다.

  1. task.InputAssets.Add()를 반복 호출하여 순서대로 더 많은 비디오 추가
  2. 같은 순서로 다른 항목을 추가하여 JSON에서 "Sources" 요소를 그에 맞게 편집

### <a name="net-code"></a>.NET 코드

    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job.
    // This output is specified as AssetCreationOptions.None, which
    // means the output asset is not encrypted.
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

### <a name="json-preset"></a>JSON 사전 설정

연결하려는 자산의 ID와 각 비디오에 적합한 시간 세그먼트로 사용자 지정 사전 설정을 업데이트합니다.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
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
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="crop"></a>미디어 인코더 표준으로 비디오 자르기
[미디어 인코더 표준으로 비디오 자르기](media-services-crop-video.md) 항목을 참조하세요.

## <a id="no_video"></a>입력에 비디오가 없을 때 비디오 트랙 삽입

기본적으로 오디오만 포함하며 비디오는 없는 입력을 인코더로 보내면 출력 자산에는 오디오 데이터만 들어 있는 파일이 포함됩니다. Azure Media Player( [이 항목](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)참조)를 비롯한 일부 플레이어는 이러한 스트림을 처리하지 못할 수도 있습니다. 해당 시나리오에서는 이 설정을 사용하여 인코더가 출력에 단색 비디오 트랙을 추가하도록 강제 지정할 수 있습니다.

> [!NOTE]
> 인코더가 출력 비디오 트랙을 삽입하도록 강제 지정하면 출력 자산의 크기가 증가하므로 인코딩 태스크에 대한 비용이 발생합니다. 따라서 테스트를 실행하여 이러한 크기 증가가 월 요금에 주는 영향이 적절한 수준인지를 확인해야 합니다.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>최저 비트 전송률에서만 비디오 삽입

["H264 다중 비트 전송률 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) 와 같은 다중 비트 전송률 인코딩 사전 설정을 사용하여 비디오 파일과 오디오 전용 파일이 혼합되어 있는 입력 카탈로그 전체를 스트리밍용으로 인코딩한다고 가정해 보겠습니다. 이 시나리오에서는 입력에 비디오가 없으면 모든 출력 비트 속도에서 비디오를 삽입하는 대신 인코더가 최저 비트 속도에서만 단색 비디오 트랙을 삽입하도록 강제 지정할 수 있습니다. 이렇게 하려면 **InsertBlackIfNoVideoBottomLayerOnly** 플래그를 사용해야 합니다.

[이 섹션](media-services-mes-presets-overview.md)에 문서화된 MES 사전 설정 중 하나를 가져온 후에 다음과 같이 수정할 수 있습니다.

#### <a name="json-preset"></a>JSON 사전 설정
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML 사전 설정

XML을 사용하는 경우 **H264Video** 요소에 대한 특성으로 Condition="InsertBlackIfNoVideoBottomLayerOnly"를 사용하고 **AACAudio**에 대한 특성으로 Condition="InsertSilenceIfNoAudio"를 사용합니다.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>모든 출력 비트 전송률에서 비디오 삽입
["H264 다중 비트 전송률 720p"](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) 와 같은 다중 비트 전송률 인코딩 사전 설정을 사용하여 비디오 파일과 오디오 전용 파일이 혼합되어 있는 입력 카탈로그 전체를 스트리밍용으로 인코딩한다고 가정해 보겠습니다. 이 시나리오에서는 입력에 비디오가 없으면 인코더가 모든 출력 비트 속도에서 단색 비디오 트랙을 삽입하도록 강제 지정할 수 있습니다. 이렇게 하면 출력 자산의 비디오 트래픽 및 오디오 트랙 수가 모두 같아집니다. 이렇게 하려면 "InsertBlackIfNoVideo" 플래그를 지정해야 합니다.

[이 섹션](media-services-mes-presets-overview.md)에 문서화된 MES 사전 설정 중 하나를 가져온 후에 다음과 같이 수정할 수 있습니다.

#### <a name="json-preset"></a>JSON 사전 설정
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML 사전 설정

XML을 사용하는 경우 **H264Video** 요소에 대한 특성으로 Condition="InsertBlackIfNoVideo"를 사용하고 **AACAudio**에 대한 특성으로 Condition="InsertSilenceIfNoAudio"를 사용합니다.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a id="rotate_video"></a>비디오 회전
[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)는 0/90/180/270도 회전을 지원합니다. 기본 동작은 들어오는 비디오 파일에서 회전 메타데이터를 검색하여 그에 맞게 보정하는 "Auto"입니다. 다음 **Sources** 요소를 [이 섹션](media-services-mes-presets-overview.md)에 정의된 사전 설정 중 하나에 포함합니다.

### <a name="json-preset"></a>JSON 사전 설정
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...
### <a name="xml-preset"></a>XML 사전 설정
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

또한 회전 보정이 트리거된 경우 인코더가 사전 설정에서 너비 및 높이를 해석하는 방법에 대한 자세한 내용은 [이](media-services-mes-schema.md#PreserveResolutionAfterRotation) 항목을 참조하세요.

"0" 값을 사용하여 인코더가 입력 비디오에서 회전 메타데이터를 무시한다는 것을 나타낼 수 있습니다(있는 경우).

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>관련 항목
[Media Services Encoding 개요](media-services-encode-asset.md)
