---
title: IoT 이벤트 집계에 대한 Azure Security Center 이해 | Microsoft Docs
description: IoT 이벤트 집계에 대한 Azure Security Center에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: ca1d1a5761e62b2838a474dcb83f450987972998
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928968"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>IoT 이벤트 집계를 위한 Azure Security Center

IoT 보안 에이전트에 대한 Azure Security Center 로컬 장치에서 데이터 및 시스템 이벤트를 수집 하 고이 데이터를 처리 및 분석을 위해 Azure 클라우드에 보냅니다. 보안 에이전트는 새로운 프로세스 및 새 연결 이벤트를 포함 하 여 다양 한 유형의 장치 이벤트를 수집 합니다. 새 프로세스와 새 연결 이벤트는 모두 한 초 안에 장치에서 자주 발생 하는 반면, 강력 하 고 포괄적인 보안에 중요 한 경우 보안 에이전트가 강제로 보내는 메시지 수가 빠르게 도달 하거나 초과 하는 IoT Hub 수 있습니다. 할당량 및 비용 제한. 그러나 이러한 이벤트에는 장치를 보호 하는 데 매우 중요 한 보안 정보가 포함 되어 있습니다.

장치를 보호 하는 동안 추가 할당량과 비용을 줄이기 위해 Azure Security Center IoT 에이전트는 이러한 유형의 이벤트를 집계 합니다.

이벤트 집계는 기본적으로 **설정** 되어 있지만 권장 되지는 않지만 언제 든 지 수동으로 **해제할** 수 있습니다.

집계는 현재 다음과 같은 유형의 이벤트에 사용할 수 있습니다.
* ProcessCreate
* ConnectionCreate
* ProcessTerminate (Windows에만 해당)

## <a name="how-does-event-aggregation-work"></a>이벤트 집계는 어떻게 작동 하나요?
이벤트 집계가 **설정**되어 있으면 IoT 에이전트에 대한 Azure Security Center 간격 기간 또는 기간에 대한 이벤트를 집계 합니다.
간격 기간이 지나면 에이전트는 추가 분석을 위해 집계 된 이벤트를 Azure 클라우드로 보냅니다.
집계 된 이벤트는 Azure 클라우드로 전송 될 때까지 메모리에 저장 됩니다.

에이전트에서 메모리에 이미 유지 중인 이벤트와 동일한 이벤트를 수집할 때마다 에이전트의 메모리 사용 공간을 줄이기 위해 에이전트는이 특정 이벤트의 적중 횟수를 늘립니다. 집계 기간이 지나면 에이전트는 발생 한 이벤트의 각 특정 유형에 대한 적중 횟수를 보냅니다. 이벤트 집계는 수집 된 각 이벤트 형식의 적중 횟수에 대한 집계 일 뿐입니다.

이벤트는 다음 조건이 충족 될 때만 동일 하 게 간주 됩니다. 

* ProcessCreate 이벤트- **명령줄**, **실행 파일**, **사용자 이름**및 **userid** 가 동일한 경우
* ConnectionCreate 이벤트- **명령줄**, **userId**, **방향**, **로컬 주소**, **원격 주소**, * * 프로토콜 및 **대상 포트가** 동일한 경우
* ProcessTerminate events- **실행 파일** 및 **종료 상태가** 동일한 경우

### <a name="working-with-aggregated-events"></a>집계 이벤트 작업

집계 중에 집계 되지 않은 이벤트 속성은 무시 되 고 값이 0 인 log analytics에 표시 됩니다. 
* ProcessCreate 이벤트- **processId**및 **parentprocessid** 가 0으로 설정 되어 있습니다.
* ConnectionCreate 이벤트- **processId**및 **원본 포트가** 0으로 설정 됨

## <a name="event-aggregation-based-alerts"></a>이벤트 집계 기반 경고 
분석 후 IoT에 대한 Azure Security Center는 의심 스러운 집계 이벤트에 대한 보안 경고를 생성 합니다. 집계 이벤트에서 생성 된 경고는 집계 된 각 이벤트에 대해 한 번만 나타납니다.

각 이벤트에 대한 집계 시작 시간, 종료 시간 및 적중 횟수는 조사 중에 사용할 Log Analytics 내의 이벤트 **ExtraDetails** 필드에 기록 됩니다. 

집계 된 각 이벤트는 24 시간 동안 수집 된 경고를 나타냅니다. 각 이벤트의 왼쪽 위에 있는 이벤트 옵션 메뉴를 사용 하 여 각 개별 집계 이벤트를 **해제할** 수 있습니다.    

## <a name="event-aggregation-twin-configuration"></a>이벤트 집계 쌍 구성
**Azureiotsecurity** 모듈의 모듈 쌍 id의 [에이전트 구성 개체](how-to-agent-configuration.md) 내에서 IoT 이벤트 집계에 대한 Azure Security Center 구성을 변경 합니다.

| 구성 이름 | 가능한 값 | 세부 정보 | 설명 |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | 프로세스 생성 이벤트에 대한 이벤트 집계 사용/사용 안 함 |
| aggregationIntervalProcessCreate | ISO8601 Timespan 문자열 | 프로세스 생성 이벤트에 대한 집계 간격 |
| aggregationEnabledConnectionCreate | boolean| 연결 생성 이벤트에 대한 이벤트 집계 사용/사용 안 함 |
| aggregationIntervalConnectionCreate | ISO8601 Timespan 문자열 | 연결 생성 이벤트에 대한 집계 간격 |
| aggregationEnabledProcessTerminate | boolean | 프로세스 종료 이벤트에 대한 이벤트 집계 사용/사용 안 함 | Windows만|
| aggregationIntervalProcessTerminate | ISO8601 Timespan 문자열 | 프로세스 종료 이벤트에 대한 집계 간격 | Windows만|
|

## <a name="default-configurations-settings"></a>기본 구성 설정

| 구성 이름 | 기본값 |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | PT1H.JSON|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | PT1H.JSON|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | PT1H.JSON|
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 보안 에이전트 집계 및 사용 가능한 이벤트 구성 옵션에 대한 Azure Security Center에 대해 알아보았습니다.

IoT 배포에 대한 Azure Security Center 시작 하려면 다음 문서를 사용 하세요.

- [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md) 이해
- [보안 에이전트](how-to-deploy-agent.md) 선택 및 배포
- IoT [서비스 필수 구성 요소](service-prerequisites.md) 에 대한 Azure Security Center 검토
- [IoT Hub에서 IoT 서비스에 Azure Security Center를 사용 하도록 설정](quickstart-onboard-iot-hub.md) 하는 방법을 알아봅니다.
- [IoT에 대한 AZURE SECURITY CENTER FAQ](resources-frequently-asked-questions.md) 에서 서비스에 대해 자세히 알아보세요.
