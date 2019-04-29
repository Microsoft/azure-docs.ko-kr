---
title: 디바이스 시뮬레이션에서 프로토콜 버퍼 사용 - Azure| Microsoft Docs
description: 이 사용 방법 가이드에서는 프로토콜 버퍼를 사용하여 디바이스 시뮬레이션 솔루션 가속기에서 보낸 원격 분석을 직렬화하는 방법을 알아봅니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 74bb2d181533f802e1428eaa8a855f60fb855193
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447984"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>프로토콜 버퍼를 사용하여 원격 분석 직렬화

Protobuf(프로토콜 버퍼)는 구조화된 데이터의 이진 직렬화 형식입니다. Protobuf는 XML보다 더 작고 빠른 것을 목표로 하여 단순성과 성능을 강화하도록 설계되었습니다.

디바이스 시뮬레이션은 **proto3** 버전의 프로토콜 버퍼 언어를 지원합니다.

Protobuf는 데이터를 직렬화하기 위해 컴파일된 코드가 필요하므로 디바이스 시뮬레이션의 사용자 지정 버전을 빌드해야 합니다.

이 방법 가이드의 단계에서는 다음을 수행하는 방법을 보여 줍니다.

1. 개발 환경 준비
1. 디바이스 모델에서 Protobuf 형식을 사용하여 지정
1. Protobuf 형식 정의
1. Protobuf 클래스 생성
1. 로컬에서 테스트

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 수행하려면 다음이 필요합니다.

