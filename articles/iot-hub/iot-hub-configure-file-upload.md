---
title: Azure Portal을 사용하여 파일 업로드 구성 | Microsoft Docs
description: Azure Portal을 사용하여 연결된 디바이스에서 파일 로드를 사용하도록 IoT Hub를 구성하는 방법입니다. 대상 Azure Storage 계정 구성에 대한 정보가 포함됩니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: bd7cc37b8fc81fc9d4109826743f2243913d0604
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735058"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Azure Portal을 사용하여 IoT Hub 파일 업로드 구성

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>파일 업로드

[IoT Hub에서 파일 업로드 기능](iot-hub-devguide-file-upload.md)을 사용하려면 먼저 Azure Storage 계정을 허브에 연결해야 합니다. **파일 업로드**를 선택하여 수정하려는 IoT hub에 대한 파일 업로드 속성의 목록을 표시합니다.

![포털에서 IoT Hub 파일 보기 설정 보기](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **스토리지 컨테이너**: Azure portal를 사용 하 여 IoT Hub에 연결 하려면 현재 Azure 구독에 Azure Storage 계정에서 blob 컨테이너를 선택 합니다. 필요에 따라 **Storage 계정** 블레이드에 Azure Storage 계정을 만들고 **컨테이너** 블레이드에 Blob 컨테이너를 만들 수 있습니다. IoT Hub는 파일을 업로드하는 경우에 사용할 디바이스에 대한 이 Blob 컨테이너에 쓰기 권한이 있는 SAS URI를 자동으로 생성합니다.

   ![포털에서 파일 업로드에 대한 저장소 컨테이너 보기](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **업로드된 파일에 대한 알림 받기**: 토글을 통해 파일 업로드 알림을 사용 하지 않도록 설정 하거나 사용 합니다.

* **SAS TTL**: 이 설정은 IoT Hub에서 디바이스로 반환하는 SAS URI의 TTL(Time-to-Live)입니다. 기본적으로 1시간으로 설정되지만 슬라이더를 사용하여 다른 값으로 사용자 지정할 수 있습니다.

* **파일 알림 설정 기본 TTL**: 만료되기 전의 파일 업로드 알림 TTL입니다. 기본적으로 1시간으로 설정되지만 슬라이더를 사용하여 다른 값으로 사용자 지정할 수 있습니다.

* **파일 알림 최대 배달 횟수**: IoT Hub가 파일 업로드 알림 배달을 시도하는 횟수입니다. 기본적으로 10으로 설정되지만 슬라이더를 사용하여 다른 값으로 사용자 지정할 수 있습니다.

   ![포털에서 IoT Hub 파일 업로드 구성](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>다음 단계

IoT Hub의 파일 업로드 기능에 대한 자세한 내용은 IoT Hub 개발자 가이드의 [디바이스에서 파일 업로드](iot-hub-devguide-file-upload.md)를 참조하세요.

Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [IoT 디바이스 대량 관리](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub 메트릭](iot-hub-metrics.md)
* [작업 모니터링](iot-hub-operations-monitoring.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)
* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)
* [처음부터 IoT 솔루션 보안 유지](../iot-fundamentals/iot-security-ground-up.md)
