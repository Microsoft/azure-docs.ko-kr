---
title: Widevine 보호 된 콘텐츠의 오프 라인 스트리밍을 위한 계정 구성-Azure
description: 이 항목에서는 Widevine 보호 된 콘텐츠의 오프 라인 스트리밍을 위해 Azure Media Services 계정을 구성 하는 방법을 보여 줍니다.
services: media-services
keywords: 대시, DRM, Widevine 오프 라인 모드, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: 5137f35a4707aa68adfbf3f326ca9e4bfb40f0f4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970332"
---
# <a name="offline-widevine-streaming-for-android"></a>Android 용 오프 라인 Widevine 스트리밍  

> [!div class="op_single_selector" title1="사용 중인 Media Services의 버전을 선택 합니다."]
> * [3-as verzió](../latest/offline-widevine-for-android.md)
> * [2-es verzió](offline-widevine-for-android.md)

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). 또한 [v2에서 v3로 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md) 을 참조 하세요.

온라인 스트리밍에 대 한 콘텐츠를 보호 하는 것 외에도, 미디어 콘텐츠 구독 및 임대 서비스는 인터넷에 연결 되어 있지 않을 때 작동 하는 다운로드 가능한 콘텐츠를 제공 합니다. 네트워크에서의 연결을 끊으면 비행기 모드로 재생 하기 위해 휴대폰 이나 태블릿에 콘텐츠를 다운로드 해야 할 수 있습니다. 콘텐츠를 다운로드 하는 데 사용할 수 있는 추가 시나리오는 다음과 같습니다.

- 일부 콘텐츠 공급자는 국가/지역 테두리를 넘어 DRM 라이선스 배달을 허용 하지 않을 수 있습니다. 해외 이동 하는 동안 사용자가 콘텐츠를 시청 하려는 경우 오프 라인 다운로드가 필요 합니다.
- 일부 국가/지역에서는 인터넷 가용성 및/또는 대역폭이 제한 됩니다. 사용자는 만족 스러운 보기 환경을 위해 충분히 높은 해상도로 콘텐츠를 볼 수 있도록 콘텐츠를 다운로드 하도록 선택할 수 있습니다.

이 문서에서는 Android 장치에서 Widevine으로 보호 되는 대시 콘텐츠에 대해 오프 라인 모드 재생을 구현 하는 방법을 설명 합니다. 오프 라인 DRM을 사용 하면 콘텐츠에 대 한 구독, 임대 및 구매 모델을 제공 하 여 서비스 고객이 인터넷의 연결을 끊을 때 콘텐츠를 쉽게 가져올 수 있습니다.

Android player 앱을 빌드하는 경우 다음 세 가지 옵션을 설명 합니다.

> [!div class="checklist"]
> * ExoPlayer SDK의 Java API를 사용 하 여 플레이어 빌드
> * ExoPlayer SDK의 Xamarin 바인딩을 사용 하 여 플레이어 빌드
> * Chrome mobile browser v62 이상에서 EME (암호화 된 미디어 확장) 및 MSE (미디어 원본 확장)를 사용 하 여 플레이어 빌드

이 문서는 또한 Widevine 보호 된 콘텐츠의 오프 라인 스트림과 관련 된 몇 가지 일반적인 질문에 대 한 답을 얻을 수 있습니다.

## <a name="requirements"></a>Követelmények 

Android 장치에서 Widevine에 대 한 오프 라인 DRM을 구현 하기 전에 먼저 다음을 수행 해야 합니다.

