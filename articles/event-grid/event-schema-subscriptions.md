---
title: Azure Event Grid 구독 이벤트 스키마
description: Azure Event Grid를 사용하여 구독 이벤트에 제공되는 속성을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 4994063dfc3bce88489f70969c06bf36b591f907
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561679"
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Azure Event Grid 구독에 대한 이벤트 스키마

이 문서에서는 Azure 구독 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

Azure 구독 및 리소스 그룹은 동일한 이벤트 유형을 내보냅니다. 이벤트 유형은 리소스 변경 또는 작업과 관련이 있습니다. 주요 차이는 리소스 그룹이 리소스 그룹 내 리소스에 대한 이벤트를 내보내고 Azure 구독은 구독 전체에서 리소스에 대한 이벤트를 내보낸다는 것입니다.

`management.azure.com`로 전송되는 PUT, PATCH, POST 및 DELETE 작업에 대한 리소스 이벤트가 만들어집니다. GET 작업은 이벤트를 만들지 않습니다. 데이터 평면으로 전송된 작업(예: `myaccount.blob.core.windows.net`)은 이벤트를 만들지 않습니다. 작업 이벤트는 리소스에 대한 키 나열 같은 작업의 이벤트 데이터를 제공합니다.

Azure 구독에 대한 이벤트를 구독하면 엔드포인트는 해당 구독에 대한 모든 이벤트를 받습니다. 이 이벤트에는 가상 머신 업데이트 같이 확인하려는 이벤트뿐만 아니라 배포 기록에 새 항목 작성 같이 중요하지 않을 수 있는 이벤트도 포함될 수 있습니다. 엔드포인트에서 모든 이벤트를 수신하고, 처리하려는 이벤트를 처리하는 코드를 작성할 수 있습니다. 또는 이벤트 구독을 만들 때 필터를 설정할 수 있습니다.

프로그래밍 방식으로 이벤트를 처리하려면 `operationName` 값을 검토해 이벤트를 정렬할 수 있습니다. 예를 들어 이벤트 엔드포인트는 `Microsoft.Compute/virtualMachines/write` 또는 `Microsoft.Storage/storageAccounts/write`와 동일한 작업에 대한 이벤트만 처리할 수 있습니다.

이벤트 주체는 작업의 대상이 되는 리소스의 리소스 ID입니다. 리소스에 대한 이벤트를 필터링하려면 이벤트 구독을 만들 때 해당 리소스 ID를 제공합니다. 리소스 종류별로 필터링하려면 `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`과 같은 형식으로 값을 사용합니다.

샘플 스크립트 및 자습서 목록은 [Azure 구독 이벤트 원본](event-sources.md#azure-subscriptions)을 참조하세요.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

VM을 만들거나 저장소 계정을 삭제할 때와 같이 Azure 구독은 Azure Resource Manager에서 관리 이벤트를 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | 리소스에서 작업이 취소되면 발생합니다. |
| Microsoft.Resources.ResourceActionFailure | 리소스에서 작업이 실패하면 발생합니다. |
| Microsoft.Resources.ResourceActionSuccess | 리소스에서 작업이 성공하면 발생합니다. |
| Microsoft.Resources.ResourceDeleteCancel | 삭제 작업이 취소되면 발생합니다. 이 이벤트는 템플릿 배포가 취소될 때 발생합니다. |
| Microsoft.Resources.ResourceDeleteFailure | 삭제 작업이 실패하면 발생합니다. |
| Microsoft.Resources.ResourceDeleteSuccess | 삭제 작업이 성공하면 발생합니다. |
| Microsoft.Resources.ResourceWriteCancel | 만들기 또는 업데이트 작업이 취소되면 발생합니다. |
| Microsoft.Resources.ResourceWriteFailure | 만들기 또는 업데이트 작업이 실패하면 발생합니다. |
| Microsoft.Resources.ResourceWriteSuccess | 만들기 또는 업데이트 작업이 성공하면 발생합니다. |

## <a name="example-event"></a>예제 이벤트

다음 예제는 **ResourceWriteSuccess** 이벤트의 스키마를 보여줍니다. 동일한 스키마가 `eventType`에 대한 다른 값을 사용하여 **ResourceWriteFailure** 및 **ResourceWriteCancel** 이벤트에 사용됩니다.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

다음 예제는 **ResourceDeleteSuccess** 이벤트의 스키마를 보여줍니다. 동일한 스키마가 `eventType`에 대한 다른 값을 사용하여 **ResourceDeleteFailure** 및 **ResourceDeleteCancel** 이벤트에 사용됩니다.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

다음 예제는 **ResourceActionSuccess** 이벤트의 스키마를 보여줍니다. 동일한 스키마가 `eventType`에 대한 다른 값을 사용하여 **ResourceActionFailure** 및 **ResourceActionCancel** 이벤트에 사용됩니다.

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}" 
}]
```

## <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 자산 | Type | 설명 |
| -------- | ---- | ----------- |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| 제목 | 문자열 | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | 구독 이벤트 데이터입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 자산 | Type | 설명 |
| -------- | ---- | ----------- |
| 권한 부여 | object | 작업에 대해 요청된 권한입니다. |
| claims | object | 클레임의 속성입니다. 자세한 내용은 [JWT 사양](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)을 참조하세요. |
| CorrelationId | 문자열 | 문제 해결을 위한 작업 ID입니다. |
| httpRequest | object | 작업의 세부 정보입니다. 이 개체는 기존 리소스를 업데이트하거나 리소스를 삭제하는 경우에만 포함됩니다. |
| resourceProvider | 문자열 | 작업에 대한 리소스 공급자입니다. |
| resourceUri | 문자열 | 작업에서 리소스의 URI입니다. |
| operationName | 문자열 | 수행된 작업입니다. |
| status | 문자열 | 작업의 상태. |
| subscriptionId | 문자열 | 리소스의 구독 ID입니다. |
| tenantId | 문자열 | 리소스의 테넌트 ID입니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
