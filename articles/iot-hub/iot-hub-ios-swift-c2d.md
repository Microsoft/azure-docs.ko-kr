---
title: Azure IoT Hub(iOS)를 사용한 클라우드-디바이스 메시지 | Microsoft Docs
description: iOS용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 디바이스로 클라우드-디바이스 메시지를 보내는 방법입니다.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 544b1108a3c79da50969f7fdd33cfb9af373d854
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110875"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>IoT Hub(iOS)를 사용하여 클라우드-디바이스 메시지 보내기

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [장치에서 iot hub로 원격 분석 전송 빠른 시작](quickstart-send-telemetry-ios.md) 에서는 iot hub를 만들고, 장치 id를 프로 비전 하 고, 장치-클라우드 메시지를 보내는 시뮬레이션 된 장치 앱을 코딩 하는 방법을 보여 줍니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-디바이스 메시지를 단일 디바이스로 보냅니다.

* 디바이스에서 클라우드-디바이스 메시지를 받습니다.

* 솔루션 백 엔드에서 IoT Hub 장치에 전송 된 메시지에 대 한 배달 승인 (*피드백*)을 요청 합니다.

[IoT Hub 개발자 가이드의 메시징 섹션](iot-hub-devguide-messaging.md)에서 클라우드-디바이스 메시지에 대한 자세한 내용을 찾아볼 수 있습니다.

이 문서의 마지막 부분에서는 다음과 같은 두 Swift iOS 프로젝트를 실행합니다.

* **sample-device** - [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](quickstart-send-telemetry-ios.md)에서 만든 앱과 동일한 앱으로, IoT Hub에 연결하고 클라우드-디바이스 메시지를 수신합니다.

* **샘플-** IoT Hub를 통해 시뮬레이션 된 장치 앱에 클라우드-장치 메시지를 보낸 다음 배달 승인을 수신 하는 샘플 서비스입니다.

