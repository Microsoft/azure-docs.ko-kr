---
title: Azure Active Directory 앱 매니페스트 이해 | Microsoft Docs
description: Azure AD 테넌트의 애플리케이션 ID 구성을 나타내고 OAuth 권한 부여, 승인 환경 등을 용이하게 하는 데 사용되는 Azure Active Directory 앱 매니페스트에 대한 자세한 정보를 다룹니다.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: f15bd1fa95cbbd998d23ac6d946db4ecd93b1e65
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703034"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 앱 매니페스트

애플리케이션 매니페스트는 Microsoft ID 플랫폼에 있는 애플리케이션 개체의 모든 특성 정의를 포함합니다. 또한 애플리케이션 개체를 업데이트하기 위한 메커니즘으로도 사용됩니다. 응용 프로그램 엔터티 및 해당 스키마에 대 한 자세한 내용은 [Graph API 응용 프로그램 엔터티 설명서](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)를 참조 하세요.

Azure Portal를 통해 또는 [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) 또는 [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications)을 사용 하 여 프로그래밍 방식으로 앱의 특성을 구성할 수 있습니다. 그러나 앱 매니페스트를 편집하여 앱 특성을 구성해야 하는 경우도 있습니다. 이 시나리오에는 다음이 포함됩니다.

* Azure AD 다중 테넌트 및 개인 Microsoft 계정으로 앱을 등록 한 경우 UI에서 지원 되는 Microsoft 계정을 변경할 수 없습니다. 대신 애플리케이션 매니페스트 편집기를 사용하여 지원되는 계정 유형을 변경해야 합니다.
* 앱이 지원하는 권한 및 역할을 정의해야 하는 경우 애플리케이션 매니페스트를 수정해야 합니다.

## <a name="configure-the-app-manifest"></a>앱 매니페스트 구성

애플리케이션 매니페스트를 구성하려면

