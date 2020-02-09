---
title: Azure Media Services를 사용 하 여 액세스 제어를 사용 하는 콘텐츠 보호 시스템 디자인 Microsoft Docs
description: Microsoft 부드러운 스트리밍 클라이언트 포팅 키트의 라이선스를 사용 하는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.openlocfilehash: b0fec44a59bd70c6f1d0236861d93e81aaba033c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969448"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Azure Media Services를 사용 하 여 액세스 제어를 사용 하는 콘텐츠 보호 시스템 디자인 

## <a name="overview"></a>Áttekintés

최신 (고급) 또는 온라인 스트리밍 솔루션에 대 한 DRM (디지털 권한 관리) 하위 시스템을 설계 하 고 구축 하는 작업은 복잡 한 작업입니다. 일반적으로 운영자/온라인 비디오 공급자는이 작업을 전문화 된 DRM 서비스 공급자에 게 아웃소싱 합니다. 이 문서의 목표는 이상 또는 온라인 스트리밍 솔루션에서 종단 간 DRM 하위 시스템의 참조 디자인 및 구현을 제공 하는 것입니다.

이 문서에 대 한 대상 독자는 DRM 하위 시스템 또는 온라인 스트리밍/멀티 스크린 솔루션의 DRM 하위 시스템에서 작업 하는 엔지니어 이거나 DRM 하위 시스템에 관심이 있는 독자입니다. 독자는 PlayReady, Widevine, Adobe Access 등 시장에서 하나 이상의 DRM 기술에 대해 잘 알고 있는 것으로 가정 합니다.

이 DRM의 설명에는 다중 DRM을 사용 하는 CENC (일반 암호화)도 포함 되어 있습니다. 온라인 스트리밍 및 이상 버전의 주요 추세는 다양 한 클라이언트 플랫폼에서 다중 기본 DRM으로 CENC를 사용 하는 것입니다. 이러한 추세는 다양 한 클라이언트 플랫폼에서 단일 DRM과 해당 클라이언트 SDK를 사용 하는 이전 항목 으로부터의 이동입니다. 다중 원시 DRM으로 CENC를 사용 하는 경우 PlayReady 및 Widevine [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) 사양에 따라 암호화 됩니다.

다중 DRM을 사용 하는 CENC의 이점은 다음과 같습니다.

* 는 단일 암호화 프로세스를 사용 하 여 네이티브 DRMs를 사용 하는 다른 플랫폼을 대상으로 하므로 암호화 비용이 줄어듭니다.
* 암호화 된 자산의 복사본이 하나만 필요 하므로 암호화 된 자산의 관리 비용이 절감 됩니다.
* 기본 플랫폼에서 기본 DRM 클라이언트는 일반적으로 무료로 제공 되므로 DRM 클라이언트 라이선스 비용이 제거 됩니다.

Microsoft는 몇 가지 주요 업계 플레이어와 함께 대시 및 CENC의 활성 프로모터. Azure Media Services는 대시 및 CENC를 지원 합니다. 최근 공지 사항은 다음 블로그를 참조 하세요.

