---
title: Azure IoT 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용(Node.js) | Microsoft Docs
description: Node.js를 사용하여 Azure IoT 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스에 연결하고 상호 작용합니다.
author: elhorton
ms.author: elhorton
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: fd65dcc9ce0be07daa5848a0ac583cf795150e47
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184757"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>빠른 시작: 솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용(Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT 플러그 앤 플레이 미리 보기를 사용하면 기본 디바이스 구현에 대한 지식이 없어도 디바이스 기능과 상호 작용할 수 있으므로 IoT가 간소화됩니다. 이 빠른 시작에서는 Node.js를 사용하여 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스에 연결하고 제어하는 방법을 보여줍니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 개발 머신에 Node.js가 필요합니다. [nodejs.org](https://nodejs.org)에서 여러 플랫폼에 권장하는 최신 버전을 다운로드할 수 있습니다.

다음 명령을 사용하여 개발 컴퓨터에서 Node.js의 현재 버전을 확인할 수 있습니다.

```cmd/sh
node --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행하여 허브에 대한 _IoT 허브 연결 문자열_을 가져옵니다. 이 연결 문자열을 기록해 두십시오. 이 빠른 시작의 뒷부분에서 사용하게 됩니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

다음 명령을 실행하여 허브에 추가한 디바이스에 대한 디바이스 연결 문자열을 가져옵니다. 이 연결 문자열을 기록해 두었다가 이 빠른 시작의 뒷부분에서 사용합니다.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>샘플 코드를 사용하여 SDK 리포지토리 복제

서비스 SDK는 미리 보기 상태이므로 [노드 SDK 미리 보기 분기](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh)에서 샘플을 복제해야 합니다. 원하는 폴더에서 터미널 창을 엽니다. 다음 명령을 실행하여 [Node.js용 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 리포지토리의 **pnp-preview-refresh** 분기를 복제합니다.

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
```

## <a name="run-the-sample-device"></a>샘플 디바이스 실행

이 빠른 시작에서는 Node.js로 작성한 샘플 자동 온도 조절 디바이스를 IoT 플러그 앤 플레이 디바이스로 사용할 수 있습니다. 샘플 디바이스를 실행하려면 다음을 수행합니다.

1. 터미널 창을 열고 GitHub에서 복제한 Node.js용 Microsoft Azure IoT SDK 리포지토리가 포함된 로컬 폴더로 이동합니다.

1. 이 터미널 창은 이제 **디바이스** 터미널로 사용됩니다. 복제된 리포지토리의 폴더로 이동하여 */azure-iot-sdk-node/device/samples/pnp* 폴더로 이동합니다. 다음 명령을 실행하여 모든 종속 요소를 설치합니다.

    ```cmd/sh
    npm install
    ```

1. 다음과 같이 _디바이스 연결 문자열_을 구성합니다.

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 다음 명령을 사용하여 샘플 자동 온도 조절 디바이스를 실행합니다.

    ```cmd/sh
    node simple_thermostat.js
    ```

1. 디바이스에서 일부 정보를 보냈으며 현재 온라인 상태임을 보고했다는 메시지가 표시됩니다. 이 메시지는 디바이스가 허브로 원격 분석 데이터를 보내기 시작했으며, 이제 명령 및 속성 업데이트를 받을 준비가 되었음을 나타냅니다. 이 터미널을 닫지 마세요. 서비스 샘플이 작동하는지 확인하는 데 필요합니다.

## <a name="run-the-sample-solution"></a>샘플 솔루션 실행

이 빠른 시작에서는 Node.js에서 샘플 IoT 솔루션을 사용하여 방금 설정한 샘플 디바이스와 상호 작용합니다.

1. **서비스** 터미널로 사용할 또 다른 터미널 창을 엽니다. 서비스 SDK는 미리 보기 상태이므로 [Node SDK 미리 보기 분기](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh)에서 샘플을 복제해야 합니다.

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
    ```

1. 복제된 이 리포지토리 분기의 폴더로 이동하여 */azure-iot-sdk-node/digitaltwins/samples/service/javascript* 폴더로 이동합니다. 다음 명령을 실행하여 모든 종속 요소를 설치합니다.

    ```cmd/sh
    npm install
    ```

1. 디바이스 ID 및 IoT Hub 연결 문자열의 환경 변수를 구성합니다.

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>속성 읽기

1. **디바이스** 터미널에서 샘플 자동 온도 조절 디바이스를 실행하면 온라인 상태를 나타내는 다음 메시지가 표시됩니다.

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. **서비스** 터미널로 이동한 후 다음 명령을 사용하여 디바이스 정보를 읽는 샘플을 실행합니다.

    ```cmd/sh
    node get_digital_twin.js
    ```

1. **service** 터미널 출력에서 디지털 쌍의 응답을 확인합니다. 보고된 디바이스의 모델 ID 및 관련 속성이 다음과 같이 표시됩니다.

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. 다음 코드 조각은 장치 쌍의 모델 ID를 검색하는 *get_digital_twin.js*의 코드를 보여줍니다.

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

이 시나리오에서는 `Model Id: dtmi:com:example:Thermostat;1`을 출력합니다.

### <a name="update-a-writable-property"></a>쓰기 가능 속성 업데이트

1. 코드 편집기에서 *update_digital_twin.js* 파일을 엽니다.

1. 샘플 코드를 검토합니다. 디바이스의 디지털 쌍을 업데이트 하는 JSON 패치를 만드는 방법을 볼 수 있습니다. 이 샘플에서는 코드가 자동 온도 조절기의 온도를 42라는 값으로 바꿉니다.

    ```javascript
    const patch = [{
        op: 'add',
        path: '/targetTemperature',
        value: '42'
      }]
    ```

1. **서비스** 터미널에서 다음 명령을 사용하여 속성을 업데이트하는 샘플을 실행합니다.

    ```cmd/sh
    node update_digital_twin.js
    ```

1. **디바이스** 터미널로 이동하면 디바이스가 업데이트를 수신한 것을 볼 수 있습니다.

    ```cmd/sh
    The following properties will be updated for root interface:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    Properties have been reported for component
    ```

1. **서비스** 터미널에서 다음 명령을 실행하여 속성이 업데이트되었는지 확인합니다.

    ```cmd/sh
    node get_digital_twin.js
    ```

1. **서비스** 터미널 출력의 `thermostat1` 구성 요소 아래에 있는 디지털 쌍 응답에서 업데이트된 대상 온도가 보고된 것을 볼 수 있습니다. 디바이스가 업데이트를 완료하는 데 시간이 걸릴 수 있습니다. 디바이스가 속성 업데이트를 처리할 때까지 이 단계를 반복합니다.

    ```json
    targetTemperature: 42,
    ```

### <a name="invoke-a-command"></a>명령 호출

1. *invoke_command.js* 파일을 열고 코드를 검토합니다.

1. **서비스** 터미널로 이동합니다. 다음 명령을 사용하여 명령을 호출하는 샘플을 실행합니다.

    ```cmd/sh
    set IOTHUB_COMMAND_NAME=getMaxMinReport
    set IOTHUB_COMMAND_PAYLOAD=commandpayload
    node invoke_command.js
    ```

1. **서비스** 터미널의 출력에 다음과 같은 확인이 표시됩니다.

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. **디바이스** 터미널에서 명령이 승인된 것을 볼 수 있습니다.

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 플러그 앤 플레이 디바이스를 IoT 솔루션에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스 모델에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT 플러그 앤 플레이 미리 보기 모델링 개발자 가이드](concepts-developer-guide.md)
