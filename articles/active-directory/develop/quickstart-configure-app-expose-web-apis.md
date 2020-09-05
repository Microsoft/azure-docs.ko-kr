---
title: '빠른 시작: 웹 API를 공개하도록 앱 구성 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 클라이언트 애플리케이션에서 애플리케이션을 사용할 수 있도록 새 권한/범위와 역할을 공개하도록 애플리케이션을 구성하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830294"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>빠른 시작: 웹 API를 공개하는 애플리케이션 구성

웹 API를 개발하고 [권한/범위](developer-glossary.md#scopes) 및 [역할](developer-glossary.md#roles)을 공개하여 클라이언트 애플리케이션에서 사용할 수 있게 만들 수 있습니다. 올바르게 구성된 웹 API는 Graph API 및 Office 365 API와 같은 다른 Microsoft 웹 API와 마찬가지로 사용 가능합니다.

이 빠른 시작에서는 클라이언트 애플리케이션이 사용할 수 있게 새 범위를 공개하도록 애플리케이션을 구성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](quickstart-register-app.md)

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Azure Portal에 로그인하고 앱 선택

앱을 구성하려면 먼저 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록**을 차례로 선택합니다.
1. 구성하려는 애플리케이션을 찾아 선택합니다. 앱을 선택하면 볼 **개요** 또는 기본 등록 페이지가 나타납니다.
1. 새 범위를 공개하는 데 사용할 방법, UI 또는 애플리케이션 매니페스트를 선택합니다.
    * [UI를 통해 새 범위 공개](#expose-a-new-scope-through-the-ui)
    * [애플리케이션 매니페스트를 통해 새 범위 또는 역할 공개](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>UI를 통해 새 범위 공개

[![UI를 사용하여 API를 공개하는 방법 표시](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

UI를 통해 새 범위를 공개하려면

1. 앱의 **개요** 페이지에서 **API 공개** 섹션을 선택합니다.

1. **범위 추가**를 선택합니다.

1. **애플리케이션 ID URI**를 설정하지 않은 경우 입력하라는 메시지가 표시됩니다. 애플리케이션 ID URI를 입력하거나, 제공된 항목을 사용한 다음, **저장 및 계속**을 선택합니다.

1. **범위 추가** 페이지가 나타나면 범위 정보를 입력합니다.

    | 필드 | Description |
    |-------|-------------|
    | **범위 이름** | 의미 있는 범위 이름을 입력합니다.<br><br>`Employees.Read.All`)을 입력합니다. |
    | **동의할 수 있는 사람** | 범위를 사용자가 동의할 수 있는지 또는 관리자 동의가 필요한지 선택합니다. 높은 권한을 적용하려면 **관리자만**을 선택합니다. |
    | **관리자 동의 표시 이름** | 관리자에게 표시되는 의미 있는 범위 설명을 입력합니다.<br><br>예를 들어 `Read-only access to Employee records` |
    | **관리자 동의 설명** | 관리자에게 표시되는 의미 있는 범위 설명을 입력합니다.<br><br>예를 들어 `Allow the application to have read-only access to all Employee data.` |

    사용자가 범위에 동의할 수 있는 경우 다음 필드에 대한 값도 추가합니다.

    | 필드 | Description |
    |-------|-------------|
    | **사용자 동의 표시 이름** | 사용자에게 표시되는 의미 있는 범위 이름을 입력합니다.<br><br>예를 들어 `Read-only access to your Employee records` |
    | **사용자 동의 설명** | 사용자에게 표시되는 의미 있는 설명을 입력합니다.<br><br>예를 들어 `Allow the application to have read-only access to your Employee data.` |

1. **상태**를 설정하고 마치면 **범위 추가**를 선택합니다.

1. (선택 사항) 관리자가 정의한 범위에 동의하도록 요청하는 메시지를 앱 사용자에게 표시하지 않으려면 웹 API에 액세스하도록 클라이언트 애플리케이션에 "미리 권한을 부여"하면 됩니다. 사용자가 동의를 거부할 기회가 없기 때문에 신뢰할 수 있는 클라이언트 애플리케이션*에만* 미리 권한을 부여해야 합니다.
    1. **권한 있는 클라이언트 애플리케이션**에서 **클라이언트 애플리케이션 추가**를 선택합니다.
    1. 미리 권한을 부여하려는 클라이언트 애플리케이션의 **애플리케이션(클라이언트) ID**를 입력합니다. 예를 들어 앞에서 등록한 웹 애플리케이션의 ID를 입력합니다.
    1. **권한 있는 모든 범위**에서 동의 메시지를 표시하지 않을 범위를 선택한 다음, **애플리케이션 추가**를 선택합니다.

    클라이언트 앱은 이제 PCA(미리 권한이 부여된 클라이언트 앱)이며, 사용자가 클라이언트 앱에 로그인할 때 동의 여부를 묻는 메시지가 표시되지 않습니다.

1. [웹 API가 다른 애플리케이션에 공개되는지 확인](#verify-the-web-api-is-exposed-to-other-applications)하기 위한 단계를 따릅니다.

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>애플리케이션 매니페스트를 통해 새 범위 또는 역할 공개

애플리케이션 매니페스트는 Azure AD 앱 등록의 특성을 정의하는 애플리케이션 엔터티를 업데이트하기 위한 메커니즘으로 사용됩니다.

[![매니페스트에서 oauth2Permissions 컬렉션을 사용하여 새 범위를 공개합니다.](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

애플리케이션 매니페스트를 편집하여 새 범위를 공개하려면 다음을 수행합니다.

1. 앱의 **개요** 페이지에서 **매니페스트** 섹션을 선택합니다. 웹 기반 매니페스트 편집기가 열리면 포털 내에서 매니페스트를 **편집**할 수 있습니다. 필요에 따라 **다운로드**를 선택하고 로컬로 매니페스트를 편집하고 **업로드**를 사용하여 애플리케이션에 다시 적용할 수 있습니다.

    다음 예제에서는 `oauth2Permissions` 컬렉션에 다음 JSON 요소를 추가하여 리소스/API에서 `Employees.Read.All`이라는 새 범위를 공개합니다.

    프로그래밍 방식으로 또는 [guidgen](https://www.microsoft.com/download/details.aspx?id=55984)과 같은 GUID 생성 도구를 사용하여 `id` 값을 생성합니다.

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

1. 완료되면 **저장**을 클릭합니다. 이제 Web API가 디렉토리에 있는 다른 애플리케이션에 의해 사용되도록 구성되었습니다.
1. [웹 API가 다른 애플리케이션에 공개되는지 확인](#verify-the-web-api-is-exposed-to-other-applications)하기 위한 단계를 따릅니다.

애플리케이션 엔터티 및 해당 스키마에 대한 자세한 내용은 Microsoft Graph의 [애플리케이션][ms-graph-application] 리소스 형식 참조 설명서를 참조하세요.

스키마 참조를 포함하여 애플리케이션 매니페스트에 대한 자세한 내용은 [Azure AD 앱 매니페스트 이해](reference-app-manifest.md)를 참조하세요.

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>웹 API가 다른 애플리케이션에 공개되는지 확인합니다.

1. Azure AD 테넌트로 돌아가서 **앱 등록**을 선택한 다음, 구성하려는 클라이언트 애플리케이션을 찾아 선택합니다.
1. [웹 API에 액세스하는 클라이언트 애플리케이션 구성](quickstart-configure-app-access-web-apis.md)에서 개요로 설명한 단계를 반복합니다.
1. [API 선택](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis) 단계가 되면 리소스(웹 API 앱 등록)를 선택합니다.
    * Azure Portal을 사용하여 웹 API 앱 등록을 만든 경우 API 리소스가 **내 API** 탭에 나열됩니다.
    * 프로젝트를 만드는 동안 Visual Studio에서 웹 API 앱 등록을 만들 수 있도록 허용한 경우 API 리소스가 **내 조직에서 사용하는 API** 탭에 표시됩니다.

웹 API를 선택하면 클라이언트 권한 요청에 사용할 수 있는 새 범위가 표시됩니다.

## <a name="using-the-exposed-scopes"></a>공개된 범위 사용

웹 API에 액세스할 수 있는 권한이 있는 클라이언트를 적절하게 구성하면 Azure AD에서 OAuth 2.0 액세스 토큰을 발급할 수 있습니다. 클라이언트가 웹 API를 호출하면 해당 애플리케이션 등록에서 요청된 권한으로 설정된 범위(`scp`) 클레임을 갖는 액세스 토큰을 표시합니다.

추가 범위를 나중에 필요한 대로 노출할 수 있습니다. 웹 API에서 다양한 기능과 관련된 여러 범위를 공개할 수도 있음을 고려하세요. 리소스에서는 OAuth 2.0 액세스 토큰에서 수신된 범위(`scp`) 클레임을 평가하여 런타임 시 웹 API에 대한 액세스를 제어할 수 있습니다.

애플리케이션에서 전체 범위 값은 웹 API의 **애플리케이션 ID URI**(리소스)와 **범위 이름**의 연결입니다.

예를 들어 웹 API의 애플리케이션 ID URI가 `https://contoso.com/api`이고 범위 이름이 `Employees.Read.All`인 경우 전체 범위는 다음과 같습니다.

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>다음 단계

이제 범위를 구성하여 웹 API를 공개했으므로 해당 범위에 액세스할 수 있는 권한을 사용하여 클라이언트 앱의 등록을 구성합니다.

> [!div class="nextstepaction"]
> [웹 API 액세스를 위한 앱 등록 구성](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
