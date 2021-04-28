---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 85883cd465626764d7af0c20af480b6075e13f9e
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108070584"
---
액세스 토큰을 제공하는 클라이언트 애플리케이션을 통해 보호된 리소스 요청을 수락하고 응답하려면 먼저 웹 API 리소스를 테넌트에 등록해야 합니다.

Azure AD B2C 테넌트에 애플리케이션을 등록하려면 새로운 통합 **앱 등록** 환경 또는 레거시 **애플리케이션(레거시)** 환경을 사용하면 됩니다. [새 환경에 대해 자세히 알아보세요](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예를 들어 *webapi1* 과 같습니다.
1. **리디렉션 URI** 에서 **Web** 을 선택한 다음, Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환해야 하는 엔드포인트를 입력합니다. 이 자습서의 샘플은 로컬에서 실행되고 `http://localhost:5000`에서 수신 대기합니다.
1. **등록** 을 선택합니다.
1. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID** 를 기록합니다.

다음으로, 암시적 권한 부여 흐름을 사용하도록 설정합니다.

1. **관리** 에서 **인증** 을 선택합니다.
1. **새 환경을 체험해 보세요.** (표시되는 경우)를 선택합니다.
1. **암시적 권한 부여** 에서 **액세스 토큰** 및 **ID 토큰** 확인란을 둘 다 선택합니다.
1. **저장** 을 선택합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **애플리케이션(레거시)** 을 선택한 다음, **추가** 를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapi1* 과 같습니다.
1. **Web App / Web API** 에 대해 **예** 를 선택합니다.
1. **암시적 흐름 허용** 에 대해 **예** 를 선택합니다.
1. **회신 URL** 에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 이 자습서의 샘플은 로컬에서 실행되고 `https://localhost:5000`에서 수신 대기합니다.
1. **앱 ID URI** 의 경우 표시된 URI에 API 엔드포인트 식별자를 추가합니다. 이 자습서의 경우 전체 URI가 `https://contosob2c.onmicrosoft.com/api`와 비슷하도록 `api`를 입력합니다.
1. **만들기** 를 선택합니다.
1. 이후 단계에서 사용할 **애플리케이션 ID** 를 기록합니다.