---
title: 자습서 - IoT 플러그 앤 플레이(미리 보기) 디바이스를 Azure IoT Central에 연결
description: 이 자습서에서는 디바이스 기능 모델을 사용하여 디바이스 코드를 만드는 방법을 보여줍니다. 그런 다음, 디바이스 코드를 실행하고, 디바이스가 IoT Central 애플리케이션에 연결되는 것을 확인하고, 자동으로 생성되는 보기를 사용합니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: ca2ac62892d1c8d438cc37bffcbfede14058bc23
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026376"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>자습서: 디바이스 기능 모델을 사용하여 IoT 플러그 앤 플레이(미리 보기) 디바이스를 만들고 및 IoT Central 애플리케이션에 연결

_DCM(디바이스 기능 모델)_ 은 [IoT 플러그 앤 플레이(미리 보기)](../../iot-pnp/overview-iot-plug-and-play.md) 디바이스의 기능을 설명합니다. 디바이스를 처음으로 연결할 때 IoT Central은 DCM을 사용하여 디바이스 템플릿을 만들고 디바이스를 시각화할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Visual Studio Code에서 DCM을 사용하여 IoT 플러그 앤 플레이(미리 보기) 디바이스를 만듭니다.
> * Windows에서 디바이스 코드를 실행하고 IoT Central 애플리케이션에 연결되는 것을 확인합니다.
> * 디바이스가 전송하는 시뮬레이션된 원격 분석 데이터 보기

## <a name="prerequisites"></a>사전 요구 사항

[Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md) 빠른 시작을 완료하여 **사용자 지정 앱 > 사용자 지정 애플리케이션** 템플릿을 사용하여 IoT Central 애플리케이션을 만듭니다.

이 자습서를 완료하려면 로컬 머신에 다음 소프트웨어를 설치해야 합니다.

