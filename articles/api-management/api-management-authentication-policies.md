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
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: c0f8da779ca656cf357c418b8766a53307643695
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764350"
---
# <a name="api-management-authentication-policies"></a>API Management 인증 정책
이 토픽에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](https://go.microsoft.com/fwlink/?LinkID=398186)을 참조하세요.  

##  <a name="AuthenticationPolicies"></a> 인증 정책  
  
-   [기본 사용 인증](api-management-authentication-policies.md#Basic) - 기본 인증을 사용하여 백 엔드 서비스를 인증합니다.  
  
-   [클라이언트 인증서 사용 인증](api-management-authentication-policies.md#ClientCertificate) - 클라이언트 인증서를 사용하여 백 엔드 서비스를 인증합니다.  

-   [관리 되는 id를 사용 하 여 인증](api-management-authentication-policies.md#ManagedIdentity) -사용 하 여 인증 합니다 [관리 id](../active-directory/managed-identities-azure-resources/overview.md) API Management 서비스에 대 한 합니다.  
  
##  <a name="Basic"></a> 기본 사용 인증  
 `authentication-basic` 정책을 사용하여 기본 인증을 사용하는 백 엔드 서비스를 인증합니다. 이 정책은 HTTP 권한 부여 헤더를 정책에서 제공한 자격 증명에 해당하는 값으로 효과적으로 설정합니다.  
  
### <a name="policy-statement"></a>정책 문  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>예  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>요소  
  
|이름|설명|필수|  
|----------|-----------------|--------------|  
|인증-기본|루트 요소입니다.|예|  
  
### <a name="attributes"></a>특성  
  
|이름|설명|필수|기본값|  
|----------|-----------------|--------------|-------------|  
|username|기본 자격 증명의 사용자 이름을 지정합니다.|예|N/A|  
|암호|기본 자격 증명의 비밀번호를 지정합니다.|예|N/A|  
  
### <a name="usage"></a>사용 현황  
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.  
  
-   **정책 섹션:** inbound  
  
-   **정책 범위:** API  
  
##  <a name="ClientCertificate"></a> 클라이언트 인증서 사용 인증  
 `authentication-certificate` 정책을 사용하여 클라이언트 인증서를 사용하는 백 엔드 서비스를 인증합니다. 먼저 인증서를 [API Management에 설치](https://go.microsoft.com/fwlink/?LinkID=511599)하고 지문으로 식별해야 합니다.  
  
### <a name="policy-statement"></a>정책 문  
  
```xml  
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>  
```  
  
### <a name="examples"></a>예  
  
이 예제에서는 클라이언트 인증서의 지 문으로 식별 됩니다.
```xml  
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />  
``` 
이 예제에서 클라이언트 인증서는 리소스 이름으로 식별 됩니다.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>요소  
  
|이름|설명|필수|  
|----------|-----------------|--------------|  
|인증-인증서|루트 요소입니다.|예|  
  
### <a name="attributes"></a>특성  
  
|이름|설명|필수|기본값|  
|----------|-----------------|--------------|-------------|  
|thumbprint|클라이언트 인증서에 대한 지문입니다.|중 하나 `thumbprint` 또는 `certificate-id` 있어야 합니다.|N/A|  
|인증서 id|인증서 리소스 이름입니다.|중 하나 `thumbprint` 또는 `certificate-id` 있어야 합니다.|N/A|  
  
### <a name="usage"></a>사용 현황  
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.  
  
-   **정책 섹션:** inbound  
  
-   **정책 범위:** API  

##  <a name="ManagedIdentity"></a> 관리 되는 id를 사용 하 여 인증  
 사용 된 `authentication-managed-identity` API Management 서비스의 관리 되는 id를 사용 하 여 백 엔드 서비스를 사용 하 여 인증 하는 정책입니다. 이 정책은 효과적으로 관리 되는 id를 사용 하 여 지정된 된 리소스에 액세스 하기 위한 Azure Active Directory에서 액세스 토큰을 가져오려고 합니다. 
  
### <a name="policy-statement"></a>정책 문  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>예  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>요소  
  
|이름|설명|필수|  
|----------|-----------------|--------------|  
|authentication-managed-identity |루트 요소입니다.|예|  
  
### <a name="attributes"></a>특성  
  
|이름|설명|필수|기본값|  
|----------|-----------------|--------------|-------------|  
|resource|문자열입니다. Azure Active Directory에서 대상 웹 API (보안된 리소스)의 앱 ID URI입니다.|예|N/A|  
|output-token-variable-name|문자열입니다. 개체 형식으로 토큰 값을 받는 컨텍스트 변수의 이름을 `string`입니다.|아닙니다.|N/A|  
|ignore-error|부울 값입니다. 경우 설정 `true`, 정책 파이프라인 계속 액세스 토큰을 가져오지 않은 경우에 실행 됩니다.|아닙니다.|false|  
  
### <a name="usage"></a>사용 현황  
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.  
  
-   **정책 섹션:** inbound  
  
-   **정책 범위:** global, product, API, operation  

## <a name="next-steps"></a>다음 단계
정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ [정책 참조](api-management-policy-reference.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](policy-samples.md)   
