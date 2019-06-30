---
title: Azure Advisor 시작 | Microsoft Docs
description: Azure Adviser를 시작합니다.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: f91e48a532a278c95d50775e135ac6379e8d8070
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332066"
---
# <a name="get-started-with-azure-advisor"></a>Azure Adviser 시작

Azure Portal을 통해 Advisor에 액세스하고, 권장 사항을 검색하고, 권장 사항을 구현하는 방법을 알아봅니다.

> [!NOTE]
> Azure Advisor는 백그라운드에서 자동으로 실행되어 새로 만든 리소스를 찾습니다. 해당 리소스에 대한 권장 사항을 제공하는 데 최대 24시간이 걸릴 수 있습니다.

## <a name="get-recommendations"></a>권장 사항 가져오기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 창에서 **Advisor**를 클릭합니다.  왼쪽 창에 Advisor가 표시되지 않으면 **모든 서비스**를 클릭합니다.  서비스 메뉴 창의 **모니터링 및 관리** 아래에서 **Advisor**를 클릭합니다. Advisor 대시보드가 표시됩니다.

   ![Azure Portal을 사용하여 Azure Advisor 액세스](./media/advisor-get-started/advisor-portal-menu.png) 

1. Advisor 대시보드에는 선택한 모든 구독에 대한 권장 사항 요약이 표시됩니다.  구독 필터 드롭다운을 사용하여 권장 사항을 표시할 구독을 선택할 수 있습니다.

1. 특정 범주에 대한 권장 사항을 가져오려면 탭 중 하나를 클릭합니다. **고가용성**, **보안**, **성능** 또는 **비용** 

   ![Azure Advisor 대시보드](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>권장 사항 세부 정보 가져오기 및 솔루션 구현

Advisor에서 권장 사항을 선택하여 권장 사항 작업 및 영향을 받는 리소스와 같은 추가 세부 사항을 보고 권장 사항에 대한 솔루션을 구현할 수 있습니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

1. 해당 범주 내의 권장 사항 목록을 표시하려면 권장 사항 범주를 선택하고 **모두** 탭을 선택하여 모든 권장 사항을 표시합니다.

1. 자세하게 검토하려는 권장 사항을 클릭합니다.

1. 권장 사항과 해당 권장 사항이 적용되는 리소스에 대한 정보를 검토합니다.

1. 권장 사항을 구현하려면 **권장 작업**을 클릭합니다.

## <a name="filter-recommendations"></a>필터 권장 사항

권장 사항을 필터링하여 가장 중요한 항목으로 드릴다운할 수 있습니다.  구독, 리소스 유형 또는 권장 사항 상태별로 필터링할 수 있습니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

1. Advisor 대시보드의 드롭다운을 사용하여 구독, 리소스 유형 또는 권장 사항 상태별로 필터링합니다.

    ![Advisor 검색 필터 조건](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>권장 사항 연기 또는 해제

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

1. 연기하거나 해제할 권장 사항으로 이동합니다.

1. 권장 사항을 클릭합니다.

1. **연기**를 클릭합니다. 

1. 연기 기간을 지정하거나, 권장 사항을 해제하려면 **안 함**을 선택합니다.

## <a name="exclude-subscriptions-or-resource-groups"></a>구독 또는 리소스 그룹 제외

Advisor 권장 사항을 수신하지 않으려는 리소스 그룹 또는 구독이 있을 수 있습니다(예: '테스트' 리소스).  특정 구독 및 리소스 그룹에 대한 권장 사항만 생성하도록 Advisor를 구성할 수 있습니다.

> [!NOTE]
> Advisor에서 구독 또는 리소스 그룹을 포함 또는 제외하려면 구독 소유자여야 합니다.  구독 또는 리소스 그룹에 대해 필요한 사용 권한이 없는 경우 사용자 인터페이스에서 포함 또는 제외 옵션을 사용할 수 없습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

1. 작업 모음에서 **구성**을 클릭합니다.

1. Advisor 권장 사항을 수신하지 않으려는 구독 또는 리소스 그룹의 선택을 취소합니다.

    ![Advisor 구성 리소스 예제](./media/advisor-get-started/advisor-configure-resources.png)

1. **적용** 단추를 클릭합니다.

## <a name="configure-low-usage-vm-recommendation"></a>사용량이 낮은 VM 권장 사항 구성

이 절차에서는 사용량이 낮은 가상 머신 권장 사항에 대한 평균 CPU 사용률 규칙을 구성합니다.

Advisor는 7 일 동안 가상 머신 사용량을 모니터링 하 고 사용률이 낮은 가상 컴퓨터를 식별 합니다. 가상 컴퓨터 사용률이 낮은 경우 해당 CPU 사용률이 5% 이하인 것으로 간주 됩니다 및 해당 네트워크 사용률은 2% 보다 작으면 또는 더 작은 가상 머신 크기에서 현재 워크 로드를 수용할 수 있는지 표시 합니다.

사용량이 낮은 가상 머신을 더 적극적으로 식별하려면 구독당 평균 CPU 사용률 규칙을 조정할 수 있습니다.  5%, 10%, 15% 또는 20 %CPU 사용률 규칙을 설정할 수 있습니다.

> [!NOTE]
> 사용량이 낮은 가상 머신을 식별하기 위한 평균 CPU 사용률 규칙을 조정하려면 구독 *소유자*여야 합니다.  구독 또는 리소스 그룹에 대해 필요한 사용 권한이 없는 경우 사용자 인터페이스에서 포함 또는 제외 옵션을 사용할 수 없습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

1. 작업 모음에서 **구성**을 클릭합니다.

1. **규칙** 탭을 클릭합니다.

1. 평균 CPU 사용률 규칙을 조정할 구독을 선택한 다음 **편집**을 클릭합니다.

1. 원하는 평균 CPU 사용률 값을 선택하고 **적용**을 클릭합니다.

1. 새 평균 CPU 사용률 규칙을 사용하도록 기존 권장 사항을 업데이트하려면 **권장 사항 새로 고침**을 클릭합니다. 

   ![Advisor 구성 권장 사항 규칙 예제](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>권장 사항 다운로드

Advisor는 권장 사항 요약을 다운로드할 수 있습니다.  권장 사항을 PDF 파일 또는 CSV 파일로 다운로드할 수 있습니다.  권장 사항을 다운로드하면 동료와 쉽게 공유하거나 권장 사항 데이터를 바탕으로 고유한 분석을 수행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

1. 작업 모음에서 **CSV로 다운로드** 또는 **PDF로 다운로드**를 클릭합니다.

다운로드 옵션은 Advisor 대시보드에 적용한 모든 필터를 반영합니다.  특정 권장 사항 범주 또는 권장 사항을 보면서 다운로드 옵션을 선택하면 다운로드한 요약에 해당 범주 또는 권장 사항에 대한 정보만 포함됩니다. 

## <a name="next-steps"></a>다음 단계

Advisor에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure Advisor 소개](advisor-overview.md)
- [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
- [Advisor 보안 권장 사항](advisor-security-recommendations.md)
- [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
- [Advisor 비용 권장 사항](advisor-performance-recommendations.md)
