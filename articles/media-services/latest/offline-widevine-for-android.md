---
title: Azure Media Services v3로 Widevstandroid 오프 라인 스트리밍
description: 이 항목에서는 Widevine 보호 콘텐츠의 오프라인 스트리밍에 대한 Azure Media Services 계정을 구성하는 방법을 보여 줍니다.
services: media-services
keywords: DASH, DRM, Widevine 오프라인 모드, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: a1e85f37a0dfdbb7ff10db13e53b1998ef391caa
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267194"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Media Services v3을 사용 하 여 Android 용 오프 라인 Widevine 스트리밍

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

온라인 스트리밍을 위해 콘텐츠를 보호하는 것 외에도, 미디어 콘텐츠 구독 및 임대 서비스는 인터넷에 연결되지 않았을 때 작동하는 다운로드 가능한 콘텐츠를 제공합니다. 네트워크에서 연결이 끊긴 상태로 비행하는 비행기 모드에서 재생하려면 휴대폰이나 태블릿에 콘텐츠를 다운로드해야 할 수 있습니다. 콘텐츠를 다운로드하려는 추가적인 시나리오는 다음과 같습니다.

- 일부 콘텐츠 공급자는 국가/지역 테두리를 넘어 DRM 라이선스 배달을 허용 하지 않을 수 있습니다. 사용자를 해외 여행을 하면서 콘텐츠를 보려 할 경우 오프 라인 다운로드가 필요합니다.
- 일부 국가/지역에서는 인터넷 가용성 및/또는 대역폭이 제한 됩니다. 사용자가 만족스러운 보기 환경을 위해 충분히 높은 해상도로 콘텐츠를 볼 수 있도록 콘텐츠를 다운로드하도록 선택할 수도 있습니다.

이 문서에서는 Android 디바이스에서 Widevine에 의해 보호되는 DASH 콘텐츠에 대해 오프라인 모드 재생을 구현하는 방법을 설명합니다. 오프라인 DRM은 콘텐츠에 대해 구독, 임대 및 구매 모델을 제공하여, 서비스 고객이 인터넷에 연결되어 있지 않을 때도 콘텐츠를 쉽게 사용하도록 할 수 있습니다.

Android 플레이어 앱을 빌드하는 경우 다음 세 가지 옵션을 사용할 수 있습니다.

> [!div class="checklist"]
> * ExoPlayer SDK의 Java API를 사용하여 플레이어 빌드
> * ExoPlayer SDK의 Xamarin 바인딩을 사용하여 플레이어 빌드
> * Chrome 모바일 브라우저 v62 이상에서 EME(Encrypted Media Extension) 및 MSE(Media Source Extension)를 사용하여 플레이어 빌드

또한 Widevine 보호 콘텐츠의 오프라인 스트리밍과 관련된 몇 가지 일반적인 질문에 대한 답변도 제공되어 있습니다.

> [!NOTE]
> 오프 라인 DRM은 콘텐츠를 다운로드할 때 라이선스에 대 한 단일 요청을 만드는 경우에만 청구 됩니다. 모든 오류는 청구 되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소 

Android 디바이스에서 Widevine에 대한 오프라인 DRM을 구현하기 전에 먼저 다음을 수행해야 합니다.

- Widevine DRM을 사용하는 온라인 콘텐츠 보호를 위해 도입된 개념을 숙지합니다. 이 내용은 다음 문서/샘플에서 자세히 다룹니다.
    - [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md)
    - [DRM 동적 암호화 및 라이선스 배달 서비스 사용](protect-with-drm.md)
- https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git을 복제합니다.

    [.NET을 사용하여 DRM으로 암호화](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)에서 코드를 수정하여 Widevine 구성을 추가해야 합니다.  
