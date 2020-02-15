---
title: Python을 사용하여 디바이스에서 Azure IoT Hub로 파일 업로드 | Microsoft Docs
description: Python용 Azure IoT 디바이스 SDK를 사용하여 디바이스에서 클라우드로 파일을 업로드 하는 방법입니다. 업로드된 파일은 Azure Storage blob 컨테이너에 저장됩니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: f1c0c046c40ff8edbc33c5e93e4207d9fe2fc67a
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110755"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>IoT Hub (Python)를 사용 하 여 장치에서 클라우드로 파일 업로드

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

이 문서에서는 [IoT Hub의 파일 업로드 기능](iot-hub-devguide-file-upload.md)을 사용하여 [Azure Blob Storage](../storage/index.yml)에 파일을 업로드하는 방법에 대해 설명합니다. 이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 파일을 업로드하기 위해 스토리지 컨테이너를 안전하게 제공합니다.

* Python 클라이언트를 사용하여 IoT 허브를 통해 파일을 업로드합니다.

[장치에서 IoT hub로 원격 분석 전송 빠른 시작](quickstart-send-telemetry-python.md) 은 IoT Hub의 기본적인 장치-클라우드 메시징 기능을 보여 줍니다. 그러나 일부 시나리오에서는 디바이스에서 전송하는 데이터를 IoT Hub에서 허용하는 비교적 작은 디바이스-클라우드 메시지에 쉽게 매핑할 수 없습니다. 디바이스에서 파일을 업로드해야 할 때 IoT Hub의 보안 및 안정성을 여전히 사용할 수 있습니다.

> [!NOTE]
> IoT Hub Python SDK는 현재 **.txt** 파일과 같은 문자 기반 파일의 업로드만 지원합니다.

이 자습서의 끝 부분에서 Python 콘솔 앱을 실행합니다.

* **FileUpload.py**는 Python 디바이스 SDK를 사용하여 파일을 스토리지로 업로드합니다.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> 이 가이드에서는 새로운 V2 SDK에서 파일 업로드 기능이 아직 구현 되지 않았기 때문에 사용 되지 않는 V1 Python SDK를 사용 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* 방화벽에서 포트 8883가 열려 있는지 확인 합니다. 이 문서의 device 샘플에서는 포트 8883을 통해 통신 하는 MQTT 프로토콜을 사용 합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단 될 수 있습니다. 이 문제를 해결 하는 방법 및 방법에 대 한 자세한 내용은 [IoT Hub에 연결 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조 하세요.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>디바이스 앱에서 파일 업로드

이 섹션에서는 IoT Hub에 파일을 업로드하는 디바이스 앱을 만듭니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub-device-client** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. 텍스트 편집기를 사용하여 Blob Storage에 업로드할 테스트 파일을 만듭니다.

    > [!NOTE]
    > IoT Hub Python SDK는 현재 **.txt** 파일과 같은 문자 기반 파일의 업로드만 지원합니다.

3. 텍스트 편집기를 사용하여 작업 폴더에 **FileUpload.py** 파일을 만듭니다.

4. `import`FileUpload.py**파일의 시작 부분에서 다음** 문 및 변수를 추가합니다. 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. 파일에서 `[Device Connection String]`을 IoT Hub 디바이스의 연결 문자열로 바꿉니다. `[Full path to file]`을 작성한 테스트 파일 또는 업로드할 디바이스의 파일 경로로 바꿉니다. `[File name for storage]`를 Blob Storage에 업로드한 후 파일에 부여할 이름으로 바꿉니다. 

6. **upload_blob** 함수에 대한 콜백을 만듭니다.

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. 다음 코드를 추가하여 클라이언트를 연결하고 파일을 업로드합니다. `main` 루틴도 포함하세요.

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. **UploadFile.py** 파일을 저장하고 닫습니다.

## <a name="run-the-application"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. 작업 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```cmd/sh
    python FileUpload.py
    ```

2. 다음 스크린샷은 **FileUpload** 앱의 출력을 보여줍니다.

    ![simulated-device 앱의 출력](./media/iot-hub-python-python-file-upload/1.png)

3. 포털을 사용하면 구성한 스토리지 컨테이너에 업로드된 파일을 볼 수 있습니다.

    ![업로드된 파일](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스에서 파일 업로드를 단순화하기 위해 IoT Hub의 파일 업로드 기능을 사용하는 방법을 알아보았습니다. 다음 문서를 사용하여 IoT Hub 기능 및 시나리오를 계속 탐색할 수 있습니다.

* [프로그래밍 방식으로 IoT Hub 만들기](iot-hub-rm-template-powershell.md)

* [C SDK 소개](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)
