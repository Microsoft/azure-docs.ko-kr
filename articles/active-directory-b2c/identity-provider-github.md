---
title: GitHub 계정으로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 GitHub 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6b8abeda3588eae03d2c281f9e691d66ea0b39a3
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847551"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 GitHub 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>GitHub OAuth 애플리케이션 만들기

Azure Active Directory B2C (Azure AD B2C)에서 GitHub 계정을 [id 공급자로](authorization-code-flow.md) 사용 하려면 테넌트에서 응용 프로그램을 나타내는 응용 프로그램을 만들어야 합니다. 아직 GitHub 계정이 없으면 [https://www.github.com/](https://www.github.com/)에서 등록할 수 있습니다.

1. GitHub 자격 증명을 사용하여 [GitHub Developer](https://github.com/settings/developers) 웹 사이트에 로그인합니다.
1. **OAuth 앱**, **새 OAuth 앱**을 차례로 선택합니다.
1. **애플리케이션 이름** 및 **홈페이지 URL**을 입력합니다.
1. **권한 부여 콜백 URL**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용합니다.
1. **애플리케이션 등록**을 클릭합니다.
1. **클라이언트 ID** 및 **클라이언트 비밀** 값을 복사합니다. 테넌트에 ID 공급자를 추가하려면 둘 다 필요합니다.

## <a name="configure-a-github-account-as-an-identity-provider"></a>GitHub 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **Id 공급자**를 선택한 다음 **GitHub (미리 보기)** 를 선택 합니다.
1. **이름**을 입력합니다. 예: *GitHub*.
1. **클라이언트 id**에 대해 이전에 만든 GitHub 응용 프로그램의 클라이언트 id를 입력 합니다.
1. **클라이언트 암호**에 대해 기록한 클라이언트 암호를 입력 합니다.
1. **저장**을 선택합니다.
