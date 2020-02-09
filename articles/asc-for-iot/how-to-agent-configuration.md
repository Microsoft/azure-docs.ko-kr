---
title: IoT 에이전트에 대한 Azure Security Center 구성 | Microsoft Docs
description: Iot 보안 서비스용 Azure Security Center에서 사용할 IoT 보안 에이전트에 대한 Azure Security Center를 구성 하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 6adb918bbc6d4718be8518019394582a6a843fb8
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664850"
---
# <a name="tutorial-configure-security-agents"></a>자습서: 보안 에이전트 구성

이 문서에서는 IoT 보안 에이전트에 대한 Azure Security Center 설명 하 고 변경 및 구성 방법에 대해 자세히 설명 합니다. 

> [!div class="checklist"]
> * 보안 에이전트 구성
> * 쌍 속성을 편집 하 여 에이전트 동작 변경
> * 기본 구성 검색

## <a name="agents"></a>에이전트

IoT 보안 에이전트에 대한 Azure Security Center IoT 장치에서 데이터를 수집 하 고 보안 조치를 수행 하 여 검색 된 취약점을 완화 합니다. 보안 에이전트 구성은 사용자 지정할 수 있는 모듈 쌍 속성 집합을 사용 하 여 제어할 수 있습니다. 일반적으로 이러한 속성에 대한 보조 업데이트는 자주 발생 하지 않습니다.  

IoT의 보안 에이전트 쌍 구성 개체에 대한 Azure Security Center는 JSON 형식 개체입니다. 구성 개체는 에이전트의 동작을 제어 하기 위해 정의할 수 있는 제어 가능한 속성 집합입니다. 

이러한 구성을 통해 필요한 각 시나리오에 대한 에이전트를 사용자 지정할 수 있습니다. 예를 들어 일부 이벤트를 자동으로 제외 하거나 이러한 속성을 구성 하 여 전원 소비를 최소 수준으로 유지할 수 있습니다.  

