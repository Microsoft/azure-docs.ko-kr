---
title: C를 사용하여 원격 모니터링으로 Raspberry Pi 프로비전 - Azure | Microsoft Docs
description: C에 작성된 애플리케이션을 사용하여 원격 모니터링 솔루션 가속기에 Raspberry Pi 장치를 연결하는 방법을 설명합니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61454505"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>원격 모니터링 솔루션 가속기에 Raspberry Pi 디바이스 연결(C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

이 자습서는 원격 모니터링 솔루션 가속기에 실제 디바이스를 연결하는 방법을 보여 줍니다. 제한된 장치에서 실행되는 포함된 애플리케이션과 마찬가지로 Raspberry Pi 장치 애플리케이션에 대한 클라이언트 코드는 C에서 작성되었습니다. 이 자습서에서는 Raspbian OS를 실행 중인 Raspberry Pi에서 애플리케이션을 빌드합니다.

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

이 문서에서는 [Raspberry Pi에 Raspbian OS](https://www.raspberrypi.org/learning/software-guide/quickstart/)의 최신 버전을 설치했다고 가정합니다.

다음 단계는 솔루션 가속기에 연결하는 C 애플리케이션을 빌드하기 위해 Raspberry Pi를 준비하는 방법을 보여줍니다.

1. **ssh**를 사용하여 Raspberry Pi에 연결합니다. 자세한 내용은 [Raspberry Pi 웹 사이트](https://www.raspberrypi.org/)에서 [SSH(Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md)를 참조하세요.

1. 다음 명령을 사용하여 Raspberry Pi를 업데이트합니다.

    ```sh
    sudo apt-get update
    ```

1. 이 방법 가이드의 단계를 완료하려면 [Linux 개발 환경 설정](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)의 단계에 따라 필요한 개발 도구 및 라이브러리를 Raspberry Pi에 추가합니다.

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
