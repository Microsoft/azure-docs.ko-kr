---
title: IoT DevKit에서 클라우드로 - Azure IoT Hub에 IoT MXChip DevKit 연결 | Microsoft Docs
description: 이 자습서에서는 IoT DevKit AZ3166의 센서 상태를 Azure IoT 원격 모니터링 솔루션 가속기로 보내는 방법을 알아봅니다.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: ae8dc263e08528c6e3b3bae8c779162c96d51f43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61324625"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>MXChip IoT DevKit를 Azure IoT 원격 모니터링 솔루션 가속기에 연결

이 자습서에서는 DevKit에서 샘플 앱을 실행하여 Azure IoT 원격 모니터링 솔루션 가속기에 센서 데이터를 보내는 방법을 알아봅니다.

[MXChip IoT DevKit](https://aka.ms/iot-devkit)은 풍부한 주변 장치 및 센서가 포함된 올인원 Arduino 호환 보드입니다. [Arduino용 Visual Studio Code 확장](https://aka.ms/arduino)을 사용하여 개발할 수 있습니다. 그리고 Microsoft Azure 서비스를 활용하는 IoT(사물 인터넷) 솔루션의 프로토타입을 안내하기 위해 확장 중인 [프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)가 제공됩니다.

## <a name="what-you-need"></a>필요한 항목

[시작 가이드](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)를 완료하여:

* DevKit을 Wi-Fi에 연결
* 개발 환경 준비

활성 Azure 구독. 구독이 없으면 다음 두 가지 방법 중 하나를 통해 등록할 수 있습니다.

* [30일 평가판 Microsoft Azure 계정](https://azure.microsoft.com/free/) 활성화

* MSDN 또는 Visual Studio 구독자인 경우 [Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 신청합니다.

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Azure IoT 원격 모니터링 솔루션 가속기 만들기

1. [Azure IoT 솔루션 가속기 사이트](https://www.azureiotsolutions.com/)로 이동하여 **새 솔루션 만들기**를 클릭합니다.

   ![Azure IoT 솔루션 가속기 유형 선택](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > 기본적으로 이 샘플은 하나의 IoT 원격 모니터링 솔루션 가속기를 만든 후 S2 IoT Hub를 만듭니다. 이 IoT Hub가 다수의 디바이스에 사용되지 않는 경우 S2에서 S1로 다운그레이드하고 더 이상 필요 없는 관련 IoT Hub도 삭제되도록 IoT 원격 모니터링 솔루션 가속기를 삭제하는 것이 좋습니다. 

2. **원격 모니터링**을 선택합니다.

3. 솔루션 이름을 입력하고 구독 및 지역을 선택한 다음, **솔루션 만들기**를 클릭합니다. 솔루션을 프로비전하는 데 시간이 걸릴 수 있습니다.
  
   ![솔루션 만들기](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. 프로비전을 완료한 후 **시작**을 클릭합니다. 프로비전 프로세스 도중에 솔루션에 대해 시뮬레이션된 일부 디바이스가 생성됩니다. **디바이스**를 클릭하여 체크 아웃합니다.

   ![대시보드](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![콘솔](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. **디바이스 추가**를 클릭합니다.

6. **사용자 지정 디바이스**에 **새로 추가**를 클릭합니다.
  
   ![새 디바이스 추가](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. **직접 나만의 디바이스 ID 정의**를 클릭하고 `AZ3166`을 입력한 다음, **만들기**를 클릭합니다.
  
   ![ID가 있는 디바이스 만들기](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. **IoT Hub 호스트 이름**을 메모하고 **완료**를 클릭합니다.

## <a name="open-the-remotemonitoring-sample"></a>RemoteMonitoring 샘플 열기

1. DevKit가 컴퓨터에 연결되어 있으면 연결을 끊습니다.

2. VS Code를 시작합니다.

3. DevKit를 컴퓨터에 연결합니다. VS Code가 DevKit를 자동으로 감지하고 다음 페이지를 엽니다.

   * DevKit 소개 페이지
   * Arduino 예제: DevKit를 시작하기 위한 실습 예제

4. 왼쪽의 **ARDUINO 예제** 섹션을 확장하고 **MXCHIP AZ3166에 대한 예제 > AzureIoT**로 이동하여 **RemoteMonitoring**을 선택합니다. 프로젝트 폴더가 있는 새 VS Code 창이 열립니다.

   > [!NOTE]
   > 창을 닫은 경우 다시 열면 됩니다. `Ctrl+Shift+P`(macOS: `Cmd+Shift+P`)를 사용하여 명령 팔레트를 호출하고 **Arduino**를 입력한 다음, **Arduino: 예제**를 찾아서 선택합니다.

## <a name="provision-required-azure-services"></a>필요한 Azure 서비스 프로비전

솔루션 창에서 제공된 텍스트 상자에 `task cloud-provision`을 입력하여 `Ctrl+P`(macOS: `Cmd+P`)를 통해 작업을 실행합니다.

VS Code 터미널에서 대화형 명령줄은 필요한 Azure 서비스를 프로비전하는 과정을 안내합니다.

![Azure 리소스 프로비전](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>디바이스 코드 빌드 및 업로드

1. `Ctrl+P`(macOS: `Cmd + P`)를 사용하고 **task config-device-connection**을 입력합니다.

2. `task cloud-provision`단계에서 검색하는 연결 문자열을 사용할지 묻는 메시지가 터미널에 표시됩니다. '새로 만들기...'를 클릭하여 자체 디바이스 연결 문자열을 입력할 수도 있습니다.

3. 터미널에서 구성 모드를 입력하라는 메시지를 표시합니다. 이렇게 하려면 단추 A를 누르고 있다가 다시 설정 단추를 밀어서 놓습니다. 화면에 DevKit ID와 '구성'이 표시됩니다.

   ![연결 문자열 입력](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. `task config-device-connection`이 완료되면 `F1`을 클릭하여 VS Code 명령을 로드하고 `Arduino: Upload`를 선택합니다. VS Code가 Arduino 스케치를 확인하고 업로드하기 시작합니다.
  
   ![Arduino 스케치 확인 및 업로드](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit는 다시 부팅하고 코드를 실행하기 시작합니다.

## <a name="test-the-project"></a>프로젝트 테스트

샘플 앱을 실행하면 DevKit에서 WiFi를 통해 센서 데이터를 Azure IoT 원격 모니터링 솔루션 가속기로 보냅니다. 결과를 보려면 다음 단계를 수행합니다.

1. Azure IoT 원격 모니터링 솔루션 가속기로 이동하고 **대시보드**를 클릭합니다.

2. 원격 모니터링 솔루션 콘솔에 DevKit 센서 상태가 표시됩니다.

   ![Azure IoT 원격 모니터링 솔루션 가속기의 센서 데이터](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>디바이스 ID 변경

코드에서 사용자 지정된 디바이스 ID로 하드코딩된 **AZ3166**을 변경하려는 경우 [원격 모니터링 예제](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23)에 표시된 코드 줄을 수정합니다.

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생하면 [IoT 개발자 키트 FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 참조하거나 다음 지원 채널을 사용하여 문의해주세요.

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [스택 오버플로](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>다음 단계

DevKit 디바이스를 Azure IoT 원격 모니터링 솔루션 가속기에 연결하고 센서 데이터를 시각화하는 방법을 알아보았습니다. 다음 제안 단계는 다음과 같습니다.

* [Azure IoT 솔루션 가속기 개요](https://docs.microsoft.com/azure/iot-suite/)

* [Azure IoT Central 애플리케이션에 MXChip IoT DevKit 장치 연결](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)

* [IoT 개발자 키트](https://microsoft.github.io/azure-iot-developer-kit/) 
