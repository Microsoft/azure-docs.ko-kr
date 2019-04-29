---
title: 연결된 팩터리 대시보드 사용 - Azure | Microsoft Docs
description: 연결된 팩터리 대시보드의 기능을 사용하는 방법을 알아봅니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 05befc72f512f502456f798f25b6c72571451363
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450985"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>연결된 팩터리 솔루션 가속기 대시보드의 기능 사용

[산업용 IoT 디바이스를 관리하는 클라우드 기반 솔루션 배포](quickstart-connected-factory-deploy.md) 빠른 시작에서는 대시보드를 탐색하고 경보에 응답하는 방법을 보여 주었습니다. 이 방법 가이드에서는 산업용 IoT 디바이스를 모니터링하고 관리하는 데 사용할 수 있는 몇 가지 추가 대시보드 기능을 보여 줍니다.

## <a name="apply-filters"></a>필터 적용

**팩터리 위치** 패널 또는 **경보** 패널에서 대시보드에 표시되는 정보를 필터링할 수 있습니다.

1. **깔때기** 아이콘을 클릭하면 팩터리 위치 패널 또는 알람 패널에서 사용할 수 있는 필터 목록이 표시됩니다.

1. 필터 패널이 표시됩니다.

    [![연결된 팩터리 솔루션 가속기 - 필터](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. 필요한 필터를 선택하고 **적용**을 클릭합니다. 필터 필드에 자유 텍스트를 입력할 수도 있습니다.

1. 그러면 필터가 적용됩니다. 추가 깔때기형 아이콘은 필터가 적용되었음을 나타냅니다.

    [![연결된 팩터리 솔루션 가속기 - 필터 적용](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > 활성 필터는 표시된 OEE 및 KPI 값에는 영향을 주지 않고 목록 내용만 필터링합니다.

1. 필터를 지우려면 깔때기형 아이콘을 클릭하고 필터 패널에서 **지우기**를 클릭합니다.

## <a name="browse-an-opc-ua-server"></a>OPC UA 서버 찾아보기

솔루션 가속기를 배포하면 대시보드에서 찾아볼 수 있는 일단의 시뮬레이션된 OPC UA 서버가 자동으로 프로비전됩니다. 시뮬레이션된 서버를 사용하면 실제 서버를 배포하지 않고도 솔루션 가속기를 쉽게 실험할 수 있습니다. 솔루션에 실제 OPC UA 서버를 연결하려면 [연결된 팩터리 솔루션 가속기에 OPC UA 디바이스 연결](iot-accelerators-connected-factory-gateway-deployment.md) 자습서를 참조하세요.

1. 대시보드 탐색 모음에서 **브라우저 아이콘**을 클릭합니다.

    [![연결된 팩터리 솔루션 가속기 - 서버 브라우저](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. 솔루션 가속기에 배포된 서버를 보여 주는 목록에서 한 서버를 선택합니다.

    [![연결된 팩터리 솔루션 가속기 - 서버 목록](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. **연결**을 클릭하면 보안 대화 상자가 표시됩니다. 시뮬레이션의 경우 **계속**을 클릭하는 것이 안전합니다.

1. 서버 트리에서 아무 노드를 클릭하여 확장합니다. 원격 분석을 게시하는 노드 옆에는 확인 표시가 있습니다.

    [![연결된 팩터리 솔루션 가속기 - 서버 트리](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. 항목을 마우스 오른쪽 단추로 클릭하면 해당 노드를 읽고, 쓰고, 게시하고, 호출할 수 있습니다. 사용자에게 제공되는 작업은 사용자의 권한과 노드 특성에 따라 달라집니다. 읽기 옵션은 특정 노드의 값을 보여 주는 상황에 맞는 패널을 표시합니다. 쓰기 옵션은 새 값을 입력할 수 있는 상황에 맞는 패널을 표시합니다. 호출 옵션은 호출의 매개 변수를 입력할 수 있는 노드를 표시합니다.

## <a name="publish-a-node"></a>노드 게시

*시뮬레이션된 OPC UA 서버*를 검색할 때 새 노드를 게시할 수도 있습니다. 솔루션에서 이러한 노드의 원격 분석 데이터를 분석할 수 있습니다. 이러한 *시뮬레이션된 OPC UA 서버*를 사용하면 실제 디바이스를 배포하지 않고도 솔루션 가속기를 쉽게 실험할 수 있습니다.

1. OPC UA 서버 브라우저 트리에서 게시할 노드로 이동합니다.

1. 노드를 마우스 오른쪽 단추로 클릭합니다. **게시**를 클릭합니다.

    [![연결된 팩터리 솔루션 가속기 - 게시 노드](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. 게시가 성공했음을 알리는 상황에 맞는 패널이 표시됩니다. 옆에 확인 표시가 있는 노드가 스테이션 수준 보기에 표시됩니다.

    [![연결된 팩터리 솔루션 가속기 - 게시 성공](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>명령 및 컨트롤

연결된 팩터리를 사용하면 클라우드에서 직접 산업용 디바이스에 명령을 내리고 제어할 수 있습니다. 이 기능을 사용하여 디바이스에서 생성한 알람에 대응할 수 있습니다. 예를 들어 압력 방출 밸브를 여는 명령을 디바이스에 보낼 수 있습니다. OPC UA 서버 브라우저 트리의 **StationCommands** 노드에서 사용 가능한 명령을 찾을 수 있습니다. 이 시나리오에서는 뮌헨에 위치한 생산 라인의 어셈블리 스테이션 압력 방출 밸브를 엽니다. 명령 및 제어 기능을 사용하려면 솔루션 가속기 배포에 대한 **관리자** 역할에 속해야 합니다.

1. 뮌헨, 생산 라인 0, 어셈블리 스테이션에 대한 OPC UA 서버 브라우저 트리에서 **StationCommands** 노드로 이동합니다.

1. 사용할 명령을 선택합니다. **OpenPressureReleaseValve** 노드를 마우스 오른쪽 단추로 누릅니다. **호출**을 클릭합니다.

    [![연결된 팩터리 솔루션 가속기 - 호출 명령](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. 호출하려는 메서드 및 모든 매개 변수 세부 정보를 알려주는 상황에 맞는 패널이 표시됩니다. **호출**을 클릭합니다.

    [![연결된 팩터리 솔루션 가속기 - 호출 매개 변수](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. 메서드 호출이 성공했음을 알리기 위해 상황에 맞는 패널이 업데이트됩니다. 호출의 결과로 업데이트된 압력 노드의 값을 읽어 보면 호출이 성공했음을 확인할 수 있습니다.

    [![연결된 팩터리 솔루션 가속기 - 호출 성공](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>배후 상황

솔루션 가속기를 배포할 때, 배포 프로세스는 사용자가 선택한 Azure 구독에 여러 리소스를 만듭니다. [Azure Portal](https://portal.azure.com)에서 이러한 리소스를 볼 수 있습니다. 배포 프로세스는 솔루션 가속기에 대해 선택한 이름을 기반으로 하는 이름으로 **리소스 그룹**을 만듭니다.

[![연결된 팩터리 솔루션 가속기 - 리소스 그룹](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

리소스 그룹의 리소스 목록에서 각 리소스를 선택하여 설정을 볼 수 있습니다.

[azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory) GitHub 리포지토리에서 솔루션 가속기에 대한 소스 코드를 볼 수도 있습니다.

완료되면 [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) 사이트의 Azure 구독에서 솔루션 가속기를 삭제할 수 있습니다. 이 사이트를 사용하면 솔루션 가속기를 만들 때 프로비전된 모든 리소스를 쉽게 삭제할 수 있습니다.

> [!NOTE]
> 솔루션 가속기와 관련된 모든 항목을 삭제하려면 [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) 사이트에서 해당 가속기를 삭제합니다. 포털에서 리소스 그룹을 삭제하지 마세요.

## <a name="next-steps"></a>다음 단계

작동하는 솔루션 가속기를 배포했으므로 다음 문서를 참고하여 IoT 솔루션 가속기를 계속 시작할 수 있습니다.

* [연결된 팩터리 솔루션 가속기 연습](iot-accelerators-connected-factory-sample-walkthrough.md)
* [연결된 팩터리 솔루션 가속기에 디바이스 연결](iot-accelerators-connected-factory-gateway-deployment.md)
* [azureiotsolutions.com 사이트에 대한 사용 권한](iot-accelerators-permissions.md)
