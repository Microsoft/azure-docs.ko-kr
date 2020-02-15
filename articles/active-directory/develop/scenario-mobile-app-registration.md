---
title: 웹 Api를 호출 하는 모바일 앱 등록 | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 Api를 호출 하는 모바일 앱을 빌드하는 방법에 대해 알아봅니다 (앱 코드 구성).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75cfd304869bfb63131dfd2afed9f925c86d32fb
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132433"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>웹 Api를 호출 하는 모바일 앱 등록

이 문서에는 만들고 있는 모바일 응용 프로그램을 등록 하는 데 도움이 되는 지침이 포함 되어 있습니다.

## <a name="supported-account-types"></a>지원되는 계정 유형

모바일 응용 프로그램에서 지 원하는 계정 유형은 사용 하려는 환경 및 사용 하려는 흐름에 따라 달라 집니다.

### <a name="audience-for-interactive-token-acquisition"></a>대화형 토큰 획득을 위한 대상 그룹

대부분의 모바일 응용 프로그램은 대화형 인증을 사용 합니다. 앱이이 형식의 인증을 사용 하는 경우 모든 [계정 유형에](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)서 사용자에 게 로그인 할 수 있습니다.

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Windows 통합 인증, 사용자 이름-암호 및 B2C에 대 한 대상

UWP (유니버설 Windows 플랫폼) 앱이 있는 경우 Windows 통합 인증을 사용 하 여 사용자를 로그인 할 수 있습니다. Windows 통합 인증 또는 사용자 이름-암호 인증을 사용 하려면 응용 프로그램에서 사용자 고유의 LOB (기간 업무) 개발자 테 넌 트에 로그인 해야 합니다. ISV (독립 소프트웨어 공급 업체) 시나리오에서 응용 프로그램은 Azure Active Directory 조직에서 사용자에 게 로그인 할 수 있습니다. 이러한 인증 흐름은 Microsoft 개인 계정에 대해 지원 되지 않습니다.

B2C 인증 및 정책을 통과 하는 소셜 id를 사용 하 여 사용자를 로그인 할 수도 있습니다. 이 방법을 사용 하려면 대화형 인증 및 사용자 이름-암호 인증만 사용할 수 있습니다. 사용자 이름-현재 Xamarin.ios, Xamarin Android 및 UWP 에서만 암호 인증이 지원 됩니다.

자세한 내용은 [시나리오 및 지원 되는 인증 흐름](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) 및 [시나리오, 지원 되는 플랫폼 및 언어](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)를 참조 하세요.

## <a name="platform-configuration-and-redirect-uris"></a>플랫폼 구성 및 리디렉션 Uri  

### <a name="interactive-authentication"></a>대화형 인증

대화형 인증을 사용 하는 모바일 앱을 빌드할 때 가장 중요 한 등록 단계는 리디렉션 URI입니다. [ **인증** 블레이드에서 플랫폼 구성을](https://aka.ms/MobileAppReg)통해 대화형 인증을 설정할 수 있습니다.

이러한 환경을 통해 앱은 Microsoft Authenticator (및 Android의 Intune 회사 포털)를 통해 Single Sign-On (SSO)를 가져올 수 있습니다. 또한 장치 관리 정책도 지원 합니다.

앱 등록 포털은 iOS 및 Android 응용 프로그램에 대 한 조정 된 회신 URI를 계산 하는 데 도움이 되는 미리 보기 환경을 제공 합니다.

1. 앱 등록 포털에서 **인증** > 선택 하 **여 새로운 환경을 사용해 보세요**.

   ![새 환경을 선택 하는 인증 블레이드](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. **플랫폼 추가를**선택 합니다.

   ![플랫폼 추가](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 플랫폼 목록이 지원 되는 경우 **iOS**를 선택 합니다.

   ![모바일 응용 프로그램 선택](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 번들 ID를 입력 하 고 **등록**을 선택 합니다.

   ![번들 ID 입력](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

단계를 완료 하면 다음 이미지와 같이 리디렉션 URI가 사용자에 대해 계산 됩니다.

![결과 리디렉션 URI입니다.](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

리디렉션 URI를 수동으로 구성 하려는 경우 응용 프로그램 매니페스트를 통해 수행할 수 있습니다. 다음은 매니페스트에 대해 권장 되는 형식입니다.

- **iOS**: `msauth.<BUNDLE_ID>://auth` 
  - 예를 들어 `msauth.com.yourcompany.appName://auth`를 입력합니다.
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - KeyTool 명령을 통해 릴리스 키 또는 디버그 키를 사용 하 여 Android 서명 해시를 생성할 수 있습니다.

### <a name="username-password-authentication"></a>사용자 이름-암호 인증

앱에서 사용자 이름-암호 인증만 사용 하는 경우 응용 프로그램에 대 한 리디렉션 URI를 등록할 필요가 없습니다. 이 흐름은 Microsoft id 플랫폼 버전 2.0 끝점으로의 왕복을 수행 합니다. 응용 프로그램은 특정 URI에서 다시 호출 되지 않습니다. 

그러나 응용 프로그램을 공용 클라이언트 응용 프로그램으로 식별 해야 합니다. 이렇게 하려면 응용 프로그램의 **인증** 섹션을 시작 합니다. **고급 설정** 하위 섹션의 **기본 클라이언트 유형** 단락에서 질문 **응용 프로그램을 공용 클라이언트로 간주**하려면 **예**를 선택 합니다.

## <a name="api-permissions"></a>API 사용 권한

모바일 응용 프로그램은 로그인 한 사용자를 대신 하 여 Api를 호출 합니다. 앱에서 위임 된 권한을 요청 해야 합니다. 이러한 권한을 범위 라고도 합니다. 원하는 환경에 따라 Azure Portal를 통해 위임 된 사용 권한을 정적으로 요청할 수 있습니다. 또는 런타임에 동적으로 요청할 수 있습니다. 

권한을 정적으로 등록 하면 관리자가 앱을 쉽게 승인할 수 있습니다. 정적 등록을 권장 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [코드 구성](scenario-mobile-app-configuration.md)
