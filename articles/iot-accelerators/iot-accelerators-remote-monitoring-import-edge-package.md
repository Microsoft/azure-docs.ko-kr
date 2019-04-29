---
title: 원격 모니터링 솔루션에서 Edge 패키지 가져오기 - Azure | Microsoft Docs
description: 이 문서에서는 원격 모니터링 솔루션 가속기로 IoT Edge 패키지를 가져오는 방법을 설명합니다.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/10/2018
ms.date: 12/17/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61443014"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>원격 모니터링 솔루션 가속기로 IoT Edge 패키지 가져오기

배포 매니페스트는 IoT Edge 디바이스에 배포할 모듈을 정의합니다. 이 문서에서는 조직의 개발자가 배포 매니페스트를 이미 만들었다고 가정합니다. 개발자가 매니페스트를 만드는 방법에 대한 자세한 내용은 다음 IoT Edge 방법 문서 중 하나를 참조하세요.

- [Azure Portal에서 Azure IoT Edge 모듈 배포](../iot-edge/how-to-deploy-modules-portal.md)
- [Azure CLI를 사용하여 Azure IoT Edge 모듈 배포](../iot-edge/how-to-deploy-modules-cli.md)
- [Visual Studio Code에서 Azure IoT Edge 모듈 배포](../iot-edge/how-to-deploy-modules-vscode.md)

개발자는 개발 환경에서 배포 매니페스트를 만들고 테스트합니다. 준비가 완료된 경우 원격 모니터링 솔루션 가속기로 매니페스트를 가져올 수 있습니다.

## <a name="export-a-manifest"></a>매니페스트 내보내기

Azure Portal을 사용하여 개발 환경에서 배포 매니페스트를 내보냅니다.

1. Azure Portal에서 IoT Edge 디바이스를 개발 및 테스트하는 데 사용할 IoT Hub로 이동합니다. 클릭 **IoT Edge** 차례로 **IoT Edge 배포**: ![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. 사용하려는 배포 구성을 포함하는 배포를 클릭합니다. 합니다 **배포 세부 정보** 페이지가 표시 됩니다. ![IoT Edge 배포 세부 정보](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. 클릭 **IoT Edge 다운로드 매니페스트**:  ![배포 매니페스트를 다운로드 합니다.](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. JSON 파일을 **deploymentmanifest.json**이라는 로컬 파일로 저장합니다.

이제 배포 매니페스트를 포함하는 파일이 생성됩니다. 다음 섹션에서는 원격 모니터링 솔루션에서 이 매니페스트를 패키지로 가져옵니다.

## <a name="import-a-package"></a>패키지 가져오기

아래 단계에 따라 Edge 배포 매니페스트를 솔루션에 패키지로 가져옵니다.

1. 원격 모니터링 웹 UI에서 **패키지** 페이지로 이동합니다.  ![패키지 페이지](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. 클릭 **+ 새 패키지**, 선택 **Edge 매니페스트** 패키지 형식과 클릭 **찾아보기** 선택 하는 **deploymentmanifest.json** 파일 이전 섹션에서 저장 합니다.  ![매니페스트 선택](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. **업로드**를 클릭하여 원격 모니터링 솔루션에 패키지를 추가합니다.  ![업로드 된 패키지](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

이제 IoT Edge 배포 매니페스트를 패키지로 업로드했습니다. **배포** 페이지에서 이 패키지를 연결된 IoT Edge 디바이스로 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

지금까지 원격 모니터링 솔루션에서 IoT Edge 디바이스로 모듈을 배포하는 방법을 배웠으므로 다음에는 [IoT Edge](../iot-edge/about-iot-edge.md)에 대해 알아봅니다.
