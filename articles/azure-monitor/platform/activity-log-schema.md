---
title: Azure 활동 로그 이벤트 스키마
description: 활동 로그로 내보내는 데이터의 이벤트 스키마에 대해 설명합니다.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 1/16/2019
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 93e74eb6aefbaeeddf7c4f15d62f4a9ee3d617d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777392"
---
# <a name="azure-activity-log-event-schema"></a>Azure 활동 로그 이벤트 스키마
**Azure 활동 로그**는 Azure에서 발생한 모든 구독 수준 이벤트에 대한 정보를 제공하는 로그입니다. 이 문서에서는 데이터 범주별 이벤트 스키마에 대해 설명합니다. 데이터의 스키마는 포털, PowerShell, CLI 또는 REST API를 통해 직접 데이터를 읽는지, 아니면 [로그 프로필을 사용하여 데이터를 저장소 또는 Event Hubs로 스트리밍](./../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile)하는지에 따라 다릅니다. 아래 예제는 포털, PowerShell, CLI 및 REST API를 통해 사용할 수 있는 스키마를 보여 줍니다. 이러한 속성과 [Azure 진단 로그 스키마](./diagnostic-logs-schema.md)의 매핑은 문서의 끝에 제공되어 있습니다.

## <a name="administrative"></a>관리
이 범주에는 Resource Manager를 통해 수행한 모든 만들기, 업데이트, 삭제 및 동작 작업의 레코드가 포함되어 있습니다. 이 범주에 표시되는 이벤트의 유형의 예로는 "가상 머신 만들기", "네트워크 보안 그룹 삭제" 등이 있습니다. 사용자나 애플리케이션이 Resource Manager를 사용하여 취하는 모든 동작은 특정 리소스 종류에 대한 작업으로 모델링됩니다. 작업 유형이 쓰기, 삭제 또는 동작이면 해당 작업의 시작 및 성공이나 실패 레코드가 모두 관리 범주에 기록됩니다. 관리 범주에는 구독의 역할 기반 액세스 제어 변경 내용도 포함됩니다.

### <a name="sample-event"></a>샘플 이벤트
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>속성 설명
| 요소 이름 | 설명 |
| --- | --- |
| 권한 부여 |이벤트의 RBAC 속성 Blob입니다. 일반적으로 "action", "role" 및 "scope" 속성이 포함됩니다. |
| caller |가용성을 기반으로 작업, UPN 클레임 또는 SPN 클레임을 수행한 사용자의 메일 주소입니다. |
| channels |해당 값은 “Admin”, “Operation” 중 하나입니다. |
| claims |Active Directory에서 사용자 또는 애플리케이션이 리소스 관리자를 통해 이 작업을 수행할 수 있도록 인증하는 데 사용하는 JWT 토큰입니다. |
| CorrelationId |일반적으로 문자열 형식의 GUID입니다. 동일한 uber 작업에 속하는 correlationId를 공유하는 이벤트입니다. |
| description |이벤트의 정적 텍스트 설명입니다. |
| eventDataId |이벤트의 고유 식별자입니다. |
| eventName | 관리 이벤트의 이름입니다. |
| category | 항상 "관리" |
| httpRequest |Http 요청을 설명하는 Blob입니다. 일반적으로 "clientRequestId", "clientIpAddress" 및 "method"(PUT 등의 HTTP 메서드) 포함. |
| level |이벤트의 수준입니다. 해당 값은 “Critical”, “Error”, “Warning” 및 “Informational” 중 하나입니다. |
| resourceGroupName |영향을 받는 리소스의 리소스 그룹 이름입니다. |
| resourceProviderName |영향을 받는 리소스의 리소스 공급자 이름입니다. |
| resourceType | 관리 이벤트에 영향을 받은 리소스의 형식입니다. |
| resourceId |영향을 받는 리소스의 리소스 ID입니다. |
| operationId |단일 작업에 해당하는 이벤트 간에 공유되는 GUID입니다. |
| operationName |작업의 이름입니다. |
| properties |이벤트에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍의 집합(즉, 사전)입니다. |
| status |작업의 상태를 설명하는 문자열. 몇 가지 일반적인 값은 Started, In Progress, Succeeded, Failed, Active, Resolved입니다. |
| subStatus |하지만 일반적으로 해당 REST 호출의 HTTP 상태 코드는 다음 일반적인 값과 같이 하위 상태를 설명하는 다른 문자열을 포함할 수도 있습니다. 확인(HTTP 상태 코드: 200), 만들어짐(HTTP 상태 코드: 201), 수락됨(HTTP 상태 코드: 202), 콘텐츠 없음(HTTP 상태 코드: 204), 잘못된 요청(HTTP 상태 코드: 400), 찾을 수 없음(HTTP 상태 코드: 404), 충돌(HTTP 상태 코드: 409), 내부 서버 오류(HTTP 상태 코드: 500), 서비스를 사용할 수 없음(HTTP 상태 코드: 503), 게이트웨이 시간 초과(HTTP 상태 코드: 504). |
| eventTimestamp |이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| submissionTimestamp |이벤트를 쿼리할 수 있게 되는 타임스탬프입니다. |
| subscriptionId |Azure 구독 ID입니다. |

