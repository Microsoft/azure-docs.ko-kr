---
title: 세션 관리 - Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
description: 위협 모델링 도구에 노출되는 위협 완화
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: e8f3cf3889b3f79e930630ff0e768a0c4875eec6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611698"
---
# <a name="security-frame-session-management"></a>보안 프레임: 세션 관리
| 제품/서비스 | 문서 |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Azure AD를 사용하는 경우에 ADAL 메서드를 사용하여 적절한 로그아웃 구현](#logout-adal)</li></ul> |
| IoT 디바이스 | <ul><li>[생성된 SaS 토큰에 대해 한정된 수명 사용](#finite-tokens)</li></ul> |
| **Azure Document DB** | <ul><li>[생성된 리소스 토큰에 대해 최소 토큰 수명 사용](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[ADFS를 사용하는 경우에 WsFederation 메서드를 사용하여 적절한 로그아웃 구현](#wsfederation-logout)</li></ul> |
| **Identity Server** | <ul><li>[ID 서버를 사용하는 경우 적절한 로그아웃 구현](#proper-logout)</li></ul> |
| **웹 애플리케이션** | <ul><li>[HTTPS를 통해 사용할 수 있는 애플리케이션은 보안 쿠키를 사용해야 함](#https-secure-cookies)</li><li>[모든 http 기반 애플리케이션은 쿠키 정의에 대해서 http만을 지정해야 함](#cookie-definition)</li><li>[ASP.NET 웹 페이지에서 CSRF(교차 사이트 요청 위조) 공격에 대해 완화](#csrf-asp)</li><li>[비활성 수명에 대한 세션 설정](#inactivity-lifetime)</li><li>[애플리케이션에서 적절한 로그아웃 구현](#proper-app-logout)</li></ul> |
| **앱 API** | <ul><li>[ASP.NET Web API에서 CSRF(교차 사이트 요청 위조) 공격에 대해 완화](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Azure AD를 사용하는 경우에 ADAL 메서드를 사용하여 적절한 로그아웃 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure AD | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 애플리케이션이 Azure AD에서 발급하는 액세스 토큰을 사용하는 경우 로그아웃 이벤트 처리기를 호출해야 합니다. |

### <a name="example"></a>예
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>예
Session.Abandon() 메서드를 호출하여 사용자의 세션을 삭제해야 합니다. 다음 메서드에서는 사용자 로그아웃의 보안 구현을 보여 줍니다.
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a id="finite-tokens"></a>생성된 SaS 토큰에 대해 한정된 수명 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 디바이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | Azure IoT Hub를 인증하기 위해 생성된 SaS 토큰은 한정된 만료 기간이 있어야 합니다. 토큰이 손상된 경우에 재생될 수 있는 시간을 제한하려면 SaS 토큰 수명을 최소로 유지합니다.|

## <a id="resource-tokens"></a>생성된 리소스 토큰에 대해 최소 토큰 수명 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Document DB | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 리소스 토큰의 시간 범위를 필요한 최소값으로 줄입니다. 리소스 토큰은 기본 1시간의 유효한 시간을 갖습니다.|

## <a id="wsfederation-logout"></a>ADFS를 사용하는 경우에 WsFederation 메서드를 사용하여 적절한 로그아웃 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | ADFS | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 애플리케이션이 ADFS에서 발급한 STS 토큰을 사용하는 경우 로그아웃 이벤트 처리기는 WSFederationAuthenticationModule.FederatedSignOut() 메서드를 호출하여 사용자를 로그아웃시켜야 합니다. 또한 현재 세션을 제거해야 하고 세션 토큰 값을 다시 설정하고 무효화해야 합니다.|

### <a name="example"></a>예
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a id="proper-logout"></a>ID 서버를 사용하는 경우 적절한 로그아웃 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | ID 서버 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [IdentityServer3-페더레이션된 로그아웃](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **단계** | IdentityServer는 외부 ID 공급자를 사용하여 페더레이션하는 기능을 지원합니다. 업스트림 ID 공급자에서 사용자가 로그아웃하는 경우 사용된 프로토콜에 따라 사용자가 로그아웃하는 경우의 알림을 받을 수 있습니다. 또한 사용자가 로그아웃할 수도 있도록 IdentityServer가 해당 클라이언트에 알릴 수 있습니다 구현 세부 정보는 참조 섹션에 있는 설명서를 확인합니다.|

## <a id="https-secure-cookies"></a>HTTPS를 통해 사용할 수 있는 애플리케이션은 보안 쿠키를 사용해야 함

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | EnvironmentType - OnPrem |
| **참조**              | [httpCookies 요소(ASP.NET 설정 스키마)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure 속성](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **단계** | 쿠키는 일반적으로 범위가 지정된 도메인에만 액세스할 수 있습니다. 아쉽게도 HTTPS를 통해 만들어진 쿠키가 HTTP를 통해 액세스할 수 있도록 "도메인"의 정의는 프로토콜을 포함하지 않습니다. "보안" 특성은 쿠키가 HTTPS를 통해서만 사용될 수 있음을 브라우저에 나타냅니다. HTTPS를 통해 설정된 모든 쿠키는 **보안** 특성을 사용해야 합니다. 이 요구 사항은 requireSSL 특성을 true로 설정하여 web.config 파일에 적용할 수 있습니다. 그러면 추가 코드를 변경할 필요 없이 모든 현재 및 미래의 쿠키에 **보안** 특성을 적용하기 때문에 가장 좋은 방법이라고 할 수 있습니다.|

### <a name="example"></a>예
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
이 설정은 HTTP가 애플리케이션에 액세스하는 데 사용되는 경우에도 적용됩니다. HTTP를 사용하여 애플리케이션에 액세스하는 경우 쿠키가 보안 특성으로 설정되고 브라우저가 쿠키를 애플리케이션에 다시 보내지 않기 때문에 이 설정은 애플리케이션을 중단시킵니다.

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 웹 양식, MVC5 |
| **특성**              | EnvironmentType - OnPrem |
| **참조**              | N/A  |
| **단계** | 웹 애플리케이션이 신뢰 당사자이고 IdP가 ADFS 서버인 경우 FedAuth 토큰의 보안 특성은 web.config의 `system.identityModel.services` 섹션에서 requireSSL을 true로 설정하여 구성될 수 있습니다.|

### <a name="example"></a>예
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a id="cookie-definition"></a>모든 http 기반 애플리케이션은 쿠키 정의에 대해서 http만을 지정해야 함

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [보안 쿠키 특성](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **단계** | 교차 사이트 스크립팅(XSS) 공격으로 인한 정보 공개의 위험을 완화하려면 새 특성(httpOnly)을 쿠키에 도입하고 모든 주요 브라우저에서 지원합니다. 이 특성은 스크립트를 통해 쿠키에 액세스할 수 없도록 지정합니다. 웹 애플리케이션은 HttpOnly 쿠키를 사용하여 쿠키에 포함된 중요한 정보가 스크립트를 통해 도난당하거나 공격자의 웹 사이트에 전송될 가능성을 줄여 줍니다. |

### <a name="example"></a>예
쿠키를 사용하는 모든 HTTP 기반 애플리케이션은 web.config에서 다음 구성을 구현하여 쿠키 정의에 HttpOnly를 지정해야 합니다.
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 웹 양식 |
| **특성**              | N/A  |
| **참조**              | [FormsAuthentication.RequireSSL 속성](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **단계** | RequireSSL 속성 값은 구성 요소의 requireSSL 특성을 사용하여 ASP.NET 애플리케이션의 구성 파일에서 설정됩니다. ASP.NET 애플리케이션에 대한 Web.config 파일에서 SSL(Secure Sockets Layer)이 requireSSL 특성을 설정하여 서버에 양식 인증 쿠키를 반환해야 하는지 여부를 지정할 수 있습니다.|

### <a name="example"></a>예 
다음 코드 예제에서는 Web.config 파일에서 requireSSL 특성을 설정합니다.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC5 |
| **특성**              | EnvironmentType - OnPrem |
| **참조**              | [WIF(Windows Identity Foundation) 구성 – 2부](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **단계** | FedAuth 쿠키에 대한 httpOnly 특성을 설정하려면 hideFromCsript 특성 값을 True로 설정해야 합니다. |

### <a name="example"></a>예
다음 구성에서는 올바른 구성을 보여 줍니다.
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a id="csrf-asp"></a>ASP.NET 웹 페이지에서 CSRF(교차 사이트 요청 위조) 공격에 대해 완화

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 교차 사이트 요청 위조(CSRF 또는 XSRF)는 공격자가 웹 사이트에서 다른 사용자가 설정한 세션의 보안 컨텍스트에서 작업을 수행할 수 있는 공격 유형입니다. 목표는 대상 웹 사이트가 수신된 요청을 인증하기 위해 세션 쿠키에만 배타적으로 의존할 경우 콘텐츠를 수정하거나 삭제하는 것입니다. 공격자는 다른 사용자가 이미 로그인한 취약한 사이트의 명령을 사용하여 URL을 로드한 해당 사용자의 브라우저를 가져와서 이 취약점을 악용할 수 있습니다. 공격자는 취약한 서버에서 리소스를 로드하는 다른 웹 사이트를 호스팅하거나 사용자가 링크를 클릭하도록 하는 등 여러 가지 방법을 사용합니다. 이러한 공격을 방지하려면 서버가 추가 토큰을 클라이언트에 보내고, 클라이언트가 모든 향후 요청에 해당 토큰을 포함하도록 요구하며, 모든 향후 요청에 ASP.NET AntiForgeryToken 또는 ViewState와 같은 현재 세션에 관련된 토큰이 포함되는지 확인해야 합니다. |

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC5, MVC6 |
| **특성**              | N/A  |
| **참조**              | [ASP.NET MVC 및 웹 페이지에서 XSRF/CSRF 방지](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **단계** | CSRF 방지 및 ASP.NET MVC 양식 - 뷰에서 `AntiForgeryToken` 도우미 메서드를 사용합니다. 양식에 `Html.AntiForgeryToken()`을 배치합니다. 예를 들면 다음과 같습니다.|

### <a name="example"></a>예
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>예
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>예
동시에 Html.AntiForgeryToken()은 위에 표시된 임의의 숨겨진 값과 동일한 값을 포함하는 __RequestVerificationToken이라는 쿠키를 방문자에게 제공합니다. 다음으로 들어오는 양식 게시의 유효성을 검사하려면 [ValidateAntiForgeryToken] 필터를 대상 작업 메서드에 추가합니다. 예를 들면 다음과 같습니다.
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
다음을 확인하는 권한 부여 필터:
* 들어오는 요청에는 __RequestVerificationToken이라는 쿠키가 있습니다.
* 들어오는 요청에는 __RequestVerificationToken이라는 `Request.Form` 항목이 있습니다.
* 이러한 쿠키와 `Request.Form` 값이 모든 조건이 좋다는 가정과 일치하면 요청이 정상적으로 통과됩니다. 하지만 그렇지 않으면 "필수 위조 방지 토큰을 제공하지 않았거나 올바르지 않습니다."라는 메시지와 함께 인증이 실패합니다. 

### <a name="example"></a>예
CSRF 방지 및 AJAX: AJAX 요청이 HTML 양식 데이터가 아닌 JSON 데이터를 보낼 수 있으므로 폼 토큰에서 AJAX 요청에 대 한 문제를 수 있습니다. 한 가지 솔루션은 사용자 지정 HTTP 헤더에 토큰을 보내는 것입니다. 다음 코드는 Razor 구문을 사용하여 토큰을 생성한 다음 AJAX 요청에 토큰을 추가합니다. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>예
요청을 처리하는 경우 요청 헤더에서 토큰을 추출합니다. 그런 다음 AntiForgery.Validate 메서드를 호출하여 토큰의 유효성을 검사합니다. Validate 메서드는 토큰이 유효하지 않은 경우 예외를 throw합니다.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 웹 양식 |
| **특성**              | N/A  |
| **참조**              | [웹 공격을 막는 ASP.NET 기본 제공 기능 활용](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **단계** | WebForm 기반 애플리케이션에서 CSRF 공격은 ViewStateUserKey를 각 사용자(사용자 ID 또는 나아가 세션 ID)에 따라 다른 임의의 문자열로 설정하여 완화될 수 있습니다. 다양한 기술적 및 사회적 원인으로 인해 세션 ID가 예측할 수 있고 시간이 초과하며 각 사용자에 따라 다르기 때문에 훨씬 더 적합합니다.|

### <a name="example"></a>예
페이지에 있어야 하는 모든 코드는 다음과 같습니다.
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>비활성 수명에 대한 세션 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [HttpSessionState.Timeout 속성](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **단계** | 세션 제한 시간은 사용자는 아무 작업도 수행 하지는 웹 사이트 (웹 서버에 의해 정의 됨) 하는 동안 때 발생 하는 이벤트를 나타냅니다. 서버 쪽의 이벤트는 사용자 세션의 상태를 '잘못됨'(즉, "더 이상 사용되지 않는")으로 변경하고 웹 서버가 해당 항목을 삭제하도록 지시합니다(포함된 모든 데이터 삭제). 다음 코드 예제에서는 Web.config 파일에서 시간 제한 세션 특성을 15분으로 설정합니다.|

### <a name="example"></a>예
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a id="threat-detection"></a>Azure SQL에서 위협 감지 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 웹 양식 |
| **특성**              | N/A  |
| **참조**              | [인증 (ASP.NET 설정 스키마)에 대 한 forms 요소](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **단계** | 양식 인증 티켓 쿠키 시간 제한을 15분으로 설정|

### <a name="example"></a>예
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 웹 양식, MVC5 |
| **특성**              | EnvironmentType - OnPrem |
| **참조**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **단계** | 웹 애플리케이션이 신뢰 당사자이고, ADFS가 STS인 경우 인증 쿠키(FedAuth 토큰)의 수명은 web.config에서 다음 구성으로 설정할 수 있습니다.|

### <a name="example"></a>예
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>예
또한 ADFS 서버에서 다음 PowerShell 명령을 실행하여 SAML 발급 ADFS 클레임 토큰의 수명을 15분으로 설정해야 합니다.
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>애플리케이션에서 적절한 로그아웃 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 사용자가 로그아웃 단추를 누를 때 애플리케이션에서 적절한 로그아웃을 수행합니다. 로그아웃 시 애플리케이션은 사용자의 세션을 삭제하고 인증 쿠키 값을 다시 설정하고 무효화하는 동시에 세션 쿠키 값을 다시 설정하고 무효화해야 합니다. 또한 여러 세션이 단일 사용자 ID와 연결되는 경우 제한 시간 또는 로그아웃 시 서버 쪽에서 전체적으로 종료되어야 합니다. 마지막으로 로그아웃 기능을 모든 페이지에 사용할 수 있는지 확인합니다. |

## <a id="csrf-api"></a>ASP.NET Web API에서 CSRF(교차 사이트 요청 위조) 공격에 대해 완화

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 교차 사이트 요청 위조(CSRF 또는 XSRF)는 공격자가 웹 사이트에서 다른 사용자가 설정한 세션의 보안 컨텍스트에서 작업을 수행할 수 있는 공격 유형입니다. 목표는 대상 웹 사이트가 수신된 요청을 인증하기 위해 세션 쿠키에만 배타적으로 의존할 경우 콘텐츠를 수정하거나 삭제하는 것입니다. 공격자는 다른 사용자가 이미 로그인한 취약한 사이트의 명령을 사용하여 URL을 로드한 해당 사용자의 브라우저를 가져와서 이 취약점을 악용할 수 있습니다. 공격자는 취약한 서버에서 리소스를 로드하는 다른 웹 사이트를 호스팅하거나 사용자가 링크를 클릭하도록 하는 등 여러 가지 방법을 사용합니다. 이러한 공격을 방지하려면 서버가 추가 토큰을 클라이언트에 보내고, 클라이언트가 모든 향후 요청에 해당 토큰을 포함하도록 요구하며, 모든 향후 요청에 ASP.NET AntiForgeryToken 또는 ViewState와 같은 현재 세션에 관련된 토큰이 포함되는지 확인해야 합니다. |

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC5, MVC6 |
| **특성**              | N/A  |
| **참조**              | [ASP.NET Web API에서 CSRF(교차 사이트 요청 위조) 공격 방지](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **단계** | CSRF 방지 및 AJAX: AJAX 요청이 HTML 양식 데이터가 아닌 JSON 데이터를 보낼 수 있으므로 폼 토큰에서 AJAX 요청에 대 한 문제를 수 있습니다. 한 가지 솔루션은 사용자 지정 HTTP 헤더에 토큰을 보내는 것입니다. 다음 코드는 Razor 구문을 사용하여 토큰을 생성한 다음 AJAX 요청에 토큰을 추가합니다. |

### <a name="example"></a>예
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>예
요청을 처리하는 경우 요청 헤더에서 토큰을 추출합니다. 그런 다음 AntiForgery.Validate 메서드를 호출하여 토큰의 유효성을 검사합니다. Validate 메서드는 토큰이 유효하지 않은 경우 예외를 throw합니다.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>예
CSRF 방지 및 ASP.NET MVC 양식 - 뷰에서 AntiForgeryToken 도우미 메서드를 사용합니다. 양식에 Html.AntiForgeryToken()을 배치합니다. 예를 들면 다음과 같습니다.
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>예
위 예제는 다음과 같을 결과를 출력합니다.
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>예
동시에 Html.AntiForgeryToken()은 위에 표시된 임의의 숨겨진 값과 동일한 값을 포함하는 __RequestVerificationToken이라는 쿠키를 방문자에게 제공합니다. 다음으로 들어오는 양식 게시의 유효성을 검사하려면 [ValidateAntiForgeryToken] 필터를 대상 작업 메서드에 추가합니다. 예를 들면 다음과 같습니다.
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
다음을 확인하는 권한 부여 필터:
* 들어오는 요청에는 __RequestVerificationToken이라는 쿠키가 있습니다.
* 들어오는 요청에는 __RequestVerificationToken이라는 `Request.Form` 항목이 있습니다.
* 이러한 쿠키와 `Request.Form` 값이 모든 조건이 좋다는 가정과 일치하면 요청이 정상적으로 통과됩니다. 하지만 그렇지 않으면 "필수 위조 방지 토큰을 제공하지 않았거나 올바르지 않습니다."라는 메시지와 함께 인증이 실패합니다.

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC5, MVC6 |
| **특성**              | ID 공급자 - ADFS, ID 공급자 - Azure AD |
| **참조**              | [개별 계정을 사용하는 Web API 및 ASP.NET Web API 2.2에서 로컬 로그인 보호](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **단계** | Web API가 OAuth 2.0을 사용하여 보호되는 경우 권한 부여 요청 헤더에서 전달자 토큰이 필요하고 토큰은 유효한 경우에만 요청에 대한 액세스 권한을 부여합니다. 쿠키 기반 인증과 달리 브라우저에서는 요청하는 전달자 토큰을 연결하지 않습니다. 요청하는 클라이언트는 요청 헤더에서 전달자 토큰을 명시적으로 연결해야 합니다. 따라서 ASP.NET Web API가 OAuth 2.0을 사용하여 보호되는 경우 전달자 토큰은 CSRF 공격에 대한 방어로 간주됩니다. 애플리케이션의 MVC 부분이 폼 인증을 사용(즉, 쿠키 사용)하는 경우 위조 방지 토큰은 MVC 웹앱에서 사용되어야 합니다. |

### <a name="example"></a>예
Web API는 쿠키가 아닌 전달자 토큰에서만 사용하도록 정보를 제공받아야 합니다. 이 작업은 `WebApiConfig.Register` 메서드의 다음 구성으로 수행할 수 있습니다.

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

SuppressDefaultHostAuthentication 메서드는 IIS 또는 OWIN 미들웨어를 통해, 요청이 Web API 파이프라인에 도달하기 전에 발생하는 모든 인증을 무시하도록 Web API에 지시합니다. 이러한 방식으로 Web API가 전달자 토큰만으로 인증하도록 제한할 수 있습니다.
