---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: dec912ef6479e3668f22227c5f05de5ae8b12fcc
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799971"
---
Azure AD B2C 테넌트에 응용 프로그램을 등록 하려면 현재 **응용 프로그램** 환경 또는 새로운 통합 **앱 등록 (미리 보기)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 합니다. 또는 **모든 서비스** 를 선택 하고 **Azure AD B2C**을 검색 하고 선택 합니다.
1. **애플리케이션**을 선택하고 **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *ROPC_Auth_app*합니다.
1. **Native client**의 경우 **예**를 선택 합니다.
1. 다른 값은 그대로 두고 **만들기**를 선택 합니다.
1. 이후 단계에서 사용할 **응용 프로그램 ID** 를 기록 합니다.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록 (미리 보기)](#tab/app-reg-preview/)

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 합니다. 또는 **모든 서비스** 를 선택 하 고 **Azure AD B2C**을 검색 하고 선택 합니다.
1. **앱 등록 (미리 보기)** 를 선택 하고 **새 등록**을 선택 합니다.
1. 응용 프로그램의 **이름을** 입력 합니다. 예를 들어 *ROPC_Auth_app*합니다.
1. 다른 값은 그대로 두고 **등록**을 선택 합니다.
1. 이후 단계에서 사용할 **응용 프로그램 (클라이언트) ID** 를 기록 합니다.
1. **관리**아래에서 **인증**을 선택 합니다.
1. **새 환경 사용해 보기** (표시 되는 경우)를 선택 합니다.
1. **기본 클라이언트 유형**아래에서 **예** 를 선택 하여 응용 프로그램을 공용 클라이언트로 처리 합니다. 이 설정은 ROPC 흐름에 필요 합니다.
1. **저장**을 선택합니다.