---
title: Azure Media Services를 사용하는 DRM 하위 시스템의 하이브리드 디자인 | Microsoft Docs
description: 이 항목에서는 Azure Media Services를 사용하는 DRM 하위 시스템의 하이브리드 디자인에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan;juliako
ms.openlocfilehash: 5c86a49cd9dc26f724de12ed2e5e77e645e4ab53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466598"
---
# <a name="hybrid-design-of-drm-subsystems"></a>DRM 하위 시스템의 하이브리드 디자인 

이 항목에서는 Azure Media Services를 사용하는 DRM 하위 시스템의 하이브리드 디자인에 대해 설명합니다.

## <a name="overview"></a>개요

Azure Media Services는 다음 세 가지 DRM 시스템을 지원합니다.

* PlayReady
* Widevine(모듈)
* FairPlay

DRM 지원에는 3가지 DRM을 모두 브라우저 플레이어 SDK로 지원하는 Azure Media Player와 함께 DRM 암호화(동적 암호화) 및 라이선스 배달이 포함됩니다.

DRM/CENC 하위 시스템 설계 및 구현에 대한 자세한 내용은 [다중 DRM 및 Access Control 기능을 갖춘 CENC](media-services-cenc-with-multidrm-access-control.md) 문서를 참조하세요.

3가지 DRM 시스템을 완벽하게 지원하지만 때로는 고객이 Azure Media Services 외에도 사용자 고유 인프라/하위 시스템의 다양한 구성 요소를 사용하여 하이브리드 DRM 하위 시스템을 구축해야 합니다.

고객이 묻는 몇 가지 일반적인 질문은 다음과 같습니다.

* "나만의 DRM 라이선스 서버를 사용할 수 있습니까?" (이 경우 고객은 비즈니스 논리가 포함된 DRM 라이선스 서버 팜에 투자했습니다.)
* "AMS(Azure Media Services) 콘텐츠를 호스팅하지 않고 AMS에서 DRM 라이선스 배달만 사용할 수 있습니까?"

## <a name="modularity-of-the-ams-drm-platform"></a>AMS DRM 플랫폼의 모듈화

포괄적인 클라우드 비디오 플랫폼의 일부인 Azure Media Services DRM은 유연성과 모듈성을 고려하여 설계되었습니다. 아래 표에서 설명하는 다양한 조합 중 하나로 Azure Media Services를 사용할 수 있습니다(표에 사용된 표기 항목에 대한 설명이 뒤에 나옴). 

|**콘텐츠 호스팅 및 원본**|**콘텐츠 암호화**|**DRM 라이선스 배달**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|타사|
|AMS|타사|AMS|
|AMS|타사|타사|
|타사|타사|AMS|

### <a name="content-hosting--origin"></a>콘텐츠 호스팅 및 원본

* AMS: AMS에서 비디오 자산을 호스팅하고, AMS 스트리밍 엔드포인트를 통해 스트리밍합니다(반드시 동적 패키징일 필요는 없음).
* 타사: AMS 외부의 타사 스트리밍 플랫폼에서 비디오를 호스팅하고 배달합니다.

### <a name="content-encryption"></a>콘텐츠 암호화

* AMS: AMS 동적 암호화를 통해 동적으로/요청 시 콘텐츠 암호화를 수행합니다.
* 타사: AMS 외부에서 사전 처리 워크플로를 통해 콘텐츠 암호화를 수행합니다.

### <a name="drm-license-delivery"></a>DRM 라이선스 배달

* AMS: AMS 라이선스 배달 서비스를 통해 DRM 라이선스를 배달합니다.
* 타사: AMS 외부의 타사 DRM 라이선스 서버에서 DRM 라이선스를 배달합니다.

## <a name="configure-based-on-your-hybrid-scenario"></a>하이브리드 시나리오에 따른 구성

### <a name="content-key"></a>콘텐츠 키

콘텐츠 키 구성을 통해 AMS 동적 암호화 및 AMS 라이선스 배달 서비스의 다음 특성을 모두 제어할 수 있습니다.

* 동적 DRM 암호화에 사용되는 콘텐츠 키
* 라이선스 배달 서비스에서 배달하는 DRM 라이선스 콘텐츠: 권한, 콘텐츠 키 및 제한.
* **콘텐츠 키 인증 정책 제한** 유형: 공개, IP 또는 토큰 제한
* **토큰** 형식의 **콘텐츠 키 인증 정책 제한이 사용**된 경우 라이선스를 발급하기 전에 **콘텐츠 키 인증 정책 제한**이 충족되어야 합니다.

### <a name="asset-delivery-policy"></a>자산 배달 정책

자산 배달 정책 구성을 통해 AMS 동적 포장기에서 사용하는 다음 특성과 AMS 스트리밍 엔드포인트의 동적 암호화를 제어할 수 있습니다.

* CENC(PlayReady 및 Widevine)에서 DASH, PlayReady에서 부드러운 스트리밍, Widevine 또는 PlayReady에서 HLS와 같은 스트리밍 프로토콜 및 DRM 암호화의 조합
* 포함된 DRM 각각에 대한 기본/포함된 라이선스 배달 URL
* DASH MPD 또는 HLS 재생 목록의 라이선스 획득 URL(LA_URL)에서 Widevine 및 FairPlay 각각에 대한 키 ID(KID) 쿼리 문자열을 포함하는지 여부

## <a name="scenarios-and-samples"></a>시나리오 및 샘플

이전 섹션의 설명에 따라 다음 5가지 하이브리드 시나리오에서는 각각의 **콘텐츠 키**-**자산 배정 정책** 구성 조합을 사용합니다(마지막 열에 언급된 샘플은 표에 나와 있음).

|**콘텐츠 호스팅 및 원본**|**DRM 암호화**|**DRM 라이선스 배달**|**콘텐츠 키 구성**|**자산 배달 정책 구성**|**샘플**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|예|예|샘플 1|
|AMS|AMS|타사|예|예|샘플 2|
|AMS|타사|AMS|예|아닙니다.|샘플 3|
|AMS|타사|외부|아닙니다.|아닙니다.|샘플 4|
|타사|타사|AMS|예|아닙니다.|    

샘플에서 PlayReady 보호는 DASH 및 부드러운 스트리밍 모두에서 작동합니다. 아래의 비디오 URL은 부드러운 스트리밍 URL입니다. 해당 DASH URL을 얻으려면 "(format=mpd-time-csf)"를 추가합니다. [azure media test player](https://aka.ms/amtest)를 사용하여 브라우저에서 테스트할 수 있습니다. 그러면 어떤 기술에서 사용할 스트리밍 프로토콜을 구성할 수 있습니다. Windows 10의 IE11 및 Microsoft Edge는 EME를 통해 PlayReady를 지원합니다. 자세한 내용은 [테스트 도구에 대한 세부 정보(영문)](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/)를 참조하세요.

### <a name="sample-1"></a>샘플 1

* 원본(기본) URL: https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL(DASH 및 부드러운 스트리밍): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL(DASH): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL(HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>샘플 2

* 원본(기본) URL: https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL(DASH 및 부드러운 스트리밍): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>샘플 3

* 원본 URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL(DASH 및 부드러운 스트리밍): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>샘플 4

* 원본 URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL(DASH 및 부드러운 스트리밍): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>요약

요약하면 Azure Media Services DRM 구성 요소는 유연합니다. 이 항목에서 설명한 대로 콘텐츠 키와 자산 배달 정책을 올바르게 구성하여 하이브리드 시나리오에서 이러한 구성 요소를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로 보기.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

