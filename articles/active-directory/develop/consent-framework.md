---
title: Azure AD 동의 프레임 워크
titleSuffix: Microsoft identity platform
description: Azure Active Directory의 동의 프레임워크와 이 프레임워크를 통해 다중 테넌트 웹 및 네이티브 클라이언트 애플리케이션을 쉽게 개발하는 방법에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.openlocfilehash: cb9441e6ce19094ff72e902cdeea151041ceb963
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161143"
---
# <a name="azure-active-directory-consent-framework"></a>Azure Active Directory 동의 프레임워크

Azure AD(Azure Active Directory) 동의 프레임워크를 사용하면 다중 테넌트 웹 및 네이티브 클라이언트 애플리케이션을 쉽게 개발할 수 있습니다. 이러한 애플리케이션에서는 애플리케이션이 등록되어 있는 테넌트가 아닌 다른 Azure AD 테넌트의 사용자 계정으로 로그인할 수 있습니다. 사용자 고유의 웹 API 외에도 Microsoft Graph API(Azure AD, Intune 및 Office 365의 서비스 액세스) 및 기타 Microsoft 서비스 API와 같은 웹 API에 액세스해야 할 수도 있습니다.

이 프레임워크는 애플리케이션을 자신의 디렉토리에 등록하는 것에 동의하는 사용자나 관리자를 기반으로 합니다. 이 때 디렉토리 데이터 액세스가 필요할 수도 있습니다. 예를 들어 웹 클라이언트 애플리케이션이 Office 365에서 사용자에 대한 일정 정보를 읽어야 하는 경우 해당 사용자는 먼저 클라이언트 애플리케이션에 동의해야 합니다. 사용자가 동의해 주면 해당 클라이언트 애플리케이션에서 사용자를 대신하여 Microsoft Graph API를 호출하고 필요한 대로 일정 정보를 사용할 수 있습니다. [Microsoft Graph API](https://developer.microsoft.com/graph)는 Azure AD의 사용자와 그룹 및 더 많은 Microsoft 클라우드 서비스의 기타 데이터 개체뿐만 아니라 Office 365(예: Exchange의 일정 및 메시지, SharePoint의 사이트 및 목록, OneDrive의 문서, OneNote의 전자 필기장, Planner의 작업, Excel의 통합 문서)의 데이터에 대한 액세스를 제공합니다.

동의 프레임워크는 공용 또는 기밀 클라이언트를 사용하여 인증 코드 부여 및 클라이언트 자격 증명 부여와 같은 다양한 흐름 및 OAuth 2.0을 기반으로 작성됩니다. OAuth 2.0을 사용하여 Azure AD는 전화기, 태블릿, 서버 또는 웹 애플리케이션과 같은 다양한 유형의 클라이언트 애플리케이션을 작성하고 필요한 리소스에 액세스할 수 있습니다.

OAuth 2.0 권한 부여에서 동의 프레임워크를 사용하는 방법에 대한 자세한 내용은 [OAuth 2.0 및 Azure AD를 사용하여 웹 애플리케이션에 대한 액세스 권한 부여](v2-oauth2-auth-code-flow.md) 및 [Azure AD의 인증 시나리오](authentication-scenarios.md)를 참조하세요. Microsoft Graph를 통해 Office 365에 대한 액세스 권한을 부여받는 방법은 [Microsoft Graph를 사용하여 앱 인증](https://developer.microsoft.com/graph/docs/authorization/auth_overview)을 참조하세요.

## <a name="consent-experience---an-example"></a>동의 경험 - 예

다음 단계는 애플리케이션 개발자와 사용자 둘 다에 대해 동의 경험이 어떻게 작동하는지를 보여 줍니다.

1. 리소스/API에 액세스할 수 있는 특정 사용 권한을 요청해야 하는 웹 클라이언트 애플리케이션이 있다고 가정합니다. 다음 섹션에서 이 구성을 수행하는 방법을 알아봅니다. 하지만 기본적으로 Azure Portal을 사용하여 구성 시 사용 권한 요청을 선언합니다. 다른 구성 설정과 마찬가지로 애플리케이션 Azure AD 등록의 일부입니다.

    ![다른 애플리케이션에 대한 권한](./media/consent-framework/permissions.png)

1. 애플리케이션의 사용 권한이 업데이트되었고 애플리케이션이 실행 중이며 사용자가 처음으로 사용하는 것임을 고려하세요. 먼저 애플리케이션은 Azure AD의 `/authorize` 엔드포인트에서 권한 부여 코드를 가져와야 합니다. 그런 다음 권한 부여 코드를 사용하여 새로운 액세스 토큰 및 새로 고침 토큰을 획득할 수 있습니다.

1. 사용자가 인증되지 않은 경우 Azure AD의 `/authorize` 엔드포인트는 사용자에게 로그인하라는 메시지를 표시합니다.

    ![사용자 또는 관리자가 Azure AD에 로그인](./media/consent-framework/usersignin.png)

1. 사용자가 로그인한 후 Azure AD는 사용자를 동의 페이지에 표시해야 하는지 여부를 결정합니다. 이 결정은 사용자(또는 해당 조직의 관리자)가 애플리케이션 동의를 부여했는지 여부에 따라 다릅니다. 아직 동의가 부여되지 않았다면 Azure AD는 사용자에게 동의 여부를 묻는 메시지를 표시하며 작동에 필요한 사용 권한을 표시합니다. 동의 대화 상자에 표시되는 사용 권한 집합은 Azure Portal의 **위임된 권한**에서 선택한 집합과 일치합니다.

    ![승인 대화 상자에 표시 되는 사용 권한의 예를 보여 줍니다.](./media/consent-framework/consent.png)

1. 사용자가 동의를 부여하면 권한 부여 코드가 애플리케이션에 반환되며, 이것을 교환하여 액세스 토큰 및 새로 고침 토큰을 획득할 수 있습니다. 이 흐름에 대 한 자세한 내용은 [OAuth 2.0 인증 코드 흐름](v2-oauth2-auth-code-flow.md)을 참조 하세요.

1. 관리자로 테넌트의 모든 사용자를 대신하여 애플리케이션의 위임된 권한에 동의할 수도 있습니다. 관리 동의는 테넌트의 모든 사용자에게 동의 대화 상자가 표시되지 않도록 하고, 관리자 역할이 있는 사용자가 [Azure Portal](https://portal.azure.com)에서 이를 수행할 수 있습니다. 위임된 권한에 동의할 수 있는 관리자 역할을 알아보려면 [Azure AD의 관리자 역할 권한](../users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.

    **앱의 위임된 권한에 동의하려면**

   1. 응용 프로그램에 대 한 **API 권한** 페이지로 이동 합니다.
   1. **관리자 동의 허용** 단추를 클릭 합니다.

      ![명시적 관리자 동의를 위한 권한 부여](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > **권한 부여** 단추를 사용하는 명시적 동의 부여는 현재 ADAL.js를 사용하는 SPA(단일 페이지 애플리케이션)에 필요합니다. 그렇지 않고 액세스 토큰을 요청하는 경우 애플리케이션이 실패합니다.

## <a name="next-steps"></a>다음 단계

* [앱을 다중 테넌트로 변환하는 방법](howto-convert-app-to-be-multi-tenant.md) 참조
* 좀 더 깊이 있는 이해를 위해 [인증 코드 권한 부여 흐름 동안 OAuth 2.0 프로토콜 계층에서 동의가 지원되는 방식](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code) 알아보기
