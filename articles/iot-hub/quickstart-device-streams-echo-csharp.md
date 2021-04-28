---
title: 빠른 시작 - IoT Hub 디바이스 스트림을 통해 C#에서 디바이스 앱과 통신
description: 이 빠른 시작에서는 IoT Hub를 통해 설정된 디바이스 스트림을 통해 통신하는 두 개의 샘플 C# 애플리케이션을 실행합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c4f6e06524412c76661623cb5ef2985a41f2d7fc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864092"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>빠른 시작: IoT Hub 디바이스 스트림을 통해 C#에서 디바이스 애플리케이션과 통신(미리 보기)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub는 현재 디바이스 스트림을 [미리 보기 기능](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)으로 지원합니다.

[IoT Hub 디바이스 스트림](./iot-hub-device-streams-overview.md)은 서비스 및 디바이스 애플리케이션이 안전하고 방화벽 친화적인 방식으로 통신할 수 있도록 합니다. 이 빠른 시작에서는 디바이스 스트림을 활용하여 데이터를 주고 받는(에코) 두 개의 C# 애플리케이션이 사용됩니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 디바이스 스트림 미리 보기는 현재 다음 Azure 지역에서 만든 IoT Hub만 지원합니다.
  * 미국 중부
  * 미국 중부 EUAP
  * 북유럽
  * 동남아시아

* 이 빠른 시작에서 실행하는 두 개의 샘플 애플리케이션은 C#으로 작성되었습니다. 개발 머신에는 .NET Core SDK 2.1.0 이상이 필요합니다.

    [.NET에서 여러 플랫폼에 대한 .NET Core SDK](https://dotnet.microsoft.com/download)를 다운로드하세요.

    다음 명령을 사용하여 개발 머신에서 C#의 현재 버전을 확인합니다.

    ```
    dotnet --version
    ```

* [Azure IoT C# 샘플을 다운로드](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)하고 ZIP 보관 파일을 추출합니다. 디바이스와 서비스 양쪽에 필요합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>디바이스 등록

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 섹션에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. 디바이스 ID를 만들려면 Cloud Shell에서 다음 명령을 실행합니다.

   > [!NOTE]
   > * *YourIoTHubName* 자리 표시자를 IoT 허브에서 선택한 이름으로 바꿉니다.
   > * 등록하려는 디바이스의 이름에는 표시된 대로 *MyDevice* 를 사용하는 것이 좋습니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서 해당 이름을 사용하고, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후에 실행하세요.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. 방금 등록한 디바이스의 *디바이스 연결 문자열* 을 가져오려면 Cloud Shell에서 다음 명령을 실행합니다.

   > [!NOTE]
   > *YourIoTHubName* 자리 표시자를 IoT 허브에서 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    나중에 이 빠른 시작에서 사용할 수 있도록 반환된 디바이스 연결 문자열을 적어 두세요. 다음 예제와 유사합니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. 또한 서비스 쪽 애플리케이션을 IoT Hub에 연결하고 디바이스 스트림을 설정하기 위해 IoT Hub에서 *서비스 연결 문자열* 이 필요합니다. 다음 명령은 IoT Hub에 대한 이 값을 검색합니다.

   > [!NOTE]
   > *YourIoTHubName* 자리 표시자를 IoT 허브에서 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    나중에 이 빠른 시작에서 사용할 수 있도록 반환된 서비스 연결 문자열을 적어 두세요. 다음 예제와 유사합니다.

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>디바이스 스트림을 통해 디바이스와 서비스 간 통신

이 섹션에서는 디바이스 쪽 애플리케이션과 서비스 쪽 애플리케이션을 둘 다 실행하고 서로 통신합니다.

### <a name="run-the-service-side-application"></a>서비스 쪽 애플리케이션 실행

로컬 터미널 창에서 압축 해제된 프로젝트 폴더의 `iot-hub/Quickstarts/device-streams-echo/service` 디렉터리로 이동합니다. 다음 정보를 잘 보관해 둡니다.

| 매개 변수 이름 | 매개 변수 값 |
|----------------|-----------------|
| `ServiceConnectionString` | IoT 허브의 서비스 연결 문자열입니다. |
| `MyDevice` | 이전에 만든 디바이스의 식별자입니다. |

다음 명령을 사용하여 코드를 컴파일하고 실행합니다.

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
애플리케이션은 디바이스 애플리케이션을 사용할 수 있을 때까지 기다립니다.

> [!NOTE]
> 디바이스 쪽 애플리케이션에서 시간 내에 응답하지 않는 경우 시간 초과가 발생합니다.

### <a name="run-the-device-side-application"></a>디바이스 쪽 애플리케이션 실행

다른 로컬 터미널 창에서 압축 해제된 프로젝트 폴더의 `iot-hub/Quickstarts/device-streams-echo/device` 디렉터리로 이동합니다. 다음 정보를 잘 보관해 둡니다.

| 매개 변수 이름 | 매개 변수 값 |
|----------------|-----------------|
| `DeviceConnectionString` | IoT Hub의 디바이스 연결 문자열입니다. |

다음 명령을 사용하여 코드를 컴파일하고 실행합니다.

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

마지막 단계가 끝나면 서비스 쪽 애플리케이션이 스트림을 디바이스로 보냅니다. 스트림이 설정되면 애플리케이션이 스트림을 통해 서비스로 문자열 버퍼를 보냅니다. 이 샘플의 서비스 쪽 애플리케이션은 동일한 데이터를 간단하게 다시 디바이스로 에코하며, 이는 두 애플리케이션 간의 성공적인 양방향 통신을 보여줍니다.

디바이스 쪽 콘솔 출력:

![디바이스 쪽 콘솔 출력](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

서비스 쪽 콘솔 출력:

![서비스 쪽 콘솔 출력](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

스트림을 통해 전송되는 트래픽은 직접 전송되는 것이 아니라 IoT Hub를 통해 터널링됩니다. 제공되는 이점은 [디바이스 스트림 이점](./iot-hub-device-streams-overview.md#benefits)에 자세히 나와 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 허브를 설정하고, 디바이스를 등록하고, 디바이스 및 서비스 쪽에서 C# 애플리케이션 간 디바이스 스트림을 설정하고, 스트림을 사용하여 애플리케이션 간에 데이터를 주고 받았습니다.

디바이스 스트림에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [디바이스 스트림 개요](./iot-hub-device-streams-overview.md)
