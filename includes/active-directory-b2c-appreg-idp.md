---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: dc75d78f2e73d1aa42f5fc84d39a8b22d6a121bb
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73642624"
---
Azure AD B2C 테넌트에 응용 프로그램을 등록 하려면 현재 **응용 프로그램** 환경 또는 새로운 통합 **앱 등록 (미리 보기)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테넌트가 포함 된 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 합니다. 또는 **모든 서비스** 를 선택 하 고 **Azure AD B2C**을 검색 하고 선택 합니다.
1. **애플리케이션**을 선택하고 **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들면 *testapp1-development*입니다.
1. **웹 앱/웹 API**의 경우 **예**를 선택 합니다.
1. **회신 URL**에 `https://jwt.ms`을 입력 합니다.
1. **만들기**를 선택합니다.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록 (미리 보기)](#tab/app-reg-preview/)

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함 된 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 합니다. 또는 **모든 서비스** 를 선택 하고 **Azure AD B2C**을 검색 하고 선택 합니다.
1. **앱 등록 (미리 보기)** 를 선택 하고 **새 등록**을 선택 합니다.
2. 응용 프로그램의 **이름**을 입력 합니다. 예를 들면 *testapp1-development*입니다.
3. **모든 조직 디렉터리 또는 모든 id 공급자에서 계정을**선택 합니다.
4. **URI 리디렉션**에서 **웹**을 선택한 다음 URL 텍스트 상자에 `https://jwt.ms`을 입력 합니다.
5. **사용 권한**아래에서 *openid connect and offline_access Permissions에 관리자 동의 부여* 확인란을 선택 합니다.
6. **등록**을 선택합니다.

응용 프로그램 등록이 완료 되면 암시적 권한 부여 흐름을 사용 하도록 설정 합니다.

1. **관리**아래에서 **인증**을 선택 합니다.
1. **새 환경 사용해 보기** (표시 되는 경우)를 선택 합니다.
1. **암시적 권한 부여**에서 **액세스 토큰** 및 **ID 토큰** 확인란을 모두 선택 합니다.
1. **저장**을 선택합니다.