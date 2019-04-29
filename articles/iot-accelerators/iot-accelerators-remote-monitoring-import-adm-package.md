---
title: 원격 모니터링 솔루션 가져오기 패키지-Azure | Microsoft Docs
description: 이 문서에서는 원격 모니터링 솔루션 가속기로 자동 디바이스 관리 패키지를 가져오는 방법을 설명합니다.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61443448"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>원격 모니터링 솔루션 가속기로 자동 디바이스 관리 패키지 가져오기

자동 디바이스 관리 구성은 디바이스 그룹에 배포하도록 구성 변경을 정의합니다. 이 문서에서는 조직의 개발자가 자동 디바이스 관리 구성을 이미 만들었다고 가정합니다. 개발자가 구성을 만드는 방법에 대한 자세한 내용은 다음 IoT Hub 방법 문서 중 하나를 참조하세요.

- [Azure Portal을 사용하여 대규모로 IoT 디바이스 구성 및 모니터링](../iot-hub/iot-hub-auto-device-config.md)
- [Azure CLI를 사용하여 대규모로 IoT 디바이스 구성 및 모니터링](../iot-hub/iot-hub-auto-device-config-cli.md)

개발자는 개발 환경에서 자동 디바이스 관리 구성을 만들고 테스트합니다. 준비가 완료되면 원격 모니터링 솔루션 가속기로 구성을 가져올 수 있습니다.

## <a name="export-a-configuration"></a>구성 내보내기

Azure Portal을 사용하여 개발 환경에서 자동 디바이스 관리 구성을 내보낼 수 있습니다.

1. Azure Portal에서 IoT 디바이스를 개발 및 테스트하는 데 사용할 IoT Hub로 이동합니다. **IoT 디바이스 구성**을 클릭합니다.

    [![IoT 디바이스 구성](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. 사용하려는 구성을 클릭합니다. **디바이스 구성 세부 정보** 페이지가 표시됩니다.

    [![IoT 디바이스 구성 세부 정보](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. **구성 파일 다운로드**를 클릭합니다.

    [![구성 파일 다운로드](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. JSON 파일을 **configuration.json**이라는 로컬 파일로 저장합니다.

자동 디바이스 관리 구성을 포함하는 파일이 하나 생겼습니다. 다음 섹션에서는 이 구성을 원격 모니터링 솔루션에 패키지로 가져옵니다.

## <a name="import-a-package"></a>패키지 가져오기

아래 단계에 따라 자동 디바이스 관리 구성을 솔루션에 패키지로 가져옵니다.

1. 원격 모니터링 웹 UI에서 **패키지** 페이지로 이동합니다.  ![패키지 페이지](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. **+ 새 패키지**를 클릭하고, 패키지 유형으로 **구성**을 선택하고, **찾아보기**를 클릭하여 이전 섹션에서 저장한 **configuration.json** 파일을 선택합니다.

    ![구성 선택](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. **업로드**를 클릭하여 원격 모니터링 솔루션에 패키지를 추가합니다.

    ![업로드한 패키지](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

자동 디바이스 관리 구성을 패키지로 업로드했습니다. **배포** 페이지에서 이 패키지를 연결된 디바이스에 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

구성 패키지를 만들고 원격 모니터링 솔루션으로 가져오는 방법을 알아보았으니, 그 다음 단계로 [원격 모니터링에 연결된 디바이스를 대량으로 관리](iot-accelerators-remote-monitoring-bulk-configuration-update.md)하는 방법을 알아보세요.
