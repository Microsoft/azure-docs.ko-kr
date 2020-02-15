---
title: 다중 DRM 콘텐츠 보호 시스템-Azure Media Services v3
description: 이 문서에서는 Azure Media Services를 사용 하 여 다중 DRM 콘텐츠 보호 시스템을 설계 하는 방법에 대 한 자세한 설명을 제공 합니다.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: fbc6d6fa8f9a3b424eaec1f04a61b5ca24fe14fc
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161786"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계 

OTT(Over-the-Top) 또는 온라인 스트리밍 솔루션을 위한 DRM(디지털 권한 관리) 하위 시스템을 디자인하고 구축하는 것은 복잡한 작업입니다. 일반적으로 운영자/온라인 비디오 공급자는 이러한 작업을 전문화된 DRM 서비스 공급자에게 아웃소싱합니다. 이 문서의 목표는 OTT 또는 온라인 스트리밍 솔루션에서 엔드투엔드 DRM 하위 시스템의 참조 디자인 및 참조 구현을 제공하는 것입니다.

이 문서는 OTT 또는 온라인 스트리밍/멀티 스크린 솔루션의 DRM 하위 시스템에서 작업 중인 엔지니어 또는 DRM 하위 시스템에 관심이 있는 모든 독자를 대상으로 합니다. 독자는 PlayReady, Widevine, FairPlay 또는 Adobe Access 등 한 가지 이상의 DRM 기술에 대해 잘 알고 있다고 가정합니다.

