---
title: ESP8266에서 클라우드로 - Feather HUZZAH ESP8266을 Azure IoT Hub에 연결 | Microsoft Docs
description: 이 자습서에서는 Azure 클라우드 플랫폼으로 데이터를 보내기 위해 Adafruit Feather HUZZAH ESP8266를 설정하고 해당 Azure IoT Hub에 연결하는 방법을 알아봅니다.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 293901aca3fa1a94c9c6340d2e04f47914db0e07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783334"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Adafruit Feather HUZZAH ESP8266을 클라우드의 Azure IoT Hub에 연결

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![DHT22, Feather HUZZAH ESP8266 및 IoT Hub 간 연결](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>수행할 작업

사용자가 만든 IoT Hub에 Adafruit Feather HUZZAH ESP8266을 연결합니다. 그런 다음 ESP8266에서 샘플 애플리케이션을 실행하여 DHT22 센서로부터 온도 및 습도 데이터를 수집합니다. 마지막으로 센서 데이터를 IoT Hub로 보냅니다.

> [!NOTE]
> 다른 ESP8266 보드를 사용하는 경우에도 이러한 단계에 따라 IoT Hub에 계속 연결할 수 있습니다. 사용하는 ESP8266 보드에 따라 `LED_PIN`을 다시 구성해야 할 수도 있습니다. 예를 들어 AI-Thinker의 ESP8266을 사용하는 경우 `0`을 `2`로 변경할 수 있습니다. 아직 키트가 없으세요? [Azure 웹 사이트에서 가져오기](https://azure.com/iotstarterkits)

## <a name="what-you-learn"></a>학습 내용

* IoT Hub를 만들고 디바이스를 Feather HUZZAH ESP8266으로 등록하는 방법
* 센서와 컴퓨터에 Feather HUZZAH ESP8266을 연결하는 방법
* Feather HUZZAH ESP8266에서 샘플 애플리케이션을 실행하여 센서 데이터를 수집하는 방법
* 센서 데이터를 IoT Hub로 보내는 방법

## <a name="what-you-need"></a>필요한 항목

![이 자습서에 필요한 부품](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

이 작업을 완료하려면 Feather HUZZAH ESP8266 시작 키트에서 다음 부품이 필요합니다.

* Feather HUZZAH ESP8266 보드
* Micro USB - A형 USB 케이블

또한 개발 환경에는 다음 사항도 필요합니다.

* 활성 Azure 구독. Azure 계정이 없는 경우 몇 분 만에 [Azure 평가판 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
* Windows 또는 Ubuntu를 실행하는 Mac 또는 PC
* Feather HUZZAH ESP8266을 연결할 무선 네트워크
* 구성 도구를 다운로드하기 위한 인터넷 연결
* [Arduino용 Visual Studio Code 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> Arduino용 Visual Studio Code 확장에 사용되는 Arduino IDE 버전은 1.6.8 이상 버전이어야 합니다. 이전 버전은 Azure IoT 라이브러리에서 작동하지 않습니다.

센서가 없는 경우 다음 항목은 선택 사항입니다. 시뮬레이션된 센서 데이터를 사용하는 옵션도 있습니다.

* Adafruit DHT22 온도 및 습도 센서
* 실험용 회로판
* M/M 점퍼 와이어

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT Hub에 대한 연결 문자열 검색

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT 허브에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Feather HUZZAH ESP8266을 센서와 컴퓨터에 연결

이 섹션에서는 센서를 보드에 연결합니다. 그런 다음 디바이스를 나중에 사용할 컴퓨터에 연결합니다.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Feather HUZZAH ESP8266에 DHT22 온도 및 습도 센서 연결

다음과 같이 실험용 회로판과 점퍼 와이어를 사용하여 연결합니다. 센서가 없는 경우 시뮬레이션된 센서 데이터를 사용할 수 있으므로 이 섹션을 건너뜁니다.

![연결 참조](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

센서 핀의 경우 다음 배선을 사용합니다.

| 시작(센서)           | 끝(보드)            | 케이블 색   |
| -----------------------  | ---------------------- | ------------  |
| VDD(핀 31F)            | 3V(핀 58H)           | 빨간색 케이블     |
| 데이터(핀 32F)           | GPIO 2(핀 46A)       | 파란색 케이블    |
| GND(핀 34F)            | GND(핀 56I)          | 검은색 케이블   |

자세한 내용은 [Adafruit DHT22 센서 설치](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor)(영문) 및 [Adafruit Feather HUZZAH Esp8266 핀 배열](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts)(영문)을 참조하세요.

이제 Feather Huzzah ESP8266을 작동 센서와 연결해야 합니다.

![DHT22를 Feather Huzzah에 연결](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Feather HUZZAH ESP8266을 컴퓨터에 연결

다음과 같이 Micro USB - A형 USB 케이블을 사용하여 Feather HUZZAH ESP8266을 컴퓨터에 연결합니다.

![Feather Huzzah를 컴퓨터에 연결](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>직렬 포트 권한 추가 – Ubuntu만

Ubuntu를 사용하는 경우 Feather HUZZAH ESP8266의 USB 포트에서 작동할 수 있는 권한이 있는지 확인합니다. 직렬 포트 권한을 추가하려면 다음 단계를 수행합니다.

1. 터미널에서 다음 명령을 실행합니다.

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   다음 출력 중 하나가 표시됩니다.

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   출력에서 `uucp` 또는 `dialout`이 USB 포트의 그룹 소유자 이름인지 확인합니다.

2. 다음 명령을 실행하여 그룹에 사용자를 추가 합니다.

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>`은 이전 단계에서 얻은 그룹 소유자 이름입니다. `<username>`은 Ubuntu 사용자 이름입니다.

3. Ubuntu에서 로그아웃한 다음 다시 로그인하면 변경 내용이 표시됩니다.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>센서 데이터를 수집하여 IoT Hub에 보내기

이 섹션에서는 Feather HUZZAH ESP8266에 대한 애플리케이션 예제를 배포하고 실행합니다. 샘플 애플리케이션은 Feather HUZZAH ESP8266의 LED를 깜박이고 DHT22 센서에서 수집된 온도 및 습도 데이터를 IoT Hub로 보냅니다.

### <a name="get-the-sample-application-from-github"></a>GitHub에서 샘플 애플리케이션 가져오기

샘플 애플리케이션은 GitHub에서 호스팅됩니다. GitHub에서 샘플 애플리케이션이 포함된 샘플 리포지토리를 복제합니다. 샘플 리포지토리를 복제하려면 다음 단계를 수행합니다.

1. 명령 프롬프트 또는 터미널 창을 엽니다.

2. 애플리케이션 예제를 저장하려는 폴더로 이동합니다.

3. 다음 명령 실행:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   다음으로 Visual Studio Code에 Feather HUZZAH ESP8266 패키지를 설치합니다.

4. 샘플 애플리케이션가 저장된 폴더를 엽니다.

5. Visual Studio Code의 앱 폴더에서 app.ino 파일을 엽니다.

   ![Visual Studio Code에서 샘플 애플리케이션 열기](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. Visual Studio Code에서 `F1`을 입력합니다.

7. 형식 **Arduino** 선택한 **Arduino: 보드 관리자**를 입력하고 선택합니다.

8. **Arduino: 보드 관리자** 탭에서 **추가 URL**을 클릭합니다.

   ![VS Code Arduino 보드 관리자](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. **사용자 설정** 창에서 다음 내용을 복사한 후 파일 끝에 붙여 넣습니다.

   ```json
   "arduino.additionalUrls": "http://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![VS Code에서 Arduino 패키지 URL 구성](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. 파일을 저장하고 **사용자 설정** 탭을 닫습니다.

11. **패키지 인덱스 새로 고침**을 클릭합니다. 새로 고침이 완료된 후 **esp8266**을 검색합니다.

12. esp8266에 대한 **설치** 단추를 클릭합니다.

    [보드 관리자]에서 ESP8266 2.2.0 이상 버전으로 설치되었음을 나타냅니다.

    ![esp8266 패키지가 설치됨](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. 입력 `F1`를 입력 한 다음 **Arduino** 선택한 **Arduino: 보드 구성**합니다.

14. **선택한 보드:** 에 대한 상자를 클릭하고 **esp8266**을 선택한 후 **Adafruit HUZZAH ESP8266(esp8266)** 을 선택합니다.

    ![esp8266 보드 선택](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>필요한 라이브러리 설치

1. Visual Studio Code에서 입력 `F1`를 입력 한 다음 **Arduino** 선택한 **Arduino: 라이브러리 관리자**합니다.

2. 다음 라이브러리 이름을 하나씩 검색합니다. 찾은 각 라이브러리에 대해 **설치**를 클릭합니다.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>실제 DHT22 센서가 없나요?

실제 DHT22 센서가 없는 경우 애플리케이션 예제에서 온도 및 습도 데이터를 시뮬레이션할 수 있습니다. 시뮬레이션된 데이터를 사용하도록 샘플 애플리케이션을 설정하려면 다음 단계를 수행합니다.

1. `app` 폴더에서 `config.h` 파일을 엽니다.

2. 다음 코드 줄을 찾아 값을 `false`에서 `true`로 변경합니다.

   ```c
   define SIMULATED_DATA true
   ```

   ![시뮬레이션된 데이터를 사용하도록 샘플 애플리케이션 구성](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. 파일을 저장합니다.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Feather HUZZAH ESP8266에 샘플 애플리케이션 배포

1. Visual Studio Code에서 클릭  **\<직렬 포트 선택 >** 상태 모음 및 Feather HUZZAH ESP8266에 대 한 직렬 포트를 클릭 합니다.

2. 입력 `F1`를 입력 한 다음 **Arduino** 선택한 **Arduino: 업로드** 을 빌드하고 Feather HUZZAH ESP8266에 샘플 응용 프로그램을 배포 합니다.

### <a name="enter-your-credentials"></a>자격 증명 입력

업로드를 성공적으로 완료했으면 다음 단계에 따라 자격 증명을 입력합니다.

1. Arduino IDE를 열고 **도구** > **직렬 모니터**를 클릭합니다.

2. 직렬 모니터 창의 오른쪽 아래 모서리에 있는 두 개의 드롭다운 목록을 확인합니다.

3. 왼쪽 드롭다운 목록에서 **No line ending**(줄 끝 없음)을 선택합니다.

4. 오른쪽 드롭다운 목록에서 **115200 baud**를 선택합니다.

5. 직렬 모니터 창 맨 위에 있는 입력 상자에 정보를 입력하라는 메시지가 표시되면 다음 정보를 입력한 후 **보내기**를 클릭합니다.

   * Wi-Fi SSID
   * Wi-Fi 암호
   * 디바이스 연결 문자열

> [!Note]
> 자격 증명 정보는 Feather HUZZAH ESP8266의 EEPROM에 저장됩니다. Feather HUZZAH ESP8266 보드에서 리셋 단추를 클릭하면 샘플 애플리케이션에서 정보를 지울 것인지 묻는 메시지가 표시됩니다. 정보를 지우려면 `Y`를 입력합니다. 정보를 다시 제공하라는 메시지가 표시됩니다.

### <a name="verify-the-sample-application-is-running-successfully"></a>애플리케이션 예제가 성공적으로 실행 중인지 확인합니다.

직렬 모니터 창에 다음 출력이 표시되고 Feather HUZZAH ESP8266의 LED가 깜박이면 애플리케이션 예제가 성공적으로 실행 중입니다.

![Arduino IDE의 최종 출력](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>허브에서 수신한 메시지 읽기

장치에서 IoT hub에서 수신한 메시지를 모니터링 하는 한 가지 방법은 Visual Studio Code 용 Azure IoT 도구를 사용 하는 것입니다. 자세한 내용은 참조 하세요 [for Visual Studio Code 장치 및 IoT Hub 간에 메시지를 받고 보내는 데 사용 하 여 Azure IoT 도구](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)합니다.

장치에서 보낸 데이터를 처리 하는 방법을 더 보려면, 다음 섹션을 진행 합니다.

## <a name="next-steps"></a>다음 단계

IoT Hub에 Feather HUZZAH ESP8266를 성공적으로 연결하고 캡처한 센서 데이터를 IoT Hub로 보냈습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]