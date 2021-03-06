---
title: Azure 스팟 Virtual Machines 사용
description: Azure 스팟 Virtual Machines를 사용 하 여 비용을 절감 하는 방법을 알아봅니다.
author: JagVeerappan
ms.author: jagaveer
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.reviewer: cynthn
ms.openlocfilehash: fb53fc37227e040ed7bd7fc8e47de9aed538bc2e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721395"
---
# <a name="use-azure-spot-virtual-machines"></a>Azure 스팟 Virtual Machines 사용 

Azure 지점 Virtual Machines를 사용 하면 비용을 크게 절약할 수 있는 사용 되지 않는 용량을 활용할 수 있습니다. Azure에서 용량을 다시 필요로 하는 경우 언제 든 지 azure 인프라가 azure point Virtual Machines를 제거 합니다. 따라서 Azure 스팟 Virtual Machines는 일괄 처리 작업, 개발/테스트 환경, 큰 계산 워크 로드 등의 중단을 처리할 수 있는 워크 로드에 적합 합니다.

사용 가능한 용량의 크기는 크기, 지역, 시간 등에 따라 달라질 수 있습니다. Azure 지점 Virtual Machines를 배포할 때 Azure는 사용 가능한 용량이 있는 경우 Vm을 할당 하지만 이러한 Vm에 대 한 SLA는 없습니다. Azure 스팟 가상 머신은 고가용성 보장을 제공 하지 않습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 30 초 통지를 통해 Azure point Virtual Machines를 제거 합니다. 


## <a name="eviction-policy"></a>제거 정책

Vm은 사용자가 설정한 최대 가격 또는 용량에 따라 제거할 수 있습니다. Azure 스폿 가상 머신을 만들 때 제거 정책을 *할당* 취소 (기본값) 또는 *삭제* 로 설정할 수 있습니다. 

*할당* 취소 정책은 VM을 중지 된 할당 취소 상태로 전환 하 여 나중에 다시 배포할 수 있도록 합니다. 그러나 할당이 성공하리라는 보장은 없습니다. 할당 취소 된 Vm은 할당량에 따라 계산 되며 기본 디스크에 대 한 저장소 비용이 청구 됩니다. 

제거 될 때 VM을 삭제 하려는 경우 *삭제* 하도록 제거 정책을 설정할 수 있습니다. 제거 된 Vm은 기본 디스크와 함께 삭제 되므로 저장소에 대 한 요금이 계속 청구 되지 않습니다. 

[Azure Scheduled Events](./linux/scheduled-events.md)를 통해 VM 내 알림을 받도록 옵트인 (opt in) 할 수 있습니다. 이렇게 하면 Vm을 제거 하는 경우에 알림이 표시 되며, 제거 되기 전에 작업을 완료 하 고 종료 작업을 수행 하는 데 30 초 정도 걸립니다. 


| 옵션 | 결과 |
|--------|---------|
| 최대 가격은 현재 가격과 >로 설정 됩니다. | 용량 및 할당량을 사용할 수 있는 경우 VM이 배포 됩니다. |
| 최대 가격은 현재 가격을 <로 설정 됩니다. | VM이 배포 되지 않았습니다. 최대 가격은 >= 현재 가격에 대 한 오류 메시지를 받게 됩니다. |
| 최대 가격이 >이면 VM 중지/할당 취소를 다시 시작 합니다. 현재 가격 | 용량과 할당량이 있으면 VM이 배포 됩니다. |
| 최대 가격이 현재 가격을 < 경우 VM 중지/할당 취소를 다시 시작 하는 중 | 최대 가격은 >= 현재 가격에 대 한 오류 메시지를 받게 됩니다. | 
| VM에 대 한 가격이 최신 가격으로 > 되었습니다. | VM이 제거 됩니다. 실제 제거 전에 30 초 알림을 받습니다. | 
| 제거 후 VM의 가격은 최대 가격 <로 돌아갑니다. | VM이 자동으로 다시 시작 되지 않습니다. VM을 직접 다시 시작 하 고 현재 가격으로 요금이 청구 될 수 있습니다. |
| 최대 가격이로 설정 된 경우 `-1` | VM은 가격 책정 이유로 제거 되지 않습니다. 최대 가격은 현재 가격으로 표준 Vm의 가격까지 청구 됩니다. 표준 가격 이상으로는 요금이 부과 되지 않습니다.| 
| 최대 가격 변경 | 최대 가격을 변경 하려면 VM의 할당을 취소 해야 합니다. VM 할당을 취소 하 고, 새 최대 가격을 설정한 다음, VM을 업데이트 합니다. |


## <a name="limitations"></a>제한 사항

