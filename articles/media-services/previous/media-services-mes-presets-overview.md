---
title: MES(Media Encoder Standard)에 대한 작업 미리 설정 | Microsoft Docs
description: 이 항목에서는 MES(Media Encoder Standard)에 대한 서비스 정의 샘플 미리 설정에 대해 간단히 설명합니다.
author: Juliako
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 50c52369a5a957a4dd6279cac5079e2dea023106
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463406"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>MES(Media Encoder Standard)에 대한 샘플 미리 설정

**Media Encoder Standard**는 인코딩 작업을 만들 때 사용할 수 있는 미리 설정된 시스템 인코딩 집합을 정의합니다. Media Services로 스트리밍을 위해 비디오를 인코딩하려면 "적응 스트리밍" 사전 설정을 사용하는 것이 좋습니다. 사전 설정을 지정할 경우 Media Encoder Standard는 [비트 전송률 단계를 자동으로 생성](media-services-autogen-bitrate-ladder-with-mes.md)합니다. 

### <a name="creating-custom-presets-from-samples"></a>샘플에서 사용자 지정 미리 설정 만들기
Media Services는 특정 인코딩 필요 및 요구 사항을 충족하기 위해 미리 설정에 포함된 모든 값을 완전히 사용자 지정할 수 있도록 지원합니다. 인코딩 미리 설정을 사용자 지정해야 하는 경우 이 섹션에 제공된 시스템 미리 설정 중 하나를 사용자 지정 구성에 대한 템플릿으로 사용해야 합니다. 미리 설정에 포함된 각 요소의 의미 및 유효한 값에 대한 설명은 [Media Encoder Standard 스키마](media-services-mes-schema.md) 항목을 참조하세요.  
  
> [!NOTE]
>  4k 인코드에 대한 미리 설정을 사용하는 경우 `S3` 예약 단위 형식을 가져와야 합니다. 자세한 내용은 [Encoding 크기를 조정하는 방법](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units)을 참조하세요.  

#### <a name="video-rotation-default-setting-in-presets"></a>미리 설정의 비디오 회전 기본 설정:
Media Encoder Standard로 작업할 때 기본적으로 비디오 회전이 사용됩니다. 비디오를 세로 모드의 모바일 디바이스로 녹화한 경우 이러한 미리 설정에 따라 인코딩 전에 비디오가 가로 모드로 회전됩니다.
 
## <a name="available-presets"></a>사용 가능한 사전 설정: 

 [H264 다중 비트 전송 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)은 AAC 5.1 오디오 및 6000kbps에서 400kbps에 이르는 8 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [H264 다중 비트 전송 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)은 스테레오 AAC 오디오 및 6000kbps에서 400kbps에 이르는 8 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [iOS용 H264 다중 비트 전송 16x9](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)는 스테레오 AAC 오디오 및 8500kbps에서 200kbps에 이르는 8 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [H264 다중 비트 전송 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)은 AAC 5.1 오디오 및 1900kbps에서 400kbps에 이르는 5 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [H264 다중 비트 전송 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)은 스테레오 AAC 오디오 및 1900kbps에서 400kbps에 이르는 5 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [H264 다중 비트 전송 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)은 AAC 5.1 오디오 및 20000kbps에서 1000kbps에 이르는 12 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [H264 다중 비트 전송 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)은 스테레오 AAC 오디오 및 20000kbps에서 1000kbps에 이르는 12 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [iOS용 H264 다중 비트 전송 4x3](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)는 스테레오 AAC 오디오 및 8500kbps에서 200kbps에 이르는 8 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [H264 다중 비트 전송 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)은 AAC 5.1 오디오 및 1600kbps에서 400kbps에 이르는 5 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [H264 다중 비트 전송 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)는 스테레오 AAC 오디오 및 1600kbps에서 400kbps에 이르는 5 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [H264 다중 비트 전송 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)은 AAC 5.1 오디오 및 3400kbps에서 400kbps에 이르는 6 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [H264 다중 비트 전송 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)는 스테레오 AAC 오디오 및 3400kbps에서 400kbps에 이르는 6 GOP 정렬 MP4 파일 집합을 생성합니다.  
  
 [H264 단일 비트 전송 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)은 AAC 5.1 오디오 및 비트 전송률이 6750kbps인 단일 MP4 파일을 생성합니다.  
  
 [H264 단일 비트 전송 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)는 스테레오 AAC 오디오 및 비트 전송률이 6750kbps인 단일 MP4 파일을 생성합니다.  
  
 [H264 단일 비트 전송 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)은 AAC 5.1 오디오 및 비트 전송률이 18000kbps인 단일 MP4 파일을 생성합니다.  
  
 [H264 단일 비트 전송 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)는 스테레오 AAC 오디오 및 비트 전송률이 18000kbps인 단일 MP4 파일을 생성합니다.  
  
 [H264 단일 비트 전송 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)은 AAC 5.1 오디오 및 비트 전송률이 1800kbps인 단일 MP4 파일을 생성합니다.  
  
 [H264 단일 비트 전송 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)는 스테레오 AAC 오디오 및 비트 전송률이 1800kbps인 단일 MP4 파일을 생성합니다.  
  
 [H264 단일 비트 전송 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)은 AAC 5.1 오디오 및 비트 전송률이 2200kbps인 단일 MP4 파일을 생성합니다.  
  
 [H264 단일 비트 전송 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)는 스테레오 AAC 오디오 및 비트 전송률이 2200kbps인 단일 MP4 파일을 생성합니다.  
  
 [H264 단일 비트 전송 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)은 AAC 5.1 오디오 및 비트 전송률이 4500kbps인 단일 MP4 파일을 생성합니다.  
  
 [Android용 H264 단일 비트 전송 720p](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)은 스테레오 AAC 및 비트 전송률이 2000kbps인 단일 MP4 파일을 생성합니다.  
  
 [H264 단일 비트 전송 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)는 스테레오 AAC 오디오 및 비트 전송률이 4500kbps인 단일 MP4 파일을 생성합니다.  
  
 [Android용 H264 단일 비트 전송 고품질 SD](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)는 스테레오 AAC 오디오 및 비트 전송률이 500kbps인 단일 MP4 파일을 생성합니다.  
  
 [Android용 H264 단일 비트 전송 저품질 SD](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)는 스테레오 AAC 오디오 및 비트 전송률이 56kbps인 단일 MP4 파일을 생성합니다.  
  
 Media Services 인코더와 관련된 자세한 내용은 [Azure Media Services를 사용하여 주문형 Encoding](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/)을 참조하세요.
