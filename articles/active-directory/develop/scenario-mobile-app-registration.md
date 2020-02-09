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
ms.openlocfilehash: 33510015f4f05661ad2ea041b1fd3da0e8bfb1ed
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702082"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>웹 Api를 호출 하는 모바일 앱-앱 등록

이 문서에는 모바일 응용 프로그램을 만들기 위한 앱 등록 지침이 포함 되어 있습니다.

## <a name="supported-accounts-types"></a>지원 되는 계정 유형

모바일 응용 프로그램에서 지원 되는 계정 유형은 사용 하려는 환경 및 사용 하려는 흐름에 따라 달라 집니다.

### <a name="audience-for-interactive-token-acquisition"></a>대화형 토큰 획득을 위한 대상 그룹

대부분의 모바일 응용 프로그램은 대화형 인증을 사용 합니다. 이 경우 모든 [계정 유형에](quickstart-register-app.md#register-a-new-application-using-the-azure-portal) 서 사용자에 게 로그인 할 수 있습니다.

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>통합 인증, 사용자 이름/암호 및 B2C에 대 한 대상

- Windows 통합 인증 (UWP 앱에서 가능) 또는 사용자 이름/암호를 사용 하려는 경우 응용 프로그램에서 사용자 고유의 테넌트 (LOB 개발자) 또는 Azure Active directory 조직 (ISV 시나리오)에 로그인 해야 합니다. 이러한 인증 흐름은 Microsoft 개인 계정에 대해 지원 되지 않습니다.
- B2C 인증 기관 및 정책을 전달 하는 소셜 id를 사용 하 여 사용자를 로그인 하는 경우에는 대화형 및 사용자 이름 암호만 사용할 수 있습니다. 사용자 이름-현재 Xamarin.ios, Xamarin Android 및 UWP 에서만 지원 됩니다.

큰 그림은 [시나리오 및 지원 되는 인증 흐름](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) 및 [시나리오, 지원 되는 플랫폼 및 언어](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages) 를 참조 하세요.

## <a name="platform-configuration-and-redirect-uris"></a>플랫폼 구성 및 리디렉션 Uri  

### <a name="interactive-authentication"></a>대화형 인증

대화형 인증을 사용 하 여 모바일 앱을 빌드하는 경우 가장 중요 한 등록 단계는 리디렉션 URI입니다. 이는 [인증 블레이드의 플랫폼 구성을](https://aka.ms/MobileAppReg)통해 설정할 수 있습니다.

이 환경을 사용 하면 앱이 Microsoft Authenticator (및 Android의 Intune 회사 포털)를 통해 Single Sign-On (SSO)를 가져오고 장치 관리 정책을 지원할 수 있습니다.

앱 등록 포털에는 iOS 및 Android 응용 프로그램에 대 한 조정 된 회신 URI를 계산 하는 데 도움이 되는 미리 보기 환경이 있습니다.

1. 앱 등록에서 **인증** 및 선택을 선택 **합니다. 새 환경**
   ![이미지를 선택](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. **플랫폼 추가**
   ![이미지를 선택](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 플랫폼 목록이 지원 되는 경우 **iOS**
   ![이미지를 선택](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 요청에 따라 번들 ID를 입력 하 고 **등록**
   ![이미지를 누릅니다](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. 리디렉션 URI가 계산 됩니다.
   ![image](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

리디렉션 URI를 수동으로 구성 하려는 경우 응용 프로그램 매니페스트를 통해 수행할 수 있습니다. 권장 되는 형식은 다음과 같습니다.

- ***iOS***: `msauth.<BUNDLE_ID>://auth` (예: "msauth .Com. appName:/인증")
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Android 서명 해시는 KeyTool 명령을 통해 릴리스 또는 디버그 키를 사용 하 여 생성할 수 있습니다.

### <a name="username-password"></a>사용자 이름 암호

앱이 사용자 이름/암호를 사용 하는 경우에는 응용 프로그램에 대 한 리디렉션 URI를 등록할 필요가 없습니다. 실제로이 흐름은 Microsoft identity platform v2.0 엔드포인트으로의 왕복을 수행 하며 응용 프로그램은 특정 URI에서 다시 호출 되지 않습니다. 그러나 응용 프로그램이 공용 클라이언트 응용 프로그램 임을 표현 해야 합니다. 응용 프로그램에 대 한 **인증** 섹션으로 이동 하 여이 구성을 수행 하 고, **고급 설정** 하위 섹션에서 **예**를 선택 하 고, 질문 및 답변 **응용 프로그램을 공용 클라이언트로** ( **기본 클라이언트 유형** 단락)

## <a name="api-permissions"></a>API 사용 권한

모바일 응용 프로그램은 로그인 한 사용자를 대신 하 여 Api를 호출 합니다. 앱에서 위임 된 권한 (범위 라고도 함)을 요청 해야 합니다. 원하는 환경에 따라이 작업은 Azure Portal를 통해 정적으로 또는 런타임에 동적으로 수행할 수 있습니다. 권한을 정적으로 등록 하면 관리자가 앱을 쉽게 승인할 수 있으며 권장 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [코드 구성](scenario-mobile-app-configuration.md)
