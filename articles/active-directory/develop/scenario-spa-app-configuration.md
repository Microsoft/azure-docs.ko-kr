---
title: 단일 페이지 앱 구성-Microsoft identity platform | Microsoft
description: 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기 (앱 코드 구성)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: b42d720a425b92ec9002f7c2b9797a91f70dafe2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003030"
---
# <a name="single-page-application-code-configuration"></a>단일 페이지 응용 프로그램: 코드 구성

SPA (단일 페이지 응용 프로그램)에 대 한 코드를 구성 하는 방법에 대해 알아봅니다.

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>SPAs 및 지원 되는 인증 흐름에 대 한 MSAL 라이브러리

Microsoft id 플랫폼은 업계 권장 보안 방법으로 PKCE를 사용 하 여 암시적 흐름 및 권한 부여 코드 흐름을 지원 하기 위해 다음과 같은 JavaScript 용 Microsoft 인증 라이브러리 (MSAL.js)를 제공 합니다.

| MSAL 라이브러리 | 흐름 | 설명 |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2.x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | 권한 부여 코드 흐름 (PKCE) | 모든 클라이언트 쪽 웹 앱에서 사용할 일반 JavaScript 라이브러리로, 각도, Vue.js 및 React.js 같은 JavaScript 또는 SPA 프레임 워크를 통해 작성 됩니다. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1.x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | 암시적 흐름 | 모든 클라이언트 쪽 웹 앱에서 사용할 일반 JavaScript 라이브러리로, 각도, Vue.js 및 React.js 같은 JavaScript 또는 SPA 프레임 워크를 통해 작성 됩니다. |
| ![MSAL 각도](media/sample-v2-code/logo_angular.png) <br/> [MSAL 각도](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | 암시적 흐름 | 각도 프레임 워크를 통해 빌드된 단일 페이지 앱에서 사용을 간소화 하기 위한 핵심 MSAL.js 라이브러리의 래퍼입니다. |

## <a name="application-code-configuration"></a>응용 프로그램 코드 구성

MSAL 라이브러리에서 응용 프로그램 등록 정보는 라이브러리 초기화 중에 구성으로 전달 됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

구성 가능한 옵션에 대 한 자세한 내용은 [MSAL.js를 사용 하 여 응용 프로그램 초기화 ](msal-js-initializing-client-applications.md)를 참조 하세요.

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로그인 및 로그아웃](scenario-spa-sign-in.md)
