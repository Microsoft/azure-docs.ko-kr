---
title: '자습서: 애플리케이션 등록'
titleSuffix: Azure AD B2C
description: 이 자습서에 따라 Azure Portal을 사용하여 Azure Active Directory B2C에서 웹 애플리케이션을 등록하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0fd062bd0e58ecc714e4f450c93384e47e743b65
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922016"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에서 웹 애플리케이션 등록

[애플리케이션](application-types.md)이 Azure AD B2C(Azure Active Directory B2C)와 상호 작용하려면 먼저 사용자가 관리하는 테넌트에 등록되어야 합니다. 이 자습서에서는 Azure Portal을 사용하여 웹 애플리케이션을 등록하는 방법을 보여줍니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

네이티브 앱을 대신 사용하는 경우(예: iOS, Android, 모바일 및 데스크톱) [네이티브 클라이언트 애플리케이션을 등록하는 방법](add-native-application.md)을 알아보세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

아직 고유한 [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 만들지 않았다면 지금 만듭니다. 기존 Azure AD B2C 테넌트를 사용해도 됩니다.

## <a name="register-a-web-application"></a>웹 애플리케이션 등록

Azure AD B2C 테넌트에 애플리케이션을 등록하려면 새로운 통합 **앱 등록** 환경 또는 레거시 **애플리케이션(레거시)** 환경을 사용하면 됩니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C**를 검색하고 선택합니다.
1. **앱 등록**을 선택한 다음, **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *webapp1*과 같습니다.
1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리 또는 ID 공급자의 계정을 선택합니다. Azure AD B2C를 사용하여 사용자를 인증합니다**.
1. **리디렉션 URI**에서 **웹**을 선택한 다음 URL 텍스트 상자에 `https://jwt.ms`를 입력합니다.

    리디렉션 URI는 사용자와의 상호 작용을 완료한 후 권한 부여 서버(이 경우에는 Azure AD B2C)에서 사용자를 보내고, 성공적인 권한 부여 시 액세스 토큰 또는 권한 부여 코드를 전송하는 엔드포인트입니다. 프로덕션 애플리케이션에서는 일반적으로 `https://contoso.com/auth-response`와 같이 앱이 실행되는 공개적으로 액세스할 수 있는 엔드포인트입니다. 이 자습서와 같은 테스트 목적으로 토큰을 디코딩된 토큰 콘텐츠를 표시하는 Microsoft 소유의 웹 애플리케이션 `https://jwt.ms`로 설정할 수 있습니다(토큰의 콘텐츠가 브라우저에서 벗어나면 안 됨). 앱을 개발하는 동안 애플리케이션이 로컬에서 수신 대기하는 엔드포인트(예: `https://localhost:5000`)를 추가할 수 있습니다. 언제든지 등록된 애플리케이션에서 리디렉션 URI를 추가하고 수정할 수 있습니다.

    리디렉션 URI에는 다음 제한 사항이 적용됩니다.

    * 회신 URL은 스키마 `https`로 시작해야 합니다.
    * 회신 URL은 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 예를 들어 애플리케이션의 경로 `.../abc/response-oidc`의 일부로 포함하는 경우 회신 URL에 `.../ABC/response-oidc`를 지정하지 마세요. 웹 브라우저는 경로를 대/소문자 구분하여 처리하므로 `.../abc/response-oidc`와 연결된 쿠키는 대/소문자가 일치하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외될 수 있습니다.

1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록**을 선택합니다.

애플리케이션 등록이 완료되면 암시적 권한 부여 흐름을 사용하도록 설정합니다.

1. 왼쪽 메뉴의 **관리** 아래에서 **인증**을 선택합니다.
1. **암시적 권한 부여**에서 **액세스 토큰** 및 **ID 토큰** 확인란을 둘 다 선택합니다.
1. **저장**을 선택합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C**를 검색하고 선택합니다.
1. **애플리케이션(레거시)** 을 선택한 다음, **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapp1*과 같습니다.
1. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
1. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 예를 들어, `https://localhost:44316`에서 로컬로 수신 대기하도록 설정할 수 있습니다. 포트 번호를 아직 모르는 경우 자리 표시자 값을 입력한 후 나중에 변경할 수 있습니다.

    이 자습서와 같은 테스트 목적으로 검사할 토큰 내용을 표시하도록 `https://jwt.ms`로 설정할 수 있습니다. 이 자습서의 경우 **회신 URL**을 `https://jwt.ms`로 설정합니다.

    회신 URL에는 다음 제한 사항이 적용됩니다.

    * 회신 URL은 스키마 `https`로 시작해야 합니다.
    * 회신 URL은 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 예를 들어 애플리케이션의 경로 `.../abc/response-oidc`의 일부로 포함하는 경우 회신 URL에 `.../ABC/response-oidc`를 지정하지 마세요. 웹 브라우저는 경로를 대/소문자 구분하여 처리하므로 `.../abc/response-oidc`와 연결된 쿠키는 대/소문자가 일치하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외될 수 있습니다.

1. **만들기**를 선택하여 애플리케이션 등록을 완료합니다.

* * *

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

애플리케이션이 권한 부여 코드를 액세스 토큰으로 교환하는 경우 애플리케이션 비밀을 만들어야 합니다.


#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. **Azure AD B2C - 앱 등록** 페이지에서 만든 애플리케이션을 선택합니다(예: *webapp1*).
1. 왼쪽 메뉴의 **관리**에서 **인증서 및 비밀**을 선택합니다.
1. **새 클라이언트 비밀**을 선택합니다.
1. **설명** 상자에 클라이언트 암호에 대한 설명을 입력합니다. 예: *clientsecret1*.
1. **만료**에서 암호가 유효한 기간을 선택한 다음 **추가**를 선택합니다.
1. 비밀의 **값**을 기록합니다. 이 값을 애플리케이션의 코드에서 애플리케이션 비밀로 사용합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. **Azure AD B2C - 애플리케이션** 페이지에서 만든 애플리케이션을 선택합니다(예: *webapp1*).
1. **키**를 선택한 다음 **키 생성**을 선택합니다.
1. **저장**을 선택하여 키를 확인합니다. **앱 키** 값을 기록해 둡니다. 이 값을 애플리케이션의 코드에서 애플리케이션 비밀로 사용합니다.

* * *

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

다음으로 사용자가 자신의 프로필을 등록, 로그인 및 관리할 수 있도록 사용자 흐름을 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C >에서 사용자 흐름 만들기](tutorial-create-user-flows.md)
