---
title: Microsoft ID 플랫폼의 OAuth 2.0 및 OpenID Connect 프로토콜 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼 엔드포인트에서 지원하는 OAuth 2.0 및 OpenID Connect 프로토콜에 대한 가이드입니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 12edbcda7354d9d6d4b03ebe32304d988b2eb579
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751450"
---
# <a name="oauth-20-and-openid-connect-protocols-on-microsoft-identity-platform"></a>Microsoft ID 플랫폼의 OAuth 2.0 및 OpenID Connect 프로토콜

Openid connect에 대 한 Microsoft id 플랫폼 끝점은 OIDC (업계 표준 프로토콜) 및 OAuth 2.0를 사용 하 여 인증 및 권한 부여를 구현 합니다. 서비스는 표준을 준수하지만 이러한 프로토콜의 두 구현 간에는 약간의 차이가 있을 수 있습니다. 여기에 있는 정보는 [오픈 소스 라이브러리](reference-v2-libraries.md) 중 하나를 사용하는 대신 HTTP 요청을 직접 전송 및 처리하여 코드를 작성하거나 타사 오픈 소스 라이브러리를 사용하도록 선택한 경우에 유용합니다.

## <a name="the-basics"></a>기본 사항

거의 모든 OAuth 2.0 및 OpenID Connect 흐름에서 교환에 참여하는 다음 네 가지 요소가 있습니다.

![OAuth 2.0 역할을 보여주는 다이어그램](./media/active-directory-v2-flows/protocols-roles.svg)

* **권한 부여 서버**는 Microsoft ID 플랫폼 엔드포인트이며 사용자 ID를 확인하고 리소스에 대한 액세스 권한을 부여 및 해지하고, 토큰을 발급합니다. 권한 부여 서버는 id 공급자 라고도 하며, 사용자 정보, 사용자의 액세스 권한 및 흐름의 파티 간 트러스트 관계를 사용 하 여 수행 하는 모든 작업을 안전 하 게 처리 합니다.
* **리소스 소유자**는 일반적으로 최종 사용자입니다. 데이터를 소유 하 고 클라이언트가 해당 데이터 또는 리소스에 액세스할 수 있도록 지 원하는 기능입니다.
* **OAuth 클라이언트**는 해당 애플리케이션 ID로 식별되는 앱입니다. 일반적으로 OAuth 클라이언트는 최종 사용자가 상호 작용하는 요소이며 권한 부여 서버의 토큰을 요청합니다. 리소스 소유자가 리소스에 액세스할 수 있는 권한을 클라이언트에 부여해야 합니다.
* **리소스 서버** 는 리소스 또는 데이터가 있는 곳입니다. OAuth 클라이언트를 안전하게 인증하고 권한을 부여할 수 있도록 권한 부여 서버를 신뢰하고 전달자 액세스 토큰을 사용하여 리소스에 대한 액세스 권한을 부여할 수 있도록 합니다.

## <a name="app-registration"></a>앱 등록

