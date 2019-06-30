---
title: Azure IoT Hub에 대한 디바이스 연결 확인
description: IoT Hub 도구를 사용하여 개발 중인 IoT 허브에 대한 디바이스 연결 문제를 해결합니다.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.custom: mvc
ms.date: 02/22/2019
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: caa249dda4215dfcef13df96d2dd4245cae49efd
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595761"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>자습서: 시뮬레이션된 디바이스를 사용하여 IoT Hub와 연결 테스트

이 자습서에서는 Azure IoT Hub 포털 도구와 Azure CLI 명령을 사용하여 디바이스 연결을 테스트합니다. 또한 데스크톱 컴퓨터에서 실행되는 간단한 디바이스 시뮬레이터도 사용합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 디바이스 인증 확인
> * 디바이스-클라우드 연결 확인
> * 클라우드-장치 연결 확인
> * 디바이스 쌍 동기화 확인

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서에서 실행하는 CLI 스크립트에서 [Azure CLI용 Microsoft Azure IoT 확장](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md)을 사용해야 합니다. 이 확장을 설치하려면 다음 CLI 명령을 실행합니다.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

이 자습서에서 실행하는 장치 시뮬레이터 애플리케이션은 Node.js를 사용하여 작성해야 합니다. 개발 컴퓨터에 Node.js v10.x.x 이상이 필요합니다.

