---
title: 빠른 시작 - Azure IoT Hub 디바이스 스트림을 통한 SSH 및 RDP의 Node.js 빠른 시작
description: 이 빠른 시작에서는 IoT Hub 디바이스 스트림을 통해 SSH 및 RDP 시나리오를 활성화하는 프록시 역할을 하는 Node.js 샘플 애플리케이션을 실행합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c379242187624f3d1254871a86317e3ad8daf8fc
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107887723"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>빠른 시작: Node.js 프록시 애플리케이션을 사용하여 IoT Hub 디바이스 스트림을 통해 SSH 및 RDP 사용(미리 보기)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

이 빠른 시작에서는 디바이스 스트림을 통해 디바이스로 전송되는 SSH(Secure Shell) 및 RDP(Remote Desktop Protocol) 트래픽을 사용하도록 설정합니다. Azure IoT Hub 디바이스 스트림은 서비스 및 디바이스 애플리케이션이 안전하고 방화벽 친화적인 방식으로 통신할 수 있도록 합니다. 이 빠른 시작에서는 서비스쪽에서 실행 중인 Node.js 프록시 애플리케이션을 실행하는 방법을 설명합니다. 공개 미리 보기 동안 Node.js SDK는 서비스 쪽의 디바이스 스트림만 지원합니다. 결과적으로 이 빠른 시작에서는 서비스-로컬 프록시 애플리케이션만 실행하는 지침에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [C 프록시 애플리케이션을 사용하여 IoT Hub 디바이스 스트림을 통해 SSH 및 RDP 사용](./quickstart-device-streams-proxy-c.md) 또는 [C# 프록시 애플리케이션을 사용하여 IoT Hub 디바이스 스트림을 통해 SSH 및 RDP 사용](./quickstart-device-streams-proxy-csharp.md) 완료.

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10 이상](https://nodejs.org).

    다음 명령을 사용하여 개발 머신에서 Node.js의 현재 버전을 확인할 수 있습니다.

    ```cmd/sh
    node --version
    ```

* [샘플 Node.js 프로젝트](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Microsoft Azure IoT Hub는 현재 디바이스 스트림을 [미리 보기 기능](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)으로 지원합니다.

> [!IMPORTANT]
> 디바이스 스트림의 미리 보기는 현재 다음 지역에서 만든 IoT Hub에 대해서만 지원됩니다.
>
> * 미국 중부
> * 미국 중부 EUAP
> * 북유럽
> * 동남아시아

### <a name="add-azure-iot-extension"></a>Azure IoT 확장 추가

다음 명령을 실행하여 Azure CLI용 Azure IoT 확장을 Cloud Shell 인스턴스에 추가합니다. IoT 확장은 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service) 관련 명령을 Azure CLI에 추가합니다.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이전 [빠른 시작: 원격 분석을 디바이스에서 IoT 허브로 전송](quickstart-send-telemetry-node.md)을 완료한 경우 이 단계를 건너뛸 수 있습니다.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>디바이스 등록

[빠른 시작: 디바이스에서 IoT 허브로 원격 분석 보내기](quickstart-send-telemetry-node.md)를 완료한 경우 이 단계를 건너뛸 수 있습니다.

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 섹션에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. 디바이스 ID를 만들려면 Cloud Shell에서 다음 명령을 실행합니다.

   > [!NOTE]
   > * *YourIoTHubName* 자리 표시자를 IoT 허브에서 선택한 이름으로 바꿉니다.
   > * 등록하려는 디바이스의 이름에는 표시된 대로 *MyDevice* 를 사용하는 것이 좋습니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서 해당 이름을 사용하고, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후에 실행하세요.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. 백 엔드 애플리케이션에서 IoT Hub에 연결하여 메시지를 검색할 수 있게 하려면 *서비스 연결 문자열* 도 필요합니다. 다음 명령은 IoT Hub에 대한 문자열을 검색합니다.

   > [!NOTE]
   > *YourIoTHubName* 자리 표시자를 IoT 허브에서 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --hub-name {YourIoTHubName} --output table
    ```

   나중에 이 빠른 시작에서 사용할 수 있도록 반환된 서비스 연결 문자열을 적어 두세요. 다음 예제와 유사합니다.

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>디바이스 스트림을 통해 디바이스에 대한 SSH

이 섹션에서는 SSH 트래픽을 터널링하는 엔드투엔드 스트림을 설정합니다.

### <a name="run-the-device-local-proxy-application"></a>디바이스-로컬 프록시 애플리케이션 실행

앞에서 설명한 대로 IoT Hub Node.js SDK는 서비스 쪽의 디바이스 스트림만 지원합니다. 디바이스-로컬 애플리케이션의 경우 다음 빠른 시작 중 하나에서 사용할 수 있는 디바이스 프록시 애플리케이션을 사용합니다.

   * [C 프록시 애플리케이션을 사용하여 IoT Hub 디바이스 스트림을 통해 SSH 및 RDP 사용](./quickstart-device-streams-proxy-c.md)
   * [C# 프록시 애플리케이션을 사용하여 IoT Hub 디바이스 스트림을 통해 SSH 및 RDP 사용](./quickstart-device-streams-proxy-csharp.md) 

다음 단계로 진행하기 전에 디바이스-로컬 프록시 애플리케이션이 실행되고 있는지 확인합니다. 설정 개요는 [로컬 프록시 샘플](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)을 참조하세요.

### <a name="run-the-service-local-proxy-application"></a>서비스-로컬 프록시 애플리케이션 실행

이 문서에서는 먼저 SSH 설정(22 포트 사용)을 설명한 다음, RDP 설정(3389 포트 사용)을 수정하는 방법을 설명합니다. 디바이스 스트림은 애플리케이션 및 프로토콜에 구속받지 않으므로 다른 유형의 클라이언트-서버 트래픽을 수용하도록 동일한 샘플을 수정할 수 있습니다(일반적으로 통신 포트를 수정함).

디바이스-로컬 프록시 애플리케이션이 실행된 상태에서 로컬 터미널 창에서 다음을 수행하여 Node.js로 작성된 서비스-로컬 프록시 애플리케이션을 실행합니다.

1. 환경 변수의 경우 서비스 자격 증명, SSH 디먼이 실행되는 대상 디바이스 ID 및 디바이스에서 실행되는 프록시에 대한 포트 번호를 제공합니다.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   ServiceConnectionString 자리 표시자를 서비스 연결 문자열과 일치하도록 변경하고 다른 이름을 지정한 경우 디바이스 ID와 일치하도록 **MyDevice** 를 변경합니다.

1. 압축을 푼 프로젝트 폴더에서 `Quickstarts/device-streams-service` 디렉터리로 이동합니다. 다음 코드를 사용하여 서비스-로컬 프록시 애플리케이션을 실행합니다.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>디바이스 스트림을 통해 디바이스에 대한 SSH

Linux에서 터미널의 `ssh $USER@localhost -p 2222`를 사용하여 SSH를 실행합니다. Windows에서 즐겨찾는 SSH 클라이언트(예: PuTTY)를 사용합니다.

SSH 세션이 설정된 후 서비스-로컬의 콘솔 출력(서비스-로컬 프록시 애플리케이션이 2222 포트에서 수신 대기함):

![SSH 터미널 출력](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

SSH 클라이언트 애플리케이션의 콘솔 출력(SSH 클라이언트가 서비스-로컬 프록시 애플리케이션에서 수신 대기 중인 포트 22에 연결하여 SSH 디먼과 통신함):

![SSH 클라이언트 출력](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>디바이스 스트림을 통해 디바이스에 대한 RDP

이제 RDP 클라이언트 애플리케이션을 사용하고 2222 포트(이전에 선택한 임의의 사용 가능한 포트)에서 서비스 프록시에 연결합니다.

> [!NOTE]
> 디바이스 프록시가 RDP에 대해 올바르게 구성되고 RDP 포트 3389로 구성되었는지 확인합니다.

![RDP 클라이언트에서 서비스-로컬 프록시 애플리케이션에 연결](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 허브를 설정하고, 디바이스를 등록하고, 서비스 프록시 애플리케이션을 배포하여 IoT 디바이스에서 RDP 및 SSH를 사용하도록 설정했습니다. RDP 및 SSH 트래픽은 IoT 허브에서 디바이스 스트림을 통해 터널링됩니다. 이 프로세스에서는 디바이스에 직접 연결할 필요가 없습니다.

디바이스 스트림에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [디바이스 스트림 개요](./iot-hub-device-streams-overview.md)
