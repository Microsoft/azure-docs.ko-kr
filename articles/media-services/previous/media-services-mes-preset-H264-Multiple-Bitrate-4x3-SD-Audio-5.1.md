---
title: H264 다중 비트 전송률 4x3 SD Audio 5.1 | Microsoft Docs
description: 이 항목은 **H264 다중 비트 전송률 4x3 SD Audio 5.1** 태스크 미리 설정에 대한 개요를 제공합니다.
author: WenJason
manager: digimobile
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 24faec4f-a69c-4ae5-afd4-308e03046a3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/19/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: 7f5ea80a055c34cddeab042c33071c6f5799a443
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463670"
---
# <a name="h264-multiple-bitrate-4x3-sd-audio-51"></a>H264 다중 비트 전송률 4x3 SD Audio 5.1
`Media Encoder Standard`는 인코딩 작업을 만들 때 사용할 수 있는 인코딩 미리 설정을 정의합니다. `preset name`을 사용하여 미디어 파일을 인코딩할 형식을 지정할 수 있습니다. 또는 자신만의 JSON 또는 XML 기반 미리 설정(UTF-8 또는 UTF-16 인코딩을 사용하여)을 만들 수 있습니다. 그런 다음 사용자 지정 미리 설정을 인코더에 전달합니다. `Media Encoder Standard` 인코더에서 지원되는 모든 미리 설정 이름의 목록을 보려면 [Media Encoder Standard에 대한 작업 미리 설정](media-services-mes-presets-overview.md)을 참조하세요.  
  
 이 항목은 XML 및 JSON 형식의 `H264 Multiple Bitrate 4x3 SD Audio 5.1` 미리 설정을 보여줍니다.  
  
 미리 설정은 AAC 5.1 오디오 및 1600kbps에서 400kbps에 이르는 5 GOP 정렬 MP4 파일 집합을 생성합니다. 미리 설정의 프로필, 비트 전송률, 샘플링 주기 등에 대한 자세한 내용을 보려면 아래 정의된 XML 또는 JSON을 검토하세요. 각 요소의 의미 및 유효한 값에 대한 설명은 [Media Encoder Standard 스키마](media-services-mes-schema.md)를 참조하세요.  
  
> [!NOTE]
>  계층에서 `Width` 및 `Height` 값을 수정하는 경우 가로 세로 비율을 일관적으로 유지해야 합니다. 예를 들면 다음과 같습니다. 1920x1080, 1280x720, 1080x576, 640x360. 가로 세로 비율을 혼합하여 사용하지 말아야 합니다. 예: 1280x720, 720x480, 640x360.  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>1600</Bitrate>  
          <Width>640</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>1600</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>1300</Bitrate>  
          <Width>640</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>1300</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>800</Bitrate>  
          <Width>480</Width>  
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
          <MaxBitrate>800</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>600</Bitrate>  
          <Width>480</Width>  
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
          <MaxBitrate>600</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>400</Bitrate>  
          <Width>360</Width>  
          <Height>240</Height>  
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
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 1600,  
          "MaxBitrate": 1600,  
          "BufferWindow": "00:00:05",  
          "Width": 640,  
          "Height": 480,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 1300,  
          "MaxBitrate": 1300,  
          "BufferWindow": "00:00:05",  
          "Width": 640,  
          "Height": 480,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 800,  
          "MaxBitrate": 800,  
          "BufferWindow": "00:00:05",  
          "Width": 480,  
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
          "Bitrate": 600,  
          "MaxBitrate": 600,  
          "BufferWindow": "00:00:05",  
          "Width": 480,  
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
          "Width": 360,  
          "Height": 240,  
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
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
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
