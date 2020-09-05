---
title: IoT 플러그 앤 플레이 미리 보기 샘플 Python 구성 요소 디바이스 코드를 IoT Hub에 연결 | Microsoft Docs
description: 여러 구성 요소를 사용하고 IoT 허브에 연결하는 IoT 플러그 앤 플레이 미리 보기 샘플 Python 디바이스 코드를 빌드하고 실행합니다. Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 571f0e0ceff0adfbf1814abc627fcab6b23acbe1
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905859"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-python"></a>자습서: 샘플 IoT 플러그 앤 플레이 미리 보기 여러 구성 요소 디바이스 애플리케이션을 IoT Hub(Python)에 연결

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

이 자습서에서는 구성 요소 및 루트 인터페이스를 사용하여 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT 허브에 연결하고, Azure IoT 탐색기 도구를 사용하여 허브로 전송된 정보를 확인하는 방법을 보여줍니다. 샘플 애플리케이션은 Python으로 작성되었으며 Python용 Azure IoT 디바이스 SDK에 포함되어 있습니다. 솔루션 빌더는 디바이스 코드를 볼 필요 없이 Azure IoT 탐색기 도구를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 개발 머신에 Python 3.7이 필요합니다. [python.org](https://www.python.org/)에서 여러 플랫폼의 최신 권장 버전을 다운로드할 수 있습니다. 다음 명령을 사용하여 현재 Python 버전을 확인할 수 있습니다.  

```cmd/sh
python --version
```

[python.org](https://www.python.org/)에서 여러 플랫폼의 최신 권장 버전을 다운로드할 수 있습니다.

### <a name="azure-iot-explorer"></a>Azure IoT 탐색기

이 자습서의 두 번째 부분에서 샘플 디바이스와 상호 작용하려면 **Azure IoT 탐색기** 도구를 사용합니다. 운영 체제용 [최신 릴리스의 Azure IoT 탐색기를 다운로드하여 설치](./howto-use-iot-explorer.md)합니다.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행하여 허브에 대한 _IoT 허브 연결 문자열_을 가져옵니다. 이 연결 문자열을 기록해 둡니다. 이 자습서의 뒷부분에서 필요합니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Azure IoT 탐색기 도구를 사용하여 IoT 허브 연결 문자열을 찾을 수도 있습니다.

다음 명령을 실행하여 허브에 추가한 디바이스에 대한 _디바이스 연결 문자열_을 가져옵니다. 이 연결 문자열을 기록해 둡니다. 이 자습서의 뒷부분에서 필요합니다.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="set-up-your-environment"></a>환경 설정

이 패키지는 공개 미리 보기 새로 고침을 위한 PIP로 게시됩니다. 패키지 버전은 `2.1.4` 이상이어야 합니다.

로컬 Python 환경에서 다음과 같이 파일을 설치합니다.

```cmd/sh
pip install azure-iot-device
```

다음과 같이 Python SDK IoT 리포지토리를 복제하고 **pnp-preview-refresh**를 확인합니다.

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>코드 검토

이 샘플은 IoT 플러그 앤 플레이 온도 컨트롤러 디바이스를 구현합니다. 이 샘플에서 구현하는 모델은 [여러 구성 요소](concepts-components.md)를 사용합니다. [온도 디바이스용 DTDL(Digital Twins 정의 언어) 모델 파일](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)은 디바이스에서 구현하는 원격 분석, 속성 및 명령을 정의합니다.

*azure-iot-sdk-python\azure-iot-device\samples\pnp* 폴더에는 IoT 플러그 앤 플레이 디바이스에 대한 샘플 코드가 포함되어 있습니다. 온도 컨트롤러 샘플의 파일은 다음과 같습니다.

- pnp_temp_controller_with_thermostats.py
- pnp_helper_preview_refresh.py

온도 컨트롤러에는 온도 컨트롤러 DTDL 모델을 기반으로 하는 여러 구성 요소와 루트 인터페이스가 있습니다.

선택한 편집기에서 *pnp_temp_controller_with_thermostats.py* 파일을 엽니다. 이 파일의 코드는 다음과 같습니다.

1. 도우미 메서드에 대한 액세스 권한을 얻기 위해 `pnp_helper_preview_refresh.py`를 가져옵니다.

2. DTDL 모델에 정의된 두 개의 서로 다른 인터페이스를 고유하게 나타내는 DTMI(디지털 쌍 모델 식별자) 두 개를 정의합니다. 실제 온도 컨트롤러의 구성 요소는 이러한 두 인터페이스를 구현해야 합니다. 이러한 두 인터페이스는 중앙 리포지토리에 이미 게시되어 있습니다. 이러한 DTMI는 사용자에게 알려져야 하며 디바이스 구현 시나리오에 따라 달라집니다. 현재 샘플의 경우 두 인터페이스가 다음을 나타냅니다.

  - 자동 온도 조절기
  - Azure에서 개발한 디바이스 정보입니다.

3. 구현되는 디바이스의 DTMI `model_id`를 정의합니다. DTMI는 사용자 정의되며 DTDL 모델 파일의 DTMI와 일치해야 합니다.

4. DTDL 파일의 구성 요소에 지정된 이름을 정의합니다. DTDL에는 두 개의 자동 온도 조절기와 하나의 디바이스 정보 구성 요소가 있습니다. 또한 `serial_number`라는 상수가 루트 인터페이스에 정의됩니다. 디바이스의 `serial_number`를 변경할 수 없습니다.

5. 명령 처리기 구현을 정의합니다. 디바이스가 명령 요청을 수신할 때 수행하는 작업을 정의합니다.

6. 명령 응답을 만드는 함수를 정의합니다. 디바이스가 명령 요청에 응답하는 방법을 정의합니다. 명령에서 사용자 지정 응답을 다시 IoT 허브로 보내야 하는 경우 명령 응답 함수를 만듭니다. 명령에 대한 응답 함수를 제공하지 않으면 일반 응답이 전송됩니다. 이 샘플에서는 **getMaxMinReport** 명령에만 사용자 지정 응답이 있습니다.

7. 이 디바이스의 원격 분석 데이터를 전송하는 함수를 정의합니다. 자동 온도 조절기와 루트 인터페이스 둘 다 원격 분석 데이터를 전송합니다. 이 함수는 선택적 구성 요소 이름 매개 변수를 가져와서 원격 분석 데이터를 보낸 구성 요소를 식별할 수 있도록 합니다.

8. 명령 요청의 수신기를 정의합니다.

9. desired 속성 업데이트에 대한 수신기를 정의합니다.

10. 다음과 같은 `main` 함수를 갖고 있습니다.

    1. 디바이스 SDK를 사용하여 디바이스 클라이언트를 만들고 IoT 허브에 연결합니다. 디바이스에서 `model_id`를 보냅니다. 그러면 IoT 허브가 디바이스를 IoT 플러그 앤 플레이 디바이스로 식별할 수 있습니다.

    1. 도우미 파일의 `create_reported_properties` 함수를 사용하여 속성을 만듭니다. 구성 요소 이름과 속성을 이 함수에 키 값 쌍으로 전달합니다.

    1. `patch_twin_reported_properties`를 호출하여 해당 구성 요소의 읽기 가능 속성을 업데이트합니다.

    1. `execute_command_listener` 함수를 사용하여 명령 요청 수신 대기를 시작합니다. 이 함수는 서비스의 명령 요청에 대한 수신기를 설정합니다. 수신기를 설정할 때 `method_name`, `user_command_handler` 및 선택적 `create_user_response_handler`를 매개 변수로 제공합니다.
        - `method_name`은 명령 요청을 정의합니다. 이 샘플의 모델은 **reboot** 및 **getMaxMinReport** 명령을 정의합니다.
        - `user_command_handler` 함수는 디바이스가 명령을 수신할 때 수행할 작업을 정의합니다.
        - `create_user_response_handler` 함수는 명령이 성공적으로 실행될 때 IoT 허브로 전송할 응답을 만듭니다. 포털에서 이 응답을 볼 수 있습니다. 이 함수를 제공하지 않으면 일반 응답이 서비스로 전송됩니다.

    1. `execute_property_listener`를 사용하여 속성 업데이트를 수신 대기합니다.

    1. `send_telemetry`를 사용하여 원격 분석 보내기를 시작합니다. 샘플 코드는 루프를 사용하여 세 가지 원격 분석 보내기 함수를 호출합니다. 각 함수는 8초마다 호출됩니다.

    1. **Q** 또는 **q**를 누르면 모든 수신기 및 작업을 사용하지 않도록 설정하고 루프를 종료합니다.

코드를 살펴보았으므로, **IOTHUB_DEVICE_CONNECTION_STRING**이라는 환경 변수를 만들어 이전에 기록한 디바이스 연결 문자열을 저장합니다. 다음 명령을 사용하여 샘플을 실행합니다.

```cmd/sh
python pnp_temp_controller_with_thermostats.py
```

샘플 디바이스는 몇 초마다 원격 분석 메시지를 IoT 허브로 보냅니다.

디바이스가 허브로 원격 분석 데이터를 보내고 있으며, 이제 명령 및 속성 업데이트를 받을 준비가 되었다는 것을 나타내는 다음 출력이 표시됩니다.

![디바이스 확인 메시지](media/tutorial-multiple-components-python/multiple-component.png)

다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작된 후 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT 플러그 앤 플레이 디바이스를 구성 요소와 IoT 허브에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스 모델에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT 플러그 앤 플레이 미리 보기 모델링 개발자 가이드](concepts-developer-guide.md)
