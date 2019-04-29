---
title: Azure Functions를 사용하여 Twitter 메시지 검색
description: 동작 센서를 사용하여 흔들기를 감지하고 Azure Functions를 사용하여 사용자가 지정하는 해시태그가 달린 임의의 트윗을 찾습니다.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: dc4ff35ff04680e8635d54c25212c8ae639ae472
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60779838"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>흔들기, 흔들어서 트윗 찾기 -- Azure Functions를 사용하여 Twitter 메시지 검색

이 프로젝트에서는 동작 센서를 사용하여 Azure Functions를 사용하는 이벤트를 트리거하는 방법을 알아봅니다. 앱은 사용자가 Arduino 스케치에서 구성한 해시태그가 달린 임의의 트윗을 검색합니다. 트윗은 DevKit 화면에 표시됩니다.

## <a name="what-you-need"></a>필요한 항목

[시작 가이드](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)를 완료하여:

* DevKit을 Wi-Fi에 연결.
* 개발 환경 준비.

활성 Azure 구독. 아직 구독이 없으면 다음 방법 중 하나를 통해 등록할 수 있습니다.

* [30일 평가판 Microsoft Azure 계정](https://azure.microsoft.com/free/) 활성화
* MSDN 또는 Visual Studio 구독자인 경우 [Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 신청합니다.

## <a name="open-the-project-folder"></a>프로젝트 폴더 열기

프로젝트 폴더를 열어 시작합니다. 

### <a name="start-vs-code"></a>VS Code 시작

* DevKit가 컴퓨터에 연결되어 있는지 확인합니다.

* VS Code를 시작합니다.

* DevKit를 컴퓨터에 연결합니다.

   > [!NOTE]
   > VS Code를 시작할 때 Arduino IDE 또는 관련 보드 패키지를 찾을 수 없다는 오류 메시지가 나타날 수 있습니다. 이 오류가 발생할 경우 VS Code를 닫고 Arduino IDE를 다시 시작합니다. 이제 VS Code가 Arduino IDE 경로를 제대로 찾을 것입니다.

### <a name="open-the-arduino-examples-folder"></a>Arduino 예제 폴더 열기

왼쪽의 **ARDUINO 예제** 섹션을 확장하고 **MXCHIP AZ3166에 대한 예제 > AzureIoT**로 이동하여 **ShakeShake**를 선택합니다. 프로젝트 폴더를 표시하는 새 VS Code 창이 열립니다. MXCHIP AZ3166 섹션을 볼 수 없다면 디바이스가 제대로 연결되었는지 확인하고 Visual Studio Code를 다시 시작합니다.  
![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

명령 팔레트에서 샘플 프로젝트를 열 수도 있습니다. `Ctrl+Shift+P`(macOS: `Cmd+Shift+P`)를 클릭하여 명령 팔레트를 호출하고 **Arduino**를 입력한 다음, **Arduino: 예제**를 찾아서 선택합니다.

## <a name="provision-azure-services"></a>Azure 서비스 프로비전

솔루션 창에서 `task cloud-provision`을 입력하여 `Ctrl+P`(macOS: `Cmd+P`)를 통해 해당 작업을 실행합니다.

VS Code 터미널에서 대화형 명령줄은 필요한 Azure 서비스를 프로비전하는 과정을 안내합니다.

![클라우드 프로비전](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Azure에 로그인하려고 시도하면 페이지가 로딩 상태에서 중지되는 경우 [IoT DevKit FAQ의 "로그인 페이지가 중지됨" 단계](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure)를 참조하세요.
 
## <a name="modify-the-hashtag"></a>#해시태그 수정

`ShakeShake.ino`를 열고 이 코드 줄을 찾습니다.

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

중괄호 안의 `iot` 문자열을 원하는 해시태그로 바꿉니다. 나중에 DevKit는 사용자가 이 단계에서 지정한 해시태그가 포함된 임의의 트윗을 검색합니다.

## <a name="deploy-azure-functions"></a>Azure Functions 배포

`Ctrl+P`(macOS: `Cmd+P`)를 사용하여 `task cloud-deploy` 명령을 실행하고 Azure Functions 코드 배포를 시작합니다.

![클라우드 배포](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> 경우에 따라 Azure 함수가 제대로 작동하지 않을 수 있습니다. 문제가 발생하는 경우 이를 해결하려면 [IoT DevKit FAQ의 "컴파일 오류" 섹션](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function)을 확인합니다.

## <a name="build-and-upload-the-device-code"></a>디바이스 코드 빌드 및 업로드

다음으로 디바이스 코드를 빌드 및 업로드합니다.

### <a name="windows"></a>Windows

1. `Ctrl+P`를 사용하여 `task device-upload`를 실행합니다.

2. 터미널에서 구성 모드를 입력하라는 메시지를 표시합니다. 이렇게 하려면 다음을 수행합니다.

   * A 단추를 길게 누르기

   * 재설정 단추를 눌렀다가 놓습니다.

3. 화면에 DevKit ID와 '구성'이 표시됩니다.

### <a name="macos"></a>macOS

1. DevKit를 구성 모드로 전환합니다.

   단추 A를 누르고 있다가 다시 설정 단추를 밀어서 놓습니다. 화면에는 '구성'이 표시됩니다.

2. `Cmd+P`를 사용하여 `task device-upload` 명령을 실행하고 `task cloud-provision` 단계에서 검색되는 연결 문자열을 설정합니다.

### <a name="verify-upload-and-run"></a>확인, 업로드 및 실행

이제 연결 문자열이 설정되고, 앱을 확인 및 업로드한 다음, 실행합니다. 

1. VS Code가 Arduino 스케치를 확인하고 DevKit에 업로드하기 시작합니다.

   ![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. DevKit는 다시 부팅하고 코드를 실행하기 시작합니다.

"오류: AZ3166: 알 수 없는 패키지" 오류 메시지가 표시될 수 있습니다. 보드 패키지 인덱스가 올바르게 새로 고쳐지지 않으면 이 오류가 발생합니다. 이 문제를 해결하려면 [IoT DevKit FAQ의 "알 수 없는 패키지" 오류](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)를 확인합니다.

## <a name="test-the-project"></a>프로젝트 테스트

앱 초기화 후 A 단추를 클릭했다가 놓은 다음, DevKit 보드를 살살 흔듭니다. 이 동작은 앞에서 지정한 해시태그를 포함하고 있는 임의의 트윗을 검색합니다. 몇 초 안에 DevKit 화면에 트윗이 표시됩니다.

### <a name="arduino-application-initializing"></a>Arduino 애플리케이션 초기화 중...

![Arduino 응용 프로그램 초기화 중](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>흔들려면 A 단추 누르기...

![흔들려면 A 단추 누르기](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>흔들기 준비 완료...

![흔들기 준비 완료](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>처리 중...

![처리 중](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>읽으려면 B 단추 누르기...

![읽으려면 B 단추 누르기](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>임의의 트윗 표시...

![임의의 트윗 표시](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- 다시 A 단추를 누른 후 흔들어서 새 트윗을 검색합니다.
- B 단추를 눌러 나머지 트윗을 스크롤합니다.

## <a name="how-it-works"></a>작동 방법

![다이어그램](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Arduino 스케치가 Azure IoT Hub에 이벤트를 보냅니다. 이 이벤트는 Azure Functions 앱을 트리거합니다. Azure Functions 앱은 Twitter의 API에 연결하여 트윗을 검색하는 논리를 포함하고 있습니다. 그런 후 트윗 텍스트를 C2D(클라우드-디바이스) 메시지에 래핑하고 다시 디바이스로 보냅니다.

## <a name="optional-use-your-own-twitter-bearer-token"></a>선택 사항: 사용자 고유의 Twitter 전달자 토큰 사용

이 샘플 프로젝트는 테스트를 목적으로 미리 구성된 Twitter 전달자 토큰을 사용합니다. 그러나 모든 Twitter 계정에는 [속도 제한](https://dev.twitter.com/rest/reference/get/search/tweets)이 있습니다. 사용자 고유의 토큰을 사용하려면 다음 단계를 따르세요.

1. [Twitter 개발자 포털](https://dev.twitter.com/)로 이동하여 새 Twitter 앱을 등록합니다.

2. 앱의 [소비자 키 및 소비자 비밀을 가져옵니다](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-).

3. [일부 유틸리티](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/)를 사용하여 두 키로 Twitter 전달자 토큰을 생성합니다.

4. [Azure Portal](https://portal.azure.com/){:target="_blank"}에서 **리소스 그룹**으로 이동하여 "Shake, Shake" 프로젝트의 Azure 함수(유형: 앱 서비스)를 찾습니다. 이름에는 항상 'Shake...' 문자열이 포함됩니다.

   ![azure-function](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. **함수 > shakeshake-cs** 내에서 `run.csx`의 코드를 사용자의 토큰으로 업데이트합니다.

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![트위터 토큰](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. 파일을 저장하고 **실행**을 클릭합니다.

## <a name="problems-and-feedback"></a>문제 및 피드백

문제를 해결하거나 피드백을 제공하는 방법입니다. 

### <a name="problems"></a>문제

모든 단계가 성공적으로 실행되었지만 화면에 '트윗 없음'이 표시되는지 확인할 수 있는 한 가지 문제입니다. 이 조건은 샘플을 처음으로 배포하고 실행할 때 주로 발생합니다. 함수 앱이 앱을 콜드 시작할 때까지 수초에서 1분이 걸리기 때문입니다. 

또는 코드를 실행하면 앱이 다시 시작되는 일시적인 문제가 있는 경우도 있습니다. 이 조건이 발생하면 디바이스 앱이 트윗을 가져오는 시간이 제한될 수 있습니다. 이 경우 다음 두 가지 방법 중 하나 또는 둘 다 사용하여 문제 해결을 시도할 수 있습니다.

1. DevKit의 재설정 단추를 클릭하여 디바이스 앱을 다시 실행합니다.

2. [Azure Portal](https://portal.azure.com/)에서 사용자가 만든 Azure Functions 앱을 찾아 다시 시작합니다.

   ![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>사용자 의견

다른 문제가 발생하면 [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 참조하거나 다음 지원 채널을 사용하여 문의해주세요.

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [스택 오버플로](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>다음 단계

지금까지 DevKit 디바이스를 Azure IoT 원격 모니터링 솔루션 가속기에 연결하고 트윗을 검색하는 방법을 알아보았으며, 이제부터는 다음 단계를 살펴볼 것을 권장합니다.

* [Azure IoT 원격 모니터링 솔루션 가속기 개요](https://docs.microsoft.com/azure/iot-suite/)