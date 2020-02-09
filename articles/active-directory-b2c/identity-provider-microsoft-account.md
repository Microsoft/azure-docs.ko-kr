---
title: Microsoft 계정을 사용 하 여 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용 하 여 응용 프로그램에서 Microsoft 계정을 사용 하 여 고객에 게 등록 및 로그인을 제공 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f023fea4340fed6fa50defb3797c776a51bad5f4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849553"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Microsoft 계정으로 등록 설정 및 로그인

## <a name="create-a-microsoft-account-application"></a>Microsoft 계정 애플리케이션 만들기

Azure Active Directory B2C (Azure AD B2C)에서 Microsoft 계정를 [id 공급자로](openid-connect.md) 사용 하려면 Azure AD 테넌트에서 응용 프로그램을 만들어야 합니다. Azure AD 테넌트는 Azure AD B2C 테넌트와 다릅니다. 아직 Microsoft 계정 없는 경우 [https://www.live.com/](https://www.live.com/)에서 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 azure ad 테넌트가 포함 된 디렉터리를 선택 하 여 azure ad 테넌트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *MSAapp1* 등을 입력합니다.
1. **지원 되는 계정 유형**에서 **조직 디렉터리와 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com)의 계정을**선택 합니다. 이 옵션은 가장 넓은 Microsoft id 집합을 대상으로 합니다.

   다른 계정 유형 선택에 대한 자세한 내용은 빠른 시작 [: Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록](../active-directory/develop/quickstart-register-app.md)을 참조 하세요.
1. **URI 리디렉션 (선택 사항)** 에서 **웹** 을 선택 하 고 텍스트 상자에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`을 입력 합니다. `your-tenant-name`를 Azure AD B2C 테넌트 이름으로 바꿉니다.
1. **등록** 선택
1. 응용 프로그램 개요 페이지에 표시 된 **응용 프로그램 (클라이언트) ID** 를 기록 합니다. 다음 섹션에서 id 공급자를 구성할 때 필요 합니다.
1. **인증서 & 암호** 선택
1. **새 클라이언트 암호** 를 클릭 합니다.
1. 비밀에 대한 **설명** (예: *응용 프로그램 암호 1)* 을 입력 한 다음 **추가**를 클릭 합니다.
1. **값** 열에 표시 된 응용 프로그램 암호를 기록 합니다. 다음 섹션에서 id 공급자를 구성할 때 필요 합니다.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Microsoft 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **Id 공급자**를 선택한 다음 **Microsoft 계정**을 선택 합니다.
1. **이름**을 입력합니다. 예: *MSA*.
1. **클라이언트 id**에 대해 이전에 만든 Azure AD 응용 프로그램의 응용 프로그램 (클라이언트) id를 입력 합니다.
1. **클라이언트 암호**에 대해 기록한 클라이언트 암호를 입력 합니다.
1. **저장**을 선택합니다.