이 설명에서 다중 DRM으로 Azure Media Services에서 지원하는 3가지 DRM을 포함합니다. PlayReady 및 Widevine에 대한 CENC(Common Encryption), FairPlay 및 AES-128 암호화되지 않은 키 암호화 온라인 스트리밍 및 OTT 업계의 주요 추세는 다양한 클라이언트 플랫폼에서 기본 DRM을 사용하는 것입니다. 이러한 추세는 다양한 클라이언트 플랫폼에서 단일 DRM과 해당 클라이언트 SDK를 사용하는 이전 추세로부터 변화된 것입니다. 다중 기본 DRM의 CENC를 사용할 때 PlayReady와 Widevine 모두 [일반적인 암호화(ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) 사양에 따라 암호화됩니다.

콘텐츠 보호에 대해 기본 다중 DRM을 사용하는 이점은 다음과 같습니다.

* 기본 DRM을 포함하는 다양한 플랫폼을 대상으로 단일 암호화 처리가 사용되므로 암호화 비용이 절감됩니다.
* 스토리지에서 자산의 복사본이 하나만 필요하므로 자산의 관리 비용이 절감됩니다.
* 기본 플랫폼에서 기본 DRM 클라이언트는 일반적으로 무료로 제공되므로 DRM 클라이언트 라이선스 비용이 없습니다.

### <a name="goals-of-the-article"></a>문서의 목표

이 문서의 목표는 다음과 같습니다.

* 3가지 모든 DRM(DASH용 CENC, HLS용 FairPlay 및 부드러운 스트리밍을 위한 PlayReady)을 사용하는 DRM 하위 시스템의 참조 설계를 제공합니다.
* Azure 및 Azure Media Services 플랫폼에 대한 참조 구현을 제공합니다.
* 몇 가지 디자인 및 구현 토픽에 대해 설명합니다.

다음 표는 다양한 플랫폼에서 기본 DRM 지원 및 다양한 브라우저에서 EME 지원을 요약합니다.

| **클라이언트 플랫폼** | **기본 DRM** | **EME** |
| --- | --- | --- |
| **스마트 TV, STB** | PlayReady, Widevine 및/또는 기타 | 포함된 브라우저/PlayReady용 EME 및/또는 Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/PlayReady용 IE11|
| **Android 디바이스(전화, 태블릿, TV)** |Widevine |Widevine용 Chrome |
| **iOS** | FairPlay | FairPlay용 Safari(iOS 11.2 이후) |
| **macOS** | FairPlay | FairPlay용 Safari(Safari 9 이후+Mac OS X 10.11에서+El Capitan)|
| **tvOS** | FairPlay | |

각 DRM에 대한 배포의 현재 상태를 고려하면 서비스는 일반적으로 가장 좋은 방법으로 모든 유형의 엔드포인트을 해결하도록 2개 또는 3개의 DRM을 구현해야 합니다.

다양한 클라이언트에서 사용자 환경의 특정 수준에 도달하는 데 서비스 논리의 복잡성과 클라이언트 쪽의 복잡성 사이의 장단점이 있습니다.

선택할 때는 다음 사항에 유의하세요.

* PlayReady는 모든 Windows 디바이스와 일부 Android 디바이스에서 고유하게 구현되며 거의 모든 플랫폼에서 소프트웨어 SDK를 통해 사용할 수 있습니다.
* Widevine은 모든 Android 디바이스, Chrome 및 일부 다른 디바이스에서 고유하게 구현됩니다. Widevine은 또한 DASH를 통해 Firefox 및 Opera 브라우저에서 지원됩니다.
* FairPlay는 iOS, macOS 및 tvOS에서 사용할 수 있습니다.


## <a name="a-reference-design"></a>참조 디자인
이 섹션에서는 구현에 사용되는 기술과 관계 없이 적용할 수 있는 참조 디자인을 제공합니다.

DRM 하위 시스템은 다음 구성 요소를 포함할 수 있습니다.

* 키 관리
* DRM 암호화 패키징
* DRM 라이선스 배달
* 자격 확인/액세스 제어
* 사용자 인증/권한 부여
* 플레이어 앱
* 원본/CDN(Content Delivery Network)

다음 다이어그램에서는 DRM 하위 시스템에서 구성 요소 간의 상호 작용을 개략적으로 보여 줍니다.

![CENC를 사용하는 DRM 하위 시스템](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

이 디자인에는 세 개의 기본 계층이 있습니다.

* 백오피스 계층(검은색)은 외부적으로 노출되지 않습니다.
* DMZ 계층(진한 파란색)은 공용으로 연결되는 모든 엔드포인트를 포함합니다.
* 공용 인터넷 계층(밝은 파란색)은 공용 인터넷을 통과하는 트래픽과 CDN 및 플레이어를 포함합니다.

정적 또는 동적 암호화에 관계 없이 DRM 보호를 제어하기 위한 콘텐츠 관리 도구도 있습니다. DRM 암호화에 대한 입력은 다음을 포함해야 합니다.

* MBR 비디오 콘텐츠
* 콘텐츠 키
* 라이선스 획득 URL

재생 시간 동안의 개략적인 흐름은 다음과 같습니다.

* 사용자가 인증됩니다.
* 사용자에 대한 권한 부여 토큰이 만들어집니다.
* DRM으로 보호된 콘텐츠(매니페스트)가 플레이어로 다운로드됩니다.
* 플레이어는 라이선스 서버에 키 ID 및 권한 부여 토큰과 함께 라이선스 획득 요청을 제출합니다.

다음 섹션에서는 키 관리의 디자인에 대해 설명합니다.

| **ContentKey-to-asset** | **시나리오** |
| --- | --- |
| 일대일 |가장 간단한 경우입니다. 가장 미세한 제어를 제공합니다. 하지만 이 배열의 경우 일반적으로 이렇게 가장 높은 라이선스 배달 비용이 발생합니다. 보호된 각 자산에 대해 최소 하나의 라이선스 요청이 필요합니다. |
| 일대다 |여러 자산에 대해 동일한 콘텐츠 키를 사용할 수 있습니다. 예를 들어 장르 또는 장르의 하위 집합(또는 영화 장르)과 같은 논리적 그룹의 모든 자산에 대해 단일 콘텐츠 키를 사용할 수 있습니다. |
| 다대일 |각 자산에 대해 여러 콘텐츠 키가 필요합니다. <br/><br/>예를 들어 MPEG DASH에 대해 다중 DRM의 동적 CENC 보호를 적용하고 HLS에 대해 AES-128 암호화를 적용해야 하는 경우 두 가지 별도의 콘텐츠 키가 필요합니다. 각 콘텐츠 키에는 고유한 ContentKeyType이 필요합니다. (동적 CENC 보호에 사용된 콘텐츠 키의 경우, ContentKeyType.CommonEncryption을 사용합니다. 동적 AES-128 암호화에 사용되는 콘텐츠 키의 경우, ContentKeyType.EnvelopeEncryption을 사용합니다.)<br/><br/>또 다른 예로 DASH 콘텐츠의 CENC 보호에서는 이론적으로 비디오 스트림을 보호하는 데 하나의 콘텐츠 키를 사용할 수 있으며 오디오 스트림을 보호하는 데는 다른 콘텐츠 키를 사용할 수 있습니다. |
| 다대다 |위의 두 가지 시나리오를 조합한 것입니다. 동일한 자산 그룹의 여러 자산 각각에 대해 한 집합의 콘텐츠 키를 사용합니다. |

또 다른 중요한 고려 사항은 영구 및 비영구 라이선스의 사용에 관한 것입니다.

이러한 고려 사항이 중요한 이유는 무엇인가요?

라이선스 배달에 퍼블릭 클라우드를 사용하는 경우 영구 및 비영구 라이선스는 라이선스 배달 비용에 직접적인 영향을 미칩니다. 다음 두 가지 다른 디자인 사례가 이러한 사항을 보여 줍니다.

* 월간 구독: 영구 라이선스와 일대다(1–to-Many) 콘텐츠 키 대 자산 매핑을 사용합니다. 예를 들어, 모든 어린이용 영화의 경우 암호화에 단일 콘텐츠 키를 사용합니다. 이 경우 다음과 같습니다.

    모든 어린이용 영화에 요청된 전체 라이선스 수/디바이스 = 1

* 월간 구독: 비영구 라이선스와 콘텐츠 키 및 자산 간에 일대일(1–to-1) 매핑을 사용합니다. 이 경우 다음과 같습니다.

    모든 어린이용 영화에 요청된 전체 라이선스 수/디바이스 = [본 영화 수] x [세션 수]

두 가지 서로 다른 디자인으로 인해 라이선스 요청 패턴이 매우 달라집니다. 라이선스 배달 서비스가 Media Services와 같은 퍼블릭 클라우드에서 제공되는 경우 패턴이 다르면 라이선스 배달 비용이 달라집니다.

## <a name="map-design-to-technology-for-implementation"></a>디자인을 구현 기술에 매핑
다음으로, 각 구성 요소에 사용할 기술을 지정하여 일반적인 디자인이 Azure/Media Services 플랫폼의 기술에 매핑됩니다.

다음 표에서 이러한 매핑을 보여 줍니다.

| **구성 요소** | **Technology** |
| --- | --- |
| **플레이어** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **IDP(ID 공급자)** |Azure AD(Azure Active Directory) |
| **STS(보안 토큰 서비스)** |Azure AD |
| **DRM 보호 워크플로** |Azure Media Services 동적 보호 |
| **DRM 라이선스 배달** |* Media Services 라이선스 배달(PlayReady, Widevine, FairPlay) <br/>* Axinom License Server <br/>* 사용자 지정 PlayReady 라이선스 서버 |
| **원본** |Azure Media Services 스트리밍 엔드포인트 |
| **키 관리** |참조 구현에는 필요하지 않음 |
| **콘텐츠 관리** |C# 콘솔 애플리케이션 |

즉, IDP와 STS 둘 다 Azure AD에서 제공됩니다. 플레이어로는 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)가 사용됩니다. Azure Media Services 및 Azure Media Player 둘 다 DASH를 통한 CENC, HLS를 통한 FairPlay, 부드러운 스트리밍을 통한 PlayReady 및 DASH, HLS 및 부드러운 스트리밍을 통한 AES-128 암호화를 제공합니다.

다음 다이어그램에서는 이전 기술 매핑을 사용하는 전체적인 구조 및 흐름을 보여 줍니다.

![Media Services의 CENC](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

DRM 콘텐츠 보호를 설정하기 위해 콘텐츠 관리 도구는 다음 입력을 사용합니다.

* 개방 콘텐츠
* 키 관리에서 콘텐츠 키
* 라이선스 획득 URL
* 대상, 발급자 및 토큰 클레임과 같은 Azure AD의 정보 목록

콘텐츠 관리 도구의 출력은 다음과 같습니다.

* ContentKeyPolicy는 사용되는 각 유형의 DRM에 대한 DRM 라이선스 템플릿을 설명합니다.
* ContentKeyPolicyRestriction은 DRM 라이선스가 발급되기 전에 액세스 제어를 설명합니다.
* Streamingpolicy는 스트리밍을 위한 다양한 DRM(암호화 모드, 스트리밍 프로토콜, 컨테이너 형식)의 조합을 설명합니다.
* StreamingLocator는 암호화 및 스트리밍 URL에 사용되는 콘텐츠 키/IV를 설명합니다. 

런타임 동안 흐름은 다음과 같습니다.

* 사용자 인증 시 JWT가 생성됩니다.
* JWT에 포함된 클레임 중 하나는 "EntitledUserGroup"의 그룹 개체 ID를 포함하는 groups 클레임입니다. 이 클레임은 자격 확인을 전달하는 데 사용됩니다.
* 플레이어는 CENC로 보호된 콘텐츠의 클라이언트 매니페스트를 다운로드하고 다음을 확인합니다.
   * 키 ID
   * 콘텐츠는 DRM으로 보호됩니다.
   * 라이선스 획득 URL
* 플레이어는 브라우저/DRM 지원에 따라 라이선스 획득 요청을 합니다. 라이선스 획득 요청에서 키 ID 및 JWT도 제출됩니다. 라이선스 배달 서비스는 필요한 라이선스를 발급하기 전에 포함된 JWT 및 클레임을 확인합니다.

## <a name="implementation"></a>구현
### <a name="implementation-procedures"></a>구현 절차
구현에는 다음 단계가 포함됩니다.

1. 테스트 자산을 준비합니다. Media Services에서 테스트 비디오를 다중 비트 전송률 조각화된 MP4로 인코딩/패키징합니다. 이 자산은 DRM으로 보호되지 *않습니다*. DRM 보호는 나중에 동적 보호로 수행됩니다.

2. 키 ID 및 콘텐츠 키(필요한 경우 키 시드에서)를 만듭니다. 이 인스턴스에서는 여러 테스트 자산에 대해 단일 키 ID 및 콘텐츠 키만 필요하므로 키 관리 시스템이 필요하지 않습니다.

3. Media Services API를 사용하여 테스트 자산에 대한 다중 DRM 라이선스 배달 서비스를 구성합니다. 회사 또는 회사의 공급업체에서 Media Services의 라이선스 서비스 대신 사용자 지정 라이선스 서버를 사용하는 경우 이 단계를 건너뜁니다. 라이선스 배달 구성 단계에 라이선스 획득 URL을 지정할 수 있습니다. Media Services API는 권한 부여 정책 제한, 다양한 DRM 라이선스 서비스에 대한 라이선스 응답 템플릿 등과 같은 일부 세부적인 구성을 지정하는 데 필요합니다. 현재는 Azure Portal에서 이 구성에 필요한 UI를 제공하지 않습니다. API 수준 정보 및 샘플 코드에 대해서는 [PlayReady 및/또는 Widevine 동적 일반 암호화 사용](protect-with-drm.md)을 참조하세요.

4. Media Services API를 사용하여 테스트 자산에 대한 자산 배달 정책을 구성합니다. API 수준 정보 및 샘플 코드에 대해서는 [PlayReady 및/또는 Widevine 동적 일반 암호화 사용](protect-with-drm.md)을 참조하세요.

5. Azure에서 Azure AD 테넌트를 만들고 구성합니다.

6. Azure AD 테넌트에서 몇 가지 사용자 계정 및 그룹을 만듭니다. "Entitled User" 그룹 이상을 만들고 사용자를 이 그룹에 추가합니다. 이 그룹의 사용자는 라이선스 획득 시 자격 확인을 전달합니다. 이 그룹에 없는 사용자는 인증 확인 전달에 실패하며 라이선스를 획득할 수 없게 됩니다. 이 "Entitled User" 그룹의 멤버가 되려면 Azure AD에서 발급한 JWT의 필수 groups 클레임이 필요합니다. 이 클레임 요구 사항은 다중 DRM 라이선스 배달 서비스 구성 단계에서 지정합니다.

7. 비디오 플레이어를 호스트할 ASP.NET MVC 앱을 만듭니다. 이 ASP.NET 앱은 Azure AD 테넌트에 대한 사용자 인증으로 보호됩니다. 적절한 클레임이 사용자 인증 후 얻은 액세스 토큰에 포함됩니다. 이 단계에는 OpenID Connect API가 권장됩니다. 다음 NuGet 패키지를 설치합니다.

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)를 사용하여 플레이어를 만듭니다. [Azure Media Player의 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/)를 통해 다양한 DRM 플랫폼에 사용할 DRM 기술을 지정할 수 있습니다.

9. 다음 표에서는 테스트 매트릭스를 보여 줍니다.

    | **DRM** | **브라우저** | **자격이 있는 사용자에 대한 결과** | **자격이 없는 사용자에 대한 결과** |
    | --- | --- | --- | --- |
    | **PlayReady** |Windows 10의 Microsoft Edge 또는 Internet Explorer 11 |합격 |실패 |
    | **Widevine** |Chrome, Firefox, Opera |합격 |실패 |
    | **FairPlay** |macOS의 Safari      |합격 |실패 |
    | **AES-128** |최신 브라우저  |합격 |실패 |

ASP.NET MVC 플레이어 앱에 대해 Azure AD를 설정하는 방법에 대한 내용은 [Azure Media Services OWIN MVC 기반 앱을 Azure Active Directory와 통합하고 JWT 클레임을 기준으로 콘텐츠 키 배달 제한](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)을 참조하세요.

자세한 내용은 [Azure Media Services 및 동적 암호화의 JWT 토큰 인증](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)을 참조하세요.  

Azure AD에 대한 내용:

* [Azure Active Directory 개발자 가이드](../../active-directory/develop/v2-overview.md)에서 개발자 정보를 찾을 수 있습니다.
* [Azure AD 테넌트 디렉터리 관리](../../active-directory/fundamentals/active-directory-administer.md)에서 관리자 정보를 찾을 수 있습니다.

### <a name="some-issues-in-implementation"></a>구현에 대한 몇 가지 문제

구현 문제에 대한 도움을 얻으려면 다음 문제 해결 정보를 참조하세요.

* 발급자 URL는 "/"로 끝나야 합니다. 대상은 플레이어 애플리케이션 클라이언트 ID여야 합니다. 발급자 URL 끝에 "/"를 추가해야 합니다.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    [JWT 디코더](http://jwt.calebb.net/)에서 JWT에 다음과 같이 **aud** 및 **iss**가 나타납니다.

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* 애플리케이션의 **구성** 탭에서 Azure AD의 애플리케이션에 권한을 추가합니다. 각 애플리케이션(로컬 및 배포된 버전)에 사용 권한이 필요합니다.

    ![사용 권한](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* 동적 CENC 보호 설정에 올바른 발급자를 사용합니다.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    다음은 작동하지 않습니다.

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID는 Azure AD 테넌트 ID입니다. Azure Porta의 **엔드포인트** 팝업 창에서 GUID를 찾을 수 있습니다.

* 그룹 멤버 자격 클레임 권한을 부여합니다. Azure AD 애플리케이션 매니페스트 파일에서 다음이 있는지 확인합니다. 

    "groupMembershipClaims": "All", (기본값은 null)

* 제한 사항 요구 사항을 만들 때 적절한 TokenType을 설정합니다.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    SWT(ACS) 외에도 JWT(Azure AD)의 지원이 추가되었으므로 기본 TokenType은 TokenType.JWT입니다. SWT/ACS를 사용하는 경우 토큰을 TokenType.SWT로 설정해야 합니다.

## <a name="the-completed-system-and-test"></a>완료된 시스템 및 테스트

이 섹션에서는 사용자가 로그인 계정을 얻기 전 동작에 대한 기본적인 그림을 그려볼 수 있도록, 완료된 엔드투엔드 시스템에 대한 몇 가지 시나리오를 살펴볼 것입니다.

* 비통합 시나리오가 필요한 경우

    * 동영상 자산이 보호되지 않거나 토큰 인증 없이 DRM으로 보호되는(요청하는 모든 대상에 라이선스 발급) Media Services에서 호스트되는 경우, 로그인 없이 테스트할 수 있습니다. 비디오 스트리밍이 HTTP를 통해 이루어지는 경우 HTTP로 전환합니다.

* 엔드투엔드 통합 시나리오가 필요한 경우:

    * 동영상 자산이 Media Services에서 동적 DRM 보호 하에 있고 토큰 인증 및 Azure AD가 생성하는 JWT가 있는 경우 로그인해야 합니다.

플레이어 웹 애플리케이션 및 해당 로그인에 대해서는 [이 웹 사이트](https://openidconnectweb.azurewebsites.net/)를 참조하세요.

### <a name="user-sign-in"></a>사용자 로그인
엔드투엔드 통합된 DRM 시스템을 테스트하려면 계정을 만들거나 추가해야 합니다.

계정이란?

원래 Microsoft 계정 사용자만 Azure에 액세스할 수 있었으나 이제는 이 두 가지 시스템의 사용자도 액세스할 수 있습니다. 이제 모든 Azure 속성이 인증을 위해 Azure AD를 신뢰하고, Azure AD가 조직 사용자를 인증합니다. 소비자 사용자를 인증하는 Microsoft 계정 소비자 ID 시스템을 Azure AD가 신뢰하는 경우 페더레이션 관계가 생성되었습니다. 그 결과, Azure AD가 기본 Azure AD 계정 뿐만 아니라 게스트 Microsoft 계정도 인증할 수 있습니다.

Azure AD는 Microsoft 계정 도메인을 신뢰하므로 다음 도메인에서 사용자 지정 Azure AD 테넌트로 어떤 계정이든 추가하여 로그인에 사용할 수 있습니다.

| **도메인 이름** | **도메인** |
| --- | --- |
| **사용자 지정 Azure AD 테넌트 도메인** |somename.onmicrosoft.com |
| **회사 도메인** |microsoft.com |
| **Microsoft 계정 도메인** |outlook.com, live.com, hotmail.com |

사용자를 위한 계정을 만들거나 추가한 작성자에게 연락할 수 있습니다.

다음은 스크린샷에서는 다양한 도메인 계정에서 사용하는 다양한 로그인 페이지를 보여 줍니다.

**사용자 지정 Azure AD 테넌트 도메인 계정**: Azure AD 테넌트 도메인의 사용자 지정된 로그인 페이지

![사용자 지정 Azure AD 테넌트 도메인 계정 1](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**스마트 카드를 사용한 Microsoft 도메인 계정**: Microsoft 회사 IT에서 2단계 인증으로 사용자 지정한 로그인 페이지

![사용자 지정 Azure AD 테넌트 도메인 계정 2](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft 계정**: 소비자를 위한 Microsoft 계정의 로그인 페이지

![사용자 지정 Azure AD 테넌트 도메인 계정 3](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>PlayReady에 암호화된 미디어 확장 사용

Windows 8.1 이상의 Internet Explorer 11, Windows 10의 Microsoft Edge 브라우저와 같이 PlayReady 지원에 대한 EME(암호화된 미디어 확장)를 지원하는 최신 브라우저에서 PlayReady는 EME를 위한 기본 DRM입니다.

![PlayReady에 EME 사용](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

플레이어 영역에서 어두운 부분은 PlayReady 보호 기능이 보호된 비디오의 화면 캡처를 차단하기 때문입니다.

다음 스크린샷은 플레이어 플러그 인 및 MSE(Microsoft Security Essentials)/EME 지원을 보여 줍니다.

![PlayReady용 플레이어 플러그 인](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

Windows 10의 Microsoft Edge 및 Internet Explorer 11에 있는 EME를 통해 이를 지원하는 Windows 10 디바이스에서 [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/)을 호출할 수 있습니다. PlayReady SL3000은 향상된 프리미엄 콘텐츠(4K, HDR) 흐름 및 새 콘텐츠 배달 모델(향상된 콘텐츠용)의 잠금을 해제합니다.

Windows 디바이스에 집중: PlayReady는 Windows 디바이스(PlayReady SL3000)에서 사용 가능한 하드웨어의 유일한 DRM입니다. 스트리밍 서비스는 EME 또는 유니버설 Windows 플랫폼 애플리케이션을 통해 PlayReady를 사용하고 PlayReady SL3000을 사용하여 다른 DRM보다 더 높은 화질을 제공할 수 있습니다. 일반적으로 2K 이내의 콘텐츠는 Chrome 또는 Firefox를 통해 흐르고, 4K 이내의 콘텐츠는 동일한 디바이스의 Microsoft Edge/Internet Explorer 11 또는 유니버설 Windows 플랫폼 애플리케이션을 통해 흐릅니다. 그 양은 서비스 설정 및 구현에 따라 다릅니다.

#### <a name="use-eme-for-widevine"></a>Widevine에 EME 사용

Windows 10, Windows 8.1, Mac OSX Yosemite의 Chrome 41 이상, Android 4.4.4의 Chrome과 같이 EME/Widevine을 지원하는 최신 브라우저에서는 Google Widevine이 EME 뒤의 DRM입니다.

![Widevine에 EME 사용](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine은 보호된 비디오의 화면 캡처 만들기를 차단하지 않습니다.

![Widevine용 플레이어 플러그 인](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>FairPlay용 EME 사용

마찬가지로 macOS 또는 iOS 11.2 이상의 Safari의 이 테스트 플레이어에서 FairPlay로 보호된 콘텐츠를 테스트할 수 있습니다.

protectionInfo.type으로 "FairPlay"를 입력하고 FPS AC 경로(FairPlay 스트리밍 애플리케이션 인증서 경로)에 애플리케이션 인증서에 대한 올바른 URL을 입력해야 합니다.

### <a name="unentitled-users"></a>자격이 없는 사용자

사용자가 "Entitled Users" 그룹의 구성원이 아닌 경우 사용자는 자격 확인을 통과하지 못합니다. 그러면 다중 DRM 라이선스 서비스는 아래 표시된 것처럼 요청된 라이선스의 발급을 거부합니다. 자세한 설명은 설계된 대로 "라이선스 획득 실패"입니다.

![자격이 없는 사용자](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>사용자 지정 보안 토큰 서비스 실행

사용자 지정 STS를 실행하는 경우 사용자 지정 STS에서 대칭 또는 비대칭 키를 사용하여 JWT를 발급합니다.

다음 스크린샷에서는 대칭 키를 사용하는 시나리오를 보여 줍니다(Chrome 사용).

![대칭 키가 있는 사용자 지정 STS](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

다음 스크린샷은 X509 인증서를 통해 비대칭 키를 사용하는 시나리오를 보여 줍니다(Microsoft 최신 브라우저 사용).

![비대칭 키가 있는 사용자 지정 STS](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

이전의 두 경우 모두 사용자 인증은 동일하게 유지됩니다. 즉, Azure AD를 통해 수행됩니다. 유일한 차이점은 JWT가 Azure AD 대신 사용자 지정 STS에 의해 발급된다는 점입니다. 동적 CENC 보호를 구성할 때 라이선스 배달 서비스 제한으로 JWT 형식을 대칭 또는 비대칭 키로 지정합니다.

## <a name="next-steps"></a>다음 단계

* [질문과 대답](frequently-asked-questions.md)
* [콘텐츠 보호 개요](content-protection-overview.md)
* [DRM을 사용하여 콘텐츠 보호](protect-with-drm.md)
