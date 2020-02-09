---
title: Azure Media Services를 사용 하는 DRM 하위 시스템의 하이브리드 디자인 Microsoft Docs
description: 이 항목에서는 Azure Media Services를 사용 하는 DRM 하위 시스템의 하이브리드 디자인에 대해 설명 합니다.
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
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: d2f4ddfbff791fbfeb2eb006a628c0fdeb4fdce1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975196"
---
# <a name="hybrid-design-of-drm-subsystems"></a>DRM 하위 시스템의 하이브리드 디자인 

이 항목에서는 Azure Media Services를 사용 하는 DRM 하위 시스템의 하이브리드 디자인에 대해 설명 합니다.

## <a name="overview"></a>Áttekintés

Azure Media Services는 다음과 같은 세 가지 DRM 시스템을 지원 합니다.

* PlayReady
* Widevine (모듈식)
* FairPlay

DRM 지원에는 DRM 암호화 (동적 암호화) 및 라이선스 배달이 포함 되며, 모든 3 DRMs를 브라우저 플레이어 SDK로 지 원하는 Azure Media Player 있습니다.

DRM/CENC 하위 시스템 디자인 및 구현에 대 한 자세한 내용은 [다중 drm을 사용 하는 cenc 및 Access Control](media-services-cenc-with-multidrm-access-control.md)문서를 참조 하세요.

3 개의 DRM 시스템에 대 한 완전 한 지원을 제공 하지만 고객이 하이브리드 DRM 하위 시스템을 구축 하는 Azure Media Services 외에도 자체 인프라/하위 시스템의 다양 한 부분을 사용 해야 하는 경우가 있습니다.

다음은 고객이 자주 묻는 질문입니다.

* "나만의 DRM 라이선스 서버를 사용할 수 있습니까?" (이 경우 고객은 포함 된 비즈니스 논리를 사용 하 여 DRM 라이선스 서버 팜에 투자 했습니다.)
* "AMS에서 콘텐츠를 호스팅하지 않고 Azure Media Services에서 DRM 라이선스 배달만 사용할 수 있습니까?"

## <a name="modularity-of-the-ams-drm-platform"></a>AMS DRM 플랫폼의 모듈화

포괄적인 클라우드 비디오 플랫폼의 일부로 Azure Media Services DRM에는 유연성과 모듈화를 염두에 두어야 하는 디자인이 있습니다. 아래 표에서 설명 하는 다음과 같은 다양 한 조합과 함께 Azure Media Services를 사용할 수 있습니다 (표에 사용 된 표기법에 대 한 설명은 다음과 같습니다.). 

|**& 원본 콘텐츠 호스팅**|**콘텐츠 암호화**|**DRM-licenckézbesítés**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|타사|
|AMS|타사|AMS|
|AMS|타사|타사|
|타사|타사|AMS|

### <a name="content-hosting--origin"></a>& 원본 콘텐츠 호스팅

* AMS: 비디오 자산은 AMS에서 호스트 되 고 스트리밍은 AMS 스트리밍 엔드포인트 (반드시 동적 패키징을 제외)을 통해 진행 됩니다.
* 타사: 비디오는 AMS 외부의 타사 스트리밍 플랫폼에서 호스트 되 고 제공 됩니다.

### <a name="content-encryption"></a>콘텐츠 암호화

* AMS: 콘텐츠 암호화는 AMS 동적 암호화에 의해 동적/주문형으로 수행 됩니다.
* 타사: 콘텐츠 암호화는 전처리 워크플로를 사용 하 여 AMS 외부에서 수행 됩니다.

### <a name="drm-license-delivery"></a>DRM-licenckézbesítés

* AMS: AMS 라이선스 배달 서비스에서 DRM 라이선스를 제공 합니다.
* 타사: DRM 라이선스 외부의 타사 DRM 라이선스 서버에서 DRM 라이선스를 배달 합니다.

## <a name="configure-based-on-your-hybrid-scenario"></a>하이브리드 시나리오에 따라 구성

