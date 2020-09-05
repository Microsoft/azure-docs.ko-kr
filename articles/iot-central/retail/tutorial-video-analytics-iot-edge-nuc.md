---
title: 자습서 - Azure IoT Central에서 비디오 분석 IoT Edge 인스턴스 만들기(Intel NUC)
description: 이 자습서에서는 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿에 사용할 비디오 분석 IoT Edge 인스턴스를 만드는 방법을 보여 줍니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 4ecce689e287673a3b08f8f90f87c28e021106d6
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037938"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>자습서: 비디오 분석용 IoT Edge 인스턴스 만들기(Intel NUC)

Azure IoT Edge는 아래 항목을 배포하고 실행하여 클라우드 인텔리전스를 로컬로 제공하는 완전 관리형 서비스입니다.

* 사용자 지정 논리
* Azure 서비스
* 인공 지능

IoT Edge에서 이러한 서비스는 플랫폼 간 IoT 디바이스에서 직접 실행되므로, IoT 솔루션을 클라우드 또는 오프라인에서 안전하게 대규모로 실행할 수 있습니다.

이 자습서에서는 Intel NUC 디바이스에 IoT Edge 런타임을 설치하고 구성하는 방법을 보여 줍니다.

이 자습서에서는 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * IoT Edge 업데이트 및 구성
> * IoT Edge 게이트웨이 설정
> * Intel NUC 디바이스에 로컬 ONVIF 호환 카메라 연결

## <a name="prerequisites"></a>필수 구성 요소

* 시작하기 전에, 이전 [Azure IoT Central에서 라이브 비디오 분석 애플리케이션 만들기](./tutorial-video-analytics-create-app.md) 자습서를 먼저 완료해야 합니다.
* Linux를 실행하는 Intel NUC와 같은 디바이스로, Docker 컨테이너를 실행할 수 있고, 비디오 분석을 실행하는 데 충분한 처리 능력이 있습니다.
* [IoT Edge 런타임이 설치](../../iot-edge/how-to-install-iot-edge-linux.md)되어 있으며 디바이스에서 실행됩니다.
* Windows 머신에서 IoT Edge 디바이스에 연결할 수 있으려면 [PuTTY SSH 클라이언트](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 또는 동등한 유틸리티가 필요합니다.
* 또한 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 [Azure 가입 페이지](https://aka.ms/createazuresubscription)에서 무료로 만들 수 있습니다.

## <a name="configure-the-iot-edge-device"></a>IoT Edge 디바이스 구성

IoT Edge 런타임이 Intel NUC 머신에 설치되어 있지 않은 경우에는 [Debian 기반 Linux 시스템에서 Azure IoT Edge 런타임 설치](../../iot-edge/how-to-install-iot-edge-linux.md) 지침을 참조하세요.

IoT Edge 런타임을 업데이트하려면 다음을 수행합니다.

1. PuTTY 유틸리티를 사용하여 IoT Edge 디바이스에 연결합니다.

1. 다음 명령을 실행하여 IoT Edge 런타임 버전을 업데이트하고 확인합니다. 이 문서를 작성한 시점에서 버전은 1.0.9입니다.

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. 다음 명령을 사용하여 배포에서 필요한 사용 권한으로 사용하는 폴더를 만듭니다.

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

*state.json* 구성 파일을 IoT Edge 디바이스의 */data/storage* 폴더에 추가하려면 다음을 수행합니다.

1. 텍스트 편집기를 사용하여 로컬 머신의 *lva-configuration* 폴더에서 *state.json* 파일을 엽니다.

1. 게이트웨이 디바이스를 설명하는 문자열 값을 사용하여 `system` 및 `iotCentral > properties` 자리 표시자를 업데이트합니다. 이러한 값은 나중에 IoT Central 애플리케이션 대시보드에서 볼 수 있습니다.

1. `iotCentral > appKeys` 자리 표시자를 이전 자습서의 *scratchpad.txt* 파일에서 기록해 둔 값으로 업데이트합니다. 변경 내용을 저장합니다.

1. 명령 프롬프트에서 PuTTY `scp` 유틸리티를 사용하여 방금 편집한 *state.json* 파일을 IoT Edge 디바이스에서 */data/storage* 폴더에 복사합니다. 이 예제에서는 `192.168.0.144`를 예제 IP 주소로 사용하여 IoT Edge 디바이스의 IP 주소로 바꿉니다.

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

IoT Edge를 구성하여 IoT Central 애플리케이션에 등록하고 연결합니다.

1. PuTTY 유틸리티를 사용하여 IoT Edge 디바이스에 연결합니다.

1. `nano`와 같은 텍스트 편집기를 사용하여 IoT Edge config.yaml 파일을 엽니다.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > YAML 파일은 들여쓰기에 탭을 사용할 수 없습니다. 대신 두 개의 공백을 사용합니다. 최상위 항목에는 선행 공백이 있을 수 없습니다.

1. `# Manual provisioning configuration`이 표시될 때까지 아래로 스크롤합니다. 다음 코드 조각과 같이 다음 세 줄을 주석으로 처리합니다.

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. `# DPS symmetric key provisioning configuration`이 표시될 때까지 아래로 스크롤합니다. 다음 코드 조각과 같이 다음 8개 줄의 주석 처리를 제거합니다.

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. `{scope_id}`를 이전 자습서의 *scratchpad.txt* 파일에서 기록해 둔 **ID 범위**로 바꿉니다.

1. `{registration_id}`를 이전 자습서에서 만든 게이트웨이 디바이스인 *lva-gateway-001*로 바꿉니다.

1. `{symmetric_key}`를 이전 자습서의 *scratchpad.txt* 파일에서 기록해 둔 파일에서 기록해 둔 **lva-gateway-001** 디바이스에 대한 **기본 키**로 바꿉니다.

1. 다음 명령을 실행하여 IoT Edge 디먼을 다시 시작합니다.

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge 모듈의 상태를 확인하려면 다음 명령을 실행합니다.

    ```bash
    iotedge list
    ```

    이전 명령의 출력에는 실행 중인 다섯 개의 모듈이 표시됩니다. IoT Central 애플리케이션에서 실행 중인 모듈의 상태를 볼 수도 있습니다.

    > [!TIP]
    > 이 명령을 다시 실행하여 상태를 확인할 수 있습니다. 모든 모듈의 실행이 시작될 때까지 기다려야 할 수 있습니다.

IoT Edge 모듈이 제대로 시작되지 않으면 [IoT Edge 디바이스 문제 해결](../../iot-edge/troubleshoot.md)을 참조하세요.

## <a name="collect-the-rstp-stream-from-your-camera"></a>카메라에서 RSTP 스트림 수집

IoT Edge 디바이스에 연결된 카메라의 RTSP 스트림 URL을 식별합니다. 예를 들면 다음과 같습니다.

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> VLC와 같은 미디어 플레이어를 사용하여 IoT Edge 컴퓨터에서 카메라 스트림을 보도록 시도합니다.

## <a name="next-steps"></a>다음 단계

이제 IoT Edge 런타임 및 LVA 모듈을 Intel NUC 게이트웨이 디바이스에 배포했습니다.

카메라를 관리하려면 다음 자습서를 진행하세요.

> [!div class="nextstepaction"]
> [비디오 분석 - 개체 및 동작 감지 애플리케이션 모니터링 및 관리](./tutorial-video-analytics-manage.md)