## <a name="service-health"></a>서비스 상태
이 범주에는 Azure에서 발생한 모든 서비스 상태 관련 인시던트의 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예로는 "미국 동부의 SQL Azure가 가동 중지 상태입니다." 등이 있습니다. 서비스 상태 이벤트는 작업 필요, 복구 지원, 인시던트, 유지 관리, 정보 또는 보안의 5가지 중 하나이며 구독에 이벤트의 영향을 받는 리소스가 있는 경우에만 표시됩니다.

### <a name="sample-event"></a>샘플 이벤트
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
이 속성의 값에 대한 설명서를 보려면 [서비스 상태 알림](./../../azure-monitor/platform/service-notifications.md) 문서를 참조하세요.

## <a name="resource-health"></a>리소스 상태
이 범주에는 Azure 리소스에 발생한 모든 리소스 상태 이벤트의 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예는 “가상 머신 상태가 사용 불가능으로 변경됨”입니다. 리소스 상태 이벤트는 상태 중 하나를 나타낼 수 있으며 값은 사용 가능, 사용 불가능, 저하됨, 알 수 없음 중 하나입니다. 또한 리소스 상태 이벤트는 플랫폼 시작 또는 사용자 시작으로 분류될 수 있습니다.

### <a name="sample-event"></a>샘플 이벤트

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription Id>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>속성 설명
| 요소 이름 | 설명 |
| --- | --- |
| channels | 항상 "Admin, Operation"입니다. |
| CorrelationId | 문자열 형식의 GUID입니다. |
| description |경고 이벤트의 정적 텍스트 설명입니다. |
| eventDataId |경고 이벤트의 고유 식별자입니다. |
| category | 항상 “ResourceHealth” |
| eventTimestamp |이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| level |이벤트의 수준입니다. 해당 값은 “Critical”, “Error”, “Warning”, “Informational” 및 “Verbose” 중 하나입니다. |
| operationId |단일 작업에 해당하는 이벤트 간에 공유되는 GUID입니다. |
| operationName |작업의 이름입니다. |
| resourceGroupName |리소스를 포함하는 리소스 그룹의 이름 |
| resourceProviderName |항상 “Microsoft.Resourcehealth/healthevent/action”. |
| resourceType | 리소스 상태 이벤트에 영향을 받은 리소스 유형입니다. |
| resourceId | 영향을 받는 리소스의 리소스 ID 이름입니다. |
| status |상태 이벤트의 상태를 설명하는 문자열입니다. 값은 활성, 해결됨, 진행 중, 업데이트됨일 수 있습니다. |
| subStatus | 경고의 경우 대개 null입니다. |
| submissionTimestamp |이벤트를 쿼리할 수 있게 되는 타임스탬프입니다. |
| subscriptionId |Azure 구독 ID입니다. |
| properties |이벤트에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍의 집합(즉, 사전)입니다.|
| properties.title | 리소스의 상태를 설명하는 사용자에게 친숙한 문자열입니다. |
| properties.details | 이벤트에 대한 추가 세부 정보를 설명하는 사용자에게 친숙한 문자열입니다. |
| properties.currentHealthStatus | 리소스의 현재 상태. 해당 값은 “사용 가능”, “사용 불가능”, “저하됨”, “알 수 없음” 중 하나입니다. |
| properties.previousHealthStatus | 리소스의 이전 상태. 해당 값은 “사용 가능”, “사용 불가능”, “저하됨”, “알 수 없음” 중 하나입니다. |
| properties.type | 리소스 상태 이벤트의 유형에 대한 설명입니다. |
| properties.cause | 리소스 상태 이벤트의 원인에 대한 설명입니다. "UserInitiated" 및 "PlatformInitiated" 중 하나입니다. |


