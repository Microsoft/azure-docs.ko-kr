---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 1e919f22d5dd7975f055f262ec9ba69230aebd17
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780845"
---
이 섹션에서는 Azure CLI를 사용 하 여이 문서에 대한 장치 id를 만듭니다. 디바이스 ID는 대/소문자를 구분합니다.

1. [Azure Cloud Shell](https://shell.azure.com/)을 엽니다.

1. Azure Cloud Shell에서 다음 명령을 실행 하 Azure CLI에 대한 Microsoft Azure IoT 확장을 설치 합니다.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

2. 이라는 `myDeviceId` 새 장치 id를 만들고 다음 명령을 사용 하 여 장치 연결 문자열을 검색 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

결과에서 디바이스 연결 문자열을 기록해 둡니다. 이 디바이스 연결 문자열은 디바이스 앱이 디바이스로 IoT Hub에 연결하는 데 사용됩니다.

<!-- images and links -->
