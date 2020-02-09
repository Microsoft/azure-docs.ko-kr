---
title: 사용자에 게 중요 한 Azure Advisor 권장 사항 보기
description: 노이즈를 줄이기 위해 Azure Advisor 권장 사항을 확인 하 고 필터링 합니다.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422366"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>사용자에 게 중요 한 Azure Advisor 권장 사항 보기

Azure Advisor는 Azure 배포를 최적화 하는 데 도움이 되는 권장 사항을 제공 합니다. Advisor 내에서 사용자의 권장 사항에 대해서만 권장 사항의 범위를 좁히는 데 도움이 되는 몇 가지 기능에 액세스할 수 있습니다.

## <a name="configure-subscriptions-and-resource-groups"></a>구독 및 리소스 그룹 구성

Advisor를 사용 하면 사용자와 조직에 중요 한 구독 및 리소스 그룹을 선택할 수 있습니다. 선택한 구독 및 리소스 그룹에 대한 권장 사항만 표시 됩니다. 기본적으로 모두가 선택 됩니다. 구성 설정은 구독 또는 리소스 그룹에 적용 되므로 동일한 설정이 해당 구독 또는 리소스 그룹에 대한 액세스 권한이 있는 모든 사용자에 게 적용 됩니다. 구성 설정은 Azure Portal에서 또는 프로그래밍 방식으로 변경할 수 있습니다.

Azure Portal를 변경 하려면 다음을 수행 합니다.

1. Azure Portal에서 [Azure Advisor](https://aka.ms/azureadvisordashboard) 를 엽니다.

1. 메뉴에서 **구성** 을 선택 합니다.

   ![Advisor 구성 메뉴](./media/view-recommendations/configuration.png)

1. Advisor 권장 사항을 수신 하려면 구독 또는 리소스 그룹에 대한 **포함** 열의 확인란을 선택 합니다. 이 확인란을 사용할 수 없는 경우 해당 구독 또는 리소스 그룹에 대한 구성을 변경할 수 있는 권한이 없을 수 있습니다. [Azure Advisor의 사용 권한에](permissions.md)대해 자세히 알아보세요.

1. 변경을 수행한 후 아래쪽에서 **적용** 을 클릭 합니다.

## <a name="filtering-your-view-in-the-azure-portal"></a>Azure Portal에서 보기 필터링

구성 설정은 변경 될 때까지 활성 상태로 유지 됩니다. 단일 보기에 대한 권장 구성 보기를 제한 하려면 Advisor 패널의 맨 위에 제공 된 드롭다운 목록을 사용 하면 됩니다. 개요, 고가용성, 보안, 성능, 비용 및 모든 권장 사항 패널에서 보려는 구독, 리소스 종류 및 권장 사항 상태를 선택할 수 있습니다.

   ![Advisor 필터링 메뉴](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>권장 구성 해제 및 연기

Azure Advisor를 사용 하면 단일 리소스에 대한 권장 사항을 해제 하거나 연기할 수 있습니다. 권장 사항을 해제 하는 경우 수동으로 활성화 하지 않으면 다시 표시 되지 않습니다. 그러나 권장 사항을 연기 하면 권장 구성이 자동으로 다시 활성화 되는 기간을 지정할 수 있습니다. 연기는 Azure Portal에서 수행 하거나 프로그래밍 방식으로 수행할 수 있습니다.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Azure Portal에서 단일 권장 사항 연기 

1. Azure Portal에서 [Azure Advisor](https://aka.ms/azureadvisordashboard) 를 엽니다.
1. 권장 사항을 보려면 권장 사항 범주를 선택 합니다.
1. 권장 사항 목록에서 권장 사항을 선택 합니다.
1. 연기 하거나 해제 하려는 권장 사항에 대해 연기 또는 해제를 선택 합니다.

     ![Advisor 필터링 메뉴](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Azure Portal에서 여러 권장 사항을 연기 하거나 해제 합니다.

1. Azure Portal에서 [Azure Advisor](https://aka.ms/azureadvisordashboard) 를 엽니다.
1. 권장 사항을 보려면 권장 사항 범주를 선택 합니다.
1. 권장 사항 목록에서 권장 사항을 선택 합니다.
1. 연기 하려는 모든 리소스에 대해 행의 왼쪽에 있는 확인란을 선택 하 고 권장 사항을 해제 합니다.
1. 테이블의 왼쪽 위에서 **연기** 또는 **해제** 를 선택 합니다.

     ![Advisor 필터링 메뉴](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> 권장 사항을 해제 하거나 연기 하려면 참가자 또는 소유자 권한이 있어야 합니다. Azure Advisor의 사용 권한에 대해 자세히 알아보세요.

> [!NOTE]
> 선택 상자를 사용할 수 없는 경우에도 권장 사항이 로드 될 수 있습니다. 모든 권장 사항이 로드 될 때까지 기다렸다가 연기 하거나 해제 하십시오.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>연기 되거나 해제 된 권장 사항 다시 활성화

연기 되거나 해제 된 권장 사항을 활성화할 수 있습니다. 이 작업은 Azure Portal에서 수행 하거나 프로그래밍 방식으로 수행할 수 있습니다. Azure Portal에서 다음을 수행합니다.

1. Azure Portal에서 [Azure Advisor](https://aka.ms/azureadvisordashboard) 를 엽니다.

1. 개요 패널의 필터를 **연기**됨으로 변경 합니다. 그러면 Advisor는 연기 되거나 해제 한 권장 사항을 표시 합니다.

    ![Advisor 필터링 메뉴](./media/view-recommendations/activate-postponed.png)

1. 범주를 선택 하 여 **연기** 되거나 **해제** 된 권장 사항을 확인할 수 있습니다.

1. 권장 사항 목록에서 권장 사항을 선택 합니다. 이는이 권장 사항이 연기 되거나 해제 된 리소스를 표시 하기 위해 **연기 된 &** 해제 탭이 이미 선택 되어 있는 권장 사항을 엽니다.

1. 행의 끝에 있는 **활성화** 를 클릭 합니다. 클릭 하면 해당 리소스에 대한 권장 사항이 활성화 되며이 테이블에서 제거 됩니다. 이제 권장 사항이 **활성** 탭에 표시 됩니다.
 
     ![Advisor 필터링 메뉴](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Advisor에 대한 권장 사항을 볼 수 있는 방법을 설명 합니다. Advisor에 대해 자세히 알아보려면 다음을 참조하세요. 

- [Azure Advisor란?](advisor-overview.md)
- [Advisor 시작](advisor-get-started.md)
- [Azure Advisor의 사용 권한](permissions.md)