## <a name="alert"></a>경고
이 범주에는 모든 Azure 경고 활성화의 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예로는 "지난 5분 동안 myVM의 CPU 사용률이 80%를 초과했습니다." 등이 있습니다. 다수의 Azure 시스템에서 경고 개념이 사용됩니다. 일종의 규칙을 정의하여 조건이 해당 규칙과 일치하면 알림을 수신할 수 있습니다. 지원되는 Azure 경고 유형이 '활성화'되거나 알림 생성을 위한 조건이 충족될 때마다 활성화 레코드도 이 활동 로그 범주로 푸시됩니다.

### <a name="sample-event"></a>샘플 이벤트

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>속성 설명
| 요소 이름 | 설명 |
| --- | --- |
| caller | Always Microsoft.Insights/alertRules |
| channels | 항상 "Admin, Operation"입니다. |
| claims | 경고 엔진의 SPN(서비스 사용자 이름) 또는 리소스 종류가 포함된 JSON Blob입니다. |
| CorrelationId | 문자열 형식의 GUID입니다. |
| description |경고 이벤트의 정적 텍스트 설명입니다. |
| eventDataId |경고 이벤트의 고유 식별자입니다. |
| category | 항상 "경고" |
| level |이벤트의 수준입니다. 해당 값은 “Critical”, “Error”, “Warning” 및 “Informational” 중 하나입니다. |
| resourceGroupName |메트릭 경고의 경우 영향을 받는 리소스의 리소스 그룹 이름입니다. 다른 경고 유형의 경우 경고 자체가 포함된 리소스 그룹의 이름입니다. |
| resourceProviderName |메트릭 경고의 경우 영향을 받는 리소스의 리소스 공급자 이름입니다. 다른 경고 유형의 경우 경고 자체에 대한 리소스 공급자 이름입니다. |
| resourceId | 메트릭 경고의 경우 영향을 받는 리소스의 리소스 ID 이름입니다. 다른 경고 유형의 경우 경고 리소스 자체의 리소스 ID입니다. |
| operationId |단일 작업에 해당하는 이벤트 간에 공유되는 GUID입니다. |
| operationName |작업의 이름입니다. |
| properties |이벤트에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍의 집합(즉, 사전)입니다. |
| status |작업의 상태를 설명하는 문자열. 몇 가지 일반적인 값은 Started, In Progress, Succeeded, Failed, Active, Resolved입니다. |
| subStatus | 경고의 경우 대개 null입니다. |
| eventTimestamp |이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| submissionTimestamp |이벤트를 쿼리할 수 있게 되는 타임스탬프입니다. |
| subscriptionId |Azure 구독 ID입니다. |

