---
title: Microsoft ID 플랫폼 앱 계정 수정 | Azure
description: Microsoft ID 플랫폼에 등록된 애플리케이션을 구성하여 애플리케이션에 액세스할 수 있는 계정 또는 사용자를 변경합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 56771658380e0a5b946c3acc70df98a262561b5c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160692"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>빠른 시작: 애플리케이션에서 지원되는 계정 수정

Microsoft ID 플랫폼에서 애플리케이션을 등록하는 경우 조직 내의 사용자만 해당 애플리케이션에 액세스할 수 있도록 제한하는 것이 좋습니다. 또는 애플리케이션을 외부 조직의 사용자나, 꼭 조직의 구성원은 아닌 사용자가 액세스할 수 있게 할 수 있습니다.

이 빠른 시작에서는 애플리케이션 구성을 수정하여 애플리케이션에 액세스할 수 있는 사용자 또는 계정을 변경하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음과 같은 필수 구성을 완료했는지 확인합니다.

* 지원되는 [권한 및 동의](v2-permissions-and-consent.md)를 살펴봅니다. 여기서는 다른 사용자 또는 애플리케이션에서 사용해야 하는 애플리케이션을 빌드하는 경우를 이해하는 것이 중요합니다.
* 애플리케이션이 등록된 테넌트가 있습니다.
  * 앱이 등록되지 않았다면 [Microsoft ID 플랫폼에 애플리케이션을 등록하는 방법](quickstart-register-app.md)을 살펴봅니다.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Azure Portal에 로그인하고 앱 선택

앱을 구성하려면 먼저 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록**을 차례로 선택합니다.
1. 구성하려는 애플리케이션을 찾아 선택합니다. 앱을 선택하면 볼 **개요** 또는 기본 등록 페이지가 나타납니다.
1. 다음 단계에 따라 [다른 계정을 지원하기 위해 애플리케이션을 변경](#change-the-application-registration-to-support-different-accounts)합니다.
1. 단일 페이지 애플리케이션인 경우 [OAuth 2.0 암시적 허용](#enable-oauth-20-implicit-grant-for-single-page-applications)을 사용합니다.

## <a name="change-the-application-registration-to-support-different-accounts"></a>다른 계정을 지원하기 위해 애플리케이션을 변경합니다.

고객이나 조직 외부의 파트너가 사용할 수 있는 애플리케이션을 작성 중인 경우 Azure Portal에서 애플리케이션 정의를 업데이트해야 합니다.

> [!IMPORTANT]
> Azure AD에서는 다중 테넌트 애플리케이션의 애플리케이션 ID URI가 전역적으로 고유해야 합니다. 앱 ID URI는 프로토콜 메시지에서 애플리케이션을 식별하는 방법 중 하나입니다. 단일 테넌트 애플리케이션의 경우 앱 ID URI이 해당 테넌트 내에서 고유한 것으로 충분합니다. 다중 테넌트 애플리케이션의 경우, 앱 ID URI이 전역적으로 고유해야 Azure AD가 모든 테넌트에서 애플리케이션을 찾을 수 있습니다. 앱 ID URI이 Azure AD 테넌트의 확인된 도메인과 일치하는 호스트 이름을 갖게 함으로써 전역 고유성이 적용됩니다. 예를 들어, 테넌트의 이름이 contoso.onmicrosoft.com이라면 유효한 앱 ID URI은 https://contoso.onmicrosoft.com/myapp 이 될 것입니다. 테넌트에 contoso.com이라는 확인된 도메인이 있으면 유효한 앱 ID URI도 https://contoso.com/myapp 이 됩니다. 앱 ID URI가 이 패턴을 따르지 않으면 애플리케이션을 다중 테넌트로 설정하지 못합니다.

### <a name="to-change-who-can-access-your-application"></a>애플리케이션에 액세스할 수 있는 사용자를 변경하려면

1. 앱 **개요** 페이지에서 **인증** 섹션을 선택하고 **지원되는 계정 유형**에서 선택한 값을 변경합니다.
    * 업무용(LOB) 애플리케이션을 빌드하는 경우 **이 디렉터리의 계정에만 해당**을 선택합니다. 이 옵션은 애플리케이션이 디렉터리에 등록되지 않은 경우에는 사용할 수 없습니다.
    * 모든 비즈니스 및 교육용 고객을 대상으로 하려면 **모든 조직 디렉터리의 계정**을 선택합니다.
    * 가장 광범위한 고객을 대상으로 하려면 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
1. **저장**을 선택합니다.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>단일 페이지 애플리케이션에 OAuth 2.0 암시적 허용 사용

SPA(단일 페이지 애플리케이션)는 일반적으로 브라우저에서 실행되는 JavaScript 기반 프런트 엔드로 구성됩니다. 이는 애플리케이션의 웹 API 백 엔드를 호출하여 비즈니스 논리를 수행합니다. Azure AD에서 호스트되는 SPA의 경우, OAuth 2.0 암시적 권한 부여를 사용하여 Azure AD에서 사용자를 인증하고, 애플리케이션의 JavaScript 클라이언트에서 해당 백 엔드 웹 API로의 보안 호출을 사용할 수 있는 토큰을 가져옵니다.

사용자가 승인하면 이 동일한 인증 프로토콜을 사용하여 클라이언트와 애플리케이션에 대해 구성된 다른 웹 API 리소스 간의 보안 호출을 위해 토큰을 가져올 수 있습니다. 암시적 허용에 관한 자세한 내용을 확인하고 자신의 애플리케이션 시나리오에 적절한지 판단하려면 Azure AD [v1.0](../azuread-dev/v1-oauth2-implicit-grant-flow.md) 및 [v2.0](v2-oauth2-implicit-grant-flow.md)의 OAuth 2.0 암시적 허용 흐름에 대해 알아보세요.

기본적으로 애플리케이션에 대해 OAuth 2.0 암시적 허용이 사용되지 않도록 설정됩니다. 아래 개요의 단계에 따라 애플리케이션에 대해 OAuth 2.0 암시적 허용을 사용할 수 있습니다.

### <a name="to-enable-oauth-20-implicit-grant"></a>OAuth 2.0 암시적 허용을 사용하도록 설정하려면

1. 앱의 **개요** 페이지에서 **인증** 섹션을 선택합니다.
1. **고급 설정**에서**암시적 허용** 섹션을 찾습니다.
1. **ID 토큰**, **액세스 토큰** 또는 둘 다를 선택합니다.
1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

앱에 대한 다른 관련 앱 관리 빠른 시작에 대해 알아봅니다.

* [Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](quickstart-register-app.md)
* [웹 API에 액세스하는 클라이언트 애플리케이션 구성](quickstart-configure-app-access-web-apis.md)
* [웹 API를 공개하는 애플리케이션 구성](quickstart-configure-app-expose-web-apis.md)
* [Microsoft ID 플랫폼을 사용하여 등록된 애플리케이션 제거](quickstart-remove-app.md)

등록된 애플리케이션 및 이들 간의 관계를 나타내는 두 개의 Azure AD 개체에 대한 자세한 내용은 [애플리케이션 개체 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

Azure Active Directory를 사용해 애플리케이션을 개발할 때 사용해야 하는 브랜딩 지침에 대해 자세히 알아보려면 [애플리케이션에 대한 브랜딩 지침](howto-add-branding-in-azure-ad-apps.md)을 참조하세요.
