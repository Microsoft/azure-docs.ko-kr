---
title: Media Services v3 동적 암호화를 사용 하 여 콘텐츠 보호
titleSuffix: Azure Media Services
description: Azure Media Services에서 동적 암호화, 스트리밍 프로토콜 및 암호화 유형을 사용 하 여 콘텐츠를 보호 하는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 81ec96d244c36c0328134b415465d632ef6c1dd5
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267924"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Media Services 동적 암호화를 사용 하 여 콘텐츠 보호

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services를 사용하여 컴퓨터를 떠날 때부터 스토리지, 처리 및 배달에 이르는 과정 내내 미디어를 보호할 수 있습니다. Media Services를 사용하면 Advanced Encryption Standard(AES-128) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 동적 암호화된 라이브 콘텐츠 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 콘텐츠가 AES 암호화 되지 않은 키로 암호화 되 고 HTTPS를 통해 전송 되는 경우 클라이언트에 도달할 때까지 명확 하지 않습니다. 

Media Services v 3에서 콘텐츠 키가 스트리밍 로케이터와 연결 되어 있습니다 ( [이 예제](protect-with-aes128.md)참조). Media Services 키 배달 서비스를 사용 하는 경우 콘텐츠 키를 Azure Media Services 생성 하도록 할 수 있습니다. 고유한 키 배달 서비스를 사용 하는 경우 또는 두 데이터 센터에서 동일한 콘텐츠 키를 가져야 하는 고가용성 시나리오를 처리 해야 하는 경우 콘텐츠 키를 직접 생성 해야 합니다.

플레이어가 스트림을 요청하면 Media Services는 지정된 키를 사용하고 AES 암호화되지 않은 키 또는 DRM 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 Media Services 키 배달 서비스 또는 지정한 키 배달 서비스에서 키를 요청합니다. 사용자에 게 키를 가져올 수 있는 권한이 있는지 여부를 결정 하기 위해 서비스는 키에 대해 지정한 콘텐츠 키 정책을 평가 합니다.

REST API 또는 Media Services 클라이언트 라이브러리를 사용하여 라이선스 및 키에 대한 권한 부여 및 인증 정책을 구성할 수 있습니다.

다음 이미지는 Media Services 콘텐츠 보호에 대 한 워크플로를 보여 줍니다.

![Media Services 콘텐츠 보호를 위한 워크플로](./media/content-protection/content-protection.svg)
  
&#42; *동적 암호화는 AES-128 clear key, CBCS 및 CBCS를 지원 합니다. 자세한 내용은 [지원 매트릭스](#streaming-protocols-and-encryption-types)를 참조 하세요.*

이 문서에서는 Media Services의 콘텐츠 보호를 이해 하는 데 도움이 되는 개념과 용어에 대해 설명 합니다.

## <a name="main-components-of-a-content-protection-system"></a>콘텐츠 보호 시스템의 주요 구성 요소

콘텐츠 보호 시스템을 성공적으로 완료 하려면 활동의 범위를 완전히 이해 해야 합니다. 다음 섹션에서는 구현 해야 하는 세 부분에 대 한 개요를 제공 합니다.

> [!NOTE]
> 다음 부분으로 이동 하기 전에 다음 섹션에서 각 부분에 중점을 두고 전체 테스트를 수행 하는 것이 좋습니다. 콘텐츠 보호 시스템을 테스트 하려면 섹션에 지정 된 도구를 사용 합니다.

### <a name="media-services-code"></a>Media Services 코드
  
[DRM 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) 에서는 .net을 사용 하 여 Media Services v3로 다중 DRM 시스템을 구현 하는 방법을 보여 줍니다. 또한 Media Services 라이선스/키 배달 서비스를 사용 하는 방법을 보여 줍니다.
  
