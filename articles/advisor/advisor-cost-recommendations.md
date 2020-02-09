---
title: Azure Advisor를 사용하여 서비스 비용 절감
description: Azure Advisor를 사용하여 Azure 배포 비용을 최적화합니다.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: afa5a4068d2ec5f4730d261801760fe68d7a330e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443118"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Azure Advisor를 사용하여 서비스 비용 절감

Advisor는 유휴 및 사용 미달 리소스를 식별하여 전체적인 Azure 사용을 최적화하고 줄이는 데 도움을 줍니다. Advisor 대시보드의 **비용** 탭에서 비용 권장 사항을 가져올 수 있습니다.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>사용량이 낮은 인스턴스의 크기를 조정하거나 종료하여 가상 머신 소비 최적화 

특정 애플리케이션 시나리오에서는 기본적으로 사용률이 낮을 수 있으나 가상 머신의 크기와 수를 관리하여 비용을 절감할 수도 있습니다. Advisor 고급 평가 모델은 최대 CPU 사용률에 대한 최대 값이 3% 미만이 고 네트워크 사용률이 7 일 동안 2% 미만인 경우 P95th 가상 머신을 종료 하는 것으로 간주 합니다. 가상 컴퓨터는 현재 부하가 사용자 지향 작업을 수행할 때 40%를 초과 하지 않을 때 현재 부하가 80%의 사용률을 초과 하지 않도록 하는 작은 SKU (동일한 SKU 제품군 내) 또는 더 작은 수의 인스턴스로 현재 부하를 수용할 수 있는 경우 올바른 크기를 고려 합니다. 여기에서 워크 로드의 유형은 워크 로드의 CPU 사용률 특성을 분석 하 여 결정 됩니다.

권장 되는 작업은 권장 되는 리소스와 관련 하 여 종료 하거나 크기를 조정 하는 것입니다. Advisor에서는 권장 되는 작업 (크기 조정 또는 종료)에 대한 예상 비용 절감 액을 보여 줍니다. 또한 크기 조정 권장 작업의 경우 Advisor는 현재 및 대상 SKU 정보를 제공 합니다. 

미달 사용 가상 컴퓨터를 더 적극적으로 식별 하려면 구독 별로 CPU 사용률 규칙을 조정할 수 있습니다.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>프로비저닝되지 않은 ExpressRoute 회로를 제거하여 비용 절감

Advisor는 공급자 상태가 1개월 이상 *프로비저닝되지 않음*인 ExpressRoute 회로를 식별하고, 연결 공급자로 회로를 프로비저닝할 계획이 아닌 경우 회로를 삭제할 것을 권장합니다.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>유휴 상태의 가상 네트워크 게이트웨이를 삭제하거나 다시 구성하여 비용 절감

Advisor는 90 일 넘게 유휴 상태의 가상 네트워크 게이트웨이를 식별 합니다. 이러한 게이트웨이는 시간당 요금이 부과되므로 다시 구성하거나, 더 이상 사용하지 않으려는 경우 삭제를 고려해야 합니다. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>예약 가상 머신 인스턴스를 구매하여 종량제 비용보다 비용 절약

Advisor는 지난 30일 동안 가상 머신 사용량을 검토하고Azure 예약을 구매하여 비용을 절감할 수 있는지 확인합니다. Advisor는 잠재적으로 가장 절약할 수 있는 지역 및 크기를 보여주고, 예약 구매로 예상되는 절감을 보여줍니다. Azure 예약을 사용하여 가상 머신에 대한 기본 비용을 미리 구입할 수 있습니다. 할인은 예약과 동일한 크기 및 지역이 있는 새로운 또는 기존 VM에 자동으로 적용됩니다. [Azure Reserved VM Instances에 대한 자세한 정보](https://azure.microsoft.com/pricing/reserved-vm-instances/)

또한 Advisor는 30 일 후에 만료 되는 예약 인스턴스를 알려줍니다. 종 량 제 가격 책정을 방지 하기 위해 새 예약 인스턴스를 구매 하는 것이 좋습니다.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>연결 되지 않은 공용 IP 주소를 삭제 하 여 비용 절감

Advisor는 부하 분산 장치 또는 Vm과 같은 Azure 리소스에 현재 연결 되어 있지 않은 공용 IP 주소를 식별 합니다. 이러한 공용 IP 주소에는 명목 요금이 부과 됩니다. 사용 하지 않으려는 경우 삭제 하면 비용을 절감할 수 있습니다.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>실패 하는 Azure Data Factory 파이프라인 삭제

Azure Advisor은 반복적으로 실패 하는 Azure Data Factory 파이프라인을 검색 하 고, 더 이상 필요 하지 않은 경우 실패 한 파이프라인을 삭제 하거나 문제를 해결 하는 것을 권장 합니다. 오류가 발생 하는 동안에는 제공 하지 않더라도 이러한 파이프라인에 대한 요금이 청구 됩니다. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Managed Disks에 대한 표준 스냅숏 사용
60%의 비용을 절감하려면 부모 디스크의 스토리지 유형과 관계없이 Standard Storage에 스냅샷을 저장하는 것이 좋습니다. 이 옵션은 Managed Disks 스냅숏의 기본 옵션입니다. Azure Advisor는 Premium Storage 저장 된 스냅숏을 식별 하 고 Premium에서 Standard Storage로 스냅숏을 마이그레이션하는 것이 좋습니다. [관리 디스크 가격 책정에 대한 자세한 정보](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Advisor에서 비용 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 모든 페이지에서 [**Advisor**](https://aka.ms/azureadvisordashboard) 를 검색 하 고 선택 합니다.

1. **Advisor** 대시보드에서 **비용** 탭을 선택 합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [시작](advisor-get-started.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 작동의 뛰어난 권장 사항](advisor-operational-excellence-recommendations.md)
