---
title: IoT 에이전트 미리 보기에 대 한 Azure Security Center 구성 | Microsoft Docs
description: IoT 용 Azure Security center 사용에 대 한 에이전트를 구성 하는 방법에 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 1e212e752309fd8347836d462a3394da2fca4a15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358539"
---
# <a name="tutorial-configure-security-agents"></a>자습서: 보안 에이전트 구성

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 IoT 보안 에이전트에 대 한 Azure 보안 센터 (ASC) 설명 ASC IoT 보안 에이전트에 대 한 구성으로 변경 하는 방법입니다.

> [!div class="checklist"]
> * 보안 에이전트 구성
> * 쌍 속성을 편집 하 여 에이전트 동작을 변경 합니다.
> * 기본 구성 검색

## <a name="agents"></a>에이전트

IoT 보안 에이전트에 대 한 ASC IoT 장치에서 데이터를 수집 하 고 검색 된 취약성을 완화 하기 위한 보안 작업을 수행 합니다. 보안 에이전트 구성을 사용자 지정할 수 있습니다 하는 모듈 쌍 속성 집합을 사용 하 여 제어할 수 있는 경우 일반적으로 이러한 속성에 대 한 보조 업데이트는 자주 일어나지 합니다.  

IoT의 보안 에이전트 쌍 구성 개체에 대 한 ASC.json 형식 개체입니다. 구성 개체에는 에이전트의 동작을 제어 하려면 정의할 수 있는 제어할 수 있는 속성 집합입니다. 

이러한 구성은 필요한 각 시나리오에 대 한 에이전트를 사용자 지정할 수 있습니다. 예를 들어 자동으로 일부 이벤트를 제외 하거나 전력 소비량을 최소 수준으로 유지 됩니다 가능한 이러한 속성을 구성 하 여 합니다.  

