---
title: 리디렉션 URI (회신 URL) 제한 사항 | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft id 플랫폼에서 적용 하는 리디렉션 URI (회신 URL) 형식에 대 한 제한 및 제한 사항에 대 한 설명입니다.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 08/07/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8be13a299de0fc3de0acaf0001722d8c96a460e6
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88205936"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>리디렉션 URI (회신 URL) 제한 사항 및 제한 사항

리디렉션 URI 또는 회신 URL은 앱이 성공적으로 인증 되 고 인증 코드 또는 액세스 토큰이 부여 된 후 권한 부여 서버에서 사용자를 전송 하는 위치입니다. 권한 부여 서버는 코드 또는 토큰을 리디렉션 URI로 보내기 때문에 앱 등록 프로세스의 일부로 올바른 위치를 등록 하는 것이 중요 합니다.

 리디렉션 URI에는 다음 제한 사항이 적용됩니다.

* 리디렉션 URI는 스키마로 시작 해야 합니다 `https` .

* 리디렉션 URI는 대/소문자를 구분 합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 예를 들어 응용 프로그램이 경로의 일부로를 포함 하는 경우 `.../abc/response-oidc` 리디렉션 URI에를 지정 하지 마십시오 `.../ABC/response-oidc` . 웹 브라우저는 경로를 대/소문자 구분으로 처리하므로 `.../abc/response-oidc`와 연결된 쿠키는 대/소문자가 일치하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외될 수 있습니다.

## <a name="maximum-number-of-redirect-uris"></a>최대 리디렉션 URI 수

다음 표에서는 Microsoft id 플랫폼에서 앱 등록에 추가할 수 있는 리디렉션 Uri의 최대 개수를 보여 줍니다.

| 로그인한 계정 | 최대 리디렉션 URI 수 | Description |
|--------------------------|---------------------------------|-------------|
| 모든 조직의 Azure AD(Azure Active Directory) 테넌트에 있는 Microsoft 회사 또는 학교 계정 | 256 | 애플리케이션 매니페스트의 `signInAudience` 필드가 *AzureADMyOrg* 또는 *AzureADMultipleOrgs*로 설정됨 |
| 개인 Microsoft 계정 및 회사/학교 계정 | 100 | 애플리케이션 매니페스트의 `signInAudience` 필드가 *AzureADandPersonalMicrosoftAccount*로 설정됨 |

## <a name="maximum-uri-length"></a>최대 URI 길이

앱 등록에 추가 하는 각 리디렉션 URI에 대해 최대 256 문자를 사용할 수 있습니다.

## <a name="supported-schemes"></a>지원되는 스키마

Azure Active Directory (Azure AD) 응용 프로그램 모델은 현재 조직의 Azure AD 테 넌 트에서 회사 또는 학교 계정에 로그인 하는 앱에 대해 HTTP 및 HTTPS 스키마를 모두 지원 합니다. 이러한 계정 유형은 `AzureADMyOrg` `AzureADMultipleOrgs` `signInAudience` 응용 프로그램 매니페스트의 필드에서 및 값으로 지정 됩니다. 개인 Microsoft 계정 (MSA) *및* 회사 및 학교 계정에 로그인 하는 앱의 경우 (즉, `signInAudience` 가로 설정 된 경우 `AzureADandPersonalMicrosoftAccount` ) HTTPS 스키마만 허용 됩니다.

회사 또는 학교 계정에 로그인 하는 앱 등록에 HTTP 체계를 사용 하 여 리디렉션 Uri를 추가 하려면 Azure Portal의 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 에서 응용 프로그램 매니페스트 편집기를 사용 해야 합니다. 그러나 매니페스트 편집기를 사용 하 여 HTTP 기반 리디렉션 URI를 설정할 수 있지만, 리디렉션 Uri에는 HTTPS 체계를 사용 하는 *것이 좋습니다* .

## <a name="localhost-exceptions"></a>Localhost 예외

