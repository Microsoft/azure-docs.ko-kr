---
title: 웹 API 응용 프로그램 추가-Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C 테넌트에 web API 응용 프로그램을 추가 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 37cb242f667190fcd29bed1b7a82ca44ba2c94e9
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641556"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C 테넌트에 웹 API 애플리케이션 추가

 액세스 토큰을 제공 하는 클라이언트 응용 프로그램에서 요청을 수락 하 고 응답할 수 있도록 테넌트에 web API 리소스를 등록 합니다. 이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 web API를 등록 하는 방법을 보여 줍니다.

Azure AD B2C 테넌트에 응용 프로그램을 등록 하려면 현재 **응용 프로그램** 환경 또는 새로운 통합 **앱 등록 (미리 보기)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테넌트가 포함 된 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **애플리케이션**을 선택하고 **추가**를 선택합니다.
5. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapi1*과 같습니다.
6. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
7. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 프로덕션 응용 프로그램에서 회신 URL을 `https://localhost:44332`와 같은 값으로 설정할 수 있습니다. 테스트 목적으로 회신 URL을 `https://jwt.ms`로 설정 합니다.
8. **앱 ID URI**에 웹 API에 사용하는 식별자를 입력합니다. 도메인을 포함하는 전체 식별자 URI가 생성됩니다. 예: `https://contosotenant.onmicrosoft.com/api`
9. **만들기**를 클릭합니다.
10. 속성 페이지에서 웹 애플리케이션을 구성할 때 사용할 애플리케이션 ID를 기록합니다.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록 (미리 보기)](#tab/app-reg-preview/)

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테넌트가 포함 된 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 합니다. 또는 **모든 서비스** 를 선택 하 고 **Azure AD B2C**을 검색 하 고 선택 합니다.
1. **앱 등록 (미리 보기)** 를 선택 하 고 **새 등록**을 선택 합니다.
1. 응용 프로그램의 **이름을** 입력 합니다. 예를 들어 *webapi1*과 같습니다.
1. **URI 리디렉션**에서 **웹**을 선택한 다음 Azure AD B2C 응용 프로그램에서 요청 하는 토큰을 반환 해야 하는 엔드포인트을 입력 합니다. 프로덕션 응용 프로그램에서 리디렉션 URI를 `https://localhost:5000`와 같은 엔드포인트으로 설정할 수 있습니다. 개발 또는 테스트 중에 토큰의 디코딩된 콘텐츠를 표시 하는 Microsoft 소유의 웹 응용 프로그램 (토큰의 내용은 브라우저에서 벗어나면 안 됨)으로 `https://jwt.ms`설정할 수 있습니다. 언제 든 지 등록 된 응용 프로그램에서 리디렉션 Uri를 추가 하 고 수정할 수 있습니다.
1. **등록**을 선택합니다.
1. 웹 API의 코드에서 사용할 **응용 프로그램 (클라이언트) ID** 를 기록 합니다.

JavaScript 기반 SPA (단일 페이지 응용 프로그램)와 같이 암시적 권한 부여 흐름을 구현 하는 응용 프로그램이 있는 경우 다음 단계를 수행 하 여 흐름을 사용 하도록 설정할 수 있습니다.

1. **관리**아래에서 **인증**을 선택 합니다.
1. **새 환경 사용해 보기** (표시 되는 경우)를 선택 합니다.
1. **암시적 권한 부여**에서 **액세스 토큰** 및 **ID 토큰** 확인란을 모두 선택 합니다.
1. **저장**을 선택합니다.

* * *

## <a name="configure-scopes"></a>범위 구성

범위는 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 웹 API 사용자가 읽기 및 쓰기 액세스 권한을 모두 갖고 있을 수도 있고, 읽기 권한만 갖고 있을 수도 있습니다. 이 자습서에서는 범위를 사용하여 웹 API에 대한 읽기 및 쓰기 권한을 정의합니다.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>권한 부여

애플리케이션에서 보호된 웹 API를 호출하려면 애플리케이션 사용 권한을 API에 부여해야 합니다. 예를 들어 [자습서: Azure Active Directory B2C에 응용 프로그램 등록](tutorial-register-applications.md)에서 *webapp1* 이라는 웹 응용 프로그램이 Azure AD B2C에 등록 됩니다. 이 애플리케이션을 사용하여 웹 API를 호출할 수 있습니다.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

애플리케이션이 보호된 웹 API를 호출하도록 등록됩니다. 사용자가 Azure AD B2C로 인증하여 애플리케이션을 사용합니다. 애플리케이션은 Azure AD B2C에서 권한 부여를 받아 보호되는 웹 API에 액세스합니다.
