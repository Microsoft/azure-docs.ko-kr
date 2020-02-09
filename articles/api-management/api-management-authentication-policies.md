---
title: Azure API Management 인증 정책 | Microsoft Docs
description: Azure API Management에 사용할 수 있는 인증 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 572d8c4b59622156e8b3aca4565bbc206367f6d4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514846"
---
# <a name="api-management-authentication-policies"></a>API Management 인증 정책
이 토픽에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](https://go.microsoft.com/fwlink/?LinkID=398186)을 참조하세요.

##  <a name="AuthenticationPolicies"></a> 인증 정책

-   [기본 사용 인증](api-management-authentication-policies.md#Basic) - 기본 인증을 사용하여 백 엔드 서비스를 인증합니다.

-   [클라이언트 인증서 사용 인증](api-management-authentication-policies.md#ClientCertificate) - 클라이언트 인증서를 사용하여 백 엔드 서비스를 인증합니다.

-   [관리 id로 인증](api-management-authentication-policies.md#ManagedIdentity) -API Management 서비스에 대한 [관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 인증 합니다.

##  <a name="Basic"></a> 기본 사용 인증
 `authentication-basic` 정책을 사용하여 기본 인증을 사용하는 백 엔드 서비스를 인증합니다. 이 정책은 HTTP 권한 부여 헤더를 정책에서 제공한 자격 증명에 해당하는 값으로 효과적으로 설정합니다.

### <a name="policy-statement"></a>정책 명령문

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>예제

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>요소

|name|설명|필수|
|----------|-----------------|--------------|
|인증-기본|루트 요소입니다.|yes|

### <a name="attributes"></a>특성

|name|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|username|기본 자격 증명의 사용자 이름을 지정합니다.|yes|N/A|
|암호|기본 자격 증명의 비밀번호를 지정합니다.|yes|N/A|

### <a name="usage"></a>사용량
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** 인바운드

-   **정책 범위:** 모든 범위

##  <a name="ClientCertificate"></a> 클라이언트 인증서 사용 인증
 `authentication-certificate` 정책을 사용하여 클라이언트 인증서를 사용하는 백 엔드 서비스를 인증합니다. 먼저 인증서를 [API Management에 설치](https://go.microsoft.com/fwlink/?LinkID=511599)하고 지문으로 식별해야 합니다.

### <a name="policy-statement"></a>정책 명령문

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>예시

이 예제에서는 클라이언트 인증서가 해당 지 문으로 식별 됩니다.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
이 예제에서 클라이언트 인증서는 리소스 이름으로 식별 됩니다.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>요소  
  
|name|설명|필수|  
|----------|-----------------|--------------|  
|인증-인증서|루트 요소입니다.|yes|  
  
### <a name="attributes"></a>특성  
  
|name|설명|필수|기본값|  
|----------|-----------------|--------------|-------------|  
|thumbprint|클라이언트 인증서에 대한 지문입니다.|@No__t_0 또는 `certificate-id`이 있어야 합니다.|N/A|  
|인증서 id|인증서 리소스 이름입니다.|@No__t_0 또는 `certificate-id`이 있어야 합니다.|N/A|  
  
### <a name="usage"></a>사용량  
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.  
  
-   **정책 섹션:** 인바운드  
  
-   **정책 범위:** 모든 범위  

##  <a name="ManagedIdentity"></a>관리 id를 사용 하 여 인증  
 @No__t_0 정책을 사용 하 여 API Management 서비스의 관리 id를 사용 하 여 백 엔드 서비스로 인증 합니다. 이 정책은 기본적으로 관리 id를 사용 하 여 Azure Active Directory에서 지정 된 리소스에 액세스 하기 위한 액세스 토큰을 가져옵니다. 토큰을 성공적으로 가져오면 정책에서는 `Bearer` 체계를 사용 하 여 `Authorization` 헤더의 토큰 값을 설정 합니다.
  
### <a name="policy-statement"></a>정책 명령문  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>예제  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>관리 id를 사용 하 여 백 엔드 서비스 인증
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>전송 요청 정책에서 관리 id 사용
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>요소  
  
|name|설명|필수|  
|----------|-----------------|--------------|  
|인증 관리-id |루트 요소입니다.|yes|  
  
### <a name="attributes"></a>특성  
  
|name|설명|필수|기본값|  
|----------|-----------------|--------------|-------------|  
|resource|문자열입니다. Azure Active Directory에 있는 대상 web API (보안 리소스)의 앱 ID URI입니다.|yes|N/A|  
|출력-토큰 변수-이름|문자열입니다. @No__t_0 개체 형식으로 토큰 값을 받는 컨텍스트 변수의 이름입니다. |아닙니다.|N/A|  
|ignore-error|부울 값입니다. @No__t_0로 설정 된 경우 액세스 토큰을 가져올 수 없더라도 정책 파이프라인은 계속 실행 됩니다.|아닙니다.|false|  
  
### <a name="usage"></a>사용량  
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.  
  
-   **정책 섹션:** 인바운드  
  
-   **정책 범위:** 모든 범위

## <a name="next-steps"></a>다음 단계
정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ 정책 명령문 및 설정의 전체 목록에 대한 [정책 참조](api-management-policy-reference.md)
+ [정책 샘플](policy-samples.md)
