---
title: 경고 및 스마트 그룹 상태 관리
description: 경고 및 스마트 그룹 인스턴스의 상태 관리
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 2aa521b0552b60e5a875a5f46ab9887c6e5b6e3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60919665"
---
# <a name="manage-alert-and-smart-group-states"></a>경고 및 스마트 그룹 상태 관리
Azure Monitor의 경고에는 이제 [경고 상태 및 모니터 조건](https://aka.ms/azure-alerts-overview)이 있으며, 이와 유사하게 스마트 그룹에는 [스마트 그룹 상태](https://aka.ms/smart-groups)가 있습니다. 이제 상태 변경 사항은 각각의 경고 또는 스마트 그룹과 연관된 히스토리에 캡처됩니다. 이 문서에서는 경고 및 스마트 그룹 모두에 대한 상태 변경 프로세스를 단계별로 안내합니다.

## <a name="change-the-state-of-an-alert"></a>경고 상태 변경
1. 다음과 같은 여러 다른 방법으로 경고의 상태를 변경할 수 있습니다. 
    * [모든 경고] 페이지에서 상태를 변경할 경고 옆의 확인란을 클릭하고 [상태 변경]을 클릭합니다.   
    ![모니터링](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * 특정 경고 인스턴스의 [경고 세부사항] 페이지에서 [상태 변경]을 클릭할 수 있습니다.   
    ![모니터링](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * 특정 경고 인스턴스의 [경고 세부사항] 페이지에 있는 스마트 그룹 분할창에서 원하는 경고 옆에 있는 확인란을 클릭할 수 있습니다.    
    ![모니터링](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * [스마트 그룹 세부사항] 페이지의 [멤버 경고] 목록에서 상태를 변경하려는 경고 옆에 있는 확인란을 클릭하고 [상태 변경]을 클릭하여 상태를 변경할 수 있습니다.   
    ![모니터링](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. [상태 변경]을 클릭하여 팝업이 열리면 상태(New/Acknowledged/Closed)를 선택하고 필요한 경우 주석을 입력할 수 있습니다.   
![모니터링](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. 완료되면 상태 변경이 각 경고의 히스토리에 기록됩니다. 이 내용은 각 [세부사항] 페이지를 열고 히스토리 섹션을 확인하여 볼 수 있습니다.    
![모니터링](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>스마트 그룹의 상태를 변경합니다.
1. 다음과 같은 다른 방법으로 스마트 그룹의 상태를 변경할 수 있습니다.
    1. [스마트 그룹] 목록 페이지에서 상태를 변경하려는 스마트 그룹 옆에 있는 선택란을 클릭하고 [상태 변경]을 클릭할 수 있습니다.  
    ![모니터링](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. [스마트 그룹 세부사항] 페이지에서 [상태 변경]을 클릭할 수 있습니다.        
    ![모니터링](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. [상태 변경]을 클릭하여 팝업이 열리면 상태(New/Acknowledged/Closed)를 선택하고 필요한 경우 주석을 입력할 수 있습니다. 
![모니터링](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  스마트 그룹의 상태를 변경해도 개별 멤버 경고의 상태는 변경되지 않습니다.

1. 완료되면 상태 변경이 각 스마트 그룹의 히스토리에 기록됩니다. 이 내용은 각 [세부사항] 페이지를 열고 히스토리 섹션을 확인하여 볼 수 있습니다.     
![모니터링](./media/alerts-managing-alert-states/state-sg-history.jpg)
