---
title: Azure IoT Central에 대 한 장치 연결 문자열을 생성 합니다. | Microsoft Docs
description: 장치 개발자를 생성 하려면 어떻게 IoT Central 응용 프로그램에 연결 해야 하는 장치에 대 한 연결 문자열을?
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f302cbfa7152ae30be434f560c0c39056d40f9f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60885649"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램에 연결 하는 장치 연결 문자열 생성

이 문서에서는 설명 장치 개발자 IoT Central 응용 프로그램에 연결 해야 하는 장치에 대 한 연결 문자열을 생성 하는 방법입니다. 이 문서에 설명 된 절차 신속 하 게 공유 액세스 서명 (SAS)을 사용 하 여 단일 장치를 연결 하는 방법을 보여 줍니다. 이 방법은 IoT Central을 사용 하 여 실험 하거나 장치를 테스트 하는 경우에 유용 합니다. 프로덕션 환경에서 사용 하는 대안에 대해서 [Azure IoT Central의 장치 연결](concepts-connectivity.md)합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

- Azure IoT Central 애플리케이션. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.
- 개발 시스템과 [Node.js](https://nodejs.org/) 버전 8.0.0 이상을 설치 합니다. 명령줄에서 `node --version` 명령을 실행하여 버전을 확인할 수 있습니다. Node.js는 다양한 운영 체제에 사용할 수 있습니다.

## <a name="get-connection-information"></a>연결 정보 가져오기

다음 단계는 장치에 대 한 SAS 연결 문자열을 생성 하는 데 필요한 정보를 가져오는 방법을 설명 합니다.

1. 에 **Device Explorer**, 응용 프로그램에 연결 하려는 실제 장치 찾기:

    ![실제 장치를 선택 합니다.](media/howto-generate-connection-string/real-devices.png)

1. 에 **장치** 페이지에서 **Connect**:

    ![연결 선택](media/howto-generate-connection-string/connect.png)

1. 연결 세부 정보를 기록해 **범위 ID**를 **장치 ID**, 및 **장치 키**에서 다음 단계를 사용 하려면:

    ![연결 정보](media/howto-generate-connection-string/device-connect.png)

    저장 하려면이 페이지에서 값을 복사할 수 있습니다.

## <a name="generate-the-connection-string"></a>연결 문자열 생성

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에 연결 하는 실제 장치에 대 한 연결 문자열을 생성 한 했으므로 다음 제안된 단계는 다음과 같습니다.

* [DevKit 디바이스 준비 및 연결(C)](howto-connect-devkit.md)
* [Raspberry Pi 준비 및 연결(Python)](howto-connect-raspberry-pi-python.md)
* [Raspberry Pi 준비 및 연결(C#)](howto-connect-raspberry-pi-csharp.md)
* [Windows 10 IoT Core 디바이스 준비 및 연결(C#)](howto-connect-windowsiotcore.md)
* [Azure IoT Central 애플리케이션에 일반 Node.js 클라이언트 연결](howto-connect-nodejs.md)