### <a name="properties-field-per-alert-type"></a>경고 유형별 속성 필드
속성 필드에는 경고 이벤트의 원본에 따라 다른 값이 포함됩니다. 일반적으로 사용되는 두 경고 이벤트 공급자는 활동 로그 경고와 메트릭 경고입니다.

#### <a name="properties-for-activity-log-alerts"></a>활동 로그 경고의 속성
| 요소 이름 | 설명 |
| --- | --- |
| properties.subscriptionId | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 구독 ID입니다. |
| properties.eventDataId | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 이벤트 데이터 ID입니다. |
| properties.resourceGroup | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 리소스 그룹입니다. |
| properties.resourceId | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 리소스 ID입니다. |
| properties.eventTimestamp | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 이벤트 타임스탬프입니다. |
| properties.operationName | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 작업 이름입니다. |
| properties.status | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 상태입니다.|

#### <a name="properties-for-metric-alerts"></a>메트릭 경고의 속성
| 요소 이름 | 설명 |
| --- | --- |
| properties.RuleUri | 메트릭 경고 규칙 자체의 리소스 ID입니다. |
| properties.RuleName | 메트릭 경고 규칙의 이름입니다. |
| properties.RuleDescription | 경고 규칙에 정의된 메트릭 경고 규칙의 설명입니다. |
| properties.Threshold | 메트릭 경고 규칙의 평가에 사용되는 임계값입니다. |
| properties.WindowSizeInMinutes | 메트릭 경고 규칙의 평가에 사용되는 창 크기입니다. |
| properties.Aggregation | 메트릭 경고 규칙에 정의된 집계 유형입니다. |
| properties.Operator | 메트릭 경고 규칙의 평가에 사용되는 조건부 연산자입니다. |
| properties.MetricName | 메트릭 경고 규칙의 평가에 사용되는 메트릭의 이름입니다. |
| properties.MetricUnit | 메트릭 경고 규칙의 평가에 사용되는 메트릭의 메트릭 단위입니다. |

## <a name="autoscale"></a>자동 크기 조정
이 범주에는 구독에서 정의한 자동 크기 조정 설정을 기준으로 하는 자동 크기 조정 엔진 작업 관련 이벤트의 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예로는 "자동 크기 조정 강화 동작이 실패했습니다." 등이 있습니다 자동 크기 조정을 사용하면 자동 크기 조정 설정을 사용하여 시간 및/또는 로드(메트릭) 데이터를 기준으로 지원되는 리소스 종류의 인스턴스 수를 자동으로 규모 확장하거나 규모 감축할 수 있습니다. 강화 또는 규모 축소 조건이 충족되면 시작 이벤트와 성공 또는 실패 이벤트가 이 범주에 기록됩니다.

### <a name="sample-event"></a>샘플 이벤트
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>속성 설명
| 요소 이름 | 설명 |
| --- | --- |
| caller | 항상 Microsoft.Insights/autoscaleSettings입니다. |
| channels | 항상 "Admin, Operation"입니다. |
| claims | 자동 크기 조정 엔진의 SPN(서비스 사용자 이름) 또는 리소스 종류가 포함된 JSON Blob입니다. |
| CorrelationId | 문자열 형식의 GUID입니다. |
| description |자동 크기 조정 이벤트의 정적 텍스트 설명입니다. |
| eventDataId |자동 크기 조정 이벤트의 고유 식별자입니다. |
| level |이벤트의 수준입니다. 해당 값은 “Critical”, “Error”, “Warning” 및 “Informational” 중 하나입니다. |
| resourceGroupName |자동 크기 조정 설정의 리소스 그룹 이름입니다. |
| resourceProviderName |자동 크기 조정 설정의 리소스 공급자 이름입니다. |
| resourceId |자동 크기 조정 설정의 리소스 ID입니다. |
| operationId |단일 작업에 해당하는 이벤트 간에 공유되는 GUID입니다. |
| operationName |작업의 이름입니다. |
| properties |이벤트에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍의 집합(즉, 사전)입니다. |
| properties.Description | 자동 크기 조정 엔진이 수행 중이었던 작업의 자세한 설명입니다. |
| properties.ResourceName | 영향을 받는 리소스(크기 조정 동작을 수행 중이었던 리소스)의 리소스 ID입니다. |
| properties.OldInstancesCount | 자동 크기 조정 동작이 적용되기 전의 인스턴스 수입니다. |
| properties.NewInstancesCount | 자동 크기 조정 동작이 적용된 후의 인스턴스 수입니다. |
| properties.LastScaleActionTime | 자동 크기 조정이 수행된 시간의 타임스탬프입니다. |
| status |작업의 상태를 설명하는 문자열. 몇 가지 일반적인 값은 Started, In Progress, Succeeded, Failed, Active, Resolved입니다. |
| subStatus | 자동 크기 조정의 경우 대개 null입니다. |
| eventTimestamp |이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| submissionTimestamp |이벤트를 쿼리할 수 있게 되는 타임스탬프입니다. |
| subscriptionId |Azure 구독 ID입니다. |