ASC를 사용 하 여 IoT 보안 에이전트 구성을 [스키마](https://aka.ms/iot-security-github-module-schema) 변경할 수 있도록 합니다.  

## <a name="configuration-objects"></a>구성 개체 

IoT 보안 에이전트에 대 한 각 ASC의 에이전트 구성 개체의 desired 속성 섹션에 있는 속성 관련 된 **azureiotsecurity** 모듈입니다. 

구성을 수정 하려면 만들기 및이 개체를 수정 합니다 **azureiotsecurity** 모듈 쌍 id입니다. 

에이전트 구성 개체에 없는 경우는 **azureiotsecurity** 모듈 쌍 모든 보안 에이전트 속성 값은 기본값으로 설정 됩니다. 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

이 대 한 에이전트 구성 변경으로 인해 유효성을 검사 해야 [스키마](https://aka.ms/iot-security-github-module-schema)합니다.
구성 개체의 스키마와 일치 하지 않으면 에이전트가 시작 되지 됩니다.

## <a name="configuration-schema-and-validation"></a>구성 스키마 및 유효성 검사 

이 대 한 에이전트 구성의 유효성을 검사 해야 [스키마](https://aka.ms/iot-security-github-module-schema)합니다. 구성 개체의 스키마와 일치 하지 않으면 에이전트 시작 되지 됩니다.

 
에이전트가 실행 되는 동안 구성 개체 (구성 스키마가 일치 하지 않습니다) 유효 하지 않은 구성으로 변경 되 면 하는 경우 에이전트는 잘못 된 구성을 무시 하 고 현재 구성을 사용 하 여 계속 됩니다. 

## <a name="editing-a-property"></a>속성 편집 

내 에이전트 구성 개체 내에서 모든 사용자 지정 속성을 설정 해야 합니다 **azureiotsecurity** 모듈 쌍입니다.
기본 속성 값을 사용 하려면 구성 개체에서 속성을 제거 합니다.

### <a name="setting-a-property"></a>속성 설정

1. IoT Hub에서 찾아 변경 하려는 장치를 선택 합니다.

1. 장치에서 선택한 다음 클릭 **azureiotsecurity** 모듈입니다.

1. 클릭할 **모듈 Identity 쌍**합니다.

1. 보안 모듈의 desired 속성을 편집 합니다.
   
   예를 들어 높은 우선 순위로 연결 이벤트를 구성 하 고 우선 순위가 높은 이벤트 7 분 마다 수집을 다음 구성을 사용 합니다.
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. **저장**을 클릭합니다.

### <a name="using-a-default-value"></a>기본값을 사용 하 여

기본 속성 값을 사용 하려면 구성 개체에서 속성을 제거 합니다.

## <a name="default-properties"></a>기본 속성 

다음 표에서 IoT 보안 에이전트에 대 한 ASC의 제어할 수 있는 속성을 포함합니다.

기본값은 적절 한 스키마에서 사용할 수 있습니다 [Github](https://aka.ms/iot-security-module-default)합니다.

| 이름| 상태 | 유효한 값| 기본값| 설명 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|필수: false |유효한 값:  ISO 8601 형식으로 기간 |기본값: PT7M |최대 시간 우선 순위가 높은 메시지 보다 먼저 전송 됩니다.|
|lowPriorityMessageFrequency |필수: false|유효한 값:  ISO 8601 형식으로 기간 |기본값: PT5H |최대 시간 우선 순위가 낮은 메시지 보다 먼저 전송 됩니다.| 
|snapshotFrequency |필요: false|ISO 8601 형식으로 올바른 값: 기간 |기본값은 PT13H |장치 상태 스냅숏 만들기에 대 한 시간 간격입니다.| 
|maxLocalCacheSizeInBytes |필수: false |유효한 값:  |기본값: 8192 보다 큰 2560000 | 최대 저장소 (바이트) 에이전트의 메시지가 캐시에 대 한 허용 합니다. 메시지 전송 되기 전에 장치에 메시지를 저장 하도록 허용 하는 공간의 최대 크기입니다.| 
|maxMessageSizeInBytes |필수: false |유효한 값:  8192 보다 작은 262144 보다 큰 양의 숫자 |기본값: 204800 |최대 허용 크기에 클라우드 메시지 에이전트. 이 설정은 각 메시지의 최대 데이터 양을 제어 합니다. |
|eventPriority${EventName} |필수: false |유효한 값:  높음, 낮음, 해제 |기본값: |이벤트를 생성 하는 모든 에이전트의 우선 순위 | 

### <a name="supported-security-events"></a>지원 되는 보안 이벤트

|이벤트 이름| PropertyName | 기본값| 스냅숏 이벤트| 세부 정보 상태  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|진단 이벤트|eventPriorityDiagnostic| 꺼짐| 거짓| 에이전트 관련 진단 이벤트입니다. 이 이벤트를 사용 하 여 자세한 정보 로깅에 대 한 합니다.| 
|구성 오류 |eventPriorityConfigurationError |낮음 |거짓 |에이전트는 구성을 구문 분석 하지 못했습니다. 스키마에 대 한 구성을 확인 합니다.| 
|삭제 된 이벤트 통계 |eventPriorityDroppedEventsStatistics |낮음 |True |에이전트 관련 이벤트 통계. |
|메시지 통계|eventPriorityMessageStatistics |낮음 |True  |에이전트 관련 메시지 통계입니다. |
|연결 된 하드웨어|eventPriorityConnectedHardware |낮음 |True  |스냅숏의 모든 하드웨어 장치에 연결 합니다.|
|수신 대기 포트|eventPriorityListeningPorts |높음 |True  |장치에서 열려 있는 모든 수신 포트의 스냅숏.|
|프로세스 만들기 |eventPriorityProcessCreate |낮음 |거짓 |감사 프로세스는 장치에서 생성 합니다.|
|프로세스 종료|eventPriorityProcessTerminate |낮음 |거짓 |감사는 장치에서 종료를 처리합니다.| 
|시스템 정보 |eventPrioritySystemInformation |낮음 |True  |스냅숏으로 시스템 정보 (예: OS 또는 CPU)입니다.| 
|로컬 사용자| eventPriorityLocalUsers |높음 |True |시스템 내에서 등록 된 로컬 사용자는 스냅숏. |
|로그인|  eventPriorityLogin |높음|거짓|장치 (로컬 및 원격 로그인)에 로그인 이벤트를 감사 합니다.|
|연결 만들기 |eventPriorityConnectionCreate|낮음|거짓|장치에서 만들어지고 TCP 연결을 감사 합니다. |
|방화벽 구성| eventPriorityFirewallConfiguration|낮음|True |장치 방화벽 구성 (방화벽 규칙)의 스냅숏. |
|OS 기준| eventPriorityOSBaseline| 낮음|True |장치 OS 기준의 스냅숏을 확인 합니다.|
 

## <a name="next-steps"></a>다음 단계

- [ASC IoT 권장 사항에 대 한 이해](concept-recommendations.md)
- [ASC IoT 경고에 대 한 탐색](concept-security-alerts.md)
- [원시 보안 데이터에 액세스](how-to-security-data-access.md)
