---
title: Azure Security Center를 사용 하 여 IoT 미리 보기에 대 한 데이터에 액세스 | Microsoft Docs
description: IoT 용 Azure Security Center를 사용 하는 경우 보안 경고 및 권장 사항 데이터를 액세스 하는 방법에 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 0788a62de701cb85a65c7053006e28cf4fffe5cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357206"
---
# <a name="access-your-security-data"></a>보안 데이터에 액세스 

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IoT 용 azure Security Center (ASC) 저장 보안 경고, 권장 사항 및 보안 원시 데이터 (저장 하려는) 하는 경우 Log Analytics 작업 영역에서 합니다.

## <a name="log-analytics"></a>Log Analytics

Log Analytics 작업 영역을 사용 하는 구성:

1. IoT Hub를 엽니다.
1. 클릭 **보안**
2. 클릭 **설정을**, Log Analytics 작업 영역 구성을 변경 합니다.

에 액세스 하려면 Log Analytics 작업 영역 구성 후:

1. IoT 용 ASC에서 경고 또는 권장 사항을 선택 합니다. 
2. 클릭 **좀**, 클릭 **여기를 클릭 하 고 DeviceId 열을 확인이 경고는 장치를 확인 하려면**합니다.

Log Analytics에서 데이터를 쿼리에 대 한 자세한 내용은 참조 하세요. [Log Analytics에서 쿼리를 사용 하 여 시작](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)합니다.

## <a name="security-alerts"></a>보안 경고

보안 경고에 저장 됩니다 _AzureSecurityOfThings.SecurityAlert_ IoT 솔루션에 대 한 ASC 구성 Log Analytics 작업 영역에는 테이블입니다.

보안 경고를 탐색 하는 데 유용한 쿼리 수가 시작 제공 되었습니다.

### <a name="sample-records"></a>샘플 레코드

몇 가지 임의 레코드를 선택 합니다.

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | 설명                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 성공한 무차별 대입 공격           | 장치의 Brute force 공격 성공 했습니다.        |    {"전체 원본 주소": "[\"10.165.12.18:\"]", "사용자 이름": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 로컬 로그인      | 장치에 로컬 로그인이 성공 하면 검색 되었습니다.     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 로컬 로그인 시도가 실패 했습니다.  | 장치에 시도 하는 로컬 로그인에 실패 했습니다. |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "user Name": "공격자 가", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>장치 요약

지난주 IoT Hub, 장치, 경고 심각도, 경고 유형을 검색 하는 별도 보안 경고 수를 선택 합니다.

```
// Select number of distinct security alerts detected last week by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | 카운트 |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 성공한 무차별 대입 공격           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 장치에서 로컬 로그인 시도가 실패 했습니다.  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 로컬 로그인      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 암호화 동전 Miner                     | 4   |

### <a name="iot-hub-summary"></a>IoT hub 요약

지난 주에 IoT Hub, 경고 심각도, 경고 유형의 경고 있는 고유 장치의 수를 선택 합니다.

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 높음          | 성공한 무차별 대입 공격           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 중간        | 장치에서 로컬 로그인 시도가 실패 했습니다.  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 높음          | 장치에서 로컬 로그인      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 중간        | 암호화 동전 Miner                     | 1          |

## <a name="security-recommendations"></a>보안 권장 사항

보안 권장 사항에 저장 됩니다 _AzureSecurityOfThings.SecurityRecommendation_ IoT 솔루션에 대 한 ASC 구성 Log Analytics 작업 영역에는 테이블입니다.

다양 한 보안 권장 사항 탐색을 시작 하는 데 유용한 쿼리를 제공 했습니다.

### <a name="sample-records"></a>샘플 레코드

몇 가지 임의 레코드를 선택 합니다.

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | IoTHubId | deviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | 설명 | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간 | Active | 입력된 체인에서 관대 한 방화벽 규칙을 찾을 수 있습니다. | 광범위한 IP 주소 또는 포트에 대한 허용 패턴을 포함하는 방화벽 규칙을 찾았습니다. | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간 | Active | 입력된 체인에서 관대 한 방화벽 규칙을 찾을 수 있습니다. | 광범위한 IP 주소 또는 포트에 대한 허용 패턴을 포함하는 방화벽 규칙을 찾았습니다. | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>장치 요약

IoT Hub, 장치, 권장 사항 심각도 및 형식에 따라 고유한 활성 보안 권장 사항 수를 선택 합니다.

```
// Select number of distinct active security recommendations by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | RecommendationSeverity | 카운트 |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 4   |


## <a name="next-steps"></a>다음 단계

- ASC IoT에 대 한 읽기 [개요](overview.md)
- IoT에 대 한 ASC 알아봅니다 [아키텍처](architecture.md)
- 이해 및 탐색 [ASC IoT 경고에 대 한](concept-security-alerts.md)
- 이해 및 탐색 [ASC IoT 권장 사항](concept-recommendations.md)