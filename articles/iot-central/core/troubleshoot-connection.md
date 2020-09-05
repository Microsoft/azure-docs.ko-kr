---
title: Azure IoT Central에 대 한 장치 연결 문제 해결 | Microsoft Docs
description: 에서 장치의 데이터가 표시 되지 않는 이유를 해결 IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.openlocfilehash: 6a1506de0bf21e44d84925fabeeea860f5807e2c
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958102"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>장치의 데이터가 Azure에 표시 되지 않는 이유를 해결 IoT Central

이 문서는 장치 개발자가 IoT Central로 전송 되는 데이터가 응용 프로그램에 표시 되지 않을 수 있는 이유를 확인 하는 데 도움이 됩니다.

다음 두 가지 주요 영역을 조사할 수 있습니다.

- 장치 연결 문제
  - 장치와 같은 인증 문제에 잘못 된 자격 증명이 있습니다.
  - 네트워크 연결 문제
  - 장치가 승인 되지 않았거나 차단 됨
- 장치 페이로드 셰이프 문제

이 문제 해결 가이드는 장치 연결 문제 및 장치 페이로드 셰이프 문제에 초점을 맞춘 것입니다.

## <a name="device-connectivity-issues"></a>장치 연결 문제

이 섹션은 데이터가 IoT Central에 도달 하는지 확인 하는 데 도움이 됩니다.

아직 수행 하지 않은 경우 `az cli` 도구와 확장을 설치 `azure-iot` 합니다.

을 설치 하는 방법에 대 `az cli` 한 자세한 내용은 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조 하세요.

