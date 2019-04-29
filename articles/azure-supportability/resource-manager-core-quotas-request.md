---
title: Azure Resource Manager vCPU 할당량 증가 요청 | Microsoft Docs
description: Azure Resource Manager vCPU 할당량 증가 요청
author: ganganarayanan
ms.author: gangan
ms.date: 6/13/2018
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 204deaf3a67984c0dd5eca5352686719e7767885
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60649352"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Resource Manager vCPU 할당량 증가 요청

Resource Manager vCPU 할당량은 지역 수준 및 SKU 제품군 수준에 적용됩니다.
[Azure 구독 및 서비스 제한](https://aka.ms/quotalimits) 페이지에서 할당량이 적용되는 방식에 대해 자세히 알아보세요.
SKU 제품군에 대해 알아보려면 [Virtual Machines 가격 책정](https://aka.ms/pricingcompute) 페이지에서 비용과 성능을 비교할 수 있습니다.

증가를 요청하려면 Azure Portal에서 사용할 수 있는 Azure의 '사용량+할당량' 블레이드를 통해 지원 요청을 만들기 위해 사용하는 아래 지침에 따릅니다. 

## <a name="request-quota-increase-at-subscription-level"></a>구독 수준에서 할당량 증가 요청

1. https://portal.azure.com에서 **구독**을 선택합니다.

   ![구독](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 할당량을 늘려야 하는 구독을 선택합니다.

   ![구독 선택](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **사용량 + 할당량**을 선택합니다.

   ![사용량 + 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 오른쪽 위 모서리에서 **증가 요청**을 선택합니다.

   ![증가 요청](./media/resource-manager-core-quotas-request/request-increase.png)

5. 1단계 - 따옴표 형식으로 **코어**를 선택합니다. 

   ![양식 작성](./media/resource-manager-core-quotas-request/forms.png)
   
6. 2단계 - 배포 모델을 "Resource Manager"로 선택하고 위치를 선택합니다.

    ![할당량 문제 블레이드](./media/resource-manager-core-quotas-request/Problem-step.png)

3. 증가가 필요한 SKU 제품군을 선택합니다.

    ![SKU 시리즈 선택됨](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. 구독에 대한 새 한도를 입력합니다.

    ![SKU 새 할당량 요청](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- 줄을 제거하려면 SKU 제품군 드롭다운에서 SKU 선택을 해제하거나 삭제 "x" 아이콘을 클릭합니다.
각 SKU 제품군에 대해 원하는 할당량을 입력한 다음 문제 단계 페이지에서 "다음"을 클릭하여 지원 요청 만들기를 계속합니다.

