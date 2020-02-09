---
title: Azure IoT Hub IoT DevKit AZ3166 연결
description: 이 자습서에서는 Azure 클라우드 플랫폼으로 데이터를 보내도록 DevKit AZ3166을 설정하고 Azure IoT Hub에 연결하는 방법을 알아봅니다.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 139d1a470c67d5dab310c4fa2a9171f433df2061
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912370"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Azure IoT Hub에 IoT DevKit AZ3166 연결

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Microsoft Azure 서비스를 활용하는 IoT(사물 인터넷) 솔루션을 개발하고 프로토타입하는 데 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)를 사용할 수 있습니다. 여기에는 다양 한 주변 장치 및 센서가 있는 Arduino 호환 보드, 오픈 소스 보드 패키지 및 풍부한 [샘플 갤러리가](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)포함 되어 있습니다.

## <a name="what-you-learn"></a>학습 내용

* IoT Hub를 만들고 MXChip IoT DevKit용 디바이스를 등록하는 방법
* IoT DevKit를 Wi-fi에 연결 하 고 IoT Hub 연결 문자열을 구성 하는 방법을 설명 합니다.
* DevKit 센서 원격 분석 데이터를 IoT hub로 보내는 방법입니다.
* IoT DevKit에 대 한 개발 환경을 준비 하 고 응용 프로그램을 개발 하는 방법을 설명 합니다.

