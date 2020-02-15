---
title: Azure IoT Central에 Azure IoT Edge 디바이스 추가 | Microsoft Docs
description: 운영자가 Azure IoT Edge 디바이스를 Azure IoT Central 애플리케이션에 추가합니다.
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: bf74784998de6bbad6310c48c24d6353512bff13
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026396"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>자습서: Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 추가

이 자습서에서는 Azure IoT Edge 디바이스를 구성하고 Azure IoT Central 애플리케이션에 추가하는 방법을 보여 줍니다. 자습서에서 Azure Marketplace의 IoT Edge 사용 Linux VM(가상 머신)을 사용하여 IoT Edge 디바이스를 시뮬레이션합니다. IoT Edge 디바이스는 시뮬레이션된 환경 원격 분석을 생성하는 모듈을 사용합니다. 원격 분석은 IoT Central 애플리케이션의 대시보드에서 볼 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * IoT Edge 디바이스에 대한 디바이스 템플릿 만들기
> * IoT Central에서 IoT Edge 디바이스 만들기
> * Linux VM에 시뮬레이션된 IoT Edge 디바이스 배포

## <a name="prerequisites"></a>사전 요구 사항

[Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md) 빠른 시작을 완료하여 **사용자 지정 앱 > 사용자 지정 애플리케이션** 템플릿을 사용하여 IoT Central 애플리케이션을 만듭니다.

이 자습서의 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

