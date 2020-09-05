---
title: '자습서: 투명 게이트웨이를 통해 디바이스 데이터 보내기 - Azure IoT Edge의 Machine Learning'
description: 이 자습서에서는 개발 머신을 시뮬레이션된 IoT Edge 디바이스로 사용하여 투명 게이트웨이로 구성된 디바이스를 통해 IoT Hub로 데이터를 보내는 방법을 보여줍니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 78d3cbac28a85452f23e4a7c7ba3904fe3d1f488
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997883"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>자습서: 투명 게이트웨이를 통해 데이터 보내기

> [!NOTE]
> 이 문서는 IoT Edge에서 Azure Machine Learning을 사용하는 방법에 대한 자습서 시리즈의 일부입니다. 이 문서로 바로 이동한 경우에는 학습 효과를 극대화할 수 있도록 시리즈의 [첫 번째 문서](tutorial-machine-learning-edge-01-intro.md)부터 시작하는 것이 좋습니다.

이 문서에서는 개발 VM을 시뮬레이션된 디바이스로 다시 사용합니다. 그러나 IoT Hub로 직접 데이터를 보내는 대신 투명 게이트웨이로 구성된 IoT Edge 디바이스로 데이터를 보냅니다.

시뮬레이션된 디바이스가 데이터를 보내는 동안 IoT Edge 디바이스의 작업을 모니터링하겠습니다. 디바이스 실행이 완료되면 스토리지 계정의 데이터를 살펴보고 모든 것이 예상대로 작동했는지 확인하겠습니다.

이 단계는 일반적으로 클라우드 또는 디바이스 개발자가 수행합니다.

## <a name="review-device-harness"></a>디바이스 도구 검토

[DeviceHarness 프로젝트](tutorial-machine-learning-edge-03-generate-data.md)를 다시 사용하여 다운스트림(또는 리프) 디바이스를 시뮬레이션합니다. 투명 게이트웨이에 연결하려면 다음 두 가지 조건을 추가로 충족해야 합니다.

* 인증서를 등록하여 다운스트림 IoT 디바이스가 IoT Edge 런타임에 사용되는 인증 기관을 신뢰하게 만듭니다. 이 경우 다운스트림 디바이스는 개발 VM입니다.
* 에지 게이트웨이 FQDN(정규화된 도메인 이름)을 디바이스 연결 문자열에 추가합니다.

코드를 보면서 두 항목이 어떻게 구현되는지 알아봅니다.

1. 개발 머신에서 Visual Studio Code를 엽니다.

1. **파일** > **폴더 열기...** 를 사용하여 C:\\source\\IoTEdgeAndMlSample\\DeviceHarness를 엽니다.

1. Program.cs의 InstallCertificate() 메서드를 살펴봅니다.

1. 코드는 인증서 경로를 찾으면 CertificateManager.InstallCACert 메서드를 호출하여 머신에 인증서를 설치합니다.

1. 이제 TurbofanDevice 클래스의 GetIotHubDevice 메서드를 살펴보겠습니다.

1. 사용자가 "-g" 옵션을 사용하여 게이트웨이의 FQDN을 지정하면 해당 값이 이 메서드에 `gatewayFqdn` 변수로 전달되어 디바이스 연결 문자열에 추가됩니다.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>리프 디바이스 빌드 및 실행

1. Visual Studio Code에서 DeviceHarness 프로젝트가 열려 있는 상태에서 프로젝트를 빌드합니다. **터미널** 메뉴에서 **빌드 작업 실행**을 선택하고 **빌드**를 선택합니다.

1. Azure Portal에서 IoT Edge 디바이스(Linux VM)로 이동하고 개요 페이지에서 **DNS 이름** 값을 복사하여 에지 게이트웨이의 FQDN(정규화된 도메인 이름)을 찾습니다.

1. IoT 디바이스(Linux VM)가 아직 실행되지 않은 경우 시작합니다.

1. Visual Studio Code 터미널을 엽니다. **터미널** 메뉴에서 **새 터미널**을 선택하고 다음 명령을 실행하여 `<edge_device_fqdn>`을 IoT Edge 디바이스(Linux VM)에서 복사한 DNS 이름으로 바꿉니다.

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. 애플리케이션이 개발 머신에 인증서를 설치하려고 시도합니다. 이때 표시되는 보안 경고를 수락합니다.

1. IoT Hub 연결 문자열을 요청하는 메시지가 표시되면 Azure IoT Hub 디바이스 패널에서 줄임표( **...** )를 클릭하고 **IoT Hub 연결 문자열 복사**를 선택합니다. 이 값을 터미널에 붙여넣습니다.

1. 다음과 같은 출력이 표시됩니다.

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   디바이스 연결 문자열에 “GatewayHostName”이 추가되었습니다. 따라서 디바이스가 IoT Edge 투명 게이트웨이를 통해 IoT Hub와 통신합니다.

## <a name="check-output"></a>출력 확인

### <a name="iot-edge-device-output"></a>IoT Edge 디바이스 출력