- 오프라인 Widevine DRM 재생을 지원할 수 있는 오픈 소스 비디오 플레이어 SDK인 Android용 Google ExoPlayer SDK에 익숙해집니다. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer 개발자 가이드](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer 개발자 블로그](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Azure Media Services에서 콘텐츠 보호 구성

[GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) 메서드에서는 다음 단계가 필요합니다.

1. 라이선스 배달 서비스에서 콘텐츠 키 배달이 허가되는 방식을 지정합니다. 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Widevine 라이선스 템플릿 구성:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. ContentKeyPolicyOptions 만들기:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>오프라인 모드 사용

Widevine 라이선스에 대해 **오프라인** 모드를 사용하도록 설정하려면 [Widevine 라이선스 템플릿](widevine-license-template-overview.md)을 구성해야 합니다. [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563)에 표시된 대로 **policy_overrides** 개체에서 **can_persist** 속성을 **true**(기본값은 false)로 설정합니다. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>오프라인 재생을 위한 Android 플레이어 구성

Android 디바이스용 네이티브 플레이어 앱을 개발하는 가장 쉬운 방법은 오픈 소스 비디오 플레이어 SDK인 [Google ExoPlayer SDK](https://github.com/google/ExoPlayer)를 사용하는 것입니다. ExoPlayer는 MPEG-2 및 Microsoft 부드러운 스트리밍 배달 프로토콜을 포함 하 여 Android의 기본 MediaPlayer API에서 현재 지원 되지 않는 기능을 지원 합니다.

ExoPlayer 버전 2.6 이상에는 오프라인 Widevine DRM 재생을 지원하는 여러 클래스가 포함되어 있습니다. 특히, OfflineLicenseHelper 클래스는 DefaultDrmSessionManager를 사용하여 오프라인 라이선스 다운로드, 갱신 및 해제를 용이하게 수행하도록 하는 유틸리티 함수를 제공합니다. SDK 폴더 "library/core/src/main/java/com/google/android/exoplayer2/offline/"에 제공되는 클래스는 오프라인 비디오 콘텐츠 다운로드를 지원합니다.

다음과 같은 클래스 목록은 Android용 ExoPlayer SDK에서 오프라인 모드를 지원합니다.

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

개발자는 애플리케이션 개발 중에 [ExoPlayer 개발자 가이드](https://google.github.io/ExoPlayer/guide.html) 및 해당 [개발자 블로그](https://medium.com/google-exoplayer)를 참조하는 것이 좋습니다. Google은 현재, Widevine 오프라인을 지원하는 ExoPlayer 앱의 완전히 문서화된 참조 구현 또는 샘플 코드를 출시하지 않았으므로, 정보가 개발자 가이드 및 블로그로 제한됩니다. 

### <a name="working-with-older-android-devices"></a>이전 Android 디바이스 사용

일부 이전 Android 디바이스의 경우, 다음 **policy_overrides** 속성([Widevine 라이선스 템플릿](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** 및 **license_duration_seconds**)에 대한 값을 설정해야 합니다. 또는 무한/무제한 기간을 의미하는 0으로 설정할 수도 있습니다.  

정수 오버플로 버그를 방지하도록 해당 값을 설정해야 합니다. 문제에 대한 자세한 설명은 https://github.com/google/ExoPlayer/issues/3150 및 https://github.com/google/ExoPlayer/issues/3112를 참조하세요. <br/>값을 명시적으로 설정하지 않으면 **PlaybackDurationRemaining** 및 **LicenseDurationRemaining** 값으로 매우 큰 값(예: 64비트 정수에 대한 최대 양수인 9223372036854775807)이 할당됩니다. 결과적으로, Widevine 라이선스는 만료된 것으로 나타나며, 암호 해독은 발생하지 않습니다. 

Android 4.4 KitKat은 원래부터 다른 이전 Android 버전과 마찬가지로 ARMv7 및 32비트 플랫폼을 지원하도록 디자인되어 있지만, Android 5.0은 ARMv8([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) 및 64비트 플랫폼을 완전하게 지원하도록 디자인된 최초의 Android 버전이므로 Android 5.0 Lollipop 이상에서는 이 문제가 발생하지 않습니다.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Xamarin을 사용하여 Android 재생 앱 빌드

다음 링크를 사용하여 ExoPlayer에 대한 Xamarin 바인딩을 찾을 수 있습니다.

- [Google ExoPlayer 라이브러리에 대한 Xamarin 바인딩 라이브러리](https://github.com/martijn00/ExoPlayerXamarin)
- [ExoPlayer NuGet에 대한 Xamarin 바인딩](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

스레드 [Xamarin 바인딩](https://github.com/martijn00/ExoPlayerXamarin/pull/57)도 참조하세요. 

## <a name="chrome-player-apps-for-android"></a>Android용 Chrome 플레이어 앱

[Android v. 62의 Chrome](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)릴리스부터 EME의 영구 라이선스가 지원 됩니다. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1)은 이제 Android용 Chrome에서도 지원됩니다. 따라서, 최종 사용자에게 이 버전 이상의 Chrome이 있기만 하면 Chrome에서 오프라인 재생 애플리케이션을 만들 수 있습니다. 

또한 Google은 PWA(Progressive Web App) 샘플을 생성하고 오픈 소스로 설정했습니다. 

- [소스 코드](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google 호스티드 버전](https://biograf-155113.appspot.com/ttt/episode-2/)(Android 디바이스의 Chrome v 62 이상에서만 작동)

Android 휴대폰에서 모바일 Chrome 브라우저를 v62(또는 이상)로 업그레이드하고 위의 호스티드 샘플 앱을 테스트하는 경우, 온라인 스트리밍 및 오프라인 재생이 둘 다 작동되는 것을 볼 수 있습니다.

위의 오픈 소스 PWA 앱은 Node.js에서 작성됩니다. Ubuntu 서버에 고유한 버전을 호스트하려는 경우, 재생을 방해할 수 있는 다음과 같은 일반적인 문제가 발생한다는 점에 유의합니다.

1. CORS 문제: 샘플 앱의 샘플 비디오는 https://storage.googleapis.com/biograf-video-files/videos/에서 호스팅됩니다. Google은 Google 클라우드 스토리지 버킷에 호스트되는 모든 테스트 샘플에 대해 CORS를 설정했습니다. CORS 항목 `https://biograf-155113.appspot.com` (Google에서 해당 샘플을 호스트하는 도메인)을 명시적으로 지정하는 CORS 헤더가 사용되어 다른 사이트의 액세스를 방지합니다. 시도 하면 다음과 같은 HTTP 오류가 표시 됩니다. `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. 인증서 문제: Chrome v 58부터, Widevine용 EME에는 HTTPS가 필요합니다. 따라서 X509 인증서를 사용해서 HTTPS를 통해 샘플 앱을 호스트해야 합니다. 일반적인 테스트 인증서는 다음 요구 사항 때문에 작동하지 않습니다. 즉, 다음과 같은 최소 요구 사항을 충족하는 인증서를 가져와야 합니다.
    - Chrome 및 Firefox에서는 SAN 주체 대체 이름 설정이 인증서에 있어야 합니다.
    - 인증서는 신뢰할 수 있는 CA에서 발급한 것이어야 하고, 자체 서명된 인증서가 작동하지 않습니다.
    - 인증서는 웹 서버 또는 게이트웨이의 DNS 이름과 일치하는 CN이 있어야 합니다.

## <a name="faqs"></a>FAQ

자세한 내용은 [Widevine faq](frequently-asked-questions.md#widevine-streaming-for-android)를 참조 하십시오.

## <a name="additional-notes"></a>추가적인 참고 사항

Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="summary"></a>요약

이 문서에서는 Android 디바이스에서 Widevine에 의해 보호되는 DASH 콘텐츠에 대해 오프라인 모드 재생을 구현하는 방법을 설명합니다.  Widevine 보호 콘텐츠의 오프라인 스트리밍과 관련된 몇 가지 일반적인 질문에 대한 답변도 제공되어 있습니다.
