---
title: VM 당 azure vCPU 할당량 증가 요청 | Microsoft Docs
description: 당 VM vCPU 할당량 증가 요청
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 79879cd7f5ea5af1b794735f32e6e1367458e124
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310718"
---
# <a name="vm-series-vcpu-limit-increase"></a>VM 시리즈 vCPU 제한 증가

Virtual machines 및 virtual machine scale sets에 대 한 resource Manager vCPU 할당량은 각 지역의 각 구독에 대 한 두 가지 계층에서 적용 됩니다. 

첫 번째 계층은는 **지역별 총 Vcpu 제한을** (모든 VM 시리즈)에 걸쳐 두 번째 계층은 합니다 **Vcpu VM 시리즈 당 제한** (예: D-시리즈 Vcpu). 새 VM을 배포 해야 하는 해당 VM 시리즈에 대 한 신규 및 기존 Vcpu 사용 합계는 특정 VM 시리즈에 대 한 승인 vCPU 할당량을 넘지 않아야 합니다. 또한 모든 VM 시리즈 간에 배포 된 총 신규 및 기존 vCPU 수는 구독에 대 한 승인 하는 지역별 총 Vcpu 할당량을 초과할 수 없습니다. 이러한 할당량 중 하나가 초과되면 VM 배포가 허용되지 않습니다.
Azure portal에서 VM 시리즈에 대 한 Vcpu 할당량 제한 증가 요청할 수 있습니다. VM 시리즈 할당량 증가 같은 크기 만큼 지역별 총 Vcpu 한계를 자동으로 증가합니다. 

새 구독이 생성 된 기본 지역별 총 Vcpu 모든 개별 VM 시리즈에 대 한 기본 vCPU 할당량의 합으로 같지 않을 수 있습니다. 이 구독에 배포 하려는 각 개별 VM 시리즈에 대 한 충분 한 할당량이 있지만 모든 배포에 대 한 지역별 총 vcpu 할당량이 충분 하지 발생할 수 있습니다. 이 경우에 늘리려면 지역별 총 Vcpu 명시적으로 요청을 제출 해야 합니다. 지역별 총 Vcpu 제한 된 영역에 대 한 모든 VM 시리즈에서 승인 된 할당량의 합계를 초과할 수 없습니다.

에 대 한 자세한 할당량에는 [가상 머신 vCPU 할당량 페이지](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) 및 [Azure 구독 및 서비스 제한](https://aka.ms/quotalimits) 페이지입니다. 

통해 증가 요청할 수 있습니다 **도움말 + 지원** 블레이드 또는 **사용량 + 할당량** 포털에서 블레이드입니다. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>당 구독 수준을 사용 하 여 VM 시리즈 vCPU 할당량 증가 요청 합니다 **도움말 + 지원** 블레이드

Azure portal에서 제공 하는 Azure의 '도움말 + 지원' 블레이드를 통해 지원 요청을 만들려면 아래 지침을 따릅니다. 

1. https://portal.azure.com 을 선택 **도움말 + 지원**합니다.

![도움말 + 지원](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **새 지원 요청**을 선택합니다. 

![새 지원 요청](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 문제 유형 드롭다운 목록에서 선택 **서비스 및 구독 제한 (할당량)** 합니다.

![문제 유형 드롭다운](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 할당량을 늘려야 하는 구독을 선택합니다.

![구독 선택 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 선택 **-VM (코어-개의 Vcpu) 구독 제한을 증가 계산** 에 **할당량 유형** 드롭다운 합니다. 

![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. **문제 세부 정보**를 클릭 하 여 보다 쉽게 처리할 수 있는 추가 정보를 요청 제공 **세부 정보를 제공**합니다.

![세부 정보를 제공 합니다.](./media/resource-manager-core-quotas-request/provide-details.png)

7. 에 **할당량 정보** 패널, 배포 모델을 선택 하 고 위치를 선택 합니다.

![할당량 정보 DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. 선택 된 **SKU 제품군** 증가 해야 하는 합니다. 

![SKU 제품군](./media/resource-manager-core-quotas-request/sku-family.png)

9. 구독에 대한 새 한도를 입력합니다. 줄을 제거하려면 SKU 제품군 드롭다운에서 SKU 선택을 해제하거나 삭제 "x" 아이콘을 클릭합니다. 각 SKU 제품군에 대해 원하는 할당량을 입력 한 후 클릭 **저장 및 계속** 지원 요청 만들기를 계속 진행 하려면 할당량 세부 정보 패널에 있습니다.

![새 제한](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>당 구독 수준을 사용 하 여 VM 시리즈 vCPU 할당량 증가 요청 **사용량 + 할당량** 블레이드

Azure의 '사용량 + 할당량'를 통해 지원 요청을 만들려면 사용 하 여 아래 지침에 따라 블레이드에서 Azure portal에서 사용할 수 있습니다. 

1. https://portal.azure.com 에서 **구독**을 선택합니다.

![구독](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 할당량을 늘려야 하는 구독을 선택합니다.

![구독 선택](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **사용량 + 할당량**을 선택합니다.

![사용량 + 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 오른쪽 위 모서리에서 **증가 요청**을 선택합니다.

![증가 요청](./media/resource-manager-core-quotas-request/request-increase.png)

5. 선택 **계산 VM (코어-개의 Vcpu) 구독 제한을 증가** 따옴표 형식으로 합니다. 

![양식 작성](./media/resource-manager-core-quotas-request/forms.png)
   
6. 에 **할당량 정보** 패널, 배포 모델을 선택 하 고 위치를 선택 합니다.

![할당량 문제 블레이드](./media/resource-manager-core-quotas-request/problemstep.png)

7. 선택 된 **SKU 제품군** 증가 해야 하는 합니다.

![SKU 시리즈 선택됨](./media/resource-manager-core-quotas-request/sku-family.png)

8. 구독에 대한 새 한도를 입력합니다. 줄을 제거하려면 SKU 제품군 드롭다운에서 SKU 선택을 해제하거나 삭제 "x" 아이콘을 클릭합니다. 각 SKU 제품군에 대해 원하는 할당량을 입력 한 후 클릭 **저장 및 계속** 지원 요청 만들기를 계속 진행 하려면 문제 단계 페이지에서.

![SKU 새 할당량 요청](./media/resource-manager-core-quotas-request/new-limits.png)