IoT 보안 에이전트 구성 [스키마](https://aka.ms/iot-security-github-module-schema) 에 Azure Security Center를 사용 하 여 변경 합니다.  

## <a name="configuration-objects"></a>구성 개체 

IoT 보안 에이전트에 대한 모든 Azure Security Center 관련 된 속성은 **azureiotsecurity** 모듈의 desired 속성 섹션에 있는 에이전트 구성 개체에 있습니다. 

구성을 수정 하려면 **azureiotsecurity** 모듈 쌍 id 내에서이 개체를 만들고 수정 합니다. 

에이전트 구성 개체가 **azureiotsecurity** 모듈 쌍에 없으면 모든 보안 에이전트 속성 값이 기본값으로 설정 됩니다. 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>구성 스키마 및 유효성 검사 

이 [스키마](https://aka.ms/iot-security-github-module-schema)에 대해 에이전트 구성의 유효성을 검사 해야 합니다. 구성 개체가 스키마와 일치 하지 않는 경우 에이전트가 시작 되지 않습니다.

 
에이전트가 실행 되는 동안 구성 개체가 유효 하지 않은 구성 (구성이 스키마와 일치 하지 않음)으로 변경 되 면 에이전트에서 잘못 된 구성을 무시 하 고 현재 구성을 계속 사용 합니다. 

### <a name="configuration-validation"></a>구성 유효성 검사

IoT 보안 에이전트에 대한 Azure Security Center는 **azureiotsecurity** 모듈 쌍 id의 보고 된 속성 섹션 내에서 현재 구성을 보고 합니다.
에이전트는 사용 가능한 모든 속성을 보고 합니다. 사용자가 속성을 설정 하지 않으면 에이전트가 기본 구성을 보고 합니다.

구성의 유효성을 검사 하려면 원하는 섹션에 설정 된 값과 보고 된 섹션에 보고 된 값을 비교 합니다.

원하는와 보고 된 속성이 일치 하지 않으면 에이전트가 구성을 구문 분석할 수 없습니다.

[스키마](https://aka.ms/iot-security-github-module-schema)에 대해 원하는 속성의 유효성을 검사 하 고, 오류를 수정 하 고, 원하는 속성을 다시 설정 합니다.

> [!NOTE]
> 에이전트가 원하는 구성을 구문 분석할 수 없는 경우 에이전트에서 구성 오류 경고가 발생 합니다.
> 보고 및 원하는 섹션을 비교 하 여 경고가 여전히 적용 되는지 확인 합니다.

## <a name="editing-a-property"></a>속성 편집 

모든 사용자 지정 속성은 **azureiotsecurity** 모듈 쌍 내의 에이전트 구성 개체 내에서 설정 해야 합니다.
기본 속성 값을 사용 하려면 구성 개체에서 속성을 제거 합니다.

### <a name="setting-a-property"></a>속성 설정

1. IoT Hub에서 변경 하려는 장치를 찾아 선택 합니다.

1. 장치를 클릭 한 다음 **azureiotsecurity** 모듈에서 클릭 합니다.

1. **모듈 id**쌍을 클릭 합니다.

1. 보안 모듈에서 변경 하려는 속성을 편집 합니다.
   
   예를 들어 연결 이벤트를 높은 우선 순위로 구성 하 고 7 분 마다 높은 우선 순위 이벤트를 수집 하려면 다음 구성을 사용 합니다.
   
    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. 페이지 맨 아래에 있는 **저장**을 참조하세요.

### <a name="using-a-default-value"></a>기본값 사용

기본 속성 값을 사용 하려면 구성 개체에서 속성을 제거 합니다.

## <a name="default-properties"></a>기본 속성 

다음 표에서는 IoT 보안 에이전트에 대한 Azure Security Center의 제어 가능한 속성을 포함 합니다.

[GitHub](https\://aka.ms/iot-security-module-default)의 적절 한 스키마에서 기본값을 사용할 수 있습니다.

| name| 상태 | 유효한 값| 기본값| 설명 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|필수: false |유효한 값: ISO 8601 형식의 기간 |기본값: PT7M |우선 순위가 높은 메시지가 전송 되기 전의 최대 시간 간격입니다.|
|lowPriorityMessageFrequency |필수: false|유효한 값: ISO 8601 형식의 기간 |기본값: PT5H |낮은 우선 순위의 메시지가 전송 되기 전의 최대 시간입니다.| 
|snapshotFrequency |필요: false|유효한 값: ISO 8601 형식의 기간 |기본값 PT13H |장치 상태 스냅숏을 만드는 시간 간격입니다.| 
|maxLocalCacheSizeInBytes |필수: false |유효한 값: |기본값: 256만, 8192 보다 큼 | 에이전트의 메시지 캐시에 허용 되는 최대 저장소 (바이트)입니다. 메시지를 보내기 전에 장치에 메시지를 저장 하는 데 사용할 수 있는 공간의 최대 크기입니다.| 
|maxMessageSizeInBytes |필수: false |유효한 값: 8192 보다 큰 양수입니다. 262144 보다 작음 |기본값: 204800 |클라우드 메시지에 대한 에이전트의 최대 허용 크기입니다. 이 설정은 각 메시지에서 전송 되는 최대 데이터의 양을 제어 합니다. |
|eventPriority $ {EventName} |필수: false |유효한 값: 높음, 낮음, 꺼짐 |기본값: |에이전트에서 생성 한 모든 이벤트의 우선 순위 | 

### <a name="supported-security-events"></a>지원 되는 보안 이벤트

|이벤트 이름| PropertyName | 기본값| 스냅숏 이벤트| 세부 정보 상태  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|진단 이벤트|eventPriorityDiagnostic| 해제| 거짓| 에이전트 관련 진단 이벤트입니다. 자세한 정보 로깅에이 이벤트를 사용 합니다.| 
|구성 오류 |eventPriorityConfigurationError |낮음 |거짓 |에이전트가 구성을 구문 분석 하지 못했습니다. 스키마에 대한 구성을 확인 합니다.| 
|삭제 된 이벤트 통계 |eventPriorityDroppedEventsStatistics |낮음 |참|에이전트 관련 이벤트 통계입니다. |
|메시지 통계|eventPriorityMessageStatistics |낮음 |참 |에이전트 관련 메시지 통계입니다. |
|연결 된 하드웨어|eventPriorityConnectedHardware |낮음 |참 |장치에 연결 된 모든 하드웨어의 스냅숏입니다.|
|수신 대기 포트|eventPriorityListeningPorts |높음 |참 |장치에서 열려 있는 모든 수신 포트의 스냅숏입니다.|
|프로세스 만들기 |eventPriorityProcessCreate |낮음 |거짓 |장치에서 프로세스 생성을 감사 합니다.|
|프로세스 종료|eventPriorityProcessTerminate |낮음 |거짓 |장치에서 프로세스 종료를 감사 합니다.| 
|시스템 정보 |eventPrioritySystemInformation |낮음 |참 |시스템 정보의 스냅숏 (예: OS 또는 CPU)입니다.| 
|로컬 사용자| eventPriorityLocalUsers |높음 |참|시스템 내에서 등록 된 로컬 사용자의 스냅숏입니다. |
|로그인|  eventPriorityLogin |높음|거짓|로그인 이벤트를 장치 (로컬 및 원격 로그인)로 감사 합니다.|
|연결 만들기 |eventPriorityConnectionCreate|낮음|거짓|장치에서 생성 된 TCP 연결을 감사 합니다. |
|방화벽 구성| eventPriorityFirewallConfiguration|낮음|참|장치 방화벽 구성의 스냅숏 (방화벽 규칙). |
|OS 기준| eventPriorityOSBaseline| 낮음|참|장치 OS 기준 검사의 스냅숏입니다.|
|
 

## <a name="next-steps"></a>다음 단계

- [IoT 권장 사항에 대한 Azure Security Center 이해](concept-recommendations.md)
- [IoT 경고에 대한 Azure Security Center 살펴보기](concept-security-alerts.md)
- [원시 보안 데이터 액세스](how-to-security-data-access.md)