## <a name="security"></a>보안
이 범주에는 Azure Security Center에서 경고가 생성한 모든 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예로는 "의심스러운 이중 확장 파일이 실행되었습니다." 등이 있습니다.

### <a name="sample-event"></a>샘플 이벤트
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>속성 설명
| 요소 이름 | 설명 |
| --- | --- |
| channels | 항상 “Operation”입니다. |
| CorrelationId | 문자열 형식의 GUID입니다. |
| description |보안 이벤트에 대한 정적 텍스트 설명입니다. |
| eventDataId |보안 이벤트의 고유 식별자입니다. |
| eventName |보안 이벤트의 이름입니다. |
| category | 항상 "보안" |
| id |보안 이벤트의 고유 리소스 식별자입니다. |
| level |이벤트의 수준입니다. 해당 값은 “Critical”, “Error”, “Warning” 또는 “Informational” 중 하나입니다. |
| resourceGroupName |리소스의 리소스 그룹 이름입니다. |
| resourceProviderName |Azure Security Center의 리소스 공급자 이름입니다. 항상 "Microsoft.Security"입니다. |
| resourceType |보안 이벤트를 생성한 리소스 유형(예: "Microsoft.Security/locations/alerts")입니다. |
| resourceId |보안 경고의 리소스 ID입니다. |
| operationId |단일 작업에 해당하는 이벤트 간에 공유되는 GUID입니다. |
| operationName |작업의 이름입니다. |
| properties |이벤트에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍의 집합(즉, 사전)입니다. 이러한 속성은 보안 경고 유형에 따라 다릅니다. Security Center에서 제공되는 경고 유형에 대한 설명은 [이 페이지](../../security-center/security-center-alerts-type.md)를 참조하세요. |
| properties.Severity |심각도 수준입니다. 가능한 값은 "High", "Medium" 또는 "Low"입니다. |
| status |작업의 상태를 설명하는 문자열. 몇 가지 일반적인 값은 Started, In Progress, Succeeded, Failed, Active, Resolved입니다. |
| subStatus | 보안 이벤트의 경우 일반적으로 null입니다. |
| eventTimestamp |이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| submissionTimestamp |이벤트를 쿼리할 수 있게 되는 타임스탬프입니다. |
| subscriptionId |Azure 구독 ID입니다. |

## <a name="recommendation"></a>권장 사항
이 범주에는 서비스에 대해 생성되는 새 권장 사항 레코드가 포함됩니다. 권장 사항의 예로 "내결함성을 향상하려면 가용성 집합을 사용하세요"를 들 수 있습니다. 생성될 수 있는 Recommendation 이벤트의 네 가지 유형은 다음과 같습니다. 고가용성, 성능, 보안 및 비용 최적화가 생성될 수 있습니다. 