> [!NOTE]
> IoT Hub에는 Azure IoT 장치 Sdk를 통해 많은 장치 플랫폼 및 언어 (C, Java, Python 및 Javascript 포함)에 대 한 SDK 지원이 있습니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터](https://www.azure.com/develop/iot)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

* Azure의 활성 IoT Hub.

* [Azure 샘플](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)의 코드 샘플

* 최신 버전의 iOS SDK를 실행 중인 최신 버전의 [XCode](https://developer.apple.com/xcode/). 이 빠른 시작은 XCode 9.3 및 iOS 11.3에서 테스트되었습니다.

* 최신 버전의 [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* 방화벽에서 포트 8883가 열려 있는지 확인 합니다. 이 문서의 device 샘플에서는 포트 8883을 통해 통신 하는 MQTT 프로토콜을 사용 합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단 될 수 있습니다. 이 문제를 해결 하는 방법 및 방법에 대 한 자세한 내용은 [IoT Hub에 연결 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조 하세요.

## <a name="simulate-an-iot-device"></a>IoT 디바이스 시뮬레이션

이 섹션에서는 IoT Hub에서 클라우드-디바이스 메시지를 수신하는 Swift 애플리케이션을 실행하는 iOS 디바이스를 시뮬레이션합니다. 

이것은 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](quickstart-send-telemetry-ios.md) 문서에서 만드는 샘플 디바이스입니다. 이미 샘플 장치를 실행 중인 경우 이 섹션을 건너뛰어도 됩니다.

### <a name="install-cocoapods"></a>CocoaPods 설치

CocoaPods는 타사 라이브러리를 사용하는 iOS 프로젝트의 종속성을 관리합니다.

터미널 창에서 필수 구성 요소에 다운로드한 Azure-IoT-Samples-iOS 폴더로 이동합니다. 그런 다음, 샘플 프로젝트로 이동합니다.

```sh
cd quickstart/sample-device
```

XCode가 닫혀 있는지 확인한 후, 다음 명령을 실행하여 **podfile** 파일에 선언된 CocoaPods를 설치합니다.

```sh
pod install
```

설치 명령은 프로젝트에 필요한 포드를 설치하고, 종속성에 Pod를 사용하도록 이미 구성된 XCode 작업 영역 파일도 만들었습니다.

### <a name="run-the-sample-device-application"></a>샘플 디바이스 애플리케이션 실행

1. 디바이스의 연결 문자열을 검색합니다. 디바이스 세부 정보 블레이드에서 [Azure Portal](https://portal.azure.com)을 통해 이 문자열을 복사하거나 다음 CLI 명령을 사용하여 검색할 수 있습니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. XCode에서 샘플 작업 영역을 엽니다.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. **MQTT 클라이언트 샘플** 프로젝트를 확장한 다음, 같은 이름의 폴더를 확장합니다.  

3. XCode에서 편집을 위해 **ViewController.swift**를 엽니다. 

4. **connectionString** 변수를 검색하고 첫 번째 단계에서 복사해 둔 디바이스 연결 문자열로 값을 업데이트합니다.

5. 변경 내용을 저장합니다. 

6. **빌드 및 실행** 단추 또는 **명령 + r** 키 콤보를 사용하여 디바이스 에뮬레이터에서 프로젝트를 실행합니다.

   ![프로젝트 실행](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>IoT hub 연결 문자열을 가져옵니다.

이 문서에서는 [장치에서 iot hub로 원격 분석 전송](quickstart-send-telemetry-ios.md)에서 만든 iot hub를 통해 클라우드-장치 메시지를 보내는 백 엔드 서비스를 만듭니다. 클라우드-장치 메시지를 보내려면 서비스에 **서비스 연결** 권한이 있어야 합니다. 기본적으로 모든 IoT Hub은이 사용 권한을 부여 하는 **서비스** 라는 공유 액세스 정책으로 만들어집니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>서비스 디바이스 시뮬레이션

이 섹션에서는 IoT Hub를 통해 클라우드-디바이스 메시지를 보내는 Swift 앱을 사용하여 두 번째 iOS 디바이스를 시뮬레이션합니다. 이 구성은 iPhone 또는 iPad 한 대가 IoT Hub에 연결된 다른 iOS 디바이스의 컨트롤러 역할을 하는 IoT 시나리오에 유용합니다.

### <a name="install-cocoapods"></a>CocoaPods 설치

CocoaPods는 타사 라이브러리를 사용하는 iOS 프로젝트의 종속성을 관리합니다.

필수 구성 요소에서 다운로드한 Azure IoT iOS 샘플 폴더로 이동합니다. 그런 다음, 샘플 서비스 프로젝트로 이동합니다.

```sh
cd quickstart/sample-service
```

XCode가 닫혀 있는지 확인한 후, 다음 명령을 실행하여 **podfile** 파일에 선언된 CocoaPods를 설치합니다.

```sh
pod install
```

설치 명령은 프로젝트에 필요한 포드를 설치하고, 종속성에 Pod를 사용하도록 이미 구성된 XCode 작업 영역 파일도 만들었습니다.

### <a name="run-the-sample-service-application"></a>샘플 서비스 애플리케이션 실행

1. XCode에서 샘플 작업 영역을 엽니다.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. **AzureIoTServiceSample** 프로젝트를 확장한 다음, 같은 이름의 폴더를 확장합니다.  

3. XCode에서 편집을 위해 **ViewController.swift**를 엽니다. 

4. **ConnectionString** 변수를 검색 하 고 이전에 [IoT Hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 서비스 연결 문자열로 값을 업데이트 합니다.

5. 변경 내용을 저장합니다.

6. Xcode에서 에뮬레이터 설정을 IoT 디바이스 실행에 사용한 다른 iOS 디바이스로 변경합니다. XCode는 같은 유형의 에뮬레이터를 여러 개 실행할 수 없습니다.

   ![에뮬레이터 디바이스 변경](media/iot-hub-ios-swift-c2d/change-device.png)

7. **빌드 및 실행** 단추 또는 **명령 + r** 키 콤보를 사용하여 디바이스 에뮬레이터에서 프로젝트를 실행합니다.

   ![프로젝트 실행](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>클라우드-디바이스 메시지 보내기

이제 클라우드-디바이스 메시지를 주고 받는 두 애플리케이션을 사용할 준비가 완료되었습니다.

1. 시뮬레이션된 IoT 디바이스에서 실행되는 **iOS 앱 샘플**에서 **시작**을 클릭합니다. 애플리케이션이 디바이스-클라우드 메시지 송신을 시작하고, 그와 동시에 클라우드-디바이스 메시지 수신 대기도 시작합니다.

   ![샘플 IoT 디바이스 앱 보기](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. 시뮬레이션된 서비스 디바이스에서 실행되는 **IoTHub 서비스 클라이언트 샘플** 앱에서 메시지를 보내고 싶은 IoT 디바이스의 ID를 입력합니다. 

3. 일반 텍스트 메시지를 작성한 다음, **보내기**를 클릭합니다.

    보내기를 클릭하는 즉시 여러 작업이 발생합니다. 서비스 샘플은 IoT Hub로 메시지를 보내고, 앱은 사용자가 입력한 서비스 연결 문자열 때문에 허브에 액세스할 수 있습니다. IoT Hub는 디바이스 ID를 검사하고, 대상 디바이스에 메시지를 보내고, 원본 디바이스에 수신 확인을 보냅니다. 시뮬레이션된 IoT 디바이스에서 실행 중인 앱은 IoT Hub에서 보낸 메시지를 검사하여 가장 최근에 온 메시지의 텍스트를 화면에 인쇄합니다.

    출력은 다음 예제와 비슷합니다.

   ![클라우드-디바이스 메시지 보기](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서 클라우드-디바이스 메시지를 보내고 받는 방법을 알아보았습니다.

IoT Hub를 사용하는 완벽한 엔드투엔드 솔루션의 예를 보려면 [Azure IoT 솔루션 가속기](https://azure.microsoft.com/documentation/suites/iot-suite/) 설명서를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.
