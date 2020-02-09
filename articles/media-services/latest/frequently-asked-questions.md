---
title: Azure Media Services v3 질문과 대답 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v3 질문과 대답을 제공 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: c9da29ad288811bbed225fd906f2a7eb1fd9edf7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977729"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 질문과 대답

이 문서에서는 AMS (Azure Media Services) v3 질문과 대답을 제공 합니다.

## <a name="general"></a>Általános

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure Media Services 리소스에 대 한 작업을 수행할 수 있는 Azure 역할은 무엇 인가요? 

[Media Services 계정에 대 한 RBAC (역할 기반 액세스 제어)를](rbac-overview.md)참조 하세요.

### <a name="how-do-i-configure-media-reserved-units"></a>미디어 예약 단위를 구성 어떻게 할까요??

A Media Services v3 vagy a Video Indexer által aktivált hangelemzési és videoelemzési feladatok esetében javasolt 10 S3 MRU-val ellátni a fiókot. 10 개가 넘는 S3 Mru 필요 하면 [Azure Portal](https://portal.azure.com/)를 사용 하 여 지원 티켓을 여세요.

자세한 내용은 [CLI를 사용 하 여 미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조 하세요.

### <a name="what-is-the-recommended-method-to-process-videos"></a>비디오를 처리 하는 데 권장 되는 방법은 무엇 인가요?

[변환을](https://docs.microsoft.com/rest/api/media/transforms) 사용 하 여 비디오를 인코딩 또는 분석 하기 위한 일반 작업을 구성 합니다. 각 **변환은** 비디오 또는 오디오 파일을 처리 하기 위한 조리법 또는 작업 워크플로를 설명 합니다. [작업](https://docs.microsoft.com/rest/api/media/jobs) 은 지정 된 입력 비디오 또는 오디오 콘텐츠에 **변환을** 적용 하는 Media Services에 대 한 실제 요청입니다. 변환을 만든 후에 Media Services Api 또는 게시 된 Sdk 중 하나를 사용 하 여 작업을 제출할 수 있습니다. További információt az [átalakításokkal és feladatokkal](transforms-jobs-concept.md) kapcsolatos cikkben olvashat.

### <a name="how-does-pagination-work"></a>페이지 매김을 어떻게 작동 하나요?

페이지 매김을 사용 하는 경우 항상 다음 링크를 사용 하 여 컬렉션을 열거 하 고 특정 페이지 크기에 종속 되지 않아야 합니다. 자세한 내용과 예제는 [필터링, 정렬, 페이징](entities-overview.md)을 참조 하세요.

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure Media Services v3에서 아직 사용할 수 없는 기능은 무엇입니까?

자세한 내용은 [V2 api에 대 한 기능 간격](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis)을 참조 하세요.

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>구독 간에 Media Services 계정을 이동 하는 프로세스는 무엇 인가요?  

자세한 내용은 [구독 간 Media Services 계정 이동](media-services-account-concept.md)을 참조 하세요.

## <a name="live-streaming"></a>Live streaming (Élő adatfolyam) 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>라이브 스트림 중에 나누기/비디오 및 이미지 슬레이트 삽입 하는 방법

Media Services v3 라이브 인코딩은 라이브 스트림 중에 video 또는 image 슬레이트 삽입을 아직 지원 하지 않습니다. 

[라이브 온-프레미스 인코더](recommended-on-premises-live-encoders.md) 를 사용 하 여 원본 비디오를 전환할 수 있습니다. 많은 앱이 Telestream Wirecast, 전환기 Studio (iOS), OBS Studio (무료 앱) 등을 포함 하 여 소스를 전환할 수 있는 기능을 제공 합니다.

## <a name="content-protection"></a>Tartalomvédelem

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>AES-128 암호화 되지 않은 키 암호화 또는 DRM 시스템을 사용 해야 하나요?

고객은 종종 AES 암호화 또는 DRM 시스템을 사용 해야 하는지 여부를 궁금해 합니다. 두 시스템 간의 주요 차이점은 AES 암호화를 사용 하는 경우 키가 전송 중에 암호화 되지만 추가 암호화 ("clear") 없이 암호화 되도록 콘텐츠 키가 TLS를 통해 클라이언트에 전송 된다는 것입니다. 따라서 콘텐츠를 해독 하는 데 사용 되는 키는 클라이언트 플레이어에서 액세스할 수 있으며, 클라이언트의 네트워크 추적에서 일반 텍스트로 볼 수 있습니다. AES-128 암호화 되지 않은 키 암호화는 뷰어가 신뢰할 수 있는 당사자 (예: 직원이 볼 수 있도록 회사 내에 배포 되는 회사 비디오 암호화) 인 사용 사례에 적합 합니다.

PlayReady, Widevine 및와 같은 DRM 시스템은 모두 AES-128 암호화 되지 않은 키와 비교 하 여 콘텐츠를 암호 해독 하는 데 사용 되는 키에 대 한 추가 수준의 암호화를 제공 합니다. 콘텐츠 키는 TLS에서 제공 하는 전송 수준 암호화에 추가 하 여 DRM 런타임에 의해 보호 되는 키로 암호화 됩니다. 또한 암호 해독은 악의적인 사용자가 공격 하기 어려운 운영 체제 수준의 보안 환경에서 처리 됩니다. DRM은 뷰어가 신뢰할 수 있는 파티가 아닌 경우 가장 높은 수준의 보안이 필요한 사용 사례에 권장 됩니다.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Azure AD를 사용 하지 않고 특정 권한이 있는 사용자 에게만 비디오를 표시 하는 방법

특정 토큰 공급자 (예: Azure AD)를 사용할 필요가 없습니다. 비대칭 키 암호화를 사용 하 여 사용자 고유의 [JWT](https://jwt.io/) 공급자 (따라서 STS, 보안 토큰 서비스)를 만들 수 있습니다. 사용자 지정 STS에서 비즈니스 논리를 기준으로 클레임을 추가할 수 있습니다.

발급자, 대상 그룹 및 클레임이 모두 JWT와 ContentKeyPolicy에서 사용 되는 ContentKeyPolicyRestriction 사이에서 정확 하 게 일치 하는지 확인 합니다.

자세한 내용은 [Media Services 동적 암호화를 사용 하 여 콘텐츠 보호](content-protection-overview.md)를 참조 하세요.

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>JWT 토큰을 사용 하 여 라이선스 또는 키를 요청 하기 전에 JWT 토큰을 가져오는 방법 및 위치

1. 프로덕션의 경우 HTTPS 요청 시 JWT 토큰을 발급 하는 STS (보안 토큰 서비스) (웹 서비스)가 있어야 합니다. 테스트의 경우 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)에 정의 된 **GetTokenAsync** 메서드에 표시 된 코드를 사용할 수 있습니다.
2. 플레이어는 사용자를 인증 한 후 해당 토큰에 대 한 STS에 요청 하 고 토큰의 값으로 할당 해야 합니다. [AZURE MEDIA PLAYER API](https://amp.azure.net/libs/amp/latest/docs/)를 사용할 수 있습니다.

* 대칭 키와 비대칭 키 중 하나를 사용 하 여 STS를 실행 하는 예제는 [https://aka.ms/jwt](https://aka.ms/jwt)를 참조 하세요. 
* 이러한 JWT 토큰을 사용 하 Azure Media Player을 기반으로 하는 플레이어의 예는 [https://aka.ms/amtest](https://aka.ms/amtest) (토큰 입력을 보려면 "player_settings" 링크 확장)를 참조 하세요.

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>AES 암호화를 사용 하 여 비디오를 스트리밍하기 위해 요청에 권한을 부여 하려면 어떻게 해야 하나요?

올바른 방법은 STS (보안 토큰 서비스)를 활용 하는 것입니다.

STS에서 사용자 프로필에 따라 다른 클레임 (예: "프리미엄 사용자", "기본 사용자", "무료 평가판 사용자")을 추가 합니다. JWT에서 다른 클레임을 사용 하는 경우 사용자는 다른 콘텐츠를 볼 수 있습니다. 물론 다른 콘텐츠/자산에 대해 ContentKeyPolicyRestriction에는 해당 RequiredClaims 포함 됩니다.

[이 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)에 나와 있는 것 처럼 라이선스/키 제공을 구성 하 고 자산을 암호화 하는 Azure Media Services api를 사용 합니다.

További információ eléréséhez lásd:

- [A tartalomvédelem áttekintése](content-protection-overview.md)
- [Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP 또는 HTTPS?
ASP.NET MVC player 응용 프로그램은 다음을 지원 해야 합니다.

* HTTPS를 사용 하는 Azure AD를 통한 사용자 인증.
* 클라이언트와 Azure AD 간의 JWT 교환 (HTTPS)
* 클라이언트에의 한 DRM 라이선스 획득 (Media Services에서 라이선스 배달이 제공 되는 경우 HTTPS 미만 이어야 함) PlayReady 제품군은 라이선스 배달에 대해 HTTPS를 요구 하지 않습니다. PlayReady 라이선스 서버가 Media Services 외부에 있는 경우 HTTP 또는 HTTPS 중 하나를 사용할 수 있습니다.

ASP.NET player 응용 프로그램은 https를 사용 하 여 https를 사용 하는 것이 좋습니다. 따라서 Media Player는 HTTPS의 페이지에 있습니다. 그러나 스트리밍의 경우 HTTP를 선호 하므로 혼합 된 콘텐츠의 문제를 고려해 야 합니다.

* 브라우저에서는 혼합 콘텐츠를 허용 하지 않습니다. 그러나 Silverlight 및 OSMF 플러그인과 같은 플러그 인은 부드러운 및 대시로 사용할 수 있습니다. 혼합 된 콘텐츠는 악의적인 JavaScript를 삽입 하는 기능을 위협 하 여 고객 데이터가 위험 해질 수 있기 때문에 보안 문제가 됩니다. 브라우저는 기본적으로이 기능을 차단 합니다. 이 문제를 해결 하는 유일한 방법은 서버 (원본) 쪽에서 HTTPS 또는 HTTP에 관계 없이 모든 도메인을 허용 하는 것입니다. 이는 바람직하지 않을 수 있습니다.
* 혼합 콘텐츠를 사용 하지 않습니다. 플레이어 응용 프로그램과 Media Player 모두 HTTP 또는 HTTPS를 사용 해야 합니다. 혼합 콘텐츠를 재생할 때 silverlightSS 기술에서는 혼합 콘텐츠 경고를 지워야 합니다. Flashss 기술은 tech는 혼합 콘텐츠 경고 없이 혼합 된 콘텐츠를 처리 합니다.
* 스트리밍 엔드포인트이 2014 년 8 월 이전에 만들어진 경우 HTTPS를 지원 하지 않습니다. 이 경우 HTTPS에 대 한 새 스트리밍 엔드포인트을 만들어 사용 합니다.

### <a name="what-about-live-streaming"></a>라이브 스트리밍에 대 한 자세한 정보

프로그램과 연결 된 자산을 VOD 자산으로 처리 하 여 Media Services에서 라이브 스트리밍을 보호 하는 데 정확히 동일한 디자인 및 구현을 사용할 수 있습니다. 라이브 콘텐츠의 다중 DRM 보호를 제공 하려면 자산을 라이브 출력과 연결 하기 전에 VOD 자산과 동일한 설정/처리를 자산에 적용 합니다.

### <a name="what-about-license-servers-outside-media-services"></a>Media Services 외부 라이선스 서버는 어떻게 되나요?

고객은 종종 자체 데이터 센터 또는 DRM 서비스 공급자가 호스트 하는 라이선스 서버 팜에 투자 합니다. Media Services 콘텐츠 보호를 사용 하면 하이브리드 모드로 작업할 수 있습니다. Media Services에서 콘텐츠를 호스트 하 고 동적으로 보호할 수 있는 반면 DRM 라이선스는 Media Services 외부의 서버에서 제공 됩니다. 이 경우 다음 사항을 고려해 야 합니다.

* STS는 라이선스 서버 팜에서 허용 되 고 확인할 수 있는 토큰을 발급 해야 합니다. 예를 들어 Axinom에서 제공 하는 Widevine 라이선스 서버에는 자격 메시지를 포함 하는 특정 JWT가 필요 합니다. 따라서 이러한 JWT를 발급 하는 STS가 있어야 합니다. 
* 더 이상 Media Services에서 라이선스 배달 서비스를 구성할 필요가 없습니다. Contentkeypolicy를 구성할 때 라이선스 획득 Url (PlayReady, Widevine 및 용)을 제공 해야 합니다.

> [!NOTE]
> Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure Portal를 사용 하 여 v3 리소스를 관리할 수 있나요?

Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>V3에 AssetFile 개념이 있나요?

AssetFiles는 저장소 SDK 종속성에서 Media Services를 분리 하기 위해 AMS API에서 제거 되었습니다. 이제 Media Services 아닌 저장소에는 저장소에 속하는 정보가 보관 됩니다. 

자세한 내용은 [Media Services v3로 마이그레이션](migrate-from-v2-to-v3.md)을 참조 하세요.

### <a name="where-did-client-side-storage-encryption-go"></a>클라이언트 쪽 저장소 암호화는 어디에 있나요?

이제 서버 쪽 저장소 암호화 (기본적으로 설정 됨)를 사용 하는 것이 좋습니다. 자세한 내용은 [미사용 데이터에 대 한 서비스 암호화 Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)를 참조 하세요.

## <a name="next-steps"></a>Következő lépések

[Media Services v3 개요](media-services-overview.md)
