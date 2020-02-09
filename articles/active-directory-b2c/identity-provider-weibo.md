---
title: Weibo 계정을 사용 하 여 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Weibo 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f84d1d33874ac70a21c9d596c6fa5a9e608bb84
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847395"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Weibo 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Weibo 애플리케이션 만들기

Azure Active Directory B2C (Azure AD B2C)에서 Weibo 계정을 id 공급자로 사용 하려면 테넌트에서 응용 프로그램을 나타내는 응용 프로그램을 만들어야 합니다. Weibo 계정이 아직 없는 경우 [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us)에서 등록할 수 있습니다.

1. Weibo 계정 자격 증명을 사용하여 [Weibo 개발자 포털](https://open.weibo.com/)에 로그인합니다.
1. 로그인한 후 오른쪽 위 모서리의 표시 이름을 선택합니다.
1. 드롭다운에서 **编辑开发者信息**(개발자 정보 편집)을 선택합니다.
1. 필요한 정보를 입력하고 **提交**(제출)을 선택합니다.
1. 전자 메일 확인 프로세스를 완료합니다.
1. [ID 확인 페이지](https://open.weibo.com/developers/identity/edit)로 이동합니다.
1. 필요한 정보를 입력하고 **提交**(제출)을 선택합니다.

### <a name="register-a-weibo-application"></a>Weibo 애플리케이션 등록

1. [새 Weibo 앱 등록 페이지](https://open.weibo.com/apps/new)로 이동합니다.
1. 필요한 애플리케이션 정보를 입력합니다.
1. **创 建**(만들기)를 선택합니다.
1. **앱 키** 및 **앱 암호**의 값을 복사합니다. 테넌트에 ID 공급자를 추가하려면 두 값이 모두 필요합니다.
1. 필요한 사진을 업로드하고 필요한 정보를 입력합니다.
1. **保存以上信息**(저장)을 선택합니다.
1. **高级信息**(고급 정보)를 선택합니다.
1. OAuth2.0 **授权设置**(리디렉션 URL)에 대한 필드 옆의 **编辑**(편집)을 선택합니다.
1. OAuth2.0 **授权设置**(리디렉션 URL)에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. 예를 들어 테넌트 이름이 contoso인 경우 URL을 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`로 설정합니다.
1. **提交**(제출)을 선택합니다.

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Weibo 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **Id 공급자**를 선택한 다음 **Weibo (미리 보기)** 를 선택 합니다.
1. **이름**을 입력합니다. 예를 들면 *Weibo*입니다.
1. **클라이언트 ID**에 대해 앞에서 만든 Weibo 응용 프로그램의 앱 키를 입력 합니다.
1. **클라이언트 암호**의 경우 기록한 앱 암호를 입력 합니다.
1. **저장**을 선택합니다.