OAuth 2.0 또는 OpenID Connect를 사용하여 해당 사용자에게 로그인하려면 개인 및 회사 또는 학교 계정을 모두 수락하려는 모든 앱을 [Azure Portal](https://aka.ms/appregistrations)의 **앱 등록** 환경을 통해 등록해야 합니다. 앱 등록 프로세스는 몇 개의 값을 수집하고 앱에 할당합니다.

* 앱을 고유하게 식별하는 **애플리케이션 ID**
* 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI**(선택 사항)
* 다른 몇 가지 시나리오 관련 값.

자세한 내용은 [앱 등록](quickstart-register-app.md)방법을 참조하세요.

## <a name="endpoints"></a>엔드포인트

등록되면 앱이 엔드포인트에 요청을 보내어 Microsoft ID 플랫폼과 통신합니다.

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

여기서 `{tenant}` 은 네 개의 서로 다른 값 중 하나를 가질 수 있습니다.

| 값 | Description |
| --- | --- |
| `common` | 개인 Microsoft 계정과 Azure AD의 회사/학교 계정이 모두 있는 사용자가 애플리케이션에 로그인할 수 있습니다. |
| `organizations` | Azure AD의 회사/학교 계정이 있는 사용자만 애플리케이션에 로그인할 수 있습니다. |
| `consumers` | 개인 Microsoft 계정(MSA)이 있는 사용자만이 애플리케이션에 로그인 할 수 있습니다. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 또는 `contoso.onmicrosoft.com` | 특정 Azure AD 테넌트의 회사/학교 계정이 있는 사용자만 애플리케이션에 로그인할 수 있습니다. Azure AD 테넌트의 친숙한 도메인 이름 또는 테넌트의 GUID 식별자를 사용할 수 있습니다. |

이러한 엔드포인트와 상호 작용하는 방법을 알아보려면 [프로토콜](#protocols) 섹션에서 특정 앱 형식을 선택하고 자세한 정보 링크를 따릅니다.

> [!TIP]
> Azure AD에 등록된 모든 앱은 개인 계정에 로그인하지 않아도 Microsoft ID 플랫폼 엔드포인트를 사용할 수 있습니다.  이러한 방식으로 애플리케이션을 다시 만들지 않고도 기존 애플리케이션을 Microsoft ID 플랫폼 및 [MSAL](reference-v2-libraries.md)로 마이그레이션할 수 있습니다.

## <a name="tokens"></a>토큰

OAuth 2.0 및 Openid connect Connect는 일반적으로 [Jwts (JSON 웹 토큰)](https://tools.ietf.org/html/rfc7519)로 표현 되는 **전달자 토큰**을 광범위 하 게 사용 합니다. 전달자 토큰은 보호된 리소스에 대한 "전달자" 액세스 권한을 부여하는 간단한 보안 토큰입니다. 이러한 점에서 "전달자"는 토큰의 복사본을 가져오는 모든 사용자입니다. 당사자는 전달자 토큰을 받으려면 먼저 Microsoft ID 플랫폼을 사용하여 인증해야 하지만 전송 및 스토리지에서 토큰 보안을 유지하는 데 필요한 단계를 취하지 않으면 의도하지 않은 당사자가 토큰을 가로채서 사용할 수 있습니다. 일부 보안 토큰에는 권한 없는 당사자가 사용하는 것을 방지하는 기본 제공 메커니즘이 있지만, 전달자 토큰에는 이러한 메커니즘이 없으므로 전송 계층 보안(HTTPS)과 같은 보안 채널에서 전달자 토큰을 전송해야 합니다. 전달자 토큰이 보안되지 않은 상태로 전송되는 경우, 악의적 사용자가 메시지 가로채기(man-in-the-middle) 공격으로 토큰을 획득하고 보호된 리소스에 무단으로 액세스하는 데 이 토큰을 사용할 수 있습니다. 나중에 사용하기 위해 전달자 토큰을 저장하거나 캐싱할 때도 동일한 보안 원칙이 적용됩니다. 항상 앱이 안전한 방식으로 전달자 토큰을 전송하고 저장하도록 합니다. 전달자 토큰의 보안 고려 사항을 자세히 알아보려면 [RFC 6750 Section 5](https://tools.ietf.org/html/rfc6750)를 참조하세요.

OAuth 2.0/OIDC에서 주로 세 가지 유형의 토큰이 사용 됩니다.

* [액세스 토큰](access-tokens.md) -클라이언트에 부여 된 권한을 포함 하 여 리소스 서버가 클라이언트에서 받는 토큰입니다.  
* [ID 토큰](id-tokens.md) -클라이언트가 권한 부여 서버에서 수신 하는 토큰으로, 사용자를 로그인 하 고 해당 사용자에 대 한 기본 정보를 얻는 데 사용 됩니다.
* 새로 고침 토큰-시간에 따라 새 액세스 및 ID 토큰을 가져오기 위해 클라이언트에서 사용 합니다.  이러한 문자열은 불투명 문자열이 며 권한 부여 서버 에서만 이해할 수 있습니다.

## <a name="protocols"></a>프로토콜

몇 가지 예제 요청을 볼 준비가 되 면 아래 프로토콜 문서 중 하나를 사용 하 여 시작 하세요. 각각 특정 인증 시나리오에 해당합니다. 사용자에게 적합한 흐름을 결정하는 데 도움이 필요하면 [Microsoft ID 플랫폼으로 빌드할 수 있는 앱 형식](v2-app-types.md)을 확인하세요.

* [OAuth 2.0를 사용 하 여 모바일, 네이티브 및 웹 응용 프로그램 빌드](v2-oauth2-auth-code-flow.md)
* [Openid connect Connect를 사용 하 여 사용자 로그인](v2-protocols-oidc.md)
* [OAuth 2.0 클라이언트 자격 증명 흐름으로 디먼 또는 서버 쪽 프로세스 빌드](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 On Behalf Of 흐름으로 웹 API에서 토큰 가져오기](v2-oauth2-on-behalf-of-flow.md)
* [OAuth 2.0 암시적 흐름을 사용 하 여 단일 페이지 앱 빌드](v2-oauth2-implicit-grant-flow.md)
