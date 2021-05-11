---
title: Azure Portal을 사용하여 Resource Health 경고 만들기
description: Azure Portal를 사용해 Azure 리소스를 사용할 수 없게 되면 알리는 경고를 만듭니다.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: e48c400e5be3516b08496db7a4cb6a19e45d6c97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594631"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Azure Portal을 사용하여 리소스 상태 경고 구성

이 문서에서는 Azure Portal을 사용하여 리소스 상태 알림에 대한 활동 로그 경고를 설정하는 방법을 보여 줍니다.

Azure Resource Health는 Azure 리소스의 현재 및 과거 상태에 대한 정보를 알려줍니다. Azure Resource Health 경고는 이러한 리소스의 상태가 변경되면 거의 실시간으로 알려줍니다. Resource Health 경고를 프로그래밍 방식으로 만들면 사용자가 경고를 대량으로 생성하고 사용자 지정할 수 있습니다.

리소스 상태 알림은 [Azure 활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에 저장됩니다. 활동 로그에 저장된 정보의 양이 많을 수 있으므로, 리소스 상태 알림에 대한 경고를 보다 쉽게 확인하고 설정할 수 있도록 별도의 사용자 인터페이스가 있습니다.
Azure 리소스가 Azure 구독에 리소스 상태 알림을 보낼 때 경고를 받습니다. 다음 항목에 따라 경고를 구성할 수 있습니다.

* 영향을 받는 구독
* 영향을 받는 리소스 유형
* 영향을 받는 리소스 그룹
* 영향을 받는 리소스
* 영향을 받는 리소스의 이벤트 상태
* 영향을 받는 리소스 상태
* 영향을 받는 리소스의 원인 유형

다음과 같이 경고를 받는 사람도 구성할 수 있습니다.

* 기존 작업 그룹을 선택합니다.
* 새 작업 그룹을 만듭니다(향후 경고에 사용할 수 있음).

작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../azure-monitor/alerts/action-groups.md)를 참조하세요.

Azure Resource Manager 템플릿을 사용하여 리소스 상태 알림 경고를 구성하는 방법에 대한 자세한 내용은 [Resource Manager 템플릿](./resource-health-alert-arm-template-guide.md)을 참조하세요.
Azure Portal을 사용한 Resource Health 경고

## <a name="resource-health-alert-using-azure-portal"></a>Azure Portal을 사용한 Resource Health 경고

1. Azure [Portal](https://portal.azure.com/)에서 **Service Health** 를 선택합니다.

    ![Service Health 선택 영역](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. **Resource Health** 섹션에서 **Service Health** 를 선택합니다.
3. **리소스 상태 경고 추가** 를 선택하고 필드를 입력합니다.
4. 경고 대상에서 경고를 받고자 하는 **구독**, **리소스 유형**, **리소스 그룹**, **리소스** 를 선택합니다.

    ![대상 선택 영역 선택](./media/resource-health-alert-monitor-guide/alert-target.png)

5. 경고 조건에서 다음을 선택합니다.
    1. 경고를 받고자 하는 **이벤트 상태** 입니다. 이벤트의 심각도 수준: 활성, 해결됨, 진행 중, 업데이트됨
    2. 경고를 받고자 하는 **리소스 상태** 입니다. 이벤트의 리소스 상태: 사용 가능, 사용할 수 없음, 알 수 없음, 성능 저하됨
    3. 경고를 받고자 하는 **원인 유형** 입니다. 이벤트의 원인: 플랫폼에서 발생됨, 사용자가 발생시킴 ![경고 조건 선택 영역 상태 선택 영역](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. 경고 세부 정보 정의에서 다음 세부 정보를 입력합니다.
    1. **경고 규칙 이름**: 새 경고 규칙의 이름입니다.
    2. **설명**: 새 경고 규칙에 대한 설명입니다.
    3. **리소스 그룹에 경고 저장**: 이 새 규칙을 저장하고자 하는 리소스 그룹을 선택합니다.
7. **작업 그룹** 아래의 드롭다운 메뉴에서 이 새 경고 규칙에 할당할 작업 그룹을 지정합니다. 또는 [새 작업 그룹을 만들어](../azure-monitor/alerts/action-groups.md) 새 규칙에 할당합니다. 새 그룹을 만들려면 + **새 그룹** 을 선택합니다.
8. 규칙을 만든 후 사용하도록 설정하려면 **규칙을 만들면 바로 사용** 에 대해 **예** 를 선택합니다.
9. **경고 규칙 만들기** 를 선택합니다.

활동 로그에 대한 새 경고 규칙이 생성되고 창의 오른쪽 상단에 확인 메시지가 나타납니다.
규칙을 사용/사용하지 않도록 설정, 편집 또는 삭제할 수 있습니다. [활동 로그 규칙을 관리하는 방법](../azure-monitor/alerts/alerts-activity-log.md#view-and-manage-in-the-azure-portal)을 알아봅니다.

## <a name="next-steps"></a>다음 단계

Resource Health에 대해 알아봅니다.

* [Azure Resource Health 개요](Resource-health-overview.md)
* [Azure Resource Health를 통해 사용할 수 있는 리소스 유형 및 상태 검사](resource-health-checks-resource-types.md)

Service Health 경고 만들기:

* [Service Health에 대한 경고 구성](./alerts-activity-log-service-notifications-portal.md) 
* [Azure 활동 로그 이벤트 스키마](../azure-monitor/essentials/activity-log-schema.md)
* [Resource Manager 템플릿을 사용하여 리소스 상태 경고 구성](./resource-health-alert-arm-template-guide.md)
