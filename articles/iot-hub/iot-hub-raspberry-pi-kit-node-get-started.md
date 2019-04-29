---
title: Raspberry Pi-클라우드(Node.js) - Raspberry Pi를 Azure IoT Hub에 연결 | Microsoft Docs
description: 설정 하 고이 자습서에서는 Azure 클라우드 플랫폼으로 데이터를 보내기 위해 Raspberry Pi에 대 한 Azure IoT Hub에 Raspberry Pi를 연결 하는 방법을 알아봅니다.
author: wesmc7777
manager: philmea
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi에서 클라우드로 데이터 전송, raspberry pi-클라우드
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: d1e9a6da399adcdca87c1d6dc30eaf425ec0541e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61453603"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Raspberry Pi를 Azure IoT Hub에 연결(Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

이 자습서에서는 Raspbian을 실행하는 Raspberry Pi 작업의 기초부터 학습합니다. 그런 다음 [Azure IoT Hub](about-iot-hub.md)를 사용하여 디바이스를 클라우드에 원활하게 연결하는 방법을 알아봅니다. Windows 10 IoT Core 샘플이 필요하면 [Windows 개발자 센터](https://www.windowsondevices.com/)로 이동하세요.

아직 키트가 없으세요? [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md)를 사용해 보세요. 또는 새 키트를 [여기](https://azure.microsoft.com/develop/iot/starter-kits)에서 구입합니다.

## <a name="what-you-do"></a>수행할 작업

* IoT Hub를 만듭니다.

* IoT Hub에 Pi용 디바이스를 등록합니다.

* Raspberry Pi를 설정합니다.

* Pi에서 샘플 애플리케이션을 실행하여 IoT Hub로 센서 데이터를 보냅니다.

## <a name="what-you-learn"></a>학습 내용

* Azure IoT Hub를 만들고 새 디바이스 연결 문자열을 가져오는 방법.

* Pi를 BME280 센서와 연결하는 방법.

* Pi에서 샘플 애플리케이션을 실행하여 센서 데이터를 수집하는 방법.

* IoT Hub로 센서 데이터를 보내는 방법.

## <a name="what-you-need"></a>필요한 항목

![필요한 항목](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Raspberry Pi 2 또는 Raspberry Pi 3 보드.

* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* Pi에 연결할 모니터, USB 키보드 및 마우스.

* Windows 또는 Linux를 실행하는 Mac 또는 PC.

* 인터넷 연결.

* 16GB 이상의 microSD 카드.

* 운영 체제 이미지를 microSD 카드에 굽기 위한 USB-SD 어댑터 또는 microSD 카드.

* 6피트 마이크로 USB 케이블과 5볼트 2암페어 전원 공급 장치.

다음 항목은 선택 사항입니다.

* 조립된 Adafruit BME280 온도, 압력 및 습도 센서.

* 실험용 회로판

* F/M 점퍼 와이어 6개.

* 확산형 10mm LED.

> [!NOTE]
> 선택 항목이 없는 경우 시뮬레이트된 센서 데이터를 사용할 수 있습니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT Hub에 대한 연결 문자열 검색

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT 허브에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi 설정

### <a name="install-the-raspbian-operating-system-for-pi"></a>Pi용 Raspbian 운영 체제 설치

Raspbian 이미지를 설치를 위해 microSD 카드를 준비합니다.

1. Raspbian을 다운로드합니다.

   a. [Raspbian Stretch를 다운로드합니다](https://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/)(.zip 파일).

   > [!WARNING]
   > 위의 링크를 사용하여 `raspbian-2017-07-5` zip 이미지를 다운로드하십시오. 최신 버전의 Raspbian 이미지에는 Wiring-Pi Node와 관련된 몇 가지 알려진 문제점이 있으며 이로 인해 다음 단계에서 오류가 발생할 수 있습니다.

   b. 컴퓨터의 폴더에 Raspbian 이미지의 압축을 풉니다.

2. microSD 카드에 Raspbian을 설치합니다.

   a. [Etcher SD 카드 버너 유틸리티를 다운로드하여 설치합니다](https://etcher.io/).

   b. Etcher를 실행하고 1단계에서 압축을 푼 Raspbian 이미지를 선택합니다.

   다. microSD 카드 드라이브를 선택합니다. Etcher가 이미 정확한 드라이브를 선택했을 수 있습니다.

   d. Flash를 클릭하여 microSD 카드에 Raspbian을 설치합니다.

   e. 설치가 완료되면 컴퓨터에서 microSD 카드를 제거합니다. 완료되면 Etcher가 microSD 카드를 자동으로 배출하거나 탑재를 해제하므로 microSD 카드를 바로 제거하는 것이 안전합니다.

   f. Pi에 microSD 카드를 삽입합니다.

### <a name="enable-ssh-and-i2c"></a>SSH 및 I2C를 사용하도록 설정

1. Pi를 모니터, 키보드 및 마우스에 연결합니다.

2. Pi를 시작한 다음 Raspbian를 사용 하 여 로그인 `pi` 사용자 이름으로 및 `raspberry` 암호로 합니다.

3. Raspberry 아이콘 > **기본 설정** > **Raspberry Pi 구성**을 클릭합니다.

   ![Raspbian 기본 설정 메뉴](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. **인터페이스** 탭에서 **I2C** 및 **SSH**를 **사용**으로 설정한 다음 **확인**을 클릭합니다. 실제 센서가 없고 시뮬레이트된 센서 데이터를 사용하려는 경우 이 단계는 선택 사항입니다.

   ![Raspberry Pi에서 I2C 및 SSH를 사용하도록 설정](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> SSH 및 I2C를 사용하려는 경우 [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) 및 [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c)에서 더 많은 참조 문서를 찾을 수 있습니다.

### <a name="connect-the-sensor-to-pi"></a>Pi에 센서 연결

실험용 회로판과 점퍼 와이어를 사용하여 LED 및 BME280 Pi를 다음과 같이 연결합니다. 센서가 없는 경우 [이 섹션을 건너뛰세요](#connect-pi-to-the-network).

![Raspberry Pi와 센서 연결](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

BME280 센서는 온도 및 습도 데이터를 수집할 수 있습니다. 디바이스에서 클라우드로 메시지를 보내면 LED가 깜박입니다. 

센서 핀의 경우 다음 배선을 사용합니다.

| 시작(센서 및 LED)     | 끝(보드)            | 케이블 색   |
| -----------------------  | ---------------------- | ------------: |
| VDD(5G 핀)             | 3.3V PWR(1 핀)       | 흰색 케이블   |
| GND(7G 핀)             | GND(6 핀)            | 갈색 케이블   |
| SDI(10G 핀)            | I2C1 SDA(3 핀)       | 빨간색 케이블     |
| SCK(8G 핀)             | I2C1 SCL(5 핀)       | 주황색 케이블  |
| LED VDD(18F 핀)        | GPIO 24(18 핀)       | 흰색 케이블   |
| LED GND(17F 핀)        | GND(20 핀)           | 검은색 케이블   |

참조용으로 [Raspberry Pi 2 및 3 핀 매핑](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi)을 보려면 클릭하세요.

BME280이 Raspberry Pi에 성공적으로 연결되면 아래 이미지처럼 보여야 합니다.

![Pi와 BME280 연결](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>네트워크에 Pi 연결

마이크로 USB 케이블 및 전원 공급 장치를 사용하여 Pi를 켭니다. 이더넷 케이블을 사용하여 Pi를 유선 네트워크에 연결하거나 [Raspberry Pi Foundation의 지침](https://www.raspberrypi.org/learning/software-guide/wifi/)에 따라 Pi를 무선 네트워크에 연결합니다. Pi가 네트워크에 성공적으로 연결된 후 [Pi의 IP 주소](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)를 적어 두어야 합니다.

![유선 네트워크에 연결](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Pi가 컴퓨터와 같은 네트워크에 연결되어 있어야 합니다. 예를 들어 컴퓨터는 무선 네트워크, Pi는 유선 네트워크에 연결되었다면 devdisco 출력에 IP 주소가 표시되지 않을 수 있습니다.

## <a name="run-a-sample-application-on-pi"></a>Pi에서 샘플 애플리케이션 실행

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>샘플 애플리케이션을 복제하고 필수 구성 요소 패키지 설치

1. 호스트 컴퓨터에서 다음 SSH 클라이언트 중 하나를 사용하여 Raspberry Pi에 연결합니다.

   **Windows 사용자**
  
   a. Windows용 [PuTTY](https://www.putty.org/)를 다운로드 및 설치합니다. 

   b. 호스트 이름(또는 IP 주소) 섹션에 Pi의 IP 주소를 복사하고 연결 형식으로 SSH를 선택합니다.

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac 및 Ubuntu 사용자**

   Ubuntu 또는 macOS에서 기본 제공되는 SSH 클라이언트를 사용합니다. SSH를 통해 Pi를 연결하려면 `ssh pi@<ip address of pi>`를 실행해야 할 수도 있습니다.

   > [!NOTE]
   > 기본 사용자 이름은 `pi`이며 암호는 `raspberry`입니다.

2. Node.js 및 NPM을 Pi에 설치합니다.

   먼저 Node.js 버전을 확인합니다.

   ```bash
   node -v
   ```

   버전이 4.x보다 낮거나 Pi에 Node.js가없는 경우 최신 버전을 설치합니다.

   ```bash
   curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. 샘플 애플리케이션을 복제합니다.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

4. 샘플의 모든 패키지를 설치합니다. 설치에는 Azure IoT 디바이스 SDK, BME280 센서 라이브러리 및 배선 Pi 라이브러리가 포함되어 있습니다.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```

   > [!NOTE]
   >네트워크 연결에 따라 이 설치 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="configure-the-sample-application"></a>샘플 애플리케이션 구성

1. 다음 명령을 실행하여 config 파일을 엽니다.

   ```bash
   nano config.json
   ```

   ![Config 파일](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   이 파일에는 사용자가 구성할 수 있는 두 개 항목이 있습니다. 첫 번째는 클라우드로 전송되는 두 메시지 사이의 시간 간격(밀리초)을 정의하는 `interval`입니다. 두 번째는 시뮬레이트된 센서 데이터의 사용 여부에 대한 부울 값인 `simulatedData`입니다.

   **센서가 없는 경우**`simulatedData` 값을 `true`로 설정하여 샘플 애플리케이션에서 시뮬레이션된 센서 데이터를 만들어서 사용하게 합니다.

2. Control-O > Enter > Control-X를 입력하여 저장하고 종료합니다.

### <a name="run-the-sample-application"></a>샘플 애플리케이션 실행

다음 명령을 실행하여 샘플 애플리케이션을 실행합니다.

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > 디바이스 연결 문자열을 복사하여 작은따옴표 안에 붙여 넣습니다.

IoT Hub로 전송되는 센서 데이터와 메시지를 보여 주는 다음 출력이 표시됩니다.

![출력 - Raspberry Pi에서 IoT Hub로 전송된 센서 데이터](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>허브에서 수신한 메시지 읽기

장치에서 IoT hub에서 수신한 메시지를 모니터링 하는 한 가지 방법은 Visual Studio Code 용 Azure IoT 도구를 사용 하는 것입니다. 자세한 내용은 참조 하세요 [for Visual Studio Code 장치 및 IoT Hub 간에 메시지를 받고 보내는 데 사용 하 여 Azure IoT 도구](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)합니다.

장치에서 보낸 데이터를 처리 하는 방법을 더 보려면, 다음 섹션을 진행 합니다.

## <a name="next-steps"></a>다음 단계

샘플 애플리케이션을 실행하여 센서 데이터를 수집하고 IoT Hub로 전송했습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
