---
title: Azure Media Services v3 질문과 대답 | Microsoft Docs
description: 이 문서에서는 v3 Azure Media Services에 대 한 질문과 대답을 제공 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: d34b5aaaa12a3d296f92e0d7be34ae76931d8506
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268488"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 질문과 대답

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 v3 Azure Media Services에 대 한 질문과 대답을 제공 합니다.

## <a name="general"></a>일반

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Media Services v3에 대 한 Azure Portal 제한 사항은 무엇입니까?

[Azure Portal](https://portal.azure.com/) 를 사용 하 여 v3 라이브 이벤트를 관리 하 고, v3 자산 및 작업을 보고, api 액세스에 대 한 정보를 가져오고, 콘텐츠를 암호화할 수 있습니다. <br/>다른 모든 관리 작업 (예: 변환 및 작업 관리 또는 v3 콘텐츠 분석)의 경우 [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)또는 지원 되는 [sdk](media-services-apis-overview.md#sdks)중 하나를 사용 합니다.

이전에 Media Services v3 API를 사용하여 비디오를 Media Services 계정에 업로드했거나 라이브 출력을 기반으로 콘텐츠가 생성된 경우 Azure Portal에서 **인코딩**, **분석** 또는 **암호화** 단추가 표시되지 않습니다. Media Services v3 API를 사용하여 이러한 작업을 수행합니다.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure Media Services 리소스에 대 한 작업을 수행할 수 있는 Azure 역할은 무엇 인가요? 

[Media Services 계정에 대 한 RBAC (역할 기반 액세스 제어)를](rbac-overview.md)참조 하세요.

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Apple iOS 장치로 stream을 어떻게 할까요? 하 시겠습니까?

경로 끝에 **(format = m3u8-aapl-v3-aapl)** 가 있는지 확인 하 여 (URL의 **/매니페스트** 부분 뒤에) 스트리밍 원본 서버가 Apple iOS 네이티브 장치에서 사용 하기 위한 HLS (HTTP 라이브 스트리밍) 콘텐츠를 반환 하도록 지시 합니다. 자세한 내용은 [콘텐츠 배달](dynamic-packaging-overview.md)을 참조 하세요.

### <a name="how-do-i-configure-media-reserved-units"></a>미디어 예약 단위를 구성하려면 어떻게 할까요?

Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 S3 Mru (미디어 예약 단위) 10 개를 사용 하 여 계정을 프로 비전 하는 것이 좋습니다. 10 개가 넘는 S3 Mru 필요 하면 [Azure Portal](https://portal.azure.com/)를 사용 하 여 지원 티켓을 여세요.

자세한 내용은 [미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조 하세요.

### <a name="what-is-the-recommended-method-to-process-videos"></a>비디오 처리에 권장하는 방법은 무엇입니까?

[Transforms](/rest/api/media/transforms)는 비디오 인코딩 또는 분석에 대한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 변환은 비디오 또는 오디오 파일을 처리하는 작업의 작성법 또는 워크플로를 설명합니다. [작업](/rest/api/media/jobs) 은 입력 비디오 또는 오디오 콘텐츠에 변환을 적용 하는 Media Services에 대 한 실제 요청입니다. 변환을 만든 후에 Media Services Api 또는 게시 된 Sdk 중 하나를 사용 하 여 작업을 제출할 수 있습니다. 자세한 내용은 [Transform 및 Jobs](transforms-jobs-concept.md)를 참조하세요.

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>비디오를 업로드, 인코딩 및 게시합니다. 스트리밍 하려고 할 때 비디오가 재생 되지 않는 이유는 무엇 인가요?

가장 일반적인 이유 중 하나는 재생 중인 상태로 재생 하려는 스트리밍 끝점이 없는 것입니다.

### <a name="how-does-pagination-work"></a>페이지 매김은 어떻게 작동하나요?

페이지 매김을 사용 하는 경우 항상 다음 링크를 사용 하 여 컬렉션을 열거 하 고 특정 페이지 크기에 종속 되지 않아야 합니다. 자세한 내용과 예제는 [필터링, 정렬, 페이징](entities-overview.md)을 참조하세요.

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure Media Services v3에서 아직 사용할 수 없는 기능은 무엇입니까?

자세한 내용은 [V2 api에 대 한 기능 간격](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)을 참조 하세요.

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>구독 간에 Media Services 계정을 이동 하는 프로세스는 무엇 인가요?  

자세한 내용은 [구독 간 Media Services 계정 이동](media-services-account-concept.md)을 참조 하세요.

## <a name="live-streaming"></a>라이브 스트리밍 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>브로드캐스트가 완료 된 후 라이브 스트림을 중지 어떻게 할까요??

클라이언트 쪽 이나 서버 쪽에서 사용할 수 있습니다.

#### <a name="client-side"></a>클라이언트 쪽

웹 응용 프로그램은 브라우저를 닫을 때 브로드캐스트를 종료 하려는 경우 사용자에 게 메시지를 표시 해야 합니다. 웹 응용 프로그램에서 처리할 수 있는 브라우저 이벤트입니다.

#### <a name="server-side"></a>서버 쪽

Azure Event Grid 이벤트를 구독 하 여 라이브 이벤트를 모니터링할 수 있습니다. 자세한 내용은 [Eventgrid 이벤트 스키마](media-services-event-schemas.md#live-event-types)를 참조 하세요.

다음 작업 중 하나를 수행할 수 있습니다.

* 스트림 수준 [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) 이벤트를 [구독](reacting-to-media-services-events.md) 하 고 라이브 이벤트를 중지 하 고 삭제 하는 데 횟수가 제공 되지 않도록 합니다.
* 트랙 수준 [하트 비트](media-services-event-schemas.md#liveeventingestheartbeat) 이벤트를 [구독](reacting-to-media-services-events.md) 합니다. 모든 트랙에서 들어오는 비트 전송률이 0으로 삭제 되거나 마지막 타임 스탬프가 더 이상 늘어나고 있지 않으면 라이브 이벤트를 안전 하 게 종료할 수 있습니다. 하트 비트 이벤트는 모든 트랙에 대해 20 초 마다 표시 되므로 약간 자세한 정보를 얻을 수 있습니다.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>라이브 스트림 중에 어떻게 할까요? 삽입 나누기/비디오 및 이미지 슬레이트?

Media Services v3 라이브 인코딩은 아직 라이브 스트리밍 도중에 비디오 또는 이미지 슬레이트를 삽입하는 기능을 지원하지 않습니다. 

[라이브 온-프레미스 인코더](recommended-on-premises-live-encoders.md)를 사용하여 원본 비디오를 바꿀 수 있습니다. 많은 앱이 Telestream Wirecast, 전환기 Studio (iOS) 및 OBS Studio (무료 앱)를 포함 하 여 소스를 전환할 수 있는 기능을 제공 합니다.

## <a name="content-protection"></a>콘텐츠 보호

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>AES-128 암호화 되지 않은 키 암호화 또는 DRM 시스템을 사용 해야 하나요?

고객들은 종종 AES 암호화 또는 DRM 시스템을 사용해야 할지 여부를 궁금해 합니다. 두 시스템 간의 주요 차이점은 AES 암호화를 사용 하는 경우 키가 전송 중에 암호화 되지만 추가 암호화 ("clear") 없이 암호화 되도록 콘텐츠 키가 TLS를 통해 클라이언트에 전송 된다는 것입니다. 따라서 콘텐츠를 해독 하는 데 사용 되는 키는 클라이언트 플레이어에서 액세스할 수 있으며, 클라이언트의 네트워크 추적에서 일반 텍스트로 볼 수 있습니다. AES-128 암호화되지 않은 키 암호화는 뷰어가 신뢰할 만한 당사자(예: 직원이 볼 수 있도록 회사 내에 배포되는 회사 비디오 암호화)인 사용 사례에 적합합니다.

PlayReady, Widevine FairPlay 등의 DRM 시스템은 모두 AES-128 암호화 되지 않은 키와 비교 하 여 콘텐츠를 암호 해독 하는 데 사용 되는 키에 대 한 추가 수준의 암호화를 제공 합니다. 콘텐츠 키는 TLS에서 제공 하는 전송 수준 암호화 외에 DRM 런타임으로 보호 하는 키로 암호화 됩니다. 또한 암호 해독은 악의적인 사용자가 공격하기에 좀 더 어려운 운영 체제 수준의 보안 환경에서 처리됩니다. 뷰어가 신뢰할 수 있는 파티가 아닐 수 있으며 가장 높은 수준의 보안이 필요한 사용 사례에 대해 DRM을 권장 합니다.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Azure AD를 사용 하지 않고 특정 권한이 있는 사용자 에게만 비디오를 표시 어떻게 할까요??

Azure Active Directory (Azure AD)와 같은 특정 토큰 공급자를 사용할 필요가 없습니다. 비대칭 키 암호화를 사용 하 여 고유한 [JWT](https://jwt.io/) 공급자 (보안 토큰 서비스 또는 STS)를 만들 수 있습니다. 사용자 지정 STS에서 비즈니스 논리를 기준으로 클레임을 추가할 수 있습니다.

발급자, 대상 그룹 및 클레임이 모두 JWT의 내용과에서 사용 되는 값 사이에서 정확 하 게 일치 하는지 확인 합니다 `ContentKeyPolicyRestriction` `ContentKeyPolicy` .

자세한 내용은 [Media Services 동적 암호화를 사용 하 여 콘텐츠 보호](content-protection-overview.md)를 참조 하세요.

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>JWT 토큰을 사용 하 여 라이선스 또는 키를 요청 하기 전에 JWT 토큰을 가져오는 방법 및 위치

프로덕션의 경우 HTTPS 요청 시 JWT 토큰을 발급 하는 보안 토큰 서비스 (즉, 웹 서비스)가 있어야 합니다. 테스트를 위해 `GetTokenAsync` [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)에 정의 된 메서드에 표시 된 코드를 사용할 수 있습니다.

플레이어는 사용자를 인증 한 후 해당 토큰에 대해 STS에 요청 하 고 토큰의 값으로 할당 합니다. [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)를 사용할 수 있습니다.

대칭 키 또는 비대칭 키를 사용 하 여 STS를 실행 하는 예제는 [JWT 도구](https://aka.ms/jwt)를 참조 하세요. 이러한 JWT 토큰을 사용 하 Azure Media Player에 따른 플레이어의 예는 [Azure 미디어 테스트 도구](https://aka.ms/amtest)를 참조 하세요. 토큰 입력을 보려면 **player_settings** 링크를 확장 합니다.

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>AES 암호화를 사용 하 여 비디오를 스트리밍하기 위해 요청에 권한을 부여 어떻게 할까요??

올바른 방법은 보안 토큰 서비스를 사용 하는 것입니다. STS에서 사용자 프로필에 따라 다른 클레임을 추가 합니다 (예: "프리미엄 사용자", "기본 사용자", "무료 평가판 사용자"). JWT에서 서로 다른 클레임을 사용하면 사용자가 각각의 콘텐츠를 볼 수 있습니다. 다른 내용 또는 자산의 경우는 `ContentKeyPolicyRestriction` 해당 값을 갖습니다 `RequiredClaims` .

[이 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)에 나와 있는 것 처럼 라이선스/키 제공을 구성 하 고 자산을 암호화 하는 Azure Media Services api를 사용 합니다.

자세한 내용은 다음을 참조하십시오.

- [콘텐츠 보호 개요](content-protection-overview.md)
- [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>HTTP 또는 HTTPS를 사용 해야 하나요?
ASP.NET MVC 플레이어 애플리케이션은 다음을 지원해야 합니다.

* Azure AD를 통한 사용자 인증이 HTTPS를 사용해야 함
* 클라이언트 및 Azure AD 간 JWT 교환이 HTTPS를 사용해야 함
* Media Services에서 라이선스 배달이 제공된 경우 클라이언트에 의한 DRM 라이선스 획득 시 HTTPS를 사용해야 함. PlayReady 제품군은 라이선스 배달에 대한 HTTPS를 위임하지 않습니다. PlayReady 라이선스 서버가 Media Services 외부에 있는 경우 HTTP 또는 HTTPS를 사용할 수 있습니다.

ASP.NET 플레이어 애플리케이션은 HTTPS를 사용하는 것이 가장 좋으므로 Media Player는 HTTPS를 사용하는 페이지에 있게 됩니다. 그러나 스트리밍의 경우 HTTP를 사용 하는 것이 좋습니다. 따라서 혼합 된 콘텐츠를 사용 하 여 이러한 문제를 고려해 야 합니다.

* 브라우저에서는 혼합 콘텐츠를 허용하지 않습니다. 그러나 Silverlight 및 OSMF 플러그인과 같은 플러그 인은 부드러운 및 대시로 사용할 수 있습니다. 혼합 된 콘텐츠는 악의적인 JavaScript를 삽입 하는 기능을 위협 하 여 고객 데이터를 위험에 노출 시킬 수 있는 위협으로 인해 발생 하는 보안 문제입니다. 브라우저는 기본적으로 이 기능을 차단합니다. 이 문제를 해결하는 유일한 방법이 서버(원본) 쪽에서 HTTPS 또는 HTTP에 관계없이 모든 도메인을 허용하는 것입니다. 하지만 좋은 방법은 아닙니다.
* 혼합 콘텐츠를 피해야 합니다. 플레이어 애플리케이션과 Media Player 둘 다 HTTP 또는 HTTPS를 사용해야 합니다. 혼합 콘텐츠를 재생 하는 경우 SilverlightSS 기술에서는 혼합 된 콘텐츠 경고를 지워야 합니다. Flashss 기술은 tech는 혼합 콘텐츠 경고 없이 혼합 된 콘텐츠를 처리 합니다.
* 스트리밍 엔드포인트가 2014년 8월 전에 만들어진 경우 HTTPS를 지원하지 않습니다. 이 경우 HTTPS에 대한 새 스트리밍 엔드포인트를 만들어 사용하세요.

### <a name="what-about-live-streaming"></a>라이브 스트리밍의 경우는 어떨까요?

정확히 동일한 디자인 및 구현을 사용 하 여 프로그램과 관련 된 자산을 VOD 자산으로 처리 하 여 Media Services에서 라이브 스트리밍을 보호할 수 있습니다. 라이브 콘텐츠의 다중 DRM 보호를 제공 하려면 자산을 라이브 출력과 연결 하기 전에 VOD 자산과 동일한 설정/처리를 자산에 적용 합니다.

### <a name="what-about-license-servers-outside-media-services"></a>Media Services 외부에서 라이선스 서버는 어떨까요?

고객은 종종 자체 데이터 센터에서 또는 DRM 서비스 공급자가 호스팅하는 라이선스 서버 팜에 투자 했습니다. Media Services 콘텐츠 보호를 사용하면 하이브리드 모드로 작업할 수 있습니다. Media Services에서 콘텐츠를 호스트 하 고 동적으로 보호할 수 있는 반면 DRM 라이선스는 Media Services 외부의 서버에서 제공 됩니다. 이 경우 다음과 같은 변경을 고려하세요.

* STS는 라이선스 서버 팜에서 허용되고 확인할 수 있는 토큰을 발급해야 합니다. 예를 들어 Axinom에서 제공하는 Widevine 라이선스 서버에는 자격 메시지를 포함하는 특정 JWT가 필요합니다. 이러한 JWT를 발급 하려면 STS가 있어야 합니다. 
* 더 이상 Media Services에서 라이선스 배달 서비스를 구성하지 않아도 됩니다. 를 구성할 때 라이선스 획득 Url (PlayReady, Widevine 및 용)을 제공 해야 `ContentKeyPolicy` 합니다.

> [!NOTE]
> Widevine는 Google에서 제공 하는 서비스로, Google의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="media-services-v2-vs-v3"></a>Media Services v2와 v3 비교 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure Portal을 사용하여 v3 리소스를 관리할 수 있나요?

현재 [Azure Portal](https://portal.azure.com/) 를 사용 하 여 다음을 수행할 수 있습니다.

* Media Services v3에서 [라이브 이벤트](live-events-outputs-concept.md) 를 관리 합니다. 
* V3 [자산](assets-concept.md)보기 (관리 안 함) 
* [Api에 액세스 하는 방법에 대 한 정보를 가져옵니다](./access-api-howto.md). 

다른 모든 관리 작업 (예: [변환 및 작업](transforms-jobs-concept.md) 및 [콘텐츠 보호](content-protection-overview.md))의 경우 [REST API](/rest/api/media/), [Azure CLI](https://aka.ms/ams-v3-cli-ref)또는 지원 되는 [sdk](media-services-apis-overview.md#sdks)중 하나를 사용 합니다.

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3에 AssetFile 개념이 있나요?

`AssetFile`개념은 저장소 SDK 종속성에서 Media Services를 분리 하기 위해 MEDIA SERVICES API에서 제거 되었습니다. 이제 Media Services 하지 않고 Azure Storage는 저장소 SDK에 속하는 정보를 유지 합니다. 

자세한 내용은 [Media Services v3로 마이그레이션](media-services-v2-vs-v3.md)을 참조하세요.

### <a name="where-did-client-side-storage-encryption-go"></a>클라이언트 쪽 스토리지 암호화는 어디에 있나요?

이제 서버 쪽 저장소 암호화 (기본적으로 설정 됨)를 사용 하는 것이 좋습니다. 자세한 내용은 [미사용 데이터에 대 한 서비스 암호화 Azure Storage](../../storage/common/storage-service-encryption.md)를 참조 하세요.

## <a name="offline-streaming"></a>오프 라인 스트리밍

### <a name="fairplay-streaming-for-ios"></a>IOS에 대 한 FairPlay 스트리밍

다음 질문과 대답은 iOS 용 오프 라인 FairPlay 스트리밍 문제 해결에 대 한 지원을 제공 합니다.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>왜 오프라인 모드에서는 오디오만 재생되고 비디오는 재생되지 않나요?

이 동작은 의도적으로 샘플 앱의 것입니다. 오프 라인 모드에서 대체 오디오 트랙이 있는 경우 (HLS의 경우) iOS 10과 iOS 11은 모두 대체 오디오 트랙을 기본값으로 설정 합니다. FPS 오프 라인 모드에 대 한이 동작을 보완 하려면 스트림에서 대체 오디오 트랙을 제거 합니다. Media Services에서이 작업을 수행 하려면 동적 매니페스트 필터 **오디오 전용 = false**를 추가 합니다. 즉, HLS URL은 **. p s/매니페스트 (format = m3u8-aapl-v3-aapl, audio 전용 = false)** 로 끝납니다. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>audio-only=false를 추가한 후에도 왜 여전히 오프라인 모드에서 동영상 없이 오디오만 재생되나요?

콘텐츠 배달 네트워크에 대 한 캐시 키 디자인에 따라 콘텐츠가 캐시 될 수 있습니다. 캐시를 제거합니다.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Ios 10 외에도 iOS 11에서 FPS 오프 라인 모드가 지원 되나요?

예. FPS 오프라인 모드는 iOS 10과 iOS 11 모두에서 지원됩니다.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>FPS Server SDK에서 “FairPlay 스트리밍 및 HTTP 라이브 스트리밍을 사용하여 오프라인 재생” 문서를 찾을 수 없는 이유는 무엇인가요?

FPS Server SDK 버전 4부터 이 문서는 “FairPlay Streaming Programming Guide”에 병합되었습니다.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>iOS 디바이스에서 다운로드된/오프라인 파일 구조체는 무엇입니까?

iOS 디바이스에 다운로드된 파일 구조체는 다음 스크린샷과 같습니다. `_keys` 폴더에는 다운로드된 FPS 라이선스가 저징됩니다(각 라이선스 서비스 호스트당 하나의 저장소 파일). `.movpkg` 폴더에는 오디오 및 동영상 콘텐츠가 저장됩니다. 

대시 뒤에 숫자가 오는 이름의 첫 번째 폴더는 동영상 콘텐츠를 포함 합니다. 숫자 값은 비디오 변환의 최고 대역폭입니다. 대시에 이어 0으로 끝나는 이름의 두 번째 폴더는 오디오 콘텐츠를 포함합니다. 이라는 세 번째 폴더에는 `Data` FPS 콘텐츠의 마스터 재생 목록이 포함 되어 있습니다. 마지막으로, boot.xml은 `.movpkg` 폴더 내용에 대한 전체 설명을 제공합니다. 

![FairPlay iOS 샘플 앱에 대 한 오프 라인 파일 구조](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

샘플 boot.xml 파일은 다음과 같습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Android 용 widevine 스트리밍

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>일부 클라이언트/사용자 및 비영구 라이선스 (오프 라인 사용 안 함)에 대해 영구 라이선스 (오프 라인 사용)를 제공 하려면 어떻게 해야 하나요? 콘텐츠를 복제 하 고 별도의 콘텐츠 키를 사용 해야 하나요?

Media Services v 3에서 여러 인스턴스를 포함 하도록 허용 하기 때문에 `StreamingLocator` 다음을 수행할 수 있습니다.

* 에서 `ContentKeyPolicy` 클레임이 있는 `license_type = "persistent"` `ContentKeyPolicyRestriction` `"persistent"` 및 해당의 `StreamingLocator` 인스턴스 하나
* 및가 인 다른 `ContentKeyPolicy` 인스턴스 `license_type="nonpersistent"` `ContentKeyPolicyRestriction` `"nonpersistent` 및 `StreamingLocator`
* `StreamingLocator`값이 서로 다른 두 인스턴스입니다 `ContentKey` .

사용자 지정 STS의 비즈니스 논리에 따라 JWT 토큰에서 다른 클레임이 발급됩니다. 이 토큰을 사용하여 해당 라이선스만 가져올 수 있고 해당 URL만 재생할 수 있습니다.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Widevine와 Media Services DRM 보안 수준 간의 매핑은 무엇입니까?

Google의 "Widevine DRM 아키텍처 개요"는 세 가지 보안 수준을 정의 합니다. 그러나 [Widevine 라이선스 템플릿에 대 한 Azure Media Services 설명서](widevine-license-template-overview.md) 는 5 가지 보안 수준 (재생을 위한 클라이언트 견고성 요구 사항)을 간략하게 설명 합니다. 이 섹션에서는 보안 수준을 매핑하는 방법에 대해 설명 합니다.

두 보안 수준 집합은 Google Widevine 통해 정의 됩니다. 차이점은 사용 수준: 아키텍처 또는 API입니다. 5가지 보안 수준은 Widevine API에서 사용됩니다. `content_key_specs`을 포함 하는 개체는 `security_level` deserialize 되어 Azure Media Services widevine 라이선스 서비스에 의해 widevine 글로벌 배달 서비스로 전달 됩니다. 다음 표에서는 두 보안 수준 집합 간의 매핑을 보여 줍니다.

| **Widevine 아키텍처에 정의 된 보안 수준** |**Widevine API에서 사용 되는 보안 수준**|
|---|---| 
| **보안 수준 1**: 모든 콘텐츠 처리, 암호화 및 제어는 신뢰할 수 있는 실행 환경 (티) 내에서 수행 됩니다. 일부 구현 모델에서는 다양 한 칩에서 보안 처리를 수행할 수 있습니다.|**security_level = 5**: 미디어의 암호화, 디코딩 및 모든 처리 (압축 및 압축 해제)는 하드웨어 지원 티에서 처리 되어야 합니다.<br/><br/>**security_level = 4**: 하드웨어 지원 티에서 콘텐츠의 암호화 및 디코딩을 수행 해야 합니다.|
**보안 수준 2**: 암호화 (비디오 처리는 아님)는 티에서 수행 됩니다. 해독 된 버퍼는 응용 프로그램 도메인으로 반환 되 고 별도의 비디오 하드웨어 또는 소프트웨어를 통해 처리 됩니다. 그러나 수준 2에서 암호화 정보는 여전히 티 티에서 처리 됩니다.| **security_level = 3**: 하드웨어 지원 티에서 키 자료 및 암호화 작업을 수행 해야 합니다. |
| **보안 수준 3**: 장치에는 티가 없습니다. 호스트 운영 체제에서 암호화 정보 및 암호 해독 된 콘텐츠를 보호 하기 위해 적절 한 조치를 취할 수 있습니다. 수준 3 구현에는 하드웨어 암호화 엔진도 포함 될 수 있지만 보안이 아닌 성능도 향상 됩니다. | **security_level = 2**: 소프트웨어 암호화 및 난독 처리 된 디코더가 필요 합니다.<br/><br/>**security_level = 1**: 소프트웨어 기반 백서 암호화가 필요 합니다.|

#### <a name="why-does-content-download-take-so-long"></a>콘텐츠 다운로드 시간이 너무 오래 걸리는 이유는 무엇인가요?

다운로드 속도를 개선하는 방법으로는 다음 두 가지가 있습니다.

* 콘텐츠 다운로드를 위해 원본/스트리밍 끝점 대신 사용자가 적중 가능성이 더 높은 콘텐츠 배달 네트워크를 사용 하도록 설정 합니다. 사용자가 스트리밍 끝점에 도달 하면 각 HLS 세그먼트 또는 대시 조각이 동적으로 패키지 되 고 암호화 됩니다. 이 대기 시간이 각 세그먼트 또는 조각에 대 한 밀리초 단위로 표시 되는 경우에도 시간이 오래 걸리는 비디오를 사용할 경우 누적 된 대기 시간이 클 수 있으며 다운로드가 길어질 수 있습니다.
* 모든 콘텐츠가 아닌 비디오 품질 계층 및 오디오 트랙을 선택적으로 다운로드 하는 옵션을 사용자에 게 제공 합니다. 오프 라인 모드의 경우 모든 품질 계층을 다운로드할 필요가 없습니다. 두 가지 방법으로 이 작업을 수행할 수 있습니다.

  * 클라이언트 제어: 플레이어 앱은 다운로드할 비디오 품질 계층과 오디오 트랙을 자동으로 선택 하거나 선택 합니다.
  * 서비스 제어: Azure Media Services의 동적 매니페스트 기능을 사용 하 여 HLS 재생 목록이 나 대시 MPD 단일 비디오 품질 계층 및 선택한 오디오 트랙으로 제한 하는 (전역) 필터를 만들 수 있습니다. 그러면 사용자에 게 제공 된 다운로드 URL에이 필터가 포함 됩니다.

## <a name="next-steps"></a>다음 단계

[Media Services v3 개요](media-services-overview.md)