* Visual Studio Code. [Mac, Linux 및 Windows용 Visual Studio Code를 다운로드](https://code.visualstudio.com/download)할 수 있습니다.
* .NET Core [Mac, Linux 및 Windows용 .NET Core를 다운로드](https://www.microsoft.com/net/download)할 수 있습니다.
* Postman [Mac, Windows 또는 Linux용 Postman](https://www.getpostman.com/apps)을 다운로드할 수 있습니다.
* [Azure 구독에 배포된 IoT 허브](../iot-hub/iot-hub-create-through-portal.md) 이 가이드의 단계를 완료하려면 IoT 허브의 연결 문자열이 필요합니다. Azure Portal에서 연결 문자열을 가져올 수 있습니다.
* SQL API를 사용하고 [강력한 일관성](../cosmos-db/manage-account.md)으로 구성되어 [Azure 구독에 배포된 Cosmos DB 데이터베이스](../cosmos-db/create-sql-api-dotnet.md#create-account). 이 가이드의 단계를 완료하려면 Cosmos DB 데이터베이스의 연결 문자열이 필요합니다. Azure Portal에서 연결 문자열을 가져올 수 있습니다.
* [Azure 구독에 배포된 Azure 스토리지 계정](../storage/common/storage-quickstart-create-account.md). 이 가이드의 단계를 완료하려면 스토리지 계정의 연결 문자열이 필요합니다. Azure Portal에서 연결 문자열을 가져올 수 있습니다.

## <a name="prepare-your-development-environment"></a>개발 환경 준비

개발 환경을 준비하려면 다음 작업을 완료합니다.

* 디바이스 시뮬레이션 마이크로 서비스에 대한 소스를 다운로드합니다.
* 저장소 어댑터 마이크로 서비스에 대한 소스 다운로드
* 저장소 어댑터 마이크로 서비스를 로컬로 실행

이 문서의 지침에서는 Windows를 사용한다고 가정합니다. 다른 운영 체제를 사용하는 경우 일부 파일 경로 및 명령을 사용자 환경에 맞게 조정해야 합니다.

### <a name="download-the-microservices"></a>마이크로 서비스 다운로드

[원격 모니터링 마이크로 서비스](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip)를 GitHub에서 로컬 머신의 적절한 위치에 다운로드하고 압축을 풉니다. 이 리포지토리에는 이 방법에 필요한 스토리지 어댑터 마이크로 서비스가 포함되어 있습니다.

GitHub에서 [디바이스 시뮬레이션 마이크로 서비스](https://github.com/Azure/device-simulation-dotnet/archive/master.zip)를 로컬 머신의 적합한 위치에 다운로드하고 압축을 풉니다.

### <a name="run-the-storage-adapter-microservice"></a>저장소 어댑터 마이크로 서비스 실행

Visual Studio Code에서 **remote-monitoring-services-dotnet-master\storage-adapter** 폴더를 엽니다. **복원** 단추를 클릭하여 확인할 수 없는 종속성을 수정합니다.

**.vscode/launch.json** 파일을 열고, Cosmos DB 연결 문자열을 **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 환경 변수에 할당합니다.

> [!NOTE]
> 머신에서 마이크로 서비스를 로컬로 실행하는 경우 제대로 작동하려면 여전히 Azure에 Cosmos DB 인스턴스가 필요합니다.

스토리지 어댑터 마이크로 서비스를 로컬로 실행하려면 **디버그 \> 디버깅 시작**을 차례로 클릭합니다.

Visual Studio Code의 **터미널** 창에서 웹 서비스 상태 확인에 대한 URL(<http://127.0.0.1:9022/v1/status>)을 포함하여 실행 중인 마이크로 서비스의 출력을 표시합니다. 이 주소로 이동 하면 상태가 이어야 합니다 "확인: 활성 및 양호 "입니다.

다음 단계를 완료하는 동안 Visual Studio Code의 이 인스턴스에서 실행되는 스토리지 어댑터 마이크로 서비스는 그대로 유지합니다.

## <a name="define-your-device-model"></a>디바이스 모델 정의

Visual Studio Code의 새 인스턴스에 GitHub에서 다운로드한 **device-simulation-dotnet-master** 폴더를 엽니다. **복원** 단추를 클릭하여 확인할 수 없는 종속성을 수정합니다.

이 방법 가이드에서는 자산 추적기에 대한 새 디바이스 모델을 만듭니다.

1. **assettracker-01.json**이라는 새 디바이스 모델 파일을 **Services\data\devicemodels** 폴더에 만듭니다.

1. **assettracker-01.json** 디바이스 모델 파일에서 디바이스 기능을 정의합니다. Protobuf 디바이스 모델의 원격 분석 섹션에서 다음을 수행해야 합니다.

   * 디바이스에 대해 생성하는 Protobuf 클래스의 이름을 포함합니다. 다음 섹션에서는 이 클래스를 생성하는 방법을 보여 줍니다.
   * Protobuf를 메시지 형식으로 지정합니다.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>디바이스 동작 스크립트 만들기

디바이스의 동작 방식을 정의하는 동작 스크립트를 작성합니다. 자세한 내용은 [고급 시뮬레이션된 디바이스 만들기](iot-accelerators-device-simulation-advanced-device.md)를 참조하세요.

## <a name="define-your-protobuf-format"></a>Protobuf 형식 정의

디바이스 모델이 있고 메시지 형식을 결정했으면 **proto** 파일을 만들 수 있습니다. **proto** 파일에서 다음을 추가합니다.

* 디바이스 모델의 **ClassName** 속성과 일치하는 `csharp_namespace`
* 직렬화할 각 데이터 구조에 대한 메시지
* 메시지의 각 필드에 대한 이름 및 형식

1. **assettracker.proto**라는 새 파일을 **Services\Models\Protobuf\proto** 폴더에 만듭니다.

1. **proto** 파일의 구문, 네임스페이스 및 메시지 스키마를 다음과 같이 정의합니다.

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

각 요소의 `=1`, `=2` 표식은 이진 인코딩에서 필드가 사용하는 고유 태그를 지정합니다. 1-15의 숫자에는 더 높은 숫자보다 1바이트가 적은 인코딩이 필요합니다.

## <a name="generate-the-protobuf-class"></a>Protobuf 클래스 생성

**proto** 파일이 있는 경우 다음 단계는 메시지를 읽고 쓰는 데 필요한 클래스를 생성하는 것입니다. 이 단계를 완료하려면 **Protoc** Protobuf 컴파일러가 필요합니다.

1. [GitHub에서 Protobuf 컴파일러를 다운로드](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)합니다.

1. 컴파일러를 실행하여 소스 디렉터리, 대상 디렉터리 및 **proto** 파일의 이름을 지정합니다. 예를 들면 다음과 같습니다.

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    이 명령은 **Services\Models\Protobuf** 폴더에 **Assettracker.cs** 파일을 생성합니다.

## <a name="test-protobuf-locally"></a>로컬로 Protobuf 테스트

이 섹션에서는 이전 섹션에서 만든 자산 추적기 디바이스를 로컬로 테스트합니다.

### <a name="run-the-device-simulation-microservice"></a>디바이스 시뮬레이션 마이크로 서비스 실행

**.vscode/launch.json** 파일을 열고 다음을 할당합니다.

* **PCS\_IOTHUB\_CONNSTRING** 환경 변수에 대한 IoT Hub 연결 문자열
* **PCS\_AZURE\_STORAGE\_ACCOUNT** 환경 변수에 대한 스토리지 계정 연결 문자열
* **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 환경 변수에 대한 Cosmos DB 연결 문자열

**WebService/Properties/launchSettings.json** 파일을 열고 다음을 할당합니다.

* **PCS\_IOTHUB\_CONNSTRING** 환경 변수에 대한 IoT Hub 연결 문자열
* **PCS\_AZURE\_STORAGE\_ACCOUNT** 환경 변수에 대한 스토리지 계정 연결 문자열
* **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 환경 변수에 대한 Cosmos DB 연결 문자열

**WebService\appsettings.ini** 파일을 열고 설정을 다음과 같이 수정합니다.

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>새 디바이스 모델 파일을 포함하도록 솔루션 구성

기본적으로 새 JSON 및 JS 디바이스 모델 파일은 빌드된 솔루션에 복사되지 않습니다. 명시적으로 포함해야 합니다.

포함하려는 각 파일에 대한 항목을 **services\services.csproj**에 추가합니다. 예를 들면 다음과 같습니다.

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

마이크로 서비스를 로컬로 실행하려면 **디버그 \> 디버깅 시작**을 차례로 클릭합니다.

Visual Studio Code에서 **터미널** 창은 실행 중인 마이크로 서비스에서 출력을 표시합니다.

다음 단계를 완료하는 동안 Visual Studio Code의 이 인스턴스에서 실행되는 디바이스 시뮬레이션 마이크로 서비스를 유지합니다.

### <a name="set-up-a-monitor-for-device-events"></a>디바이스 이벤트에 대한 모니터 설정

이 섹션에서는 Azure CLI를 사용하여 IoT 허브에 연결된 디바이스에서 보낸 원격 분석을 보도록 이벤트 모니터를 설정합니다.

다음 스크립트에서는 IoT 허브의 이름이 **device-simulation-test**라고 가정합니다.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

시뮬레이션된 디바이스를 테스트하는 동안 이벤트 모니터가 실행되도록 둡니다.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>자산 추적기 디바이스 유형으로 시뮬레이션 만들기

이 섹션에서는 Postman 도구를 사용하여 디바이스 시뮬레이션 마이크로 서비스에서 자산 추적기 디바이스 유형을 사용하여 시뮬레이션을 실행하도록 요청합니다. Postman은 웹 서비스에 REST 요청을 보낼 수 있는 도구입니다.

Postman을 설정하려면:

1. 로컬 머신에서 Postman을 엽니다.

1. **파일 \> 가져오기**를 차례로 클릭합니다. 그런 다음, **파일 선택**을 클릭합니다.

1. **Azure IoT Device Simulation solution accelerator.postman\_collection** 및 **Azure IoT Device Simulation solution accelerator.postman\_environment**를 선택하고, **열기**를 클릭합니다.

1. **Azure IoT 디바이스 시뮬레이션 솔루션 가속기**를 확장하여 보낼 수 있는 요청을 봅니다.

1. **환경 없음**을 클릭하고, **Azure IoT 디바이스 시뮬레이션 솔루션 가속기**를 선택합니다.

이제 디바이스 시뮬레이션 마이크로 서비스와 상호 작용하는 데 사용할 수 있는 Postman 작업 영역에 로드된 컬렉션 및 환경이 있습니다.

시뮬레이션을 구성 및 실행하려면:

1. Postman 컬렉션에서 **자산 추적기 시뮬레이션 만들기**를 선택하고, **보내기**를 클릭합니다. 이 요청에서는 시뮬레이션된 자산 추적기 디바이스 유형의 네 가지 인스턴스를 만듭니다.

1. Azure CLI 창의 이벤트 모니터 출력은 시뮬레이션된 디바이스의 원격 분석을 보여 줍니다.

시뮬레이션을 중지하려면 Postman에서 **시뮬레이션 중지** 요청을 선택하고 **보내기**를 클릭합니다.

### <a name="clean-up-resources"></a>리소스 정리

해당 Visual Studio Code 인스턴스에서 로컬로 실행 중인 두 개의 마이크로 서비스를 중지할 수 있습니다(**디버그 \> 디버깅 중지**).

IoT Hub 및 Cosmos DB 인스턴스가 더 이상 필요하지 않은 경우 불필요한 요금 청구를 방지하기 위해 Azure 구독에서 삭제합니다.

## <a name="iot-hub-support"></a>IoT Hub 지원

많은 IoT Hub 기능에서는 Protobuf 또는 다른 이진 형식을 기본적으로 지원하지 않습니다. 예를 들어 IoT Hub에서 메시지 페이로드를 처리할 수 없으므로 메시지 페이로드를 기반으로 하여 라우팅할 수 없습니다. 그러나 메시지 헤더를 기반으로 하여 라우팅할 수는 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Protobuf를 사용하여 원격 분석을 보내는 디바이스 시뮬레이션을 사용자 지정하는 방법을 알아보았으므로, 다음 단계에서는 [사용자 지정 이미지를 클라우드에 배포하는 방법](iot-accelerators-device-simulation-deploy-image.md)을 알아봅니다.