* **C++ 빌드 도구** 및 **Nuget 패키지 관리자 구성 요소** 워크로드를 사용하여 [Visual Studio용 도구를 빌드](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16)합니다. 또는 동일한 워크로드가 설치된 [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 또는 2015가 이미 있는 경우
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/) - **CMake**를 설치할 때 **시스템 경로에 CMake 추가** 옵션을 선택합니다.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.JS](https://nodejs.org/)
* `dps-keygen` 유틸리티:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Azure IoT Tools 설치

다음 단계에 따라 VS Code 코드에 Azure IoT Tools 확장 팩을 설치합니다.

1. VS Code에서 **확장** 탭을 선택합니다.
1. **Azure IoT Tools**를 검색합니다.
1. **설치**를 선택합니다.

## <a name="prepare-the-development-environment"></a>개발 환경 준비

이 자습서에서는 [Vcpkg](https://github.com/microsoft/vcpkg) 라이브러리 관리자를 사용하여 개발 환경에 Azure IoT C 디바이스 SDK를 설치합니다.

1. 명령 프롬프트를 엽니다. 다음 명령을 실행하여 Vcpkg를 설치합니다.

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    그런 다음, 사용자 전체 [통합](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)을 연결하려면 다음을 실행합니다. 이 명령을 처음 실행할 때에는 관리자 권한이 필요합니다.

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. 다음과 같이 Azure IoT C 디바이스 SDK Vcpkg를 설치합니다.

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>디바이스 키 생성

디바이스를 IoT Central 애플리케이션에 연결하려면 디바이스 키가 필요합니다. 디바이스 키를 생성하는 방법은 다음과 같습니다.

1. [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md) 빠른 시작에서 **사용자 지정 앱 > 미리 보기 애플리케이션** 템플릿을 사용하여 만든 IoT Central 애플리케이션에 로그인합니다.

1. **관리** 페이지로 이동하여 **디바이스 연결**을 선택합니다.

1. **키 보기**를 선택할 때 표시되는 **ID 범위** 및 **기본 키**를 기록해 둡니다. 이 자습서의 뒷부분에서 이러한 값을 사용합니다.

    ![디바이스 연결](./media/tutorial-connect-pnp-device/device-connection.png)

1. 명령 프롬프트를 열고 다음 명령을 실행하여 디바이스 키를 생성합니다.

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    생성된 _디바이스 키_를 기록해 둡니다. 이 자습서의 이후 단계에서 이 값을 사용할 것입니다.

## <a name="download-your-model"></a>모델 다운로드

이 자습서에서는 MxChip IoT DevKit 디바이스용 공용 DCM을 사용합니다. 코드를 실행하기 위한 실제 DevKit 디바이스가 필요 없습니다. 이 자습서에서는 Windows에서 실행되도록 코드를 컴파일합니다.

1. `central_app` 폴더를 만들고 VS Code에서 엽니다.

1. **Ctrl+Shift+P** 를 사용하여 명령 팔레트를 열고, **IoT 플러그 앤 플레이**를 입력하고, **모델 리포지토리 열기**를 선택합니다. **공용 리포지토리**를 선택합니다. VS Code가 공용 모델 리포지토리의 DCM 목록을 표시합니다.

1. ID가 `urn:mxchip:mxchip_iot_devkit:1`인 **MXChip IoT DevKit** DCM을 선택합니다. **다운로드**를 선택합니다. 이제 `central_app` 폴더에 DCM 복사본이 있습니다.

![모델 리포지토리 및 DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> IoT Central을 사용하려면 디바이스 기능 모델의 모든 인터페이스가 동일한 파일에 인라인으로 정의되어 있어야 합니다.

## <a name="generate-the-c-code-stub"></a>C 코드 스텁 생성

이제 **MXChip IoT DevKit** DCM 및 관련 인터페이스가 있으므로, 모델을 구현하는 디바이스 코드를 생성할 수 있습니다. VS Code에서 C 코드 스텁을 생성하는 방법은 다음과 같습니다.

1. DCM 파일이 들어 있는 폴더를 열어 놓은 상태에서, **Ctrl+Shift+P**를 사용하여 명령 팔레트를 열고, **IoT 플러그 앤 플레이**를 입력하고, **디바이스 코드 스텁 생성**을 선택합니다.

    > [!NOTE]
    > IoT 플러그 앤 플레이 코드 생성기 유틸리티를 처음 사용하는 경우 다운로드하는 데 몇 초 정도 걸립니다.

1. 방금 다운로드한 **MXChip IoT DevKit** DCM 파일을 선택합니다.

1. 프로젝트 이름으로 **devkit_device**를 입력합니다.

1. 언어로 **ANSI C**를 선택합니다.

1. 연결 방법으로 **DPS(Device Provisioning Service) 대칭 키를 통해**를 선택합니다.

1. 프로젝트 형식으로 **Windows의 CMake 프로젝트**를 선택합니다. **MXChip IoT DevKit 프로젝트**를 선택하지 마세요. 이 옵션은 실제 DevKit 디바이스가 있는 경우에 사용합니다.

1. SDK를 포함하는 방법으로 **Vcpkg를 통해**를 선택합니다.

1. VS Code 새 창이 열리고 생성된 디바이스 코드 스텁 파일이 `devkit_device` 폴더에 포함되어 있습니다

![생성된 디바이스 코드](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>코드 빌드

디바이스 SDK를 사용하여 생성된 디바이스 코드 스텁을 빌드합니다. 여기서 빌드하는 애플리케이션은 **MXChip IoT DevKit** 디바이스를 시뮬레이션하고 IoT Central 애플리케이션에 연결합니다. 이 애플리케이션은 원격 분석 데이터 및 속성을 보내고 명령을 수신합니다.

1. 명령 프롬프트에서 `devkit_device` 폴더에 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다.

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. 다음 명령을 실행하여 생성된 코드 스텁을 빌드합니다. `<directory of your Vcpkg repo>` 자리 표시자를 **Vcpkg** 리포지토리의 복사본 경로로 바꿉니다.

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Visual Studio 2017 또는 2015를 사용하는 경우 사용 중인 빌드 도구에 따라 CMake 생성기를 지정해야 합니다.

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. 빌드가 성공적으로 완료되면 동일한 명령 프롬프트에서 애플리케이션을 실행합니다. `<scopeid>`, `<primarykey>`를 이전에 적어 둔 값으로 바꿉니다.

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <primarykey>
    ```

1. 디바이스 애플리케이션이 IoT Hub로 데이터를 보내기 시작합니다. 이전 명령을 처음 실행할 때 `Error registering device for DPS` 오류가 표시되는 경우도 있습니다. 이 오류가 표시되면 명령을 다시 시도합니다.

## <a name="view-the-device"></a>디바이스 보기

디바이스 코드가 IoT Central에 연결되면 애플리케이션에서 보내는 속성과 원격 분석 데이터를 볼 수 있습니다.

1. IoT Central 애플리케이션에서 **디바이스** 페이지로 이동하여 **mxchip-01** 디바이스를 선택합니다. 이 디바이스는 디바이스 코드가 연결될 때 자동으로 추가되었습니다.

    ![개요 페이지](./media/tutorial-connect-pnp-device/overview-page.png)

    몇 분 후, 이 페이지에는 디바이스에서 보내는 원격 분석의 차트가 표시됩니다.

1. 디바이스에서 보낸 속성 값을 보려면 **정보** 페이지를 선택합니다.

1. 디바이스에 대한 명령을 호출하려면 **명령** 페이지를 선택합니다. 디바이스 코드를 실행하는 명령 프롬프트에서 디바이스가 응답하는 것을 볼 수 있습니다.

1. IoT Central이 공용 리포지토리의 DCM에서 만든 템플릿을 보려면 **디바이스 템플릿** 페이지로 이동합니다.

    ![디바이스 템플릿 페이지](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 공용 모델 리포지토리의 DCM에서 생성된 IoT 플러그 앤 플레이(미리 보기) 디바이스를 연결하는 방법을 알아보았습니다.

DCM에 대한 자세한 내용과 나만의 모델을 만드는 방법에 대한 자세한 내용을 보려면 방법 가이드를 계속 진행하세요.

> [!div class="nextstepaction"]
> [새 IoT 디바이스 유형 정의](./howto-set-up-template.md)
