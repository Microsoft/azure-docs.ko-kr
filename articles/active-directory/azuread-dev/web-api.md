---
title: Azure Active Directory의 Web API 앱
description: 웹 API 애플리케이션을 소개하고 이 앱 유형에 대한 프로토콜 흐름, 등록 및 토큰 만료 기본 사항을 설명합니다.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 93e487063944801129090d6b9952143b8df887da
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163916"
---
# <a name="web-api"></a>Web API

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web API 앱은 웹 API에서 리소스를 가져와야 하는 웹 애플리케이션입니다. 이 시나리오에는 웹 애플리케이션이 웹 API를 인증하고 호출하는 데 사용할 수 있는 다음 두 가지 ID 형식이 있습니다.

- **애플리케이션 ID** - 이 시나리오에서는 OAuth 2.0 클라이언트 자격 증명 권한 부여를 사용하여 애플리케이션으로 인증하고 웹 API에 액세스합니다. 애플리케이션 ID를 사용할 때 웹 API는 사용자에 대한 정보를 수신하지 않기 때문에 웹 애플리케이션의 호출만 검색할 수 있습니다. 애플리케이션이 사용자에 대한 정보를 수신하는 경우 이 정보는 Azure AD에 의해 서명되지 않고 애플리케이션 프로토콜을 통해 전송됩니다. 웹 API는 웹 애플리케이션이 사용자를 인증한 것으로 트러스트합니다. 이런 이유로, 이 패턴을 신뢰할 수 있는 하위 시스템이라고 합니다.
- **위임된 사용자 ID** - 기밀 클라이언트에서 OpenID Connect 및 OAuth 2.0 인증 코드 권한 부여의 두 가지 방법으로 시나리오를 완성할 수 있습니다. 웹 애플리케이션은 사용자에 대한 액세스 토큰을 가져옵니다. 그러면 사용자가 성공적으로 웹 애플리케이션에 인증되었으며 웹 애플리케이션이 웹 API 호출을 위한 위임된 사용자 ID를 획득했다는 것이 웹 API에 입증됩니다. 이 액세스 토큰은 웹 API에 대한 요청으로 전송됩니다. 그러면 사용자가 인증되고 원하는 리소스가 반환됩니다.

애플리케이션 ID 및 위임된 사용자 ID 형식에 대해 아래 흐름에서 설명합니다. 이러한 ID 사이의 주요 차이점은 위임된 사용자 ID에서 먼저 인증 코드를 획득해야 사용자가 로그인하여 웹 API에 액세스할 수 있다는 것입니다.

## <a name="diagram"></a>다이어그램