### <a name="sample-event"></a>샘플 이벤트
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>속성 설명
| 요소 이름 | 설명 |
| --- | --- |
| channels | 항상 “Operation”입니다. |
| CorrelationId | 문자열 형식의 GUID입니다. |
| description |권장 사항 이벤트에 대한 정적 텍스트 설명 |
| eventDataId | 권장 사항 이벤트의 고유 식별자. |
| category | 항상 "권장 사항" |
| id |권장 사항 이벤트의 고유 리소스 식별자. |
| level |이벤트의 수준입니다. 해당 값은 “Critical”, “Error”, “Warning” 또는 “Informational” 중 하나입니다. |
| operationName |작업의 이름입니다.  항상 "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |리소스의 리소스 그룹 이름입니다. |
| resourceProviderName |이 권장 사항이 적용되는 리소스의 리소스 공급자 이름(예: "MICROSOFT.COMPUTE") |
| resourceType |이 권장 사항이 적용되는 리소스의 리소스 종류 이름(예: "MICROSOFT.COMPUTE/virtualmachines") |
| resourceId |권장 사항이 적용되는 리소스의 리소스 ID입니다. |
| status | 항상 "활성" |
| submissionTimestamp |이벤트를 쿼리할 수 있게 되는 타임스탬프입니다. |
| subscriptionId |Azure 구독 ID입니다. |
| properties |권장 사항에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍 집합(즉, 사전)입니다.|
| properties.recommendationSchemaVersion| 활동 로그 항목에 게시된 권장 사항 속성의 스키마 버전 |
| properties.recommendationCategory | 권장 사항의 범주. 가능한 값은 "고가용성", "성능", "보안" 및 "비용" |
| properties.recommendationImpact| 권장 사항의 영향. 가능한 값은 "높음", "보통" 또는 "낮음" |
| properties.recommendationRisk| 권장 사항의 위험. 가능한 값은 "오류", "경고", "없음" |

## <a name="policy"></a>정책

이 범주에는 [Azure Policy](../../governance/policy/overview.md)에서 수행하는 모든 적용 작업의 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예에는 _감사_ 및 _거부_가 포함됩니다. Policy에서 수행하는 모든 작업은 리소스에 대한 작업으로 모델링됩니다.

### <a name="sample-policy-event"></a>샘플 Policy 이벤트

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Policy 이벤트 속성 설명

