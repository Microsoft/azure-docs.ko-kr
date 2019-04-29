---
title: Azure IoT Central 애플리케이션에 Windows IoT Core 장치 연결 | Microsoft Docs
description: 장치 개발자로서 Azure IoT Central 애플리케이션에 MXChip IoT DevKit 장치를 연결하는 방법을 알아봅니다.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: af6d66d2e3eae80477a151323578b930dcd7727a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886640"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 Windows IoT Core 장치 연결

이 문서에서는 장치 개발자로서 Windows IoT Core 장치를 Microsoft Azure IoT Central 애플리케이션에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음이 필요합니다.

- **샘플 Devkits** 애플리케이션 템플릿으로 만든 Azure IoT Central 애플리케이션. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.

- Windows 10 IoT Core 운영 체제를 실행하는 디바이스. 자세한 내용은 [Windows 10 IoT Core 장치 설정](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup)합니다.

- 개발 시스템과 [Node.js](https://nodejs.org/) 버전 8.0.0 이상을 설치 합니다. 명령줄에서 `node --version` 명령을 실행하여 버전을 확인할 수 있습니다. Node.js는 다양한 운영 체제에 사용할 수 있습니다.

## <a name="the-sample-devkits-application"></a>샘플 Devkits 응용 프로그램

**샘플 Devkits** 애플리케이션 템플릿으로 만든 애플리케이션에는 다음과 같은 특징을 가진 **Windows IoT Core** 장치가 포함됩니다.

- 장치에 대 한 원격 분석 측정값: **습도**하십시오 **온도**, 및 **압력**합니다.
- 컨트롤을 설정 **팬 속도**합니다.
- 장치 속성을 **숫자 주사위** 클라우드 속성과 **위치**합니다.

장치 템플릿 구성에 대 한 전체 내용은 참조 하세요. [Windows IoT Core 장치 템플릿 세부 정보](#device-template-details)합니다.

## <a name="add-a-real-device"></a>실제 디바이스 추가

Azure IoT Central 응용 프로그램에서 사용 하 여는 **Device Explorer** 페이지에서 실제 장치를 추가 하는 **Windows 10 IoT Core** 장치 템플릿. 기록해 장치 연결 세부 정보 (**범위 ID**를 **장치 ID**, 및 **기본 키**). 자세한 내용은 [연결 정보를 가져올](howto-generate-connection-string.md#get-connection-information)합니다.

## <a name="prepare-the-device"></a>장치 준비

IoT Central에 연결할 장치에 대 한 연결 문자열을 해야 합니다.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

연결 문자열에 액세스 하려면 장치 코드의 경우 라는 파일에 저장 **connection.string.iothub** 폴더에 `C:\Data\Users\DefaultAccount\Documents\` Windows 10 IoT Core 장치에 있습니다.

복사 하는 **connection.string.iothub** 데스크톱 컴퓨터에서 파일을 `C:\Data\Users\DefaultAccount\Documents\` 장치에서 폴더를 사용할 수는 [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. 웹 브라우저를 사용 하 여 장치에서 Windows Device Portal 이동 합니다.
1. 선택한 장치에서 파일을 찾아보려면 **Apps > 파일 탐색기**합니다.
1. 이동할 **사용자 Folders\Documents**합니다. 그런 다음 업로드 합니다 **connection.string.iothub** 파일:

    ![연결 문자열을 업로드 합니다.](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>배포 및 실행

를 배포 및 장치에서 샘플 응용 프로그램을 실행 하려면 사용 합니다 [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. 웹 브라우저를 사용 하 여 장치에서 Windows Device Portal 이동 합니다.
1. 배포 및 실행 하는 **Azure IoT Hub 클라이언트** 응용 프로그램 선택 **앱 > 빠른 실행 샘플**합니다. 선택한 **Azure IoT Hub 클라이언트**합니다.
1. 선택한 **배포 및 실행**합니다.

    ![배포 및 실행](media/howto-connect-windowsiotcore/quick-run.png)

IoT Central 응용 프로그램에서 몇 분 후 장치에서 원격 분석 데이터를 볼 수 있습니다.

합니다 [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) 장치 문제를 해결 하는 데 사용할 수 있는 도구가 포함 되어 있습니다.

- 합니다 **Apps manager** 페이지에서는 장치에서 실행 되는 앱을 제어할 수 있습니다.
- 장치에 연결 하는 모니터를 설정 하지 않은 경우 사용할 수 있습니다 합니다 **장치 설정** 장치의 스크린 샷을 캡처하는 페이지입니다. 예를 들면 다음과 같습니다.

    ![앱의 스크린 샷](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>소스 코드 다운로드

탐색 하 고 클라이언트 응용 프로그램에 대 한 소스 코드를 수정 하려는 경우에서 다운로드할 수 있습니다 합니다 [Windows iotcore 샘플 GitHub 리포지토리](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients)합니다.

## <a name="device-template-details"></a>장치 템플릿 세부 정보

**샘플 Devkits** 애플리케이션 템플릿으로 만든 애플리케이션에는 다음과 같은 특징을 가진 **Windows IoT Core** 장치가 포함됩니다.

### <a name="telemetry-measurements"></a>원격 분석 측정값

| 필드 이름     | Units  | 최소 | 최대 | 소수 자릿수 |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>설정

숫자 설정

| 표시 이름 | 필드 이름 | Units | 소수 자릿수 | 최소 | 최대 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 팬 속도    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>properties

| Type            | 표시 이름 | 필드 이름 | 데이터 형식 |
| --------------- | ------------ | ---------- | --------- |
| 디바이스 속성 | 다이 번호   | dieNumber  | number    |
| Text            | Location     | location   | N/A       |

## <a name="next-steps"></a>다음 단계

제안 된 다음 단계에 알아보려면는 이제 Azure IoT Central 응용 프로그램을 Raspberry Pi를 연결 하는 방법을 배웠으므로 하는 방법 [템플릿으로 사용자 지정 장치 설정](howto-set-up-template.md) 사용자 고유의 IoT 장치에 대 한 합니다.