Azure 지점 Virtual Machines에 대해 지원 되지 않는 VM 크기는 다음과 같습니다.
 - B 시리즈
 - 모든 크기의 프로 모션 버전 (예: Dv2, NV, NC, H 프로 모션 크기)

Azure 스팟 Virtual Machines는 중국 21Vianet Microsoft Azure를 제외 하 고 모든 지역에 배포할 수 있습니다.

<a name="channel"></a>

현재 지원 되는 [제품 유형은](https://azure.microsoft.com/support/legal/offer-details/) 다음과 같습니다.

-   기업 계약 
-   종 량 제 제품 코드 (003P)
-   후원 (0036P 및 0136P)
- CSP (클라우드 서비스 공급자)의 경우 파트너에 게 문의 하세요.


## <a name="pricing"></a>가격

Azure 스폿 Virtual Machines의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대 한 VM 가격 책정을 참조 하세요. 

[Azure 소매 가격 API](/rest/api/cost-management/retail-prices/azure-retail-prices) 를 사용 하 여 가격 정보를 쿼리하여 스폿 가격 책정에 대 한 정보를 쿼리할 수도 있습니다. 및에는 `meterName` `skuName` 둘 다 포함 됩니다 `Spot` .

가변 가격 책정을 사용 하는 경우 최대 5 개의 소수 자릿수를 사용 하 여 미국 달러 (USD)로 최대 가격을 설정 하는 옵션을 사용할 수 있습니다. 예를 들어 값은 `0.98765` 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을로 설정 하는 경우 `-1` 가격에 따라 VM이 제거 되지 않습니다. VM의 가격은 사용 가능한 용량과 할당량을 초과 하는 경우 더 작은 표준 VM의 현재 가격 또는 가격입니다.

## <a name="pricing-and-eviction-history"></a>가격 책정 및 제거 기록

포털의 지역에서 크기 당 기록 가격 책정 및 제거 비율을 확인할 수 있습니다. **가격 책정 기록 보기 및 주변 지역의 가격 비교** 를 선택 하 여 특정 크기에 대 한 테이블 또는 가격 책정 그래프를 표시 합니다.  다음 이미지의 가격 책정 및 제거 요금은 예입니다. 

**차트**:

:::image type="content" source="./media/spot-chart.png" alt-text="가격 책정 및 제거 비율 차이가 차트로 포함 된 지역 옵션의 스크린샷":::

**테이블**:

:::image type="content" source="./media/spot-table.png" alt-text="가격 책정과 제거 비율의 차이가 테이블로 포함 된 지역 옵션의 스크린샷":::



##  <a name="frequently-asked-questions"></a>질문과 대답

**Q:** 일단 만들어지면 Azure 스폿 가상 머신은 일반 표준 VM과 동일 하나요?

**A:** 예. 단, Azure 스폿 Virtual Machines에 대 한 SLA는 없으며 언제 든 지 제거할 수 있습니다.


**Q:** 제거 되었지만 여전히 용량이 필요한 경우 수행할 작업

**A:** 용량이 필요한 경우 Azure 지점 Virtual Machines 대신 표준 Vm을 사용 하는 것이 좋습니다.


**Q:** Azure 지점 Virtual Machines에 대해 할당량을 관리 하는 방법

**A:** Azure 지점 Virtual Machines에는 별도의 할당량 풀이 있습니다. 지점 할당량은 Vm과 확장 집합 인스턴스 간에 공유 됩니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조 하세요.


**Q:** Azure 지점 Virtual Machines에 대 한 추가 할당량을 요청할 수 있나요?

**A:** 예, [표준 할당량 요청 프로세스](../azure-portal/supportability/per-vm-quota-requests.md)를 통해 Azure 지점 Virtual Machines의 할당량을 늘리기 위해 요청을 제출할 수 있습니다.


**Q:** 어디에서 질문을 게시할 수 있나요?

**A:** `azure-spot` [Q&A](/answers/topics/azure-spot.html)를 사용 하 여 질문을 게시 하 고 태그를 지정할 수 있습니다. 


**Q:** 지점 VM의 최대 가격을 어떻게 변경할 수 있나요?

**A:** 최대 가격을 변경 하려면 먼저 VM의 할당을 취소 해야 합니다. 그런 다음, VM에 대 한 **구성** 섹션에서 포털의 최대 가격을 변경할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[CLI](./linux/spot-cli.md), [포털](spot-portal.md), [ARM 템플릿](./linux/spot-template.md)또는 [PowerShell](./windows/spot-powershell.md) 을 사용 하 여 Azure 스폿 Virtual Machines를 배포 합니다.

[Azure 스폿 가상 머신 인스턴스를 사용 하 여 확장 집합](../virtual-machine-scale-sets/use-spot.md)을 배포할 수도 있습니다.

오류가 발생 하는 경우 [오류 코드](./error-codes-spot.md)를 참조 하세요.
