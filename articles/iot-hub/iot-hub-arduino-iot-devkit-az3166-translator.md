---
title: Azure Functions, Cognitive Services, IoT DevKit를 사용 하 여 변환기 만들기
description: IoT DevKit에서 마이크를 사용하여 음성 메시지를 수신한 다음, 영어로 번역된 텍스트로 처리하기 위해 Azure Cognitive Services를 사용합니다.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: 412a3e78006d263858ff0e28af52cf11bf44c7bb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004343"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Azure Functions 및 Cognitive Services에서 IoT DevKit AZ3166을 사용하여 언어 번역기 만들기

이 아티클에서는 [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)를 사용하여 IoT DevKit을 언어 번역기로 사용하는 방법을 알아봅니다. 음성을 기록하고 DevKit 화면에 표시된 영어 텍스트로 변환합니다.

[MXChip IoT DevKit](https://aka.ms/iot-devkit)은 풍부한 주변 장치 및 센서가 포함된 올인원 Arduino 호환 보드입니다. Visual Studio Code에서 [Azure IoT Device Workbench](https://aka.ms/iot-workbench) 또는 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 확장 팩을 사용하여 개발할 수 있습니다. [프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)에는 IoT 솔루션을 프로토타입하는 데 도움이 되는 샘플 애플리케이션이 포함되어 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서를 완료하려면 먼저 다음 작업을 수행합니다.

* [클라우드에서 Azure IoT Hub에 IoT DevKit AZ3166 연결](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)의 단계를 수행하여 DevKit를 준비합니다.

## <a name="create-azure-cognitive-service"></a>Azure Cognitive Service 만들기

1. Azure Portal에서 **리소스 만들기** 를 클릭 하 고 **음성을**검색 합니다. 양식을 작성하여 음성 서비스를 만듭니다.
  ![Speech Service](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. 방금 만든 Speech Service로 이동하고 **키** 섹션을 클릭하여 복사한 다음, 이 키에 액세스하기 위한 DevKit의 **Key1**을 적어 둡니다.
  ![키 복사](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>샘플 프로젝트 열기

1. IoT DevKit가 컴퓨터에 연결되어 있지 **않은지** 확인합니다. VS Code를 시작하고 DevKit를 컴퓨터에 연결합니다.

1. `F1` 키를 클릭하여 명령 팔레트를 열고, **Azure IoT Device Workbench: 예제 열기...** 를 입력하고 선택합니다. 보드로 **IoT DevKit**를 선택합니다.

1. IoT 워크 벤치 예제 페이지에서 **Devkit 번역기** 를 찾아 **샘플 열기**를 클릭 합니다. 기본 경로를 선택하여 샘플 코드를 다운로드합니다.
  ![샘플 열기](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Azure Functions에서 Speech Service 사용

1. VS Code에서를 클릭 하 `F1` 고 **Azure IoT 장치 워크 벤치: Azure 서비스** ![ 프로 비전 ...을 입력 하 고 선택 합니다. Azure 서비스 프로 비전](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. 다음 단계에 따라 Azure IoT Hub 및 Azure Functions의 프로비전을 완료합니다.
   ![단계 프로비전](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   만들었던 Azure IoT Hub 디바이스 이름을 적어 둡니다.

1. 에서 적어 `Functions\DevKitTranslatorFunction.cs` 둔 장치 이름 및 음성 서비스 키를 사용 하 여 다음 코드 줄을 열고 업데이트 합니다.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. `F1`을 클릭 하 고 **Azure IoT 장치 워크 벤치: Azure에 배포**...를 입력 하 고 선택 합니다. 다시 배포 확인을 묻는 메시지가 표시 되 VS Code 면 **예**를 클릭 합니다.
   ![배포 경고](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. 배포가 성공했는지 확인합니다.
   ![배포 성공](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Azure Portal에서 **함수 앱** 섹션으로 이동한 다음, 방금 만든 Azure 함수 앱을 찾습니다. `devkit_translator`를 클릭한 다음, **</> Get Function URL**(함수 URL 가져오기)을 클릭하여 URL을 복사합니다.
   ![함수 URL 복사](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. URL을 `azure_config.h` 파일에 붙여넣습니다.
   ![Azure 구성](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > 함수 앱이 제대로 작동하지 않는 경우 이 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) 섹션을 확인하여 문제를 해결합니다.

## <a name="build-and-upload-device-code"></a>디바이스 코드 빌드 및 업로드

1. 다음을 수행하여 DevKit를 **구성 모드**로 전환합니다.
   * **A** 단추를 누르고 있습니다.
   * **다시 설정** 단추를 눌렀다가 놓습니다.

   화면에 DevKit ID와 **구성**이 표시됩니다.

   ![DevKit 구성 모드](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. `F1`을 클릭 하 고 **Azure IoT 장치 워크 벤치: 장치 설정 구성 ... > 구성 장치 연결 문자열**을 입력 하 고 선택 합니다. **Select IoT Hub Device Connection String**(IoT Hub 디바이스 연결 문자열 선택)을 선택하여 DevKit로 구성합니다.
   ![연결 문자열 구성](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. 완료되면 알림이 표시됩니다.
   ![연결 문자열 구성 성공](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. `F1` 키를 다시 클릭하고, **Azure IoT Device Workbench: 디바이스 코드 업로드**를 입력하고 선택합니다. 컴파일을 시작하고 코드를 DevKit로 업로드합니다.
   ![디바이스 업로드](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>프로젝트 테스트

앱 초기화 후 DevKit 화면의 지침을 따릅니다. 기본 소스 언어는 중국어입니다.

번역을 위해 다른 언어를 선택하려면 다음과 같이 합니다.

1. 단추 A를 눌러 설정 모드로 전환합니다.

2. 모든 지원되는 소스 언어를 스크롤하려면 단추 B를 누릅니다.

3. 단추 A를 눌러서 소스 언어의 선택 내용을 확인합니다.

4. 말하는 동안 단추 B를 길게 누른 다음 단추 B를 해제하여 번역을 시작합니다.

5. 영어로 번역된 텍스트가 화면에 표시됩니다.

![스크롤하여 언어 선택](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![변환 결과](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

번역 결과 화면에서 다음을 수행할 수 있습니다.

- 단추 A와 B를 눌러 스크롤하여 소스 언어를 선택합니다.

- 단추 B를 누르고 말합니다. 음성을 보내고 번역 텍스트를 가져오려면 B 단추를 해제합니다.

## <a name="how-it-works"></a>작동 방법

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

IoT DevKit는 음성을 기록한 다음, HTTP 요청을 게시하여 Azure Functions를 트리거합니다. Azure Functions는 Cognitive Service 음성 변환기 API를 호출하여 변환 작업을 수행합니다. Azure Functions가 변환 텍스트를 가져온 후에 디바이스에 C2D 메시지를 보냅니다. 그런 다음, 변환이 화면에 표시됩니다.

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생하면 [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 참조하거나 다음 지원 채널을 사용하여 문의해주세요.

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [스택 오버플로](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>다음 단계

Azure Functions 및 Cognitive Services를 사용하여 IoT DevKit를 번역기로 사용하는 방법을 배웠습니다. 이 방법에서는 다음 방법을 배웁니다.

> [!div class="checklist"]
> * Visual Studio Code 작업을 사용하여 클라우드 프로비전 자동화
> * Azure IoT 디바이스 연결 문자열 구성
> * Azure Function 배포
> * 음성 메시지 변환 테스트

자세히 알아보려면 다른 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [IoT DevKit AZ3166을 Azure IoT 원격 모니터링 솔루션 가속기에 연결](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
