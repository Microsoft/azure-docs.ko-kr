---
title: Node.js 웹앱에 OIDC 로그인 추가 - Microsoft ID 플랫폼 | Azure
description: OpenID Connect를 사용하여 Node.js 웹 애플리케이션에서 인증을 구현하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 4aa0cce83f9adc8c648656899ec6dc12d498e26b
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160451"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>빠른 시작: OpenID Connect를 사용하여 Node.js 웹앱에 로그인 추가

이 빠른 시작에서는 Express와 함께 Node.js를 사용하여 빌드한 웹 애플리케이션에서 OpenID Connect 인증을 설정하는 방법을 알아봅니다. 샘플은 모든 플랫폼에서 실행되도록 설계되었습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 샘플을 실행하려면 다음이 필요합니다.

* [http://nodejs.org/](http://nodejs.org/ )에서 Node.js 설치

* [Microsoft 계정](https://www.outlook.com) 또는 [ Office 365 개발자 프로그램](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>애플리케이션 등록 
1. [Azure Portal](https://portal.azure.com/)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 여러 Azure AD 테넌트에 있는 경우:
    - 페이지의 오른쪽 위 모서리에 있는 메뉴에서 프로필을 선택한 다음, **디렉터리를 전환**합니다.
    - 애플리케이션을 만들려는 Azure AD 테넌트로 세션을 변경합니다.

1. [Azure Active Directory > 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908)으로 이동하여 앱을 등록합니다.

1. **새 등록**을 선택합니다.

1. **애플리케이션 등록** 페이지가 표시되면 앱의 등록 정보를 입력합니다.
    - **이름** 섹션에서 앱 사용자에게 표시할 의미 있는 이름을 입력합니다. 다음은 그 예입니다.  MyWebApp
    - **지원되는 계정 유형** 섹션에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정(얘: Skype, Xbox, Outlook.com)** 을 선택합니다.

    리디렉션 URI가 여러 개 있는 경우 나중에 앱이 성공적으로 만들어진 후 **인증** 탭에서 URI를 추가해야 합니다.

1. **등록**을 선택하여 앱을 만듭니다.

1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다. 이 프로젝트의 뒷부분에서 애플리케이션을 구성할 때 이 값이 필요합니다.

1. 앱의 페이지 목록에서 **인증**을 선택합니다.
    - **리디렉션 URI** 섹션의 콤보 상자에서 **웹**을 선택하고 다음 리디렉션 URI를 입력합니다. `http://localhost:3000/auth/openid/return`
    - **고급 설정** 섹션에서 **로그아웃 URL**을 `http://localhost:3000`으로 설정합니다.
    - 이 샘플에서는 사용자를 로그인할 수 있도록 [암시적 허용 흐름](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow)을 사용하도록 설정해야 하므로, **고급 설정 > 암시적 허용** 섹션에서 **ID 토큰**을 선택합니다.

1. **저장**을 선택합니다.

1. **인증서 및 비밀** 페이지의 **클라이언트 암호** 섹션에서 **새 클라이언트 암호**를 선택합니다.
    - 키 설명(인스턴스 앱 비밀에 대한)을 입력합니다.
    - 키 기간을 **1년 후, 2년 후** 또는 **만료 기한 제한 없음**으로 선택합니다.
    - **추가** 단추를 클릭하면 키 값이 표시됩니다. 키 값을 복사하여 안전한 위치에 저장합니다.

    나중에 애플리케이션을 구성할 때 이 키가 필요합니다. 이 키 값은 다시 표시되지 않으며 다른 어떤 방법으로도 검색할 수 없으므로, Azure Portal에 표시되는 즉시 기록해 두어야 합니다.

## <a name="download-the-sample-application-and-modules"></a>샘플 애플리케이션 및 모듈 다운로드

다음으로, 샘플 리포지토리를 복제하고 NPM 모듈을 설치합니다.

셸 또는 명령줄에서 다음 명령을 실행합니다.

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

또는

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

프로젝트 루트 디렉터리에서 다음 명령을 실행합니다.

`$ npm install`  

## <a name="configure-the-application"></a>애플리케이션 구성

지침에 따라 config.js의 `exports.creds`에 매개 변수를 입력합니다.

* `exports.identityMetadata`의 `<tenant_name>`을 \*.onmicrosoft.com 형식의 Azure AD 테넌트 이름으로 업데이트합니다.
* `exports.clientID`를 앱 등록에서 적어둔 애플리케이션 ID로 업데이트합니다.
* `exports.clientSecret`를 앱 등록에서 적어둔 애플리케이션 비밀로 업데이트합니다.
* `exports.redirectUrl`을 앱 등록에서 적어둔 리디렉션 URI로 업데이트합니다.

**프로덕션 앱에 대한 선택적 구성:**

* 다른 `post_logout_redirect_uri`를 사용하려는 경우 config.js의 `exports.destroySessionUrl`을 업데이트합니다.

* [mongoDB](https://www.mongodb.com) 또는 다른 [호환 세션 저장소](https://github.com/expressjs/session#compatible-session-stores)를 사용하려는 경우 config.js의 `exports.useMongoDBSessionStore`를 true로 설정합니다.
이 샘플의 기본 세션 저장소는 `express-session`입니다. 기본 세션 저장소는 프로덕션에 적합하지 않습니다.

* mongoDB 세션 저장소 및 다른 데이터베이스 URI를 사용하려는 경우 `exports.databaseUri`를 업데이트합니다.

* `exports.mongoDBSessionMaxAge`를 업데이트합니다. 여기서 mongoDB에 세션을 유지하려는 시간을 지정할 수 있습니다. 단위는 초입니다.

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행

mongoDB 서비스를 시작합니다. 이 앱에서 mongoDB 세션 저장소를 사용하는 경우 [mongoDB](http://www.mongodb.org/)를 설치하고 서비스를 먼저 시작해야 합니다. 기본 세션 저장소를 사용하는 경우 이 단계를 건너뛰어도 됩니다.

명령줄에서 다음 명령을 사용하여 앱을 실행합니다.

```
$ node app.js
```

**서버 출력을 이해하기 어려운가요?:** 이 샘플에서는 로깅에 `bunyan`를 사용합니다. 위와 같이 bunyan을 설치하고 서버를 실행하지만 bunyan 이진을 통해 파이핑하지 않는 한, 콘솔을 이해하기 어렵습니다.

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>완료되었습니다!

서버가 `http://localhost:3000`에서 성공적으로 실행될 것입니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계
Microsoft ID 플랫폼에서 지원하는 웹앱 시나리오에 대해 자세히 알아보세요.
> [!div class="nextstepaction"]
> [사용자를 로그인하는 웹앱 시나리오](scenario-web-app-sign-user-overview.md)
