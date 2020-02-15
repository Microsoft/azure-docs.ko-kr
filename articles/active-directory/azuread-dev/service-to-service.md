---
title: Azure Active Directory의 서비스 간 앱
description: 서비스 간 애플리케이션을 소개하고 이 앱 유형에 대한 프로토콜 흐름, 등록 및 토큰 만료 기본 사항을 설명합니다.
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
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: ff27ada23e45f2bbbb09e47af9458c1f83af277a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164228"
---
# <a name="service-to-service-apps"></a>서비스 간 앱

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

서비스 간 애플리케이션은 웹 API에서 리소스를 가져와야 하는 디먼 또는 서버 애플리케이션일 수 있습니다. 이 섹션에 적용되는 다음 두 가지 하위 시나리오가 있습니다.

- OAuth 2.0 클라이언트 자격 증명 권한 부여 형식으로 빌드된 웹 API를 호출해야 하는 디먼

    이 시나리오에서는 몇 가지 사항을 이해하는 것이 중요합니다. 첫째, 디먼 애플리케이션에서 사용자 조작이 불가능하므로, 이 애플리케이션에 고유한 ID가 있어야 합니다. 디먼 애플리케이션의 예로는 일괄 처리 작업 또는 백그라운드에서 실행되는 운영 체제 서비스가 있습니다. 이러한 종류의 애플리케이션은 애플리케이션 ID를 사용하고 애플리케이션 ID, 자격 증명(암호 또는 인증서), Azure AD에 대한 애플리케이션 ID URI 등을 제시하여 액세스 토큰을 요청합니다. 성공적인 인증 후에 디먼은 Azure AD로부터 액세스 토큰을 수신하며, 그런 다음 이 액세스 토큰을 사용하여 웹 API를 호출합니다.

- OAuth 2.0 On-Behalf-Of 초안 사양으로 빌드된 웹 API를 호출해야 하는 서버 애플리케이션(예: 웹 API)

    이 시나리오에서는 사용자가 네이티브 애플리케이션에 대해 인증했으며 이 네이티브 애플리케이션이 웹 API를 호출해야 한다고 가정합니다. Azure AD는 웹 API를 호출하는 JWT 액세스 토큰을 발급합니다. 웹 API는 다른 다운스트림 웹 API를 호출해야 하는 경우 대리 흐름을 사용하여 사용자의 ID를 위임하고 두 번째 계층 웹 API에 대해 인증할 수 있습니다.

## <a name="diagram"></a>다이어그램