*  [Announcing Google Widevine license delivery services in Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/) (A Google Widevine-licenctovábbítási szolgáltatás megjelenése az Azure Media Services-ben)
* [다중 DRM 스트림을 제공 하기 위해 Google Widevine 패키징을 추가 Azure Media Services](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>문서 목표

이 문서의 목표는 다음과 같습니다.

* 다중 DRM으로 CENC를 사용 하는 DRM 하위 시스템의 참조 디자인을 제공 합니다.
* Azure/Media Services 플랫폼에 대 한 참조 구현을 제공 합니다.
* 몇 가지 디자인 및 구현 항목에 대해 설명 합니다.

이 문서에서 "다중 DRM" 이라는 용어는 다음 제품을 포함 합니다.

* Microsoft PlayReady
* Google widev
* Apple FairPlay 

다음 표에서는 각 DRM에서 지원 되는 네이티브 플랫폼/네이티브 앱 및 브라우저를 요약 합니다.

| **클라이언트 플랫폼** | **기본 DRM 지원** | **브라우저/앱** | **스트리밍 형식** |
| --- | --- | --- | --- |
| **스마트 Tv, 운영자 STBs, 고 대** |PlayReady 주로, 및/또는 Widevine 및/또는 기타 |Linux, Opera, WebKit, 기타 |다양 한 형식 |
| **Windows 10 장치 (Windows PC, Windows 태블릿, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Univerzális Windows-platform |대시 (HLS의 경우 PlayReady는 지원 되지 않음)<br/><br/>대시, 부드러운 스트리밍 (HLS의 경우 PlayReady는 지원 되지 않음) |
| **Android 장치 (휴대폰, 태블릿, TV)** |Widevine |Chrome/EME |대시, HLS |
| **iOS (iPhone, iPad), OS X 클라이언트 및 Apple TV** |FairPlay |Safari 8 +/EME |HLS |

각 DRM에 대 한 배포의 현재 상태를 고려 하 여 서비스는 일반적으로 모든 유형의 엔드포인트을 가장 적합 한 방식으로 처리할 수 있도록 2 개 또는 3 개의 DRMs를 구현 하려고 합니다.

다양 한 클라이언트에서 특정 수준의 사용자 환경에 도달 하기 위해 서비스 논리의 복잡성과 클라이언트 쪽의 복잡성 간에 균형을 유지 합니다.

선택 하려면 다음 사항을 염두에 두어야 합니다.

* PlayReady는 모든 Windows 장치에서 고유 하 게 구현 되며 일부 Android 장치에서는 거의 모든 플랫폼에서 소프트웨어 Sdk를 통해 사용할 수 있습니다.
* Widevine는 모든 Android 장치, Chrome 및 일부 다른 장치에서 고유 하 게 구현 됩니다.
* FairPlay는 iOS 및 Mac OS 클라이언트에서 또는 iTunes를 통해 사용할 수 있습니다.

일반적인 다중 DRM에 대 한 두 가지 옵션이 있습니다.

* PlayReady 및 widev
* PlayReady, Widevine FairPlay

## <a name="a-reference-design"></a>참조 디자인
이 섹션에서는이를 구현 하는 데 사용 되는 기술과 무관 한 참조 디자인을 제공 합니다.

DRM 하위 시스템은 다음 구성 요소를 포함할 수 있습니다.

* Kulcskezelés
* DRM 패키징
* DRM-licenckézbesítés
* 권한 확인
* 인증/권한 부여
* Lejátszó
* 원본/CDN (content delivery network)

다음 다이어그램에서는 DRM 하위 시스템의 구성 요소 간 높은 수준의 상호 작용을 보여 줍니다.

![CENC를 사용 하는 DRM 하위 시스템](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

디자인에는 세 가지 기본 계층이 있습니다.

* 백 오피스 계층 (검정)은 외부적으로 노출 되지 않습니다.
* DMZ 계층 (진한 파랑)에는 공용으로 향하는 모든 엔드포인트이 포함 됩니다.
* 공용 인터넷 계층 (연한 파랑)에는 공용 인터넷을 통해 전송 되는 CDN 및 플레이어가 포함 되어 있습니다.

또한 정적 또는 동적 암호화에 관계 없이 DRM 보호를 제어 하기 위한 콘텐츠 관리 도구가 있어야 합니다. DRM 암호화에 대 한 입력은 다음과 같습니다.

* MBR 비디오 콘텐츠
* 콘텐츠 키
* 라이선스 획득 Url

재생 시간 동안의 상위 수준 흐름은 다음과 같습니다.

* 사용자가 인증 됩니다.
* 사용자에 대 한 권한 부여 토큰이 만들어집니다.
* DRM으로 보호 된 콘텐츠 (매니페스트)가 플레이어에 다운로드 됩니다.
* 플레이어는 라이선스 서버에 키 ID 및 권한 부여 토큰과 함께 라이선스 획득 요청을 제출 합니다.

다음 섹션에서는 키 관리의 디자인에 대해 설명 합니다.

| **ContentKey-자산** | **Forgatókönyv** |
| --- | --- |
| 1-1 |가장 간단한 경우입니다. 가장 세밀 한 제어를 제공 합니다. 그러나 이러한 정렬은 일반적으로 가장 높은 라이선스 배달 비용이 발생 합니다. 보호 된 각 자산에는 최소한 하나의 라이선스 요청이 필요 합니다. |
| 일대다 |여러 자산에 대해 동일한 콘텐츠 키를 사용할 수 있습니다. 예를 들어 장르 또는 장르의 하위 집합 (또는 영화 gene)과 같은 논리적 그룹의 모든 자산에 대해 단일 콘텐츠 키를 사용할 수 있습니다. |
| 다 대-1 |각 자산에는 여러 콘텐츠 키가 필요 합니다. <br/><br/>예를 들어 MPEG에 대해 다중 DRM을 사용 하 여 동적 CENC 보호를 적용 해야 하는 경우 HLS에 대 한 동적 AES-128 암호화에는 두 가지 별도의 콘텐츠 키가 필요 합니다. 각 콘텐츠 키에는 고유한 ContentKeyType이 필요 합니다. (동적 CENC 보호에 사용 되는 콘텐츠 키의 경우 ContentKeyType. CommonEncryption을 사용 합니다. 동적 AES-128 암호화에 사용 되는 콘텐츠 키의 경우 ContentKeyType. EnvelopeEncryption를 사용 합니다.<br/><br/>또 다른 예로, 이론의 CENC 보호에서 한 콘텐츠 키를 사용 하 여 비디오 스트림을 보호 하 고 오디오 스트림을 보호 하는 다른 콘텐츠 키를 사용할 수 있습니다. |
| Több-a-többhöz |이전 두 시나리오의 조합입니다. 동일한 자산 그룹의 여러 자산 각각에 대해 하나의 콘텐츠 키 집합이 사용 됩니다. |

고려해 야 할 또 다른 중요 한 요소는 영구 및 비영구 라이선스를 사용 하는 것입니다.

이러한 고려 사항이 중요 한 이유는 무엇 인가요?

라이선스 배달에 공용 클라우드를 사용 하는 경우 영구 및 비영구 라이선스는 라이선스 배달 비용에 직접적인 영향을 줍니다. 다음 두 가지 다른 디자인 사례를 보여 줍니다.

* 월간 구독: 영구 라이선스와 일대다 콘텐츠 키-자산 매핑을 사용 합니다. 예를 들어 모든 어린이 영화에 대해 암호화에 단일 콘텐츠 키를 사용 합니다. 이 경우:

    모든 어린이 영화/장치에 대해 요청 된 총 라이선스 수 = 1

* 월간 구독: 콘텐츠 키와 자산 간에 비영구 라이선스 및 일대일 매핑을 사용 합니다. 이 경우:

    모든 어린이 영화/장치에 대해 요청 된 총 라이선스 수 = [시청 중인 영화 수] x [세션 수]

두 가지 다른 디자인으로 인해 라이선스 요청 패턴이 매우 다릅니다. Media Services와 같은 공용 클라우드에서 라이선스 배달 서비스를 제공 하는 경우 여러 가지 패턴으로 인해 라이선스 배달 비용이 달라 집니다.

## <a name="map-design-to-technology-for-implementation"></a>디자인을 구현 기술에 매핑
그런 다음, 각 구성 요소에 사용할 기술을 지정 하 여 제네릭 디자인이 Azure/Media Services 플랫폼의 기술에 매핑됩니다.

다음 표에서는 매핑을 보여 줍니다.

| **빌딩 블록** | **Technológia** |
| --- | --- |
| **Lejátszó** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **IDP (id 공급자)** |Azure Active Directory (Azure AD) |
| **STS (보안 토큰 서비스)** |Azure AD |
| **DRM 보호 워크플로** |Media Services 동적 보호 |
| **DRM-licenckézbesítés** |* Media Services 라이선스 배달 (PlayReady, Widevine, FairPlay) <br/>* Axinom 라이선스 서버 <br/>* 사용자 지정 PlayReady 라이선스 서버 |
| **원본** |Media Services 스트리밍 엔드포인트 |
| **Kulcskezelés** |참조 구현에는 필요 하지 않습니다. |
| **Tartalomkezelés** |C# 콘솔 응용 프로그램 |

즉, IDP 및 STS는 모두 Azure AD와 함께 사용 됩니다. 플레이어에 [AZURE MEDIA PLAYER API](https://amp.azure.net/libs/amp/latest/docs/) 가 사용 됩니다. Media Services 및 Media Player는 모두 다중 DRM을 사용 하는 대시 및 CENC를 지원 합니다.

다음 다이어그램에서는 이전 기술 매핑과 함께 전체 구조와 흐름을 보여 줍니다.

![Media Services의 CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

동적 CENC 암호화를 설정 하기 위해 콘텐츠 관리 도구는 다음 입력을 사용 합니다.

* 콘텐츠 열기
* 키 생성/관리에서 콘텐츠 키
* 라이선스 획득 Url
* Azure AD의 정보 목록

콘텐츠 관리 도구의 출력은 다음과 같습니다.

* ContentKeyAuthorizationPolicy는 라이선스 배달이 JSON Web Token (JWT) 및 DRM 라이선스 사양을 확인 하는 방법에 대 한 사양을 포함 합니다.
* Asset배달 Ypolicy는 스트리밍 형식, DRM 보호 및 라이선스 취득 Url에 대 한 사양을 포함 합니다.

런타임 중의 흐름은 다음과 같습니다.

* 사용자 인증 시 JWT가 생성 됩니다.
* JWT에 포함 된 클레임 중 하나는 그룹 개체 ID EntitledUserGroup를 포함 하는 그룹 클레임입니다. 이 클레임은 자격 검사를 전달 하는 데 사용 됩니다.
* 플레이어는 CENC로 보호 된 콘텐츠의 클라이언트 매니페스트를 다운로드 하 고 다음을 식별 합니다.
   * 키 ID입니다.
   * 콘텐츠는 CENC로 보호 됩니다.
   * 라이선스 취득 Url.
* 플레이어는 지원 되는 브라우저/a s e를 기반으로 라이선스 취득 요청을 만듭니다. 라이선스 획득 요청에서 키 ID 및 JWT도 제출 됩니다. 라이선스 배달 서비스는 필요한 라이선스를 발급 하기 전에 JWT 및 클레임에 포함 된 클레임을 확인 합니다.

## <a name="implementation"></a>Megvalósítás
### <a name="implementation-procedures"></a>구현 절차
구현에는 다음 단계가 포함 됩니다.

1. 테스트 자산을 준비 합니다. Media Services에서 테스트 비디오를 다중 비트 전송률 조각화 된 MP4로 인코드/패키지 합니다. 이 자산은 DRM으로 보호 *되지 않습니다* . DRM 보호는 나중에 동적 보호에 의해 수행 됩니다.

2. 키 ID 및 콘텐츠 키 (선택적으로 키 초기값에서)를 만듭니다. 이 경우 두 가지 테스트 자산에는 단일 키 ID와 콘텐츠 키만 필요 하므로 키 관리 시스템이 필요 하지 않습니다.

3. Media Services API를 사용 하 여 테스트 자산에 대 한 다중 DRM 라이선스 배달 서비스를 구성 합니다. 회사 또는 회사의 공급 업체에서 Media Services의 라이선스 서비스 대신 사용자 지정 라이선스 서버를 사용 하는 경우이 단계를 건너뛸 수 있습니다. 라이선스 배달을 구성할 때 단계에서 라이선스 획득 Url을 지정할 수 있습니다. Media Services API는 다른 DRM 라이선스 서비스에 대 한 권한 부여 정책 제한 및 라이선스 응답 템플릿과 같은 몇 가지 세부 구성을 지정 하는 데 필요 합니다. 현재 Azure Portal는이 구성에 필요한 UI를 제공 하지 않습니다. API 수준 정보 및 샘플 코드는 [PlayReady 및/또는 Widevine 동적 일반 암호화 사용](media-services-protect-with-playready-widevine.md)을 참조 하세요.

4. Media Services API를 사용 하 여 테스트 자산에 대 한 자산 배달 정책을 구성 합니다. API 수준 정보 및 샘플 코드는 [PlayReady 및/또는 Widevine 동적 일반 암호화 사용](media-services-protect-with-playready-widevine.md)을 참조 하세요.

5. Azure에서 Azure AD 테넌트를 만들고 구성 합니다.

6. Azure AD 테넌트에서 몇 가지 사용자 계정 및 그룹을 만듭니다. "자격이 있는 사용자" 그룹을 하나 이상 만들고이 그룹에 사용자를 추가 합니다. 이 그룹의 사용자는 라이선스 획득 시 자격 검사를 통과 합니다. 이 그룹에 없는 사용자는 인증 확인을 통과 하지 못하고 라이선스를 획득할 수 없습니다. 이 "자격이 있는 사용자" 그룹의 멤버 자격은 Azure AD에서 발급 한 JWT의 필수 그룹 클레임입니다. 이 클레임 요구 사항은 다중 DRM 라이선스 배달 서비스를 구성 하는 단계에서 지정 합니다.

7. ASP.NET MVC 앱을 만들어 비디오 플레이어를 호스팅합니다. 이 ASP.NET 앱은 Azure AD 테넌트에 대해 사용자 인증으로 보호 됩니다. 적절 한 클레임은 사용자 인증 후 얻은 액세스 토큰에 포함 됩니다. 이 단계에서는 Openid connect Connect API를 권장 합니다. Telepítse az alábbi NuGet-csomagokat:

   * ActiveDirectory 클라이언트를 설치 합니다.
   * Owin를 설치 합니다.
   * Owin를 설치 합니다.
   * Owin 웹 패키지를 설치 합니다.
   * System.identitymodel. ActiveDirectory를 설치 합니다.

8. [AZURE MEDIA PLAYER API](https://amp.azure.net/libs/amp/latest/docs/)를 사용 하 여 플레이어를 만듭니다. [Azure Media Player의 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/)를 통해 다양한 DRM 플랫폼에 사용할 DRM 기술을 지정할 수 있습니다.

9. 다음 표에서는 테스트 매트릭스를 보여 줍니다.

    | **DRM** | **브라우저** | **자격이 있는 사용자에 대 한 결과** | **자격이 없는 사용자에 대 한 결과** |
    | --- | --- | --- | --- |
    | **PlayReady** |Windows 10의 Microsoft Edge 또는 Internet Explorer 11 |실패 |통과 |
    | **Widevine** |Chrome, Firefox, Opera |실패 |통과 |
    | **FairPlay** |MacOS의 Safari      |실패 |통과 |
    | **AES-128** |최신 브라우저  |실패 |통과 |

ASP.NET MVC player 앱에 대 한 Azure AD를 설정 하는 방법에 대 한 자세한 내용은 [AZURE MEDIA SERVICES OWIN mvc 기반 앱을 Azure Active Directory 통합 및 JWT 클레임을 기반으로 콘텐츠 키 배달 제한](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)을 참조 하세요.

자세한 내용은 [Azure Media Services 및 동적 암호화의 JWT 토큰 인증](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)을 참조 하세요.  

Azure AD에 대 한 정보:

* 개발자 정보는 [Azure Active Directory 개발자 가이드](../../active-directory/develop/v1-overview.md)에서 찾을 수 있습니다.
* [AZURE AD 테넌트 디렉터리 관리](../../active-directory/fundamentals/active-directory-administer.md)에서 관리자 정보를 찾을 수 있습니다.

### <a name="some-issues-in-implementation"></a>구현 시 몇 가지 문제
다음 문제 해결 정보를 사용 하 여 구현 문제에 대 한 도움을 줍니다.

* 발급자 URL은 "/"로 끝나야 합니다. 대상 그룹은 플레이어 응용 프로그램 클라이언트 ID 여야 합니다. 또한 발급자 URL의 끝에 "/"를 추가 합니다.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    Jwt [디코더에](http://jwt.calebb.net/)다음과 같이 jwt에 표시 된 것 처럼 **aud** 및 **iss**를 표시 합니다.

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* 응용 프로그램의 **구성** 탭에 있는 Azure AD의 응용 프로그램에 권한을 추가 합니다. 각 응용 프로그램, 로컬 및 배포 된 버전 모두에 대 한 권한이 필요 합니다.

    ![Engedélyek](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* 동적 CENC 보호를 설정할 때 올바른 발급자를 사용 합니다.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    다음은 작동 하지 않습니다.

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID는 Azure AD 테넌트 ID입니다. GUID는 Azure Portal의 **엔드포인트** 팝업 메뉴에서 찾을 수 있습니다.

* 그룹 멤버 자격 클레임 권한을 부여 합니다. Azure AD 응용 프로그램 매니페스트 파일에 다음이 있는지 확인 합니다. 

    "groupMembershipClaims": "All" (기본값은 null)

* 제한 요구 사항을 만들 때 적절 한 TokenType을 설정 합니다.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    SWT (ACS) 외에 JWT (Azure AD)에 대 한 지원을 추가 하기 때문에 기본 TokenType은 TokenType입니다. SWT/ACS를 사용 하는 경우 토큰을 SWT로 설정 해야 합니다.

## <a name="additional-topics-for-implementation"></a>구현에 대 한 추가 항목
이 섹션에서는 디자인 및 구현에서 몇 가지 추가 항목에 대해 설명 합니다.

### <a name="http-or-https"></a>HTTP 또는 HTTPS?
ASP.NET MVC player 응용 프로그램은 다음을 지원 해야 합니다.

* HTTPS를 사용 하는 Azure AD를 통한 사용자 인증.
* 클라이언트와 Azure AD 간의 JWT 교환 (HTTPS)
* 클라이언트에의 한 DRM 라이선스 획득 (Media Services에서 라이선스 배달이 제공 되는 경우 HTTPS 미만 이어야 함) PlayReady 제품군은 라이선스 배달에 대해 HTTPS를 요구 하지 않습니다. PlayReady 라이선스 서버가 Media Services 외부에 있는 경우 HTTP 또는 HTTPS 중 하나를 사용할 수 있습니다.

ASP.NET player 응용 프로그램은 https를 사용 하 여 https를 사용 하는 것이 좋습니다. 따라서 Media Player는 HTTPS의 페이지에 있습니다. 그러나 스트리밍의 경우 HTTP를 선호 하므로 혼합 된 콘텐츠의 문제를 고려해 야 합니다.

* 브라우저에서는 혼합 콘텐츠를 허용 하지 않습니다. 그러나 Silverlight 및 OSMF 플러그인과 같은 플러그 인은 부드러운 및 대시로 사용할 수 있습니다. 혼합 된 콘텐츠는 악의적인 JavaScript를 삽입 하는 기능을 위협 하 여 고객 데이터가 위험 해질 수 있기 때문에 보안 문제가 됩니다. 브라우저는 기본적으로이 기능을 차단 합니다. 이 문제를 해결 하는 유일한 방법은 서버 (원본) 쪽에서 HTTPS 또는 HTTP에 관계 없이 모든 도메인을 허용 하는 것입니다. 이는 바람직하지 않을 수 있습니다.
* 혼합 콘텐츠를 사용 하지 않습니다. 플레이어 응용 프로그램과 Media Player 모두 HTTP 또는 HTTPS를 사용 해야 합니다. 혼합 콘텐츠를 재생할 때 silverlightSS 기술에서는 혼합 콘텐츠 경고를 지워야 합니다. Flashss 기술은 tech는 혼합 콘텐츠 경고 없이 혼합 된 콘텐츠를 처리 합니다.
* 스트리밍 엔드포인트이 2014 년 8 월 이전에 만들어진 경우 HTTPS를 지원 하지 않습니다. 이 경우 HTTPS에 대 한 새 스트리밍 엔드포인트을 만들어 사용 합니다.

DRM으로 보호 된 콘텐츠에 대 한 참조 구현에서는 응용 프로그램과 스트리밍이 모두 HTTPS로 되어 있습니다. 공개 콘텐츠의 경우 플레이어는 인증 또는 라이선스가 필요 하지 않으므로 HTTP 또는 HTTPS 중 하나를 사용할 수 있습니다.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory 서명 키 롤오버
서명 키 롤오버는 구현에서 고려해 야 하는 중요 한 요소입니다. 이를 무시 하면 완료 된 시스템은 결국 6 주 이내에 완전히 작동을 중지 합니다.

Azure AD는 업계 표준을 사용 하 여 자체와 Azure AD를 사용 하는 응용 프로그램 간에 신뢰를 설정 합니다. 특히, Azure AD는 공개 키와 개인 키 쌍으로 구성 된 서명 키를 사용 합니다. Azure AD가 사용자에 대 한 정보를 포함 하는 보안 토큰을 만들 때 응용 프로그램으로 다시 전송 되기 전에 개인 키를 사용 하 여 Azure AD에서 서명 됩니다. 토큰이 유효 하 고 Azure AD에서 생성 되었는지 확인 하려면 응용 프로그램에서 토큰의 서명 유효성을 검사 해야 합니다. 응용 프로그램은 테넌트의 페더레이션 메타 데이터 문서에 포함 된 Azure AD에 의해 노출 되는 공개 키를 사용 합니다. 이 공개 키와이 키가 파생 되는 서명 키는 Azure AD의 모든 테넌트에 사용 되는 것과 동일 합니다.

Azure AD 키 롤오버에 대 한 자세한 내용은 [AZURE ad의 서명 키 롤오버에 대 한 중요 한 정보](../../active-directory/active-directory-signing-key-rollover.md)를 참조 하세요.

[공개-개인 키 쌍](https://login.microsoftonline.com/common/discovery/keys/)사이:

* Azure AD에서 JWT를 생성 하는 데 사용 되는 개인 키입니다.
* JWT를 확인 하는 Media Services의 DRM 라이선스 배달 서비스와 같은 응용 프로그램에서 공개 키를 사용 합니다.

보안을 위해 Azure AD는 주기적으로 (6 주마다) 인증서를 회전 합니다. 보안 위반의 경우 언제 든 지 키 롤오버가 발생할 수 있습니다. 따라서 Media Services의 라이선스 배달 서비스는 Azure AD에서 키 쌍을 회전 하는 데 사용 되는 공개 키를 업데이트 해야 합니다. 그렇지 않으면 Media Services의 토큰 인증이 실패 하 고 라이선스가 발급 되지 않습니다.

이 서비스를 설정 하려면 DRM 라이선스 배달 서비스를 구성할 때 TokenRestrictionTemplate를 설정 합니다.

JWT 흐름은 다음과 같습니다.

* Azure AD는 인증 된 사용자에 대 한 현재 개인 키로 JWT를 발급 합니다.
* 플레이어가 다중 DRM으로 보호 된 콘텐츠가 포함 된 CENC를 볼 때 먼저 Azure AD에서 발급 한 JWT를 찾습니다.
* 플레이어는 JWT를 포함 하는 라이선스 취득 요청을 Media Services의 라이선스 배달 서비스로 보냅니다.
* Media Services의 라이선스 배달 서비스는 라이선스를 발급 하기 전에 Azure AD의 현재/유효한 공개 키를 사용 하 여 JWT를 확인 합니다.

DRM 라이선스 배달 서비스는 항상 Azure AD에서 현재/유효한 공개 키를 확인 합니다. Azure AD에서 제공 하는 공개 키는 Azure AD에서 발급 한 JWT를 확인 하는 데 사용 되는 키입니다.

Azure AD가 JWT를 생성 한 후 JWT가 유효성 검사를 위해 Media Services의 DRM 라이선스 배달 서비스로 JWT를 보내기 전에 키 롤오버가 발생 하면 어떻게 되나요?

키는 언제 든 지 롤오버 될 수 있으므로 페더레이션 메타 데이터 문서에서 둘 이상의 유효한 공개 키를 항상 사용할 수 있습니다. Media Services 라이선스 배달에서는 문서에 지정 된 키를 사용할 수 있습니다. 하나의 키가 곧 롤오버 될 수 있으므로 다른 키가 대체 될 수도 있습니다.

### <a name="where-is-the-access-token"></a>액세스 토큰은 어디에 있나요?
웹 앱이 [OAuth 2.0 클라이언트 자격 증명 부여를 사용 하 여 응용 프로그램 id](../../active-directory/develop/web-api.md)에서 API 앱을 호출 하는 방식을 살펴보면 인증 흐름은 다음과 같습니다.

* 사용자가 웹 응용 프로그램에서 Azure AD에 로그인 합니다. 자세한 내용은 웹 [브라우저-웹 응용 프로그램을](../../active-directory/develop/web-app.md)참조 하세요.
* Azure AD 권한 부여 엔드포인트은 인증 코드를 사용 하 여 사용자 에이전트를 클라이언트 응용 프로그램으로 다시 리디렉션합니다. 사용자 에이전트는 인증 코드를 클라이언트 응용 프로그램의 리디렉션 URI로 반환 합니다.
* 웹 응용 프로그램은 웹 API에 인증 하 고 원하는 리소스를 검색할 수 있도록 액세스 토큰을 획득 해야 합니다. Azure AD 토큰 엔드포인트에 대 한 요청을 수행 하 고 자격 증명, 클라이언트 ID 및 web API의 응용 프로그램 ID URI를 제공 합니다. 사용자가 동의한 증명 하는 인증 코드를 제공 합니다.
* Azure AD는 응용 프로그램을 인증 하 고 web API를 호출 하는 데 사용 되는 JWT 액세스 토큰을 반환 합니다.
* HTTPS를 통해 웹 응용 프로그램은 반환 된 JWT 액세스 토큰을 사용 하 여 웹 API에 대 한 요청의 "인증" 헤더에 "전달자"로 지정 된 JWT 문자열을 추가 합니다. Web API는 JWT의 유효성을 검사 합니다. 유효성 검사가 성공 하면 원하는 리소스를 반환 합니다.

이 응용 프로그램 id 흐름에서 웹 API는 웹 응용 프로그램이 사용자를 인증 한 것으로 신뢰 합니다. 이러한 이유로이 패턴을 신뢰할 수 있는 하위 시스템 이라고 합니다. [권한 부여 흐름 다이어그램](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) 은 권한 부여 코드 부여 흐름이 작동 하는 방식을 설명 합니다.

토큰 제한이 있는 라이선스 취득은 동일한 신뢰할 수 있는 하위 시스템 패턴을 따릅니다. Media Services의 라이선스 배달 서비스는 웹 API 리소스 이거나 웹 응용 프로그램에서 액세스 해야 하는 "백 엔드 리소스"입니다. 그렇다면 액세스 토큰은 어디에 있나요?

액세스 토큰은 Azure AD에서 가져옵니다. 사용자 인증에 성공 하면 인증 코드가 반환 됩니다. 그러면 인증 코드가 클라이언트 ID 및 앱 키와 함께 사용 되어 액세스 토큰을 교환 합니다. 액세스 토큰은 Media Services 라이선스 배달 서비스를 가리키거나 나타내는 "포인터" 응용 프로그램에 액세스 하는 데 사용 됩니다.

Azure AD에서 포인터 앱을 등록 하 고 구성 하려면 다음 단계를 수행 합니다.

1. Azure AD 테넌트에서:

   * 로그온 URL을 사용 하 여 응용 프로그램 (리소스)을 추가 합니다. https://[resource_name] azurewebsites. net/. 
   * URL https://[aad_tenant_name]. onmicrosoft .com/[resource_name]와 함께 앱 ID를 추가 합니다.

2. 리소스 앱에 대 한 새 키를 추가 합니다.

3. GroupMembershipClaims 속성의 값이 "groupMembershipClaims": "All"이 되도록 앱 매니페스트 파일을 업데이트 합니다.

4. 플레이어 웹 앱을 가리키는 Azure AD 앱의 **다른 응용 프로그램에 대 한 사용 권한**섹션에서 1 단계에서 추가한 리소스 앱을 추가 합니다. **위임 된 권한**아래에서 **액세스 [resource_name]** 를 선택 합니다. 이 옵션은 리소스 앱에 액세스 하는 액세스 토큰을 만드는 웹 앱 사용 권한을 제공 합니다. Visual Studio 및 Azure 웹 앱을 사용 하 여 개발 하는 경우 웹 앱의 로컬 및 배포 된 버전 모두에 대해이 작업을 수행 합니다.

Azure AD에서 발급 한 JWT는 포인터 리소스에 액세스 하는 데 사용 되는 액세스 토큰입니다.

### <a name="what-about-live-streaming"></a>라이브 스트리밍에 대 한 자세한 정보
이전 논의는 주문형 자산에 초점을 두었습니다. 라이브 스트리밍에 대 한 자세한 정보

프로그램과 연결 된 자산을 VOD 자산으로 처리 하 여 Media Services에서 라이브 스트리밍을 보호 하는 데 정확히 동일한 디자인 및 구현을 사용할 수 있습니다.

특히 Media Services에서 라이브 스트리밍을 수행 하려면 채널을 만든 다음 채널에서 프로그램을 만들어야 합니다. 프로그램을 만들려면 프로그램에 대 한 라이브 아카이브를 포함 하는 자산을 만들어야 합니다. 라이브 콘텐츠의 다중 DRM 보호를 사용 하 여 CENC를 제공 하려면 프로그램을 시작 하기 전에 VOD 자산과 동일한 설정/처리를 자산에 적용 합니다.

### <a name="what-about-license-servers-outside-media-services"></a>Media Services 외부 라이선스 서버는 어떻게 되나요?
고객은 종종 자체 데이터 센터 또는 DRM 서비스 공급자가 호스트 하는 라이선스 서버 팜에 투자 합니다. Media Services Content Protection를 사용 하 여 하이브리드 모드로 작업할 수 있습니다. Media Services에서 콘텐츠를 호스트 하 고 동적으로 보호할 수 있는 반면 DRM 라이선스는 Media Services 외부의 서버에서 제공 됩니다. 이 경우 다음 사항을 고려해 야 합니다.

* STS는 라이선스 서버 팜에서 허용 되 고 확인할 수 있는 토큰을 발급 해야 합니다. 예를 들어 Axinom에서 제공 하는 Widevine 라이선스 서버에는 자격 메시지를 포함 하는 특정 JWT가 필요 합니다. 따라서 이러한 JWT를 발급 하는 STS가 있어야 합니다. 이러한 형식의 구현에 대 한 자세한 내용은 [Azure 설명서 센터](https://azure.microsoft.com/documentation/) 에서 [Axinom을 사용 하 여 Azure Media Services에 widevine 라이선스 전달](media-services-axinom-integration.md)을 참조 하세요.
* 더 이상 Media Services에서 라이선스 배달 서비스 (ContentKeyAuthorizationPolicy)를 구성 하지 않아도 됩니다. 다중 DRM을 사용 하 여 CENC를 설정 하도록 Asset배달 Ypolicy를 구성할 때 라이선스 획득 Url (PlayReady, Widevto및 FairPlay의 경우)을 제공 해야 합니다.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>사용자 지정 STS를 사용 하려면 어떻게 해야 하나요?
고객이 사용자 지정 STS를 사용 하 여 JWTs를 제공 하도록 선택할 수 있습니다. 이유는 다음과  같습니다.

* 고객이 사용 하는 IDP STS를 지원 하지 않습니다. 이 경우 사용자 지정 STS가 옵션일 수 있습니다.
* 고객은 STS를 고객의 구독자 청구 시스템과 통합하는 데 보다 유연하고 긴밀한 제어가 필요할 수 있습니다. 예를 들어 MVPD 연산자는 프리미엄, 기본 및 스포츠와 같은 여러 개의 DIST 구독자 패키지를 제공할 수 있습니다. 이 작업자는 토큰의 클레임을 구독자의 패키지와 일치시켜 특정 패키지의 콘텐츠만 제공되도록 할 수 있습니다. 이 경우 사용자 지정 STS가 필요한 유연성 및 제어를 제공합니다.

사용자 지정 STS를 사용할 때는 다음 두 가지를 변경해야 합니다.

* 자산에 대한 라이선스 배달 서비스를 구성할 때 Azure AD의 현재 키 대신 사용자 지정 STS에서 확인에 사용한 보안 키를 지정해야 합니다. 자세한 내용은을 참조 하세요. 
* JWT 토큰이 생성되면 Azure AD의 현재 X509 인증서의 프라이빗 키 대신 보안 키가 지정됩니다.

두 가지 유형의 보안 키가 있습니다.

* 대칭 키: JWT를 생성 및 확인 하는 데 동일한 키가 사용 됩니다.
* 비대칭 키: X509 인증서의 공개-개인 키 쌍은 JWT를 암호화/생성 하 고 공개 키를 사용 하 여 토큰을 확인 하는 개인 키와 함께 사용 됩니다.

> [!NOTE]
> 개발 플랫폼으로 .NET Framework/C#을 사용하는 경우 비공개 보안 키에 사용된 X509 인증서에는 키 길이가 2048 이상이어야 합니다. 이는 .NET Framework에서 System.IdentityModel.Tokens.X509AsymmetricSecurityKey 클래스의 요구 사항입니다. 그렇지 않으면 다음 예외가 throw됩니다.
> 
> IDX10630: 서명에 대 한 ' System.identitymodel. X509AsymmetricSecurityKey '는 ' 2048 ' 비트 보다 작을 수 없습니다.

## <a name="the-completed-system-and-test"></a>완료 된 시스템 및 테스트
이 섹션에서는 완료 된 종단 간 시스템에서 다음 시나리오를 안내 하므로 로그인 계정을 가져오기 전에 동작에 대 한 기본 그림을 사용할 수 있습니다.

* 통합 되지 않은 시나리오가 필요한 경우:

    * 보호 되지 않거나 DRM으로 보호 되지만 토큰 인증 (요청한 사용자에 게 라이선스 발급)이 없는 Media Services에서 호스트 되는 비디오 자산은 로그인 하지 않고 테스트할 수 있습니다. 비디오 스트리밍이 HTTP를 통해 인 경우 HTTP로 전환 합니다.

* 종단 간 통합 시나리오가 필요한 경우:

    * Media Services에서 동적 DRM 보호 하의 비디오 자산에 대해 Azure AD에서 생성 된 토큰 인증 및 JWT를 사용 하 여 로그인 해야 합니다.

플레이어 웹 응용 프로그램 및 해당 로그인의 경우 [이 웹 사이트](https://openidconnectweb.azurewebsites.net/)를 참조 하세요.

### <a name="user-sign-in"></a>Felhasználói bejelentkezés
종단 간 통합 된 DRM 시스템을 테스트 하려면 계정이 만들어지거나 추가 되어야 합니다.

계정 이란?

Azure 원래 Microsoft 계정 사용자만 액세스할 수 있지만 이제는 두 시스템의 사용자가 액세스를 허용 합니다. 이제 모든 Azure 속성이 인증을 위해 Azure AD를 신뢰 하며, Azure AD는 조직 사용자를 인증 합니다. Azure AD가 소비자 사용자를 인증 하기 위해 Microsoft 계정 소비자 id 시스템을 신뢰 하는 페더레이션 관계가 생성 되었습니다. 따라서 Azure AD는 기본 Azure AD 계정 뿐만 아니라 게스트 Microsoft 계정도 인증할 수 있습니다.

Azure AD는 Microsoft 계정 도메인을 신뢰 하므로 다음 도메인에서 사용자 지정 Azure AD 테넌트에 계정을 추가 하 고 해당 계정을 사용 하 여 로그인 할 수 있습니다.

| **도메인 이름** | **도메인** |
| --- | --- |
| **사용자 지정 Azure AD 테넌트 도메인** |somename.onmicrosoft.com |
| **회사 도메인** |microsoft.com |
| **Microsoft 계정 도메인** |outlook.com, live.com, hotmail.com |

계정이 만들어지거나 추가 되도록 작성자에 게 연락할 수 있습니다.

다음 스크린샷에서는 서로 다른 도메인 계정에서 사용 하는 다양 한 로그인 페이지를 보여 줍니다.

**사용자 지정 AZURE ad 테넌트 도메인 계정**: 사용자 지정 azure ad 테넌트 도메인의 사용자 지정 된 로그인 페이지입니다.

![사용자 지정 Azure AD 테넌트 도메인 계정](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**스마트 카드를 사용한 microsoft 도메인 계정**: MICROSOFT 회사 IT에서 2 단계 인증을 사용 하 여 사용자 지정 된 로그인 페이지입니다.

![사용자 지정 Azure AD 테넌트 도메인 계정](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft 계정**: 소비자에 대 한 Microsoft 계정의 로그인 페이지입니다.

![사용자 지정 Azure AD 테넌트 도메인 계정](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>PlayReady에 암호화 된 미디어 확장 사용
Windows 8.1 이상 및 Windows 10의 Microsoft Edge 브라우저와 같이 PlayReady 지원에 대 한 EME (암호화 된 미디어 확장)를 지 원하는 최신 브라우저에서 PlayReady는 EME에 대 한 기본 DRM입니다.

![PlayReady에 EME 사용](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

진한 플레이어 영역은 PlayReady 보호를 통해 보호 된 비디오의 화면 캡처를 차단 하기 때문입니다.

다음 스크린샷은 플레이어 플러그 인 및 MSE (Microsoft Security Essentials)/EME 지원을 보여 줍니다.

![PlayReady 용 플레이어 플러그 인](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Windows 10의 Microsoft Edge 및 Internet Explorer 11의 EME를 사용 하면 [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) 를 지 원하는 windows 10 장치에서이를 호출할 수 있습니다. PlayReady SL3000는 향상 된 프리미엄 콘텐츠 (4K, HDR) 및 새 콘텐츠 배달 모델 (향상 된 콘텐츠의 흐름)의 잠금을 해제 합니다.

Windows 장치에 집중 하기 위해 playready는 Windows 장치 (PlayReady SL3000)에서 사용할 수 있는 하드웨어의 유일한 DRM입니다. 스트리밍 서비스는 EME 또는 유니버설 Windows 플랫폼 응용 프로그램을 통해 PlayReady를 사용할 수 있으며, 다른 DRM 보다 PlayReady SL3000를 사용 하 여 더 높은 비디오 품질을 제공 합니다. 일반적으로 최대 2K의 콘텐츠는 Chrome 또는 Firefox를 통해 흐릅니다. 최대 4K의 콘텐츠는 Microsoft Edge/Internet Explorer 11 또는 동일한 장치에 있는 유니버설 Windows 플랫폼 응용 프로그램을 통해 흐릅니다. 크기는 서비스 설정 및 구현에 따라 달라 집니다.

#### <a name="use-eme-for-widevine"></a>Widevine에 대 한 사용
EME/Widevine를 지 원하는 최신 브라우저에서 (예: Windows 10의 Chrome 41 +, Windows 8.1, Mac OSX Yosemite 및 Android 4.4.4의 Chrome) Google Widevine EME 뒤에 있는 DRM

![Widevine에 대 한 사용](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine 보호 된 비디오의 화면 캡처를 방해 하지 않습니다.

![Widevine에 대 한 플레이어 플러그 인](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>자격이 없는 사용자
사용자가 "자격이 있는 사용자" 그룹의 멤버가 아닌 경우 사용자가 자격 확인을 통과 하지 않습니다. 그러면 다중 DRM 라이선스 서비스에서 표시 된 것 처럼 요청 된 라이선스의 발급을 거부 합니다. 자세한 설명은 설계 된 대로 "라이선스 획득 실패"입니다.

![자격이 없는 사용자](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>사용자 지정 보안 토큰 서비스 실행
사용자 지정 STS를 실행 하는 경우 사용자 지정 STS에서 대칭 또는 비대칭 키를 사용 하 여 JWT를 발급 합니다.

다음 스크린샷은 대칭 키 (Chrome 사용)를 사용 하는 시나리오를 보여 줍니다.

![대칭 키가 있는 사용자 지정 STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

다음 스크린샷은 X509 인증서를 통해 비대칭 키를 사용 하는 시나리오를 보여 줍니다 (Microsoft 최신 브라우저 사용).

![비대칭 키가 있는 사용자 지정 STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

위의 두 경우 모두 사용자 인증은 동일 하 게 유지 됩니다. Azure AD를 통해 수행 됩니다. 유일한 차이점은 Azure AD 대신 사용자 지정 STS에서 JWTs를 발급 한다는 것입니다. 동적 CENC 보호를 구성할 때 라이선스 배달 서비스 제한에는 JWT 유형 (대칭 또는 비대칭 키)이 지정 됩니다.

## <a name="summary"></a>Összefoglalás

이 문서에서는 Azure, Media Services 및 Media Player를 사용 하 여 다중 원시 DRM 및 토큰 인증을 통한 액세스 제어, 해당 디자인 및 구현을 통해 CENC에 대해 설명 했습니다.

* DRM/CENC 하위 시스템에 필요한 모든 구성 요소를 포함 하는 참조 디자인이 제공 되었습니다.
* 참조 구현은 Azure, Media Services 및 Media Player에 제공 되었습니다.
* 디자인 및 구현에 직접 관련 된 일부 항목에 대해서도 설명 합니다.

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
