---
title: Node.js를 사용하여 디바이스에서 Azure IoT Hub로 파일 업로드 | Microsoft Docs
description: Node.js용 Azure IoT 디바이스 SDK를 사용하여 디바이스에서 클라우드로 파일을 업로드 하는 방법입니다. 업로드된 파일은 Azure Storage blob 컨테이너에 저장됩니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: db3da5ff2d7e8b6fa493f5338fac93df0d1a7fe2
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110900"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>IoT Hub (node.js)를 사용 하 여 장치에서 클라우드로 파일 업로드

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

이 자습서에서는 [IoT Hub를 사용 하 여 클라우드-장치 메시지 보내기](iot-hub-node-node-c2d.md) 자습서의 코드를 기반으로 하 여 [IoT Hub의 파일 업로드 기능](iot-hub-devguide-file-upload.md) 을 사용 하 여 [Azure blob storage](../storage/index.yml)에 파일을 업로드 하는 방법을 보여 줍니다. 이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 파일을 업로드하기 위한 Azure blob URI를 디바이스에 안전하게 제공합니다.

* IoT Hub 파일 업로드 알림을 사용하여 앱 백 엔드에서 파일 처리를 트리거합니다.

[장치에서 IoT hub로 원격 분석 전송 빠른 시작](quickstart-send-telemetry-node.md) 은 IoT Hub의 기본적인 장치-클라우드 메시징 기능을 보여 줍니다. 그러나 일부 시나리오에서는 디바이스에서 전송하는 데이터를 IoT Hub에서 허용하는 비교적 작은 디바이스-클라우드 메시지에 쉽게 매핑할 수 없습니다. 다음은 그 예입니다.

* 이미지가 포함된 대형 파일
* 동영상
* 자주 샘플링되는 진동 데이터
* 일부 형태의 미리 처리 된 데이터입니다.

이러한 파일은 일반적으로 [Azure Data Factory](../data-factory/introduction.md) 또는 [Hadoop](../hdinsight/index.yml) 스택과 같은 도구를 사용하여 클라우드에서 배치 방식으로 처리됩니다. 디바이스에서 파일을 업로드해야 할 때 IoT Hub의 보안 및 안정성을 여전히 사용할 수 있습니다.

이 자습서의 끝 부분에서 다음 두 개의 Node.js 콘솔 앱을 실행합니다.

* **SimulatedDevice.js** - IoT Hub에서 제공하는 SAS URI를 사용하여 스토리지에 파일을 업로드합니다.

* **ReadFileUploadNotification** - IoT Hub에서 파일 업로드 알림을 받습니다.

> [!NOTE]
> IoT Hub는 Azure IoT 디바이스 SDK를 통해 많은 디바이스 플랫폼 및 언어(C, .NET, Javascript, Python 및 Java 포함)를 지원합니다. 장치를 Azure IoT Hub에 연결 하는 방법에 대 한 단계별 지침은 [Azure IoT 개발자 센터]를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Node.js 버전 10.0. x 이상 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) Windows 또는 Linux에서이 자습서에 대 한 node.js를 설치 하는 방법을 설명 합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

