---
title: Azure AD를 사용하여 엔드투엔드 콘텐츠 보호
description: 이 문서에서는 Azure Media Services 및 Azure Active Directory를 사용하여 콘텐츠를 보호하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-javascript
ms.openlocfilehash: 006e312e67f5f4014248c44a799c2dde826801c2
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89258846"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>자습서: Azure AD를 사용하여 엔드투엔드 콘텐츠 보호

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 자습서와 제공된 플레이어 샘플을 사용하면 AMS(Azure Media Services) 및 AAD(Azure Active Directory)에서 엔드투엔드 미디어 콘텐츠 보호 하위 시스템을 설정하여 모든 AMS 지원 DRM/AES-128, 스트리밍 프로토콜, 코덱 및 컨테이너 형식을 통해 미디어 콘텐츠를 스트림할 수 있습니다. 샘플은 일반적으로 PKCE(Proof Key for Code Exchange)를 사용하여 권한 부여 코드 흐름을 통해 OAuth 2에서 보호되는 REST API에 안전하게 액세스할 수 있습니다. (Azure Media Services 라이선스 전송 서비스는 그 중 하나일 뿐입니다.) 또한 OAuth 2 권한 부여 코드 흐름을 사용하여 보호되는 Microsoft Graph API 또는 사용자 지정 개발 REST API에서도 작동합니다. 이 자습서는 [샘플 코드](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)에 대한 도우미 문서입니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
>
> * 인증 요구 사항 고려
> * 앱 작동 방식 이해
> * 백 엔드 리소스 앱 등록
> * 클라이언트 앱 등록
> * 미디어 서비스 계정의 콘텐츠 키 정책 및 스트리밍 정책 설정
> * 플레이어 앱 설정