[nodejs.org](https://nodejs.org)에서 여러 플랫폼에 대한 Node.js를 다운로드할 수 있습니다.

다음 명령을 사용하여 개발 컴퓨터에서 Node.js의 현재 버전을 확인할 수 있습니다.

```cmd/sh
node --version
```

[https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip )에서 샘플 디바이스 시뮬레이터 Node.js 프로젝트를 다운로드하고 ZIP 보관 파일을 추출합니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이전의 빠른 시작 또는 자습서에서 체험 또는 표준 계층 IoT 허브를 만든 경우 이 단계를 건너뛸 수 있습니다.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>디바이스 인증 확인

허브와 데이터를 교환하려면 먼저 디바이스에서 허브를 인증해야 합니다. 포털의 **디바이스 관리** 섹션에서 **IoT 디바이스** 도구를 사용하여 디바이스를 관리하고 사용되는 인증 키를 확인할 수 있습니다. 자습서의 이 섹션에서는 새 테스트 디바이스를 추가하고, 해당 키를 검색한 다음, 테스트 디바이스에서 허브에 연결할 수 있는지 확인합니다. 나중에 디바이스에서 오래된 키를 사용하려고 할 때 발생하는 상황을 관찰하기 위해 인증 키를 다시 설정합니다. 자습서의 이 섹션에서는 Azure Portal을 사용하여 디바이스, Node.js 디바이스 시뮬레이터 샘플을 생성, 관리 및 모니터링합니다.

포털에 로그인하고 IoT 허브로 이동합니다. 그런 다음, **IoT 디바이스** 도구로 이동합니다.

![IoT 디바이스 도구](media/tutorial-connectivity/iot-devices-tool.png)

새 디바이스를 등록하려면 **+ 추가**를 클릭하고, **디바이스 ID**를 **MyTestDevice**로 설정하고, **저장**을 클릭합니다.

![새 디바이스 추가](media/tutorial-connectivity/add-device.png)

**MyTestDevice**에 대한 연결 문자열을 검색하려면 디바이스 목록에서 해당 문자열을 클릭한 다음, **연결 문자열 - 기본 키** 값을 복사합니다. 연결 문자열에는 디바이스에 대한 *공유 액세스 키*가 포함되어 있습니다.

![디바이스 연결 문자열 검색](media/tutorial-connectivity/copy-connection-string.png)

원격 분석을 IoT 허브로 보내는 **MyTestDevice**를 시뮬레이션하려면, 앞에서 다운로드한 Node.js 시뮬레이션된 장치 애플리케이션을 실행합니다.

개발 컴퓨터의 터미널 창에서 다운로드한 Node.js 프로젝트 샘플의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Tutorials\ConnectivityTests** 폴더로 이동합니다.

터미널 창에서 다음 명령을 실행하여 필요한 라이브러리를 설치하고 시뮬레이션된 장치 애플리케이션을 실행합니다. 포털에서 디바이스를 추가할 때 적어 둔 디바이스 연결 문자열을 사용합니다.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

터미널 창에서 허브에 연결하려고 하면 다음과 같은 정보가 표시됩니다.

![시뮬레이션된 디바이스 연결](media/tutorial-connectivity/sim-1-connected.png)

이제 IoT 허브에서 생성된 디바이스 키를 사용하여 디바이스에서 성공적으로 인증되었습니다.

### <a name="reset-keys"></a>키 다시 설정

이 섹션에서는 디바이스 키를 다시 설정하고, 시뮬레이션된 디바이스에서 연결하려고 할 때 발생하는 오류를 관찰합니다.

**MyTestDevice**에 대한 기본 디바이스 키를 다시 설정하려면 다음 명령을 실행합니다.

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-cli-iot-ext

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

개발 컴퓨터의 터미널 창에서 시뮬레이션된 장치 애플리케이션을 다시 실행합니다.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

이번에는 애플리케이션에서 연결하려고 하면 다음과 같은 인증 오류가 표시됩니다.

![연결 오류](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>SAS(공유 액세스 서명) 토큰 생성

디바이스에서 IoT Hub 디바이스 SDK 중 하나를 사용하는 경우 SDK 라이브러리 코드에서 허브를 인증하는 데 사용되는 SAS 토큰을 생성합니다. SAS 토큰은 허브 이름, 디바이스 이름 및 디바이스 키로부터 생성됩니다.

클라우드 프로토콜 게이트웨이 또는 사용자 지정 인증 체계의 일부와 같은 몇 가지 시나리오에서는 SAS 토큰을 직접 생성해야 할 수도 있습니다. SAS 생성 코드 문제를 해결하려면 테스트 중에 사용할 알려진 SAS 토큰을 생성하는 것이 유용합니다.

> [!NOTE]
> SimulatedDevice-2.js 샘플에는 SDK를 포함 및 포함하지 않고 SAS 토큰을 생성하는 예가 포함되어 있습니다.

CLI를 사용하여 알려진 유효한 SAS 토큰을 생성하려면 다음 명령을 실행합니다.

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

생성된 SAS 토큰의 전체 텍스트를 적어 둡니다. SAS 토큰은 다음과 같습니다. `SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307`

개발 컴퓨터의 터미널 창에서 다운로드한 Node.js 프로젝트 샘플의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Tutorials\ConnectivityTests** 폴더로 이동합니다.

터미널 창에서 다음 명령을 실행하여 필요한 라이브러리를 설치하고 시뮬레이션된 장치 애플리케이션을 실행합니다.

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

터미널 창에서 SAS 토큰을 사용하여 허브에 연결하려고 하면 다음과 같은 정보가 표시됩니다.

![토큰으로 시뮬레이션된 디바이스 연결](media/tutorial-connectivity/sim-2-connected.png)

이제 CLI 명령으로 생성된 테스트 SAS 토큰을 사용하여 디바이스에서 성공적으로 인증되었습니다. **SimulatedDevice-2.js** 파일에는 코드에서 SAS 토큰을 생성하는 방법을 보여 주는 샘플 코드가 포함되어 있습니다.

### <a name="protocols"></a>프로토콜

디바이스에서는 다음 프로토콜 중 하나를 사용하여 IoT 허브에 연결할 수 있습니다.

| 프로토콜 | 아웃바운드 포트 |
| --- | --- |
| MQTT |8883 |
| WebSocket을 통한 MQTT |443 |
| AMQP |5671 |
| Websocket 통한 AMQP |443 |
| HTTPS |443 |

아웃바운드 포트가 방화벽으로 차단되면 다음과 같이 디바이스에서 연결할 수 없습니다.

![포트 차단됨](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>디바이스-클라우드 연결 확인

디바이스가 연결되면 일반적으로 해당 디바이스에서 IoT 허브로 원격 분석을 보내려고 합니다. 이 섹션에서는 디바이스에서 보낸 원격 분석이 허브에 도달하는지 확인할 수 있는 방법을 보여 줍니다.

먼저 다음 명령을 사용하여 시뮬레이션된 디바이스에 대한 현재 연결 문자열을 검색합니다.

```azurecli-interactive
az iot hub device-identity show-connection-string --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

메시지를 보내는 시뮬레이션된 디바이스를 실행하려면 다운로드한 코드의 **iot-hub\Tutorials\ConnectivityTests** 폴더로 이동합니다.

터미널 창에서 다음 명령을 실행하여 필요한 라이브러리를 설치하고 시뮬레이션된 장치 애플리케이션을 실행합니다.

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

터미널 창에서 허브로 원격 분석을 보내면 다음과 같은 정보가 표시됩니다.

![시뮬레이션된 디바이스에서 메시지 보내기](media/tutorial-connectivity/sim-3-sending.png)

포털에서 **메트릭**을 사용하여 원격 분석 메시지가 IoT Hub에 도달하는지 확인할 수 있습니다. **리소스** 드롭다운에서 IoT 허브를 선택하고, **전송된 원격 분석 메시지 수**를 메트릭으로 선택하고, 시간 범위를 **지난 시간**으로 설정합니다. 차트에 시뮬레이션된 디바이스에서 보낸 메시지의 집계 수가 표시됩니다.

![IoT Hub 메트릭 표시](media/tutorial-connectivity/metrics-portal.png)

시뮬레이션된 디바이스를 시작한 후에 메트릭을 사용할 수 있게 하는 데 몇 분 정도 걸립니다.

## <a name="check-cloud-to-device-connectivity"></a>클라우드-장치 연결 확인

이 섹션에서는 디바이스에 대한 테스트 직접 메서드를 호출하여 클라우드-디바이스 연결을 확인하는 방법을 보여줍니다. 개발 컴퓨터에서 시뮬레이션된 디바이스를 실행하여 허브로부터의 직접 메서드 호출을 수신 대기합니다.

터미널 창에서 다음 명령을 사용하여 시뮬레이션된 장치 애플리케이션을 실행합니다.

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

CLI 명령을 사용하여 디바이스에서 직접 메서드를 호출합니다.

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

시뮬레이션된 디바이스에서 직접 메서드 호출을 받으면 콘솔에 메시지가 출력됩니다.

![시뮬레이션된 디바이스에서 직접 메서드 호출 받기](media/tutorial-connectivity/receive-method-call.png)

시뮬레이션된 디바이스에서 직접 메서드 호출을 받으면 승인을 허브로 다시 보냅니다.

![직접 메서드 승인 받기](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>쌍 동기화 확인

디바이스에서 쌍을 사용하여 디바이스와 허브 간의 상태를 동기화합니다. 이 섹션에서는 CLI 명령을 사용하여 _desired 속성_을 디바이스로 보내고 디바이스에서 보낸 _reported 속성_을 읽습니다.

이 섹션에서 사용하는 시뮬레이션된 디바이스에서는 시작할 때마다 reported 속성을 허브로 보내고, desired 속성을 받을 때마다 이 속성을 콘솔에 출력합니다.

터미널 창에서 다음 명령을 사용하여 시뮬레이션된 장치 애플리케이션을 실행합니다.

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

허브에서 디바이스로부터 reported 속성을 받았는지 확인하려면 다음 CLI 명령을 사용합니다.

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

명령의 출력에서 reported 속성 섹션의 **devicelaststarted** 속성을 볼 수 있습니다. 이 속성은 시뮬레이션된 디바이스를 마지막으로 시작한 날짜와 시간을 표시합니다.

![reported 속성 보기](media/tutorial-connectivity/reported-properties.png)

허브에서 desired 속성 값을 디바이스로 보낼 수 있는지 확인하려면 다음 CLI 명령을 사용합니다.

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

시뮬레이션된 디바이스에서 허브로부터 desired 속성 업데이트를 받으면 메시지가 출력됩니다.

![desired 속성 받기](media/tutorial-connectivity/desired-properties.png)

desired 속성 변경 내용을 받는 것 외에도, 시뮬레이션된 디바이스에서는 시작할 때 desired 속성을 자동으로 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 IoT Hub가 필요하지 않으면 포털에서 IoT Hub와 리소스 그룹을 삭제합니다. 이렇게 하려면 IoT 허브가 포함된 **tutorials-iot-hub-rg** 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스 키 확인, 디바이스-클라우드 연결 확인, 클라우드-디바이스 연결 확인 및 디바이스 쌍 동기화 확인을 수행하는 방법을 살펴보았습니다. IoT 허브를 모니터링하는 방법에 대한 자세한 내용은 IoT Hub 모니터링에 대한 방법 문서를 참조하세요.

> [!div class="nextstepaction"]
> [진단 및 모니터링](iot-hub-monitor-resource-health.md)