* 방화벽에서 포트 8883가 열려 있는지 확인 합니다. 이 문서의 device 샘플에서는 포트 8883을 통해 통신 하는 MQTT 프로토콜을 사용 합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단 될 수 있습니다. 이 문제를 해결 하는 방법 및 방법에 대 한 자세한 내용은 [IoT Hub에 연결 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조 하세요.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>디바이스 앱에서 파일 업로드

이 섹션에서는 IoT Hub에 파일을 업로드하는 디바이스 앱을 만듭니다.

1. ```simulateddevice```라는 빈 폴더를 만듭니다.  ```simulateddevice``` 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.

    ```cmd/sh
    npm init
    ```

2. ```simulateddevice``` 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 디바이스 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. 텍스트 편집기를 사용하여 **폴더에**SimulatedDevice.js```simulateddevice``` 파일을 만듭니다.

4. ```require```SimulatedDevice.js**파일 앞에 다음** 문을 추가합니다.

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. `deviceconnectionstring` 변수를 추가하고 이 변수를 사용하여 **클라이언트** 인스턴스를 만듭니다.  `{deviceconnectionstring}`을 *IoT Hub 만들기* 섹션에서 만든 디바이스 이름으로 바꿉니다.

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > 간소화를 위해 연결 문자열이 코드에 포함되어 있습니다. 이것은 권장되는 방법이 아니며 사용 사례 및 아키텍처에 따라 이러한 비밀을 보다 안전하게 저장하는 방법을 고려할 수 있습니다.

6. 클라이언트에 연결하도록 다음 코드를 추가합니다.

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. 콜백을 만들고 **uploadToBlob** 함수를 사용하여 파일을 업로드합니다.

    ```javascript
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);

        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

8. **SimulatedDevice.js** 파일을 저장한 후 닫습니다.

9. 이미지 파일을 `simulateddevice` 폴더에 복사하고 파일 이름을 `myimage.png`로 바꿉니다.

## <a name="get-the-iot-hub-connection-string"></a>IoT hub 연결 문자열을 가져옵니다.

이 문서에서는 [장치에서 iot hub로 원격 분석 전송](quickstart-send-telemetry-node.md)에서 만든 iot hub에서 파일 업로드 알림 메시지를 수신 하는 백 엔드 서비스를 만듭니다. 파일 업로드 알림 메시지를 수신 하려면 서비스에 **서비스 연결** 권한이 있어야 합니다. 기본적으로 모든 IoT Hub은이 사용 권한을 부여 하는 **서비스** 라는 공유 액세스 정책으로 만들어집니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>파일 업로드 알림 수신

이 섹션에서는 IoT Hub로부터 파일 업로드 알림 메시지를 수신하는 Node.js 콘솔 앱을 만듭니다.

이 섹션을 완료하려면 IoT Hub의 **iothubowner** 연결 문자열을 사용할 수 있습니다. 연결 문자열은 [공유 액세스 정책](https://portal.azure.com/) 블레이드의 **Azure Portal**에서 찾을 수 있습니다.

1. ```fileuploadnotification```라는 빈 폴더를 만듭니다.  ```fileuploadnotification``` 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.

    ```cmd/sh
    npm init
    ```

2. ```fileuploadnotification``` 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** SDK 패키지를 설치합니다.

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. 텍스트 편집기를 사용하여 **폴더에**FileUploadNotification.js`fileuploadnotification` 파일을 만듭니다.

4. `require`FileUploadNotification.js**파일의 시작 부분에 다음** 문을 추가합니다.

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. `iothubconnectionstring` 변수를 추가하고 이 변수를 사용하여 **클라이언트** 인스턴스를 만듭니다.  `{iothubconnectionstring}` 자리 표시자 값을 이전에 [iot hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 iot hub 연결 문자열로 바꿉니다.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > 간소화를 위해 연결 문자열이 코드에 포함되어 있습니다. 이것은 권장되는 방법이 아니며 사용 사례 및 아키텍처에 따라 이러한 비밀을 보다 안전하게 저장하는 방법을 고려할 수 있습니다.

6. 클라이언트에 연결하도록 다음 코드를 추가합니다.

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. 클라이언트를 열고 상태 업데이트를 수신하는 **getFileNotificationReceiver** 함수를 사용합니다.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

8. **FileUploadNotification.js** 파일을 저장하고 닫습니다.

## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

`fileuploadnotification` 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

```cmd/sh
node FileUploadNotification.js
```

`simulateddevice` 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

```cmd/sh
node SimulatedDevice.js
```

다음 스크린샷은 **SimulatedDevice** 앱의 출력을 보여 줍니다.

![simulated-device 앱의 출력](./media/iot-hub-node-node-file-upload/simulated-device.png)

다음 스크린샷은 **FileUploadNotification** 앱의 출력을 보여 줍니다.

![read-file-upload-notification 앱의 출력](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

포털을 사용하면 구성한 스토리지 컨테이너에 업로드된 파일을 볼 수 있습니다.

![업로드된 파일](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스에서 파일 업로드를 단순화하기 위해 IoT Hub의 파일 업로드 기능을 사용하는 방법을 알아보았습니다. 다음 문서를 사용하여 IoT Hub 기능 및 시나리오를 계속 탐색할 수 있습니다.

* [프로그래밍 방식으로 IoT Hub 만들기](iot-hub-rm-template-powershell.md)

* [C SDK 소개](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)
