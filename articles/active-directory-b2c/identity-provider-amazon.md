---
title: Amazon 계정으로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Amazon 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b22c4fb8f5c54437281e90a74032e8ee1d05bcb8
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846641"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Amazon 계정으로 등록 설정 및 로그인

## <a name="create-an-amazon-application"></a>Amazon 애플리케이션 만들기

Amazon 계정을 Azure Active Directory B2C (Azure AD B2C)에서 [id 공급자로](authorization-code-flow.md) 사용 하려면 테넌트에서 응용 프로그램을 나타내는 응용 프로그램을 만들어야 합니다. Amazon 계정이 아직 없는 경우 [https://www.amazon.com/](https://www.amazon.com/)에서 등록할 수 있습니다.

1. Amazon 계정 자격 증명을 사용하여 [Amazon 개발자 센터](https://login.amazon.com/)에 로그인합니다.
1. 이미 수행한 경우 **등록**을 클릭하고 개발자 등록 단계를 수행하며 정책에 동의합니다.
1. **새 애플리케이션 등록**을 선택합니다.
1. **이름**, **설명** 및 **개인 정보 알림 URL**을 입력하고 **저장**을 클릭합니다. 개인정보취급방침은 사용자에게 개인 정보를 제공하는 관리 대상 페이지입니다.
1. **웹 설정** 섹션에서 **클라이언트 ID** 값을 복사합니다. **비밀 표시**를 선택하여 클라이언트 암호를 표시한 후 복사합니다. 테넌트에서 Amazon 계정을 ID 공급자로 구성하려면 둘 모두가 필요합니다. **클라이언트 암호** 는 중요한 보안 자격 증명입니다.
1. **웹 설정** 섹션에서 **편집**을 선택한 후 **허용된 JavaScript 원본**에 `https://your-tenant-name.b2clogin.com`을 입력하고, **허용된 반환 URL**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용해야 합니다.
1. **저장**을 클릭합니다.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Amazon 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **Id 공급자**를 선택한 다음 **Amazon**를 선택 합니다.
1. **이름**을 입력합니다. 예: *Amazon*.
1. **클라이언트 id**에 대해 이전에 만든 Amazon 응용 프로그램의 클라이언트 id를 입력 합니다.
1. **클라이언트 암호**에 대해 기록한 클라이언트 암호를 입력 합니다.
1. **저장**을 선택합니다.