| 요소 이름 | 설명 |
| --- | --- |
| 권한 부여 | 이벤트의 RBAC 속성 배열입니다. 새 리소스의 경우 평가를 트리거한 요청의 작업 및 범위입니다. 기존 리소스의 경우 작업은 "Microsoft.Resources/checkPolicyCompliance/read"입니다. |
| caller | 새 리소스의 경우 배포를 시작한 ID입니다. 기존 리소스의 경우 Microsoft Azure Policy Insights RP의 GUID입니다. |
| channels | Policy 이벤트는 "Operation" 채널만 사용합니다. |
| claims | Active Directory에서 사용자 또는 애플리케이션이 리소스 관리자를 통해 이 작업을 수행할 수 있도록 인증하는 데 사용하는 JWT 토큰입니다. |
| CorrelationId | 일반적으로 문자열 형식의 GUID입니다. 동일한 uber 작업에 속하는 correlationId를 공유하는 이벤트입니다. |
| description | 이 필드는 Policy 이벤트에 대해 비어 있습니다. |
| eventDataId | 이벤트의 고유 식별자입니다. |
| eventName | "BeginRequest" 또는 "EndRequest"입니다. "BeginRequest"는 deployIfNotExists 적용이 템플릿 배포를 시작할 때, 지연된 auditIfNotExists 및 deployIfNotExists 평가에 사용됩니다. 다른 모든 작업은 "EndRequest"를 반환합니다. |
| category | 활동 로그 이벤트를 "Policy"에 속하는 것으로 선언합니다. |
| eventTimestamp | 이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| id | 특정 리소스에서 이벤트의 고유 식별자입니다. |
| level | 이벤트의 수준입니다. 감사는 "Warning"을 사용하고 거부는 "Error"를 사용합니다. AuditIfNotExists 또는 deployIfNotExists 오류는 심각도에 따라 "Warning" 또는 "Error"를 생성할 수 있습니다. 다른 모든 Policy 이벤트는 "Informational"을 사용합니다. |
| operationId | 단일 작업에 해당하는 이벤트 간에 공유되는 GUID입니다. |
| operationName | 작업의 이름으로, Policy 적용에 직접적으로 상관 관계가 있습니다. |
| resourceGroupName | 평가된 리소스의 리소스 그룹 이름입니다. |
| resourceProviderName | 평가된 리소스의 리소스 공급자 이름입니다. |
| resourceType | 새 리소스의 경우 평가되는 형식입니다. 기존 리소스의 경우 "Microsoft.Resources/checkPolicyCompliance"를 반환합니다. |
| resourceId | 평가된 리소스의 리소스 ID입니다. |
| status | Policy 평가 결과의 상태를 설명하는 문자열입니다. 대부분의 Policy 평가는 "Succeeded"를 반환하지만 거부 적용은 "Failed"를 반환합니다. auditIfNotExists 또는 deployIfNotExists의 오류도 "Failed"를 반환합니다. |
| subStatus | Policy 이벤트에 대한 필드가 비어 있습니다. |
| submissionTimestamp | 이벤트를 쿼리할 수 있게 되는 타임스탬프입니다. |
| subscriptionId | Azure 구독 ID입니다. |
| properties.isComplianceCheck | 새 리소스가 배포되거나 기존 리소스의 Resource Manager 속성이 업데이트되면 "False"를 반환합니다. 다른 모든 [평가 트리거](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)는 "True"를 반환합니다. |
| properties.resourceLocation | 평가 중인 리소스의 Azure 지역입니다. |
| properties.ancestors | 직계 부모에서 가장 먼 부모 순서로 정렬한 쉼표로 구분된 부모 관리 그룹 목록입니다. |
| properties.policies | 이 Policy 평가 결과에 영향을 미치는 정책 정의, 할당, 적용 및 매개 변수에 대한 세부 정보를 포함합니다. |
| relatedEvents | 이 필드는 Policy 이벤트에 대해 비어 있습니다. |

## <a name="mapping-to-diagnostic-logs-schema"></a>진단 로그 스키마에 매핑

Azure 활동 로그를 저장소 계정 또는 Event Hubs 네임스페이스로 스트리밍하는 경우, 데이터는 [Azure 진단 로그 스키마](./diagnostic-logs-schema.md)를 따릅니다. 다음은 위의 스키마에서 진단 로그 스키마로의 속성 매핑입니다.

| 진단 로그 스키마 속성 | 활동 로그 REST API 스키마 속성 | 메모 |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | subscriptionId, resourceType, resourceGroupName은 모두 resourceId에서 유추됩니다. |
| operationName | operationName.value |  |
| category | 작업 이름의 일부 | 작업 유형 분류 - “쓰기”/“삭제”/“작업” |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | description |  |
| durationMS | N/A | 항상 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| CorrelationId | CorrelationId |  |
| ID | 클레임 및 권한 부여 속성 |  |
| Level | Level |  |
| location | N/A | 이벤트가 처리된 위치입니다. *‘리소스 위치가 아니라 이벤트가 처리된 위치입니다. 이 속성은 향후 업데이트에서 제거됩니다.’* |
| properties | properties.eventProperties |  |
| properties.eventCategory | category | properties.eventCategory가 없을 경우, category는 “Administrative”입니다. |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |


## <a name="next-steps"></a>다음 단계
* [활동 로그(이전의 감사 로그)에 대해 자세히 알아보기](../../azure-monitor/platform/activity-logs-overview.md)
* [Azure 활동 로그를 Event Hubs로 스트림](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)

