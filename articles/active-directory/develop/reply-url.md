---
title: 리디렉션 URI & 회신 URL 제한-Microsoft identity platform | Microsoft
description: 회신 Url/리디렉션 Url 제한 & 제한 사항
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 1367bf32eea58b828c00ee23a59a32a2fec699ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983098"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>리디렉션 URI/회신 URL 제한 및 제한 사항

리디렉션 URI 또는 회신 URL은 앱이 성공적으로 인증 되 고 인증 코드 또는 액세스 토큰이 부여 된 후 권한 부여 서버에서 사용자를 전송 하는 위치입니다. 코드 또는 토큰은 리디렉션 URI 또는 회신 토큰에 포함 되어 있으므로 앱 등록 프로세스의 일부로 올바른 위치를 등록 하는 것이 중요 합니다.

 회신 Url에는 다음과 같은 제한 사항이 적용 됩니다.

    * 회신 URL은 `https`스키마로 시작 해야 합니다.
    * 회신 URL은 대/소문자를 구분 합니다. 해당 사례는 실행 중인 응용 프로그램의 URL 경로에 대 한 대/소문자와 일치 해야 합니다. 예를 들어 응용 프로그램의 경로 `.../abc/response-oidc`일부로를 포함 하는 경우 회신 URL에 `.../ABC/response-oidc`를 지정 하지 마십시오. 웹 브라우저에서 경로를 대/소문자를 구분 하므로 `.../abc/response-oidc`와 연결 된 쿠키는 대/소문자가 일치 하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외 될 수 있습니다.
    
## <a name="maximum-number-of-redirect-uris"></a>최대 리디렉션 Uri 수

다음 표에서는 앱을 등록할 때 추가할 수 있는 리디렉션 Uri의 최대 개수를 보여 줍니다.

| 로그인 중인 계정 | 최대 리디렉션 Uri 수 | Description |
|--------------------------|---------------------------------|-------------|
| 모든 조직의 Azure Active Directory (Azure AD) 테넌트에 있는 Microsoft 회사 또는 학교 계정 | 256 | 응용 프로그램 매니페스트의 `signInAudience` 필드가 *AzureADMyOrg* 또는 *AzureADMultipleOrgs* 로 설정 되어 있습니다. |
| 개인 Microsoft 계정 및 회사 및 학교 계정 | 100 | 응용 프로그램 매니페스트의 `signInAudience` 필드가 *AzureADandPersonalMicrosoftAccount* 로 설정 되어 있습니다. |

## <a name="maximum-uri-length"></a>최대 URI 길이

앱 등록에 추가 하는 각 리디렉션 URI에 대해 최대 256 문자를 사용할 수 있습니다.

## <a name="supported-schemes"></a>지원 되는 구성표
현재 Azure AD 응용 프로그램 모델은 모든 조직의 Azure Active Directory (Azure AD) 테넌트에서 Microsoft 회사 또는 학교 계정에 로그인 하는 앱에 대 한 HTTP 및 HTTPS 스키마를 모두 지원 합니다. 응용 프로그램 매니페스트의 `signInAudience` 필드는 *AzureADMyOrg* 또는 *AzureADMultipleOrgs*로 설정 됩니다. 개인 Microsoft 계정 및 회사 및 학교 계정 ( *AzureADandPersonalMicrosoftAccount*로 설정 `signInAudience`)에 로그인 하는 앱의 경우 HTTPS 스키마만 허용 됩니다.

> [!NOTE]
> 새로운 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서는 개발자가 HTTP 스키마를 사용 하 여 UI에 uri를 추가할 수 없습니다. 회사 또는 학교 계정에 로그인 하는 앱에 대 한 HTTP Uri 추가는 앱 매니페스트 편집기를 통해서만 지원 됩니다. 앞으로 새 앱은 리디렉션 URI에서 HTTP 스키마를 사용할 수 없습니다. 그러나 리디렉션 Uri에 HTTP 체계가 포함 된 이전 앱은 계속 작동 합니다. 개발자는 리디렉션 Uri에서 HTTPS 체계를 사용 해야 합니다.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Uri에서 와일드 카드를 사용 하는 제한 사항