Azure Media Services 구독이 없는 경우 Azure [체험 계정](https://azure.microsoft.com/free/)을 만든 다음, Media Services 계정을 만듭니다.

### <a name="duration"></a>Duration
필수 구성 요소 기술을 사용할 준비가 되면 이 자습서를 완료하는 데 약 2시간 정도 걸립니다.

## <a name="prerequisites"></a>필수 구성 요소

사용되는 최신 기술 버전 및 개념은 다음과 같습니다. 이 자습서를 시작하기 전에 이를 숙지하는 것이 좋습니다.

### <a name="prerequisite-knowledge"></a>필수 구성 요소 기술

선택 사항이지만, 이 자습서를 시작하기 전에 다음 개념을 숙지하는 것이 좋습니다.

* DRM(디지털 권한 관리)
* [AMS(Azure Media Services) v3](./media-services-overview.md)
* AMS API v3, Azure Portal 또는 [AMSE(Azure Media Services 탐색기) 도구](https://github.com/Azure/Azure-Media-Services-Explorer)를 사용하는 AMS [콘텐츠 키 정책](content-key-policy-concept.md)
* [Microsoft ID 플랫폼](../../active-directory/develop/index.yml)의 Azure AD v2 엔드포인트
* 최신 클라우드 인증(예: [OAuth 2.0 및 OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md))
  * [OAuth 2.0의 권한 부여 코드 흐름](../../active-directory/develop/v2-oauth2-auth-code-flow.md) 및 PKCE가 필요한 이유
  * [위임된 권한 및 애플리케이션 권한](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT 토큰](../../active-directory/develop/access-tokens.md), 해당 클레임 및 서명 키 롤오버(샘플에 포함되어 있음)

### <a name="prerequisite-code-and-installations"></a>필수 구성 요소 코드 및 설치

* 샘플 코드. [샘플 코드](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)를 다운로드하세요.
* Visual Studio Code 설치. [https://code.visualstudio.com/download](https://code.visualstudio.com/download)에서 Visual Studio Code를 다운로드하세요.
* Node.js 설치 [https://nodejs.org](https://nodejs.org)에서 Node.js를 다운로드하세요. NPM이 설치와 함께 제공됩니다.
* [Azure 구독](https://azure.microsoft.com/free/).
* AMS(Azure Media Services) 계정
* @azure/msal-browser v2.0(다양한 클라이언트 플랫폼용 [MSAL(Microsoft 인증 라이브러리)](../../active-directory/develop/msal-overview.md) SDK 제품군의 멤버 중 하나)
* 최신 버전의 [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)(샘플에 포함되어 있음)
* Apple의 FPS 자격 증명(클라이언트 쪽 JavaScript를 통해 액세스할 수 있는 CORS를 사용하여 호스팅되는 FairPlay DRM 및 애플리케이션 인증서를 포함하려는 경우)

> [!IMPORTANT]
> 이 자습서에서는 .NET을 사용하여 콘텐츠 키 정책 제한을 만듭니다.  .NET 개발자가 아니지만 Node.js를 사용하여 Azure Media Services에 연결하려는 경우 [Media Services v3 API에 연결 - Node.js](configure-connect-nodejs-howto.md)를 참조하세요. 또한 키 롤오버를 자동으로 처리하는 데 사용할 수 있는 Node.js 모듈도 있습니다. Node.js [passport-add 모듈](https://github.com/AzureAD/passport-azure-ad)을 참조하세요.

## <a name="consider-the-authentication-and-authorization-requirements"></a>인증 및 인증 요구 사항에 대한 고려

하위 시스템을 설계할 때 몇 가지 문제가 있습니다. 여기에는 여러 개의 이동하는 부분이 있으며, 클라이언트 앱 제약 조건 및 6주마다 발생하는 Azure AD 키 롤오버가 있습니다.

이 자습서에서 사용되는 SPA(단일 페이지 앱)는 이에 따른 인증 요구 사항 및 제한 사항에 대한 문제를 고려합니다. 이는 다음을 사용합니다.

* Azure AD v2 엔드포인트(Azure AD 개발자 플랫폼(v1 엔드포인트)은 Microsoft ID 플랫폼(v2 엔드포인트)으로 변경되고 있음)
* 권한 부여 코드 흐름(OAuth 2 암시적 허용 흐름은 더 이상 사용되지 않음)
* 다음 제약 조건이 적용되는 앱
    * 퍼블릭 클라이언트에서 클라이언트 암호를 숨길 수 없습니다.  권한 부여 코드 흐름만으로 클라이언트 암호를 숨겨야 하므로 PKCE를 사용한 권한 부여 코드 흐름이 사용됩니다.
    * 브라우저 보안 샌드박스(CORS/실행 전 제약 조건)가 적용되는 브라우저 기반 앱입니다.
    * JavaScript 보안 제약 조건(사용자 지정 헤더 접근성, 상관 관계 ID)이 적용된 최신 JavaScript를 사용하는 브라우저 기반 앱입니다.

## <a name="understand-the-subsystem-design"></a>하위 시스템 설계 이해

다음 다이어그램에서는 하위 시스템의 설계를 보여 줍니다.  세 개의 계층이 있습니다.

* 콘텐츠 키 정책을 구성하고 스트림할 콘텐츠를 게시하는 백 오피스 계층(검은색)
* 인증, 권한 부여, DRM 라이선스 및 암호화된 콘텐츠를 제공하는 플레이어/고객 연결 엔드포인트인 퍼블릭 엔드포인트(파란색)
* 모든 구성 요소를 통합하는 플레이어 앱(연한 파란색)
    * Azure AD를 통해 사용자 인증을 처리합니다.
    * Azure AD에서 access_token 가져오기를 처리합니다.
    * AMS/CDN에서 매니페스트 및 암호화된 콘텐츠를 받습니다.
    * Azure Media Services에서 DRM 라이선스를 가져옵니다.
    * 콘텐츠 암호 해독, 디코딩 및 표시를 처리합니다.

![JWT 토큰을 구문 분석하는 화면](media/aad-ams-content-protection/subsystem.svg)

하위 시스템에 대한 자세한 내용은 [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](./design-multi-drm-system-with-access-control.md)를 참조하세요.

## <a name="understand-the-single-page-app"></a>단일 페이지 앱 이해

플레이어 앱은 다음을 사용하여 Visual Studio Code에서 개발된 SPA(단일 페이지 애플리케이션)입니다.

* ES 6 JavaScript를 사용하는 Node.js
* @azure/msal-browser 2.0 베타
* Azure Media Player SDK
* Azure AD v2 엔드포인트(Microsoft ID 플랫폼)에 대한 OAuth 2 흐름

SPA 플레이어 앱에서 수행하는 작업은 다음과 같습니다.

* 테넌트에 대한 기본 사용자 및 다른 AAD 테넌트 또는 MSA 계정의 게스트 사용자에 대한 사용자 인증. 사용자는 브라우저 팝업 또는 리디렉션(Safari와 같은 팝업을 허용하지 않는 브라우저의 경우)을 통해 로그인하도록 선택할 수 있습니다.
* PKCE를 사용하는 권한 부여 코드 흐름을 통해 `access_token` 획득
* `access_token` 갱신(AAD에서 발급한 토큰은 1시간 동안 유효함), 이 경우 `refresh_token`도 획득됨
* 테스트/검사를 위한 JWT 토큰(`access_token` 및 `id_token` 모두) 구문 분석
* 세 개의 DRM 또는 AES-128 콘텐츠 키 모두에 대한 DRM 라이선스 획득
* DRM, 스트리밍 프로토콜 및 컨테이너 형식의 다양한 조합으로 콘텐츠 스트리밍. 각 조합에 대해 올바른 형식 문자열이 생성됩니다.
* 암호 해독, 디코딩 및 표시
* 문제 해결을 위한 Microsoft Graph API 호출 <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

로그인, 토큰 획득, 토큰 갱신 및 토큰 표시를 수행하는 화면은 다음과 같습니다.

 ![로그인, 토큰 획득, 토큰 갱신 및 토큰 표시를 수행하는 화면](media/aad-ams-content-protection/token-acquisition.png)

JWT 토큰(access_token 또는 id_token)을 구문 분석하는 화면은 다음과 같습니다.

![JWT 토큰을 구문 분석하는 화면](media/aad-ams-content-protection/parsing-jwt-tokens.png)

DRM/AES, 스트리밍 프로토콜 및 컨테이너 형식의 서로 다른 조합을 사용하여 보호된 콘텐츠를 테스트하는 화면은 다음과 같습니다.

![JWT 토큰을 구문 분석하는 화면](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Azure AD 테넌트 선택

> [!NOTE]
> 여기서는 Azure Portal에 로그인했고 하나 이상의 Azure AD 테넌트가 있다고 가정합니다.

엔드투엔드 샘플에 사용할 Azure AD 테넌트를 선택합니다. 다음 두 가지 옵션을 사용할 수 있습니다.

* 기존 Azure AD 테넌트. 모든 Azure 구독에는 하나의 Azure AD 테넌트가 있어야 하지만, 여러 Azure 구독에서 Azure AD 테넌트를 사용할 수 있습니다.
* Azure 구독에서 사용되지 *않는* 새 Azure AD 테넌트. 새 테넌트 옵션을 선택하는 경우 미디어 서비스 계정 및 플레이어 앱 샘플이 별도의 Azure AD 테넌트를 사용하는 Azure 구독에 있어야 합니다. 이렇게 하면 약간의 유연성이 제공됩니다. 예를 들어 고객의 Azure 구독에서 사용자 고유의 Azure AD 테넌트뿐만 아니라 고객의 미디어 서비스 계정도 사용할 수 있습니다.

## <a name="register-the-backend-resource-app"></a>백 엔드 리소스 앱 등록

1. 선택하거나 만든 Azure AD 테넌트로 이동합니다.
1. 메뉴에서 **Azure Active Directory**를 선택합니다.
1. 메뉴에서 **앱 등록**을 선택합니다.
1. **+ 새 등록**을 클릭합니다.
1. 앱의 이름을 *LicenseDeliveryResource2*로 지정합니다. 여기서 2는 AAD v2 엔드포인트를 나타냅니다.
1. **이 조직 디렉터리의 계정만 해당([*테넌트 이름*]만 - 단일 테넌트)** 을 선택합니다. 여러 테넌트에 액세스할 수 있도록 하려면 다른 다중 테넌트 옵션 중 하나를 선택합니다.
1. **리디렉션 URI**는 선택 사항이며 나중에 변경할 수 있습니다.
1. **등록**을 클릭합니다. 앱 등록 보기가 표시됩니다.
1. 메뉴에서 **매니페스트**를 선택합니다. 매니페스트 보기가 표시됩니다.
1. `accessTokenAcceptedVersion`의 값을 *2*(따옴표 없음)로 변경합니다.
1. `groupMembershipClaims`의 값을 *"SecurityGroup"* (따옴표 포함)으로 변경합니다.
1. **저장**을 클릭합니다.
1. 메뉴에서 **API 표시**를 선택합니다. 범위 추가 보기가 표시됩니다. (Azure는 애플리케이션 ID URI를 제공하지만, 변경하려는 경우 애플리케이션 ID URI 필드에서 편집할 수 있습니다.)
1. **저장 후 계속**을 클릭합니다. 보기가 변경됩니다. 아래 표의 '설정' 열에 있는 각 설정에 대해 '값' 열의 값을 입력한 다음, **범위 추가**를 클릭합니다.

| 설정 | 값 | Description |
| ------- | ----- | ----------- |
| 범위 이름 | *DRM.License.Delivery* | 이 API에 대한 액세스가 요청될 때 범위를 표시하는 방법 및 범위가 클라이언트 애플리케이션에 부여된 경우 액세스 토큰에 표시하는 방법입니다. 이는 이 애플리케이션에서 고유해야 합니다. 이름을 생성하는 패턴으로 "resource.operation.constraint"를 사용하는 것이 가장 좋습니다. |
| 동의할 수 있는 사람 | *관리자 및 사용자* | 사용자 동의를 사용하도록 설정된 디렉터리에서 사용자가 이 범위에 동의할 수 있는지 여부를 결정합니다. |
| 관리자 동의 표시 이름 | *DRM 라이선스 배달* | 관리자가 이 범위에 동의할 때 동의 화면에서 범위가 호출되는 항목입니다. |
| 관리자 동의 설명** | *DRM 라이선스 전송 백 엔드 리소스 범위* | 테넌트 관리자가 동의 화면에서 범위를 확장할 때 표시되는 범위에 대한 자세한 설명입니다. |
| 사용자 동의 표시 이름 | *DRM.License.Delivery* | 사용자가 이 범위에 동의할 때 동의 화면에서 범위가 호출되는 항목입니다. |
| 사용자 동의 설명 | *DRM 라이선스 전송 백 엔드 리소스 범위* | 사용자가 동의 화면에서 범위를 확장할 때 표시되는 범위에 대한 자세한 설명입니다. |
| 시스템 상태 | *Enabled* | 클라이언트에서 이 범위를 요청할 수 있는지 여부를 결정합니다. 클라이언트에 표시하지 않으려는 범위에 대해서는 "사용 안 함"으로 설정합니다. 사용하지 않도록 설정된 범위만 삭제할 수 있으며, 클라이언트에서 아직 사용하고 있지 않은지 확인하기 위해 범위를 사용하지 않도록 설정하고 1주일 이상 기다린 후에 삭제하는 것이 좋습니다. |

## <a name="register-the-client-app"></a>클라이언트 앱 등록

1. 선택하거나 만든 Azure AD 테넌트로 이동합니다.
1. 메뉴에서 **Azure Active Directory**를 선택합니다.
1. 메뉴에서 **앱 등록**을 선택합니다.
1. **+ 새 등록**을 클릭합니다.
1. 클라이언트 앱의 이름을 지정합니다(예: *AMS AAD Content Protection*).
1. **이 조직 디렉터리의 계정만 해당([*테넌트 이름*]만 - 단일 테넌트)** 을 선택합니다. 여러 테넌트에 액세스할 수 있도록 하려면 다른 다중 테넌트 옵션 중 하나를 선택합니다.
1. **리디렉션 URI**는 선택 사항이며 나중에 변경할 수 있습니다.
1. **등록**을 클릭합니다.
1. 메뉴에서 **API 권한**을 선택합니다.
1. **+ 권한 추가**를 클릭합니다. API 권한 요청 보기가 열립니다.
1. **내 API** 탭을 클릭하고, 마지막 섹션에서 만든 *LicenseDeliveryResource2* 앱을 선택합니다.
1. DRM 화살표를 클릭하고, *DRM.License.Delivery* 권한을 확인합니다.
1. **권한 추가**를 클릭합니다. 권한 추가 보기가 닫힙니다.
1. 메뉴에서 **매니페스트**를 선택합니다. 매니페스트 보기가 표시됩니다.
1. 다음 값 쌍을 찾아서 `replyUrlsWithType` 특성에 추가합니다.

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > 이 시점에서 플레이어 앱에 대한 URL이 아직 없습니다.  localhost 웹 서버에서 앱을 실행하는 경우 localhost 값 쌍만 사용할 수 있습니다. 플레이어 앱이 배포되면 배포된 URL을 사용하여 항목을 여기에 추가할 수 있습니다.  이렇게 하는 것을 잊은 경우 Azure AD 로그인에서 오류 메시지가 표시됩니다.

1. **저장**을 클릭합니다.
1. 마지막으로, 구성이 올바른지 확인하기 위해 **인증**을 선택합니다.  인증 보기가 표시됩니다. 클라이언트 애플리케이션이 SPA(단일 페이지 앱)로 나열되고, 리디렉션 URI가 나열되며, 권한 부여 유형이 PKCE를 사용하는 권한 부여 코드 흐름입니다.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Media Services 계정의 콘텐츠 키 정책 및 스트리밍 정책 설정

**이 섹션에서는 사용자가 .NET 개발자이며 AMS v3 API 를 사용하는 데 익숙하다고 가정합니다.**

> [!NOTE]
> 이 문서를 작성할 당시에는 OpenID-Config에서 비대칭 토큰 서명 키를 사용하도록 지원하지 않았으므로 Azure Portal은 미디어 서비스 계정 키 정책을 설정하는 데 사용할 수 없었습니다.  Azure AD에서 발급한 토큰은 비대칭 키로 서명되고 키가 6주마다 롤오버되므로 설치 프로그램에서 Azure AD 키 롤오버를 지원해야 합니다. 따라서 이 자습서에서는 .NET 및 AMS v3 API를 사용합니다.

DRM 및 AES-128에 대한 [콘텐츠 키 정책](content-key-policy-concept.md) 및 [스트리밍 정책](streaming-policy-concept.md) 구성이 적용됩니다.  콘텐츠 키 정책에서 `ContentKeyPolicyRestriction`을 변경합니다.

콘텐츠 키 정책 제한을 만드는 .NET 코드는 다음과 같습니다.

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

위의 코드에서 `ida_AADOpenIdDiscoveryDocument`, `ida_audience` 및 `ida_issuer` 값을 변경합니다. Azure Portal에서 이러한 항목에 대한 값을 찾으려면 다음을 수행합니다.

1. 이전에 사용한 AAD 테넌트를 선택하고, 메뉴에서 **앱 등록**을 클릭한 다음, **엔드포인트** 링크를 클릭합니다.
1. **OpenIdConnect 메타데이터 문서** 필드의 값을 선택하여 복사하고, 이를 `ida_AADOpenIdDiscoveryDocument` 값으로 코드에 붙여넣습니다.
1. `ida_audience` 값은 등록된 *LicenseDeliveryResource2* 앱의 애플리케이션(클라이언트) ID입니다.
1. `ida_issuer` 값은 `https://login.microsoftonline.com/[tenant_id]/v2.0` URL입니다. [*tenant_id*]를 테넌트 ID로 바꿉니다.

## <a name="set-up-the-sample-player-app"></a>플레이어 앱 샘플 설정

아직 수행하지 않은 경우 원본 리포지토리([https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad))에서 앱을 복제하거나 다운로드합니다.

플레이어 앱을 설정하는 두 가지 옵션이 있습니다.

* 최소 사용자 지정(client_id, tenant_id 및 스트리밍 URL과 같은 일부 상수 문자열 값만 바꿈)을 수행하지만 Visual Studio Code 및 Node.js를 사용해야 합니다.
* ASP.NET Core와 같은 다른 IDE 및 웹 플랫폼을 사용하려는 경우 플레이어 앱 자체에서 서버 쪽 코드를 사용하지 않으므로 웹 페이지 파일, JavaScript 파일 및 CSS 파일을 프로젝트에 넣을 수 있습니다.

### <a name="option-1"></a>옵션 1

1. Visual Studio Code를 시작합니다.
1. 프로젝트를 열려면 파일 -> 폴더 열기 -> 찾을 대상을 차례로 클릭하여 *package.json* 파일의 부모 폴더를 찾아서 선택합니다.
1. *public/javascript/constants.js* JavaScript 파일을 엽니다.
1. `OAUTH2_CONST.CLIENT_ID`를 AAD 테넌트에 등록된 클라이언트 앱의 `client_id`로 바꿉니다.  `client_id`는 Azure Portal에서 등록된 앱의 [개요] 섹션에서 찾을 수 있습니다. 참고: 이는 개체 ID가 아니라 클라이언트 ID입니다.
1. `OAUTH2_CONST.TENANT_ID`를 Azure AD 테넌트의 `tenant_id`로 바꿉니다. `tenant_id`는 Azure Active Directory 메뉴를 클릭하여 찾을 수 있습니다. tenant_id가 [개요] 섹션에 표시됩니다.
1. `OAUTH2_CONST.SCOPE`를 등록된 클라이언트 앱에 추가한 범위로 바꿉니다. 범위는 **앱 등록** 메뉴에서 등록된 클라이언트 앱으로 이동한 다음, 클라이언트 앱을 선택하여 찾을 수 있습니다.
    1. 클라이언트 앱을 선택하고, **API 권한** 메뉴를 클릭한 다음, *LicenseDeliveryResource2* API 권한 아래에서 *DRM.License.Delivery* 범위를 선택합니다. 권한은 *api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM.License.Delivery*와 같은 형식이어야 합니다. **중요**: `OAUTH2_CONST.SCOPE`에서 `offline_access` 앞에 공백을 유지합니다.
1. 아래 그림과 같이 `AMS_CONST`에 대한 두 개의 상수 문자열을 바꿉니다. 하나는 테스트 자산의 보호된 스트리밍 URL이고, 다른 하나는 FPS 애플리케이션 인증서 URL(FairPlay 테스트 사례를 포함하려는 경우)입니다. 그렇지 않으면 `AMS_CONST.APP_CERT_URL`에 대해 그대로 둘 수 있습니다. 그런 다음 **저장**을 클릭합니다.

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

로컬로 테스트하려면 다음을 수행합니다.

1. VSC(Visual Studio Code)의 주 메뉴에서 **보기**, **터미널**을 차례로 선택합니다.
1. npm을 아직 설치하지 않은 경우 명령 프롬프트에서 `npm install`을 입력합니다.
1. 명령 프롬프트에서 `npm start`를 입력합니다. (npm이 시작되지 않으면 명령 프롬프트에서 `cd npmweb`을 입력하여 디렉터리를 `npmweb`으로 변경합니다.)
1. 브라우저를 사용하여 `http://localhost:3000`으로 이동합니다.

사용하는 브라우저에 따라 로그인 후 테스트할 DRM/AES, 스트리밍 프로토콜 및 컨테이너 형식의 올바른 조합을 선택합니다(`access_token` 획득). macOS의 Safari에서 테스트하는 경우 Safari에서 팝업을 허용하지 않으므로 API 리디렉션 옵션을 선택합니다. 대부분의 다른 브라우저에서는 팝업 및 리디렉션 옵션을 모두 사용할 수 있습니다.

### <a name="option-2"></a>옵션 2

다른 IDE/웹 플랫폼 및/또는 웹 서버(예: 개발 컴퓨터에서 실행되는 IIS)를 사용하려는 경우 다음 파일을 로컬 웹 서버의 새 디렉터리에 복사합니다. 다운로드한 코드에서 찾을 수 있는 경로는 다음과 같습니다.

* *views/index.ejs*(접미사를 .html로 변경)
* *views/jwt.ejs*(접미사를 .html로 변경)
* *views/info.ejs*(접미사를 html로 변경)
* *public/* *(아래와 같은 JavaScript 파일, CSS, 이미지)

1. *view* 폴더에 있는 파일을 새 디렉터리의 루트에 복사합니다.
1. *public* 폴더에 있는 *폴더*를 새 디렉터리의 루트에 복사합니다.
1. 파일의 확장명(`.ejs`)을 `.html`로 변경합니다. (서버 쪽 변수가 사용되지 않으므로 안전하게 변경할 수 있습니다.)
1. VSC(또는 다른 코드 편집기)에서 *index.html*을 열고, 파일이 있는 위치를 반영하도록 `<script>` 및 `<link>` 경로를 변경합니다.  이전 단계를 수행한 경우 경로에서 `\`만 삭제하면 됩니다.  예를 들어 `<script type="text/javascript" src="/javascript/constants.js"></script>`는 `<script type="text/javascript" src="javascript/constants.js"></script>`가 됩니다.
1. 옵션 1에서와 같이 *javascript/constants.js* 파일의 상수를 사용자 지정합니다.

## <a name="common-customer-scenarios"></a>일반적인 고객 시나리오

이제 자습서를 완료하고 작동하는 하위 시스템이 있으므로 다음 고객 시나리오에 맞게 이 하위 시스템을 수정할 수 있습니다.

### <a name="role-based-access-control-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Azure AD 그룹 멤버 자격을 통해 라이선스를 전송하는 RBAC(역할 기반 액세스 제어)

지금까지 이 시스템은 로그인할 수 있는 모든 사용자가 유효한 라이선스를 가져오고 보호된 콘텐츠를 재생할 수 있도록 합니다.

일반적인 고객 요구 사항은 인증된 사용자의 하위 집합에서 콘텐츠를 볼 수 있도록 허용되지만, 다른 사용자(예: 비디오 콘텐츠에 대한 기본 및 프리미엄 구독을 제공하는 고객)는 허용되지 않는다는 것입니다. 기본 구독에 대한 요금을 지불한 고객은 프리미엄 구독이 필요한 콘텐츠를 볼 수 없습니다. 이 요구 사항을 충족하는 데 필요한 추가 단계는 다음과 같습니다.

#### <a name="set-up-the-azure-ad-tenant"></a>Azure AD 테넌트 설정

1. 테넌트에서 두 개의 계정을 설정합니다. *premium_user* 및 *basic_user*라는 이름을 지정할 수 있습니다.
1. 사용자 그룹을 만들고, 이를 *PremiumGroup*이라고 합니다.
1. *premium_user*는 멤버로 *PremiumGroup*에 추가하지만 *basic_user*는 그룹에 추가하지 않습니다.
1. *PremiumGroup*의 **개체 ID**를 적어 둡니다.

#### <a name="set-up-the-media-services-account"></a>Media Services 계정 설정

*groups*라는 클레임을 추가하여 `ContentKeyPolicyRestriction`('미디어 서비스 계정에서 설정'의 위 섹션에 나와 있음)을 수정합니다. 여기서 `ida_EntitledGroupObjectId`의 값은 *PremiumGroup*의 개체 ID입니다.

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

*groups* 클레임은 Azure AD에서 [제한된 클레임 집합](../../active-directory/develop/active-directory-claims-mapping.md#claim-sets)의 멤버입니다.

#### <a name="test"></a>테스트

1. *premium_user* 계정으로 로그인합니다. 보호된 콘텐츠를 재생할 수 있습니다.
1. *basic_user* 계정으로 로그인합니다. 비디오가 암호화되었지만 암호를 해독할 수 있는 키가 없다는 오류 메시지가 표시됩니다. 플레이어 진단 오버레이의 아래쪽에 있는 드롭다운을 사용하여 이벤트, 오류 및 다운로드를 보는 경우 오류 메시지에는 Azure AD 토큰 엔드포인트에서 발급한 JWT에서 그룹 클레임에 대한 클레임 값이 누락되어 라이선스를 획득할 수 없다고 표시됩니다.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>여러 미디어 서비스 계정 지원(여러 구독에서)

고객은 하나 이상의 구독에서 여러 미디어 서비스 계정을 가질 수 있습니다. 예를 들어 고객은 하나의 미디어 서비스 계정을 프로덕션 주 계정으로, 다른 하나를 보조/백업 계정으로, 다른 하나를 개발/테스트용 계정으로 가질 수 있습니다.

모든 미디어 서비스 계정에 `ContentKeyPolicyRestriction`을 만들 때 섹션('미디어 서비스 계정에서 설정')에서 사용한 것과 동일한 매개 변수 집합을 사용하기만 하면 됩니다.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>여러 AAD 테넌트에서 고객, 해당 공급업체 및/또는 자회사 지원

솔루션 사용자인 고객의 자회사, 공급업체/파트너는 `mycustomer.com`, `mysubsidiary.com` 및 `myparther.com`과 같은 서로 다른 AAD 테넌트에 상주할 수 있습니다. 이 솔루션은 `mycustomer.com`과 같은 단일 특정 AAD 테넌트를 기반으로 하지만, 다른 테넌트의 사용자에게도 효과적일 수 있습니다.

`mycustomer.com`을 이 솔루션에 사용하는 경우 `mypartner.com`의 사용자를 게스트 사용자로 `mycustomer.com` 테넌트에 추가합니다. `mypartner.com` 사용자가 게스트 계정을 활성화해야 합니다. 게스트 계정은 다른 AAD 테넌트 또는 `outlook.com` 계정 중 하나일 수 있습니다.

`mycustomer.com`에서 활성화되면 `mypartner.com`의 게스트 사용자는 여전히 자체/원래 AAD 테넌트(`mypartner.com`)를 통해 인증되지만 `access_token`은 `mycustomer.com`에서 발급됩니다.

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>구독/테넌트에서 설정을 사용하여 고객 테넌트/구독 지원

설정을 사용하여 고객의 미디어 서비스 계정/구독에서 보호된 콘텐츠를 테스트할 수 있습니다. 동일한 구독에서 Azure AD 테넌트 및 미디어 서비스 계정을 사용하여 설정합니다. 고객의 미디어 서비스 계정은 자체 Azure AD 테넌트가 포함된 Azure 구독에 있습니다.

1. 고객의 계정을 게스트 계정으로 테넌트에 추가합니다.
1. 고객과의 협력을 통해 '미디어 서비스 계정에서 설정' 섹션에 나열된 세 가지 매개 변수를 제공하여 고객의 미디어 서비스 계정에서 보호된 콘텐츠를 준비합니다.

그런 다음, 고객은 설정으로 이동하여 게스트 계정으로 로그인하고, 보호된 자체 콘텐츠를 테스트할 수 있습니다. 또한 자신의 계정으로 로그인하여 고객의 콘텐츠를 테스트할 수도 있습니다.

솔루션 샘플은 Microsoft 구독을 사용하는 Microsoft 테넌트 또는 사용자 지정 테넌트에서 설정할 수 있습니다. Azure Media Service 인스턴스는 해당 테넌트가 있는 다른 구독에서 제공될 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

> [!WARNING]
> 이 애플리케이션을 계속 사용하지 않으려면 이 자습서를 수행하는 동안 만든 리소스를 삭제합니다. 그렇지 않으면 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: 콘텐츠 암호화](encrypt-content-quickstart.md)
