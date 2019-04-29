---
title: Azure Media Services 동적 암호화를 사용 하 여 콘텐츠 보호 | Microsoft Docs
description: 이 문서에서는 동적 암호화를 사용 하 여 콘텐츠 보호 개요를 제공 합니다. 또한 스트리밍 프로토콜 및 암호화 종류에 대 한 설명 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 40e7ebcddb5cc215e071f1a34dfa8f3f4ea95141
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103719"
---
# <a name="content-protection-with-dynamic-encryption"></a>동적 암호화를 사용 하 여 콘텐츠 보호

Azure Media Services를 사용하여 컴퓨터를 떠날 때부터 저장, 처리 및 배달에 이르는 과정 내내 미디어를 보호할 수 있습니다. Media Services를 사용하면 Advanced Encryption Standard(AES-128) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 동적 암호화된 라이브 콘텐츠 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 

다음 이미지는 Media Services 콘텐츠 보호 워크플로를 보여 줍니다. 

![콘텐츠 보호](./media/content-protection/content-protection.svg)

&#42; *동적 암호화는 AES-128 "암호화되지 않은 키", CBCS 및 CENC를 지원합니다. 자세한 내용은 [여기](#streaming-protocols-and-encryption-types)에서 지원 매트릭스를 참조하세요.*

이 문서에서는 Media Services를 사용한 콘텐츠 보호를 이해하는 것과 관련된 개념 및 용어를 설명합니다.

## <a name="main-components-of-a-content-protection-system"></a>콘텐츠 보호 시스템의 주요 구성 요소

"content protection" 시스템/응용 프로그램 디자인을 성공적으로 완료하려면 활동 범위를 완전히 이해해야 합니다. 다음 목록은 구현해야 할 세 부분에 대한 개요를 제공합니다. 

