---
title: Power BI를 사용하여 원격 모니터링 데이터 시각화 - Azure | Microsoft Docs
description: 이 자습서에서는 Power BI Desktop과 Cosmos DB를 사용하여 원격 모니터링 솔루션의 데이터를 사용자 지정된 시각화로 통합시킵니다. 이렇게 하면 자신만의 사용자 지정 대시보드를 만들어서 솔루션이 아닌 곳에서 사용자에게 공유할 수 있습니다.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 3398c6d318e0e3c51d3f6cfe8af651a6e3f55c9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448134"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Power BI를 사용하여 원격 모니터링 데이터 시각화

이 자습서는 CosmosDB의 원격 모니터링 솔루션 데이터를 Power BI에 연결하는 방법을 안내합니다. 이 연결이 설정되면 자신만의 사용자 지정 대시보드를 만들어서 원격 모니터링 솔루션 대시보드에 다시 추가할 수 있습니다. 이러한 작업 흐름을 통해 기본 제공 그래프뿐만 아니라 보다 전문화된 그래프를 만들 수 있습니다. 그런 다음 이 자습서를 사용하여 다른 데이터 스트림과 통합하거나 사용자 지정 대시보드를 작성하여 원격 모니터링 솔루션 외부에서 사용할 수 있습니다. Power BI에서 대시보드를 작성하고 특정 부분을 선택하면 각 패널이 상호 작용할 수 있습니다. 예를 들어 시뮬레이션된 트럭에 대한 정보만 보여주는 필터가 있으면 대시보드의 각 부분이 상호 작용하여 시뮬레이션된 트럭 정보만 표시됩니다. Power BI 이외의 도구를 사용하려는 경우 이 단계를 확장하여 원하는 시각화 도구를 사용하고 Cosmos 데이터베이스 또는 사용자 정의 데이터베이스(설정해 둔 경우)에 연결할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

- 현재 실행 중인 원격 모니터링 솔루션이 있어야 합니다.
- [Azure Portal](https://portal.azure.com)에 액세스할 수 있어야 하며 IoT Hub 및 솔루션을 실행 중인 구독이 있어야 합니다.
- [Power BI 데스크톱](https://powerbi.microsoft.com)이 설치되어 있어야 하며 버전은 상관 없습니다.


## <a name="information-needed-from-azure-portal"></a>Azure Portal에서 필요한 정보

1. [Azure Portal](https://portal.azure.com)로 이동하고 필요한 경우 로그인합니다.

2. 왼쪽 패널에서 리소스 그룹을 클릭합니다.

    ![사이드 패널 탐색](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Iot 솔루션이 실행 중인 리소스 그룹으로 이동한 후 클릭하여 해당 리소스 그룹의 개요 페이지로 이동합니다. 

4. 개요 페이지에서 "Azure Cosmos DB Account" 형식의 항목을 클릭하면 해당 IoT 솔루션에 대한 Cosmos DB 스트림의 개요 페이지로 이동됩니다.

    ![리소스 그룹](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. 왼쪽 패널에서 "키" 섹션을 클릭하고 Power BI에서 사용할 다음 값을 적어 둡니다.

   - URI
   - 기본 키

     ![키](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Power BI에서 스트림 설정
  
1. Power BI Desktop 앱을 열고 왼쪽 위 모서리에서 "데이터 가져오기"를 클릭합니다. 

    ![데이터 가져오기](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. 데이터를 입력하라는 메시지가 표시되면 "Azure Cosmos DB"를 검색하고 이 커넥터를 선택합니다. 이 커넥터는 기본적으로 Azure IoT 솔루션의 Cosmos 데이터베이스에서 데이터를 바로 끌어옵니다.
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. 앞에서 적어둔 정보를 입력합니다.

    * URI
    * 기본 키

4. Power BI로 가져올 테이블을 모두 선택합니다. 이 작업을 통해 데이터 로딩이 시작됩니다. 솔루션을 실행한 시간이 길수록 데이터를 로드하는 데 걸리는 시간이 깁니다(최대 몇 시간까지). 

    ![테이블 가져오기](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. 데이터 로딩이 완료되면 Power BI의 맨 위 행에서 "쿼리 편집"을 클릭하고 각 테이블의 노란색 막대에 있는 화살표를 클릭하여 테이블을 모두 펼칩니다. 기본적으로 모든 열을 표시하도록 펼쳐집니다. 습도, 속도 시간 등과 같은 데이터가 올바른 형식이 아닌 것을 볼 수 있습니다.

    ![새 열](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    예를 들어, Power BI로 가져온 데이터가 커넥터를 통해 들어올 때 유닉스 시간으로 변경되었습니다. 이러한 변환을 조정하기 위해 앞으로 새로운 열을 만들고 이 방정식을 사용하여 날짜/시간 형식으로 가져올 수 있습니다. 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![업데이트된 테이블](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received는 UNIX 형식의 열 중 하나이며 변환이 필요한 다른 항목으로 대체될 수 있습니다. 
  
    다른 데이터 요소는 문자열 형식으로 변환되었으며 문자열은 위와 동일한 단계를 사용하여 적절한 경우 Doubles 또는 Int로 변경할 수 있습니다.

## <a name="creating-a-dashboard"></a>대시보드 만들기

스트림이 연결되고 나면 개인 설정 대시보드를 만들 준비가 됩니다. 아래의 대시보드는 시뮬레이션된 디바이스에서 내보낸 원격 분석 데이터를 가져와서 다음과 같이 다양한 피벗을 보여 주는 예입니다. 

* 지도에 디바이스 위치 표시(오른쪽)
* 디바이스와 함께 상태 및 심각도 표시 (왼쪽 위)
* 규칙이 적용되는 디바이스 및 해당 디바이스에 대한 경고 발생 여부(왼쪽 아래)

![PowerBI 시각화](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>대시보드 게시 및 데이터 새로 고침

대시보드 만들기를 완료한 후에는 [Power BI 대시보드를 게시](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files)하여 다른 사람들과 공유하는 것이 좋습니다.

또한 최신 데이터 집합이 표시되도록 게시된 대시보드의 [데이터를 새로 고치는 것이](https://docs.microsoft.com/power-bi/refresh-data) 좋습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Power BI를 사용하여 원격 모니터링 데이터를 시각화하는 방법을 알아보았습니다.

원격 모니터링 솔루션 사용자 지정에 대한 자세한 내용은 다음을 참조하세요.

* [원격 모니터링 솔루션 UI 사용자 지정](iot-accelerators-remote-monitoring-customize.md)
* [개발자 참조 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [개발자 문제 해결 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

