---
title: 장치에서 Azure storage로 파일 업로드 | Microsoft Docs
description: 장치에서 클라우드로 파일 업로드를 구성 하는 방법입니다. 파일 업로드를 구성한 후에는 장치에서 파일 업로드를 구현 합니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 6b717fd15b25ae4abd2af3520dba2e72f8f9f3a4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556374"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>장치에서 클라우드로 파일 업로드

*이 항목은 관리자 및 장치 개발자에 게 적용 됩니다.*

IoT Central를 사용 하 여 연결 된 장치에서 클라우드 저장소로 미디어 및 기타 파일을 업로드할 수 있습니다. IoT Central 응용 프로그램에서 파일 업로드 기능을 구성 하 고 장치 코드에서 파일 업로드를 구현 합니다.

## <a name="prerequisites"></a>전제 조건

파일 업로드를 구성 하려면 IoT Central 응용 프로그램의 관리자 여야 합니다.

업로드 된 파일을 저장할 Azure 저장소 계정 및 컨테이너가 필요 합니다. 사용할 기존 저장소 계정 및 컨테이너가 없는 경우 [Azure Portal에 새 저장소 계정을](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)만듭니다.

## <a name="configure-device-file-uploads"></a>장치 파일 업로드 구성

장치 파일 업로드를 구성 하려면:

1. 응용 프로그램의 **관리** 섹션으로 이동 합니다.

1. **장치 파일 업로드**를 선택 합니다.

1. 사용할 저장소 계정 및 컨테이너를 선택 합니다. 저장소 계정이 응용 프로그램에서 다른 Azure 구독에 있는 경우 저장소 계정 연결 문자열을 입력 합니다.

1. 필요한 경우 업로드 요청이에 대해 유효한 상태로 유지 되는 기간을 설정 하는 업로드 제한 시간을 조정 합니다. 유효한 값은 1에서 24 시간 까지입니다.

1. **저장**을 선택합니다. 상태가 **구성 됨**으로 표시 되 면 장치에서 파일을 업로드할 준비가 된 것입니다.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="응용 프로그램에서 파일 업로드 구성":::

## <a name="disable-device-file-uploads"></a>장치 파일 업로드 사용 안 함

IoT Central 응용 프로그램에 대 한 장치 파일 업로드를 사용 하지 않도록 설정 하려면 다음을 수행 합니다.

1. 응용 프로그램의 **관리** 섹션으로 이동 합니다.

1. **장치 파일 업로드**를 선택 합니다.

1. **삭제**를 선택합니다.

## <a name="upload-a-file-from-a-device"></a>장치에서 파일 업로드

IoT Central IoT Hub의 파일 업로드 기능을 사용 하 여 장치에서 파일을 업로드할 수 있도록 합니다. 장치에서 파일을 업로드 하는 방법을 보여 주는 샘플 코드는 [IoT Central 파일 업로드 장치 샘플](https://docs.microsoft.com/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 IoT Central에서 장치 파일 업로드를 구성 하 고 구현 하는 방법을 배웠으므로 제안 된 다음 단계는 장치 파일 업로드에 대 한 자세한 정보를 표시 하는 것입니다.

- [IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드(.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드(Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드(Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [IoT Hub (Python)를 사용 하 여 장치에서 클라우드로 파일 업로드](../../iot-hub/iot-hub-python-python-file-upload.md)
