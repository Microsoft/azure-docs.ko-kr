---
title: Twitter 계정을 사용 하 여 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Twitter 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8b316fdb185c8a9c78e1a0c9be5f7d57b809141c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849605"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Twitter 계정으로 등록 설정 및 로그인

## <a name="create-an-application"></a>애플리케이션 만들기

Azure AD B2C에서 ID 공급자로 Twitter를 사용하려면 Twitter 애플리케이션을 만들어야 합니다. Twitter 계정이 아직 없는 경우 [https://twitter.com/signup](https://twitter.com/signup)에서 등록할 수 있습니다.

1. Twitter 계정 자격 증명을 사용하여 [Twitter 개발자](https://developer.twitter.com/en/apps) 웹 사이트에 로그인합니다.
1. **앱 만들기**를 선택합니다.
1. **앱 이름**과 **애플리케이션 설명**을 입력합니다.
1. **웹 사이트 URL**에 `https://your-tenant.b2clogin.com`을 입력합니다. `your-tenant`을 테넌트 이름으로 바꿉니다. https://contosob2c.b2clogin.com )을 입력합니다.
1. **Callback URL**(콜백 URL)에 `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`를 입력합니다. `your-tenant`는 실제 테넌트 이름으로, `your-user-flow-Id`는 사용자 흐름의 식별자로 바꿉니다. `b2c_1A_signup_signin_twitter`)을 입력합니다. Azure AD B2C에 대문자를 사용 하 여 정의한 경우에도 모든 소문자를 사용 하 여 테넌트 이름 및 사용자 흐름 id를 입력 해야 합니다.
1. 페이지 맨 아래에서 약관을 읽고 동의한 다음 **만들기**를 선택합니다.
1. **앱 세부 정보** 페이지에서 **편집 > 세부 정보 편집**을 선택하고 **Twitter로 로그인 사용**의 확인란을 선택한 후 **저장**을 선택합니다.
1. **키 및 토큰**을 선택하고 나중에 사용할 수 있도록 **소비자 API 키** 및 **소비자 API 비밀 키** 값을 적어 둡니다.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>테넌트에서 Twitter를 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **Id 공급자**를 선택한 다음 **Twitter**를 선택 합니다.
1. **이름**을 입력합니다. 예: *Twitter*.
1. **클라이언트 ID**에 대해 이전에 만든 Twitter 응용 프로그램의 소비자 API 키를 입력 합니다.
1. **클라이언트 암호**의 경우 기록한 소비자 API 비밀 키를 입력 합니다.
1. **저장**을 선택합니다.
