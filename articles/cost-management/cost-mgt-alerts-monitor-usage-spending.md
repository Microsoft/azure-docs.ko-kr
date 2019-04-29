---
title: 비용 경고를 사용하여 사용량 및 지출 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure Cost Management에서 비용 경고를 사용하여 사용량 및 지출을 모니터링하는 방법을 설명합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: alavital
ms.custom: ''
ms.openlocfilehash: feb7fcdd9005ef131acadfc63defbe4caeaca014
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576564"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>비용 경고를 사용하여 사용량 및 지출 모니터링

이 문서는 Cost Management 경고를 이해하고 사용하여 Azure 사용량 및 지출을 모니터링하는 데 도움이 됩니다. 비용 경고는 Azure 리소스를 사용한 시기에 따라 자동으로 생성됩니다. 경고는 모든 활성 비용 관리 및 청구 경고를 한곳에서 보여 줍니다. 소비가 지정된 임계값에 도달하면 Cost Management에서 경고가 생성됩니다. 예산 경고, 크레딧 경고, 부서 지출 할당량 경고 등 세 가지 유형의 비용 경고가 있습니다.

## <a name="budget-alerts"></a>예산 경고

예산 경고는 사용량 또는 비용을 기준으로 지출이 [예산의 경고 조건](tutorial-acm-create-budgets.md)에 정의된 금액에 도달하거나 초과할 때 알립니다. Cost Management 예산은 Azure Portal 또는 [Azure Consumption](https://docs.microsoft.com/rest/api/consumption) API를 사용하여 만들어집니다.

Azure Portal에서는 비용을 통해 예산이 정의됩니다. Azure Consumption API를 사용하는 경우 비용 또는 소비 사용량을 통해 예산이 정의됩니다. 예산 경고는 비용 기반 예산과 사용량 기반 예산을 둘 다 지원합니다. 예산 경고 조건이 충족될 때마다 예산 경고가 자동으로 생성됩니다. Azure Portal에서 모든 비용 경고를 볼 수 있습니다. 경고가 생성될 때마다 비용 경고에 표시됩니다. 또한 예산의 경고 수신자 목록에 있는 사람에게 경고 메일이 전송됩니다.

## <a name="credit-alerts"></a>크레딧 경고

크레딧 경고는 Azure 크레딧 현금 약정 금액이 사용될 때 알립니다. 현금 약정 금액은 기업계약이 있는 조직에 해당합니다. 크레딧 경고는 Azure 크레딧 잔액의 90% 및 100%에서 자동으로 생성됩니다. 경고가 생성될 때마다 비용 경고와 계정 소유자에게 전송되는 메일에 반영됩니다.

## <a name="department-spending-quota-alerts"></a>부서 지출 할당량 경고

부서 지출 할당량 경고는 부서 지출이 할당량의 정해진 임계값에 도달할 때 알립니다. 지출 할당량은 EA 포털에서 구성됩니다. 임계값이 충족될 때마다 부서 소유자에게 전송되는 메일이 생성되고 비용 경고에 표시됩니다. 예를 들어 할당량의 50% 또는 75%입니다.

## <a name="supported-alert-features-by-offer-categories"></a>제품 범주별으로 지원 되는 경고 기능

경고 유형에 대 한 지원 (Microsoft 제공) 해야 하는 Azure 계정 유형에 따라 달라 집니다. 다음 표에서 지원 되는 경고 기능을 보여 줍니다. 다양 한 Microsoft 제품으로 합니다. Microsoft 제품의 전체 목록을 볼 수 있습니다 [이해 Cost Management 데이터](understand-cost-mgt-data.md)입니다.

| 경고 유형 | 기업 계약 | Microsoft 고객 계약 | Web direct/종 량 제 As-You-중단 |
|---|---|---|---|
| 예산 | ✔ | ✔ | ✔ |
| 크레딧 | ✔ |✘ | ✘ |
| 부서 지출 할당량 | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>비용 경고 보기

비용 경고를 보려면 Azure portal 선택에서 원하는 범위를 엽니다 **예산을** 메뉴에서. 사용 된 **범위** 필 다른 범위로 전환 하려면. 선택 **경고 비용** 메뉴에서. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

![Cost Management에 표시된 경고의 예제 이미지](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

활성 경고 및 해제된 경고의 총수가 비용 경고 페이지에 표시됩니다.

모든 경고에 경고 유형이 표시됩니다. 예산 경고에는 생성된 이유와 적용되는 예산의 이름이 표시됩니다. 각 경고는 생성된 날짜, 상태 및 경고가 적용되는 범위(구독 또는 관리 그룹)가 표시됩니다.

가능한 상태는 **활성**, **해제됨** 등입니다. 활성 상태는 경고가 여전히 관련이 있음을 나타냅니다. 해제됨 상태는 누군가가 더 이상 관련 없음으로 설정하도록 경고를 표시했음을 나타냅니다.

목록에서 경고를 선택하여 세부 정보를 확인합니다. 경고 세부 정보는 경고에 대한 자세한 정보를 표시합니다. 예산 경고에는 예산에 대한 링크가 포함됩니다. 예산 경고에 대한 권장 사항을 사용할 수 있는 경우 권장 사항에 대한 링크도 표시됩니다. 예산, 크레딧 및 부서 지출 할당량 경고에는 경고 범위에 대한 비용을 살펴볼 수 있는 비용 분석에서 분석하는 링크가 있습니다. 다음 예제에서는 경고 세부 정보와 함께 부서의 지출을 보여 줍니다.

![경고 세부 정보와 함께 부서의 지출이 표시된 예제 이미지](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

해제된 경고의 세부 정보를 보는 경우 수동 작업이 필요하면 다시 활성화할 수 있습니다. 다음 이미지에 예가 나와 있습니다.

![해제 및 다시 활성화 옵션이 표시된 예제 이미지](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>참고 항목

- 예산을 만들지 않았거나 예산에 대한 경고 조건을 설정하지 않은 경우 [예산 만들기 및 관리](tutorial-acm-create-budgets.md) 자습서를 완료합니다.
