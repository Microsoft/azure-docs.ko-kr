---
title: Azure IoT Hub 모듈 ID 및 모듈 쌍 시작(Node.js) | Microsoft Docs
description: Node.js용 IoT SDK를 사용하여 모듈 ID를 만들고 모듈 쌍을 업데이트하는 방법을 알아봅니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: 312d3abad2ee2c9e668f8b354aaba96f8a652698
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626221"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-nodejs-back-end-and-nodejs-device"></a>Node.js 백 엔드 및 Node.js 디바이스를 사용하여 IoT Hub 모듈 ID 및 모듈 쌍 시작

> [!NOTE]
> [모듈 ID 및 모듈 쌍](iot-hub-devguide-module-twins.md)은 Azure IoT Hub 디바이스 ID 및 디바이스 쌍과 비슷하지만 더 자세한 세분성을 제공합니다. Azure IoT Hub 장치 ID와 장치 쌍은 백 엔드 애플리케이션에서 장치를 구성할 수 있도록 하고 장치 상태에 대한 가시성을 제공하지만, 모듈 ID와 모듈 쌍은 장치의 개별 구성 요소에 대해 이러한 기능을 제공합니다. 운영 체제 기반 디바이스 또는 펌웨어 디바이스와 같이 여러 구성 요소가 있는 가능한 디바이스에서 각 구성 요소에 대해 격리된 구성과 조건을 허용합니다.

이 자습서를 마치면 두 가지 Node.js 앱이 만들어집니다.

* **CreateIdentities**: 디바이스 ID, 모듈 ID 및 관련 보안 키를 만들어 디바이스 및 모듈 클라이언트를 연결합니다.

* **UpdateModuleTwinReportedProperties**: 업데이트된 모듈 쌍 reported 속성을 IoT Hub에 보냅니다.

> [!NOTE]
> 장치와 솔루션 백 엔드에서 실행하기 위해 두 애플리케이션을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보는 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.
* IoT Hub
* 최신 [Node.js SDK](https://github.com/Azure/azure-iot-sdk-node)를 설치합니다.

이제 IoT Hub가 만들어졌고 이 자습서 나머지 부분을 완료하는 데 필요한 호스트 이름과 IoT Hub 연결 문자열을 갖게 되었습니다.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>IoT Hub에서 디바이스 ID 및 모듈 ID 만들기

이 섹션에서는 IoT Hub의 ID 레지스트리에 디바이스 ID 및 모듈 ID를 만드는 Node.js 앱을 만듭니다. ID 레지스트리에 항목이 없는 경우 디바이스 또는 모듈을 IoT Hub에 연결할 수 없습니다. 자세한 내용은 "Id 레지스트리" 섹션을 참조 합니다 [IoT Hub 개발자 가이드](iot-hub-devguide-identity-registry.md)합니다. 이 콘솔 앱을 실행하면 디바이스 및 모듈 둘 다의 고유한 ID 및 키가 생성됩니다. 디바이스 및 모듈은 IoT Hub에 디바이스-클라우드 메시지를 보낼 때 이러한 값을 사용하여 자신을 식별합니다. ID는 대/소문자를 구분합니다.

1. 코드를 저장할 디렉터리를 만듭니다.

2. 이 디렉터리 내부에서 먼저  **npm init -y** 를 실행하여 기본값으로 빈 package.json을 만듭니다. 코드의 프로젝트 파일입니다.

3. 실행 **npm 설치-S azure iot hub\@모듈-미리 보기** 서비스 SDK를 설치 하려면 내부를 **node_modules** 하위 디렉터리입니다.

    > [!NOTE]
    > 하위 디렉터리 이름 node_modules는 ‘module’이라는 단어를 사용하여 “노드 라이브러리”를 나타냅니다. 여기서 이 용어는 IoT Hub 모듈과 아무 관계도 없습니다.

4. 디렉터리에 다음 .js 파일을 만듭니다. 이름을 **add.js**로 지정합니다. 허브 연결 문자열과 허브 이름을 복사하여 붙여넣습니다.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

이 앱은 ID가 **myFirstDevice**인 디바이스 ID와 ID가 **myFirstModule**인 모듈 ID를 **myFirstDevice** 디바이스 아래에 만듭니다. ID 레지스트리에 해당 모듈 ID가 이미 있는 경우 코드는 기존 모듈 정보만 검색합니다. 그러면 앱에서 해당 ID에 대한 기본 키를 표시합니다. 이 키를 시뮬레이션된 모듈 앱에서 사용하여 IoT Hub에 연결합니다.

node add.js를 사용하여 실행합니다. 그러면 디바이스 ID에 대한 연결 문자열과 모듈 ID에 대한 다른 연결 문자열이 제공됩니다.

> [!NOTE]
> IoT Hub ID 레지스트리는 디바이스 및 모듈 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. ID 레지스트리는 보안 자격 증명으로 사용할 디바이스 ID 및 키를 저장합니다. 또한 ID 레지스트리는 각 디바이스에 대한 액세스를 사용하지 않도록 설정하는 데 사용할 수 있는 해당 디바이스에 대한 enabled/disabled 플래그를 저장합니다. 애플리케이션이 다른 장치별 메타데이터를 저장해야 할 경우 애플리케이션별 저장소를 사용해야 합니다. 모듈 ID에 대한 enabled/disabled 플래그는 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide-identity-registry.md)를 참조하세요.

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Node.js 디바이스 SDK를 사용하여 모듈 쌍 업데이트

이 섹션에서는 시뮬레이션된 디바이스에 보고된 모듈 쌍 속성을 업데이트하는 Node.js 앱을 만듭니다.

1. **모듈 연결 문자열 가져오기** -에 로그인 하는 [Azure portal](https://portal.azure.com/)합니다. IoT Hub로 이동하고 IoT 디바이스를 클릭합니다. myFirstDevice를 찾아서 열면 성공적으로 만들어진 myFirstModule이 표시됩니다. 모듈 연결 문자열을 복사합니다. 이는 다음 단계에서 필요합니다.

   ![Azure Portal 모듈 세부 정보](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. 위의 단계에서 수행한 있습니다 비슷하게, 장치 코드에 대 한 디렉터리를 만듭니다 및 NPM을 사용 하 여 초기화 하 고 장치 SDK를 설치 하려면 (**npm 설치-S azure-iot-장치-amqp\@모듈-preview**).

   > [!NOTE]
   > npm install 명령의 속도가 느린 것처럼 보일 수도 있습니다. 이 명령은 패키지 리포지토리에서 많은 코드를 끌어오고 있으므로 잠시 기다려 주세요.

   > [!NOTE]
   > JSON을 구문 분석하는 중 npm ERR! 레지스트리 오류가 발생했다는 오류가 표시되는 경우, 무시해도 됩니다. JSON을 구문 분석하는 중 npm ERR! 레지스트리 오류가 발생했다는 오류가 표시되는 경우, 무시해도 됩니다.

3. twin.js라는 파일을 만듭니다. 모듈 ID 문자열을 복사하여 붙여넣습니다.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. 이제  **node twin.js** 명령을 사용하여 실행합니다.

    ```
    F:\temp\module_twin>node twin.js
    client opened
    twin contents:
    { reported: { update: [Function: update], '$version': 1 },
      desired: { '$version': 1 } }
    new desired properties received:
    {"$version":1}
    twin state reported
    ```

## <a name="next-steps"></a>다음 단계

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [디바이스 관리 시작](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge 시작](../iot-edge/tutorial-simulate-device-linux.md)