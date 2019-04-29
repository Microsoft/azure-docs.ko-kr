---
title: Azure IoT Central 애플리케이션에 DevKit 장치 연결 | Microsoft Docs
description: 장치 개발자로서 Azure IoT Central 애플리케이션에 MXChip IoT DevKit 장치를 연결하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 82222dd927f46761941a6a750d96222cc626e71b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60887340"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 MXChip IoT DevKit 장치 연결

이 문서에서는 장치 개발자로서 MXChip IoT DevKit(DevKit) 장치를 Microsoft Azure IoT Central 애플리케이션에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료 하려면 다음 리소스가 필요 합니다.

1. **샘플 Devkits** 애플리케이션 템플릿으로 만든 Azure IoT Central 애플리케이션. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.
1. DevKit 디바이스. DevKit 디바이스를 구매하려면 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)를 방문하세요.

## <a name="sample-devkits-application"></a>샘플 Devkits 애플리케이션

만든 응용 프로그램을 **샘플 Devkits** 응용 프로그램 템플릿을 포함를 **MXChip** 다음 장치 특성을 정의 하는 장치 템플릿:

- 에 대 한 원격 분석 측정값 **습도**를 **온도**를 **압력**를 **지자기 센터** (X, Y, 따라 측정 Z 축), **가 속도계** (X, Y, 따라 측정 Z 축), 및 **자이로스코프가** (X, Y, 따라 측정 Z 축).
- 상태에 대 한 측정 **장치 상태**합니다.
- 에 대 한 이벤트 측정 **단추 B를 누르면**합니다.
- 에 대 한 설정을 **전압**, **현재**합니다 **팬 속도**, 및 **IR** 설정/해제 합니다.
- 장치 속성 **숫자 주사위** 하 고 **장치 위치**, 위치 속성인 합니다.
- 클라우드 속성 **제조에서**합니다.
- 명령을 **Echo** 하 고 **카운트다운**합니다. 실제 장치를 받으면를 **Echo** 명령을 보여 줍니다 보낸된 값 장치의 디스플레이에서. 실제 장치를 받으면를 **카운트다운** LED 주기 패턴을 통해 명령을 실행 하 고 장치는 IoT Central 돌아가기 카운트다운 값을 보냅니다.