![웹 애플리케이션-Web API 다이어그램](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>프로토콜 흐름

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>OAuth 2.0 클라이언트 자격 증명 권한 부여를 사용하는 애플리케이션 ID

1. 사용자가 웹 애플리케이션에서 Azure AD에 로그인합니다(자세한 내용은 **웹앱** 섹션 참조).
1. 웹 애플리케이션이 웹 API에 인증하고 원하는 리소스를 검색할 수 있도록 액세스 토큰을 획득해야 합니다. Azure AD의 토큰 엔드포인트에 요청하고 자격 증명, 애플리케이션 ID, 웹 API의 애플리케이션 ID URI를 제공합니다.
1. Azure AD가 애플리케이션을 인증하고 웹 API를 호출하는 데 사용되는 JWT 액세스 토큰을 반환합니다.
1. HTTPS를 통해 웹 애플리케이션이 반환된 JWT 액세스 토큰을 사용해서 웹 API에 대한 요청의 권한 부여 헤더에 “전달자”를 지정한 JWT 문자열을 추가합니다. 그러면 웹 API에서 JWT 토큰의 유효성을 검사하여 유효성 검사가 성공하면 원하는 리소스를 반환합니다.

### <a name="delegated-user-identity-with-openid-connect"></a>OpenID Connect를 사용하는 위임된 사용자 ID

1. 사용자가 Azure AD를 사용하여 웹 애플리케이션에 로그인합니다(위의 웹 브라우저-웹 애플리케이션 섹션 참조). 웹 애플리케이션 사용자가 웹 애플리케이션이 사용자를 대신하여 웹 API를 호출할 수 있도록 동의하지 않은 경우 동의해야 합니다. 애플리케이션에 필요한 권한이 표시됩니다. 이 권한 중 하나라도 관리자 수준 권한인 경우 디렉터리의 일반 사용자는 동의할 수 없습니다. 이러한 동의 프로세스는 단일 테넌트 애플리케이션이 아니라 다중 테넌트 애플리케이션에만 적용됩니다(애플리케이션에 필수 권한이 이미 있으므로). 사용자가 로그인할 때 웹 애플리케이션은 사용자에 대한 정보가 포함된 ID 토큰과 인증 코드를 수신했습니다.
1. Azure AD에서 발급된 인증 코드를 사용하여 웹 애플리케이션은 인증 코드, 클라이언트 애플리케이션에 대한 정보(애플리케이션 ID 및 리디렉션 URI), 원하는 리소스(웹 API에 대한 애플리케이션 ID URI) 등이 포함된 요청을 Azure AD의 토큰 엔드포인트에 보냅니다.
1. 웹 애플리케이션 및 웹 API에 대한 정보와 인증 코드는 Azure AD에서 유효성이 검사됩니다. 유효성 검사가 성공하면 Azure AD는 JWT 액세스 토큰과 JWT 새로 고침 토큰 등 두 가지 토큰을 반환합니다.
1. HTTPS를 통해 웹 애플리케이션이 반환된 JWT 액세스 토큰을 사용해서 웹 API에 대한 요청의 권한 부여 헤더에 “전달자”를 지정한 JWT 문자열을 추가합니다. 그러면 웹 API에서 JWT 토큰의 유효성을 검사하여 유효성 검사가 성공하면 원하는 리소스를 반환합니다.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>OAuth 2.0 인증 코드 권한 부여를 사용하는 위임된 사용자 ID

1. 인증 메커니즘이 Azure AD와 독립적인 웹 애플리케이션에 사용자가 이미 로그인되어 있습니다.
1. 웹 애플리케이션이 액세스 토큰을 획득하려면 인증 코드가 필요하므로, 웹 애플리케이션이 브라우저를 통해 Azure AD의 권한 부여 엔드포인트에 대한 요청을 발급하고 성공적인 인증 후에 애플리케이션 ID 및 웹 애플리케이션의 리디렉션 URI를 제공합니다. 사용자가 Azure AD에 로그인합니다.
1. 웹 애플리케이션 사용자가 웹 애플리케이션이 사용자를 대신하여 웹 API를 호출할 수 있도록 동의하지 않은 경우 동의해야 합니다. 애플리케이션에 필요한 권한이 표시됩니다. 이 권한 중 하나라도 관리자 수준 권한인 경우 디렉터리의 일반 사용자는 동의할 수 없습니다. 이 동의는 단일 및 다중 테넌트 애플리케이션 모두에 적용됩니다. 단일 테넌트의 경우 관리자가 사용자 대신 관리자 동의를 수행할 수 있습니다. 이 작업은 `Grant Permissions`Azure Portal[에서 ](https://portal.azure.com) 단추를 사용하여 수행할 수 있습니다. 
1. 사용자가 동의한 후에는 웹 애플리케이션이 액세스 토큰을 획득하는 데 필요한 인증 코드를 수신합니다.
1. Azure AD에서 발급된 인증 코드를 사용하여 웹 애플리케이션은 인증 코드, 클라이언트 애플리케이션에 대한 정보(애플리케이션 ID 및 리디렉션 URI), 원하는 리소스(웹 API에 대한 애플리케이션 ID URI) 등이 포함된 요청을 Azure AD의 토큰 엔드포인트에 보냅니다.
1. 웹 애플리케이션 및 웹 API에 대한 정보와 인증 코드는 Azure AD에서 유효성이 검사됩니다. 유효성 검사가 성공하면 Azure AD는 JWT 액세스 토큰과 JWT 새로 고침 토큰 등 두 가지 토큰을 반환합니다.
1. HTTPS를 통해 웹 애플리케이션이 반환된 JWT 액세스 토큰을 사용해서 웹 API에 대한 요청의 권한 부여 헤더에 “전달자”를 지정한 JWT 문자열을 추가합니다. 그러면 웹 API에서 JWT 토큰의 유효성을 검사하여 유효성 검사가 성공하면 원하는 리소스를 반환합니다.

## <a name="code-samples"></a>코드 샘플

웹 애플리케이션-Web API 시나리오에 대한 코드 샘플을 참조하세요. 그리고 새로운 샘플이 자주 추가되므로 자주 확인해 보세요. 웹 [애플리케이션-Web API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>앱 등록

응용 프로그램을 Azure AD v 1.0 끝점에 등록 하려면 [앱 등록](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조 하세요.

* 단일 테넌트 - 애플리케이션 ID 및 위임된 사용자 ID의 경우 모두, 웹 애플리케이션과 웹 API를 Azure AD의 동일한 디렉터리에 등록해야 합니다. 일련의 권한을 노출하도록 웹 API를 구성할 수 있으며, 이 방법은 해당 리소스에 대한 웹 애플리케이션의 액세스를 제한하기 위해 사용됩니다. 위임된 사용자 ID 형식을 사용하는 경우 웹 애플리케이션이 Azure Portal의 **다른 애플리케이션에 대한 사용 권한** 드롭다운 메뉴에서 원하는 권한을 선택해야 합니다. 이 단계는 애플리케이션 ID 형식을 사용 중일 때는 필요 없습니다.
* 다중 테넌트 - 먼저, 웹 애플리케이션이 작동에 필요한 권한을 나타내도록 구성됩니다. 이러한 필수 권한 목록은 대상 디렉터리의 사용자나 관리자가 애플리케이션에 동의하여 애플리케이션을 조직에서 사용할 수 있도록 만들면 대화 상자에 표시됩니다. 일부 애플리케이션에는 조직의 모든 사용자가 동의할 수 있는 사용자 수준 권한만 필요합니다. 또 일부 애플리케이션에는 조직의 사용자가 동의할 수 없는 수준인 관리자 수준 권한이 필요합니다. 이 수준의 권한이 요구되는 애플리케이션에 동의할 수 있는 사람은 디렉터리 관리자뿐입니다. 사용자 또는 관리자가 동의하면 웹 애플리케이션과 웹 API가 모두 이들의 디렉터리에 등록됩니다.

## <a name="token-expiration"></a>토큰 만료

웹 애플리케이션이 인증 코드를 사용하여 JWT 액세스 토큰을 가져오는 경우 JWT 새로 고침 토큰도 수신합니다. 액세스 토큰이 만료되면 사용자에게 다시 로그인하도록 요구하지 않고 새로 고침 토큰을 사용하여 사용자를 다시 인증할 수 있습니다. 이 새로 고침 토큰을 사용하여 사용자를 인증하면 새 액세스 토큰 및 새로 고침 토큰을 얻습니다.

## <a name="next-steps"></a>다음 단계

- 다른 [애플리케이션 유형 및 시나리오](app-types.md)에 대해 자세히 알아보기
- Azure AD [인증 기본 사항](v1-authentication-scenarios.md)에 대해 자세히 알아보기
