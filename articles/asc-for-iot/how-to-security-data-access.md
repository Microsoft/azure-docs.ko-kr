---
title: IoT에 대한 Azure Security Center를 사용 하 여 데이터 액세스 | Microsoft Docs
description: IoT에 Azure Security Center를 사용 하는 경우 보안 경고 및 권장 사항 데이터에 액세스 하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597188"
---
# <a name="access-your-security-data"></a>보안 데이터 액세스 

IoT에 대한 Azure Security Center에는 보안 경고, 권장 사항 및 원시 보안 데이터 (저장 하도록 선택한 경우)가 Log Analytics 작업 영역에 저장 됩니다.

## <a name="log-analytics"></a>Log Analytics

사용 되는 Log Analytics 작업 영역을 구성 하려면:

1. IoT Hub를 엽니다.
1. **보안** 섹션에서 **개요** 블레이드를 클릭 합니다.
2. **설정**을 클릭 하 고 Log Analytics 작업 영역 구성을 변경 합니다.

구성 후 Log Analytics 작업 영역에서 경고 및 권장 사항에 액세스 하려면 다음을 수행 합니다.

1. IoT에 대한 Azure Security Center에서 경고 또는 권장 사항을 선택 합니다. 
2. **추가 조사**를 클릭 한 다음 **이 경고가 있는 장치를 보려면 여기를 클릭 하 여 DeviceId 열을 확인**합니다.

Log Analytics에서 데이터를 쿼리 하는 방법에 대한 자세한 내용은 [Log Analytics에서 쿼리 시작](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)을 참조 하세요.

## <a name="security-alerts"></a>보안 경고

보안 경고는 IoT 솔루션 Azure Security Center에 대해 구성 된 Log Analytics 작업 영역의 _AzureSecurityOfThings 경고_ 테이블에 저장 됩니다.

보안 경고 탐색을 시작 하는 데 도움이 되는 다양 한 유용한 쿼리를 제공 하 고 있습니다.

### <a name="sample-records"></a>샘플 레코드

몇 개의 임의 레코드를 선택 합니다.

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceID      | AlertSeverity | DisplayName                           | 설명                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 무차별 암호 대입 공격 성공           | 장치에서 무차별 암호 대입 공격 성공        |    {"전체 원본 주소": "[\"10.165.12.18:\"]", "사용자 이름": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 로컬 로그인 성공      | 장치에 대한 로컬 로그인에 성공 했습니다.     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "사용자 이름": "공격자", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 로컬 로그인 시도 실패  | 장치에 대한 로컬 로그인 시도가 실패 했습니다. |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "사용자 이름": "공격자", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>장치 요약

지난 주에 검색 된 개별 보안 경고의 수를 IoT Hub, 장치, 경고 심각도, 경고 유형별로 그룹화 하 여 가져옵니다.

```
// Get the number of distinct security alerts detected in the last week, grouped by 
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

| IoTHubId                                                                                                       | DeviceID      | AlertSeverity | DisplayName                           | 개수 |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 무차별 암호 대입 공격 성공           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 보통        | 장치에서 로컬 로그인 시도 실패  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 로컬 로그인 성공      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 보통        | 암호화 동전 마이너                     | 4   |

### <a name="iot-hub-summary"></a>IoT hub 요약

지난 주에 경고가 발생 한 여러 고유 장치를 선택 합니다. IoT Hub, 경고 심각도, 경고 유형

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 높음          | 무차별 암호 대입 공격 성공           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 보통        | 장치에서 로컬 로그인 시도 실패  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 높음          | 장치에서 로컬 로그인 성공      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 보통        | 암호화 동전 마이너                     | 1          |

## <a name="security-recommendations"></a>보안 권장 사항

보안 권장 사항은 IoT 솔루션 Azure Security Center에 대해 구성 된 Log Analytics 작업 영역의 _AzureSecurityOfThings 권장 사항_ 테이블에 저장 됩니다.

보안 권장 사항 탐색을 시작 하는 데 도움이 되는 다양 한 유용한 쿼리를 제공 하 고 있습니다.

### <a name="sample-records"></a>샘플 레코드

몇 개의 임의 레코드를 선택 합니다.

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
    
| TimeGenerated | IoTHubId | DeviceID | RecommendationSeverity | RecommendationState | RecommendationDisplayName | 설명 | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 보통 | 활성 | 입력 체인에서 허용 되는 방화벽 규칙을 찾았습니다. | 광범위한 IP 주소 또는 포트에 대한 허용 패턴을 포함하는 방화벽 규칙을 찾았습니다. | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 보통 | 활성 | 입력 체인에서 허용 되는 방화벽 규칙을 찾았습니다. | 광범위한 IP 주소 또는 포트에 대한 허용 패턴을 포함하는 방화벽 규칙을 찾았습니다. | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>장치 요약

IoT Hub, 장치, 권장 사항 심각도 및 유형별로 그룹화 된 고유한 활성 보안 권장 사항의 수를 가져옵니다.

```
// Get the number of distinct active security recommendations, grouped by by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceID      | RecommendationSeverity | 개수 |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 보통        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 보통        | 4   |


## <a name="next-steps"></a>다음 단계

- IoT에 대한 Azure Security Center [개요](overview.md) 를 참조 하십시오.
- IoT [아키텍처](architecture.md) 에 대한 Azure Security Center에 대해 알아보기
- [IoT 경고에 대한 Azure Security Center](concept-security-alerts.md) 이해 및 탐색
- [IoT 권장 사항에 대한 Azure Security Center](concept-recommendations.md) 이해 및 탐색