구성에 대 한 전체 내용은 참조 하세요. [MXChip 장치 템플릿 세부 정보](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>실제 디바이스 추가

### <a name="get-your-device-connection-details"></a>장치 연결 정보 가져오기

Azure IoT Central 응용 프로그램에서 실제 장치를 추가 합니다 **MXChip** 장치 템플릿 및 장치 연결 세부 정보를 기록 합니다. **ID, 장치 ID 및 기본 키 범위**:

1. 추가 **실제 장치** Device Explorer 선택 **+ 새로 만들기 > 실제** 실제 장치를 추가 합니다.

    * 소문자 입력 **장치 ID**, 제안 된 사용할지 **장치 ID**합니다.
    * 입력 한 **장치 이름**, 제안 된 이름을 사용 하거나

    ![디바이스 추가](media/howto-connect-devkit/add-device.png)

1. 장치 연결 세부 정보를 가져오려면 **범위 ID**, **장치 ID**, 및 **Primary key**를 선택 **Connect** 장치 페이지에서.

    ![연결 정보](media/howto-connect-devkit/device-connect.png)

1. 연결 세부 정보를 기록해 둡니다. 일시적으로 연결이 끊긴 인터넷에서 다음 단계에 DevKit 장치를 준비 합니다.

### <a name="prepare-the-devkit-device"></a>DevKit 디바이스 준비

이전에 사용한 경우 장치 및 원하는 다른 WiFi 네트워크, 연결 문자열 또는 원격 분석 측정을 사용 하도록 다시 구성, 키를 눌러 모두를 **A** 및 **B** 동시 단추입니다. 작동 하지 않으면 키를 눌러 **재설정** 단추 및 다시 시도 하세요.

#### <a name="to-prepare-the-devkit-device"></a>DevKit 디바이스를 준비하려면

1. GitHub의 [릴리스](https://aka.ms/iotcentral-docs-MXChip-releases) 페이지에서 MXChip의 최신 사전 빌드 Azure IoT Central 펌웨어를 다운로드합니다.
1. USB 케이블을 사용하여 DevKit 디바이스를 개발 컴퓨터에 연결합니다. Windows에서, DevKit 디바이스의 저장소에 매핑된 드라이브에서 파일 탐색기 창이 열립니다. 예를 들어 드라이브 이름이 **AZ3166 (D:)** 일 수 있습니다.
1. **iotCentral.bin** 파일을 드라이브 창으로 끌어다 놓습니다. 복사가 완료되면 새 펌웨어를 사용하여 디바이스가 다시 부팅됩니다.

1. DevKit 디바이스가 다시 시작되면 다음 화면이 표시됩니다.

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > 화면이 다른 요소를 표시하는 경우 디바이스를 다시 설정하고 디바이스에서 **A** 및 **B** 단추를 동시에 눌러 디바이스를 다시 부팅합니다.

1. 이제 디바이스가 AP(액세스 지점) 모드에 있습니다. 컴퓨터 또는 모바일 디바이스에서 이 WiFi 액세스 지점에 연결할 수 있습니다.

1. 컴퓨터, 휴대폰 또는 태블릿에서 디바이스 화면에 표시된 WiFi 네트워크 이름에 연결합니다. 이 네트워크에 연결할 때 인터넷에 액세스할이 필요가 없습니다. 이 상태는 예상 하 고 장치를 구성 하는 동안만 짧은 시간에 대해이 네트워크에 연결 하는 키를 누릅니다.

1. 웹 브라우저를 열고 [http://192.168.0.1/start](http://192.168.0.1/start)로 이동합니다. 다음 웹 페이지가 표시됩니다.

    ![디바이스 구성 페이지](media/howto-connect-devkit/configpage.png)

    웹 페이지에서 다음을 입력 합니다.
    - WiFi 네트워크의 이름
    - WiFi 네트워크 암호
    - 장치의 디스플레이에 표시 된 PIN 코드
    - 연결 세부 정보 **범위 ID**를 **장치 ID**, 및 **기본 키** (해야에 이미 저장이 단계를 수행) 장치
    - 모든 사용 가능한 원격 분석 값을 선택

1. **디바이스 구성**을 선택하면 이 페이지가 표시됩니다.

    ![구성된 디바이스](media/howto-connect-devkit/deviceconfigured.png)

1. 디바이스의 **재설정** 단추를 누릅니다.

## <a name="view-the-telemetry"></a>원격 분석 보기

DevKit 디바이스가 다시 시작되면 디바이스에 다음과 같은 화면이 표시됩니다.

* 전송된 원격 분석 메시지 수.
* 실패 횟수.
* 받은 desired 속성 수 및 보낸 reported 속성 수.

> [!NOTE]
> 장치를 연결 하려고 할 때 나타나는 경우 장치 인지 확인 **Blocked** IoT Central의 및 **차단 해제** 장치 앱에 연결할 수 있습니다.

Reported 속성을 보내는 장치를 흔들어. 디바이스에서 **다이 번호** 디바이스 속성으로 임의의 숫자를 보냅니다.

원격 분석 측정값 및 reported 속성 값을 확인하고, Azure IoT Central에서 설정을 구성할 수 있습니다.

1. **Device Explorer**를 사용하여 추가한 실제 MXChip 디바이스에 대한 **측정값** 페이지로 이동합니다.

    ![실제 디바이스로 이동](media/howto-connect-devkit/realdevicenew.png)

1. **측정값** 페이지에서 MXChip 디바이스에서 보낸 원격 분석 데이터를 볼 수 있습니다.

    ![실제 디바이스의 원격 분석 데이터 보기](media/howto-connect-devkit/devicetelemetrynew.png)

1. **속성** 페이지에서는 디바이스에서 보고된 마지막 다이 번호 및 디바이스 위치를 볼 수 있습니다.

    ![디바이스 속성 보기](media/howto-connect-devkit/devicepropertynew.png)

1. **설정** 페이지에서 MXChip 디바이스의 설정을 업데이트할 수 있습니다.

    ![디바이스 설정 보기](media/howto-connect-devkit/devicesettingsnew.png)

1. 에 **명령** 페이지를 호출할 수 있습니다 합니다 **Echo** 및 **카운트다운** 명령:

    ![호출 명령](media/howto-connect-devkit/devicecommands.png)

1. **대시보드** 페이지에서는 위치 지도를 볼 수 있습니다.

    ![디바이스 대시보드 보기](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>소스 코드 다운로드

디바이스 코드를 탐색하고 수정하려는 경우 GitHub에서 디바이스 코드를 다운로드하면 됩니다. 코드를 수정하려는 경우 다음 지침에 따라 사용 중인 데스크톱 운영 체제의 [개발 환경을 준비](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment)해야 합니다.

소스 코드를 다운로드하려면 데스크톱 컴퓨터에서 다음 명령을 실행합니다.

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

이전 명령은 `iot-central-firmware` 폴더에 소스 코드를 다운로드합니다.

> [!NOTE]
> 개발 환경에 **git**이 설치되지 않은 경우 [https://git-scm.com/download](https://git-scm.com/download)에서 다운로드할 수 있습니다.

## <a name="review-the-code"></a>코드 검토

열려는 Visual Studio Code를 사용 합니다 `MXCHIP/mxchip_advanced` 폴더에는 `iot-central-firmware` 폴더:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Azure IoT Central 응용 프로그램에는 원격 분석이 전송 되는 방법을 보려면를 엽니다는 **telemetry.cpp** 파일을 `src` 폴더:

- `TelemetryController::buildTelemetryPayload` 함수는 디바이스의 센서 데이터를 사용하여 JSON 원격 분석 페이로드를 만듭니다.

- 함수 `TelemetryController::sendTelemetryPayload` 호출 `sendTelemetry` 에 **AzureIOTClient.cpp** Azure IoT Central 응용 프로그램 사용 하는 IoT Hub에 JSON 페이로드를 보내기 위해.

Azure IoT Central 응용 프로그램에 속성 값을 보고 하는 방법을 보려면를 엽니다는 **telemetry.cpp** 파일을 `src` 폴더:

- 함수 `TelemetryController::loop` 보냅니다 합니다 **위치** 약 30 초 마다 속성을 보고 합니다. 사용 된 `sendReportedProperty` 함수를 **AzureIOTClient.cpp** 소스 파일입니다.

- 함수 `TelemetryController::loop` 보냅니다 합니다 **dieNumber** 장치가 속도계 감지를 두 번 탭 하면 속성을 보고 합니다. 사용 된 `sendReportedProperty` 함수를 **AzureIOTClient.cpp** 소스 파일입니다.

장치에서 IoT Central 응용 프로그램에서 호출 하는 명령에 응답 하는 방법을 보려면을 엽니다는 **registeredMethodHandlers.cpp** 파일을 `src` 폴더:

- 합니다 **dmEcho** 함수에 대 한 처리기는 합니다 **echo** 명령입니다. 표시 합니다 **displayedValue** 장치 화면의 페이로드에 제출 합니다.

- 합니다 **dmCountdown** 함수에 대 한 처리기는 합니다 **카운트다운** 명령입니다. 장치의 LED의 색상을 변경 하 고 reported 속성을 사용 하 여 카운트다운 값 IoT Central 응용 프로그램에 다시 보냅니다. Reported 속성의 명령과 동일한 이름이 있습니다. 이 함수를 사용 합니다 `sendReportedProperty` 함수는 **AzureIOTClient.cpp** 소스 파일입니다.

코드를 **AzureIOTClient.cpp** 소스 파일에서 함수를 사용 합니다 [Microsoft Azure IoT Sdk 및 C 라이브러리](https://github.com/Azure/azure-iot-sdk-c) IoT Hub와 상호 작용을 합니다.

샘플 코드를 수정하고, 빌드하고, 디바이스에 업로드하는 방법은 `MXCHIP/mxchip_advanced` 폴더에 있는 **readme.md** 파일을 참조하세요.

## <a name="mxchip-device-template-details"></a>MXChip 디바이스 템플릿 세부 정보

샘플 Devkits 애플리케이션 템플릿으로 만든 애플리케이션에는 다음과 같은 특징을 가진 MXChip 장치가 포함됩니다.

### <a name="measurements"></a>측정값

#### <a name="telemetry"></a>원격 분석

| 필드 이름     | Units  | 최소 | 최대 | 소수 자릿수 |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>상태 
| 이름          | 표시 이름   | 정상 | 주의 | 위험 | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | 디바이스 상태   | 녹색  | 주황색  | 빨강    | 

#### <a name="events"></a>이벤트 
| 이름             | 표시 이름      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | 단추 B 누름  | 

### <a name="settings"></a>설정

숫자 설정

| 표시 이름 | 필드 이름 | Units | 소수 자릿수 | 최소 | 최대 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 전압      | setVoltage | 볼트 | 0              | 0       | 240     | 0       |
| Current      | setCurrent | 암페어  | 0              | 0       | 100     | 0       |
| 팬 속도    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

설정 전환

| 표시 이름 | 필드 이름 | 온 텍스트 | 오프 텍스트 | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 켜기      | 끄기      | 꺼짐     |

### <a name="properties"></a>properties

| Type            | 표시 이름 | 필드 이름 | 데이터 형식 |
| --------------- | ------------ | ---------- | --------- |
| 디바이스 속성 | 다이 번호   | dieNumber  | number    |
| 디바이스 속성 | 디바이스 위치   | location  | location    |
| Text            | 제조 일자     | manufacturedIn   | N/A       |

### <a name="commands"></a>명령

| 표시 이름 | 필드 이름 | 반환 형식 | 입력된 필드의 표시 이름 | 입력된 필드 이름 | 입력된 필드 형식 |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| echo         | echo       | text        | 표시할 값         | displayedValue   | text             |
| 카운트다운    | 카운트다운  | number      | 계산               | countFrom        | number           |

## <a name="next-steps"></a>다음 단계

제안 된 다음 단계에 알아보려면는 이제 Azure IoT Central 응용 프로그램을 Raspberry Pi를 연결 하는 방법을 배웠으므로 하는 방법 [템플릿으로 사용자 지정 장치 설정](howto-set-up-template.md) 사용자 고유의 IoT 장치에 대 한 합니다.
