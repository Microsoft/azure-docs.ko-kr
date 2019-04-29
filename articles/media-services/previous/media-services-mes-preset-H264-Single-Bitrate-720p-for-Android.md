---
title: Android용 H264 단일 비트 전송률 720p | Microsoft Docs
description: 이 항목은 **Android용 H264 단일 비트 전송률 720p** 태스크 미리 설정에 대한 개요를 제공합니다.
author: WenJason
manager: digimobile
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 4f9569a3-5aca-4fea-8242-024925a8af90
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/19/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: da44cf33882d2658b20f117053d486177117a5a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61131021"
---
# <a name="h264-single-bitrate-720p-for-android"></a>Android용 H264 단일 비트 전송률 720p
`Media Encoder Standard`는 인코딩 작업을 만들 때 사용할 수 있는 인코딩 미리 설정을 정의합니다. `preset name`을 사용하여 미디어 파일을 인코딩할 형식을 지정할 수 있습니다. 또는 자신만의 JSON 또는 XML 기반 미리 설정(UTF-8 또는 UTF-16 인코딩을 사용하여)을 만들 수 있습니다. 그런 다음 사용자 지정 미리 설정을 인코더에 전달합니다. `Media Encoder Standard` 인코더에서 지원되는 모든 미리 설정 이름의 목록을 보려면 [Media Encoder Standard에 대한 작업 미리 설정](media-services-mes-presets-overview.md)을 참조하세요.  
  
이 항목은 XML 및 JSON 형식의 `H264 Single Bitrate 720p for Android` 미리 설정을 보여줍니다.  
  
이 미리 설정은 스테레오 AAC 및 비트 전송률 2000kbps의 단일 MP4 파일을 생성합니다. 미리 설정의 프로필, 비트 전송률, 샘플링 주기 등에 대한 자세한 내용을 보려면 아래 정의된 XML 또는 JSON을 검토하세요. 미리 설정에 포함된 각 요소의 의미 및 유효한 값에 대한 설명은 [Media Encoder Standard 스키마](media-services-mes-schema.md) 항목을 참조하세요.  
  
 XML  
  
```
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:05</KeyFrameInterval>
      <SceneChangeDetection>true</SceneChangeDetection>
      <H264Layers>
        <H264Layer>
          <Bitrate>2000</Bitrate>
          <Width>1280</Width>
          <Height>720</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Baseline</Profile>
          <Level>3.1</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>false</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2000</MaxBitrate>
        </H264Layer>
      </H264Layers>
      <Chapters />
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>192</Bitrate>
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
      "KeyFrameInterval": "00:00:05",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Profile": "Baseline",
          "Level": "3.1",
          "Bitrate": 2000,
          "MaxBitrate": 2000,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
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
      "Bitrate": 192,
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
