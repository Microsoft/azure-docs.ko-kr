---
title: Visual Studio에서 모듈 개발 및 디버그-Azure IoT Edge
description: Visual Studio와 Azure IoT 도구를 사용 하 여 C 또는 C# IoT Edge 모듈을 개발 하 고 IoT Hub에서 iot 장치로 푸시합니다.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 8c174c5c201aecd83fb343a217c7944d503616c3
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509295"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio 2019을 사용 하 여 Azure IoT Edge에 대 한 모듈 개발 및 디버그

비즈니스 논리를 Azure IoT Edge용 모듈로 전환할 수 있습니다. 이 문서에서는 Visual Studio 2019를 기본 도구로 사용 하 여 모듈을 개발 하 고 디버그 하는 방법을 보여 줍니다.

Visual Studio 용 Azure IoT Edge 도구는 다음과 같은 이점을 제공합니다.

- 로컬 개발 컴퓨터에서 Azure IoT Edge 솔루션 및 모듈을 생성, 편집, 빌드, 실행 및 디버그합니다.
- Azure IoT Hub를 통해 Azure IoT Edge 디바이스에 Azure IoT Edge 솔루션을 배포합니다.
- Visual Studio 개발의 모든 혜택을 포함 C# 하는 동시에 Azure IoT 모듈을 C로 코딩 하세요.
- Azure IoT Edge 디바이스 및 모듈을 UI로 관리합니다.

이 문서에서는 Visual Studio 2019 용 Azure IoT Edge 도구를 사용 하 여 IoT Edge 모듈을 개발 하는 방법을 보여 줍니다. Azure IoT Edge 디바이스에 프로젝트를 배포하는 방법도 알아봅니다. 현재 Visual Studio 2019는 C 및 C#로 작성 된 모듈을 지원 합니다. 지원 되는 장치 아키텍처는 Windows X64 및 Linux X64 또는 ARM32입니다. 지원 되는 운영 체제, 언어 및 아키텍처에 대 한 자세한 내용은 [언어 및 아키텍처 지원](module-development.md#language-and-architecture-support)을 참조 하세요.
  
## <a name="prerequisites"></a>필수 조건

이 문서에서는 Windows를 실행하는 컴퓨터 또는 가상 머신을 개발 머신으로 사용한다고 가정합니다. Windows 컴퓨터에서는 Windows 또는 Linux 모듈을 개발할 수 있습니다. Windows 모듈을 개발 하려면 1809/build 17763 이상 버전을 실행 하는 Windows 컴퓨터를 사용 합니다. Linux 모듈을 개발 하려면 [Docker Desktop에 대 한 요구 사항을](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)충족 하는 Windows 컴퓨터를 사용 합니다.

이 문서에서는 Visual Studio 2019을 주 개발 도구로 사용 하기 때문에 Visual Studio를 설치 합니다. Visual Studio 2019 설치에서 워크 로드 **를 사용 C++**  하 여 **Azure 개발** 및 데스크톱 개발을 포함 해야 합니다. [Visual Studio 2019를 수정](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) 하 여 필요한 작업을 추가할 수 있습니다.

Visual Studio 2019이 준비 되 면 다음 도구 및 구성 요소도 필요 합니다.

