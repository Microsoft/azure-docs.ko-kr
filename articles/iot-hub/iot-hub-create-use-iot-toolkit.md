---
title: VS Code용 Azure IoT Tools를 사용하여 Azure IoT Hub 만들기 | Microsoft Docs
description: Visual Studio Code용 Azure IoT 도구를 사용하여 리소스 그룹에 Azure IoT Hub를 만드는 방법을 알아봅니다.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75912230"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Visual Studio Code용 Azure IoT Tools를 사용하여 IoT Hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

이 문서에서는 [Visual Studio Code용 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)을 사용하여 Azure IoT Hub를 만드는 방법을 보여줍니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서를 완료하려면 다음이 필요합니다.

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- [Visual Studio Code](https://code.visualstudio.com/)

- Visual Studio Code용 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

1. Visual Studio Code에서 **탐색기** 보기를 엽니다.

2. Explorer의 맨 아래에서 **Azure IoT Hub 디바이스** 섹션을 확장합니다. 

   ![Azure IoT Hub 디바이스 확장](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. **Azure IoT Hub 디바이스** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 마우스로 가리킵니다. 

4. **IoT Hub 만들기** 를 선택택합니다.

5. 처음으로 Azure에 로그인할 수 있게 하는 팝업 메시지가 오른쪽 아래 모서리에 표시됩니다.

6. Azure 구독을 선택합니다. 

7. 리소스 그룹을 선택합니다.

8. 위치를 선택합니다.

9. 가격 책정 계층을 선택합니다.

10. IoT Hub에 대한 전역 고유 이름을 입력합니다.

11. IoT Hub가 만들어질 때까지 몇 분 동안 기다립니다.

## <a name="next-steps"></a>다음 단계

Visual Studio Code용 Azure IoT Tools를 사용하여 IoT Hub를 개발했습니다. 더 자세히 알아보려면 다음 문서를 확인하세요.

* [Visual Studio Code용 Azure IoT Tools를 사용하여 디바이스와 IoT Hub 간에 메시지 보내고 받기](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Visual Studio Code용 Azure IoT Tools를 사용한 Azure IoT Hub 디바이스 관리](iot-hub-device-management-iot-toolkit.md)

* [VS Code Wiki 페이지용 Azure IoT Hub를 참조하세요](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
