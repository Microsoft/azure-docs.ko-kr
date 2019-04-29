---
title: 명령줄에서 새 디바이스 등록 - Azure IoT Edge | Microsoft Docs
description: Azure CLI용 IoT 확장을 사용하여 새 IoT Edge 디바이스를 등록하고 연결 문자열 검색
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 01/03/2019
ms.date: 01/28/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 45b05498702042c931df3765b9e1bd79489dbb6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595074"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Azure CLI를 사용하여 새 Azure IoT Edge 디바이스 등록

Azure IoT Edge에서 IoT 디바이스를 사용하려면 먼저 IoT Hub에 등록해야 합니다. 디바이스를 등록하면 Edge 워크로드에 대해 디바이스를 설정하는 데 사용할 수 있는 연결 문자열을 받게 됩니다. 

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)는 IoT Edge 같은 Azure 리소스를 관리하기 위한 오픈 소스 교차 플랫폼 명령줄 도구입니다. 이 기능을 사용하면 Azure IoT Hub 리소스, 디바이스 프로비전 서비스 인스턴스 및 연결된 허브를 즉시 관리할 수 있습니다. 새로운 IoT 확장은 디바이스 관리 및 전체 IoT Edge 같은 기능으로 Azure CLI를 강화합니다.

이 문서에서는 Azure CLI를 사용하여 새 IoT Edge 디바이스를 등록하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) 
* 사용자 환경의 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Azure CLI 버전이 2.0.24 이상이어야 합니다. `az –-version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다. 
* [Azure CLI용 IoT 확장](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>디바이스 만들기

다음 명령을 사용하여 IoT Hub에서 새 디바이스 ID를 만듭니다. 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

이 명령에는 세 개의 매개 변수가 포함됩니다.
* **device-id**: IoT Hub에 고유한 설명 이름을 제공합니다.
* **hub-name**: IoT Hub의 이름을 제공합니다.
* **edge-enabled**: 이 매개 변수는 디바이스를 IoT Edge에서 사용한다고 선언합니다.

   ![az iot hub device-identity create output](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>모든 디바이스 보기

다음 명령을 사용하여 IoT Hub에서 모든 디바이스를 봅니다.

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge 디바이스로 등록된 모든 디바이스에서는 **capabilities.iotEdge** 속성이 **true**로 설정됩니다. 

## <a name="retrieve-the-connection-string"></a>연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다. 다음 명령을 실행하여 단일 디바이스에 대한 연결 문자열을 반환합니다.

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

`device-id` 매개 변수의 값은 대소문자를 구분합니다. 연결 문자열 앞뒤의 따옴표를 복사하지 마세요.

## <a name="next-steps"></a>다음 단계

[Azure CLI를 사용하여 디바이스에 모듈을 배포](how-to-deploy-modules-cli.md)하는 방법을 알아봅니다.
