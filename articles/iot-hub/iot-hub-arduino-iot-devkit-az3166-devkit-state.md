---
title: Azure 디바이스 쌍을 사용하여 MXChip IoT DevKit 사용자 LED 제어 | Microsoft Docs
description: 이 자습서에서는 Azure IoT Hub 디바이스 쌍으로 DevKit 상태를 모니터링하고 사용자 LED를 제어하는 방법을 설명합니다.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: e955d21132dda6caa137ad3b5de9d00ccf7ed1b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369851"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

이 예제를 사용하여 MXChip IoT DevKit WiFi 정보 및 센서 상태를 모니터링하고 Azure IoT Hub 디바이스 쌍을 사용하여 사용자 LED의 색상을 제어할 수 있습니다.

## <a name="what-you-learn"></a>학습 내용

- MXChip IoT DevKit 센서 상태를 모니터링하는 방법

- Azure 디바이스 쌍을 사용하여 DevKit의 RGB LED 색을 제어하는 방법

## <a name="what-you-need"></a>필요한 항목

- [시작 가이드](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)에 따라 개발 환경을 설정합니다.

- GitBash 터미널 창(또는 다른 Git 명령줄 인터페이스)에 다음 명령을 입력합니다.

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Azure 서비스 프로비전

1. Visual Studio Code에서 **작업** 드롭다운 메뉴를 클릭하고 **작업 실행...** - **cloud-provision**을 선택합니다.

2. **시작** 패널의 **터미널** 탭 아래에 진행률이 표시됩니다.

3. *What subscription would you like to choose*(어떤 구독을 선택하시겠습니까?) 메시지가 표시되면 구독을 선택합니다.

4. 리소스 그룹을 선택합니다. 
 
   > [!NOTE]
   > 무료 IoT Hub가 이미 있는 경우 이 단계를 건너뜁니다.

5. *What IoT hub would you like to choose*(어떤 IoT 허브를 선택하시겠습니까?) 메시지가 표시되면 IoT Hub를 선택하거나 만듭니다.

6. *함수 앱: 함수 앱 이름: xxx*와 유사한 내용이 표시됩니다. 함수 앱 이름을 적어둡니다. 이후 단계에서 사용됩니다.

7. Azure Resource Manager 템플릿 배포를 마칠 때까지 기다립니다. 완료를 나타내는 *Resource Manager 템플릿 배포: 완료* 메시지가 표시됩니다.

## <a name="deploy-function-app"></a>Function App 배포

1. Visual Studio Code에서 **작업** 드롭다운 메뉴를 클릭하고 **작업 실행...** - **cloud-deploy**를 선택합니다.

2. 함수 앱 코드 업로드 프로세스가 완료될 때까지 기다리면 *함수 앱 배포: 완료* 메시지가 표시됩니다.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>DevKit에서 IoT Hub 디바이스 연결 문자열 구성

1. MXChip IoT DevKit를 컴퓨터에 연결합니다.

2. Visual Studio Code에서 **작업** 드롭다운 메뉴를 클릭하고 **작업 실행...** - **config-device-connection**을 선택합니다.

3. MXChip IoT DevKit에서 **A** 단추를 길게 누르고 **재설정** 단추를 누른 다음, **A** 단추를 놓으면 DekKit가 구성 모드로 전환됩니다.

4. 연결 문자열 구성 프로세스가 완료될 때까지 기다립니다.

## <a name="upload-arduino-code-to-devkit"></a>DevKit에 Arduino 코드 업로드

MXChip IoT DevKit를 컴퓨터에 연결한 상태로:

1. Visual Studio Code에서 **작업** 드롭다운 메뉴를 클릭하고 **빌드 작업 실행...** 을 선택합니다. Arduino 스케치가 컴파일되어 DevKit에 업로드됩니다.

2. 스케치가 성공적으로 업로드되면 *Build & Upload Sketch: success*(스케치 빌드 및 업로드: 성공) 메시지가 표시됩니다.

## <a name="monitor-devkit-state-in-browser"></a>브라우저에서 DevKit 상태 모니터링

1. 웹 브라우저에서 `DevKitState\web\index.html` 파일을 엽니다. 이 파일은 필요한 항목 단계에서 만든 파일입니다.

2. 다음 웹 페이지가 나타납니다.![함수 앱 이름을 지정합니다.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. 앞서 적어둔 함수 앱 이름을 입력합니다.

4. **연결** 단추를 클릭합니다.

5. 몇 초 안에 페이지가 새로 고쳐지고 DevKit의 WiFi 연결 상태와 각 온보드 센서의 상태가 표시됩니다.

## <a name="control-the-devkits-user-led"></a>DevKit의 사용자 LED 제어

1. 웹 페이지 그림에서 사용자 LED 그래픽을 클릭합니다.

2. 몇 초 내에 화면이 새로 고쳐지고 사용자 LED의 현재 색상 상태가 표시됩니다.

3. RGB 슬라이더 컨트롤의 여러 위치를 클릭하여 RGB LED의 색상 값을 변경해 봅니다.

## <a name="example-operation"></a>예제 작업

![예제 테스트 절차](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Azure Portal에 디바이스 쌍의 원시 데이터 IoT Hub -\> IoT 디바이스 -\> *\<고객의 디바이스\>* -\> Device 쌍이 보입니다.

## <a name="next-steps"></a>다음 단계

다음 방법에 대해 알아보았습니다.
- MXChip IoT DevKit 디바이스를 Azure IoT 원격 모니터링 솔루션 가속기에 연결합니다.
- Azure IoT 디바이스 쌍 함수를 사용하여 DevKit의 RGB LED 색상을 감지하고 제어합니다.

권장되는 단계는 다음과 같습니다.

* [Azure IoT 원격 모니터링 솔루션 가속기 개요](https://docs.microsoft.com/azure/iot-suite/)
* [Azure IoT Central 애플리케이션에 MXChip IoT DevKit 장치 연결](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
