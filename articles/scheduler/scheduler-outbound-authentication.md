---
title: 아웃바운드 인증
description: Azure Scheduler에 대한 아웃바운드 인증을 설정 또는 제거하는 방법 알아보기
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 0a8d79af9f45731971cb1be1f39fc193f9d0f0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80878972"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Azure Scheduler에 대한 아웃바운드 인증

> [!IMPORTANT]
> [Azure Scheduler](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)는 조만간 사용 중지되고 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)로 대체됩니다. Scheduler에서 설정한 작업으로 계속 작업하려면 가능한 한 빨리 [Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md)로 마이그레이션하세요. 
>
> Scheduler는 더 이상 Azure Portal에서 사용할 수 없지만 [REST API](/rest/api/scheduler) 및 [Azure Scheduler PowerShell cmdlet](scheduler-powershell-reference.md)은 현재 사용 가능하므로 작업 및 작업 컬렉션을 관리할 수 있습니다.

Azure Scheduler 작업은 다른 Azure 서비스, Salesforce.com, Facebook, 보안 사용자 지정 웹 사이트 등과 같이 인증이 필요한 서비스를 호출해야 하는 경우가 있습니다. 호출된 서비스에 따라 Scheduler 작업이 요청된 리소스에 액세스할 수 있는지 여부가 결정될 수 있습니다. 

Scheduler는 다음과 같은 인증 모델을 지원합니다. 

* SSL/TLS 클라이언트 인증서를 사용하는 경우 *클라이언트 인증서* 인증
* *기본* 인증
* *Active Directory OAuth* 인증

## <a name="add-or-remove-authentication"></a>인증 추가 또는 제거

* Scheduler 작업에 인증을 추가하려면 작업을 만들거나 업데이트할 때 `authentication` JSON(JavaScript Notation) 자식 요소를 `request` 요소에 추가하세요. 

  `authentication` 개체에서 PUT, PATCH 또는 POST 요청을 통해 Scheduler 서비스에 전달된 암호는 절대로 응답에 반환되지 않습니다. 
  응답은 비밀 정보를 null로 설정하거나 인증된 엔터티를 나타내는 공용 토큰을 사용할 수 있습니다. 

* 스케줄러 작업에서 인증을 제거하려면 작업에서 명시적으로 PUT 또는 PATCH 요청을 실행하고 `authentication` 개체를 null로 설정합니다. 응답에는 인증 속성이 포함되지 않습니다.

## <a name="client-certificate"></a>클라이언트 인증서

### <a name="request-body---client-certificate"></a>요청 본문 - 클라이언트 인증서

`ClientCertificate` 모델을 사용하여 인증을 추가할 때 요청 본문에서 다음과 같은 추가 요소를 지정합니다.  

| 요소 | 필수 | Description |
|---------|----------|-------------|
| **인증**(부모 요소) | SSL/TLS 클라이언트 인증서를 사용하기 위한 인증 개체 |
| **type** | 예 | 인증 유형입니다. SSL/TLS 클라이언트 인증서의 경우 값은 `ClientCertificate`입니다. |
| **pfx** | 예 | PFX 파일의 Base64 인코딩 콘텐츠 |
| **password** | 예 | PFX 파일에 액세스하기 위한 암호 |
||| 

### <a name="response-body---client-certificate"></a>응답 본문 - 클라이언트 인증서 

인증 정보와 함께 요청을 보내면 응답에 다음과 같은 인증 요소가 포함됩니다.

| 요소 | Description | 
|---------|-------------| 
| **인증**(부모 요소) | SSL/TLS 클라이언트 인증서를 사용하기 위한 인증 개체 |
| **type** | 인증 유형입니다. SSL/TLS 클라이언트 인증서의 경우 값은 `ClientCertificate`입니다. |
| **certificateThumbprint** |인증서의 지문 |
| **certificateSubjectName** |인증서의 고유한 주체 이름 |
| **certificateExpiration** | 인증서 만료 날짜 |
||| 

### <a name="sample-rest-request---client-certificate"></a>샘플 REST 요청 - 클라이언트 인증서

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>샘플 REST 응답 - 클라이언트 인증서

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Basic

### <a name="request-body---basic"></a>요청 본문 - 기본

`Basic` 모델을 사용하여 인증을 추가할 때 요청 본문에서 다음과 같은 추가 요소를 지정합니다.

| 요소 | 필수 | Description |
|---------|----------|-------------|
| **인증**(부모 요소) | 기본 인증을 사용하기 위한 인증 개체 | 
| **type** | 예 | 인증 유형입니다. 기본 인증의 경우 이 값은 `Basic`입니다. | 
| **username** | 예 | 인증하기 위한 사용자 이름 | 
| **password** | 예 | 인증하기 위한 암호 |
|||| 

### <a name="response-body---basic"></a>응답 본문 - 기본

인증 정보와 함께 요청을 보내면 응답에 다음과 같은 인증 요소가 포함됩니다.

| 요소 | Description | 
|---------|-------------|
| **인증**(부모 요소) | 기본 인증을 사용하기 위한 인증 개체 |
| **type** | 인증 유형입니다. 기본 인증의 경우 이 값은 `Basic`입니다. |
| **username** | 인증된 사용자 이름 |
||| 

### <a name="sample-rest-request---basic"></a>샘플 REST 요청 - 기본

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>샘플 REST 응답 - 기본

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>요청 본문 - Active Directory OAuth 

`ActiveDirectoryOAuth` 모델을 사용하여 인증을 추가할 때 요청 본문에서 다음과 같은 추가 요소를 지정합니다.

| 요소 | 필수 | Description |
|---------|----------|-------------|
| **인증**(부모 요소) | 예 | ActiveDirectoryOAuth 인증을 사용하기 위한 인증 개체 |
| **type** | 예 | 인증 유형입니다. ActiveDirectoryOAuth 인증의 경우 이 값은 `ActiveDirectoryOAuth`입니다. |
| **테넌트** | 예 | Azure AD 테넌트의 테넌트 식별자입니다. Azure AD 테넌트의 테넌트 식별자를 찾으려면 Azure PowerShell에서 `Get-AzureAccount` 명령을 실행하세요. |
| **대상** | 예 | 이 값은 `https://management.core.windows.net/`으로 설정됩니다. | 
| **clientId** | 예 | Azure AD 애플리케이션의 클라이언트 ID | 
| **암호** | 예 | 토큰을 요청하는 클라이언트의 비밀 | 
|||| 

### <a name="response-body---active-directory-oauth"></a>응답 본문 - Active Directory OAuth

인증 정보와 함께 요청을 보내면 응답에 다음과 같은 인증 요소가 포함됩니다.

| 요소 | Description |
|---------|-------------|
| **인증**(부모 요소) | ActiveDirectoryOAuth 인증을 사용하기 위한 인증 개체 |
| **type** | 인증 유형입니다. ActiveDirectoryOAuth 인증의 경우 이 값은 `ActiveDirectoryOAuth`입니다. | 
| **테넌트** | Azure AD 테넌트의 테넌트 식별자 |
| **대상** | 이 값은 `https://management.core.windows.net/`으로 설정됩니다. |
| **clientId** | Azure AD 애플리케이션의 클라이언트 ID |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>샘플 REST 요청 - Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>샘플 REST 응답 - Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="next-steps"></a>다음 단계

* [Azure Scheduler 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
* [Azure Scheduler 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)
* [Azure Scheduler REST API 참조](/rest/api/scheduler)
* [Azure Scheduler PowerShell cmdlet 참조](scheduler-powershell-reference.md)