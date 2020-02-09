---
title: Azure AD 조직에 대한 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 특정 Azure Active Directory 조직에 대한 로그인을 설정합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: b0ff1c2d913d0a4402b491f3c84ce0d35cd081df
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847590"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 특정 Azure Active Directory 조직에 대한 로그인 설정

Azure AD B2C에서 Azure AD(Azure Active Directory)를 [ID 공급자](authorization-code-flow.md)로 사용하려면 해당 계정을 나타내는 애플리케이션을 만들어야 합니다. 이 문서에서는 Azure AD B2C의 사용자 흐름을 사용하여 특정 Azure AD 조직의 사용자에 대한 로그인을 설정하는 방법을 보여 줍니다.

## <a name="create-an-azure-ad-app"></a>Azure AD 앱 만들기

특정 Azure AD 조직의 사용자에 대한 로그인을 사용하도록 설정하려면 Azure AD B2C 테넌트와 동일하지 않은 조직의 Azure AD 테넌트 내에 애플리케이션을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD 테넌트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD 테넌트가 포함 된 디렉터리를 선택 합니다. Azure AD B2C 테넌트와 같은 테넌트가 아닙니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
4. **새 등록**을 선택합니다.
5. 애플리케이션의 이름을 입력합니다. `Azure AD B2C App`)을 입력합니다.
6. 이 응용 프로그램에 대해서 **만이 조직 디렉터리에서 선택한 계정** 에 동의 합니다.
7. **리디렉션 URI**의 경우 **웹**의 값을 그대로 사용 하 고 다음 URL을 소문자로 입력 합니다. 여기서 `your-B2C-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바뀝니다. 예 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    이제 모든 URL은 [b2clogin.com](b2clogin.md)을 사용해야 합니다.

8. **등록**을 클릭합니다. 나중에 사용할 **응용 프로그램 (클라이언트) ID** 를 복사 합니다.
9. 응용 프로그램 메뉴에서 **인증서 & 암호** 를 선택 하 고 **새 클라이언트 암호**를 선택 합니다.
10. 클라이언트 암호의 이름을 입력 합니다. `Azure AD B2C App Secret`)을 입력합니다.
11. 만료 기간을 선택 합니다. 이 응용 프로그램의 경우 **1 년의**선택 항목을 수락 합니다.
12. **추가** 를 선택 하 고 나중에 사용할 수 있도록 표시 되는 새 클라이언트 암호의 값을 복사 합니다.

## <a name="configure-azure-ad-as-an-identity-provider"></a>테넌트에서 Azure AD를 ID 공급자로 구성

1. Azure AD B2C 테넌트가 포함 된 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테넌트를 포함 하는 디렉터리를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **Id 공급자**를 선택한 다음 **New openid connect Connect 공급자**를 선택 합니다.
1. **이름**을 입력합니다. 예를 들어 *Contoso Azure AD*를 입력합니다.
1. **메타 데이터 url**에 대해 `your-AD-tenant-domain`를 Azure AD 테넌트의 도메인 이름으로 바꾸는 다음 url을 입력 합니다.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`)을 입력합니다.

    예를 들어 `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`Azure AD v2.0 메타 데이터 엔드포인트을 사용 **하지 마세요** . 이렇게 하면 로그인을 시도할 때 `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'`와 유사한 오류가 발생 합니다.

1. **클라이언트 id**에 대해 이전에 기록한 응용 프로그램 id를 입력 합니다.
1. **클라이언트 암호**에 대해 이전에 기록한 클라이언트 암호를 입력 합니다.
1. **범위**, **응답 유형**및 **응답 모드**에 대한 기본값을 그대로 둡니다.
1. 필드 **Domain_hint**에 대한 값을 입력 합니다. 예를 들면 *ContosoAD*입니다. 이 값은 요청에서 *domain_hint*를 사용하여 이 ID 공급자를 참조할 때 사용할 값입니다.
1. **Id 공급자 클레임 매핑**아래에서 다음 클레임 매핑 값을 입력 합니다.

    * **사용자 ID**: *oid*
    * **표시 이름**: *이름*
    * **지정 된 이름**: *given_name*
    * **성**: *family_name*
    * **전자 메일**: *unique_name*

1. **저장**을 선택합니다.