IoT Edge 디바이스를 살펴보면 avroFileWriter 모듈의 출력을 쉽게 관찰할 수 있습니다.

1. SSH를 통해 IoT Edge 가상 머신에 연결합니다.

1. 디스크에 기록된 파일을 찾습니다.

   ```bash
   find /data/avrofiles -type f
   ```

1. 명령의 출력은 다음 예제와 비슷합니다.

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   실행 타이밍에 따라 파일이 여러 개 있을 수 있습니다.

1. 타임스탬프를 주목하세요. 마지막 수정 시간이 10분을 초과하면 avroFileWriter 모듈은 클라우드에 파일을 업로드합니다(avroFileWriter 모듈의 uploader.py에서 MODIFIED\_FILE\_TIMEOUT 참조).

1. 10분이 경과하면 모듈이 파일을 업로드합니다. 업로드가 성공하면 모듈은 디스크에서 파일을 삭제합니다.

### <a name="azure-storage"></a>Azure Storage

데이터가 라우팅될 스토리지 계정을 살펴보면 데이터를 보내는 리프 디바이스의 결과를 관찰할 수 있습니다.

1. 개발 머신에서 Visual Studio Code를 엽니다.

1. 탐색 창의 "AZURE STORAGE" 패널에서 트리를 검색하여 해당 스토리지 계정을 찾습니다.

1. **Blob 컨테이너** 노드를 확장합니다.

1. 이 자습서의 이전 부분에서 수행한 작업에서는 **ruldata** 컨테이너에 RUL이 포함된 메시지가 있어야 합니다. **ruldata** 노드를 확장합니다.

1. 이름이 `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>` 형식인 Blob 파일이 하나 이상 표시됩니다.

1. 파일 중 하나를 마우스 오른쪽 단추로 클릭하고 **Blob 다운로드**를 선택하여 개발 머신에 파일을 저장합니다.

1. **uploadturbofanfiles** 노드를 확장합니다. 이전 문서에서 이 위치를 avroFileWriter 모듈이 업로드한 파일의 대상으로 설정했습니다.

1. 파일을 마우스 오른쪽 단추로 클릭하고 **Blob 다운로드**를 선택하여 개발 머신에 파일을 저장합니다.

### <a name="read-avro-file-contents"></a>Avro 파일 콘텐츠 읽기

Avro 파일을 읽고 파일에 메시지의 JSON 문자열을 반환하기 위해 간단한 명령줄 유틸리티를 포함해 두었습니다. 이 섹션에서는 이 명령줄 유틸리티를 설치하고 실행하겠습니다.

1. Visual Studio Code에서 터미널을 엽니다(**터미널** > **새 터미널**).

1. 다음과 같이 hubavroreader를 설치합니다.

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. hubavroreader를 사용하여 **ruldata**에서 다운로드한 Avro 파일을 읽습니다.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. 메시지 본문은 디바이스 ID와 예상 RUL에서 예상한 대로 표시됩니다.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. **uploadturbofanfiles**에서 다운로드한 Avro 파일을 전달하는 동일한 명령을 실행합니다.

1. 예상대로 이러한 메시지에는 원래 메시지의 모든 센서 데이터와 운영 설정이 포함되어 있습니다. 이 데이터를 사용하여 에지 디바이스의 RUL 모델을 개선할 수 있습니다.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>리소스 정리

이 엔드투엔드 자습서에서 사용하는 리소스를 살펴볼 계획이면 여기서 만든 리소스의 정리가 완료될 때까지 기다리세요. 그렇지 않으면 다음 단계를 사용하여 삭제합니다.

1. 개발 VM, IoT Edge VM, IoT Hub, 스토리지 계정, 기계 학습 작업 영역 서비스를 보관하기 위해 만든 리소스 그룹(및 만든 리소스: 컨테이너 레지스트리, Application Insights, 키 자격 증명 모음, 스토리지 계정)을 삭제합니다.

1. [Azure Notebooks](https://notebooks.azure.com)에서 기계 학습 프로젝트를 삭제합니다.

1. 리포지토리를 로컬로 복제한 경우 로컬 리포지토리를 가리키는 모든 PowerShell 또는 VS Code 창을 닫고, 리포지토리 디렉터리를 삭제합니다.

1. 인증서를 로컬로 만든 경우 c:\\edgeCertificates 폴더를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 개발 VM을 사용하여 IoT Edge 디바이스로 센서 및 작동 데이터를 보내는 리프 디바이스를 시뮬레이션했습니다. 에지 디바이스의 실시간 운영을 검사하고 스토리지 계정에 업로드된 파일을 확인하여 이 디바이스의 모듈이 데이터를 라우팅, 분류, 유지 및 업로드하는지 확인했습니다.

자세한 내용은 다음 페이지에서 확인할 수 있습니다.

* [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)
* [IoT Edge(미리 보기)에서 Azure Blob Storage를 사용하여 에지에 데이터 저장](how-to-store-data-blob.md)
