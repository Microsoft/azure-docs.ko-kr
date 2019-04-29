---
title: 중요한 데이터 - Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
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
ms.openlocfilehash: 27028903daeaf62a25584300944538341a861c80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610578"
---
# <a name="security-frame-sensitive-data--mitigations"></a>보안 프레임: 중요한 데이터 | 위험 완화 
| 제품/서비스 | 문서 |
| --------------- | ------- |
| **컴퓨터 신뢰 경계** | <ul><li>[중요한 정보가 포함된 경우 바이너리는 난독 처리되었는지 확인](#binaries-info)</li><li>[EFS(암호화 파일 시스템)를 사용하여 비밀 사용자 지정 데이터를 보호함](#efs-user)</li><li>[파일 시스템의 애플리케이션에서 저장한 중요한 데이터가 암호화되었는지 확인](#filesystem)</li></ul> | 
| **웹 애플리케이션** | <ul><li>[브라우저에서 중요한 콘텐츠가 캐시되지 않았는지 확인](#cache-browser)</li><li>[중요한 데이터가 포함된 Web App의 구성 파일의 섹션 암호화](#encrypt-data)</li><li>[중요한 양식 및 입력에서 자동 완성 HTML 특성 명시적으로 사용 중지](#autocomplete-input)</li><li>[사용자 화면에 표시되는 중요한 데이터가 마스킹되었는지 확인](#data-mask)</li></ul> | 
| **데이터베이스** | <ul><li>[동적 데이터 마스킹을 구현하여 권한 없는 사용자에 대한 중요한 데이터 노출 제한](#dynamic-users)</li><li>[암호가 솔트된 해시 형식으로 저장되었는지 확인](#salted-hash)</li><li>[데이터베이스 열의 중요 한 데이터가 암호화 되었는지 확인](#db-encrypted)</li><li>[TDE(데이터베이스 수준 암호화)가 활성화되어 있는지 확인](#tde-enabled)</li><li>[데이터베이스 백업이 암호화되었는지 확인](#backup)</li></ul> | 
| **앱 API** | <ul><li>[Web API와 관련된 중요한 데이터가 브라우저의 저장소에 저장되지 않았는지 확인](#api-browser)</li></ul> | 
| Azure Document DB | <ul><li>[Azure Cosmos DB에 저장된 중요한 데이터 암호화](#encrypt-docdb)</li></ul> | 
| **Azure IaaS VM 신뢰 경계** | <ul><li>[Azure Disk Encryption을 사용하여 Virtual Machines에 사용되는 디스크 암호화](#disk-vm)</li></ul> | 
| **Service Fabric 신뢰 경계** | <ul><li>[Service Fabric 애플리케이션에서 암호 암호화](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[보안 모델링 수행 및 필요한 경우 비즈니스 단위/팀 사용](#modeling-teams)</li><li>[액세스를 최소화하여 중요한 엔터티에 대한 기능 공유](#entities)</li><li>[Dynamics CRM 공유 기능 및 적절한 보안 사례와 관련된 위험에 대한 사용자 학습](#good-practices)</li><li>[예외 관리에서 구성 세부 정보를 표시하는 금지된 개발 표준 규칙 포함](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[미사용 데이터에 대한 Azure Storage 서비스 암호화(SSE) 사용(미리 보기)](#sse-preview)</li><li>[클라이언트 쪽 암호화를 사용하여 Azure Storage에 중요한 데이터 저장](#client-storage)</li></ul> | 
| **모바일 클라이언트** | <ul><li>[휴대폰 로컬 저장소에 기록된 중요한 PII 데이터 암호화](#pii-phones)</li><li>[최종 사용자에게 배포하기 전에 생성된 바이너리 난독 처리](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[ClientCredentialType을 인증서 또는 Windows 설정](#cert)</li><li>[WCF 보안 모드가 활성화되지 않음](#security)</li></ul> | 

## <a id="binaries-info"></a>중요한 정보가 포함된 경우 바이너리는 난독 처리되었는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 컴퓨터 신뢰 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 이진 파일이 영업 비밀인 뒤바뀌어서는 안되는 중요한 비즈니스 논리와 같은 중요한 정보를 포함하는 경우 난독 처리되어야 합니다. 어셈블리의 리버스 엔지니어링을 중지하는 것입니다. 이 목적을 위해 `CryptoObfuscator`와 같은 도구를 사용할 수 있습니다. |

## <a id="efs-user"></a>EFS(암호화 파일 시스템)를 사용하여 비밀 사용자 지정 데이터를 보호함

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 컴퓨터 신뢰 경계 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 컴퓨터에 대한 물리적 액세스를 사용하는 악의적 사용자에게서 비밀 사용자 지정 데이터를 보호하기 위해 EFS(암호화 파일 시스템)를 사용하는 것이 좋습니다. |

## <a id="filesystem"></a>파일 시스템의 애플리케이션에서 저장한 중요한 데이터가 암호화되었는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 컴퓨터 신뢰 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | EFS를 적용할 수 없는 경우 파일 시스템의 애플리케이션에서 저장한 중요한 데이터가 암호화되었는지(예: DPAPI 사용) 확인합니다. |

## <a id="cache-browser"></a>브라우저에서 중요한 콘텐츠가 캐시되지 않았는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, Web Forms, MVC5, MVC6 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 브라우저는 캐싱 및 기록을 목적으로 정보를 저장할 수 있습니다. 이러한 캐시된 파일은 Internet Explorer의 경우 임시 인터넷 파일 폴더와 같은 폴더에 저장됩니다. 이러한 페이지를 다시 참조하는 경우 브라우저는 해당 캐시의 페이지를 표시합니다. 중요한 정보를 사용자에게 표시하는 경우(예: 해당 주소, 신용 카드 정보, 주민 등록 번호 또는 사용자 이름) 이 정보를 브라우저의 캐시에 저장할 수 있습니다. 따라서 브라우저의 캐시를 검사하거나 브라우저의 "뒤로" 단추를 눌러서 검색할 수 있습니다. 캐시 제어 응답 헤더 값을 모든 페이지에 대해 “no-store”로 설정합니다. |

### <a name="example"></a>예
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>예
이 예제는 필터를 통해 구현될 수 있습니다. 다음 예제를 사용할 수 있습니다. 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a id="encrypt-data"></a>중요한 데이터가 포함된 Web App의 구성 파일의 섹션 암호화

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [방법: DPAPI를 사용하여 ASP.NET 2.0에서 구성 섹션 암호화](https://msdn.microsoft.com/library/ff647398.aspx), [보호되는 구성 공급자 지정](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Azure Key Vault를 사용하여 애플리케이션 비밀 보호](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **단계** | Web.config, appsettings.json과 같은 구성 파일은 사용자 이름, 암호, 데이터베이스 연결 문자열 및 암호화 키를 포함하여 중요한 정보를 저장하는 데 자주 사용됩니다. 이러한 정보를 보호하지 않으면 애플리케이션은 계정 사용자 이름과 암호, 데이터베이스 이름과 서버 이름 등과 같은 중요한 정보를 얻는 공격자 또는 악의적인 사용자에 대해 취약해집니다. 배포 유형(azure/on-prem)에 따라 DPAPI 또는 Azure Key Vault와 같은 서비스를 사용하여 구성 파일의 중요한 섹션을 암호화합니다. |

## <a id="autocomplete-input"></a>중요한 양식 및 입력에서 자동 완성 HTML 특성 명시적으로 사용 중지

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [MSDN: 자동 완성 특성](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [HTML에서 자동 완성 기능 사용](https://msdn.microsoft.com/library/ms533032.aspx), [HTML 삭제 취약점](https://technet.microsoft.com/security/bulletin/MS10-071), [자동 완성., 다시?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **단계** | 자동 완성 특성은 양식이 자동 완성을 사용해야 할지 여부를 지정합니다. 자동 완성이 켜져 있으면 브라우저는 사용자가 이전에 입력한 값을 기반으로 값을 자동으로 완성합니다. 예를 들어 양식에 새 이름 및 암호를 입력하고 양식을 제출하면 브라우저는 암호를 저장해야 하는지 묻습니다. 이후로 양식이 표시되면 이름 및 암호가 자동으로 채워지거나 이름을 입력하는 대로 완성합니다. 로컬 액세스 권한이 있는 공격자는 브라우저 캐시에서 일반 텍스트 암호를 얻을 수 있습니다. 기본적으로 자동 완성은 활성화되어 있으므로 명시적으로 비활성화되어야 합니다. |

### <a name="example"></a>예
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>사용자 화면에 표시되는 중요한 데이터가 마스킹되었는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 암호, 신용 카드 번호, SSN과 같은 중요한 데이터가 화면에 표시하는 경우 마스킹되도록 해야 합니다. 그러면 권한 없는 사용자가 데이터에 액세스하지 않도록 방지하게 됩니다(예: 어깨 너머로 알게 된 암호, 사용자의 SSN 숫자를 보는 고객 지원 담당자). 이러한 데이터 요소가 일반 텍스트로 표시되지 않고 적절하게 마스킹되도록 합니다. 이러한 항목은 입력으로 허용(예: 입력 형식="암호")하고 화면에 다시 표시(예: 신용 카드 번호의 마지막 4개 숫자만 표시)하는 동안 처리되어야 합니다. |

## <a id="dynamic-users"></a>동적 데이터 마스킹을 구현하여 권한 없는 사용자에 대한 중요한 데이터 노출 제한

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | SQL Azure, 온-프레미스 |
| **특성**              | SQL 버전 - V12, SQL 버전 - MsSQL2016 |
| **참조**              | [동적 데이터 마스킹](https://msdn.microsoft.com/library/mt130841) |
| **단계** | 동적 데이터 마스킹의 목적은 중요한 데이터의 노출을 제한하여 데이터에 대한 액세스 권한이 없는 사용자가 볼 수 없도록 방지하는 것입니다. 동적 데이터 마스킹은 데이터베이스 사용자가 데이터베이스에 직접 연결하여 중요한 데이터를 노출하는 철저한 쿼리를 실행하지 못하도록 방지할 목적이 아닙니다. 동적 데이터 마스킹은 다른 SQL Server 보안 기능(감사, 암호화, 행 수준 보안...)을 보완합니다. 데이터베이스에서 중요한 데이터의 보안을 향상하기 위해 함께 이 기능을 사용하는 것이 좋습니다. 이 기능은 SQL Server 2016 이상 및 Azure SQL Database에서만 지원됩니다. |

## <a id="salted-hash"></a>암호가 솔트된 해시 형식으로 저장되었는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [.NET Crypto API를 사용하여 암호 해싱](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **단계** | 암호는 사용자 지정 사용자 저장소 데이터베이스에 저장되어야 합니다. 암호 해시는 대신 솔트 값으로 저장되어야 합니다. 사용자에 대한 솔트는 항상 고유해야 하며 무차별 암호 강제 적용 가능성을 제거하기 위해 암호를 저장하기 전에 150,000번의 루프라는 최소 작업 요소 반복 횟수로 bcrypt, scrypt 또는 PBKDF2를 적용해야 합니다.| 

## <a id="db-encrypted"></a>데이터베이스 열의 중요한 데이터가 암호화되었는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | SQL 버전 - 모두 |
| **참조**              | [SQL 서버에서 중요한 데이터 암호화](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [방법: SQL Server에서 데이터의 열 암호화](https://msdn.microsoft.com/library/ms179331), [인증서로 암호화](https://msdn.microsoft.com/library/ms188061) |
| **단계** | 신용 카드 번호와 같은 중요한 데이터는 데이터베이스에서 암호화되어야 합니다. 데이터는 열 수준 암호화를 사용하거나 암호화 기능을 사용하는 애플리케이션 기능에 의해 암호화될 수 있습니다. |

## <a id="tde-enabled"></a>TDE(데이터베이스 수준 암호화)가 활성화되어 있는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [SQL Server TDE(투명한 데이터 암호화) 이해](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **단계** | SQL Server의 TDE(투명한 데이터 암호화) 기능을 통해 데이터베이스에서 중요한 데이터를 암호화하고 인증서를 사용하여 데이터를 암호화하는 데 사용되는 키를 보호할 수 있습니다. 그러면 키가 없는 사용자가 데이터를 사용하지 않도록 방지합니다. TDE는 데이터와 로그 파일을 의미하는 미사용 데이터를 보호합니다. 다양한 산업 분야에서 제정된 많은 법률, 규정 및 지침을 준수하는 기능을 제공합니다. |

## <a id="backup"></a>데이터베이스 백업이 암호화되었는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | SQL Azure, 온-프레미스 |
| **특성**              | SQL 버전 - V12, SQL 버전 - MsSQL2014 |
| **참조**              | [SQL Database 백업 암호화](https://msdn.microsoft.com/library/dn449489) |
| **단계** | SQL Server에는 백업을 만드는 동안 데이터를 암호화하는 기능이 있습니다. 암호화 알고리즘 및 암호기(인증서 또는 비대칭 키)를 지정하여 백업을 만들 때 암호화된 백업 파일을 만들 수 있습니다. |

## <a id="api-browser"></a>Web API와 관련된 중요한 데이터가 브라우저의 저장소에 저장되지 않았는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC 5, MVC 6 |
| **특성**              | ID 공급자 - ADFS, ID 공급자 - Azure AD |
| **참조**              | N/A  |
| **단계** | <p>특정 구현에서 Web API의 인증과 관련된 중요한 아티팩트는 브라우저의 로컬 저장소에 저장됩니다. 예를 들어, adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key 등과 같은 Azure AD 인증 아티팩트입니다.</p><p>이러한 아티팩트는 모두 로그아웃 또는 브라우저가 닫힌 후에도 사용할 수 있습니다. 악의적 사용자가 이러한 아티팩트에 대한 액세스 권한을 가져온 경우 보호된 리소스(API)에 액세스하기 위해 다시 사용할 수 있습니다. Web API와 관련된 중요한 아티팩트가 모두 브라우저의 저장소에 저장되지 않았는지 확인합니다. 클라이언트 쪽 스토리지를 피할 수 없는 경우(예: 암시적 OpenIdConnect/OAuth 흐름을 활용하는 SPA(단일 페이지 애플리케이션)는 액세스 토큰을 로컬로 저장함) 스토리지 선택 항목 사용은 지속성을 갖지 않습니다. 예를 들어, LocalStorage보다 SessionStorage를 선호합니다.</p>| 

### <a name="example"></a>예
아래 JavaScript 코드 조각은 로컬 저장소에 인증 아티팩트를 저장하는 사용자 지정 인증 라이브러리에서 비롯됩니다. 이렇게 구현하지 않아야 합니다. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a id="encrypt-docdb"></a>Cosmos DB에 저장된 중요한 데이터 암호화

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Document DB | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 문서 DB에서 저장하기 전에 응용 프로그램 수준에서 중요한 데이터를 암호화하거나 Azure Storage 또는 Azure SQL과 같은 다른 스토리지 솔루션에서 중요한 데이터를 저장합니다.| 

## <a id="disk-vm"></a>Azure Disk Encryption을 사용하여 Virtual Machines에 사용되는 디스크 암호화

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure IaaS VM 트러스트 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Azure Disk Encryption를 사용하여 가상 머신에 사용되는 디스크 암호화](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **단계** | <p>Azure 디스크 암호화는 현재 미리 보기에 포함되어 있는 새로운 기능입니다. 이 기능을 사용하면 IaaS Virtual Machine에서 사용되는 OS 디스크 및 데이터 디스크를 암호화할 수 있습니다. Windows의 경우 업계 표준의 BitLocker 암호화 기술을 사용하여 드라이브가 암호화됩니다. Linux의 경우 DM-Crypt 기술을 사용하여 디스크가 암호화됩니다. 이 기술은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어 및 관리할 수 있도록 합니다. Azure 디스크 암호화 솔루션은 다음 3가지 고객 암호화 시나리오를 지원합니다.</p><ul><li>Azure Key Vault에 저장되는 고객 암호화 VHD 파일 및 고객 제공 암호화 키에서 만든 새 IaaS VM에 대해 암호화를 사용하도록 설정합니다.</li><li>Azure Marketplace에서 만든 새 IaaS VM에 대해 암호화를 사용하도록 설정합니다.</li><li>Azure에서 이미 실행 중인 기존 IaaS VM에 대해 암호화를 사용하도록 설정합니다.</li></ul>| 

## <a id="fabric-apps"></a>Service Fabric 애플리케이션에서 암호 암호화

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Service Fabric 트러스트 경계 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 환경 - Azure |
| **참조**              | [Service Fabric 애플리케이션에서 비밀 관리](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **단계** | 저장소 연결 문자열, 암호, 일반 텍스트로 처리하면 안 되는 값 등 모든 민감한 정보를 비밀로 처리할 수 있습니다. Azure Key Vault를 사용하여 Service Fabric 애플리케이션에서 키와 암호를 관리합니다. |

## <a id="modeling-teams"></a>보안 모델링 수행 및 필요한 경우 비즈니스 단위/팀 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Dynamics CRM | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 보안 모델링 수행 및 필요한 경우 비즈니스 단위/팀 사용 |

## <a id="entities"></a>액세스를 최소화하여 중요한 엔터티에 대한 기능 공유

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Dynamics CRM | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 액세스를 최소화하여 중요한 엔터티에 대한 기능 공유 |

## <a id="good-practices"></a>Dynamics CRM 공유 기능 및 적절한 보안 사례와 관련된 위험에 대한 사용자 학습

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Dynamics CRM | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | Dynamics CRM 공유 기능 및 적절한 보안 사례와 관련된 위험에 대한 사용자 학습 |

## <a id="exception-mgmt"></a>예외 관리에서 구성 세부 정보를 표시하는 금지된 개발 표준 규칙 포함

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Dynamics CRM | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 배포 외부의 예외 관리에서 구성 세부 정보를 표시하는 금지된 개발 표준 규칙을 포함합니다. 코드 검토 또는 정기적 검사의 일부로 이 항목을 테스트합니다.|

## <a id="sse-preview"></a>미사용 데이터에 대한 Azure Storage 서비스 암호화(SSE) 사용(미리 보기)

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | StorageType - Blob |
| **참조**              | [미사용 데이터에 대한 Azure Storage 서비스 암호화(미리 보기)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **단계** | <p>미사용 데이터에 대한 Azure Storage 서비스 암호화(SSE)를 사용하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 이 기능을 통해 Azure Storage는 스토리지를 유지하기 전에 데이터를 자동으로 암호화하고 검색하기 전에 암호를 해독합니다. 암호화, 암호 해독 및 키 관리는 사용자에게 완전히 투명하게 처리됩니다. SSE는 블록 Blob, 페이지 Blob 및 추가 Blob에만 적용됩니다. 테이블, 큐 및 파일을 비롯한 다른 유형의 데이터는 암호화되지 않습니다.</p><p>워크플로 암호화 및 암호 해독:</p><ul><li>고객이 저장소 계정에 대해 암호화를 설정합니다.</li><li>고객이 새 데이터(PUT Blob, PUT 블록, PUT 페이지 등)를 Blob Storage에 기록할 경우 모든 기록 내용이 가장 강력한 블록 암호화 중 하나인 256비트 AES 암호화를 사용하여 암호화됩니다.</li><li>고객이 데이터(GET Blob 등)에 액세스해야 하는 경우 사용자에게 반환되기 전에 데이터가 자동으로 해독됩니다.</li><li>암호화를 사용하지 않도록 설정하면 새로운 기록 내용은 더 이상 암호화되지 않으며 기존 암호화된 데이터는 사용자가 다시 작성할 때까지 암호화된 상태로 유지됩니다. 암호화를 사용하는 동안 Blob Storage에 기록된 내용이 암호화됩니다. 저장소 계정에 대해 암호화를 사용/사용하지 않는 것으로 사용자 전환하는 것으로 데이터의 상태는 변경되지 않습니다.</li><li>모든 암호화 키는 Microsoft에서 저장하고 암호화하며 관리합니다.</li></ul><p>현재 암호화에 사용되는 키는 Microsoft에서 관리합니다. Microsoft에서는 처음에 키를 생성한 후에, 내부 Microsoft 정책에 정의된 대로 키의 정기적인 순환뿐 아니라 보안 저장도 관리합니다. 나중에 고객은 고유한 암호화 키를 관리하는 기능을 사용할 수 있게 되며 Microsoft에서 관리하는 키에서 고객이 관리하는 키로 마이그레이션 경로를 제공할 예정입니다.</p>| 

## <a id="client-storage"></a>클라이언트 쪽 암호화를 사용하여 Azure Storage에 중요한 데이터 저장

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Microsoft Azure Storage용 클라이언트 쪽 암호화 및 Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [자습서: Azure Key Vault를 사용하여 Microsoft Azure Storage에서 Blob 암호화 및 해독](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [Azure 암호화 확장을 사용하여 Azure Blob Storage에서 데이터 안전하게 저장](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **단계** | <p>.NET용 Azure Storage 클라이언트 라이브러리 Nuget 패키지는 Azure Storage에 업로드하기 전에 클라이언트 애플리케이션 내에서 데이터를 암호화하고 클라이언트로 다운로드하는 동안 데이터를 해독하는 기능을 지원합니다. 라이브러리 또한 저장소 계정 키 관리를 위해 Azure Key Vault와의 통합을 지원합니다. 클라이언트 쪽 암호화의 작동 원리에 대한 간단한 설명은 다음과 같습니다.</p><ul><li>Azure Storage 클라이언트 SDK는 1회용 대칭 키인 CEK(콘텐츠 암호화 키)를 생성합니다.</li><li>고객 데이터는 이 CEK를 사용하여 암호화됩니다.</li><li>그런 다음 키 암호화 KEK를 사용하여 CEK를 래핑(암호화)합니다. KEK는 키 식별자로 식별되고 비대칭 키 쌍 또는 대칭 키일 수 있으며 로컬로 관리되거나 Azure Key Vault에 저장됩니다. Storage 클라이언트 자체는 KEK에 액세스할 수 없습니다. 단지 키 자격 증명 모음에서 제공되는 키 래핑 알고리즘을 호출할 뿐입니다. 고객은 원하는 경우 키 래핑/래핑 해제를 위해 사용자 지정 공급자를 사용하도록 선택할 수 있습니다.</li><li>그런 다음 암호화된 데이터를 Azure Storage 서비스에 업로드합니다. 자세한 구현 세부 정보는 참조 섹션의 링크를 확인하세요.</li></ul>|

## <a id="pii-phones"></a>휴대폰 로컬 저장소에 기록된 중요한 PII 데이터 암호화

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 모바일 클라이언트 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, Xamarin  |
| **특성**              | N/A  |
| **참조**              | [Microsoft Intune 정책을 사용하여 디바이스에서 설정 및 기능 관리](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies), [키 집합 Valet](https://components.xamarin.com/view/square.valet) |
| **단계** | <p>애플리케이션이 모바일의 파일 시스템에 사용자의 PII(이메일, 전화 번호, 이름, 성, 선호도 등)와 같은 중요한 정보를 기록하는 경우 로컬 파일 시스템에 쓰기 전에 암호화되어야 합니다. 엔터프라이즈 애플리케이션인 애플리케이션을 사용하는 경우 Windows Intune을 사용하여 애플리케이션을 게시하는 가능성을 탐색합니다.</p>|

### <a name="example"></a>예
Intune은 중요한 데이터를 보호하는 다음과 같은 보안 정책을 사용하여 구성될 수 있습니다. 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>예
엔터프라이즈 애플리케이션이 아닌 애플리케이션을 사용하는 경우 파일 시스템에서 수행될 수 있는 암호화 작업을 사용하여 플랫폼 제공 키 스토리지, 키 집합을 사용하여 암호화 키를 저장합니다. 다음 코드 조각에서는 xamarin을 사용하는 키 집합에서 키에 액세스하는 방법을 보여 줍니다. 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a id="binaries-end"></a>최종 사용자에게 배포하기 전에 생성된 바이너리 난독 처리

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 모바일 클라이언트 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [.NET에 대한 암호화 난독 처리](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **단계** | 생성된 이진 파일(apk 내의 어셈블리)을 난독 처리하여 어셈블리의 리버스 엔지니어링을 중지해야 합니다. 이 목적을 위해 `CryptoObfuscator`와 같은 도구를 사용할 수 있습니다. |

## <a id="cert"></a>clientCredentialType을 인증서 또는 Windows로 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | .NET Framework 3 |
| **특성**              | N/A  |
| **참조**              | [강화](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **단계** | 암호화되지 않은 채널을 통해 일반 텍스트 암호를 사용한 UsernameToken를 사용하면 SOAP 메시지를 찾아낼 수 있는 공격자에게 암호를 노출하게 됩니다. UsernameToken를 사용하는 서비스 공급자는 일반 텍스트로 전송된 암호를 허용할 수 있습니다. 암호화되지 않은 채널을 통해 일반 텍스트 암호는 보내면 SOAP 메시지를 찾아낼 수 있는 공격자에게 자격 증명을 노출할 수 있습니다. | 

### <a name="example"></a>예
다음 WCF 서비스 공급자 구성에서는 UsernameToken을 사용합니다. 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
clientCredentialType을 인증서 또는 Windows로 설정 

## <a id="security"></a>WCF 보안 모드가 활성화되지 않음

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, .NET Framework 3 |
| **특성**              | 보안 모드 - 전송, 보안 모드 - 메시지 |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [보안 강화](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [WCF 보안 CoDe Magazine의 기본 사항](https://www.codemag.com/article/0611051) |
| **단계** | 전송 또는 메시지 보안이 거부되지 않았습니다. 전송 또는 메시지 보안 없이 메시지를 전송하는 애플리케이션은 메시지의 무결성이나 기밀성을 보장할 수 없습니다. WCF 보안 바인딩이 없음으로 설정되면 전송 및 메시지 보안이 모두 비활성화됩니다. |

### <a name="example"></a>예
다음 구성에서는 보안 모드를 없음으로 설정합니다. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>예
5개의 가능한 보안 모드가 있는 모든 서비스 바인딩에 대한 보안 모드는 다음과 같습니다. 
* 없음. 보안 해제. 
* 전송. 상호 인증과 메시지 보호를 위해 전송 보안 사용. 
* Message. 상호 인증과 메시지 보호를 위해 메시지 보안 사용. 
* 둘 다. 전송 및 메시지 수준 보안(MSMQ에서만 지원됨)에 대한 설정을 제공할 수 있습니다. 
* TransportWithMessageCredential. 자격 증명은 메시지 및 메시지 보호를 사용하여 전달되고 서버 인증은 전송 계층에서 제공합니다. 
* TransportCredentialOnly. 클라이언트 자격 증명은 전송 계층으로 전달되고 메시지 보호가 적용되지 않습니다. 전송 및 메시지 보안을 사용하여 메시지의 무결성 및 기밀성을 보호합니다. 아래 구성에서는 서비스가 메시지 자격 증명과 함께 전송 보안을 사용하도록 지시합니다.
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```