- Visual studio marketplace에서 [Azure IoT Edge 도구](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 를 다운로드 하 여 설치 하 고 visual studio 2019에서 IoT Edge 프로젝트를 만듭니다.

> [!TIP]
> Visual Studio 2017을 사용 하는 경우 Visual Studio marketplace에서 VS 2017 용 [Azure IoT Edge 도구](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) 를 다운로드 하 여 설치 하세요.

- [Docker Community Edition](https://docs.docker.com/install/)을 개발 머신에 다운로드 및 설치하여 모듈 이미지를 빌드하고 실행합니다. Linux 컨테이너 모드 또는 Windows 컨테이너 모드에서 실행할 Docker CE를 설정해야 합니다.

- [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)을 설치하여 IoT Edge 솔루션을 디버그, 실행 및 테스트할 로컬 개발 환경을 설정합니다. [Python (2.7/3.6 +) 및 Pip](https://www.python.org/) 를 설치한 다음 터미널에서 다음 명령을 실행 하 여 **iotedgehubdev** 패키지를 설치 합니다. Azure IoT EdgeHub Dev Tool 버전이 0.3.0보다 높아야 합니다.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- 리포지토리를 복제 하 고 Vcpkg library manager를 설치한 다음 Windows 용 **azure-iot-c 패키지** 를 설치 합니다.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > 클라우드 레지스트리 대신 로컬 Docker 레지스트리를 프로토타입 및 테스트 목적으로 사용할 수 있습니다.

- 디바이스에서 모듈을 테스트하려면 하나 이상의 IoT Edge 디바이스가 있는 활성 IoT 허브가 필요합니다. 컴퓨터를 IoT Edge 디바이스로 사용하려면 [Linux](quickstart-linux.md) 또는 [Windows](quickstart.md)의 빠른 시작에서 단계를 수행합니다. 개발 머신에서 IoT Edge 디먼을 실행할 경우, Visual Studio에서 개발을 시작하기 전 EdgeHub 및 EdgeAgent를 중지해야 할 수도 있습니다.

### <a name="check-your-tools-version"></a>도구 버전 확인

1. **도구** 메뉴에서 **확장 및 업데이트**를 선택합니다. **설치됨 > 도구**를 펼치면 **Azure IoT Edge**와 **Visual Studio용 클라우드 탐색기**가 보입니다.

1. 설치된 버전을 적어둡니다. 이 버전을 Visual Studio Marketplace([클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))의 최신 버전과 비교할 수 있습니다.

1. Visual Studio Marketplace에서 제공되는 것보다 버전이 낮을 경우, 다음 섹션에 나오는 대로 Visual Studio에서 도구를 업데이트합니다.

### <a name="update-your-tools"></a>도구 업데이트

1. **확장 및 업데이트** 대화 상자에서 **업데이트 > Visual Studio Marketplace**를 펼치고, **Azure IoT Edge 도구** 또는 **Visual Studio용 클라우드 탐색기**를 선택하고 **업데이트**를 선택합니다.

1. 도구 업데이트를 다운로드한 후에 Visual Studio를 닫고 VSIX 설치 관리자를 사용하여 도구 업데이트를 트리거합니다.

1. 설치 관리자에서 **확인**을 선택하여 시작한 다음, **수정**을 선택하여 도구를 업데이트합니다.

1. 업데이트가 완료되면 **닫기**를 선택하고 Visual Studio를 다시 시작합니다.

### <a name="create-an-azure-iot-edge-project"></a>Azure IoT Edge 프로젝트 만들기

Visual Studio의 Azure IoT Edge 프로젝트 템플릿은 Azure IoT Hub의 Azure IoT Edge 디바이스에 배포할 수 있는 프로젝트를 만듭니다. 먼저 Azure IoT Edge 솔루션을 만든 다음 해당 솔루션의 첫 번째 모듈을 생성 합니다. 각 IoT Edge 솔루션에는 둘 이상의 모듈이 포함될 수 있습니다.

> [!TIP]
> Visual Studio에서 만든 IoT Edge 프로젝트 구조는 Visual Studio Code의 구조와는 다릅니다.

1. Visual Studio 새 프로젝트 대화 상자에서 **Azure IoT Edge** 프로젝트를 검색 하 고 선택 하 고 **다음**을 클릭 합니다. 프로젝트 구성 창에서 프로젝트의 이름을 입력 하 고 위치를 지정한 다음 **만들기**를 선택 합니다. 기본 프로젝트 이름은 **AzureIoTEdgeApp1**입니다.

   ![새 프로젝트 만들기](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. **IoT Edge 응용 프로그램 및 모듈 추가** 창에서  **C# 모듈** 또는 **C 모듈** 을 선택한 다음 모듈 이름 및 모듈 이미지 리포지토리를 지정 합니다. Visual Studio는 **localhost:5000/<모듈 이름\>** 으로 모듈 이름을 자동으로 채웁니다. 고유한 레지스트리 정보로 바꿉니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우 **localhost**를 사용해도 됩니다. Azure Container Registry를 사용하는 경우 레지스트리 설정의 로그인 서버를 사용합니다. 로그인 서버는  **_\<레지스트리 이름\>_ . azurecr.io**와 같습니다. 최종 결과가 **\<*레지스트리 이름*\>. azurecr.io/ _\<모듈 이름\>_** 와 유사 하 게 문자열의 **localhost: 5000** 부분만 바꿉니다. 기본 모듈 이름은 **IotEdgeModule1** 입니다.

   ![응용 프로그램 및 모듈 추가](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. **확인** 을 선택 하 여 C# 또는 C를 사용 하는 모듈을 사용 하 여 Azure IoT Edge 솔루션을 만듭니다.

이제 **AzureIoTEdgeApp1** 프로젝트 또는 **AzureIoTEdgeApp1** 프로젝트를 사용할 수 있으며 솔루션에 **IotEdgeModule1** 프로젝트도 있습니다. 각 **AzureIoTEdgeApp1** 프로젝트에는 IoT Edge 솔루션에 대해 빌드 및 배포 하려는 모듈을 정의 하 고 모듈 간의 경로도 정의 하는 `deployment.template.json` 파일이 있습니다. 기본 솔루션에는 **SimulatedTemperatureSensor** 모듈과 **IotEdgeModule1** 모듈이 있습니다. **SimulatedTemperatureSensor** 모듈은 시뮬레이션 된 데이터를 **IotEdgeModule1** 모듈에 생성 하는 반면 **IotEdgeModule1** 모듈의 기본 코드는 수신 된 메시지를 Azure IoT Hub에 직접 파이프 합니다.

시뮬레이션 된 온도 센서의 작동 방식을 확인 하려면 [SimulatedTemperatureSensor 소스 코드](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)를 확인 합니다.

**IotEdgeModule1** 프로젝트는 C# 모듈이 면 .net Core 2.1 콘솔 응용 프로그램입니다. 여기에는 IoT Edge 디바이스를 Windows 컨테이너나 Linux 컨테이너로 실행할 때 필요한 필수 Docker 파일이 포함되어 있습니다. `module.json` 파일은 모듈의 메타 데이터를 설명 합니다. Azure IoT 장치 SDK를 종속성으로 사용 하는 실제 모듈 코드는 `Program.cs` 또는 `main.c` 파일에 있습니다.

## <a name="develop-your-module"></a>모듈 개발

솔루션과 함께 제공 되는 기본 모듈 코드는 **IotEdgeModule1** > **Program.cs** (의 경우 C#) 또는 **main. c** (c)에 있습니다. 모듈 및 `deployment.template.json` 파일은 솔루션을 빌드하고 컨테이너 레지스트리에 푸시한 다음 장치에 배포 하 여 코드를 건드리지 않고도 테스트를 시작할 수 있도록 설정 됩니다. 모듈은 원본 (이 경우 데이터를 시뮬레이트하는 **SimulatedTemperatureSensor** 모듈)에서 입력을 가져와서 Azure IoT Hub로 파이프 하도록 빌드됩니다.

사용자 고유의 코드를 사용 하 여 모듈 템플릿을 사용자 지정할 준비가 되 면 [Azure IoT Hub sdk](../iot-hub/iot-hub-devguide-sdks.md) 를 사용 하 여 보안, 장치 관리 및 안정성과 같은 IoT 솔루션에 대 한 핵심 요구 사항을 해결 하는 모듈을 빌드 하세요.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>IoT Edge 디바이스 연결 문자열로 iotegehubdev 초기화

1. Visual Studio 클라우드 탐색기의 **기본 연결 문자열**에서 모든 IoT Edge 디바이스의 연결 문자열을 복사합니다. IoT Edge 디바이스의 아이콘은 non-Edge 디바이스의 아이콘과 다르므로 non-Edge 디바이스의 연결 문자열을 복사해서는 안 됩니다.

   ![Edge 디바이스 연결 문자열 복사](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. **도구** > **Azure IoT Edge 도구** > **설치 IoT Edge 시뮬레이터**로 이동 하 여 연결 문자열을 붙여넣고 **확인**을 클릭 합니다.

   ![Edge 장치 연결 문자열 설정 창 열기](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 첫 번째 단계에서 연결 문자열을 입력하고 **확인**을 선택합니다.

> [!NOTE]
> 모든 후속 Azure IoT Edge 솔루션에 결과가 자동으로 적용되기 때문에 이 단계들은 개발 컴퓨터에서 한 번만 수행해야 합니다. 다른 연결 스트링으로 변경해야 할 경우 이 과정을 다시 밟으면 됩니다.

## <a name="build-and-debug-single-module"></a>단일 모듈 빌드 및 디버그

여러 모듈이 포함된 전체 솔루션 내에서 실행하기 전데 모듈을 각각 테스트 및 디버그하는 것이 일반적입니다.

1. **IotEdgeModule1** 를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **시작 프로젝트로 설정** 을 선택 합니다.

   ![시작 프로젝트 설정](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. **F5** 키를 누르거나 아래 버튼을 클릭하여 모듈을 실행합니다. 처음에는 10&ndash;20초 정도 걸립니다.

   ![모듈 실행](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 모듈이 성공적으로 초기화된 경우 .NET Core 콘솔 앱이 시작된 것을 볼 수 있습니다.

   ![실행 중인 모듈](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. 에서 C#개발 하는 경우 **Program.cs**의 `PipeMessage()` 함수에서 중단점을 설정 합니다. C를 사용 하는 경우에는 **main. c**의 `InputQueue1Callback()` 함수에서 중단점을 설정 합니다. 그런 다음 **Git bash** 또는 **wsl bash** 셸에서 다음 명령을 실행 하 여 메시지를 전송 하 여 테스트할 수 있습니다. (PowerShell 또는 명령 프롬프트에서는 `curl` 명령을 실행할 수 없습니다.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![단일 모듈 디버그](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    중단점이 트리거됩니다. Visual Studio **로컬** 창에서 변수를 확인할 수 있습니다.

   > [!TIP]
   > `curl` 대신 [PostMan](https://www.getpostman.com/) 또는 다른 API 도구를 사용하여 메시지를 보낼 수도 있습니다.

1. **Ctrl + F5**를 누르거나 중지 버튼을 클릭하여 디버깅을 중지합니다.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>여러 모듈로 IoT Edge 솔루션 빌드 및 디버그

단일 모듈 개발을 마친 후 여러 모듈이 있는 전체 솔루션을 실행하고 디버그합니다.

1. **AzureIoTEdgeApp1** 를 마우스 오른쪽 단추로 클릭 하 고 **추가** > **새 IoT Edge 모듈**을 선택 하 여 두 번째 모듈을 솔루션에 추가 합니다. 두 번째 모듈의 기본 이름은 **IotEdgeModule2** 이 고 다른 파이프 모듈의 역할을 합니다.

1. `deployment.template.json` 파일을 열면 **모듈** 섹션에 **IotEdgeModule2** 이 추가 된 것을 볼 수 있습니다. **routes** 섹션을 다음으로 바꿉니다. 모듈 이름을 사용자 지정한 경우에는 해당 이름을 업데이트하여 맞춰 주어야 합니다.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. **AzureIoTEdgeApp1**을 오른쪽 클릭하여 바로 가기 메뉴에서 **시작 프로젝트로 설정**을 선택합니다.

1. 중단점을 만든 다음 **F5**를 눌러 여러 개의 모듈을 동시에 실행 및 디버그합니다. 여러 .NET Core 콘솔 앱 창이 표시 됩니다. 각 창에는 서로 다른 모듈이 표시 됩니다.

   ![여러 모듈 디버그](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. **Ctrl + F5**를 누르거나 중지 버튼을 클릭하여 디버깅을 중지합니다.

## <a name="build-and-push-images"></a>이미지 빌드 및 푸시

1. **AzureIoTEdgeApp1**이 시작 프로젝트여야 합니다. 모듈 이미지를 빌드할 구성으로 **디버그**와 **릴리스** 중 하나를 선택합니다.

    > [!NOTE]
    > **디버그**를 선택하면, Visual Studio가 `Dockerfile.(amd64|windows-amd64).debug`을(를) 사용하여 Docker 이미지를 빌드합니다. 이미지를 빌드하는 동안 컨테이너 이미지의 .NET Core 명령 줄 디버거 VSDBG가 포함됩니다. 프로덕션이 준비된 IoT Edge 모듈의 경우, VSDBG 없이 `Dockerfile.(amd64|windows-amd64)`을(를) 사용하는 **릴리스** 구성을 권장합니다.

1. Azure Container Registry 같은 프라이빗 레지스트리를 사용할 경우, 다음 Docker 명령을 사용하여 로그인합니다. 로컬 레지스트리를 사용할 경우 [로컬 레지스트리를 실행](https://docs.docker.com/registry/deploying/#run-a-local-registry)할 수 있습니다.

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Azure Container Registry 같은 프라이빗 레지스트리를 사용할 경우, `deployment.template.json` 파일에 있는 런타임 설정에 레지스트리 로그인 정보를 추가해야 합니다. 자리 표시자를 실제 ACR 관리 사용자 이름, 암호 및 레지스트리 이름으로 바꿉니다.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. **AzureIoTEdgeApp1**을 오른쪽 클릭한 다음, **Edge Solution 빌드 및 푸시**를 선택하여 각 모듈의 Docker 이미지를 빌드 및 푸시합니다.

   ![이미지 빌드 및 푸시](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>솔루션 배포

IoT Edge 디바이스를 설정할 때 사용한 빠른 시작 문서에서는 Azure Portal을 사용하여 모듈을 배포했습니다. Visual Studio 용 클라우드 탐색기를 사용하여 모듈을 배포할 수도 있습니다. 시나리오의 배포 매니페스트인 `deployment.json` 파일이 이미 있으므로 배포를 수신할 디바이스만 선택하면 됩니다.

1. **보기** > **클라우드 탐색기**를 클릭하여 **클라우드 탐색기**를 엽니다. Visual Studio 2019에 로그인 했는지 확인 합니다.

1. **클라우드 탐색기**에서 구독을 펼쳐서 배포하려는 Azure IoT Hub 및 Azure IoT Edge 디바이스를 찾습니다.

1. IoT Edge 디바이스를 오른쪽 클릭하여 배포를 만듭니다. `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json` 아래에 있는 배포 매니페스트 파일을 선택해야 합니다.

   > [!NOTE]
   > `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`을 선택하면 안됩니다.

1. 새로 고침 단추를 클릭 하 여 **SimulatedTemperatureSensor** 모듈과 함께 실행 되는 새 모듈과 **$edgeAgent** 및 **$edgeHub**를 확인 합니다.

## <a name="view-generated-data"></a>생성된 데이터 보기

1. 특정 장치에 대 한 D2C 메시지를 모니터링 하려면 목록에서 장치를 선택한 다음 **작업** 창에서 **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 클릭 합니다.

1. 데이터 모니터링을 중지 하려면 목록에서 장치를 선택한 다음 **작업** 창에서 **기본 제공 이벤트 엔드포인트 모니터링 중지** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

IoT Edge 디바이스의 사용자 지정 모듈을 개발하려면 [Azure IoT Hub SDK에 대해 숙지하고 사용](../iot-hub/iot-hub-devguide-sdks.md)하십시오.
