---
title: 웹 API를 호출 하는 토큰 얻기 (단일 페이지 앱)-Microsoft identity platform | Microsoft
description: 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기 (API를 호출 하는 토큰 획득)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: d5d48a2fc7aca184cf8b6e7761584a8800ca5151
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160069"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>단일 페이지 응용 프로그램: API를 호출 하는 토큰 획득

MSAL를 사용 하 여 Api에 대 한 토큰을 획득 하는 패턴은 먼저 `acquireTokenSilent` 메서드를 사용 하 여 자동 토큰 요청을 시도 하는 것입니다. 이 메서드가 호출 되 면 라이브러리는 먼저 브라우저 저장소의 캐시를 확인 하 여 유효한 토큰이 있는지 확인 하 고이를 반환 합니다. 캐시에 유효한 토큰이 없으면 숨겨진 iframe에서 Azure Active Directory (Azure AD)에 자동 토큰 요청을 보냅니다. 또한이 방법을 사용 하면 라이브러리에서 토큰을 갱신할 수 있습니다. Azure AD의 Single Sign-On 세션 및 토큰 수명 값에 대 한 자세한 내용은 [토큰 수명](active-directory-configurable-token-lifetimes.md)을 참조 하세요.

Azure AD에 대 한 자동 토큰 요청은 만료 된 Azure AD 세션 또는 암호 변경과 같은 이유로 실패할 수 있습니다. 이 경우 사용자에 게 토큰을 획득 하기 위해 대화형 메서드 (사용자에 게 표시 됨) 중 하나를 호출할 수 있습니다.

* `acquireTokenPopup` 사용 하 여 [팝업 창](#acquire-a-token-with-a-pop-up-window)
* `acquireTokenRedirect`를 사용 하 여 [리디렉션](#acquire-a-token-with-a-redirect)

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>팝업 또는 리디렉션 환경 중에서 선택

 응용 프로그램에서 팝업 및 리디렉션 메서드를 모두 사용할 수는 없습니다. 팝업 또는 리디렉션 환경 간의 선택은 응용 프로그램 흐름에 따라 달라 집니다.

* 인증 하는 동안 사용자가 주 응용 프로그램 페이지에서 멀리 이동 하지 않도록 하려면 팝업 방법이 권장 됩니다. 인증 리디렉션은 팝업 창에서 발생 하므로 주 응용 프로그램의 상태는 유지 됩니다.

* 사용자에 게 팝업 창이 사용 하지 않도록 설정 된 브라우저 제약 조건 또는 정책이 있는 경우 리디렉션 방법을 사용할 수 있습니다. Internet [explorer의 팝업 창에 알려진 문제가](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)있으므로 internet explorer 브라우저에서 리디렉션 방법을 사용 합니다.

액세스 토큰 요청을 작성 하는 경우 액세스 토큰에 포함 하려는 API 범위를 설정할 수 있습니다. 요청 된 모든 범위는 액세스 토큰에 부여 되지 않을 수 있습니다. 사용자의 동의에 따라 결정 됩니다.

## <a name="acquire-a-token-with-a-pop-up-window"></a>팝업 창에서 토큰 획득

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

다음 코드에서는 앞에서 설명한 패턴과 팝업 환경을 위한 메서드를 결합 합니다.

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angulartabangular"></a>[Angular](#tab/angular)

MSAL 각도 래퍼는 자동으로 액세스 토큰을 획득 하 고 Api에 대 한 HTTP 요청에 연결 하는 HTTP 인터셉터를 제공 합니다.

`protectedResourceMap` 구성 옵션에서 Api에 대 한 범위를 지정할 수 있습니다. `MsalInterceptor`는 토큰을 자동으로 가져올 때 이러한 범위를 요청 합니다.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

자동 토큰 획득의 성공 및 실패에 대 한 MSAL 각도는 구독할 수 있는 콜백을 제공 합니다. 구독을 취소 하는 것도 중요 합니다.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

또는 핵심 MSAL .js 라이브러리에 설명 된 대로 획득 토큰 메서드를 사용 하 여 명시적으로 토큰을 가져올 수 있습니다.

---

## <a name="acquire-a-token-with-a-redirect"></a>리디렉션을 사용 하 여 토큰 획득

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

다음 패턴은 앞서 설명 했지만 토큰을 대화형으로 얻기 위한 리디렉션 메서드로 표시 됩니다. 앞에서 설명한 대로 리디렉션 콜백을 등록 해야 합니다.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>선택적 클레임 요청

선택적인 클레임은 다음 용도로 사용할 수 있습니다.

- 응용 프로그램에 대 한 토큰에 추가 클레임을 포함 합니다.
- Azure AD에서 토큰에 반환하는 특정 클레임의 동작을 변경합니다.
- 애플리케이션에 대한 사용자 지정 클레임을 추가하고 액세스합니다. 

`IdToken`에서 선택적 클레임을 요청 하기 위해 `AuthenticationParameters.ts` 클래스의 `claimsRequest` 필드로 문자열 형식 클레임 개체를 보낼 수 있습니다.

```javascript
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

자세히 알아보려면 [선택적 클레임](active-directory-optional-claims.md)을 참조 하세요.

# <a name="angulartabangular"></a>[Angular](#tab/angular)

이 코드는 앞에서 설명한 것과 같습니다.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Web API 호출](scenario-spa-call-api.md)