`https://*.contoso.com`와 같은 와일드 카드 Uri는 편리 하지만이를 피해 야 합니다. 리디렉션 URI에서 와일드 카드를 사용 하면 보안에 영향을 미칩니다. OAuth 2.0 사양 ([RFC 6749의 3.1.2 섹션](https://tools.ietf.org/html/rfc6749#section-3.1.2))에 따라 리디렉션 엔드포인트 uri는 절대 uri 여야 합니다. 

Azure AD 응용 프로그램 모델은 개인 Microsoft 계정과 회사 또는 학교 계정에 로그인 하도록 구성 된 앱에 대 한 와일드 카드 Uri를 지원 하지 않습니다. 그러나 현재 조직의 Azure AD 테넌트에서 회사 또는 학교 계정에 로그인 하도록 구성 된 앱에는 와일드 카드 Uri를 사용할 수 있습니다. 
 
> [!NOTE]
> 개발자는 새로운 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 UI에 와일드 카드 uri를 추가할 수 없습니다. 회사 또는 학교 계정에 로그인 하는 앱에 대 한 시키려면 와일드 카드 URI 추가는 앱 매니페스트 편집기를 통해서만 지원 됩니다. 앞으로 새 앱은 리디렉션 URI에서 와일드 카드를 사용할 수 없습니다. 그러나 리디렉션 Uri에 와일드 카드가 포함 된 이전 앱은 계속 작동 합니다.

시나리오에 허용 되는 최대 제한 보다 더 많은 리디렉션 Uri가 필요한 경우 와일드 카드 리디렉션 URI를 추가 하는 대신 다음 방법 중 하나를 고려 합니다.

### <a name="use-a-state-parameter"></a>State 매개 변수 사용

여러 하위 도메인을 사용 하는 경우 및 시나리오에서 시작 된 동일한 페이지에 성공적으로 인증 되 면 사용자를 리디렉션해야 하는 경우에는 state 매개 변수를 사용 하는 것이 도움이 될 수 있습니다. 

이 방법의 경우:

1. 권한 부여 엔드포인트에서 수신 하는 보안 토큰을 처리 하는 응용 프로그램당 "공유" 리디렉션 URI를 만듭니다.
1. 응용 프로그램은 state 매개 변수에서 응용 프로그램 특정 매개 변수 (예: 사용자가 시작 하거나 브랜딩 정보와 같은 항목)를 보낼 수 있습니다. State 매개 변수를 사용 하는 경우 [RFC 6749의 섹션 10.12](https://tools.ietf.org/html/rfc6749#section-10.12)에 지정 된 대로 csrf 보호를 보호 합니다. 
1. 응용 프로그램 관련 매개 변수에는 응용 프로그램에서 사용자에 대 한 올바른 환경을 렌더링 하는 데 필요한 모든 정보가 포함 됩니다. 즉, 적절 한 응용 프로그램 상태를 생성 합니다. Azure AD 권한 부여 엔드포인트은 상태 매개 변수에서 HTML을 제거 하므로이 매개 변수에 HTML 콘텐츠를 전달 하지 않도록 합니다.
1. Azure AD가 "shared" 리디렉션 URI에 대 한 응답을 보내면 상태 매개 변수를 다시 응용 프로그램으로 보냅니다.
1. 그런 다음 응용 프로그램은 state 매개 변수의 값을 사용 하 여 사용자를 추가로 보낼 URL을 결정할 수 있습니다. CSRF 보호의 유효성을 검사 해야 합니다.

> [!NOTE]
> 이 접근 방식을 통해 손상 된 클라이언트는 상태 매개 변수에 전송 된 추가 매개 변수를 수정할 수 있으므로 사용자를 다른 URL (RFC 6819에 설명 된 [개방형 리디렉터 위협](https://tools.ietf.org/html/rfc6819#section-4.2.4) )으로 리디렉션합니다. 따라서 클라이언트는 상태를 암호화 하거나 토큰에 대해 리디렉션 URI에서 도메인 이름을 확인 하는 등의 다른 방법으로 상태를 확인 하 여 이러한 매개 변수를 보호 해야 합니다.

### <a name="add-redirect-uris-to-service-principals"></a>서비스 사용자에 게 리디렉션 Uri 추가

또 다른 방법은 모든 Azure AD 테넌트에서 앱 등록을 나타내는 [서비스 주체](app-objects-and-service-principals.md#application-and-service-principal-relationship) 에 리디렉션 uri를 추가 하는 것입니다. 상태 매개 변수를 사용할 수 없거나 시나리오에서 지원 되는 모든 새 테넌트의 앱 등록에 새 리디렉션 Uri를 추가 해야 하는 경우이 방법을 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [응용 프로그램 매니페스트에](reference-app-manifest.md) 대 한 자세한 정보