![디먼 또는 서버 애플리케이션-Web API 다이어그램](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>프로토콜 흐름

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>OAuth 2.0 클라이언트 자격 증명 권한 부여를 사용하는 애플리케이션 ID

1. 먼저, 서버 애플리케이션이 대화형 로그온 대화 상자와 같은 사용자 개입 없이 자체적으로 Azure AD에서 인증해야 합니다. Azure AD의 토큰 엔드포인트에 요청하여 자격 증명, 애플리케이션 ID, 애플리케이션 ID URI를 제공합니다.
1. Azure AD가 애플리케이션을 인증하고 웹 API를 호출하는 데 사용되는 JWT 액세스 토큰을 반환합니다.
1. HTTPS를 통해 웹 애플리케이션이 반환된 JWT 액세스 토큰을 사용해서 웹 API에 대한 요청의 권한 부여 헤더에 “전달자”를 지정한 JWT 문자열을 추가합니다. 그러면 웹 API에서 JWT 토큰의 유효성을 검사하여 유효성 검사가 성공하면 원하는 리소스를 반환합니다.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>OAuth 2.0 On-Behalf-Of 초안 사양을 사용하는 위임된 사용자 ID

아래에서 설명하는 흐름에서는 사용자가 다른 애플리케이션(예: 네이티브 애플리케이션)에 대해 인증했으며 해당 사용자 ID를 사용해서 첫 번째 계층 웹 API에 대한 액세스 토큰을 획득했다고 가정합니다.

1. 네이티브 애플리케이션이 첫 번째 계층 웹 API에 액세스 토큰을 보냅니다.
1. 첫 번째 계층 웹 API가 Azure AD의 토큰 엔드포인트에 요청을 보내 애플리케이션 ID와 자격 증명 및 사용자의 액세스 토큰을 제공합니다. 또 이 요청은 웹 API가 원래 사용자를 대신하여 다운스트림 웹 API를 호출하는 새 토큰을 요청한다는 것을 나타내는 on_behalf_of 매개 변수와 함께 전송됩니다.
1. Azure AD가 첫 번째 계층 웹 API에 두 번째 웹 API에 액세스할 권한이 있는지 확인하고 요청의 유효성을 검사하여 JWT 액세스 토큰 및 JWT 새로 고침 토큰을 첫 번째 계층 웹 API에 반환합니다.
1. HTTPS를 통해 첫 번째 계층 웹 API가 요청의 권한 부여 헤더에 토큰 문자열을 추가하여 두 번째 계층 웹 API를 호출합니다. 액세스 토큰 및 새로 고침 토큰이 유효한 경우 첫 번째 계층 웹 API는 두 번째 계층 웹 API를 계속 호출할 수 있습니다.

## <a name="code-samples"></a>코드 샘플

웹 api 시나리오에 대 한 디먼 또는 서버 응용 프로그램에 대 한 코드 샘플을 참조 하세요. [서버 또는 디먼 응용 프로그램-WEB api](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>앱 등록

* 단일 테넌트 - 애플리케이션 ID 및 위임된 사용자 ID의 경우 모두, 디먼 또는 서버 애플리케이션을 Azure AD의 동일한 디렉터리에 등록해야 합니다. 일련의 권한을 노출하도록 웹 API를 구성할 수 있으며, 이 방법은 해당 리소스에 대한 디먼 또는 서버의 액세스를 제한하기 위해 사용됩니다. 위임 된 사용자 id 유형을 사용 중인 경우 서버 응용 프로그램에서 원하는 권한을 선택 해야 합니다. 응용 프로그램 등록에 대 한 **Api 권한** 페이지에서 **사용 권한 추가** 를 선택 하 고 api 패밀리를 선택한 후 위임 된 **권한**을 선택 하 고 사용 권한을 선택 합니다. 이 단계는 애플리케이션 ID 형식을 사용 중일 때는 필요 없습니다.
* 다중 테넌트 - 먼저, 디먼 또는 서버 애플리케이션이 작동에 필요한 권한을 나타내도록 구성됩니다. 이러한 필수 권한 목록은 대상 디렉터리의 사용자나 관리자가 애플리케이션에 동의하여 애플리케이션을 조직에서 사용할 수 있도록 만들면 대화 상자에 표시됩니다. 일부 애플리케이션에는 조직의 모든 사용자가 동의할 수 있는 사용자 수준 권한만 필요합니다. 또 일부 애플리케이션에는 조직의 사용자가 동의할 수 없는 수준인 관리자 수준 권한이 필요합니다. 이 수준의 권한이 요구되는 애플리케이션에 동의할 수 있는 사람은 디렉터리 관리자뿐입니다. 사용자 또는 관리자가 동의하면 웹 응용 프로그램과 웹 API가 모두 이들의 디렉터리에 등록됩니다.

## <a name="token-expiration"></a>토큰 만료

첫 번째 애플리케이션이 인증 코드를 사용하여 JWT 액세스 토큰을 가져오는 경우 JWT 새로 고침 토큰도 수신합니다. 액세스 토큰이 만료되면 사용자에게 자격 증명을 요구하지 않고 새로 고침 토큰을 사용하여 사용자를 다시 인증할 수 있습니다. 이 새로 고침 토큰을 사용하여 사용자를 인증하면 새 액세스 토큰 및 새로 고침 토큰을 얻습니다.

## <a name="next-steps"></a>다음 단계

- 다른 [애플리케이션 유형 및 시나리오](app-types.md)에 대해 자세히 알아보기
- Azure AD [인증 기본 사항](v1-authentication-scenarios.md)에 대해 자세히 알아보기
