---
title: C에 대한 에이전트 로컬 구성에 대한 Azure Security Center 이해 | Microsoft Docs
description: C에 대한 에이전트 로컬 구성에 대한 Azure Security Center에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 2725a824da26dafcbc215e4c302ec38ad4b5a699
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600541"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>LocalConfiguration. json 파일-C 에이전트 이해

IoT 보안 에이전트의 Azure Security Center은 로컬 구성 파일의 구성을 사용 합니다.
보안 에이전트는 에이전트 시작 시 구성을 한 번 읽습니다.
로컬 구성 파일에 있는 구성에는 인증 구성 및 기타 에이전트 관련 구성이 포함 됩니다.
이 파일에는 JSON 표기법에서 "키-값" 쌍의 구성이 포함 되며 에이전트가 설치 될 때 구성이 채워집니다. 

기본적으로 파일은:/var/ASCIoTAgent/LocalConfiguration.json에 있습니다.

구성 파일에 대한 변경 내용은 에이전트가 다시 시작 될 때 수행 됩니다. 

## <a name="security-agent-configurations-for-c"></a>C에 대한 보안 에이전트 구성
| 구성 이름 | 가능한 값 | 세부 정보 | 
|:-----------|:---------------|:--------|
| 인 | GUID | 에이전트 고유 식별자입니다. |
| TriggerdEventsInterval | ISO8601 문자열 | 트리거된 이벤트 컬렉션에 대한 스케줄러 간격 |
| ConnectionTimeout | ISO8601 문자열 | IoThub에 대한 연결 시간이 초과 될 때 까지의 기간입니다. |
| 인증 | JsonObject | 인증 구성. 이 개체는 IoTHub에 대한 인증에 필요한 모든 정보를 포함 합니다. |
| 클레임 | "DPS", "SecurityModule", "Device" | 인증 id-DPS를 통해 인증을 수행 하는 경우 DPS, SecurityModule을 통해 인증 되는 경우 보안 모듈 자격 증명 또는 장치를 통해 인증 된 경우 |
| AuthenticationMethod | "SasToken", "New-selfsignedcertificate" | 인증에 대한 사용자 암호-SasToken을 선택 합니다. 암호 사용이 대칭 키인 경우 자체 서명 된 인증서 인 경우 자체 서명 된 인증서를 선택 합니다.  |
| Null | 파일 경로 (문자열) | 인증 암호를 포함 하는 파일의 경로입니다. |
| HostName | string | Azure iot hub의 호스트 이름입니다. 일반적으로 < 내 허브 >. azure-devices.net |
| DeviceID | string | 장치 ID (Azure IoT Hub에 등록 됨) |
| 않으면 | JsonObject | DPS 관련 구성 |
| IDScope | string | DPS의 ID 범위 |
| RegistrationId | string  | DPS 장치 등록 ID |
| 로깅 | JsonObject | 에이전트로 거 관련 구성 |
| SystemLoggerMinimumSeverity | 0 < = number < = 4 | 이 심각도가 같은 로그 메시지는/var/log/syslog에 기록 됩니다 (0은 가장 낮은 심각도 임). |
| DiagnosticEventMinimumSeverity | 0 < = number < = 4 | 이 심각도가 같은 로그 메시지는 진단 이벤트로 전송 됩니다 (0은 가장 낮은 심각도 임). |

## <a name="security-agent-configurations-code-example"></a>보안 에이전트 구성 코드 예제
```JSON
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
- IoT 서비스에 대한 Azure Security Center [개요](overview.md) 를 참조 하십시오.
- IoT [아키텍처](architecture.md) 에 대한 Azure Security Center에 대해 자세히 알아보기
- IoT [서비스](quickstart-onboard-iot-hub.md) 에 대한 Azure Security Center 사용
- IoT 서비스에 대한 Azure Security Center [FAQ](resources-frequently-asked-questions.md) 읽기
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [추천 사항](concept-recommendations.md)을 살펴봅니다.
- 보안 [경고](concept-security-alerts.md) 이해