GitHub에서 IoT Edge 매니페스트 파일을 다운로드합니다. 다음 링크를 마우스 오른쪽 단추로 클릭하고 **다른 이름으로 링크 저장**을 선택합니다. [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>디바이스 템플릿 만들기

이 섹션에서는 IoT Central 애플리케이션에 연결되는 IoT Edge 디바이스에 대한 디바이스 템플릿을 만듭니다. IoT Edge 매니페스트를 가져와서 시작한 다음, 원격 분석 정의 및 보기를 추가하도록 템플릿을 수정합니다.

### <a name="import-manifest-to-create-template"></a>템플릿을 만드는 매니페스트 가져오기

IoT Edge 매니페스트에서 디바이스 템플릿을 만들려면 다음을 수행합니다.

1. IoT Central 애플리케이션에서 **디바이스 템플릿**으로 이동하고 **+ 새로 만들기**를 선택합니다.

1. **템플릿 유형 선택** 페이지에서 **Azure IoT Edge** 타일을 선택합니다. 그런 다음, **Next: 사용자 지정**을 선택합니다.

1. **Azure IoT Edge 배포 매니페스트 업로드** 페이지에서 **찾아보기**를 선택하여 이전에 다운로드한 **EnvironmentalSensorManifest.json**을 업로드합니다. 그런 다음, **Next: 검토**를 클릭합니다.

1. **검토** 페이지에서 **만들기**를 선택합니다.

1. 템플릿이 만들어지면 이름을 *환경 센서 에지 디바이스*로 변경합니다.

1. **SimulatedTemperatureSensor** 모듈에서 **관리** 인터페이스를 선택하여 매니페스트에 정의된 두 가지 속성을 확인합니다.

![IoT Edge 매니페스트에서 만든 디바이스 템플릿](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>매니페스트에 원격 분석 추가

IoT Edge 매니페스트는 모듈에서 보내는 원격 분석을 정의하지 않습니다. 원격 분석 정의를 디바이스 템플릿에 추가해야 합니다. **SimulatedTemperatureSensor** 모듈은 다음 JSON과 비슷한 원격 분석 메시지를 보냅니다.

```json
{
    "machine": {
        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

원격 분석 정의를 디바이스 템플릿에 추가하려면 다음을 수행합니다.

1. **환경 센서 에지 디바이스** 템플릿에서 **관리** 인터페이스를 선택합니다.

1. **+ 기능 추가**를 선택합니다. **표시 이름**으로 *machine*을 입력하고, **기능 유형**이 **원격 분석**인지 확인합니다.

1. 스키마 유형으로 **Object**를 선택한 다음, **정의**를 선택합니다. 개체 정의 페이지에서 *온도* 및 *압력*을 **Double** 형식의 특성으로 추가한 다음, **적용**을 선택합니다.

1. **+ 기능 추가**를 선택합니다. **표시 이름**으로 *ambient*를 입력하고, **기능 유형**이 **원격 분석**인지 확인합니다.

1. 스키마 유형으로 **Object**를 선택한 다음, **정의**를 선택합니다. 개체 정의 페이지에서 *온도* 및 *습도*를 **Double** 형식의 특성으로 추가한 다음, **적용**을 선택합니다.

1. **+ 기능 추가**를 선택합니다. **표시 이름**으로 *timeCreated*를 입력하고, **기능 유형**이 **원격 분석**인지 확인합니다.

1. 스키마 유형으로 **DateTime**을 선택합니다.

1. **저장**을 선택하여 템플릿을 업데이트합니다.

이제 **관리** 인터페이스에는 **machine**, **ambient** 및 **timeCreated** 원격 분석 유형이 포함되어 있습니다.

![machine 및 ambient 원격 분석 유형을 사용하는 인터페이스](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>템플릿에 보기 추가

디바이스 템플릿에는 운영자가 IoT Edge 디바이스에서 원격 분석을 볼 수 있는 보기가 아직 없습니다. 디바이스 템플릿에 보기를 추가하려면 다음을 수행합니다.

1. **환경 센서 에지 디바이스** 템플릿에서 **보기**를 선택합니다.

1. **새 보기를 추가하려면 선택** 페이지에서 **디바이스를 시각화하는 중** 타일을 선택합니다.

1. 보기 이름을 *IoT Edge 디바이스 원격 분석 보기*로 변경합니다.

1. **ambient** 및 **machine** 원격 분석 유형을 선택합니다. 그런 다음, **타일 추가**를 선택합니다.

1. **저장**을 선택하여 **IoT Edge 디바이스 원격 분석 보기** 보기를 저장합니다.

![원격 분석 보기를 사용하는 디바이스 템플릿](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>템플릿 게시

**환경 센서 에지 디바이스** 템플릿을 사용하는 디바이스를 추가하려면 먼저 해당 템플릿을 게시해야 합니다.

**환경 센서 에지 디바이스** 템플릿으로 이동하고, **게시**를 선택합니다. 그런 다음, **게시**를 선택하여 템플릿을 게시합니다.

![디바이스 템플릿 게시](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>IoT Edge 디바이스 추가

이제 **환경 센서 에지 디바이스** 템플릿이 게시되었으므로 디바이스를 IoT Central 애플리케이션에 추가할 수 있습니다.

1. IoT Central 애플리케이션에서 **디바이스** 페이지로 이동하고, 사용 가능한 템플릿 목록에서 **환경 센서 에지 디바이스**를 선택합니다.

1. **+** 를 선택하여 템플릿에서 새 디바이스를 추가합니다. **새 디바이스 만들기** 페이지에서 **만들기**를 선택합니다.

이제 **등록됨**상태의 새 디바이스가 있습니다.

![디바이스 템플릿 게시](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>디바이스 자격 증명 가져오기

이 자습서의 뒷부분에서 IoT Edge 디바이스를 배포하는 경우 디바이스에서 IoT Central 애플리케이션에 연결할 수 있도록 하는 자격 증명이 필요합니다. 디바이스 자격 증명을 가져오려면 다음을 수행합니다.

1. **디바이스** 페이지에서 만든 디바이스를 선택합니다.

1. **연결**을 선택합니다.

1. **디바이스 연결** 페이지에서 **ID 범위**, **디바이스 ID** 및 **기본 키**를 적어 둡니다. 이러한 값은 나중에 사용합니다.

1. **닫기**를 선택합니다.

이제 IoT Edge 디바이스를 연결할 수 있도록 IoT Central 애플리케이션을 구성하는 작업이 완료되었습니다.

## <a name="deploy-an-iot-edge-device"></a>IoT Edge 디바이스 배포

이 자습서에서는 Azure에서 만든 Azure IoT Edge 사용 Linux VM을 사용하여 IoT Edge 디바이스를 시뮬레이션합니다. IoT Edge 사용 VM을 만들려면 다음을 수행합니다.

1. Azure Marketplace에서 [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)로 이동합니다. 그런 다음, **지금 가져오기**를 선택합니다.

1. **Azure에서 이 앱 만들기** 페이지에서 **계속**을 선택합니다. 이 링크를 통해 Azure 구독에 로그인해야 하는 Azure Portal로 이동할 수 있습니다.

1. Azure Portal의 **Azure IoT Edge on Ubuntu** 페이지에서 **만들기**를 선택합니다.

1. **가상 머신 만들기 > 기본 사항** 페이지에서 다음을 수행합니다.

    - Azure 구독을 선택합니다.
    - **iot-edge-devices**라는 새 리소스 그룹을 만듭니다.
    - **iotedgevm**이라는 가상 머신 이름을 사용합니다.
    - 가장 가까운 지역을 선택합니다.
    - 인증 유형을 **암호**로 설정합니다.
    - 사용자 이름 및 암호를 선택합니다.
    - 다른 옵션은 해당 기본값으로 그대로 둘 수 있습니다.
    - **검토 + 만들기**를 선택합니다.

1. 유효성 검사가 완료되면 **만들기**를 선택합니다.

몇 분 후에 배포가 완료되면 **리소스로 이동**을 선택합니다.

### <a name="provision-vm-as-an-iot-edge-device"></a>VM을 IoT Edge 디바이스로 프로비저닝 

VM을 IoT Edge 디바이스로 프로비저닝하려면 다음을 수행합니다.

1. **지원 + 문제 해결** 섹션에서 **직렬 콘솔**을 선택합니다.

1. **Enter** 키를 눌러 `login:` 프롬프트를 표시합니다. 로그인할 사용자 이름과 암호를 입력합니다.

1. 다음 명령을 실행하여 IoT Edge 런타임 버전을 확인합니다. 이 문서를 작성한 시점에서 버전은 1.0.8입니다.

    ```bash
    sudo iotedge --version
    ```

1. `nano` 편집기를 사용하여 IoT Edge config.yaml 파일을 엽니다.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. `# Manual provisioning configuration`이 표시될 때까지 아래로 스크롤합니다. 다음 코드 조각과 같이 다음 세 줄을 주석으로 처리합니다.

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
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

1. `{scope_id}`를 이전에 적어 둔 **ID 범위**로 바꿉니다.

1. `{registration_id}`를 이전에 적어 둔 **디바이스 ID**로 바꿉니다.

1. `{symmetric_key}`를 이전에 적어 둔 **기본 키**로 바꿉니다.

1. 변경 내용을 저장하고(**Ctrl-O**), `nano` 편집기를 종료합니다(**Ctrl-X**).

1. 다음 명령을 실행하여 IoT Edge 디먼을 다시 시작합니다.

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge 모듈의 상태를 확인하려면 다음 명령을 실행합니다.

    ```bash
    iotedge list
    ```

    출력은 다음과 같이 표시됩니다.

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>원격 분석 보기

이제 시뮬레이션된 IoT Edge 디바이스가 VM에서 실행되고 있습니다. IoT Central 애플리케이션의 **디바이스** 페이지에서 디바이스 상태는 이제 **프로비저닝됨**입니다.

![프로비저닝된 디바이스](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

**IoT Edge 디바이스 원격 분석 보기** 페이지에서 원격 분석을 볼 수 있습니다.

![디바이스 원격 분석](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

**모듈** 페이지에서 IoT Edge 모듈의 상태가 표시됩니다.

![디바이스 원격 분석](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>다음 단계

이제 IoT Central에서 IoT Edge 디바이스를 사용하고 관리하는 방법을 알아보았으므로 제안되는 다음 단계는 다음과 같습니다.

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [투명한 게이트웨이 구성](../../iot-edge/how-to-create-transparent-gateway.md)