DevKit가 아직 없으세요? [DevKit 시뮬레이터](https://azure-samples.github.io/iot-devkit-web-simulator/)를 시도하거나 [DevKit를 구매](https://aka.ms/iot-devkit-purchase)합니다.

[코드 샘플 갤러리](https://docs.microsoft.com/samples/browse/?term=mxchip)에서 모든 devkit 자습서에 대 한 소스 코드를 찾을 수 있습니다.

## <a name="what-you-need"></a>필요한 항목

* 마이크로 USB 케이블로 MXChip IoT DevKit 보드 [지금 사용해 보세요](https://aka.ms/iot-devkit-purchase).
* Windows 10, macOS 10.10 + 또는 Ubuntu 18.04 +를 실행 하는 컴퓨터
* 활성화된 Azure 구독. [30일 평가판 Microsoft Azure 계정](https://azureinfo.microsoft.com/us-freetrial.html) 활성화

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>하드웨어 준비

컴퓨터에 다음 하드웨어를 연결합니다.

* DevKit 보드
* 마이크로 USB 케이블

![필수 하드웨어](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit를 컴퓨터에 연결하려면 다음 단계를 수행합니다.

1. USB 끝을 컴퓨터에 연결합니다.

2. 마이크로 USB 끝을 DevKit에 연결합니다.

3. 전원의 녹색 LED는 연결을 확인합니다.

   ![하드웨어 연결](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>빠른 시작: DevKit에서 IoT Hub 원격 분석 보내기

퀵 스타트는 미리 컴파일된 DevKit 펌웨어를 사용 하 여 IoT Hub에 원격 분석을 보냅니다. 실행 하기 전에 IoT hub를 만들고 허브에 장치를 등록 합니다.

### <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>디바이스 등록

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 디바이스 ID를 만듭니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

   **Mynodedevice**: 등록 하는 장치의 이름입니다. 표시된 것처럼 **MyNodeDevice**를 사용하세요. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서 해당 이름을 사용해야 하고, 애플리케이션 예제에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > `device-identity`를 실행 하는 동안 오류가 발생 하면 [Azure CLI에 대 한 AZURE IOT 확장](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) 을 설치 합니다. 자세한 내용은을 참조 하세요.
  
1. Azure Cloud Shell에서 다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    다음과 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

### <a name="send-devkit-telemetry"></a>DevKit 원격 분석 보내기

DevKit는 IoT hub의 장치 특정 엔드포인트에 연결 하 여 온도 및 습도 원격 분석을 전송 합니다.

1. IoT DevKit에 대 한 최신 버전의 [Getstarted](https://aka.ms/devkit/prod/getstarted/latest) 를 다운로드 합니다.

1. IoT DevKit에서 USB를 통해 컴퓨터에 연결 하는지 확인 합니다. 파일 탐색기 열기 **AZ3166**이라는 USB 대용량 저장 장치가 있습니다.

    ![Windows 탐색기 열기](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. 다운로드 한 펌웨어를 대용량 저장 장치에 끌어서 놓으면 자동으로 깜박입니다.

    ![펌웨어 복사](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. DevKit에서 단추 **b**를 길게 누른 다음 **다시 설정** 단추를 눌렀다가 놓고 단추 **b**를 해제 합니다. DevKit가 AP 모드로 전환 됩니다. 확인을 위해이 화면에는 DevKit의 SSID (서비스 집합 식별자)와 구성 포털 IP 주소가 표시 됩니다.

    ![단추, 단추 B 및 SSID 다시 설정](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![AP 모드 설정](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. 다른 Wi-fi 사용 장치 (컴퓨터 또는 휴대폰)의 웹 브라우저를 사용 하 여 이전 단계에서 표시 된 IoT DevKit SSID에 연결 합니다. 암호를 요청 하는 경우 비워 둡니다.

    ![SSID 연결](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. 브라우저에서 **192.168.0.1** 을 엽니다. IoT DevKit에 연결할 Wi-fi를 선택 하 고 Wi-fi 암호를 입력 한 다음 이전에 기록해 둔 장치 연결 문자열을 붙여넣습니다. 그런 다음 저장을 클릭합니다.

    ![구성 UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit는 2.4 g h z 네트워크만 지원 합니다. 자세한 내용은 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration)를 참조하세요.

1. WiFi 정보 및 장치 연결 문자열은 결과 페이지가 표시 될 때 IoT DevKit에 저장 됩니다.

    ![구성 결과](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Wi-Fi를 구성한 후 디바이스가 분리되는 경우에도 자격 증명은 해당 연결에 대해 디바이스에서 유지됩니다.

1. IoT DevKit은 몇 초 안에 재부팅 됩니다. DevKit 화면에서 DevKit에 대 한 IP 주소는 메시지 수를 전송 하는 온도 및 습도 값을 포함 하는 원격 분석 데이터를 사용 하 여 Azure IoT Hub 합니다.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![데이터 전송](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Azure로 전송 되는 원격 분석 데이터를 확인 하려면 Azure Cloud Shell에서 다음 명령을 실행 합니다.

    ```bash
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>개발 환경 준비

다음 단계를 수행 하 여 DevKit에 대 한 개발 환경을 준비 합니다.

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Azure IoT Tools 확장 패키지를 사용 하 여 Visual Studio Code 설치

1. [Arduino IDE](https://www.arduino.cc/en/Main/Software)를 설치합니다. Arduino 코드를 컴파일 및 업로드하는 데 필요한 도구 체인을 제공합니다.
    * **Windows**: Windows Installer 버전을 사용합니다. 앱 스토어에서 설치 하지 마십시오.
    * **macOS**: 추출된 **Arduino.app**을 `/Applications` 폴더로 끌어다 놓습니다.
    * **Ubuntu**: `$HOME/Downloads/arduino-1.8.8` 같은 폴더에 압축을 풉니다.

2. 강력한 intellisense를 사용 하는 플랫폼 간 소스 코드 편집기, 코드 완성 및 디버깅 지원 뿐만 아니라 marketplace에서 다양 한 확장을 설치할 수 [Visual Studio Code](https://code.visualstudio.com/)를 설치 합니다.

3. VS Code를 실행하고, 확장 마켓플레이스에서 **Arduino**를 검색하여 설치합니다. 이 확장은 Arduino 플랫폼에서 개발에 대한 향상된 환경을 제공합니다.

    ![Arduino 설치](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 확장 마켓플레이스에서 [Azure IoT Tools](https://aka.ms/azure-iot-tools)를 검색하여 설치합니다.

    ![Azure IoT Tools 설치](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    또는이 직접 링크를 사용 합니다.
    > [!div class="nextstepaction"]
    > [Azure IoT Tools 확장 팩 설치](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Azure IoT Tools 확장 팩에는 다양 한 IoT devkit 장치에서 개발 하 고 디버그 하는 데 사용 되는 [Azure Iot 장치 워크 벤치](https://aka.ms/iot-workbench) 가 포함 되어 있습니다. Azure IoT Tools 확장 팩에도 포함 된 [Azure IoT Hub 확장](https://aka.ms/iot-toolkit)은 Azure iot hub를 관리 하 고 상호 작용 하는 데 사용 됩니다.

5. Arduino 설정을 사용하여 VS Code를 구성합니다.

    Visual Studio Code에서 **파일 > 기본 설정 > 설정** (macos, **코드 > 기본 설정 > 설정**)을 클릭 합니다. 그런 다음 *설정* 페이지의 오른쪽 위 모퉁이에 있는 **설정 열기 (JSON)** 아이콘을 클릭 합니다.

    ![Azure IoT Tools 설치](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    플랫폼에 따라 다음 줄을 추가하여 Arduino를 구성합니다. 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        **{username}** 을 사용자 이름 아래의 자리 표시자로 바꿉니다.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. `F1` 키를 클릭하여 명령 팔레트를 연 다음, **Arduino: 보드 관리자**를 입력하고 선택합니다. **AZ3166**을 검색하고 최신 버전을 설치합니다.

    ![DevKit SDK 설치](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>ST-Link 드라이버 설치

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html)는 IoT DevKit가 개발 머신과 통신하는 데 사용하는 USB 인터페이스입니다. 컴파일된 장치 코드를 DevKit로 플래시 하려면 Windows에 설치 해야 합니다. 해당 OS의 단계에 따라 머신이 디바이스에 액세스할 수 있도록 허용합니다.

* **Windows**: [STMicroelectronics 웹 사이트](https://www.st.com/en/development-tools/stsw-link009.html)에서 USB 드라이버를 다운로드하여 설치합니다.
* **macOS**: macOS는 드라이버가 필요 없습니다.
* **Ubuntu**: 터미널에서 명령을 실행 하 고 로그 아웃 한 다음 그룹 변경 내용을 적용 하도록 로그인 합니다.

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

개발 환경을 준비하고 구성하기 위한 작업이 모두 끝났습니다. 방금 실행 한 GetStarted 샘플을 빌드할 수 있습니다.

## <a name="build-your-first-project"></a>첫 번째 프로젝트 빌드

### <a name="open-sample-code-from-sample-gallery"></a>샘플 갤러리에서 샘플 코드 열기

IoT DevKit에는 다양 한 Azure 서비스에 DevKit를 연결 하는 방법을 배우는 데 사용할 수 있는 풍부한 샘플 갤러리가 포함 되어 있습니다.

1. IoT DevKit가 컴퓨터에 연결되어 있지 **않은지** 확인합니다. VS Code를 시작하고 DevKit를 컴퓨터에 연결합니다.

1. `F1`를 클릭 하 여 명령 팔레트를 열고 **Azure IoT 장치 워크 벤치: 예제 열기**...를 입력 하 고 선택 합니다. 그런 다음 **IoT DevKit** as board를 선택 합니다.

1. IoT Workbench 예제 페이지에서 **시작**을 찾아서 **샘플 열기**를 클릭합니다. 기본 경로를 선택하여 샘플 코드를 다운로드합니다.

    ![샘플 열기](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Azure IoT Hub 및 장치 프로 비전

Azure Portal에서 Azure IoT Hub 및 장치를 프로 비전 하는 대신 개발 환경을 종료 하지 않고 VS Code에서이 작업을 수행할 수 있습니다.

1. 새 열린 프로젝트 창에서 `F1`을 클릭 하 여 명령 팔레트를 열고 **Azure IoT 장치 워크 벤치: Azure 서비스 프로 비전**...을 입력 하 고 선택 합니다. 단계별 가이드에 따라 Azure IoT Hub 프로 비전을 완료 하 고 IoT Hub 장치를 만듭니다.

    ![프로 비전 명령](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Azure에 로그인 하지 않은 경우 로그인에 대 한 팝업 알림을 따릅니다.

1. 사용할 구독을 선택합니다.

    ![하위 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. 그런 다음 새 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)을 선택 하거나 만듭니다.

    ![리소스 그룹 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. 지정한 리소스 그룹에서 가이드에 따라 새 Azure IoT Hub를 선택 하거나 만듭니다.

    ![IoT Hub 단계 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![IoT Hub 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![선택한 IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. 출력 창에 프로 비전 된 Azure IoT Hub 표시 됩니다.

    ![프로 비전 된 IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. 프로 비전 한 Azure IoT Hub에서 새 장치를 선택 하거나 만듭니다.

    ![IoT 장치 단계를 선택 합니다.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![프로 비전 된 IoT 장치 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. 이제 프로 비전 된 Azure IoT Hub 장치에서 장치를 만들었습니다. 또한 장치 연결 문자열은 나중에 IoT DevKit를 구성 하기 위해 VS Code에 저장 됩니다.

    ![프로 비전 완료](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>장치 코드 구성 및 컴파일

1. 오른쪽 아래의 상태 표시줄에서 **MXCHIP AZ3166**이 보드로 선택되고 **STMicroelectronics**가 있는 직렬 포트가 사용되는지 확인합니다.

    ![보드 및 COM 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. `F1`를 클릭 하 여 명령 팔레트를 열고 **Azure IoT 장치 워크 벤치: 장치 설정 구성**...을 입력 한 다음 **구성 장치 연결 문자열을 선택 하 > IoT Hub 장치 연결 문자열**을 선택 합니다.

1. DevKit에서 **단추 a**를 누르고 **다시 설정** 단추를 눌렀다가 놓은 다음 **단추 a**를 놓습니다. DevKit에서 구성 모드로 전환 하 여 연결 문자열을 저장 합니다.

    ![연결 문자열](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. `F1`를 다시 클릭 하 고 **Azure IoT 장치 워크 벤치: 장치 코드 업로드**를 입력 하 고 선택 합니다. 컴파일을 시작하고 코드를 DevKit로 업로드합니다.

    ![Arduino 업로드](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit는 다시 부팅하고 코드를 실행하기 시작합니다.

> [!NOTE]
> 오류 또는 중단이 있으면 언제든지 명령을 다시 실행하여 복구할 수 있습니다.

## <a name="test-the-project"></a>프로젝트 테스트

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Azure IoT Hub로 전송된 원격 분석 보기

상태 표시줄의 전원 플러그 아이콘을 클릭하여 직렬 모니터를 엽니다.

![직렬 모니터](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

다음과 같은 결과가 표시되면 샘플 애플리케이션은 성공적으로 실행됩니다.

* 직렬 모니터는 IoT Hub로 전송된 메시지를 표시합니다.
* MXChip IoT DevKit의 LED가 깜박입니다.

![직렬 모니터 출력](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Azure IoT Hub에서 수신한 원격 분석 보기

[Azure IoT 도구](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)를 사용하여 IoT Hub에서 디바이스-클라우드(D2C) 메시지를 모니터링할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, 이전에 만든 IoT Hub를 찾습니다.

    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. **공유 액세스 정책** 창에서 **iothubowner** 정책을 클릭하고, IoT Hub의 연결 문자열을 기록해 둡니다.

    ![연결 문자열 Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. VS Code에서 `F1`를 클릭 하 고를 입력 한 다음 **Azure IoT Hub: IoT Hub 연결 문자열 설정**을 선택 합니다. 연결 문자열을 복사합니다.

    ![연결 문자열 Azure IoT Hub 설정](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 오른쪽의 **AZURE IOT HUB 장치** 창을 확장 하 고, 만든 장치 이름을 마우스 오른쪽 단추로 클릭 하 고, **기본 제공 이벤트 엔드포인트 모니터링 시작**을 선택 합니다.

    ![D2C 메시지 모니터링](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. **출력** 창에서, IoT Hub로 들어오는 D2C 메시지를 볼 수 있습니다.

    ![D2C 메시지](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>코드 검토

`GetStarted.ino` 기본 Arduino sketch 파일입니다.

![D2C 메시지](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

장치 원격 분석을 Azure IoT Hub 전송 하는 방법을 보려면 동일한 폴더에서 `utility.cpp` 파일을 엽니다. IoT DevKit에서 센서 및 주변 장치를 사용 하는 방법을 알아보려면 [API 참조](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) 를 확인 하세요.

사용 되는 `DevKitMQTTClient`는 [Microsoft Azure IoT sdk 및 C 용 라이브러리](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) 에서 Azure IoT Hub와 상호 작용 하는 **iothub_client** 의 래퍼입니다.

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 있는 경우 [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)에서 해결 방법을 찾아보거나 [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit)에서 문의할 수 있습니다. 또한 이 페이지에서 의견을 남겨 피드백을 제공해 주실 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub에 MXChip IoT DevKit를 성공적으로 연결하고 캡처된 센서 데이터를 IoT Hub에 전송했습니다.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