확장을 [설치](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-IoT?view=azure-cli-latest#extension-reference-installation) 하려면 `azure-iot` 다음 명령을 실행 합니다.

```cmd/bash
az extension add --name azure-iot
```

> [!NOTE]
> 확장 명령을 처음 실행할 때 라이브러리를 설치할지 묻는 메시지가 표시 될 수 있습니다 `uamqp` .

확장을 설치한 경우 `azure-iot` 장치를 시작 하 여 전송 하는 메시지에서 IoT Central 하는 방법을 확인 합니다.

다음 명령을 사용 하 여 IoT Central 응용 프로그램이 있는 구독에 로그인 합니다.

```cmd/bash
az login
az set account --subscription <your-subscription-id>
```

장치에서 보내는 원격 분석을 모니터링 하려면 다음 명령을 사용 합니다.

```cmd/bash
az iot central app monitor-events --app-id <app-id> --device-id <device-name>
```

장치가 IoT Central 성공적으로 연결 되 면 다음과 유사한 출력이 표시 됩니다.

```cmd/bash
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

장치가 IoT Central와 교환 하는 속성 업데이트를 모니터링 하려면 다음 미리 보기 명령을 사용 합니다.

```cmd/bash
az iot central app monitor-properties --app-id <app-id> --device-id <device-name>
```

장치에서 속성 업데이트를 성공적으로 보내면 다음과 유사한 출력이 표시 됩니다.

```cmd/bash
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

데이터가 터미널에 표시 되는 경우 데이터를 IoT Central 응용 프로그램의 데이터와 동일 하 게 만듭니다.

몇 분 후에 표시 되는 데이터가 표시 되지 않는 경우 `Enter` 출력이 중단 되는 `return` 경우 키보드에서 또는 키를 누릅니다.

여전히 터미널에 데이터가 표시 되지 않는 경우 장치가 네트워크 연결에 문제가 있거나 데이터를 IoT Central에 올바르게 전송 하지 않을 수 있습니다.

### <a name="check-the-provisioning-status-of-your-device"></a>장치의 프로 비전 상태를 확인 합니다.

데이터가 모니터에 표시 되지 않으면 다음 명령을 실행 하 여 장치의 프로 비전 상태를 확인 합니다.

```cmd/bash
az iot central app device registration-info --app-id <app-id> --device-id <device-name>
```

다음 출력은 연결에서 차단 된 장치의 예를 보여 줍니다.

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| 장치 프로 비전 상태 | 설명 | 가능한 완화 방법 |
| - | - | - |
| 프로비전됨 | 바로 인식할 때 문제가 없습니다. | N/A |
| 등록됨 | 장치가 IoT Central에 아직 연결 되지 않았습니다. | 연결 문제에 대 한 장치 로그를 확인 합니다. |
| 차단 | 장치가 IoT Central에 연결 되지 못하도록 차단 되었습니다. | 장치가 IoT Central 응용 프로그램에 대 한 연결을 차단 합니다. IoT Central에서 장치 차단을 해제 하 고 다시 시도 하세요. 자세히 알아보려면 [장치 차단](concepts-get-connected.md#device-status-values)을 참조 하세요. |
| 안 | 장치가 승인 되지 않았습니다. | 장치가 IoT Central 응용 프로그램에 연결 하도록 승인 되지 않았습니다. IoT Central에서 장치를 승인한 후 다시 시도 하세요. 자세히 알아보려면 [장치 승인](concepts-get-connected.md#connect-without-registering-devices) 을 참조 하세요. |
| 되지 | 장치가 장치 템플릿에 연결 되어 있지 않습니다. | IoT Central에서 데이터를 구문 분석 하는 방법을 알 수 있도록 장치를 장치 템플릿에 연결 합니다. |

[장치 상태 코드](concepts-get-connected.md#device-status-values)에 대해 자세히 알아보세요.

### <a name="error-codes"></a>오류 코드

그래도 데이터가 표시 되지 않는 이유를 진단할 수 없는 경우 `monitor-events` 다음 단계는 장치에서 보고 한 오류 코드를 검색 하는 것입니다.

장치에서 디버깅 세션을 시작 하거나 장치에서 로그를 수집 합니다. 장치에서 보고 하는 오류 코드를 확인 합니다.

다음 표에서는 일반적인 오류 코드 및 완화할 수 있는 작업을 보여 줍니다.

인증 흐름과 관련 된 문제가 발생 하는 경우:

| 오류 코드 | Description | 가능한 완화 방법 |
| - | - | - |
| 400 | 요청 본문이 잘못 되었습니다. 예를 들어이를 구문 분석할 수 없거나 개체의 유효성을 검사할 수 없습니다. | 올바른 요청 본문을 증명 흐름의 일부로 전송 하 고 있는지 확인 하거나 장치 SDK를 사용 합니다. |
| 401 | 권한 부여 토큰의 유효성을 검사할 수 없습니다. 예를 들어 만료 되었거나 요청의 URI에 적용 되지 않습니다. 이 오류 코드는 TPM 증명 흐름의 일부로 장치에도 반환 됩니다. | 장치에 올바른 자격 증명이 있는지 확인 합니다. |
| 404 | 장치 프로 비전 서비스 인스턴스 또는 등록과 같은 리소스가 존재 하지 않습니다. | [고객 지원 서비스를 사용 하 여 티켓을](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)제출 합니다. |
| 412 | `ETag`요청에서가 `ETag` RFC7232에 따라 기존 리소스의와 일치 하지 않습니다. | [고객 지원 서비스를 사용 하 여 티켓을](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)제출 합니다. |
| 429 | 서비스에서 작업을 제한 하 고 있습니다. 특정 서비스 제한에 대해서는 [IoT Hub Device Provisioning Service 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits)을 참조 하세요. | 메시지 빈도를 줄이고 더 많은 장치 간에 책임을 분할 합니다. |
| 500 | 내부 오류가 발생했습니다. | [고객 지원 서비스를 통해 티켓을](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 제출 하 여 더 유용 하 게 사용할 수 있는지 확인 합니다. |

## <a name="payload-shape-issues"></a>페이로드 셰이프 문제

장치가 IoT Central으로 데이터를 전송 하는 것으로 설정 된 경우 다음 단계는 장치가 데이터를 올바른 형식으로 전송 하 고 있는지 확인 하는 것입니다.

장치 데이터가 IoT Central에 표시 되지 않도록 하는 일반적인 문제에는 두 가지 주요 범주가 있습니다.

- 장치 템플릿이 장치 데이터 일치 하지 않음:
  - 오타가 나 대/소문자 일치 문제와 같은 이름 지정이 일치 하지 않습니다.
  - 스키마가 장치 템플릿에 정의 되지 않은 모델링 되지 않은 속성입니다.
  - 템플릿에 정의 된 형식과 같이 스키마가 일치 하지 `boolean` 않지만 데이터는 문자열입니다.
  - 동일한 원격 분석 이름이 여러 인터페이스에 정의 되어 있지만 장치가 IoT 플러그 앤 플레이 호환 되지 않습니다.
- 데이터 셰이프가 잘못 된 JSON입니다. 자세히 알아보려면 [원격 분석, 속성 및 명령 페이로드](concepts-telemetry-properties-commands.md)를 참조 하세요.

문제가 있는 범주를 검색 하려면 시나리오에 가장 적합 한 명령을 실행 합니다.

- 원격 분석의 유효성을 검사 하려면 미리 보기 명령을 사용 합니다.

    ```cmd/bash
    az iot central app validate-messages --app-id <app-id> --device-id <device-name>
    ```

- 속성 업데이트의 유효성을 검사 하려면 미리 보기 명령을 사용 합니다.

    ```cmd/bash
    az iot central app validate-properties --app-id <app-id> --device-id <device-name>
    ```

명령을 처음 실행할 때 라이브러리를 설치할지 묻는 메시지가 표시 될 수 있습니다 `uamqp` `validate` .

다음 출력은 validate 명령의 예 오류 및 경고 메시지를 보여 줍니다.

```cmd/bash
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

GUI를 사용 하려면 IoT Central **원시 데이터** 뷰를 사용 하 여 모델을 모델링 하 고 있지 않은지 확인 합니다. **원시 데이터** 뷰는 장치에서 형식이 잘못 된 JSON을 보내는 경우를 검색 하지 않습니다.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="원시 데이터 뷰의 스크린샷":::

문제를 발견 하면 장치 펌웨어를 업데이트 하거나 이전에 모델링 되지 않은 데이터를 모델링 하는 새 장치 템플릿을 만들어야 할 수 있습니다.

데이터를 올바르게 모델링 하는 새 템플릿을 만들도록 선택한 경우 이전 템플릿에서 새 템플릿으로 장치를 마이그레이션합니다. 자세히 알아보려면 [Azure IoT Central 응용 프로그램에서 장치 관리](howto-manage-devices.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

도움이 더 필요한 경우 [MSDN azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에 게 문의할 수 있습니다. 또는 [Azure 지원 티켓](https://portal.azure.com/#create/Microsoft.Support)을 파일에 추가할 수 있습니다.

자세한 내용은 [Azure IoT 지원 및 도움말 옵션](../../iot-fundamentals/iot-support-help.md)을 참조 하세요.
