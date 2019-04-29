---
title: Azure Portal에서 새 디바이스 등록 - Azure IoT Edge | Microsoft Docs
description: Azure Portal을 사용하여 새 IoT Edge 디바이스를 등록하고 연결 문자열 검색
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6414f694296ce1f5a8b65ccab30cceaf2172dee7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126324"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Azure Portal에서 새 Azure IoT Edge 디바이스 등록

Azure IoT Edge에서 IoT 디바이스를 사용하려면 먼저 IoT Hub에 등록해야 합니다. 디바이스를 등록하면 Edge 워크로드에 대해 디바이스를 설정하는 데 사용할 수 있는 연결 문자열을 받게 됩니다.

이 문서에서는 Azure Portal을 사용하여 새 IoT Edge 디바이스를 등록하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)

## <a name="create-a-device"></a>디바이스 만들기

Azure Portal에서 IoT Edge 디바이스는 IoT Hub에 연결되지만 에지를 사용할 수 없는 디바이스와 별도로 만들어지고 관리됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
2. 메뉴에서 **IoT Edge**를 선택합니다.
3. **IoT Edge 디바이스 추가**를 선택합니다.
4. 설명이 포함된 디바이스 ID를 제공합니다. 인증 키 자동 생성 및 허브에 새 디바이스 연결에는 기본 설정을 사용합니다.
5. **저장**을 선택합니다.

## <a name="view-all-devices"></a>모든 디바이스 보기

IoT Hub에 연결된 에지를 사용할 수 있는 모든 디바이스는 **IoT Edge** 페이지에 나열되어 있습니다.

## <a name="retrieve-the-connection-string"></a>연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

1. 포털에서 **IoT Edge** 페이지의 Edge 디바이스 목록에서 디바이스 ID를 클릭합니다.
2. **연결 문자열(기본 키)** 또는 **연결 문자열(보조 키)** 중 하나의 값을 복사합니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal을 사용하여 디바이스에 모듈을 배포](how-to-deploy-modules-portal.md)하는 방법을 알아봅니다.