- Widevine DRM을 사용 하는 온라인 콘텐츠 보호에 도입 된 개념을 숙지 하세요. 이 내용은 다음 문서/샘플에 자세히 설명 되어 있습니다.
    - [Azure Media Services를 사용 하 여 DRM 라이선스 또는 AES 키 제공](media-services-deliver-keys-and-licenses.md)
    - [CENC többplatformos DRM és hozzáférés-vezérlés használatával: Egy referenciaterv és megvalósítás az Azure-on és az Azure Media Services szolgáltatásban](media-services-cenc-with-multidrm-access-control.md)
    - [.NET에서 PlayReady 및/또는 Widevine 동적 Common Encryption 사용](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Azure Media Services를 사용 하 여 .NET으로 PlayReady 및/또는 Widevine 라이선스 제공](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- 오프 라인 Widevine DRM 재생을 지원할 수 있는 오픈 소스 비디오 플레이어 SDK 인 Android 용 Google SDK에 대해 잘 알고 있어야 합니다. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer 개발자 가이드](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer 개발자 블로그](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Azure Media Services의 콘텐츠 보호 구성

Media Services에서 자산의 Widevine 보호를 구성 하는 경우 다음 세 가지를 지정 하는 ContentKeyAuthorizationPolicyOption을 만들어야 합니다.

1. DRM 시스템 (Widevine
2. 콘텐츠 키 배달이 라이선스 배달 서비스 (개방형 또는 토큰 권한 부여)에서 승인 되는 방법을 지정 하는 ContentKeyAuthorizationPolicyRestriction
3. DRM (Widevine) 라이선스 템플릿

Widevine 라이선스에 대해 **오프 라인** 모드를 사용 하도록 설정 하려면 [widevine 라이선스 템플릿을](media-services-widevine-license-template-overview.md)구성 해야 합니다. **Policy_overrides** 개체에서 **can_persist** 속성을 **true** (기본값은 false)로 설정 합니다. 

다음 코드 샘플에서는 .NET을 사용 하 여 Widevine 라이선스에 대해 **오프 라인** 모드를 사용 하도록 설정 합니다. 이 코드는 .NET 샘플에서 [PlayReady 및/또는 Widevine Dynamic Common Encryption를 사용 하](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) 는 것을 기반으로 합니다. 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                             //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),   //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>오프 라인 재생을 위한 Android 플레이어 구성

Android 장치용 네이티브 플레이어 앱을 개발 하는 가장 쉬운 방법은 오픈 소스 비디오 플레이어 SDK 인 [Google EXOPLAYER sdk](https://github.com/google/ExoPlayer)를 사용 하는 것입니다. ExoPlayer는 MPEG-2 및 Microsoft 부드러운 스트리밍 배달 프로토콜을 포함 하 여 Android의 기본 MediaPlayer API에서 현재 지원 되지 않는 기능을 지원 합니다.

ExoPlayer 버전 2.6 이상에는 오프 라인 Widevine DRM 재생을 지 원하는 많은 클래스가 포함 되어 있습니다. 특히 OfflineLicenseHelper 클래스는 용이를 사용 하 여 오프 라인 라이선스를 다운로드, 갱신 및 해제할 수 있는 유틸리티 함수를 제공 합니다. SDK 폴더 "library/core/src/main/java/com/google/android/exoplayer2/offline/"에서 제공 하는 클래스는 오프 라인 비디오 콘텐츠 다운로드를 지원 합니다.

다음 클래스 목록은 Android 용 ExoPlayer SDK의 오프 라인 모드를 용이 하 게 합니다.

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession. java
- library/core/src/main/java/com/google/android/exoplayer2/drm/용이
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession. java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession. java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper 
- library/core/src/main/java/com/google/android/exoplayer2/오프 라인/다운로더. java
- library/대시/src/main/java/com/google/android/exoplayer2/source/대시/오프 라인/대시보드. java 

개발자는 응용 프로그램을 개발 하는 동안 [ExoPlayer 개발자 가이드](https://google.github.io/ExoPlayer/guide.html) 및 해당 [개발자 블로그](https://medium.com/google-exoplayer) 를 참조 해야 합니다. Google은 현재 Widevine offline을 지 원하는 ExoPlayer 앱에 대 한 완전히 문서화 된 참조 구현 또는 샘플 코드를 릴리스 하지 않았으므로 개발자 가이드 및 블로그로 정보를 제한 합니다. 

### <a name="working-with-older-android-devices"></a>이전 Android 장치 작업

일부 이전 Android 장치의 경우 다음 **policy_overrides** 속성에 대 한 값을 설정 해야 합니다 ( [widevine 라이선스 템플릿에](media-services-widevine-license-template-overview.md)정의 됨 **: rental_duration_seconds**, **playback_duration_seconds**및 **license_duration_seconds**. 또는이 값을 0으로 설정 하 여 무한/무제한 기간을 의미 합니다.  

정수 오버플로 버그가 발생 하지 않도록 하려면 값을 설정 해야 합니다. 문제에 대 한 자세한 설명은 https://github.com/google/ExoPlayer/issues/3150 및 https://github.com/google/ExoPlayer/issues/3112 를 참조 하세요. <br/>값을 명시적으로 설정 하지 않으면 **PlaybackDurationRemaining** 및 **LicenseDurationRemaining** 에 대 한 매우 큰 값이 할당 됩니다 (예: 64 비트 정수의 최대 양수 값인 9223372036854775807). 결과적으로 Widevine 사용 하는 라이선스는 만료 된 것 이므로 암호 해독이 발생 하지 않습니다. 

Android 5.0은 ARMv8 ([ADVANCED RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) 및 64 비트 플랫폼을 완벽 하 게 지원 하도록 설계 된 최초의 android 버전 이므로 Android 5.0 롤리팝 이상에서는이 문제가 발생 하지 않습니다. Android 4.4 KitKat는 원래 다른 이전 Android 버전과 마찬가지로 ARMv7 및 32 비트 플랫폼을 지원 하도록 설계 되었습니다.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Xamarin을 사용 하 여 Android 재생 앱 빌드

다음 링크를 사용 하 여 ExoPlayer에 대 한 Xamarin 바인딩을 찾을 수 있습니다.

- [Google ExoPlayer 라이브러리에 대 한 Xamarin 바인딩 라이브러리](https://github.com/martijn00/ExoPlayerXamarin)
- [ExoPlayer NuGet에 대 한 Xamarin 바인딩](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

또한 다음 스레드를 참조 하세요. [Xamarin 바인딩](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Android 용 Chrome player 앱

[Android v. 62의 Chrome](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)릴리스부터 EME의 영구 라이선스가 지원 됩니다. 이제 [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) 은 Android 용 Chrome 에서도 지원 됩니다. 최종 사용자에 게이 버전의 Chrome이 있는 경우 Chrome에서 오프 라인 재생 응용 프로그램을 만들 수 있습니다. 

또한 Google은 점진적 웹 앱 (PWA) 샘플을 생성 하 고 오픈 소스를 제공 합니다. 

- [Forráskód](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google 호스팅된 버전](https://biograf-155113.appspot.com/ttt/episode-2/) (Android 장치에서 Chrome v 62 이상 에서만 작동)

Android 휴대폰에서 모바일 Chrome 브라우저를 v62 이상으로 업그레이드 하 고 위의 호스팅된 샘플 앱을 테스트 하는 경우 온라인 스트리밍 및 오프 라인 재생이 모두 작동 하는 것을 볼 수 있습니다.

위의 오픈 소스 PWA 앱은 node.js에서 작성 됩니다. Ubuntu 서버에서 자신의 버전을 호스트 하려는 경우 재생을 방해할 수 있는 다음과 같은 일반적인 문제를 염두에 두어야 합니다.

1. CORS 문제: 샘플 앱의 샘플 비디오는 https://storage.googleapis.com/biograf-video-files/videos/ 에서 호스팅됩니다. Google은 Google Cloud Storage 버킷에 호스트 되는 모든 테스트 샘플에 대해 CORS를 설정 했습니다. Cors 헤더를 사용 하 여 제공 되며 CORS 항목을 명시적으로 지정 하 여 https://biograf-155113.appspot.com (google에서 샘플을 호스트 하는 도메인) 다른 사이트의 액세스를 방지 합니다. 시도 하면 다음 HTTP 오류가 표시 됩니다. 요청 된 리소스에 ' 액세스 제어- https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: -원본 ' 헤더가 없어서 ' 액세스 하지 못했습니다. ' 헤더가 표시 되지 않습니다. 따라서 원본 ' https:\//13.85.80.81:8080 '은 액세스가 허용 되지 않습니다. 불투명 응답이 요구에 서비스를 제공 하는 경우 요청 모드를 ' 비 cors '로 설정 하 여 CORS를 사용 하지 않도록 설정 된 리소스를 인출 합니다.
2. 인증서 문제: Chrome v 58부터 Widevine의 경우 HTTPS가 필요 합니다. 따라서 X509 인증서를 사용 하 여 HTTPS를 통해 샘플 앱을 호스트 해야 합니다. 다음 요구 사항으로 인해 일반적인 테스트 인증서가 작동 하지 않습니다. 다음과 같은 최소 요구 사항을 충족 하는 인증서를 얻어야 합니다.
    - Chrome 및 Firefox에는 인증서에 있는 SAN 주체 대체 이름 설정이 있어야 합니다.
    - 인증서에 신뢰할 수 있는 CA가 있어야 하 고 자체 서명 된 개발 인증서가 작동 하지 않습니다.
    - 인증서에는 웹 서버 또는 게이트웨이의 DNS 이름과 일치 하는 CN이 있어야 합니다.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="question"></a>Kérdés

일부 클라이언트/사용자 및 비영구 라이선스 (오프 라인 사용 안 함)에 대해 영구 라이선스 (오프 라인 사용)를 어떻게 제공할 수 있나요? 콘텐츠를 복제 하 고 별도의 콘텐츠 키를 사용 해야 하나요?

### <a name="answer"></a>Válasz
콘텐츠를 복제할 필요는 없습니다. 콘텐츠 및 단일 ContentKeyAuthorizationPolicy의 단일 복사본을 사용할 수 있지만 두 개의 별도의 ContentKeyAuthorizationPolicyOption을 사용할 수 있습니다.

1. IContentKeyAuthorizationPolicyOption 1: 영구 라이선스를 사용 하 고, ContentKeyAuthorizationPolicyRestriction 1을 사용 하 여 license_type = "Persistent"와 같은 클레임을 포함 합니다.
2. IContentKeyAuthorizationPolicyOption 2: 비영구 라이선스를 사용 하 고, ContentKeyAuthorizationPolicyRestriction 2를 사용 합니다. 여기에는 license_type = "비영구"와 같은 클레임이 포함 됩니다.

이러한 방식으로 라이선스 요청이 클라이언트 앱에서 들어오는 경우 라이선스 요청에서 차이가 발생 하지 않습니다. 그러나 다른 최종 사용자/장치에 대해 STS는 서로 다른 클레임을 포함 하는 다양 한 JWT 토큰을 발급 하는 비즈니스 논리를 포함 해야 합니다 (위의 두 license_type). JWT 토큰의 클레임 값은 라이선스 서비스에서 라이선스 유형을 발급 하도록 결정 하는 데 사용 됩니다 (영구 또는 비영구).

즉, STS (보안 토큰 서비스)에는 해당 하는 클레임 값을 토큰에 추가 하기 위한 비즈니스 논리 및 클라이언트/장치 정보가 있어야 합니다.

### <a name="question"></a>Kérdés

Widevine 보안 수준에 대해 Google의 [WIDEVINE DRM 아키텍처 개요 문서](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) 에서는 세 가지 보안 수준을 정의 합니다. 그러나 [Widevine 라이선스 템플릿에 대 한 Azure Media Services 설명서](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)에서는 5 가지 보안 수준을 간략하게 설명 합니다. 서로 다른 두 보안 수준 집합 간의 관계 또는 매핑은 무엇입니까?

### <a name="answer"></a>Válasz

Google의 [Widevine DRM 아키텍처 개요](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)에서 다음 세 가지 보안 수준을 정의 합니다.

1.  보안 수준 1: 모든 콘텐츠 처리, 암호화 및 제어는 신뢰할 수 있는 실행 환경 (티) 내에서 수행 됩니다. 일부 구현 모델에서는 다양 한 칩에서 보안 처리를 수행할 수 있습니다.
2.  보안 수준 2: 티의 암호화 (비디오 처리는 아님)를 수행 합니다. 암호 해독 된 버퍼는 응용 프로그램 도메인으로 반환 되 고 별도의 비디오 하드웨어 또는 소프트웨어를 통해 처리 됩니다. 그러나 수준 2에서 암호화 정보는 여전히 티 티에서 처리 됩니다.
3.  보안 수준 3에는 장치에 대 한 티가 없습니다. 호스트 운영 체제에서 암호화 정보 및 암호 해독 된 콘텐츠를 보호 하기 위해 적절 한 조치를 취할 수 있습니다. 수준 3 구현에는 하드웨어 암호화 엔진도 포함 될 수 있지만 보안이 아닌 성능도 향상 됩니다.

이와 동시에 [Widevine 라이선스 템플릿에 대 한 Azure Media Services 설명서](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)에서 content_key_specs의 security_level 속성은 다음과 같은 5 가지 값을 가질 수 있습니다 (재생을 위한 클라이언트 견고성 요구 사항).

1.  소프트웨어 기반 화이트 박스 암호화가 필요 합니다.
2.  소프트웨어 암호화 및 난독 처리 된 디코더가 필요 합니다.
3.  키 자료 및 암호화 작업은 하드웨어 지원 된 티에서 수행 해야 합니다.
4.  콘텐츠의 암호화 및 디코딩을 하드웨어 지원 된 티에서 수행 해야 합니다.
5.  미디어의 암호화, 디코딩 및 모든 처리 (압축 및 압축 해제)는 하드웨어 지원 티에서 처리 되어야 합니다.

두 보안 수준은 모두 Google Widevine 통해 정의 됩니다. 차이점은 아키텍처 수준 또는 API 수준의 사용 수준에 있습니다. 5 가지 보안 수준은 Widevine API에서 사용 됩니다. Security_level를 포함 하는 content_key_specs 개체는 역직렬화 되 고 Widevine 라이선스 서비스를 Azure Media Services 여 Widevine 글로벌 배달 서비스로 전달 됩니다. 다음 표에서는 두 보안 수준 집합 간의 매핑을 보여 줍니다.

| **Widevine 아키텍처에 정의 된 보안 수준** |**Widevine API에서 사용 되는 보안 수준**|
|---|---| 
| **보안 수준 1**: 모든 콘텐츠 처리, 암호화 및 제어는 신뢰할 수 있는 실행 환경 (티) 내에서 수행 됩니다. 일부 구현 모델에서는 다양 한 칩에서 보안 처리를 수행할 수 있습니다.|**security_level = 5**: 미디어의 암호화, 디코딩 및 모든 처리 (압축 및 압축 해제)는 하드웨어 지원 된 티에서 처리 되어야 합니다.<br/><br/>**security_level = 4**: 콘텐츠의 암호화 및 디코딩을 하드웨어 지원 된 티에서 수행 해야 합니다.|
**보안 수준 2**: 티의 암호화 (비디오 처리는 아님)를 수행 합니다. 암호 해독 된 버퍼는 응용 프로그램 도메인으로 반환 되 고 별도의 비디오 하드웨어 또는 소프트웨어를 통해 처리 됩니다. 그러나 수준 2에서 암호화 정보는 여전히 티 티에서 처리 됩니다.| **security_level = 3**: 하드웨어 지원 된 티에서 키 자료 및 암호화 작업을 수행 해야 합니다. |
| **보안 수준 3**:은 장치에 대 한 티가 없습니다. 호스트 운영 체제에서 암호화 정보 및 암호 해독 된 콘텐츠를 보호 하기 위해 적절 한 조치를 취할 수 있습니다. 수준 3 구현에는 하드웨어 암호화 엔진도 포함 될 수 있지만 보안이 아닌 성능도 향상 됩니다. | **security_level = 2**: 소프트웨어 암호화 및 난독 처리 된 디코더가 필요 합니다.<br/><br/>**security_level = 1**: 소프트웨어 기반 화이트 박스 암호화가 필요 합니다.|

### <a name="question"></a>Kérdés

콘텐츠 다운로드에 시간이 오래 걸리는 이유는 무엇 인가요?

### <a name="answer"></a>Válasz

다운로드 속도를 개선 하는 방법에는 두 가지가 있습니다.

1.  최종 사용자가 콘텐츠 다운로드를 위해 원본/스트리밍 엔드포인트 대신 CDN을 적중 하지 않도록 CDN을 사용 하도록 설정 합니다. 사용자가 스트리밍 엔드포인트을 적중 하는 경우 각 HLS 세그먼트 또는 대시 조각이 동적으로 패키지 되 고 암호화 됩니다. 이 대기 시간이 각 세그먼트/조각에 대해 밀리초 단위로 표시 되는 경우에도 시간이 오래 걸리는 비디오를 사용할 경우 누적 된 대기 시간으로 인해 다운로드가 길어질 수 있습니다.
2.  최종 사용자에 게 모든 콘텐츠 대신 비디오 품질 계층과 오디오 트랙을 선택적으로 다운로드 하는 옵션을 제공 합니다. 오프 라인 모드의 경우 모든 품질 계층을 다운로드할 수 있는 지점이 없습니다. 다음 두 가지 방법으로이 작업을 수행할 수 있습니다.
    1.  클라이언트 제어: 플레이어 앱 자동 선택 또는 사용자가 다운로드할 비디오 품질 계층 및 오디오 트랙을 선택 합니다.
    2.  서비스 제어: Azure Media Services의 동적 매니페스트 기능을 사용 하 여 HLS 재생 목록이 나 대시 MPD 단일 비디오 품질 계층 및 선택한 오디오 트랙으로 제한 하는 (전역) 필터를 만들 수 있습니다. 그런 다음 최종 사용자에 게 제공 되는 다운로드 URL에이 필터를 포함 합니다.

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="summary"></a>Összefoglalás

이 문서에서는 Android 장치에서 Widevine으로 보호 되는 대시 콘텐츠에 대해 오프 라인 모드 재생을 구현 하는 방법을 설명 했습니다.  또한 Widevine 보호 된 콘텐츠의 오프 라인 스트림과 관련 된 몇 가지 일반적인 질문에 답변 했습니다.
