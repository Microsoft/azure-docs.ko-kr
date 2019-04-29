---
title: Azure IoT Hub의 연결 끊김 문제 진단 및 해결
description: Azure IoT Hub의 디바이스 연결에 대한 일반적인 오류를 진단하고 해결하는 방법을 알아봅니다.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: a107689796c58b17c445e7a9cf7c6f0402ef6005
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440151"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Azure IoT Hub의 연결 끊김 문제 감지지 및 해결

IoT 디바이스의 연결 문제는 가능한 실패 지점이 많기 때문에 문제 해결이 어려울 수 있습니다. 장치 쪽 애플리케이션 로직, 물리적 네트워크, 프로토콜, 하드웨어 및 Azure IoT Hub 모두 문제를 일으킬 수 있습니다. 이 문서에서는 클라우드 쪽(디바이스 쪽에 대한 반대 개념)에서 디바이스 연결 문제를 감지 및 해결하는 방법에 대한 권장 사항을 제공합니다.

## <a name="get-alerts-and-error-logs"></a>경고 및 오류 로그 얻기

디바이스 연결이 끊어지면 Azure Monitor를 사용하여 경고를 받고 로그를 작성합니다.

### <a name="turn-on-diagnostic-logs"></a>진단 로그 설정

디바이스 연결 이벤트 및 오류를 기록하려면 IoT Hub 진단을 켭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. IoT Hub로 이동합니다.

3. **진단 설정**을 선택합니다.

4. **진단 켜기**를 선택합니다.

5. **연결** 로그가 수집되도록 설정합니다.