1. Azure Media Services 코드
  
   합니다 [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) 샘플에서는 Media Services v3 사용 하 여 다중 DRM 시스템을 구현 하 고 또한 Media Services 라이선스/키 배달 서비스를 사용 하는 방법을 보여 줍니다. 여러 암호화 유형(AES-128, PlayReady, Widevine, FairPlay)을 사용하여 각 자산을 암호화할 수 있습니다. 결합에 적합한 것을 확인하려면 [스트리밍 프로토콜 및 암호화 유형](#streaming-protocols-and-encryption-types)을 참조합니다.
  
   예제에서는 다음을 수행하는 방법을 보여 줍니다.

   1. 만들기 및 구성 [콘텐츠 키 정책](https://docs.microsoft.com/rest/api/media/contentkeypolicies)합니다.

      * JWT의 클레임에 따라 권한 부여 확인 논리를 지정하여 라이선스 전송 권한 부여를 정의합니다.
      * 콘텐츠 키를 지정 하 여 DRM 암호화를 구성 합니다.
      * 구성 [PlayReady](playready-license-template-overview.md)하십시오 [Widevine](widevine-license-template-overview.md), 및/또는 [FairPlay](fairplay-license-overview.md) 라이선스. 이 템플릿을 사용하여 각 사용된 DRM에 대한 권한을 구성할 수 있습니다.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. 만들기는 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators) 하 여 암호화 된 자산을 스트리밍하려면 하도록 구성 됩니다. 
  
      합니다 **스트리밍 로케이터** 연결에 [스트리밍 정책](https://docs.microsoft.com/rest/api/media/streamingpolicies)합니다. 예제에서는 "Predefined_MultiDrmCencStreaming" 정책에 StreamingLocator.StreamingPolicyName 설정합니다. 이 정책에서는 원하는 두 가지 콘텐츠 키 (봉투 (envelope) 및 CENC)을 통해 생성 된 가져오기 및 설정에 대 한 로케이터에 나타냅니다. 따라서 봉투, PlayReady 및 Widevine 암호화가 적용됩니다(키가 구성된 DRM 라이선스에 따라 재생 클라이언트로 배달됨). 또한 CBCS(FairPlay)를 사용하여 스트림을 암호화하려면 "Predefined_MultiDrmStreaming"을 사용합니다.
    
      비디오를 암호화 하려고 하므로 합니다 **콘텐츠 키 정책** 는 이전에 구성한도 연결 하는 경우를 **스트리밍 로케이터**합니다. 
    
   3. 테스트 토큰을 만듭니다.

      **GetTokenAsync** 메서드에서 테스트 토큰을 만드는 방법을 보여 줍니다.
   4. 스트리밍 URL을 작성합니다.

      **GetDASHStreamingUrlAsync** 메서드에서 스트리밍 URL을 작성하는 방법을 보여 줍니다. 이 경우 URL은 **DASH** 콘텐츠를 스트림합니다.

2. AES 또는 DRM 클라이언트를 사용한 플레이어. 플레이어 SDK(기본 또는 브라우저 기반)에 따라 비디오 플레이어 앱은 다음 요구 사항을 충족해야 합니다.
   * 플레이어 SDK는 필요한 DRM 클라이언트 지원
   * 플레이어 SDK는 필요한 스트리밍 프로토콜 즉, Smooth, DASH 및/또는 HLS를 지원합니다.
   * 플레이어 SDK는 라이선스 취득 요청에서 JWT 토큰 전달을 처리할 수 있어야 함
  
     [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)를 사용하여 플레이어를 만들 수 있습니다. [Azure Media Player의 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/)를 통해 다양한 DRM 플랫폼에 사용할 DRM 기술을 지정할 수 있습니다.

     AES 또는 CENC(Widevine 및/또는 PlayReady) 암호화된 콘텐츠를 테스트하려면 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html)를 사용할 수 있습니다. “고급 옵션”을 클릭하고 암호화 옵션을 확인합니다.

     FairPlay 암호화된 콘텐츠를 테스트하려는 경우 [이 테스트 플레이어](https://aka.ms/amtest)를 사용합니다. 플레이어는 Widevine, PlayReady, FairPlay DRMs 및 AES-128 암호화되지 않은 키 암호화를 지원합니다. 
    
     다양한 DRM을 테스트하기 위해 적절한 브라우저 즉, Widevine의 경우 Chrome/Opera/Firefox, PlayReady의 경우 Microsoft Edge/IE11, FairPlay의 경우 macOS에서 Safari를 선택해야 합니다.

3. 백 엔드 리소스 액세스에 대한 액세스 토큰으로 JWT(JSON Web Token)를 발급하는 STS(보안 토큰 서비스). AMS 라이선스 배달 서비스를 백 엔드 리소스로 사용할 수 있습니다. STS는 다음을 정의해야 합니다.

   * 발급자 및 대상(또는 범위)
   * 콘텐츠 보호의 비즈니스 요구 사항에 종속되는 클레임
   * 서명 확인에 대한 대칭 또는 비대칭 확인
   * 키 롤오버 지원(필요한 경우)

     [이 STS 도구](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt)를 사용하여 대칭, 비대칭 또는 키 롤오버가 있는 Azure AD라는 세 가지 유형의 확인 키를 모두 지원하는 STS를 테스트할 수 있습니다. 

> [!NOTE]
> 다음 부분으로 이동하기 전에 각 부분을 완벽히 테스트하고 집중하는 것을 매우 권장합니다. "content protection" 시스템을 테스트하려면 위 목록에서 지정된 도구를 사용합니다.  

## <a name="streaming-protocols-and-encryption-types"></a>스트리밍 프로토콜 및 암호화 유형

Media Services를 사용하여 AES 암호화되지 않은 키 또는 PlayReady, Widevine 또는 FairPlay 등과 같은 DRM 암호화로 동적 암호화된 콘텐츠를 제공할 수 있습니다. 현재, HLS(HTTP 라이브 스트리밍) MPEG DASH 및 부드러운 스트리밍 형식을 암호화할 수 있습니다. 각 프로토콜은 다음 암호화 방법을 지원합니다.

### <a name="hls"></a>HLS

HLS 프로토콜 다음 컨테이너 형식 및 암호화 체계를 지원합니다.

|컨테이너 형식|암호화 체계|URL 예제|
|---|---|---|
|모두|AES|`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) ||
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC(PlayReady) ||
|CMAF(fmp4) |CENC(PlayReady) ||

HLS/CMAF + FairPlay (HEVC를 포함 하 여 / 아닌 H.265) 다음 장치에서 지원 됩니다.

* iOS v11 이상 
* iPhone 8 이상
* MacOS high Sierra intel 7 Gen CPU

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG DASH 프로토콜 다음 컨테이너 형식 및 암호화 체계를 지원합니다.

|컨테이너 형식|암호화 체계|URL 예
|---|---|---|
|모두|AES|`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC(Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC(Widevine + PlayReady)||

### <a name="smooth-streaming"></a>부드러운 스트리밍

부드러운 스트리밍 프로토콜 다음 컨테이너 형식 및 암호화 체계를 지원합니다.

|프로토콜|컨테이너 형식|암호화 체계|
|---|---|---|
|fMP4|AES||
|fMP4 | CENC(PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>브라우저

일반적인 브라우저는 다음과 같은 DRM 클라이언트를 지원합니다.

|브라우저|암호화|
|---|---|
|Chrome|Widevine|
|Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 암호화되지 않은 키 및 DRM

고객들은 종종 AES 암호화 또는 DRM 시스템을 사용해야 할지 여부를 궁금해 합니다. 두 시스템 간의 주요 차이점은는 AES 암호화를 사용 하 여 콘텐츠 키가 클라이언트로 전송 TLS를 통한 전송에서 하지만 모든 추가에서 암호화 하지 않고 ("지우기")은 키가 암호화 되도록 합니다. 결과적으로, 콘텐츠의 암호를 해독 하는 데 사용 되는 키에 액세스할 수는 클라이언트 플레이어가 있으며 일반 텍스트로 클라이언트에 대 한 네트워크 추적에서 볼 수 있습니다. AES-128 암호화되지 않은 키 암호화는 뷰어가 신뢰할 만한 당사자(예: 직원이 볼 수 있도록 회사 내에 배포되는 회사 비디오 암호화)인 사용 사례에 적합합니다.

PlayReady, Widevine 및 FairPlay 모두 AES-128 암호화 되지 않은 키와 비교할 콘텐츠의 암호를 해독 하는 데 사용 된 키에 추가 수준의 암호화를 제공 같은 하는 DRM 시스템입니다. 콘텐츠 키의 DRM 런타임에서 보호 되는 키 암호화는 TLS로 제공 하는 모든 전송 수준 암호화를 추가 합니다. 또한 암호 해독은 악의적인 사용자가 공격하기에 좀 더 어려운 운영 체제 수준의 보안 환경에서 처리됩니다. DRM은 뷰어가 신뢰할 만한 당사자가 아니고 가장 높은 수준의 보안이 필요한 사용 사례에 권장됩니다.

## <a name="dynamic-encryption-and-key-delivery-service"></a>동적 암호화 및 키 배달 서비스

콘텐츠 키를 Media Services v3에서는 스트리밍 로케이터를 사용 하 여 연결 됩니다 (참조 [이 예제에서는](protect-with-aes128.md)). Media Services 키 배달 서비스를 사용 하는 경우 Azure Media Services를 콘텐츠 키를 생성할 수 있습니다. 자체 키 배달 서비스를 사용 중인 경우 또는 두 데이터 센터에 동일한 콘텐츠 키가 있어야 하는 고가용성 시나리오를 처리해야 하는 경우 콘텐츠 키를 직접 생성해야 합니다.

플레이어가 스트림을 요청하면 Media Services는 지정된 키를 사용하고 AES 암호화되지 않은 키 또는 DRM 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 Media Services 키 배달 서비스 또는 지정한 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.

Microsoft Azure Media Services는 DRM(PlayReady, Widevine, FairPlay) 라이선스 및 AES 키를 인증된 클라이언트에 키 배달 서비스를 제공합니다. REST API 또는 Media Services 클라이언트 라이브러리를 사용하여 라이선스 및 키에 대한 권한 부여 및 인증 정책을 구성할 수 있습니다.

### <a name="custom-key-and-license-acquisition-url"></a>사용자 지정 키 및 라이선스 획득 URL

다른 키 및 라이선스 배달 서비스 (미디어 서비스가 아님)를 지정 하려는 경우 다음 템플릿을 사용 합니다. 템플릿 대체할 수 있는 두 필드는 스트리밍 정책 자산별 스트리밍 정책을 새로 만드는 대신 상당수의 자산이 간에 공유할 수 있도록입니다. 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate-최종 사용자 플레이어에 키를 제공 하는 사용자 지정 서비스의 URL에 대 한 템플릿. Azure Media Services 키를 발급 하는 데 사용 하는 경우 필요 하지 않습니다. 템플릿을은 대체할 수 있는 토큰 요청에 특정 값을 사용 하 여 런타임에 업데이트 하는 서비스를 지원 합니다.  현재 지원 되는 토큰 값은 {AlternativeMediaId}가 바뀌는 StreamingLocatorId.AlternativeMediaId, 및 {ContentKeyId}의 값을 사용 하 여 요청 된 키 식별자의 값으로 대체 됩니다입니다.
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate-최종 사용자 플레이어에 라이선스를 배달 하는 사용자 지정 서비스의 URL에 대 한 템플릿. Azure Media Services 라이선스를 발급 하는 데 사용 하는 경우 필요 하지 않습니다. 템플릿을은 대체할 수 있는 토큰 요청에 특정 값을 사용 하 여 런타임에 업데이트 하는 서비스를 지원 합니다. 현재 지원 되는 토큰 값은 {AlternativeMediaId}가 바뀌는 StreamingLocatorId.AlternativeMediaId, 및 {ContentKeyId}의 값을 사용 하 여 요청 된 키 식별자의 값으로 대체 됩니다입니다. 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate-위와 동일 하며 Widevine에 대해서만 합니다. 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate-위와 동일 하며 FairPlay에 대 한 합니다.  

예를 들면 다음과 같습니다.

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

합니다 `ContentKeyId` 요청 된 키의 값 및 `AlternativeMediaId` 요청 쪽에서 엔터티를 매핑할 경우 사용할 수 있습니다. 예를 들어를 `AlternativeMediaId` 권한을 조회 하는 데 사용할 수 있습니다.

사용자 지정을 사용 하는 REST 예제 키 목록의 라이선스 획득 Url에 대 한 참조 [스트리밍 정책-만들기](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="control-content-access"></a>콘텐츠 액세스 제어

콘텐츠 키 정책을 구성하여 콘텐츠에 액세스하는 사람을 제어할 수 있습니다. Media Services는 키를 요청 하는 사용자에 권한을 부여하는 여러 방법을 지원합니다. 콘텐츠 키 정책을 구성해야 합니다. 키가 클라이언트에 배달되려면 먼저 클라이언트(플레이어)에서 정책을 충족해야 합니다. 콘텐츠 키 정책에는 **개방** 또는 **토큰** 제한이 있을 수 있습니다. 

토큰 제한 콘텐츠 키 정책을 사용하여 콘텐츠 키를 키/라이선스 요청에서 유효한 JWT(JSON Web Token) 또는 SWT(단순 웹 토큰)를 표시하는 클라이언트에만 보낼 수 있습니다. 이 토큰은 STS(보안 토큰 서비스)에 의해 발급되어야 합니다. Azure Active Directory를 STS로 사용하거나 사용자 지정 STS를 배포할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효하고 해당 토큰의 클레임이 키/라이선스에 대해 구성된 클레임과 일치하는 경우 Media Services 키 배달 서비스는 요청된 키/라이선스를 클라이언트에 반환합니다.

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급하는 보안 토큰 서비스입니다. 때로 범위라고도 하는 대상은 토큰의 의도 또는 토큰에서 접근을 인증하는 대상 리소스를 설명합니다. Media Services 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.

다양 한 DRM 라이선스 매개 변수 (차량 대 여 라이선스 및 구독 라이선스)를 사용 하 여 다른 ContentKeyPolicyOptions 중에서 선택 하거나 콘텐츠 키를 나타내는 클레임을 포함 하도록 토큰에 사용자 지정 클레임을 포함 하도록 고객에 게 자주 사용 하 여 사용자 지정 STS 토큰에서 액세스 부여 하는 키의 식별자입니다.
 
## <a name="storage-side-encryption"></a>저장소 쪽 암호화

미사용 자산을 보호하려면 저장소 쪽 암호화를 사용하여 자산을 암호화해야 합니다. 다음 표는 Media Services v3에서 저장소 쪽 암호화가 작동하는 원리를 보여줍니다.

|암호화 옵션|설명|Media Services v3|
|---|---|---|
|Media Services 저장소 암호화| AES-256 암호화, Media Services에서 키 관리|지원되지 않음<sup>(1)</sup>|
|[미사용 데이터에 대한 Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage가 제공하는 서버 쪽 암호화, Azure 또는 고객이 키 관리|지원됨|
|[저장소 클라이언트 쪽 암호화](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage가 제공하는 클라이언트 쪽 암호화, Key Vault의 고객이 키 관리|지원되지 않음|

<sup>1</sup> Media Services v3에서 저장소 암호화(AES-256 암호화)는 자산을 Media Services v2를 사용하여 만들었을 경우 이전 버전과의 호환성에 대해서만 지원됩니다. v3는 기존 저장소 암호화된 자산과 함께 작동하지만 새로 만들기를 허용하지는 않습니다.

## <a name="troubleshoot"></a>문제 해결

표시 되 면는 `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` 오류를 적절 한 스트리밍 정책을 지정 해야 합니다.

로 끝나는 오류가 발생할 경우 `_NOT_SPECIFIED_IN_URL`를 확인 하는 암호화 형식을 URL에 지정 해야 합니다. 예: `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)` 참조 [스트리밍 프로토콜 및 암호화 유형을](#streaming-protocols-and-encryption-types)합니다.

## <a name="provide-feedback"></a>피드백 제공

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [AES 암호화로 보호](protect-with-aes128.md)
* [DRM으로 보호](protect-with-drm.md)
* [Access control 사용한 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md)
* [질문과 대답](frequently-asked-questions.md)

