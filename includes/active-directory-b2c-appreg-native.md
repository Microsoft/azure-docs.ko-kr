---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 65fb406fa4f52f19b4d8e9dc4e3895207ffe6336
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84793657"
---
Azure AD B2C 테넌트에 애플리케이션을 등록하려면 새로운 통합 **앱 등록** 환경 또는 레거시 **애플리케이션(레거시)** 환경을 사용하면 됩니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록**을 선택한 다음, **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *nativeapp1*과 같습니다.
1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리 또는 ID 공급자의 계정**을 선택합니다.
1. **리디렉션 URI** 아래에서 드롭다운을 사용하여 **퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)** 를 선택합니다.
1. 고유 구성표가 있는 리디렉션 URI를 입력합니다. 예들 들어 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`입니다. 리디렉션 URI를 선택하는 경우 다음과 같은 중요한 고려 사항이 있습니다.
    * **개발**: 개발 용도로 리디렉션 URI를 `http://localhost`로 설정할 수 있으며 Azure AD B2C는 요청의 모든 포트를 준수합니다. 등록된 URI에 포트가 포함된 경우 Azure AD B2C는 해당 포트만 사용합니다. 예를 들어 등록된 리디렉션 URI가 `http://localhost`인 경우 요청의 리디렉션 URI는 `http://localhost:<randomport>`일 수 있습니다. 등록된 리디렉션 URI가 `http://localhost:8080`인 경우 요청의 리디렉션 URI는 `http://localhost:8080`이여야 합니다.
    * **고유**: 리디렉션 URI의 구성표는 모든 애플리케이션에 대해 고유해야 합니다. 보기 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`에서 `com.onmicrosoft.contosob2c.exampleapp`은 구성표입니다. 이 패턴을 따라야 합니다. 두 애플리케이션이 동일한 체계를 공유하는 경우 애플리케이션을 선택하기 위한 옵션이 제공됩니다. 사용자가 잘못 선택하면 로그인이 실패합니다.
    * **전체**: 리디렉션 URI에는 구성표 및 경로가 있어야 합니다. 경로는 도메인 다음에 하나 이상의 포워드 슬래시를 포함해야 합니다. 예를 들어 `//oauth/`는 실행되고 `//oauth`는 실행되지 않습니다. URI에 밑줄과 같은 특수 문자를 사용하지 마세요.
1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
2. **등록**을 선택합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **애플리케이션(레거시)** 을 선택한 다음, **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *nativeapp1*과 같습니다.
1. **원시 클라이언트**에 대해 **예**를 선택합니다.
1. 고유 구성표가 있는 **사용자 지정 리디렉션 URI**를 입력합니다. 예들 들어 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`입니다. 리디렉션 URI를 선택하는 경우 다음과 같은 두 가지 중요한 고려 사항이 있습니다.
    * **고유**: 리디렉션 URI의 구성표는 모든 애플리케이션에 대해 고유해야 합니다. 보기 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`에서 `com.onmicrosoft.contosob2c.exampleapp`은 구성표입니다. 이 패턴을 따라야 합니다. 두 애플리케이션이 동일한 체계를 공유하는 경우 애플리케이션을 선택하기 위한 옵션이 제공됩니다. 사용자가 잘못 선택하면 로그인이 실패합니다.
    * **전체**: 리디렉션 URI에는 구성표 및 경로가 있어야 합니다. 경로는 도메인 다음에 하나 이상의 포워드 슬래시를 포함해야 합니다. 예를 들어 `//oauth/`는 실행되고 `//oauth`는 실행되지 않습니다. URI에 밑줄과 같은 특수 문자를 사용하지 마세요.
1. **만들기**를 선택합니다.