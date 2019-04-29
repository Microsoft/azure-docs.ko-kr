---
title: Visual Studio용 클라우드 탐색기를 사용하여 Azure IoT Hub 클라우드 디바이스 메시지 관리 | Microsoft Docs
description: Visual Studio용 클라우드 탐색기를 사용하여 디바이스-클라우드 메시지를 모니터링하고 클라우드를 Azure IoT Hub의 디바이스 메시지로 보내는 방법을 알아봅니다.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
origin.date: 01/07/2018
ms.date: 05/06/2019
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440746"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Visual Studio용 클라우드 탐색기를 사용하여 디바이스와 IoT Hub 간에 메시지 보내고 받기

![종단 간 다이어그램](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)는 Azure 리소스를 보고, 해당 속성을 검사하고, Visual Studio 내에서 핵심 개발자 작업을 수행할 수 있는 유용한 Visual Studio 확장입니다. 이 문서에서는 클라우드 탐색기를 사용하여 디바이스와 IoT Hub 간에 메시지를 보내고 받는 방법에 중점을 둡니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>알아볼 내용

Visual Studio용 클라우드 탐색기를 사용하여 디바이스-클라우드 메시지를 모니터링하고 클라우드-디바이스 메시지를 보내는 방법을 알아봅니다. 디바이스-클라우드 메시지는 디바이스에서 수집한 다음, IoT Hub로 보내는 센서 데이터일 수 있습니다. 클라우드-디바이스 메시지는 IoT Hub에서 디바이스로 보내는 명령일 수 있습니다. 예를 들어 디바이스에 연결된 LED를 깜박입니다.

## <a name="what-you-will-do"></a>수행할 사항

- Visual Studio용 클라우드 탐색기를 사용하여 디바이스-클라우드 메시지를 모니터링합니다.
- Visual Studio용 클라우드 탐색기를 사용하여 클라우드-디바이스 메시지를 보냅니다.

## <a name="what-you-need"></a>필요한 항목

- 활성 Azure 구독.
- 구독 중인 Azure IoT Hub
- Microsoft Visual Studio 2017 업데이트 8 이상
- Visual Studio 설치 관리자의 클라우드 탐색기 구성 요소(Azure 워크로드를 사용하여 기본적으로 선택됨)

## <a name="update-cloud-explorer-to-latest-version"></a>최신 버전으로 클라우드 탐색기 업데이트

Visual Studio 설치 관리자의 클라우드 탐색기 구성 요소는 디바이스-클라우드 및 클라우드-디바이스 메시지 모니터링만을 지원합니다. 디바이스 또는 클라우드에 메시지를 보내려면 최신 [클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)를 다운로드하고 설치합니다.

## <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

1. Visual Studio **클라우드 탐색기** 창에서 계정 관리 아이콘을 클릭합니다. **보기** > **클라우드 탐색기** 메뉴에서 클라우드 탐색기 창을 열 수 있습니다.

    ![계정 관리 클릭](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. 클라우드 탐색기에서 **계정 관리**를 클릭합니다.
1. 새 창에서 **계정 추가...** 를 클릭하여 처음으로 Azure에 로그인합니다.
1. 로그인한 후 Azure 구독 목록이 나타납니다. 보려는 Azure 구독을 선택하고 **적용**을 클릭합니다.
1. **구독** > **IoT Hub** > **IoT Hub**를 확장하면 IoT Hub 노드 아래에 디바이스 목록이 표시됩니다.

    ![디바이스 목록](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>디바이스-클라우드 메시지 모니터링

디바이스에서 IoT Hub로 보낸 메시지를 모니터링하려면 다음 단계를 수행합니다.

1. IoT Hub 또는 디바이스를 마우스 오른쪽 단추로 클릭하고 **D2C 메시지 모니터링 시작**을 선택합니다.

    ![D2C 메시지 모니터링 시작](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

1. 모니터링되는 메시지가 **IoT Hub** 출력 창에 표시됩니다.

    ![D2C 메시지 모니터링 결과](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

1. 모니터링을 중지하려면 IoT Hub 또는 디바이스를 마우스 오른쪽 단추로 클릭하고 **D2C 메시지 모니터링 중지**를 선택합니다.

## <a name="send-cloud-to-device-messages"></a>클라우드-장치 메시지 보내기

IoT Hub에서 디바이스로 메시지를 보내려면 다음 단계를 수행합니다.

1. 디바이스를 마우스 오른쪽 단추로 클릭하고 **C2D 메시지 보내기**를 선택합니다.

    ![C2D 메시지 보내기](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

1. 입력 상자에 메시지를 입력합니다.
1. **IoT Hub** 출력 창에 결과가 표시됩니다.

    ![C2D 메시지 보내기 결과](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>다음 단계

IoT 디바이스와 Azure IoT Hub 간에 디바이스-클라우드 메시지를 모니터링하고 클라우드-디바이스 메시지를 보내는 방법을 알아보았습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]