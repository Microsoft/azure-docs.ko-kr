---
title: 부터 Azure에 대 한 OPC 쌍 모듈을 배포 하는 방법 | Microsoft Docs
description: 부터 OPC 쌍을 배포 하는 방법입니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f470beb79e69b5a4a3febeb6a433c48490b96cf7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451075"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>OPC 쌍 모듈과부터 종속성 배포

OPC 쌍 모듈이 IoT Edge에서 실행 하 고 여러 OPC 장치 쌍을에 지 서비스와 레지스트리 서비스를 제공 합니다. 

여러 가지 방법으로 모듈을 배포 하 여 [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 그중에서 게이트웨이

- [Azure portal의 IoT Edge에 대 한 블레이드에서 배포](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [AZ CLI를 사용 하 여 배포](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> 배포 세부 정보 및 지침에 대 한 자세한 내용은 참조는 GitHub [리포지토리](https://github.com/Azure/azure-iiot-components)합니다.

## <a name="deployment-manifest"></a>배포 매니페스트

모든 모듈은 배포 매니페스트를 사용 하 여 배포 됩니다.  둘 다를 배포 하는 예제 매니페스트 [OPC 게시자](https://github.com/Azure/iot-edge-opc-publisher) 하 고 [OPC 쌍](https://github.com/Azure/azure-iiot-opc-twin-module) 아래에 표시 됩니다.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
            }
          }
        },
        "modules": {
          "opctwin": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
              "createOptions": "{\"HostConfig\": { \"NetworkMode\": \"host\", \"CapAdd\": [\"NET_ADMIN\"] } }"
            }
          },
          "opcpublisher": {
            "version": "2.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
              "createOptions": "{\"Hostname\": \"publisher\", \"Cmd\": [ \"publisher\", \"--pf=./pn.json\", \"--di=60\", \"--to\", \"--aa\", \"--si=0\", \"--ms=0\" ], \"ExposedPorts\": { \"62222/tcp\": {} }, \"HostConfig\": { \"PortBindings\": { \"62222/tcp\": [{ \"HostPort\": \"62222\" }] } } }"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
          "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Azure portal에서 배포

Azure IoT Edge 게이트웨이 장치에 모듈을 배포 하는 가장 쉬운 방법은 Azure portal을 통해 됩니다.  

### <a name="prerequisites"></a>필수 조건

1. OPC 쌍 배포할 [종속성](howto-opc-twin-deploy-dependencies.md) 결과 가져올 `.env` 파일입니다. 배포 참고 `hub name` 의 합니다 `PCS_IOTHUBREACT_HUB_NAME` 결과 변수 `.env` 파일입니다.

2. 등록 및 시작을 [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) 하거나 [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge 게이트웨이 및 참고 해당 `device id`합니다.

### <a name="deploy-to-an-edge-device"></a>Edge 장치에 배포

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 IoT Hub로 이동합니다.

2. 선택 **IoT Edge** 왼쪽 메뉴에서.

3. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.

4. **모듈 설정**을 선택합니다.

5. 에 **배포 모듈** 선택 페이지의 섹션 **추가** 고 **IoT Edge 모듈입니다.**

6. 에 **IoT Edge 사용자 지정 모듈** 대화 상자 사용 `opctwin` 모듈에 대 한 이름으로 컨테이너를 지정 합니다 *URI 이미지* 으로

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   로 *만들기 옵션* 다음 JSON을 사용 합니다.

   ```json
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
   ```

   필요한 경우 선택적 필드를 작성합니다. 컨테이너 생성 옵션, 다시 시작 정책 및 원하는 상태에 대한 자세한 내용은 [EdgeAgent desired 속성](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties)을 참조하세요. 모듈 쌍에 대한 자세한 내용은 [desired 속성 정의 또는 업데이트](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)을 참조하세요.

7. 선택 **저장할** 고 단계를 반복 **5**합니다.  

8. IoT Edge 사용자 지정 모듈 대화 상자에서 사용 하 여 `opcpublisher` 모듈 및 컨테이너 이름으로 *URI 이미지* 으로 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   로 *만들기 옵션* 다음 JSON을 사용 합니다.

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. 선택 **저장할** 차례로 **다음** 경로 섹션으로 계속 합니다.

10. 경로 탭에서 다음 붙여넣기 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    선택한 **다음**

11. 배포 정보를 검토 하 고 매니페스트 키를 누릅니다.  위의 배포 매니페스트 처럼 보여야 합니다.  **제출**을 선택합니다.

12. 디바이스에 모듈을 배포하면 포털의 **디바이스 세부 정보** 페이지에서 모두 볼 수 있습니다. 이 페이지에서는 배포 상태 및 종료 코드와 같은 유용한 정보뿐만 아니라 배포된 각 모듈의 이름을 표시합니다.

## <a name="deploying-using-azure-cli"></a>Azure CLI를 사용 하 여 배포

### <a name="prerequisites"></a>필수 조건

1. 최신 버전을 설치 합니다 [Azure 명령줄 인터페이스 (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 에서 [여기](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)합니다.

### <a name="quickstart"></a>빠른 시작

1. 위의 배포 매니페스트를 저장 한 `deployment.json` 파일입니다.  

2. 다음 명령을 사용하여 IoT Edge 디바이스에 구성을 적용합니다.

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   `device id` 매개 변수는 대/소문자 구분 합니다. 콘텐츠 매개 변수는 저장한 배포 매니페스트 파일을 가리킵니다. 
    ![az IoT Edge 집합 모듈 출력](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. 디바이스에 모듈을 배포하면 다음 명령을 사용하여 모두 볼 수 있습니다.

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   장치 ID 매개 변수는 대/소문자 구분 합니다. ![az iot hub module-identity list output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="run-and-debug-locally"></a>실행 및 로컬 디버그

문제 해결 및 디버깅은 사용 하 여 로컬로 Edge 모듈을 실행 하는 데 유용 합니다 [IoT Edge 개발 시뮬레이터](https://github.com/Azure/iotedgehubdev)합니다.  만들기, 개발, 테스트, 실행 및 Azure IoT Edge 모듈 및 프로덕션 환경에서 사용 되는 동일한 비트/코드를 사용 하는 솔루션 디버깅에 대 한 시뮬레이터를 사용 하 여 로컬 개발 환경을 제공 합니다.

### <a name="prerequisites"></a>필수 조건

1. OPC 쌍 배포할 [종속성](howto-opc-twin-deploy-dependencies.md)합니다.

2. 설치 [Docker CE (18.02.0+)](https://www.docker.com/community-edition) 온 [Windows](https://docs.docker.com/docker-for-windows/install/), [macOS](https://docs.docker.com/docker-for-mac/install/) 하거나 [Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)합니다.

3. 설치할 [Docker Compose (1.20.0+)](https://docs.docker.com/compose/install/#install-compose) (에 필요 **Linux**합니다. Compose 설치 된 Windows/macOS Docker CE에에서 이미 포함 되어)

4. 설치 [Python (2.7 / 3.5+) 및 Pip](https://www.python.org/)

5. Iotedgehubdev 아래 터미널에서 명령 실행 하 여 설치

   ```bash
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> 설치 `iotedgehubdev` 하 **루트** Linux/macOS에서 (*사용 하지 않는 '-사용자 ' 옵션 'pip 설치' 명령에서*).
> 동일한 포트 필요 하므로 iotedgehubdev 사용 하 여 동일한 컴퓨터에서 실행 중인 Azure IoT Edge 런타임이 없습니다 인지 확인 합니다.

### <a name="quickstart"></a>빠른 시작

1. 지침에 따라 [Azure portal에서 Edge 장치 만들기](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)합니다.  Edge 장치 연결 문자열을 복사 합니다.

2. Edge 연결 문자열을 사용 하 여 시뮬레이터를 설정 합니다.

    ```bash
    iotedgehubdev setup -c <edge-device-connection-string>
    ```

3. 매니페스트를 위의 복사는 `deployment.json` 동일한 폴더에 파일입니다.  배포를 사용 하 여 시뮬레이터 시작

    ```bash
    iotedgehubdev start -d deployment.json
    ```

4. 사용 하 여 시뮬레이터를 중지 합니다.

   ```bash
   iotedgehubdev stop
   ```

## <a name="next-steps"></a>다음 단계

이제부터 OPC 쌍을 배포 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [기존 프로젝트에 OPC 쌍 배포](howto-opc-twin-deploy-existing.md)