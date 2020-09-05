---
title: 인증, 요청 및 응답
description: Azure Key Vault에서 JSON 형식 요청 및 응답과 키 자격 증명 모음을 사용 하는 데 필요한 인증을 사용 하는 방법에 대해 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 2b4c8ad666efa32d98e78a0bc2544d0f8851be5e
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191791"
---
# <a name="authentication-requests-and-responses"></a>인증, 요청 및 응답

Azure Key Vault는 JSON 형식 요청과 응답을 지원합니다. Azure Key Vault에 대한 요청은 일부 URL 매개 변수 및 JSON 인코딩 요청 및 응답 본문을 통해 HTTPS를 사용하여 올바른 Azure Key Vault URL로 이동됩니다.

이 항목에서는 Azure Key Vault 서비스에 대한 구체적인 정보를 다룹니다. 인증/권한 부여 및 액세스 토큰을 확보하는 방법을 비롯한 Azure REST 인터페이스 사용에 대한 일반적인 정보는 [Azure REST API 참조](https://docs.microsoft.com/rest/api/azure)를 참조하세요.

## <a name="request-url"></a>요청 URL  
 키 관리 작업은 HTTP DELETE, GET, PATCH, PUT 및 HTTP POST 및 HTTP POST를 사용하는 기존 키 개체에 대한 암호화 작업을 사용합니다. 특정 HTTP 동사를 지원할 수 없는 클라이언트는 X-HTTP-REQUEST 헤더를 사용한 HTTP POST를 사용하여 대상 동사를 지정할 수 있습니다. HTTP POST를 사용하는 경우(예: DELETE 대신 POST를 사용하는 경우) 일반적으로 본문이 필요하지 않은 요청은 빈 본문을 포함해야 합니다.  

 Azure Key Vault에 있는 개체를 사용하기 위한 예제 URL은 다음과 같습니다.  

- Key Vault에서 TESTKEY라는 키를 만들려면 `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1` 사용  

- Key Vault에서 IMPORTEDKEY라는 키를 가져오려면 `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1` 사용  

- Key Vault에서 MYSECRET이라는 비밀을 가져오려면 `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1` 사용  

- Key Vault에서 TESTKEY라는 키를 사용하여 다이제스트에 서명하려면 `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1` 사용  

  Key Vault에 대한 요청을 위한 기관은 항상 다음과 같습니다. `https://{keyvault-name}.vault.azure.net/`  

  키는 항상 /keys 경로에 저장되며, 비밀은 항상 /secrets 경로에 저장됩니다.  

## <a name="api-version"></a>API 버전  
 Azure Key Vault 서비스는 일부 기능을 해당 클라이언트에서 사용할 수 있는 하위 수준 클라이언트와의 호환성을 제공하기 위해 프로토콜 버전 관리를 지원합니다. 클라이언트는 기본값이 없으므로 지원하는 프로토콜의 버전을 지정하려면 `api-version` 쿼리 문자열 매개 변수를 사용해야 합니다.  

 Azure Key Vault 프로토콜 버전은 {YYYY} {MM} {DD} 형식을 사용하는 날짜 수 스키마를 따릅니다.  

## <a name="request-body"></a>요청 본문  
 HTTP 사양에 따라 GET 작업은 요청 본문을 포함할 수 없으며 POST 및 PUT 작업은 요청 본문을 포함해야 합니다. DELETE 작업의 본문은 HTTP의 경우 선택 사항입니다.  

 작업 설명에 별도로 언급되지 않는 한, 요청 본문 콘텐츠 형식은 application/json이어야 하며 콘텐츠 형식을 따르는 직렬화된 JSON 개체를 포함해야 합니다.  

 작업 설명에 별도로 언급되지 않는 한, Accept 요청 헤더는 application/json 미디어 유형을 포함해야 합니다.  

## <a name="response-body"></a>응답 본문  
 작업 설명에 별도로 언급되지 않는 한, 성공 및 실패한 작업의 응답 본문 콘텐츠 형식은 application/json이어야 하며 자세한 오류 정보를 포함합니다.  

## <a name="using-http-post"></a>HTTP POST 사용  
 일부 클라이언트는 PATCH 또는 DELETE와 같은 특정 HTTP 동사를 사용할 수 없습니다. Azure Key Vault는 원래 HTTP 동사에 특정적인 “X-HTTP-METHOD” 헤더가 포함되어 제공되는 이러한 클라이언트에 대한 대안으로 HTTP POST를 지원합니다. HTTP POST에 대한 지원은 이 문서에서 정의된 각 API에 대해 언급됩니다.  

## <a name="error-responses"></a>오류 응답  
 오류 처리는 HTTP 상태 코드를 사용합니다. 일반적인 결과는 다음과 같습니다.  

- 2xx – 성공: 일반 작업에 사용됩니다. 응답 본문은 예상된 결과를 포함합니다.  

- 3xx – 리디렉션: 조건부 GET을 충족하기 위해 304 “수정되지 않음”이 반환될 수 있습니다. 나중에 DNS 및 경로 변경을 나타내기 위해 다른 3xx 코드가 사용될 수 있습니다.  

- 4xx – 클라이언트 오류: 잘못 된 요청, 누락 된 키, 구문 오류, 잘못 된 매개 변수, 인증 오류 등에 사용 됩니다. 응답 본문에는 자세한 오류 설명이 포함 됩니다.  

- 5xx – 서버 오류: 내부 서버 오류에 사용됩니다. 응답 본문에는 요약된 오류 정보가 포함됩니다.  

  시스템은 프록시 또는 방화벽 뒤에서 작업하도록 고안되었습니다. 따라서 클라이언트는 다른 오류 코드를 받을 수도 있습니다.  

  또한 Azure Key Vault는 문제가 발생하는 경우 응답 본문에 오류 정보를 반환합니다. 응답 본문은 JSON 형식이며 다음과 같은 형식을 사용합니다.  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>인증  
 Azure Key Vault에 대한 모든 요청은 인증되어야 합니다. Azure Key Vault는 OAuth2를 사용하여 가져올 수 있는 Azure Active Directory 액세스 토큰을 지원합니다[[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 애플리케이션 등록 및 Azure Key Vault 사용을 위한 인증에 대한 자세한 내용은 [Azure AD로 클라이언트 애플리케이션 등록](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad)을 참조하세요.
 
 액세스 토큰은 HTTP 권한 부여 헤더를 사용하여 서비스에 전송해야 합니다.  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 액세스 토큰이 적용되지 않거나 토큰이 서비스에서 수락되지 않는 경우 HTTP 401 오류가 클라이언트에 반환되고 다음 예제와 같이 WWW-Authenticate 헤더를 포함하게 됩니다.  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 WWW-Authenticate 헤더에 대한 매개 변수는 다음과 같습니다.  

-   권한 부여: 요청에 대한 액세스 토큰을 가져오는 데 사용할 수 있는 OAuth2 권한 부여 서비스의 주소입니다.  

-   리소스: `https://vault.azure.net` 권한 부여 요청에 사용할 리소스 ()의 이름입니다.  