6. 쉽게 분석하려면 **Log Analytics에 보내기**([가격 책정 참고](https://azure.microsoft.com/pricing/details/log-analytics/))를 켜야 합니다. [연결 오류 해결](#resolve-connectivity-errors)의 예제를 참조하세요.

   ![권장 설정](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

자세히 알아보려면 [Azure IoT Hub 상태 모니터링 및 신속한 문제 진단](iot-hub-monitor-resource-health.md)을 참조하세요.

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>_연결된 디바이스_ 개수 메트릭에 대한 경고 설정

장치 연결을 끊을 때 경고를 받으려면,에서 경고를 구성 합니다 **연결 된 장치 (미리 보기)** 메트릭.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. IoT Hub로 이동합니다.

3. **경고**를 선택합니다.

4. 선택 **새 경고 규칙**합니다.

5. 선택 **조건 추가**, "연결 된 장치 (미리 보기)"를 선택 합니다.

6. 프로그램이 원하는 임계값을 설정 하 고 다음 프롬프트를 여는 경고 옵션을 완료 합니다.

자세한 내용은은 [Microsoft Azure의 클래식 경고란?](../azure-monitor/platform/alerts-overview.md)을 참조하세요.

## <a name="resolve-connectivity-errors"></a>연결 오류 해결

연결된 디바이스에 대한 진단 로그 및 경고가 켜져 있는 경우 오류가 발생하면 경고가 표시됩니다. 이 섹션에서는 경고를 받게 되면 일반적인 문제를 해결하는 방법을 설명합니다. 아래 단계에 진단 로그에 대 한 Azure Monitor 로그를 설정한 것을 가정 합니다.

1. Azure Portal에서 **Log Analytics**의 작업 영역으로 이동합니다.

2. **로그 검색**을 선택합니다.

3. IoT Hub에 대한 연결 오류 로그를 격리하려면 다음 쿼리를 입력한 다음, **실행**을 선택합니다.

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. 결과가 있는 경우 오류에 대한 자세한 정보를 얻으려면 `OperationName`, `ResultType`(오류 코드) 및 `ResultDescription`(오류 메시지)을 검색합니다.

   ![오류 로그의 예](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. 이 표를 사용하여 일반적인 오류를 이해하고 해결합니다.

    | 오류 | 근본 원인 | 해결 방법 |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | 디바이스에서 연결을 종료했지만 IoT Hub에서 이유를 모릅니다. 일반적인 원인에는 MQTT/AMQP 시간 초과 및 인터넷 연결 손실이 있습니다. | [연결 테스트](tutorial-connectivity.md)를 통해 디바이스가 IoT Hub에 연결할 수 있는지 확인합니다. 연결이 정상이지만 디바이스 연결이 간헐적으로 끊어지는 경우 사용자가 선택한 프로토콜(MQTT/AMPQ)에 적절한 디바이스 연결 유지 논리를 구현해야 합니다. |
    | 401003 IoTHubUnauthorized | IoT Hub가 연결을 인증할 수 없습니다. | SAS 또는 사용하는 기타 보안 토큰이 만료되지 않았는지 확인합니다. [Azure IoT SDK](iot-hub-devguide-sdks.md)는 특별한 구성없이 토큰을 자동으로 생성합니다. |
    | 409002 LinkCreationConflict | 디바이스에는 하나를 초과하는 연결이 있습니다. 디바이스에 대한 새로운 연결 요청이 들어오면 IoT Hub는 이 오류와 함께 이전 연결을 닫습니다. | 가장 일반적인 경우 디바이스는 연결 끊김을 감지하고 연결을 다시 설정하려 합니다. 그러나 IoT Hub는 여전히 디바이스가 연결된 것으로 간주합니다. IoT Hub는 이전 연결을 닫고 이 오류를 기록합니다. 이 오류는 대부분 다른 일시적인 문제의 부작용으로 나타나므로 추가로 문제를 해결하려면 로그에서 다른 오류를 검색합니다. 그렇지 않으면, 연결이 끊어질 경우만 새 연결 요청을 발급해야 합니다. |
    | 500001 ServerError | IoT Hub에 서버 쪽 문제가 발생했습니다. 대부분의 경우 문제는 일시적입니다. IoT Hub 팀에서는 [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)를 유지하기 위해 열심히 노력하고 있지만, IoT Hub 노드의 작은 하위 집합에 이따금 일시적인 오류가 발생할 수 있습니다. 디바이스에서 문제가 있는 노드에 연결을 시도하면, 이 오류가 나타납니다. | 일시적인 오류를 줄이려면 디바이스에서 재시도를 발급합니다. [재시도를 자동으로 관리하려면](iot-hub-reliability-features-in-sdks.md#connection-and-retry) 최신 버전의 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 사용해야 합니다.<br><br>일시적인 오류 처리 및 재시도에 대한 모범 사례는 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)를 참조하세요.  <br><br>재시도 후에도 문제가 지속되면[Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) 및 [Azure 상태](https://azure.microsoft.com/status/history/)를 확인하여 IoT Hub에 알려진 문제가 있는지 확인합니다. 알려진 문제가 없이 문제가 계속되면 추가 조사를 위해 [지원팀에 문의](https://azure.microsoft.com/support/options/)하세요. |
    | 500008 GenericTimeout | IoT Hub가 시간이 초과되기 전에 연결 요청을 완료할 수 없습니다. 500001 ServerError처럼 이 오류는 일시적일 수 있습니다. | 500001 ServerError의 문제 해결 단계에 따라 근본 원인을 찾고 이 오류를 해결합니다.|

## <a name="other-steps-to-try"></a>시도할만한 다른 단계

이전 단계가 도움이 되지 않으면 다음을 시도할 수 있습니다.

* 물리적으로 또는 원격으로(예: SSH) 문제가 있는 디바이스에 액세스할 수 있으면 [디바이스 쪽 문제 해결 가이드](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)에 따라 문제 해결을 계속합니다.

* Azure Portal > IoT Hub > IoT 디바이스에서 해당 디바이스가 **사용 가능** 상태인지 확인합니다.

* [Azure IoT Hub 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) 또는 [Azure 고객 지원팀](https://azure.microsoft.com/support/options/)의 도움을 받습니다.

이 가이드가 도움이 되지 않는 경우 모두를 위해 설명서 개선에 도움이 되도록 피드백 섹션에 의견을 남겨 주세요.

## <a name="next-steps"></a>다음 단계

* 일시적인 문제 해결에 대한 자세한 내용은 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)를 참조하세요.

* Azure IoT SDK 및 재시도 관리에 대한 자세한 내용은 [Azure IoT Hub 디바이스 SDK를 사용하여 연결 및 신뢰할 수 있는 메시징을 관리하는 방법](iot-hub-reliability-features-in-sdks.md#connection-and-retry)을 참조하세요.
