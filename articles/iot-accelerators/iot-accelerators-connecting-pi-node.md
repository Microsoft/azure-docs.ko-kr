---
title: Node.js에서 원격 모니터링으로 Raspberry Pi 프로비전 - Azure | Microsoft Docs
description: Node.js에 작성된 애플리케이션을 사용하여 원격 모니터링 솔루션 가속기에 Raspberry Pi 장치를 연결하는 방법을 설명합니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: 20d50ac4ac4a1919077ebe67bb529e2dc5abf187
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449695"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>원격 모니터링 솔루션 가속기에 Raspberry Pi 디바이스 연결(Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

이 자습서는 원격 모니터링 솔루션 가속기에 실제 디바이스를 연결하는 방법을 보여 줍니다. 이 자습서에서는 최소한의 리소스 제약 조건으로 환경에 적합한 옵션인 Node.js를 사용합니다.

디바이스를 시뮬레이션하려면 [새 시뮬레이션된 디바이스 만들기 및 테스트](iot-accelerators-remote-monitoring-create-simulated-device.md)를 참조하세요.

### <a name="required-hardware"></a>필수 하드웨어

Raspberry Pi의 명령줄에 원격으로 연결할 수 있는 데스크톱 컴퓨터이며

[Raspberry Pi 3용 Microsoft IoT 시작 키트](https://azure.microsoft.com/develop/iot/starter-kits/) 또는 동등한 구성 요소입니다. 이 자습서에서는 키트에서 다음 항목을 사용합니다.

- Raspberry Pi 3
- MicroSD 카드(NOOBS 포함)
- USB 미니 케이블
- 이더넷 케이블

### <a name="required-desktop-software"></a>필수 데스크톱 소프트웨어

Raspberry Pi의 명령줄에 원격으로 액세스할 수 있도록 데스크톱 컴퓨터에 SSH 클라이언트가 필요합니다.

- Windows에서는 SSH 클라이언트를 포함하지 않습니다. [PuTTY](https://www.putty.org/)를 사용하는 것이 좋습니다.
- 대부분의 Linux 배포판 및 Mac OS는 명령줄 SSH 유틸리티를 포함합니다. 자세한 내용은 [Linux 또는 Mac OS를 사용하는 SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)를 참조하세요.

### <a name="required-raspberry-pi-software"></a>필수 Raspberry Pi 소프트웨어

아직 그렇게 하지 않은 경우 Raspberry Pi에 Node.js 버전 4.0.0 이상을 설치합니다. 다음 단계에서는 Raspberry Pi에 Node.js v6을 설치하는 방법을 보여 줍니다.

1. `ssh`를 사용하여 Raspberry Pi에 연결합니다. 자세한 내용은 [Raspberry Pi 웹 사이트](https://www.raspberrypi.org/)에서 [SSH(Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md)를 참조하세요.

1. 다음 명령을 사용하여 Raspberry Pi를 업데이트합니다.

    ```sh
    sudo apt-get update
    ```

1. 다음 명령을 사용하여 Raspberry Pi에서 Node.js의 기존 설치를 제거합니다.

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. 다음 명령을 사용하여 Raspberry Pi에 Node.js v6을 다운로드하고 설치합니다.

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. 다음 명령을 사용하여 Node.js v6.11.4를 성공적으로 설치했는지 확인합니다.

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Node.js 솔루션 만들기

Raspberry Pi에 `ssh` 연결을 사용하여 다음 단계를 완료합니다.

1. Raspberry Pi의 홈 폴더에 `remotemonitoring`이라는 폴더를 만듭니다. 명령줄에서 이 폴더로 이동합니다.

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. 샘플 앱을 완료해야 하는 패키지를 다운로드하고 설치하려면 다음 명령을 실행합니다.

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. `remotemonitoring` 폴더에 **remote_monitoring.js**라는 파일을 만듭니다. 텍스트 편집기에서 이 파일을 엽니다. Raspberry Pi에서 `nano` 또는 `vi` 텍스트 편집기를 사용할 수 있습니다.

1. **remote_monitoring.js** 파일에서 다음 `require` 문을 추가합니다.

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. 다음 변수 선언을 `require` 문 뒤에 추가합니다. 자리 표시자 값 `{device connection string}`을 원격 모니터링 솔루션에서 프로비전한 디바이스에 대해 기록한 값으로 바꿉니다.

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. 기본 원격 분석 데이터를 정의하려면 다음 변수를 추가합니다.

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. 속성 값을 정의하려면 다음 변수를 추가합니다.

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. 다음 변수를 추가하여 솔루션에 보내는 reported 속성을 정의합니다. 이러한 속성에는 웹 UI에 표시할 메타데이터가 포함됩니다.

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. 작업 결과를 출력하려면 다음 도우미 함수를 추가합니다.

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. 다음 도우미 함수를 추가하여 원격 분석 값을 임의로 지정하는 데 사용합니다.

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. 다음 제네릭 함수를 추가하여 솔루션에서 직접 메서드 호출을 처리합니다. 함수는 호출된 직접 메서드에 대한 정보를 표시하지만, 이 샘플에서는 어떤 방식으로도 디바이스를 수정하지 않습니다. 솔루션은 직접 메서드를 사용하여 디바이스에서 작동합니다.

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. 다음 함수를 추가하여 솔루션에서 **FirmwareUpdate** 직접 메서드 호출을 처리합니다. 함수는 직접 메서드 페이로드에 전달된 매개 변수를 확인한 다음, 펌웨어 업데이트 시뮬레이션을 비동기적으로 실행합니다.

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. 다음 함수를 추가하여 진행 상태를 솔루션에 다시 보고하는 장기 실행 펌웨어 업데이트 흐름을 시뮬레이션합니다.

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. 다음 코드를 추가하여 솔루션에 원격 분석 데이터를 보냅니다. 클라이언트 앱은 메시지 스키마를 식별하기 위해 메시지에 속성을 추가합니다.

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. 다음 코드를 추가하여 클라이언트 인스턴스를 만듭니다.

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. 다음 코드를 추가하여:

    * 연결을 엽니다.
    * desired 속성에 대한 처리기를 설정합니다.
    * reported 속성을 보냅니다.
    * 직접 메서드에 대한 처리기를 등록합니다. 샘플은 펌웨어 업데이트 직접 메서드에 대한 별도 처리기를 사용합니다.
    * 원격 분석 보내기를 시작합니다.

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. **remote_monitoring.js** 파일에 변경 내용을 저장합니다.

1. 샘플 애플리케이션을 시작하려면 Raspberry Pi의 명령 프롬프트에서 다음 명령을 실행합니다.

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