[RFC 8252 섹션 8.3](https://tools.ietf.org/html/rfc8252#section-8.3) 및 [7.3](https://tools.ietf.org/html/rfc8252#section-7.3), "루프백" 또는 "localhost" 리디렉션 uri는 다음과 같은 두 가지 특별 한 고려 사항과 함께 제공 됩니다.

1. `http` 리디렉션이 장치를 벗어날 수 없기 때문에 URI 체계가 허용 됩니다. 이러한 두 가지 경우 모두 사용할 수 있습니다.
    - `http://127.0.0.1/myApp`
    - `https://127.0.0.1/myApp`
1. 기본 응용 프로그램에서 사용 되는 사용 후 삭제 포트 범위 때문에 포트 구성 요소 (예: `:5001` 또는 `:443` )는 리디렉션 URI 일치를 위해 무시 됩니다. 따라서 이러한 모든 항목은 동일 하 게 간주 됩니다.
    - `http://127.0.0.1/MyApp`
    - `http://127.0.0.1:1234/MyApp`
    - `http://127.0.0.1:5000/MyApp`
    - `http://127.0.0.1:8080/MyApp`

개발 관점에서이는 다음과 같은 몇 가지 것을 의미 합니다.

* 포트가 서로 다른 경우에는 여러 리디렉션 Uri를 등록 하지 마십시오. 로그인 서버는 임의의 항목을 임의로 선택 하 고 해당 리디렉션 URI와 연결 된 동작을 사용 합니다 (예: `web` -, `native` -또는 `spa` -type 리디렉션).
* 호스트에서 여러 리디렉션 Uri를 등록 하 여 개발 중에 서로 다른 흐름을 테스트 해야 하는 경우에는 URI의 *경로* 구성 요소를 사용 하 여 구분 합니다. 예를 들어,는 `http://127.0.0.1/MyWebApp` 와 일치 하지 않습니다 `http://127.0.0.1/MyNativeApp` .
* RFC 지침에 따라 리디렉션 URI에서를 사용 하면 안 `localhost` 됩니다. 대신 실제 루프백 IP 주소를 사용 `127.0.0.1` 합니다. 이렇게 하면 잘못 구성 된 방화벽 또는 이름이 바뀐 네트워크 인터페이스로 인해 앱이 중단 되지 않습니다.

    IPv6 루프백 주소 ( `[::1]` )는 현재 지원 되지 않습니다.

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>리디렉션 Uri의 와일드 카드에 대 한 제한 사항

와 같은 와일드 카드 Uri는 `https://*.contoso.com` 편리 하 게 보일 수 있지만 보안의 영향을 받을 수 있으므로 피해 야 합니다. OAuth 2.0 사양([RFC 6749의섹션 3.1.2](https://tools.ietf.org/html/rfc6749#section-3.1.2))에 따라 리디렉션 엔드포인트 URI는 절대 URI여야 합니다.

와일드 카드 Uri는 현재 개인 Microsoft 계정과 회사 또는 학교 계정에 로그인 하도록 구성 된 앱 등록에서 지원 되지 않습니다. 그러나 와일드 카드 Uri는 조직의 Azure AD 테 넌 트에서 회사 또는 학교 계정에만 로그인 하도록 구성 된 앱에 허용 됩니다.

회사 또는 학교 계정에 로그인 하는 앱 등록에 와일드 카드를 사용 하 여 리디렉션 Uri를 추가 하려면 Azure Portal의 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 에서 응용 프로그램 매니페스트 편집기를 사용 해야 합니다. 매니페스트 편집기를 사용 하 여 와일드 카드를 사용 하 여 리디렉션 URI를 설정할 수 있지만 [RFC 6749의 3.1.2 섹션](https://tools.ietf.org/html/rfc6749#section-3.1.2) 을 준수 하 고 절대 uri만 사용 하는 *것이 좋습니다* .

시나리오에 허용 되는 최대 제한 보다 더 많은 리디렉션 Uri가 필요한 경우 와일드 카드 리디렉션 URI를 추가 하는 대신 [다음 방법을](#use-a-state-parameter) 고려 합니다.

### <a name="use-a-state-parameter"></a>상태 매개 변수 사용

여러 하위 도메인이 있고 시나리오에 필요한 경우 인증에 성공 하면 사용자가 시작 된 동일한 페이지로 사용자를 리디렉션하여 상태 매개 변수를 사용 하는 것이 도움이 될 수 있습니다.

이 방법의 경우 다음을 수행합니다.

1. 애플리케이션당 "공유" 리디렉션 URI를 만들어 권한 부여 엔드포인트에서 수신하는 보안 토큰을 처리합니다.
1. 응용 프로그램은 state 매개 변수에서 응용 프로그램 특정 매개 변수 (예: 사용자가 시작 하거나 브랜딩 정보와 같은 항목을 가진 하위 도메인 URL)를 보낼 수 있습니다. 상태 매개 변수를 사용하는 경우 [RFC 6749의 섹션 10.12](https://tools.ietf.org/html/rfc6749#section-10.12)에 지정된 대로 CSRF 보호를 제공합니다.
1. 애플리케이션 관련 매개 변수에는 애플리케이션이 사용자에 대한 올바른 환경을 렌더링하는 데 필요한, 즉 적절한 애플리케이션 상태를 생성하는 데 필요한 모든 정보가 포함됩니다. Azure AD 권한 부여 엔드포인트는 상태 매개 변수에서 HTML을 제거하므로 이 매개 변수에 HTML 콘텐츠를 전달하지 않아야 합니다.
1. Azure AD가 "공유" 리디렉션 URI에 대한 응답을 보내면 이 URI가 상태 매개 변수를 다시 애플리케이션으로 보냅니다.
1. 그런 다음 애플리케이션은 상태 매개 변수의 값을 사용하여 사용자를 추가로 보낼 URL을 결정할 수 있습니다. CSRF 보호의 유효성을 검사해야 합니다.

> [!WARNING]
> 이 접근 방식에서는 손상된 클라이언트가 상태 매개 변수에 전송된 추가 매개 변수를 수정하여 사용자를 다른 URL로 리디렉션할 수 있습니다. 이것이 RFC 6819에 설명된 [개방형 리디렉터 위협](https://tools.ietf.org/html/rfc6819#section-4.2.4)입니다. 따라서 클라이언트는 토큰에 대해 리디렉션 URI에서 도메인 이름의 유효성을 검사 하는 것과 같이 상태를 암호화 하거나 다른 방법으로 확인 하 여 이러한 매개 변수를 보호 해야 합니다.

## <a name="next-steps"></a>다음 단계

앱 등록 [응용 프로그램 매니페스트에](reference-app-manifest.md)대해 알아봅니다.
