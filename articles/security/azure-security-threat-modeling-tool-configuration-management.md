---
title: 구성 관리 - Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
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
ms.openlocfilehash: 284d0e888b89d340088f770af22c026a861a4685
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610718"
---
# <a name="security-frame-configuration-management--mitigations"></a>보안 프레임: 구성 관리 | 완화 
| 제품/서비스 | 문서 |
| --------------- | ------- |
| **웹 애플리케이션** | <ul><li>[CSP(콘텐츠 보안 정책)를 구현하고 인라인 JavaScript를 사용하지 않도록 설정](#csp-js)</li><li>[브라우저의 XSS 필터를 사용하도록 설정](#xss-filter)</li><li>[ASP.NET 애플리케이션에서 배포 전에 추적 및 디버깅을 사용하지 않도록 설정](#trace-deploy)</li><li>[신뢰할 수 있는 원본에서만 타사 JavaScript에 액세스](#js-trusted)</li><li>[인증된 ASP.NET 페이지에 UI 변조(UI Redressing) 또는 클릭재킹(clickjacking) 방어 기능이 통합되어 있는지 확인](#ui-defenses)</li><li>[ASP.NET 웹 애플리케이션에서 CORS를 사용하도록 설정하는 경우 신뢰할 수 있는 원본만 허용되는지 확인](#cors-aspnet)</li><li>[ASP.NET 페이지에서 ValidateRequest 특성을 사용하도록 설정](#validate-aspnet)</li><li>[로컬로 호스팅되는 최신 버전의 JavaScript 라이브러리 사용](#local-js)</li><li>[자동 MIME 스니핑을 사용하지 않도록 설정](#mime-sniff)</li><li>[지문 인식을 방지하기 위해 Microsoft Azure 웹 사이트에서 표준 서버 헤더 제거](#standard-finger)</li></ul> |
| **데이터베이스** | <ul><li>[데이터베이스 엔진 액세스를 위한 Windows 방화벽 구성](#firewall-db)</li></ul> |
| **앱 API** | <ul><li>[ASP.NET Web API에서 CORS를 사용하도록 설정하는 경우 신뢰할 수 있는 원본만 허용되는지 확인](#cors-api)</li><li>[중요한 데이터가 포함된 Web API 구성 파일의 섹션 암호화](#config-sensitive)</li></ul> |
| **IoT 디바이스** | <ul><li>[모든 관리 인터페이스를 강력한 자격 증명으로 보호하는지 확인](#admin-strong)</li><li>[디바이스에서 알 수 없는 코드를 실행할 수 없는지 확인](#unknown-exe)</li><li>[bit-locker를 사용하여 OS 및 IoT 디바이스의 추가 파티션 암호화](#partition-iot)</li><li>[디바이스에서 최소 서비스/기능만 사용하도록 설정되어 있는지 확인](#min-enable)</li></ul> |
| **IoT 필드 게이트웨이** | <ul><li>[bit-locker를 사용하여 OS 및 IoT 필드 게이트웨이의 추가 파티션 암호화](#field-bit-locker)</li><li>[설치 중에 필드 게이트웨이의 기본 로그인 자격 증명이 변경되었는지 확인](#default-change)</li></ul> |
| **IoT 클라우드 게이트웨이** | <ul><li>[클라우드 게이트웨이에서 연결된 디바이스 펌웨어를 최신 상태로 유지하는 프로세스를 구현하는지 확인](#cloud-firmware)</li></ul> |
| **컴퓨터 신뢰 경계** | <ul><li>[디바이스에서 조직 정책에 따라 구성된 끝점 보안 제어를 사용하는지 확인](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Azure 저장소 액세스 키의 보안 관리 확인](#secure-keys)</li><li>[Azure 저장소에서 CORS를 사용하도록 설정하는 경우 신뢰할 수 있는 원본만 허용되는지 확인](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[WCF의 서비스 제한 기능을 사용하도록 설정](#throttling)</li><li>[WCF - 메타데이터를 통한 정보 공개](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>CSP(콘텐츠 보안 정책)를 구현하고 인라인 JavaScript를 사용하지 않도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [CPS(콘텐츠 보안 정책) 소개](https://www.html5rocks.com/en/tutorials/security/content-security-policy/)(영문), [콘텐츠 보안 정책 참조](https://content-security-policy.com/)(영문), [보안 기능](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/)(영문), [콘텐츠 보안 정책 소개](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy)(영문), [CSP를 사용할 수 있습니까?](https://caniuse.com/#feat=contentsecuritypolicy)(영문) |
| **단계** | <p>CSP(콘텐츠 보안 정책)는 웹 애플리케이션 소유자가 자신의 사이트에 포함된 콘텐츠를 제어할 수 있게 하는 심층 방어 보안 메커니즘인 W3C 표준입니다. CSP는 웹 서버에 HTTP 응답 헤더로 추가되며 브라우저에서 클라이언트 쪽에 적용됩니다. 허용 목록 기반 정책이며, 웹 사이트에서 JavaScript와 같은 액티브 콘텐츠를 로드할 수 있는 트러스트된 도메인 집합을 선언할 수 있습니다.</p><p>CSP는 다음과 같은 보안 이점을 제공합니다.</p><ul><li>**XSS에 대한 보호:** 페이지가 XSS에 취약한 경우 공격자는 다음 2가지 방법으로 이를 악용할 수 있습니다.<ul><li>`<script>malicious code</script>`를 삽입합니다. 이 악용은 CSP의 기본 제한-1로 인해 작동하지 않습니다.</li><li>`<script src=”http://attacker.com/maliciousCode.js”/>`를 삽입합니다. 공격자가 제어하는 도메인은 CSP의 도메인 허용 목록에 없으므로 이 악용은 작동하지 않습니다.</li></ul></li><li>**데이터 유출 제어:** 웹 페이지의 악성 콘텐츠가 외부 웹 사이트에 연결하여 데이터를 도용하려고 하면 CSP에서 연결을 중단합니다. 이는 대상 도메인이 CSP의 허용 목록에 없기 때문입니다.</li><li>**클릭재킹에 대한 방어:** 클릭재킹은 악의적 사용자가 실제 웹 사이트를 구성하고 사용자가 UI 요소를 클릭하도록 강요하는 공격 기법입니다. 현재 클릭재킹에 대한 방어는 X-Frame-Options 응답 헤더를 구성하여 수행됩니다. 모든 브라우저에서 이 헤더를 사용하지는 않으며, CSP가 클릭재킹을 방어하기 위한 표준 방법이 됩니다.</li><li>**실시간 공격보고:** CSP 지원 웹 사이트에 대한 삽입 공격이 있는 경우 브라우저에서 자동으로 웹 서버에 구성된 엔드포인트로 알림을 트리거합니다. CSP는 이러한 방식으로 실시간 경고 시스템의 역할을 수행합니다.</li></ul> |

### <a name="example"></a>예
예제 정책: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
이 정책을 사용하면 웹 애플리케이션 서버 및 Google 분석 서버에서만 스크립트를 로드할 수 있습니다. 다른 사이트에서 로드한 스크립트는 거부됩니다. 웹 사이트에서 CSP를 사용하는 경우 XSS 공격을 완화하기 위해 다음 기능들이 자동으로 비활성화됩니다. 

### <a name="example"></a>예
인라인 스크립트가 실행되지 않습니다. 다음은 인라인 스크립트의 예제입니다. 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>예
문자열이 코드로 평가되지 않습니다. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>브라우저의 XSS 필터를 사용하도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [XSS 보호 필터](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection)(영문) |
| **단계** | <p>X-XSS-Protection 응답 헤더 구성은 브라우저의 사이트 간 스크립트 필터를 제어합니다. 이 응답 헤더의 값은 다음과 같습니다.</p><ul><li>`0:` - 필터를 비활성화합니다.</li><li>`1: Filter enabled` - 사이트 간 스크립팅 공격이 감지되면 공격을 중지하도록 브라우저에서 페이지를 삭제합니다.</li><li>`1: mode=block : Filter enabled` - XSS 공격이 감지되면 브라우저에서 페이지를 삭제하는 대신 페이지의 렌더링을 방해합니다.</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. 브라우저에서 페이지를 삭제하고 위반을 보고합니다.</li></ul><p>이는 CSP 위반 보고서를 활용하여 선택한 URI에 세부 정보를 보내는 Chromium 함수입니다. 마지막 두 옵션은 안전한 값으로 간주됩니다.</p>|

## <a id="trace-deploy"></a>ASP.NET 애플리케이션에서 배포 전에 추적 및 디버깅을 사용하지 않도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [ASP.NET 디버깅 개요](https://msdn.microsoft.com/library/ms227556.aspx), [ASP.NET 추적 개요](https://msdn.microsoft.com/library/bb386420.aspx), [방법: ASP.NET 애플리케이션에 대해 추적 사용](https://msdn.microsoft.com/library/0x5wc973.aspx), [방법: ASP.NET 애플리케이션에 대해 디버깅 사용](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **단계** | 페이지에 대한 추적이 사용되는 경우 해당 페이지를 요청하는 모든 브라우저에서 내부 서버 상태 및 워크플로에 대한 데이터가 포함된 추적 정보를 얻습니다. 이 정보는 보안에 중요할 수 있습니다. 페이지에 대한 디버깅이 사용되는 경우 서버에서 발생하는 오류로 인해 전체 스택 추적 데이터가 브라우저에 표시됩니다. 이 데이터는 서버의 워크플로 보안에 중요한 정보를 노출할 수 있습니다. |

## <a id="js-trusted"></a>신뢰할 수 있는 원본에서만 타사 JavaScript에 액세스

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 타사 JavaScript는 신뢰할 수 있는 원본에서만 참조해야 합니다. 참조 엔드포인트는 항상 SSL에 있어야 합니다. |

## <a id="ui-defenses"></a>인증된 ASP.NET 페이지에 UI 변조(UI Redressing) 또는 클릭재킹(clickjacking) 방어 기능이 통합되어 있는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [OWASP 클릭재킹 방어 참고 자료](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [IEInternals - X-Frame-Options로 클릭재킹 대응](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **단계** | <p>"UI 변조 공격"이라고도 하는 클릭재킹은 공격자가 여러 투명 레이어 또는 불투명 레이어를 사용하여 사용자가 최상위 수준 페이지를 클릭하려고 할 때 다른 페이지의 단추 또는 링크를 클릭하도록 속이는 경우입니다.</p><p>이러한 레이어는 iframe을 포함한 악의적인 페이지를 만들어 공격 대상 사용자의 페이지를 로드함으로써 수행됩니다. 따라서 공격자는 자신의 페이지에 대한 클릭을 "하이재킹"하고 다른 애플리케이션, 도메인 또는 둘 모두가 소유하는 다른 페이지로 라우팅합니다. 클릭재킹 공격을 방지하려면 적절한 X-Frame-Options HTTP 응답 헤더를 설정하여 다른 도메인의 프레이밍을 허용하지 않도록 브라우저에 지시합니다.</p>|

### <a name="example"></a>예
X-FRAME-OPTIONS 헤더는 IIS web.config를 통해 설정할 수 있습니다. 절대로 프레이밍하지 않아야 하는 사이트에 대한 web.config 코드 조각은 다음과 같습니다. 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>예
동일한 도메인의 페이지에서만 프레이밍해야 하는 사이트에 대한 web.config 코드는 다음과 같습니다. 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>ASP.NET 웹 애플리케이션에서 CORS를 사용하도록 설정하는 경우 신뢰할 수 있는 원본만 허용되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 웹 양식, MVC5 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>브라우저 보안은 웹 페이지에서 다른 도메인으로 AJAX 요청을 수행하지 못하도록 방지합니다. 이렇게 제한하는 것을 동일 원본 정책이라고 하며, 악성 사이트에서 다른 사이트의 중요한 데이터를 읽을 수 없도록 합니다. 그러나 때로는 다른 사이트에서 사용할 수 있는 API를 안전하게 노출해야 할 수도 있습니다. CORS(원본 간 리소스 공유)는 서버에서 동일 원본 정책을 완화할 수 있게 하는 W3C 표준입니다. CORS를 사용하면 서버에서 명시적으로 일부 원본 간 요청을 허용하는 한편 다른 요청은 거부할 수 있습니다.</p><p>CORS는 JSONP와 같은 이전 기술보다 더 안전하고 유연합니다. 근본적으로 CORS를 사용하도록 설정하면 몇 가지 HTTP 응답 헤더(Access-Control-*)를 웹 애플리케이션에 추가할 수 있습니다. 이는 몇 가지 방법으로 수행할 수 있습니다.</p>|

### <a name="example"></a>예
Web.config에 액세스할 수 있는 경우 다음 코드를 통해 CORS를 추가할 수 있습니다. 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>예
Web.config에 액세스할 수 없는 경우 다음 CSharp 코드를 추가하여 CORS를 구성할 수 있습니다. 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

"Access-Control-Allow-Origin" 특성의 원본 목록이 유한하고 신뢰할 수 있는 원본 집합으로 설정되어 있는지 확인해야 합니다. 이를 부적절하게 구성하지 못하면(예: 값으로 '*' 설정) 악성 사이트에서 아무런 제한 없이 웹 애플리케이션에 대한 원본 간 요청을 트리거할 수 있으므로 애플리케이션이 CSRF 공격에 취약하게 됩니다. 

## <a id="validate-aspnet"></a>ASP.NET 페이지에서 ValidateRequest 특성을 사용하도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 웹 양식, MVC5 |
| **특성**              | N/A  |
| **참조**              | [요청 유효성 검사 - 스크립트 공격 방지](https://www.asp.net/whitepapers/request-validation) |
| **단계** | <p>버전 1.1부터 ASP.NET 기능 중 하나인 요청 유효성 검사는 서버에서 인코딩되지 않은 HTML을 포함한 콘텐츠를 허용하지 않도록 방지합니다. 이 기능은 클라이언트 스크립트 코드 또는 HTML을 무의식적으로 서버에 제출하고 저장한 다음 다른 사용자에게 제공할 수 있는 스크립트 삽입 공격을 방지하도록 설계되었습니다. 모든 입력 데이터의 유효성을 검사하고 적절한 경우 HTML로 인코딩하는 것이 좋습니다.</p><p>요청 유효성 검사는 모든 입력 데이터를 잠재적으로 위험한 값의 목록과 비교함으로써 수행됩니다. 일치하는 경우 ASP.NET에서 `HttpRequestValidationException`을 발생시킵니다. 요청 유효성 검사 기능은 기본적으로 사용하도록 설정됩니다.</p>|

### <a name="example"></a>예
그러나 이 기능은 다음과 같이 페이지 수준에서 사용하지 않도록 설정할 수 있습니다. 
```XML
<%@ Page validateRequest="false" %> 
```
또는 다음과 같이 애플리케이션 수준에서 사용하지 않도록 설정할 수 있습니다. 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
요청 유효성 검사 기능은 지원되지 않으며 MVC6 파이프라인의 일부가 아닙니다. 

## <a id="local-js"></a>로컬로 호스팅되는 최신 버전의 JavaScript 라이브러리 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>JQuery와 같은 표준 JavaScript 라이브러리를 사용하는 개발자는 알려진 보안 결함이 없는 일반적인 JavaScript 라이브러리의 승인된 버전을 사용해야 합니다. 이전 버전의 알려진 취약성에 대한 보안 픽스가 포함되어 있으므로 최신 버전의 라이브러리를 사용하는 것이 좋습니다.</p><p>호환성 문제로 인해 최신 릴리스를 사용할 수 없는 경우 다음과 같은 최소 버전을 사용해야 합니다.</p><p>허용 가능한 최소 버전:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Validate 1.9</li><li>JQuery Mobile 1.0.1</li><li>JQuery Cycle 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Ajax Control Toolkit 40412</li></ul></li><li>**ASP.NET Web Forms and Ajax**<ul><li>ASP.NET Web Forms and Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>공용 CDN과 같이 외부 사이트의 JavaScript 라이브러리를 로드하면 안됩니다.</p>|

## <a id="mime-sniff"></a>자동 MIME 스니핑을 사용하지 않도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [IE8 보안 5부: 포괄적 보호](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [MIME 형식](https://en.wikipedia.org/wiki/Mime_type) |
| **단계** | X-Content-Type-Options 헤더는 개발자가 콘텐츠를 MIME 스니핑하지 않아야 한다고 지정할 수 있는 HTTP 헤더입니다. 이 헤더는 MIME 스니핑 공격을 완화하도록 설계되었습니다. 사용자가 제어할 수 있는 콘텐츠를 포함할 수 있는 각 페이지에 대해 X-Content-Type-Options: nosniff HTTP 헤더를 사용해야 합니다. 애플리케이션의 모든 페이지에 대해 필수 헤더를 전역으로 사용하도록 설정하려면 다음 중 하나를 수행할 수 있습니다.|

### <a name="example"></a>예
IIS(인터넷 정보 서비스) 7 이상에서 애플리케이션을 호스팅하는 경우 web.config 파일에 헤더를 추가합니다. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>예
글로벌 Application\_BeginRequest를 통해 헤더를 추가합니다. 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>예
사용자 지정 HTTP 모듈을 구현합니다. 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>예
개별 응답에 추가하여 특정 페이지에 대해서만 필수 헤더를 사용하도록 설정할 수 있습니다. 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>지문 인식을 방지하기 위해 Microsoft Azure 웹 사이트에서 표준 서버 헤더 제거

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | EnvironmentType - Azure |
| **참조**              | [Microsoft Azure 웹 사이트에서 표준 서버 헤더 제거](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)(영문) |
| **단계** | Server, X-Powered-By, X-AspNet-Version과 같은 헤더는 서버 및 기본 기술에 대한 정보를 표시합니다. 이러한 헤더를 숨겨 애플리케이션의 지문 인식을 방지하는 것이 좋습니다 |

## <a id="firewall-db"></a>데이터베이스 엔진 액세스를 위한 Windows 방화벽 구성

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | SQL Azure, 온-프레미스 |
| **특성**              | N/A, SQL 버전 - V12 |
| **참조**              | [Azure SQL 데이터베이스 방화벽을 구성하는 방법](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [데이터베이스 엔진 액세스에 대한 Windows 방화벽 구성](https://msdn.microsoft.com/library/ms175043) |
| **단계** | 방화벽 시스템은 컴퓨터 리소스에 대한 무단 액세스를 방지합니다. 방화벽을 통해 SQL Server 데이터베이스 엔진의 인스턴스에 액세스하려면 SQL Server를 실행하는 컴퓨터의 방화벽에서 액세스를 허용하도록 구성해야 합니다. |

## <a id="cors-api"></a>ASP.NET Web API에서 CORS를 사용하도록 설정하는 경우 신뢰할 수 있는 원본만 허용되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC 5 |
| **특성**              | N/A  |
| **참조**              | [ASP.NET Web API 2에서 원본 간 요청 사용](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)(영문), [ASP.NET Web API - ASP.NET Web API 2에서 CORS 지원](https://msdn.microsoft.com/magazine/dn532203.aspx)(영문) |
| **단계** | <p>브라우저 보안은 웹 페이지에서 다른 도메인으로 AJAX 요청을 수행하지 못하도록 방지합니다. 이렇게 제한하는 것을 동일 원본 정책이라고 하며, 악성 사이트에서 다른 사이트의 중요한 데이터를 읽을 수 없도록 합니다. 그러나 때로는 다른 사이트에서 사용할 수 있는 API를 안전하게 노출해야 할 수도 있습니다. CORS(원본 간 리소스 공유)는 서버에서 동일 원본 정책을 완화할 수 있게 하는 W3C 표준입니다.</p><p>CORS를 사용하면 서버에서 명시적으로 일부 원본 간 요청을 허용하는 한편 다른 요청은 거부할 수 있습니다. CORS는 JSONP와 같은 이전 기술보다 더 안전하고 유연합니다.</p>|

### <a name="example"></a>예
App_Start/WebApiConfig.cs에서 WebApiConfig.Register 메서드에 다음 코드를 추가합니다. 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>예
EnableCors 특성은 다음과 같이 컨트롤러의 작업 메서드에 적용할 수 있습니다. 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

EnableCors 특성의 원본 목록이 유한하고 신뢰할 수 있는 원본 집합으로 설정되어 있는지 확인해야 합니다. 이를 부적절하게 구성하지 못하면(예: 값으로 '*' 설정) 악성 사이트에서 아무런 제한 없이 API에 대한 원본 간 요청을 트리거할 수 있으므로 API가 CSRF 공격에 취약하게 됩니다. EnableCors는 컨트롤러 수준에서 데코레이팅할 수 있습니다. 

### <a name="example"></a>예
클래스의 특정 메서드에서 CORS를 사용하도록 설정하려면 DisableCors 특성을 아래와 같이 사용할 수 있습니다. 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC 6 |
| **특성**              | N/A  |
| **참조**              | [ASP.NET Core 1.0에서 CORS(원본 간 요청) 사용](https://docs.asp.net/en/latest/security/cors.html)(영문) |
| **단계** | <p>ASP.NET Core 1.0에서 CORS는 미들웨어 또는 MVC를 통해 사용하도록 설정할 수 있습니다. MVC를 통해 CORS를 사용하도록 설정하는 경우 동일한 CORS 서비스를 사용하지만 CORS 미들웨어는 사용하지 않습니다.</p>|

**방법 1** 미들웨어로 CORS를 사용하도록 설정: 전체 애플리케이션에 대해 CORS를 사용하도록 설정하려면 UseCors 확장 메서드를 사용하여 CORS 미들웨어를 요청 파이프라인에 추가합니다. 원본 간 정책은 CorsPolicyBuilder 클래스를 사용하여 CORS 미들웨어를 추가할 때 지정할 수 있습니다. 이 작업을 수행하는 방법에는 다음 두 가지가 있습니다.

### <a name="example"></a>예
첫 번째 방법은 UseCors를 람다로 호출하는 것입니다. 람다는 다음과 같이 CorsPolicyBuilder 개체를 사용합니다. 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>예
두 번째 방법은 하나 이상의 명명된 CORS 정책을 정의한 다음 런타임에 이름별로 정책을 선택하는 것입니다. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**방법 2** MVC에서 CORS를 사용하도록 설정: 개발자가 MVC를 사용하여 작업별, 컨트롤러별로 특정 CORS를 적용하거나 모든 컨트롤러에 대해 전역적으로 CORS를 적용할 수 있습니다.

### <a name="example"></a>예
작업별: 특정 작업에 대해 CORS 정책을 지정하려면 해당 작업에 [EnableCors] 특성을 추가합니다. 정책 이름을 지정합니다. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>예
컨트롤러별: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>예
전역적으로: 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
EnableCors 특성의 원본 목록이 유한하고 신뢰할 수 있는 원본 집합으로 설정되어 있는지 확인해야 합니다. 이를 부적절하게 구성하지 못하면(예: 값으로 '*' 설정) 악성 사이트에서 아무런 제한 없이 API에 대한 원본 간 요청을 트리거할 수 있으므로 API가 CSRF 공격에 취약하게 됩니다. 

### <a name="example"></a>예
컨트롤러별 또는 작업별로 CORS를 사용하지 않도록 설정하려면 [DisableCors] 특성을 사용합니다. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>중요한 데이터가 포함된 Web API 구성 파일의 섹션 암호화

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [방법: DPAPI를 사용하여 ASP.NET 2.0에서 구성 섹션 암호화](https://msdn.microsoft.com/library/ff647398.aspx), [보호되는 구성 공급자 지정](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Azure Key Vault를 사용하여 애플리케이션 비밀 보호](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **단계** | Web.config, appsettings.json과 같은 구성 파일은 사용자 이름, 암호, 데이터베이스 연결 문자열 및 암호화 키를 포함하여 중요한 정보를 저장하는 데 자주 사용됩니다. 이러한 정보를 보호하지 않으면 애플리케이션은 계정 사용자 이름과 암호, 데이터베이스 이름과 서버 이름 등과 같은 중요한 정보를 얻는 공격자 또는 악의적인 사용자에 대해 취약해집니다. 배포 유형(azure/on-prem)에 따라 DPAPI 또는 Azure Key Vault와 같은 서비스를 사용하여 구성 파일의 중요한 섹션을 암호화합니다. |

## <a id="admin-strong"></a>모든 관리 인터페이스를 강력한 자격 증명으로 보호하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 디바이스 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 디바이스 또는 필드 게이트웨이에서 노출하는 모든 관리 인터페이스는 강력한 자격 증명을 사용하여 보호해야 합니다. 또한 WiFi, SSH, 파일 공유, FTP와 같이 공개된 다른 인터페이스도 모두 강력한 자격 증명으로 보호해야 합니다. 기본적인 약한 암호는 사용하면 안됩니다. |

## <a id="unknown-exe"></a>디바이스에서 알 수 없는 코드를 실행할 수 없는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 디바이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Windows 10 IoT Core에서 보안 부팅 및 bit-locker 디바이스 암호화 사용](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **단계** | UEFI 보안 부팅은 시스템에서 지정된 권한으로 서명된 바이너리만 실행할 수 있도록 제한합니다. 이 기능은 플랫폼에서 알 수 없는 코드가 실행되고 잠재적으로 플랫폼의 보안 상태가 약화되지 않도록 방지합니다. UEFI 보안 부팅을 사용하도록 설정하고 서명 코드에 대해 신뢰할 수 있는 인증 기관 목록을 제한합니다. 신뢰할 수 있는 기관 중 하나를 사용하여 디바이스에 배포된 모든 코드에 서명합니다. |

## <a id="partition-iot"></a>bit-locker를 사용하여 OS 및 IoT 디바이스의 추가 파티션 암호화

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 디바이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | Windows 10 IoT Core는 필요한 측정을 수행하는 UEFI의 필수 preOS 프로토콜을 포함하여 플랫폼에서 TPM의 존재에 강하게 종속되는 경량 버전의 bit-locker 디바이스 암호화를 구현합니다. 이러한 preOS 측정을 통해 나중에 OS를 시작한 방식에 대한 명확한 기록이 OS에 있는지 확인할 수 있습니다. 중요한 데이터를 저장할 경우 bit-locker 및 추가 파티션을 사용하여 OS 파티션을 암호화합니다. |

## <a id="min-enable"></a>디바이스에서 최소 서비스/기능만 사용하도록 설정되어 있는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 디바이스 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 솔루션의 작동에 필요하지 않은 OS의 기능 또는 서비스를 사용하도록 설정하거나 해제하면 안됩니다. 예를 들어 디바이스에 UI를 배포할 필요가 없는 경우 Windows IoT Core를 헤드리스 모드로 설치합니다. |

## <a id="field-bit-locker"></a>bit-locker를 사용하여 OS 및 IoT 필드 게이트웨이의 추가 파티션 암호화

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 필드 게이트웨이 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | Windows 10 IoT Core는 필요한 측정을 수행하는 UEFI의 필수 preOS 프로토콜을 포함하여 플랫폼에서 TPM의 존재에 강하게 종속되는 경량 버전의 bit-locker 디바이스 암호화를 구현합니다. 이러한 preOS 측정을 통해 나중에 OS를 시작한 방식에 대한 명확한 기록이 OS에 있는지 확인할 수 있습니다. 중요한 데이터를 저장할 경우 bit-locker 및 추가 파티션을 사용하여 OS 파티션을 암호화합니다. |

## <a id="default-change"></a>설치 중에 필드 게이트웨이의 기본 로그인 자격 증명이 변경되었는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 필드 게이트웨이 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 설치 중에 필드 게이트웨이의 기본 로그인 자격 증명이 변경되었는지 확인합니다. |

## <a id="cloud-firmware"></a>클라우드 게이트웨이에서 연결된 디바이스 펌웨어를 최신 상태로 유지하는 프로세스를 구현하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 클라우드 게이트웨이 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 게이트웨이 선택 - Azure IoT Hub |
| **참조**              | [IoT Hub 디바이스 관리 개요](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [디바이스 펌웨어를 업데이트하는 방법](https://docs.microsoft.com/azure/iot-hub/tutorial-firmware-update) |
| **단계** | LWM2M은 IoT 디바이스 관리를 위한 Open Mobile Alliance의 프로토콜입니다. Azure IoT 디바이스 관리를 통해 디바이스 작업을 사용하여 물리적 디바이스와 상호 작용할 수 있습니다. 클라우드 게이트웨이에서 Azure IoT Hub 디바이스 관리를 사용하여 정기적으로 디바이스 및 기타 구성 데이터를 최신 상태로 유지하는 프로세스를 구현하는지 확인합니다. |

## <a id="controls-policies"></a>디바이스에서 조직 정책에 따라 구성된 끝점 보안 제어를 사용하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 컴퓨터 신뢰 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 디바이스에 디스크 수준 암호화를 위한 bit-locker, 업데이트된 서명이 있는 바이러스 백신, 호스트 기반 방화벽, OS 업그레이드, 그룹 정책 등과 같은 끝점 보안 제어 기능이 조직의 보안 정책에 따라 구성되어 있는지 확인합니다. |

## <a id="secure-keys"></a>Azure 저장소 액세스 키의 보안 관리 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Azure Storage 보안 가이드 - 스토리지 계정 키 관리](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **단계** | <p>키 스토리지: Azure Storage 액세스 키를 Azure Key Vault에 비밀로 저장하고, 애플리케이션에서 키 자격 증명 모음의 키를 검색하도록 하는 것이 좋습니다. 이는 다음과 같은 이유로 권장됩니다.</p><ul><li>애플리케이션에서 스토리지 키를 구성 파일에 하드 코딩하지 않습니다. 이렇게 하면 누군가가 특정 권한이 없이 키에 액세스할 수 있는 여지를 제거합니다.</li><li>Azure Active Directory를 사용하여 키에 대한 액세스를 제어할 수 있습니다. 즉 계정 소유자가 Azure Key Vault에서 키를 검색해야 하는 몇 가지 애플리케이션에 대한 액세스 권한을 부여할 수 있습니다. 다른 애플리케이션은 키에 대한 특별한 권한을 부여하지 않고는 해당 키에 액세스할 수 없습니다.</li><li>키 다시 생성: 보안상의 이유로 Azure 스토리지 액세스 키를 다시 생성하는 프로세스를 갖추는 것이 좋습니다. 키를 다시 생성하는 이유와 계획하는 방법에 대한 자세한 내용은 Azure Storage 보안 가이드 참조 문서에서 설명하고 있습니다.</li></ul>|

## <a id="cors-storage"></a>Azure 저장소에서 CORS를 사용하도록 설정하는 경우 신뢰할 수 있는 원본만 허용되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Azure Storage 서비스에 대한 CORS 지원](https://msdn.microsoft.com/library/azure/dn535601.aspx)(영문) |
| **단계** | Azure Storage를 사용하면 CORS(크로스 원본 자원 공유)를 사용하도록 설정할 수 있습니다. 각 저장소 계정에 대해 해당 저장소 계정의 리소스에 액세스할 수 있는 도메인을 지정할 수 있습니다. 기본적으로 CORS는 모든 서비스에서 사용되지 않도록 설정되어 있습니다. REST API 또는 저장소 클라이언트 라이브러리를 사용하여 서비스 정책을 설정하는 방법 중 하나를 호출하도록 CORS를 설정할 수 있습니다. |

## <a id="throttling"></a>WCF의 서비스 제한 기능을 사용하도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | .NET Framework 3 |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify, 영국](https://vulncat.fortify.com) |
| **단계** | <p>시스템 리소스 사용을 제한하지 않으면 리소스가 모두 소모되고 최종적으로 서비스가 거부될 수 있습니다.</p><ul><li>**설명:** WCF(Windows Communication Foundation)는 서비스 요청을 제한할 수 있는 기능을 제공합니다. 클라이언트 요청을 너무 많이 허용하면 시스템이 과도하게 작동되며 해당 리소스가 모두 소모될 수 있습니다. 반면에 약간의 서비스 요청만 허용하면 합법적인 사용자가 서비스를 사용할 수 없도록 방해할 수 있습니다. 각 서비스는 개별적으로 조정되어 적절한 양의 리소스를 허용하도록 구성해야 합니다.</li><li>**권장 사항:** WCF의 서비스 제한 기능을 사용하도록 설정하고 애플리케이션에 적합한 제한을 설정합니다.</li></ul>|

### <a name="example"></a>예
다음은 제한을 사용하도록 설정된 예제 구성입니다.
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>WCF - 메타데이터를 통한 정보 공개

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | .NET Framework 3 |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify, 영국](https://vulncat.fortify.com) |
| **단계** | 메타데이터는 공격자가 시스템에 대해 알아보고 공격 형태를 계획하는 데 도움이 될 수 있습니다. WCF 서비스는 메타데이터를 노출하도록 구성할 수 있습니다. 메타데이터는 자세한 서비스 설명 정보를 제공하며 프로덕션 환경에서 브로드캐스트하지 않아야 합니다. ServiceMetaData 클래스의 `HttpGetEnabled` / `HttpsGetEnabled` 속성은 서비스에서 메타데이터를 노출할지 여부를 정의합니다 | 

### <a name="example"></a>예
아래 코드는 WCF에서 서비스의 메타데이터를 브로드캐스트하도록 지시합니다.
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
프로덕션 환경에서 서비스 메타데이터를 브로드캐스트하면 안됩니다. ServiceMetaData 클래스의 HttpGetEnabled/HttpsGetEnabled 속성을 false로 설정합니다. 

### <a name="example"></a>예
아래 코드는 WCF에서 서비스의 메타데이터를 브로드캐스트하지 않도록 지시합니다. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
