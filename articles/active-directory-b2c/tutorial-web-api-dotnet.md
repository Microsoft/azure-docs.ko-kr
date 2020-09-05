---
title: '자습서: ASP.NET 웹 API에 대한 액세스 권한 부여'
titleSuffix: Azure AD B2C
description: Active Directory B2C를 사용하여 ASP.NET 웹 API를 보호하고 ASP.NET 웹 애플리케이션에서 호출하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: bfa8943af16fe62015a4736f561875235e205fc1
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163886"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 ASP.NET Web API로의 액세스 권한 부여

이 자습서에서는 ASP.NET 웹 애플리케이션에서 Azure AD B2C(Azure Active Directory B2C)로 보호된 웹 API 리소스를 호출하는 방법을 보여줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 웹 API 애플리케이션 추가
> * 웹 API에 대한 범위 구성
> * 웹 API에 사용 권한 부여
> * 애플리케이션을 사용하도록 샘플 구성

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

[자습서: Azure Active Directory B2C를 사용하여 웹 애플리케이션에서 인증을 사용하도록 설정](tutorial-web-app-dotnet.md)의 단계 및 필수 조건을 완료합니다.

## <a name="add-a-web-api-application"></a>웹 API 애플리케이션 추가

액세스 토큰을 제공하는 클라이언트 애플리케이션을 통해 보호된 리소스 요청을 수락하고 응답하려면 먼저 웹 API 리소스를 테넌트에 등록해야 합니다.

Azure AD B2C 테넌트에 애플리케이션을 등록하려면 새로운 통합 **앱 등록** 환경 또는 레거시 **애플리케이션(레거시)** 환경을 사용하면 됩니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록**을 선택한 다음, **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *webapi1*과 같습니다.
1. **리디렉션 URI**에서 **Web**을 선택한 다음, Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환해야 하는 엔드포인트를 입력합니다. 이 자습서의 샘플은 로컬에서 실행되고 `https://localhost:44332`에서 수신 대기합니다.
1. **등록**을 선택합니다.
1. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **애플리케이션(레거시)** 을 선택한 다음, **추가**를 선택합니다.
5. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapi1*과 같습니다.
6. **웹앱/웹 API 포함**에 대해 **예**를 선택합니다.
7. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 이 자습서의 샘플은 로컬에서 실행되고 `https://localhost:44332`에서 수신 대기합니다.
8. **앱 ID URI**에 웹 API에 사용하는 식별자를 입력합니다. 도메인을 포함하는 전체 식별자 URI가 생성됩니다. 예들 들어 `https://contosotenant.onmicrosoft.com/api`입니다.
9. **만들기**를 클릭합니다.
10. 속성 페이지에서 웹 애플리케이션을 구성할 때 사용할 애플리케이션 ID를 기록합니다.

* * *

## <a name="configure-scopes"></a>범위 구성

범위는 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 웹 API 사용자가 읽기 및 쓰기 액세스 권한을 모두 갖고 있을 수도 있고, 읽기 권한만 갖고 있을 수도 있습니다. 이 자습서에서는 범위를 사용하여 웹 API에 대한 읽기 및 쓰기 권한을 정의합니다.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>권한 부여

애플리케이션에서 보호된 웹 API를 호출하려면 애플리케이션 사용 권한을 API에 부여해야 합니다. 필수 조건 자습서에서 *webapp1*이라는 웹 애플리케이션을 Azure AD B2C에 만들었습니다. 이 애플리케이션은 웹 API를 호출하는 데 사용됩니다.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

애플리케이션이 보호된 웹 API를 호출하도록 등록됩니다. 사용자가 Azure AD B2C로 인증하여 애플리케이션을 사용합니다. 애플리케이션은 Azure AD B2C에서 권한을 부여받아 보호된 웹 API에 액세스합니다.

## <a name="configure-the-sample"></a>샘플 구성

이제 웹 API가 등록되었고 범위가 정의되었으므로 웹 API에서 Azure AD B2C 테넌트를 사용하도록 구성합니다. 이 자습서에서는 샘플 웹 API를 구성합니다. 샘플 웹 API는 필수 구성 요소 자습서에서 다운로드한 프로젝트에 포함됩니다.

샘플 솔루션에는 두 개의 프로젝트가 있습니다.

* **TaskWebApp** - 작업 목록을 만들고 편집합니다. 이 샘플은 **등록 또는 로그인** 사용자 흐름을 사용하여 사용자를 등록하거나 로그인시킵니다.
* **TaskService** - 작업 목록 만들기, 읽기, 업데이트 및 삭제 기능을 지원합니다. API는 Azure AD B2C를 통해 보호되고 TaskWebApp에서 호출됩니다.

### <a name="configure-the-web-application"></a>웹 애플리케이션 구성

1. Visual Studio에서 **B2C-WebAPI-DotNet** 솔루션을 엽니다.
1. **TaskWebApp** 프로젝트에서 **Web.config**를 엽니다.
1. API를 로컬로 실행하려면 **api:TaskServiceUrl**에 대한 localhost 설정을 사용합니다. Web.config를 다음과 같이 변경합니다.

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. API의 URI를 구성합니다. 이는 웹 애플리케이션에서 API를 요청하는 데 사용하는 URI입니다. 또한 요청된 권한도 구성합니다.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Web API 구성

1. **TaskService** 프로젝트에서 **Web.config**를 엽니다.
1. 테넌트를 사용하도록 API를 구성합니다.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. 클라이언트 ID를 등록된 웹 API 애플리케이션의 애플리케이션 ID(*webapi1*)로 설정합니다.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. 사용자 흐름 설정을 가입 및 로그인 사용자 흐름의 이름(*B2C_1_signupsignin1*)으로 업데이트합니다.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. 포털에서 만든 범위와 일치하도록 범위 설정을 구성합니다.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>샘플 실행

**TaskWebApp** 및 **TaskService** 프로젝트를 모두 실행해야 합니다.

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정**을 선택합니다.
1. **여러 시작 프로젝트**를 선택합니다.
1. **시작**할 두 프로젝트에 대한 **작업**을 변경합니다.
1. **확인**을 클릭하여 구성을 저장합니다.
1. **F5** 키를 눌러 두 애플리케이션을 모두 실행합니다. 각 애플리케이션은 자체 브라우저 탭에서 열립니다.
    * `https://localhost:44316/`는 웹 애플리케이션입니다.
    * `https://localhost:44332/`는 웹 API입니다.

1. 웹 애플리케이션에서 **가입/로그인**을 선택하여 웹 애플리케이션에 로그인합니다. 이전에 만든 계정을 사용합니다.
1. 로그인한 후 **할 일 목록**을 선택하고 할 일 목록 항목을 만듭니다.

할 일 목록 항목을 만들면 웹 애플리케이션에서 할 일 목록 항목을 생성하는 웹 API를 요청합니다. 보호된 웹 애플리케이션은 Azure AD B2C로 보호되는 웹 API를 호출합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 웹 API 애플리케이션 추가
> * 웹 API에 대한 범위 구성
> * 웹 API에 사용 권한 부여
> * 애플리케이션을 사용하도록 샘플 구성

> [!div class="nextstepaction"]
> [자습서: Azure Active Directory B2C의 애플리케이션에 ID 공급자 추가](tutorial-add-identity-providers.md)
