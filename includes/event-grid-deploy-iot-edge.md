---
title: 포함 파일
description: 포함 파일
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844594"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge 모듈 배포

IoT Edge 장치에 모듈을 배포 하는 방법에는 여러 가지가 있으며 모든 장치는 IoT Edge에서 Azure Event Grid에 대해 작동 합니다. 이 문서에서는 Azure Portal에서 IoT Edge에 Event Grid를 배포 하는 단계를 설명 합니다.

>[!NOTE]
> 이 자습서에서는 지 속성 없이 Event Grid 모듈을 배포 합니다. 이는 모듈을 다시 배포할 때이 자습서에서 만든 토픽 및 구독이 삭제 됨을 의미 합니다. 지 속성을 설정 하는 방법에 대한 자세한 내용은 [Linux에서 상태 유지](../articles/event-grid/edge/persist-state-linux.md) 또는 [Windows에서 지속 상태](../articles/event-grid/edge/persist-state-windows.md)문서를 참조 하세요. 프로덕션 워크 로드의 경우 지 속성을 사용 하 여 Event Grid 모듈을 설치 하는 것이 좋습니다.

>[!IMPORTANT]
> 이 자습서에서는 클라이언트 인증 기능이 해제 된 상태에서 Event Grid 모듈이 배포 되며 HTTP 구독자가 허용 됩니다. 프로덕션 워크 로드의 경우 클라이언트 인증을 사용 하는 HTTPS 요청 및 구독자만 사용 하도록 설정 하는 것이 좋습니다. Event Grid 모듈을 안전 하 게 구성 하는 방법에 대한 자세한 내용은 [보안 및 인증](../articles/event-grid/edge/security-authentication.md)을 참조 하세요.
 
### <a name="select-your-iot-edge-device"></a>IoT Edge 장치 선택

1. [Azure 포털](https://portal.azure.com)
1. IoT Hub로 이동합니다.
1. **자동 장치 관리** 섹션의 메뉴에서 **IoT Edge** 를 선택 합니다. 
1. 장치 목록에서 대상 장치의 ID를 클릭 합니다.
1. **모듈 설정**을 선택합니다. 페이지를 열어 둡니다. 다음 섹션의 단계를 계속 진행 합니다.

### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure Portal에는 JSON 문서를 수동으로 빌드하는 대신 배포 매니페스트를 만드는 과정을 안내 하는 마법사가 있습니다.  **모듈 추가**, **경로 지정** 및 **배포 검토** 등 세 가지 단계가 있습니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 선택 합니다.
1. 드롭다운 목록의 모듈 형식에서 **IoT Edge 모듈** 을 선택 합니다.
1. 컨테이너의 이름, 이미지, 컨테이너 만들기 옵션을 제공 합니다.

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **이름**: eventgridmodule
   * **이미지 URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **컨테이너 만들기 옵션**:

    ```json
        {
          "Env": [
            "inbound__clientAuth:clientCert__enabled=false",
            "outbound__webhook__httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                  "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```

 1. 페이지 맨 아래에 있는 **저장**
 1. **다음** 을 클릭 하 여 경로 섹션으로 이동 합니다.

    > [!NOTE]
    > Azure VM을에 지 장치로 사용 하는 경우 포트 4438에서 인바운드 트래픽을 허용 하는 인바운드 포트 규칙을 추가 합니다. 규칙을 추가 하는 방법에 대한 지침은 [VM에 포트를 여는 방법](../articles/virtual-machines/windows/nsg-quickstart-portal.md)을 참조 하세요.


### <a name="setup-routes"></a>설정 경로

 기본 경로를 유지 하 고 **다음** 을 선택 하 여 검토 섹션으로 이동 합니다.

### <a name="review-deployment"></a>배포 검토

1. 검토 섹션에서는 이전 두 개의 섹션에서 선택한 항목에 따라 생성된 JSON 배포 매니페스트를 보여줍니다. **$EdgeAgent** 및 **$edgeHub**목록에 두 개의 모듈이 있는지 확인 합니다. 이러한 두 모듈은 IoT Edge 런타임을 구성 하며 모든 배포에 필요한 기본값입니다.
1. 배포 정보를 검토한 다음 **제출**을 선택합니다.

### <a name="verify-your-deployment"></a>배포 확인

1. 배포를 제출한 후에는 IoT hub의 IoT Edge 페이지로 돌아갑니다.
1. 배포의 대상으로 지정 된 **IoT Edge 장치** 를 선택 하 여 세부 정보를 엽니다.
1. 장치 세부 정보에서 Event Grid 모듈이 **배포에 지정** 되 고 **장치에서 보고**되는 것으로 표시 되는지 확인 합니다.

모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.