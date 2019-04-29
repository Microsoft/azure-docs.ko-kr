---
title: C로 Windows 디바이스를 원격 모니터링에 프로비전 - Azure | Microsoft Docs
description: Windows에서 실행 중인 C로 작성한 애플리케이션을 Linux에서 실행하여 원격 모니터링 솔루션 가속기에 장치를 연결하는 방법을 설명합니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450232"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>디바이스를 원격 모니터링 솔루션 가속기에 연결(Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

이 자습서는 원격 모니터링 솔루션 가속기에 실제 디바이스를 연결하는 방법을 보여 줍니다.

제한된 장치에서 실행되는 대부분의 임베디드 애플리케이션과 마찬가지로, 장치 애플리케이션의 클라이언트 코드는 C로 작성됩니다. 이 자습서에서는 Windows를 실행하는 머신에서 장치 클라이언트 애플리케이션을 빌드합니다.

디바이스를 시뮬레이션하려면 [새 시뮬레이션된 디바이스 만들기 및 테스트](iot-accelerators-remote-monitoring-create-simulated-device.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 완료하려면 [Windows 개발 환경 설정](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment)의 단계에 따라 필요한 개발 도구 및 라이브러리를 Windows 머신에 추가합니다.

## <a name="view-the-code"></a>코드 보기

이 가이드에서 사용되는 [샘플 코드](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client)는 Azure IoT C SDK GitHub 리포지토리에서 제공됩니다.

### <a name="download-the-source-code-and-prepare-the-project"></a>소스 코드 다운로드 및 프로젝트 준비

프로젝트를 준비하려면 GitHub에서 [Azure IoT C SDK 리포지토리를 복제](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment)합니다.

샘플은 **samples/solutions/remote_monitoring_client** 폴더에 있습니다.

**samples/solutions/remote_monitoring_client** 폴더의 **remote_monitoring.c** 파일을 텍스트 편집기에서 엽니다.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

1. **remote_monitoring.c** 파일을 편집하여 `<connectionstring>`을 이 방법 가이드 시작 부분에서 솔루션 가속기에 장치를 추가할 때 적어 둔 장치 연결 문자열로 바꿉니다.

1. [Windows에서 C SDK 빌드](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows)의 단계에 따라 SDK 및 원격 모니터링 클라이언트 애플리케이션을 빌드합니다.

1. 솔루션을 빌드하는 데 사용한 명령 프롬프트에서 다음을 실행합니다.

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    다음과 같은 경우 콘솔에 메시지가 표시됩니다.

    - 애플리케이션이 샘플 원격 분석 데이터를 솔루션 가속기에 보낼 때.
    - 솔루션 대시보드에서 호출된 메서드에 응답할 때.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
