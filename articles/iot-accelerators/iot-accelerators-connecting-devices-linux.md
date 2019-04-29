---
title: C로 Linux 디바이스를 원격 모니터링에 프로비전 - Azure | Microsoft Docs
description: C로 작성한 애플리케이션을 Linux에서 실행하여 원격 모니터링 솔루션 가속기에 장치를 연결하는 방법을 설명합니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61454500"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>디바이스를 원격 모니터링 솔루션 가속기에 연결(Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

이 자습서는 원격 모니터링 솔루션 가속기에 실제 디바이스를 연결하는 방법을 보여 줍니다.

제한된 장치에서 실행되는 대부분의 임베디드 애플리케이션과 마찬가지로, 장치 애플리케이션의 클라이언트 코드는 C로 작성됩니다. 이 자습서에서는 Ubuntu(Linux)를 실행하는 컴퓨터에서 애플리케이션을 빌드합니다.

디바이스를 시뮬레이션하려면 [새 시뮬레이션된 디바이스 만들기 및 테스트](iot-accelerators-remote-monitoring-create-simulated-device.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 완료하려면 Ubuntu 버전 15.04 이상을 실행하는 디바이스가 필요합니다. 계속하기 전에 [Linux 개발 환경 설정](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)을 수행하세요.

## <a name="view-the-code"></a>코드 보기

이 가이드에서 사용되는 [샘플 코드](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client)는 Azure IoT C SDK GitHub 리포지토리에서 제공됩니다.

### <a name="download-the-source-code-and-prepare-the-project"></a>소스 코드 다운로드 및 프로젝트 준비

프로젝트를 준비하려면 GitHub에서 [Azure IoT C SDK 리포지토리](https://github.com/Azure/azure-iot-sdk-c)를 복제하거나 다운로드합니다.

샘플은 **samples/solutions/remote_monitoring_client** 폴더에 있습니다.

**samples/solutions/remote_monitoring_client** 폴더의 **remote_monitoring.c** 파일을 텍스트 편집기에서 엽니다.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행

다음 단계에서는 *CMake*를 사용하여 클라이언트 응용 프로그램을 빌드하는 방법을 설명합니다. 원격 모니터링 클라이언트 애플리케이션은 SDK 빌드 프로세스의 일부로 빌드됩니다.

1. **remote_monitoring.c** 파일을 편집하여 `<connectionstring>`을 이 방법 가이드 시작 부분에서 솔루션 가속기에 디바이스를 추가할 때 적어 둔 디바이스 연결 문자열로 바꿉니다.

1. [Azure IoT C SDK 리포지토리](https://github.com/Azure/azure-iot-sdk-c)의 복제된 복사본 루트로 이동한 후에 다음 명령을 실행하여 클라이언트 응용 프로그램을 빌드합니다.

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. 클라이언트 애플리케이션을 실행하고 IoT Hub에 원격 분석을 전송합니다.

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    다음과 같은 경우 콘솔에 메시지가 표시됩니다.

    - 애플리케이션이 샘플 원격 분석 데이터를 솔루션 가속기에 보낼 때.
    - 솔루션 대시보드에서 호출된 메서드에 응답할 때.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
