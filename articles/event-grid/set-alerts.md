---
title: Azure Event Grid 메트릭 및 활동 로그 작업에 대한 경고 설정
description: 이 문서에서는 Azure Event Grid 메트릭 및 활동 로그 작업에 대한 경고를 만드는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48cb402e31435cb3e9390e8aeb461fcc5f90702f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572017"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>Azure Event Grid 메트릭 및 활동 로그에 대한 경고 설정
이 문서에서는 Azure Event Grid 메트릭 및 활동 로그 작업에 대한 경고를 만드는 방법을 설명합니다. Azure Event Grid 리소스(토픽 및 도메인)에 대한 게시 및 전달 메트릭 모두에 대한 경고를 만들 수 있습니다. 시스템 토픽의 경우 [**메트릭** 페이지를 사용하여 경고를 만듭니다.](#create-alerts-using-the-metrics-page)

## <a name="create-alerts-on-dead-lettered-events"></a>배달 못한 편지 이벤트에 대한 경고 만들기
다음 절차에서는 사용자 지정 토픽의 **배달 못한 편지 이벤트** 메트릭에 대한 경고를 만드는 방법을 보여 줍니다. 이 예제에서는 토픽의 배달 못한 편지 수가 10개를 초과하면 Azure 리소스 그룹 소유자에게 이메일을 보냅니다. 

1. 토픽의 **Event Grid 토픽** 페이지의 왼쪽 메뉴에서 **경고** 를 선택한 다음 **+ 새 경고 규칙** 을 선택합니다. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="경고 페이지 - 새 경고 규칙 단추":::
2. **경고 규칙 만들기** 페이지에서 리소스에 대해 토픽이 선택되어 있는지 확인합니다. 그런 다음 **선택** 을 클릭합니다. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="경고 페이지-조건 선택":::    
3. **신호 논리 구성** 페이지에서 다음 단계를 수행합니다.
    1. 메트릭 또는 활동 로그 항목을 선택합니다. 이 예제에서는 **배달 못한 편지 이벤트** 를 선택합니다. 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="배달 못한 편지 이벤트 선택":::        
    2. 차원을 선택합니다(선택 사항). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="신호 논리 구성":::        

        > [!NOTE]
        > **EventSubscriptionName** 의 **+** 단추를 선택하여 이벤트를 필터링할 이벤트 구독 이름을 지정할 수 있습니다. 
    3. 아래로 스크롤합니다. **경고 논리** 섹션에서 **연산자**, **집계 유형** 을 선택하고 **임계값** 을 입력한 다음 **완료** 를 선택합니다. 이 예제에서는 총 배달 못한 메시지 수가 10보다 큰 경우 경고가 트리거됩니다. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="경고 논리":::                
4. 다시 **경고 규칙 만들기** 페이지에서 **작업 그룹 선택** 을 클릭합니다.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="작업 그룹 만들기 단추":::
5. 도구 모음에서 **작업 그룹 만들기** 를 선택하여 새 작업 그룹을 만듭니다. 기존 작업 그룹을 선택할 수도 있습니다.        
6. **작업 그룹 추가** 페이지에서 다음 단계를 수행합니다.
    1. **작업 그룹의 이름** 을 입력합니다.
    1. 작업 그룹의 **짧은 이름** 을 입력합니다.
    1. 작업 그룹을 만들 **Azure 구독** 을 선택합니다.
    1. 작업 그룹을 만들 **Azure 리소스 그룹** 을 선택합니다.
    1. **작업의 이름** 을 입력합니다. 
    1. **작업 유형** 을 선택합니다. 이 예제에서는 **Azure Resource Manager 역할로 이메일**(특히 **소유자** 역할)을 선택합니다. 
    1. **확인** 을 선택하여 페이지를 닫습니다. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="작업 그룹 페이지 추가":::                   
7. 다시 **경고 규칙 만들기** 페이지에서 경고 규칙의 이름을 입력한 다음 **경고 규칙 만들기** 를 선택합니다.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="경고 규칙 이름":::  
8. 이제 토픽의 **경고** 페이지에 아직 경고가 없는 경우 경고 규칙을 관리하는 링크가 표시됩니다. 경고가 있는 경우 도구 모음에서 **관리자 경고 규칙** 을 선택합니다.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="경고 관리":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>다른 메트릭 또는 활동 로그 작업에 대한 경고 만들기
이전 섹션에서는 배달 못한 편지 이벤트에 대해 경고를 만드는 방법을 살펴보았습니다. 다른 메트릭 또는 활동 로그 작업에 대한 경고를 만드는 단계도 비슷합니다. 

예를 들어 배달 실패 이벤트에 대한 경고를 만들려면 **신호 논리 구성** 페이지에서 **배달 실패 이벤트** 를 선택합니다. 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="배달 실패 이벤트 선택":::


## <a name="create-alerts-using-the-metrics-page"></a>메트릭 페이지를 사용하여 경고 만들기
**메트릭** 페이지를 사용하여 경고를 만들 수도 있습니다. 단계는 비슷합니다. 시스템 토픽의 경우 **경고** 페이지를 사용할 수 없으므로 **메트릭** 페이지만 사용하여 경고를 만들 수 있습니다. 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="메트릭 페이지 - 경고 만들기 단추":::   
    

> [!NOTE]
> 이 문서에서는 경고를 만드는 데 사용할 수 있는 다양한 단계 및 조합을 다루지 않습니다. 경고에 대한 개요는 [경고 개요](../azure-monitor/alerts/alerts-metric.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md)를 참조하세요.
* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