### <a name="content-key"></a>콘텐츠 키

콘텐츠 키 구성을 통해 AMS 동적 암호화와 AMS 라이선스 배달 서비스의 다음 특성을 제어할 수 있습니다.

* 동적 DRM 암호화에 사용 되는 콘텐츠 키입니다.
* 라이선스 배달 서비스에서 제공 하는 DRM 라이선스 콘텐츠: 권한, 콘텐츠 키 및 제한 사항입니다.
* **콘텐츠 키 인증 정책 제한**유형: 열기, IP 또는 토큰 제한.
* **콘텐츠 키 권한 부여 정책 제한**의 **토큰** 형식을 사용 하는 경우 라이선스를 발급 하기 전에 **콘텐츠 키 권한 부여 정책 제한을** 충족 해야 합니다.

### <a name="asset-delivery-policy"></a>자산 배달 정책

자산 배달 정책을 구성 하 여 AMS 스트리밍 엔드포인트의 AMS 동적 패키지 및 동적 암호화에서 사용 하는 다음 특성을 제어할 수 있습니다.

* CENC (PlayReady 및 Widevine)의 대시, PlayReady의 부드러운 스트리밍, Widevto또는 PlayReady의 HLS와 같은 스트리밍 프로토콜 및 DRM 암호화 조합입니다.
* 관련 된 각 DRMs에 대 한 기본/포함 라이선스 배달 Url입니다.
* 대시 MPD 또는 HLS 재생 목록의 라이선스 취득 Url (LA_URLs)에는 각각 Widevine 및 FairPlay에 대 한 쿼리 문자열 (KID)이 포함 되어 있는지 여부가 결정 됩니다.

## <a name="scenarios-and-samples"></a>시나리오 및 샘플

이전 섹션의 설명에 따라 다음과 같은 5 가지 하이브리드 시나리오는 **자산 배달 정책** 구성 조합-각 **콘텐츠 키** 를 사용 합니다 (마지막 열에 언급 된 샘플은 테이블을 따름).

|**& 원본 콘텐츠 호스팅**|**DRM 암호화**|**DRM-licenckézbesítés**|**콘텐츠 키 구성**|**자산 배달 정책 구성**|**Minta**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Igen|Igen|1\. példa|
|AMS|AMS|타사|Igen|Igen|2\. példa|
|AMS|타사|AMS|Igen|Nem|3\. példa|
|AMS|타사|벗어날|Nem|Nem|4\. példa|
|타사|타사|AMS|Igen|Nem|    

샘플에서 PlayReady 보호는 대시 및 부드러운 스트리밍 모두에 대해 작동 합니다. 아래 비디오 Url은 부드러운 스트리밍 Url입니다. 해당 하는 대시 Url을 가져오려면 "(format = mpd-csf)"를 추가 하면 됩니다. [Azure 미디어 테스트 플레이어](https://aka.ms/amtest) 를 사용 하 여 브라우저에서 테스트할 수 있습니다. 이를 통해 사용할 스트리밍 프로토콜을 구성할 수 있습니다. IE11 및 Windows 10의 Microsoft Edge는 EME를 통해 PlayReady를 지원 합니다. 자세한 내용은 [테스트 도구에 대 한 세부](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/)정보를 참조 하세요.

### <a name="sample-1"></a>1\. példa

* 원본 (기본) URL: https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (대시 & 부드러운): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine (대시): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* HLS (FairPlay LA_URL): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>2\. példa

* 원본 (기본) URL: https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (대시 & 부드러운): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>3\. példa

* 원본 URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (대시 & 부드러운): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>4\. példa

* 원본 URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (대시 & 부드러운): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="summary"></a>Összefoglalás

요약 하자면 Azure Media Services, DRM 구성 요소가 유연 하지만이 항목에서 설명 하는 대로 콘텐츠 키 및 자산 배달 정책을 적절히 구성 하 여 하이브리드 시나리오에서 사용할 수 있습니다.

## <a name="next-steps"></a>Következő lépések
Media Services 학습 경로를 봅니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