1. [Azure 포털](https://portal.azure.com)로 이동합니다. **Azure Active Directory** 서비스를 검색 하 고 선택 합니다.
1. **앱 등록**을 선택합니다.
1. 구성하려는 앱을 선택합니다.
1. 앱의 **개요** 페이지에서 **매니페스트** 섹션을 선택합니다. 웹 기반 매니페스트 편집기가 열리면 포털 내에서 매니페스트를 편집할 수 있습니다. 필요에 따라 **다운로드**를 선택하여 로컬로 매니페스트를 편집하고 **업로드**를 사용하여 애플리케이션에 다시 적용할 수 있습니다.

## <a name="manifest-reference"></a>매니페스트 참조

> [!NOTE]
> **설명** 다음에 **예제 값** 열이 표시되지 않는 경우 브라우저 창을 최대화하고 **예제 값** 열이 표시될 때까지 스크롤/살짝 밉니다.

| 키  | 값 형식 | Description  | 예제 값 |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Nullable Int32 | 리소스에서 필요한 액세스 토큰 버전을 지정합니다. 이 매개 변수는 액세스 토큰을 요청 하는 데 사용 되는 엔드포인트 또는 클라이언트에 독립적으로 생성 된 JWT의 버전 및 형식을 변경 합니다.<br/><br/>사용된 엔드포인트, v1.0 또는 v2.0은 클라이언트에서 선택되고 id_token의 버전에만 영향을 줍니다. 리소스는 `accesstokenAcceptedVersion`을 명시적으로 구성해서 지원되는 액세스 토큰 형식을 지정해야 합니다.<br/><br/>`accesstokenAcceptedVersion`에 가능한 값은 1, 2 또는 null입니다. 값이 null 이면이 매개 변수는 기본적으로 1로 설정 되며,이 값은 v 1.0 엔드포인트에 해당 합니다. <br/><br/>`signInAudience`을 `AzureADandPersonalMicrosoftAccount`경우 값을 `2` 해야 합니다.  | `2` |
| `addIns` | 컬렉션 | 사용하는 서비스가 특정 컨텍스트에서 앱을 호출하는 데 사용할 수 있는 사용자 지정 동작을 정의합니다. 예를 들어 파일 스트림을 렌더링할 수 있는 응용 프로그램은 "FileHandler" 기능에 대 한 addIns 속성을 설정할 수 있습니다. 이 매개 변수를 사용 하면 Office 365 같은 서비스가 사용자가 작업 중인 문서의 컨텍스트에서 응용 프로그램을 호출할 수 있습니다. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | 부울 | 대체 애플리케이션 유형을 지정합니다. Azure AD는 기본적으로 replyUrlsWithType에서 애플리케이션 유형을 유추합니다. Azure AD에서 클라이언트 앱 유형을 확인할 수 없는 특정 시나리오가 있습니다. 예를 들어 이러한 시나리오 중 하나는 URL 리디렉션이 없는 HTTP 요청이 발생 하는 [Ropc](https://tools.ietf.org/html/rfc6749#section-4.3) 흐름입니다. 이러한 경우 Azure AD는이 속성의 값에 따라 응용 프로그램 유형을 해석 합니다. 이 값을 true로 설정하면 대체 애플리케이션 유형이 모바일 디바이스에서 실행 중인 설치된 애플리케이션과 같은 공용 클라이언트로 설정됩니다. 기본값은 false이며, 대체 애플리케이션 유형이 웹앱과 같은 기밀 클라이언트임을 의미합니다. | `false` |
| `availableToOtherTenants` | 부울 | 응용 프로그램이 다른 테넌트와 공유 되는 경우 true입니다. 그렇지 않으면 false입니다. <br><br> _참고:이 기능은 앱 등록 (레거시) 환경 에서만 사용할 수 있습니다. [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 `signInAudience`로 대체 되었습니다._ | |
| `appId` | String | Azure AD가 할당한 앱의 고유 식별자를 지정합니다. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | 컬렉션 | 앱에서 선언할 수 있는 역할의 컬렉션을 지정합니다. 이러한 역할은 사용자, 그룹 또는 서비스 주체에게 할당될 수 있습니다. 더 많은 예제 및 정보는 [애플리케이션에서 앱 역할을 추가하고 토큰에서 수신하기](howto-add-app-roles-in-azure-ad-apps.md)를 참조하세요. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | String | 앱의 표시 이름입니다. <br><br> _참고:이 기능은 앱 등록 (레거시) 환경 에서만 사용할 수 있습니다. [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 `name`로 대체 되었습니다._ | `"MyRegisteredApp"` |
| `errorUrl` | String | 지원 안 됨 | |
| `groupMembershipClaims` | String | 앱에 필요한, 사용자 또는 OAuth 2.0 액세스 토큰에 발급되는 `groups` 클레임을 구성합니다. 이 특성을 설정하려면 다음과 같은 유효한 문자열 값 중 하나를 사용합니다.<br/><br/>- `"None"`<br/>- `"SecurityGroup"`(보안 그룹 및 Azure AD 역할의 경우)<br/>- `"All"`(로그인한 사용자가 속한 모든 보안 그룹, 배포 그룹, Azure AD 디렉터리 역할을 가져옵니다.) | `"SecurityGroup"` |
| `homepage` | String | 응용 프로그램 홈페이지의 URL입니다. <br><br> _참고:이 기능은 앱 등록 (레거시) 환경 에서만 사용할 수 있습니다. [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 `signInUrl`로 대체 되었습니다._ | `"https://MyRegisteredApp"` |
| `objectId` | String | 디렉터리의 앱에 대한 고유 식별자입니다. <br><br> _참고:이 기능은 앱 등록 (레거시) 환경 에서만 사용할 수 있습니다. [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 `id`로 대체 되었습니다._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | String | 이 특정 앱에 대해 보안 토큰 서비스에서 토큰으로 반환한 선택적 클레임입니다.<br>현재 앱 등록 포털을 통해 등록된 Azure AD 및 개인 계정을 모두 지원하는 앱은 선택적 클레임을 사용할 수 없습니다. 그러나 v2.0 엔드포인트를 사용하여 Azure AD에만 등록된 앱은 매니페스트에서 요청한 선택적 클레임을 가져올 수 있습니다. 자세한 내용은 [선택적 클레임](active-directory-optional-claims.md)을 참조하세요. | `null` |
| `id` | String | 디렉터리의 앱에 대한 고유 식별자입니다. 이 ID는 프로토콜 트랜잭션에서 앱을 식별하는 데 사용되는 식별자가 아닙니다. 디렉터리 쿼리의 개체를 참조하는 데 사용됩니다. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | 문자열 배열 | Azure AD 테넌트 안에서 또는 앱이 멀티 테넌트인 경우 확인된 사용자 지정 도메인 안에서 웹앱을 고유하게 식별하는 사용자 정의 URI입니다. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | String | 앱의 서비스 약관 및 개인정보처리방침 링크를 지정합니다. 서비스 약관 및 개인정보처리방침은 사용자 동의 환경을 통해 사용자에게 표시됩니다. 자세한 내용은 [등록된 Azure AD 앱의 서비스 약관 및 개인정보처리방침을 추가하는 방법](howto-add-terms-of-service-privacy-statement.md)을 참조하세요. | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | 컬렉션 | 앱 할당 자격 증명, 문자열 기반 공유 비밀, X.509 인증서에 대한 참조가 들어 있습니다. ). | <code>[<br>&nbsp;{<br>&nbsp;&These credentials are used when requesting access tokens (when the app is acting as a client rather that as resourcenbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | 문자열 배열 | 클라이언트 앱과 사용자 지정 웹 API 앱 등 두 부분을 포함하는 솔루션이 있을 때 동의를 묶는 데 사용됩니다. 클라이언트 앱의 appID를 이 값에 입력하면 사용자는 클라이언트 앱에 대해 한 번만 동의하면 됩니다. Azure AD는 동의 클라이언트에 게 암시적으로 동의 웹 API를 의미 합니다. 클라이언트 및 web API에 대 한 서비스 주체를 동시에 자동으로 프로 비전 합니다. 클라이언트와 웹 API 앱은 모두 동일한 테넌트에 등록되어야 합니다. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | String | 포털에서 업로드된 로고에 대한 CDN URL을 가리키는 읽기 전용 값입니다. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | String | 앱을 로그아웃하는 URL입니다. | `"https://MyRegisteredAppLogout"` |
| `name` | String | 앱의 표시 이름입니다. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | 부울 | 이 웹앱이 OAuth2.0 암시적 흐름 액세스 토큰을 요청할 수 있는지 여부를 지정합니다. 기본값은 false입니다. 이 플래그는 Javascript 단일 페이지 앱과 같은 브라우저 기반 앱에 사용됩니다. 자세히 알아보려면 목차에서 `OAuth 2.0 implicit grant flow`을 입력하여 암시적 흐름에 대한 항목을 참조하세요. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | 부울 | 이 웹앱이 OAuth2.0 암시적 흐름 ID 토큰을 요청할 수 있는지 여부를 지정합니다. 기본값은 false입니다. 이 플래그는 Javascript 단일 페이지 앱과 같은 브라우저 기반 앱에 사용됩니다. | `false` |
| `oauth2Permissions` | 컬렉션 | 웹 API(리소스) 앱을 클라이언트 앱에 노출하는 OAuth 2.0 권한 범위 컬렉션을 지정합니다. 이러한 권한 범위를 동의를 통해 클라이언트 앱에 부여할 수 있습니다. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | 부울 | OAuth 2.0 토큰 요청의 일부로 GET 요청과는 반대로 Azure AD의 POST 요청 허용 여부를 지정합니다. 기본값은 false로, GET 요청만 허용하도록 지정합니다. | `false` |
| `parentalControlSettings` | String | `countriesBlockedForMinors`는 미성년자가 사용할 수 없도록 앱이 차단되는 국가를 지정합니다.<br>`legalAgeGroupRule`은 앱 사용자에게 적용되는 법적 연령 그룹 규칙을 지정합니다. 설정 가능한 값은 `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` 또는 `BlockMinors`입니다.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | 컬렉션 | `keyCredentials`에 대한 설명을 참조하세요. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | 컬렉션 | 암시적 동의에 대해 요청된 사용 권한 및 애플리케이션을 나열합니다. 관리자가 애플리케이션에 대한 동의를 제공한 상태여야 합니다. preAuthorizedApplications는 사용자가 요청된 사용 권한에 동의하지 않아도 됩니다. preAuthorizedApplications에 나열된 사용 권한은 사용자 동의가 필요하지 않습니다. 그러나 preAuthorizedApplications에 나열되지 않은 요청된 추가 사용 권한에는 사용자 동의가 필요합니다. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | 부울 | 이 애플리케이션이 모바일 디바이스에 설치되어 실행되는 애플리케이션 등의 공용 클라이언트인지 여부를 지정합니다. <br><br> _참고:이 속성은 앱 등록 (레거시) 환경 에서만 사용할 수 있습니다. [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 `allowPublicClient`로 대체 되었습니다._ | |
| `publisherDomain` | String | 응용 프로그램에 대해 확인 된 게시자 도메인입니다. 읽기 전용입니다. | https://www.contoso.com |
| `replyUrls` | 문자열 배열 | 이 다중값 속성은 Azure AD가 토큰을 반환할 때 대상으로 허용하는 등록된 redirect_uri 값 목록을 포함합니다. <br><br> _참고:이 속성은 앱 등록 (레거시) 환경 에서만 사용할 수 있습니다. [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 `replyUrlsWithType`로 대체 되었습니다._ | |
| `replyUrlsWithType` | 컬렉션 | 이 다중값 속성은 Azure AD가 토큰을 반환할 때 대상으로 허용하는 등록된 redirect_uri 값 목록을 포함합니다. 각 URI 값에는 연결 된 앱 유형 값이 포함 되어야 합니다. 지원 되는 형식 값은 다음과 같습니다. <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> [ReplyUrl 제한](https://docs.microsoft.com/azure/active-directory/develop/reply-url)사항에 대해 자세히 알아보세요. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | 컬렉션 | 동적 동의를 통해 `requiredResourceAccess`는 관리자 동의 환경 및 정적 동의를 사용하는 사용자 동의 환경을 구동합니다. 그러나이 매개 변수는 일반 사례에 대 한 사용자 동의 환경을 구동 하지 않습니다.<br>`resourceAppId`는 앱이 액세스를 요청하는 대상 리소스의 고유 식별자입니다. 이 값은 대상 리소스 앱에 대해 선언된 appId와 같아야 합니다.<br>`resourceAccess`는 앱이 지정된 리소스에서 요구하는 OAuth2.0 권한 범위 및 앱 역할을 나열하는 배열입니다. 지정된 리소스의 `id` 및 `type` 값을 포함합니다. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | String | 앱의 SAML 메타데이터 URL입니다. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | String | 앱의 홈페이지 URL을 지정합니다. | `https://MyRegisteredApp` |
| `signInAudience` | String | 현재 애플리케이션에 대해 지원되는 Microsoft 계정을 지정합니다. 지원되는 값은 다음과 같습니다.<ul><li>**AzureADMyOrg** -조직의 Azure AD 테넌트 (예: 단일 테넌트)에서 Microsoft 회사 또는 학교 계정이 있는 사용자</li><li>**AzureADMultipleOrgs** -조직의 Azure AD 테넌트 (예: 다중 테넌트)에서 Microsoft 회사 또는 학교 계정이 있는 사용자</li> <li>**AzureADandPersonalMicrosoftAccount** - 임의 조직의 Azure AD 테넌트에서 개인 Microsoft 계정이나 회사 또는 학교 계정이 있는 사용자</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | 문자열 배열 | 애플리케이션을 범주화하고 식별하는 데 사용할 수 있는 사용자 지정 문자열입니다. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>일반 문제

### <a name="manifest-limits"></a>매니페스트 제한

응용 프로그램 매니페스트에는 컬렉션 이라고 하는 여러 특성이 있습니다. 예: approles, keycredentials, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess 및 oauth2Permissions. 모든 응용 프로그램에 대 한 전체 응용 프로그램 매니페스트 내에서 결합 된 모든 컬렉션의 총 항목 수는 1200에 포함 되어 있습니다. 이전에 응용 프로그램 매니페스트에서 100 리디렉션 Uri를 지정 하는 경우 매니페스트를 구성 하는 결합 된 다른 모든 컬렉션에서 사용할 수 있도록 1100 남은 항목을 남겨 둘 수 있습니다.

> [!NOTE]
> 응용 프로그램 매니페스트에서 1200 개를 초과 하는 항목을 추가 하려는 경우 **"응용 프로그램을 업데이트 하지 못했습니다 xxxxxx 오류가 표시 될 수 있습니다. 오류 세부 정보: 매니페스트의 크기가 제한을 초과 했습니다. 값의 수를 줄이고 요청을 다시 시도 하세요. "**

### <a name="unsupported-attributes"></a>지원 되지 않는 특성

응용 프로그램 매니페스트는 Azure AD에서 기본 응용 프로그램 모델의 스키마를 나타냅니다. 기본 스키마가 진화 함에 따라 새 스키마를 반영 하도록 매니페스트 편집기가 업데이트 됩니다. 따라서 응용 프로그램 매니페스트에 새 특성이 표시 되는 것을 볼 수 있습니다. 드물지만 기존 특성에 구문 또는 의미 체계가 변경 되거나 이전에 존재 했던 특성이 더 이상 지원 되지 않는 경우가 있습니다. 예를 들어 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908)에 새 특성이 표시 됩니다 .이 특성은 앱 등록 (레거시) 환경에서 다른 이름으로 알려져 있습니다.


| 앱 등록 (레거시)| 앱 등록           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

이러한 특성에 대 한 설명은 [매니페스트 참조](#manifest-reference) 섹션을 참조 하세요.

이전에 다운로드 한 매니페스트를 업로드 하려고 하면 다음 오류 중 하나가 표시 될 수 있습니다. 매니페스트 편집기에서 최신 버전의 스키마를 지원 하기 때문에이 오류가 발생할 수 있습니다 .이는 업로드 하려는 스키마와 일치 하지 않습니다.

- "**Xxxxxx 응용 프로그램을 업데이트 하지 못했습니다. 오류 세부 정보: 개체 식별자 ' undefined '이 (가) 잘못 되었습니다. [].** "
- "**Xxxxxx 응용 프로그램을 업데이트 하지 못했습니다. 오류 세부 정보: 지정 된 하나 이상의 속성 값이 잘못 되었습니다. [].** "
- "**Xxxxxx 응용 프로그램을 업데이트 하지 못했습니다. 오류 세부 정보:이 api 버전에서 업데이트에 대해 availableToOtherTenants을 설정할 수 없습니다. [].** "
- "**Xxxxxx 응용 프로그램을 업데이트 하지 못했습니다. 오류 정보:이 응용 프로그램에 대해 ' replyUrls ' 속성을 업데이트할 수 없습니다. 대신 ' replyUrlsWithType ' 속성을 사용 하세요. [].** "
- "**Xxxxxx 응용 프로그램을 업데이트 하지 못했습니다. 오류 세부 정보: 형식 이름이 없는 값을 찾을 수 없습니다. 사용할 수 있는 형식이 없습니다. 모델을 지정 하는 경우 페이로드의 각 값은 페이로드에서 명시적으로 지정 하거나 부모 값에서 암시적으로 유추 될 수 있는 형식 이어야 합니다. []** "

이러한 오류 중 하나가 표시 되 면 다음 작업을 수행 하는 것이 좋습니다.

1. 이전에 다운로드 한 매니페스트를 업로드 하지 않고 매니페스트 편집기에서 특성을 개별적으로 편집 합니다. 관심 있는 특성을 성공적으로 편집할 수 있도록 [매니페스트 참조](#manifest-reference) 테이블을 사용 하 여 이전 및 새 특성의 구문 및 의미 체계를 이해 합니다. 
1. 워크플로에서 나중에 사용할 수 있도록 매니페스트를 소스 리포지토리에 저장 해야 하는 경우 **앱 등록** 환경에서 볼 수 있는 것과 함께 저장 된 매니페스트를 리포지토리에의 기준 주소 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 앱의 응용 프로그램 및 서비스 주체 개체 간의 관계에 대 한 자세한 내용은 [AZURE AD의 응용 프로그램 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조 하세요.
* [Microsoft id 플랫폼 개발자 용어집](developer-glossary.md) 에서 일부 핵심 microsoft id 플랫폼 개발자 개념에 대 한 정의를 참조 하세요.

다음 의견 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖는 데 도움을 줄 수 있습니다.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