여러 암호화 유형(AES-128, PlayReady, Widevine, FairPlay)을 사용하여 각 자산을 암호화할 수 있습니다. 결합에 대 한 의미를 확인 하려면 [스트리밍 프로토콜 및 암호화 유형](#streaming-protocols-and-encryption-types)을 참조 하세요.

예제에서는 다음을 수행하는 방법을 보여 줍니다.

1. [콘텐츠 키 정책을](content-key-policy-concept.md)만들고 구성 합니다.

   콘텐츠 키 정책을 만들어 자산에 대 한 보안 액세스를 제공 하는 콘텐츠 키를 최종 클라이언트에 전달 하는 방법을 구성 합니다.  

   * 라이선스 배달 권한 부여를 정의 합니다. JWT (JSON Web Token)의 클레임을 기반으로 하는 권한 부여 확인 논리를 지정 합니다.
   * [PlayReady](playready-license-template-overview.md), [widevine](widevine-license-template-overview.md)및/또는 라이선스를 [FairPlay](fairplay-license-overview.md) 구성 합니다. 템플릿을 사용 하면 각 DRMs에 대 한 권한 및 사용 권한을 구성할 수 있습니다.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. 암호화 된 자산을 스트리밍하려면 구성 된 [스트리밍 로케이터](streaming-locators-concept.md) 를 만듭니다.
  
   스트리밍 로케이터는 [스트리밍 정책과](streaming-policy-concept.md)연결 해야 합니다. 이 예에서는 `StreamingLocator.StreamingPolicyName` 를 "Predefined_MultiDrmCencStreaming" 정책으로 설정 합니다.

   PlayReady 및 Widevine 암호화가 적용 되 고, 구성 된 DRM 라이선스에 따라 키가 재생 클라이언트에 전달 됩니다. 또한 CBCS (FairPlay)를 사용 하 여 스트림을 암호화 하려는 경우 "Predefined_MultiDrmStreaming" 정책을 사용 합니다.

   또한 스트리밍 로케이터는 정의한 콘텐츠 키 정책과 연결 됩니다.

3. 테스트 토큰을 만듭니다.

   `GetTokenAsync`메서드는 테스트 토큰을 만드는 방법을 보여 줍니다.
4. 스트리밍 URL을 작성합니다.

   `GetDASHStreamingUrlAsync`메서드는 스트리밍 URL을 작성 하는 방법을 보여 줍니다. 이 경우 URL은 DASH 콘텐츠를 스트림합니다.

### <a name="player-with-an-aes-or-drm-client"></a>AES 또는 DRM 클라이언트를 사용 하는 플레이어

플레이어 SDK(기본 또는 브라우저 기반)에 따라 비디오 플레이어 앱은 다음 요구 사항을 충족해야 합니다.

* 플레이어 SDK는 필요한 DRM 클라이언트를 지원 합니다.
* 플레이어 SDK는 필요한 스트리밍 프로토콜인 부드러운, 대시 및/또는 HTTP 라이브 스트리밍 (HLS)를 지원 합니다.
* 플레이어 SDK는 라이선스 획득 요청에 JWT 토큰을 전달 하는 것을 처리할 수 있습니다.

[Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)를 사용하여 플레이어를 만들 수 있습니다. [Azure Media Player의 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/)를 통해 다양한 DRM 플랫폼에 사용할 DRM 기술을 지정할 수 있습니다.

AES 또는 CENC(Widevine 및/또는 PlayReady) 암호화된 콘텐츠를 테스트하려면 [Azure Media Player](https://aka.ms/azuremediaplayer)를 사용할 수 있습니다. **고급 옵션** 을 선택 하 고 암호화 옵션을 선택 했는지 확인 합니다.

FairPlay 암호화된 콘텐츠를 테스트하려는 경우 [이 테스트 플레이어](https://aka.ms/amtest)를 사용합니다. 플레이어는 AES-128 암호화 되지 않은 키 암호화와 함께 Widevg, PlayReady 및 FairPlay DRMs을 지원 합니다.

올바른 브라우저를 선택 하 여 다른 DRMs을 테스트 합니다.

* Widevine에 대 한 Chrome, Opera 또는 Firefox.
* PlayReady 용 Microsoft Edge 또는 Internet Explorer 11
* FairPlay 용 macOS의 Safari.

### <a name="security-token-service"></a>보안 토큰 서비스

STS (보안 토큰 서비스)는 백 엔드 리소스 액세스를 위한 액세스 토큰으로 JWT를 발급 합니다. Azure Media Services 라이선스/키 배달 서비스를 백 엔드 리소스로 사용할 수 있습니다. STS는 다음 항목을 정의 해야 합니다.

* 발급자 및 대상 (또는 범위).
* 클레임-콘텐츠 보호의 비즈니스 요구 사항에 따라 달라 집니다.
* 서명 확인을 위한 대칭 또는 비대칭 확인.
* 키 롤오버 지원 (필요한 경우)

[이 sts 도구](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) 를 사용 하 여 sts를 테스트할 수 있습니다. 키 롤오버가 있는 대칭, 비대칭 또는 Azure Active Directory (Azure AD)의 세 가지 유형의 확인 키를 모두 지원 합니다.

## <a name="streaming-protocols-and-encryption-types"></a>스트리밍 프로토콜 및 암호화 유형

Media Services를 사용하여 AES 암호화되지 않은 키 또는 PlayReady, Widevine 또는 FairPlay 등과 같은 DRM 암호화로 동적 암호화된 콘텐츠를 제공할 수 있습니다. 현재 HLS, MPEG 대시 및 부드러운 스트리밍 형식을 암호화할 수 있습니다. 각 프로토콜은 다음과 같은 암호화 방법을 지원 합니다.

### <a name="hls"></a>HLS

HLS 프로토콜은 다음과 같은 컨테이너 형식 및 암호화 스키마를 지원 합니다.

|컨테이너 형식|암호화 체계|URL 예|
|---|---|---|
|모두|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC(PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC(PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (HEVC/265 포함)는 다음 장치에서 지원 됩니다.

* iOS 11 이상
* iPhone 8 이상
* MacOS는 Intel 7 세대 CPU를 포함 하 고 있습니다.

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG 대시 프로토콜은 다음과 같은 컨테이너 형식 및 암호화 스키마를 지원 합니다.

|컨테이너 형식|암호화 체계|URL 예
|---|---|---|
|모두|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC(Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC(Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>부드러운 스트리밍

부드러운 스트리밍 프로토콜은 다음과 같은 컨테이너 형식 및 암호화 스키마를 지원 합니다.

|프로토콜|컨테이너 형식|암호화 체계|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC(PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>브라우저

일반 브라우저는 다음 DRM 클라이언트를 지원 합니다.

|브라우저|암호화|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>콘텐츠 액세스 제어

콘텐츠 키 정책을 구성하여 콘텐츠에 액세스하는 사람을 제어할 수 있습니다. Media Services는 키를 요청 하는 사용자에 권한을 부여하는 여러 방법을 지원합니다. 키가 클라이언트에 배달되려면 먼저 클라이언트(플레이어)에서 정책을 충족해야 합니다. 콘텐츠 키 정책에는 *개방* 또는 *토큰* 제한이 있을 수 있습니다.

권한 부여 없이 모든 사용자에 게 라이선스를 발급 하려는 경우 공개 제한 된 콘텐츠 키 정책을 사용할 수 있습니다. 예를 들어 수익이 ad 기반 이며 구독 기반이 아닌 경우입니다.  

토큰 제한 콘텐츠 키 정책을 사용 하는 경우 콘텐츠 키는 라이선스/키 요청에서 유효한 JWT 토큰 또는 SWT (단순 웹 토큰)를 제공 하는 클라이언트에만 전송 됩니다. 이 토큰은 STS에서 발급 되어야 합니다.

Azure AD를 STS로 사용 하거나 [사용자 지정 sts](#using-a-custom-sts)를 배포할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. Media Services 라이선스/키 배달 서비스는 다음 조건에 모두 해당 하는 경우 요청 된 라이선스 또는 키를 클라이언트에 반환 합니다.

* 토큰이 유효 합니다.
* 토큰의 클레임은 라이선스 또는 키에 대해 구성 된 클레임과 일치 합니다.

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급하는 STS입니다. 범위 라고도 하는 대상 그룹은 토큰이 액세스 권한을 부여 하는 토큰 또는 리소스의 의도를 설명 합니다. Media Services 라이선스/키 배달 서비스는 토큰의 이러한 값이 템플릿의 값과 일치 하는지 확인 합니다.

### <a name="token-replay-prevention"></a>토큰 재생 방지

Media Services 고객은 *토큰 재생 방지* 기능을 통해 동일한 토큰을 사용하여 키 또는 라이선스를 요청하는 횟수에 대한 제한을 설정할 수 있습니다. 고객은 토큰에 형식의 클레임을 추가할 수 있습니다 `urn:microsoft:azure:mediaservices:maxuses` . 여기서 값은 토큰이 라이선스 또는 키를 획득 하는 데 사용 될 수 있는 횟수입니다. 키 배달이 동일한 토큰을 사용 하는 모든 후속 요청은 권한 없는 응답을 반환 합니다. [DRM 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)에서 클레임을 추가 하는 방법을 참조 하세요.
 
#### <a name="considerations"></a>고려 사항

* 고객은 토큰 생성을 제어 해야 합니다. 클레임은 토큰 자체에 배치 해야 합니다.
* 이 기능을 사용 하는 경우 만료 시간이 요청을 수신 하는 시간부터 1 시간을 초과 하는 토큰이 있는 요청은 거부 된 응답으로 거부 됩니다.
* 토큰은 해당 시그니처로 고유 하 게 식별 됩니다. 페이로드를 변경 하는 경우 (예: 만료 시간 또는 클레임에 대 한 업데이트) 토큰의 서명을 변경 하 고 키 배달이 이전에 발생 하지 않는 새 토큰으로 계산 됩니다.
* 토큰이 고객에 의해 설정 된 값을 초과 하면 재생이 실패 `maxuses` 합니다.
* 이 기능은 기존의 모든 보호 된 콘텐츠에 사용할 수 있습니다 (발급 된 토큰만 변경 해야 함).
* 이 기능은 JWT 및 SWT 모두에서 작동 합니다.

## <a name="using-a-custom-sts"></a>사용자 지정 STS 사용

고객이 사용자 지정 STS를 사용 하 여 토큰을 제공 하도록 선택할 수 있습니다. 이유는 다음과 같습니다.

* 고객이 사용 하는 IDP (id 공급자)가 STS를 지원 하지 않습니다. 이 경우 사용자 지정 STS가 옵션일 수 있습니다.
* 고객은 STS를 고객의 구독자 청구 시스템과 통합하는 데 보다 유연하고 긴밀한 제어가 필요할 수 있습니다.

   예를 들어, [고 대 수의 서비스 운영자](https://en.wikipedia.org/wiki/Over-the-top_media_services) 는 프리미엄, 기본 및 스포츠와 같은 여러 구독자 패키지를 제공할 수 있습니다. 이 작업자는 토큰의 클레임을 구독자의 패키지와 일치시켜 특정 패키지의 콘텐츠만 제공되도록 할 수 있습니다. 이 경우 사용자 지정 STS가 필요한 유연성 및 제어를 제공합니다.

* 토큰에 사용자 지정 클레임을 포함 하 여 서로 다른 DRM 라이선스 매개 변수 (구독 라이선스와 임대 라이선스)를 사용 하는 서로 다른 ContentKeyPolicyOptions 사이에서 선택할 수 있습니다.
* 토큰에서 액세스 권한을 부여 하는 키의 콘텐츠 키 식별자를 나타내는 클레임을 포함 하려면입니다.

사용자 지정 STS를 사용할 때는 다음 두 가지를 변경해야 합니다.

* 자산에 대한 라이선스 배달 서비스를 구성할 때 Azure AD의 현재 키 대신 사용자 지정 STS에서 확인에 사용한 보안 키를 지정해야 합니다.
* JWT 토큰이 생성되면 Azure AD의 현재 X509 인증서의 프라이빗 키 대신 보안 키가 지정됩니다.

두 가지 유형의 보안 키가 있습니다.

* 대칭 키: JWT를 생성 및 확인하는 데 동일한 키가 사용됩니다.
* 비대칭 키: JWT를 암호화/생성하는 데는 프라이빗 키가, 토큰을 확인하는 데는 공개 키가 사용되는 방식으로 X509 인증서에 공개-프라이빗 키 쌍이 사용됩니다.

개발 플랫폼으로 .NET Framework/C#을 사용하는 경우 비공개 보안 키에 사용된 X509 인증서에는 키 길이가 2048 이상이어야 합니다. 이 키 길이는 .NET Framework의 X509AsymmetricSecurityKey 클래스에 대 한 요구 사항입니다. 그렇지 않으면 다음 예외가 throw 됩니다. IDX10630: 서명에 대 한 ' System.identitymodel. X509AsymmetricSecurityKey '은 ' 2048 ' 비트 보다 작을 수 없습니다.

## <a name="custom-key-and-license-acquisition-url"></a>사용자 지정 키 및 라이선스 취득 URL

다른 라이선스/키 배달 서비스 (Media Services)를 지정 하려면 다음 템플릿을 사용 합니다. 템플릿에서 두 개의 대체 가능 필드가 있으므로 자산 당 스트리밍 정책을 만드는 대신 여러 자산에서 스트리밍 정책을 공유할 수 있습니다. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: 최종 사용자 플레이어에 게 키를 전달 하는 사용자 지정 서비스의 URL에 대 한 템플릿입니다. 키를 발급 하는 Azure Media Services을 사용 하는 경우에는 필요 하지 않습니다. 

   이 템플릿은 서비스에서 요청에 지정 된 값을 사용 하 여 런타임에 업데이트 하는 대체 가능한 토큰을 지원 합니다.  현재 지원 되는 토큰 값은 다음과 같습니다.
   * `{AlternativeMediaId}`-StreaminglocAlternativeMediaId의 값으로 대체 됩니다.
   * `{ContentKeyId}`는 요청 된 키의 식별자 값으로 대체 됩니다.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: 최종 사용자 플레이어에 게 라이선스를 제공 하는 사용자 지정 서비스의 URL에 대 한 템플릿입니다. 라이선스 발급에 Azure Media Services를 사용 하는 경우에는 필요 하지 않습니다.

   이 템플릿은 서비스에서 요청에 지정 된 값을 사용 하 여 런타임에 업데이트 하는 대체 가능한 토큰을 지원 합니다. 현재 지원 되는 토큰 값은 다음과 같습니다.  
   * `{AlternativeMediaId}`-StreaminglocAlternativeMediaId의 값으로 대체 됩니다.
   * `{ContentKeyId}`는 요청 된 키의 식별자 값으로 대체 됩니다. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: 이전 템플릿과 동일 합니다. Widevine 전용입니다. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: 이전 템플릿과 동일 하며 FairPlay에만 해당 합니다.  

예를 들면 다음과 같습니다.

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` 에는 요청 된 키의 값이 있습니다. `AlternativeMediaId`사용자 측의 엔터티에 요청을 매핑하려는 경우를 사용할 수 있습니다. 예를 들어를 `AlternativeMediaId` 사용 하 여 사용 권한을 조회할 수 있습니다.

사용자 지정 라이선스/키 취득 Url을 사용 하는 REST 예제는 [스트리밍 정책-만들기](/rest/api/media/streamingpolicies/create)를 참조 하세요.

> [!NOTE]
> Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="troubleshoot"></a>문제 해결

`MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY`오류가 발생 하면 적절 한 스트리밍 정책을 지정 해야 합니다.

로 끝나는 오류가 발생 하면 `_NOT_SPECIFIED_IN_URL` URL에 암호화 형식을 지정 해야 합니다. 예제는 `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`입니다. [스트리밍 프로토콜 및 암호화 유형을](#streaming-protocols-and-encryption-types)참조 하세요.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [AES 암호화로 보호](protect-with-aes128.md)
* [DRM으로 보호](protect-with-drm.md)
* [액세스 제어를 사용 하 여 다중 DRM 콘텐츠 보호 시스템 디자인](design-multi-drm-system-with-access-control.md)
* [스토리지 쪽 암호화](storage-account-concept.md#storage-side-encryption)
* [자주 묻는 질문](frequently-asked-questions.md)
* [JSON 웹 토큰 처리기](/dotnet/framework/security/json-web-token-handler)
