---
title: '자습서: 웹 애플리케이션에서 인증 사용'
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용하여 ASP.NET 웹 애플리케이션에 대한 사용자 로그인을 제공하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b23bed8163ffed6a610eda7677099989e966a646
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163818"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 웹 애플리케이션에서 인증을 사용하도록 설정

이 자습서에서는 Azure AD B2C를(Azure Active Directory B2C를)를 사용하여 로그인하고 ASP.NET 웹 애플리케이션에서 사용자를 가입하는 방법을 보여줍니다. Azure AD B2C를 사용하면 애플리케이션이 개방형 표준 프로토콜을 사용하여 소셜 계정, 엔터프라이즈 계정 및 Azure Active Directory 계정을 인증할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure AD B2C에서 애플리케이션 업데이트
> * 애플리케이션을 사용하도록 샘플 구성
> * 사용자 흐름을 사용하여 등록

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* [사용자 흐름을 생성](tutorial-create-user-flows.md)하여 애플리케이션에 사용자 환경을 사용하도록 설정합니다.
* **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019](https://www.visualstudio.com/downloads/)를 설치합니다.

## <a name="update-the-application-registration"></a>애플리케이션 등록 업데이트

필수 조건의 일부로 완료한 자습서에서 Azure AD B2C에 웹 애플리케이션을 등록했습니다. 이 자습서에서 샘플과의 통신을 사용하도록 설정하려면 리디렉션 URI를 추가하고 등록된 애플리케이션에 대해 클라이언트 암호(키)를 만들어야 합니다.

### <a name="add-a-redirect-uri-reply-url"></a>리디렉션 URI 추가(회신 URL)

Azure AD B2C 테넌트에 애플리케이션을 업데이트하려면 새로운 통합 **앱 등록** 환경 또는 레거시 **애플리케이션(레거시)** 환경을 사용하면 됩니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록**을 선택하고 **소유한 애플리케이션** 탭을 선택한 다음, *webapp1* 애플리케이션을 선택합니다.
1. **웹** 아래에서 **URI 추가** 링크를 선택하고, `https://localhost:44316`을 입력한 후 **저장**을 선택합니다.
1. **개요**를 선택합니다.
1. 웹 애플리케이션을 구성하는 이후 단계에 사용할 수 있도록 **애플리케이션(클라이언트) ID**를 기록합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **애플리케이션(레거시)** 을 선택한 다음, *webapp1* 애플리케이션을 선택합니다.
1. **회신 URL** 아래에서 `https://localhost:44316`을 추가합니다.
1. **저장**을 선택합니다.
1. 속성 페이지에서 웹 애플리케이션을 구성하는 이후 단계에 사용할 수 있도록 애플리케이션 ID를 기록합니다.

* * *

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

그런 후 등록된 웹 애플리케이션에 대해 클라이언트 암호를 만듭니다. 웹 애플리케이션 코드 샘플은 토큰을 요청할 때 이를 사용하여 ID를 입증합니다.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>샘플 구성

이 자습서에서는 GitHub에서 다운로드할 수 있는 샘플을 구성합니다. 이 샘플은 ASP.NET을 사용하여 간단한 할 일 목록을 제공합니다. 이 샘플은 [Microsoft OWIN 미들웨어 구성 요소](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/)를 사용합니다. GitHub에서 [zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)하거나 샘플을 복제합니다. 경로의 총 문자 길이가 260자 미만인 폴더에 샘플 파일을 추출합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

다음의 두 프로젝트는 샘플 솔루션에 있습니다.

* **TaskWebApp** - 작업 목록을 만들고 편집합니다. 이 샘플은 **등록 또는 로그인** 사용자 흐름을 사용하여 사용자를 등록하고 로그인합니다.
* **TaskService** - 작업 목록 만들기, 읽기, 업데이트 및 삭제 기능을 지원합니다. API는 Azure AD B2C를 통해 보호되고 TaskWebApp에서 호출됩니다.

테넌트에서 등록된 애플리케이션을 사용하도록 샘플을 변경해야 하며, 여기에는 애플리케이션 ID와 이전에 기록한 키가 포함됩니다. 또한 만든 사용자 흐름도 구성해야 합니다. 샘플은 *Web.config* 파일에서 구성 값을 설정으로 정의합니다.

Web.config 파일의 설정을 사용자 흐름에 맞게 업데이트합니다.

1. Visual Studio에서 **B2C-WebAPI-DotNet** 솔루션을 엽니다.
1. **TaskWebApp** 프로젝트에서 **Web.config** 파일을 엽니다.
    1. `ida:Tenant` 및 `ida:AadInstance`의 값을 앞에서 만든 Azure AD B2C 테넌트 이름으로 바꿉니다. 예를 들어 `fabrikamb2c`를 `contoso`로 바꿉니다.
    1. `ida:TenantId`의 값을 Azure B2C 테넌트의 속성에서 찾을 수 있는 디렉터리 ID로 바꿉니다(**Azure Active Directory** > **Properties** > **Directory ID** 아래의 Azure Portal에서).
    1. `ida:ClientId`의 값을 앞에서 기록한 애플리케이션 ID로 바꿉니다.
    1. `ida:ClientSecret`의 값을 앞에서 기록한 키로 바꿉니다. 클라이언트 비밀에 미리 정의된 XML 엔터티가 포함된 경우(예: 보다 작음(`<`), 보다 큼(`>`), 앰퍼샌드(`&`) 또는 큰따옴표(`"`)) Web.config에 추가하기 전에 클라이언트 비밀을 XML로 인코딩하여 해당 문자를 이스케이프해야 합니다.
    1. `ida:SignUpSignInPolicyId`의 값을 `b2c_1_signupsignin1`로 바꿉니다.
    1. `ida:EditProfilePolicyId`의 값을 `b2c_1_profileediting1`로 바꿉니다.
    1. `ida:ResetPasswordPolicyId`의 값을 `b2c_1_passwordreset1`로 바꿉니다.

## <a name="run-the-sample"></a>샘플 실행

1. 솔루션 탐색기에서 **TaskWebApp** 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **시작 프로젝트로 설정**을 클릭합니다.
1. **F5**키를 누릅니다. 기본 브라우저가 로컬 웹 사이트 주소(`https://localhost:44316/`)에서 시작됩니다.

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

1. **가입/로그인**을 선택하여 애플리케이션의 사용자로 가입합니다. **b2c_1_signupsignin1** 사용자 흐름이 사용됩니다.
1. Azure AD B2C에서 등록 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 등록**을 선택합니다. 등록 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 가입 워크플로에서도 사용자 흐름에 정의된 사용자의 암호와 요청된 특성을 수집합니다.
1. 유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다.

    ![로그인/등록 워크플로의 일부로 표시되는 등록 페이지](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. **만들기**를 선택하여 로컬 계정을 Azure AD B2C 테넌트에 만듭니다.

이제 애플리케이션 사용자는 이메일 주소를 사용하여 로그인하고 웹 애플리케이션을 사용할 수 있습니다.

그러나 **할 일 목록** 기능은 이 시리즈의 다음 자습서인 [자습서: Azure AD B2C를 사용하여 ASP.NET 웹 API 보호](tutorial-web-api-dotnet.md)를 완료할 때까지 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C에서 애플리케이션 업데이트
> * 애플리케이션을 사용하도록 샘플 구성
> * 사용자 흐름을 사용하여 등록

이제 다음 자습서로 이동하여 웹 애플리케이션의 **할 일 목록** 기능을 사용하도록 설정합니다. 이 자습서에서는 자체 Azure AD B2C 테넌트에 웹 API 애플리케이션을 등록한 다음, API 인증에 테넌트를 사용하도록 코드 샘플을 수정합니다.

> [!div class="nextstepaction"]
> [자습서: Azure Active Directory B2C를 사용하여 ASP.NET 웹 API 보호 >](tutorial-web-api-dotnet.md)
