---
title: 비용 분석 및 예산
description: Batch 워크로드를 실행하는 데 사용되는 기본 컴퓨팅 리소스 및 소프트웨어 라이선스의 비용 분석을 수행하고 예산을 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 07/19/2019
ms.openlocfilehash: 50ca1ecfd0a973ff39dabfcb62175ce820d0a0d6
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654245"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch의 비용 분석 및 예산

Azure Batch 자체에 부과되는 요금은 없으며, Batch 워크로드를 실행하는 데 사용된 기본 컴퓨팅 리소스 및 소프트웨어 라이선스에 대한 요금만 부과됩니다. 비용은 주로 풀의 VM(가상 머신), VM에서의 데이터 전송 또는 클라우드에 저장된 입력 또는 출력 데이터로 인해 발생합니다. Batch의 몇 가지 주요 구성 요소를 살펴보고, 비용이 발생하는 위치, 풀 또는 계정의 예산을 설정하는 방법 및 Batch 워크로드의 비용 효율을 높이는 몇 가지 기법을 살펴보겠습니다.

## <a name="batch-resources"></a>Batch 리소스

가상 머신은 Batch 처리에 사용되는 가장 중요한 리소스입니다. Batch의 VM 사용 비용은 유형, 수량 및 사용 기간을 기준으로 계산됩니다. VM 청구 옵션에는 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 및 [예약](../cost-management-billing/reservations/save-compute-costs-reservations.md)(선불) 등이 있습니다. 두 결제 옵션의 이점은 컴퓨팅 워크로드에 따라 달라지며, 두 결제 모델은 청구서에 다르게 적용됩니다.

[애플리케이션 패키지](batch-application-packages.md)를 사용하여 Batch 노드(VM)에 애플리케이션을 배포하는 경우 애플리케이션 패키지에서 사용하는 Azure Storage 리소스에 대한 요금이 청구됩니다. 또한 리소스 파일 및 기타 로그 데이터와 같은 입력 또는 출력 파일의 스토리지에 대한 요금도 청구됩니다. 일반적으로 Batch와 연결된 스토리지 데이터의 비용이 컴퓨팅 리소스 비용보다 훨씬 적습니다. **VirtualMachineConfiguration**을 사용하여 만든 풀의 각 VM에는 Azure 관리 디스크를 사용하는 OS 디스크가 연결되어 있습니다. Azure 관리 디스크에는 추가 비용이 부과되며 다른 디스크 성능 계층에도 다른 비용이 부과됩니다.

Batch 풀은 네트워킹 리소스를 사용합니다. 특히 **VirtualMachineConfiguration** 풀의 경우 표준 부하 분산 장치가 사용되며, 고정 IP 주소를 사용해야 합니다. Batch에서 사용하는 부하 분산 장치는 **사용자 구독** 계정에 표시되지만 **Batch 서비스** 계정에는 표시되지 않습니다. 표준 부하 분산 장치는 Batch 풀 VM에서 들어오고 나가는 모든 데이터에 요금을 부과합니다. 풀 노드(예: 가져오기 작업/노드 파일)에서 데이터를 검색하는 Batch API, 작업 애플리케이션 패키지, 리소스/출력 파일 및 컨테이너 이미지를 선택하면 요금이 발생합니다.

### <a name="additional-services"></a>서비스

VM 및 스토리지를 포함하지 않는 서비스는 Batch 계정의 비용으로 간주될 수 있습니다.

Batch와 함께 일반적으로 사용되는 다른 서비스는 다음과 같습니다.

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- 그래픽 애플리케이션이 있는 VM

Batch 솔루션과 함께 사용하는 서비스에 따라 추가 요금이 발생할 수 있습니다. 각 추가 서비스의 비용을 확인하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 참조하세요.

## <a name="cost-analysis-and-budget-for-a-pool"></a>풀의 비용 분석 및 예산

Azure Portal을 통해 Batch 풀 또는 Batch 계정에 대한 예산 및 지출 경고를 만들 수 있습니다. 예산과 경고는 관련자에게 과도한 지출 위험을 알리는 데 유용합니다. 지출 경고가 지연되어 예산을 약간 초과하는 경우가 있을 수 있습니다. 이 예에서는 개별 Batch 풀의 비용 분석을 볼 수 있습니다.

1. Azure Portal의 왼쪽 탐색 모음에서 **Cost Management + Billing**을 선택합니다.
1. **내 구독** 섹션에서 구독 선택
1. 왼쪽 탐색 모음의 **Cost Management** 섹션 아래에 있는 **비용 분석**으로 이동하여 다음과 같은 보기를 표시합니다.
1. **필터 추가**를 선택합니다. 첫 번째 드롭다운 목록에서 **리소스** ![리소스 필터 선택](./media/batch-budget/resource-filter.png)을 선택합니다.
1. 두 번째 드롭다운 목록에서 Batch 풀을 선택합니다. 풀을 선택하면 다음과 같은 비용 분석 결과가 표시됩니다.
    ![풀의 비용 분석](./media/batch-budget/pool-cost-analysis.png)

비용 분석 결과는 이 비용에 영향을 주는 리소스뿐만 아니라 풀의 비용도 보여 줍니다. 이 예에서는 풀에 사용된 VM이 가장 비용이 많이 드는 리소스입니다.

풀에 대한 예산을 만들려면 **예산: 없음**을 선택한 다음, **새 예산 만들기 >** 를 선택합니다. 이제 이 창을 사용하여 풀의 구체적인 예산을 구성합니다.

예산을 구성하는 방법에 대한 자세한 내용은 [Azure 예산 만들기 및 관리](../cost-management-billing/costs/tutorial-acm-create-budgets.md)를 참조하세요.

> [!NOTE]
> Azure Batch는 Azure Cloud Services 및 Azure Virtual Machines 기술을 기반으로 빌드됩니다. **Cloud Services 구성**을 선택하는 경우 Cloud Services 가격 책정 체계에 따라 요금이 청구됩니다. **Virtual Machine 구성**을 선택하는 경우 Virtual Machines 가격 책정 체계에 따라 요금이 청구됩니다. 이 페이지의 예제에서는 **가상 머신 구성**을 사용 합니다.

## <a name="minimize-cost"></a>비용 최소화

오랜 기간 동안 여러 VM 및 Azure 서비스를 사용하면 비용이 많이 들 수 있습니다. 다행히 워크로드 효율을 극대화할 수 있는 전략뿐만 아니라 지출 절감에 도움이 되는 서비스를 사용할 수 있습니다.

### <a name="low-priority-virtual-machines"></a>우선 순위가 낮은 가상 머신

우선 순위가 낮은 VM은 Azure에서 여분의 컴퓨팅 용량을 활용하여 Batch 워크로드의 비용을 줄입니다. 풀에서 우선 순위가 낮은 VM을 지정하면 Batch는 이러한 남는 용량을 사용하여 워크로드를 실행할 수 있습니다. 전용 VM 대신 우선 순위가 낮은 VM을 사용하여 상당한 비용을 절감할 수 있습니다.

[Batch에 우선 순위가 낮은 VM 사용](batch-low-pri-vms.md)에서 워크로드에 우선 순위가 낮은 VM을 설정하는 방법에 대해 자세히 알아보세요.

### <a name="virtual-machine-os-disk-type"></a>가상 머신 OS 디스크 유형

여러 [VM OS 디스크 유형](../virtual-machines/disks-types.md)이 있습니다. 대부분의 VM 시리즈는 프리미엄 및 표준 스토리지를 모두 지원하는 크기를 가집니다. 풀에 ‘s’ VM 크기가 선택되면 Batch가 프리미엄 SSD OS 디스크를 구성합니다. ‘non-s’ VM 크기가 선택되면 더 저렴한 표준 HDD 디스크 유형이 사용됩니다. 예를 들어 프리미엄 SSD OS 디스크는 `Standard_D2s_v3`에 사용되고 표준 HDD OS 디스크는 `Standard_D2_v3`에 사용됩니다.

프리미엄 SSD OS 디스크는 비용이 더 많이 들지만 더 고성능이며, 프리미엄 디스크를 사용하는 VM은 표준 HDD OS 디스크를 사용하는 VM보다 약간 더 빠르게 시작할 수 있습니다. Batch를 사용하면 애플리케이션 및 태스크 파일이 VM 임시 SSD 디스크에 있기 때문에 OS 디스크가 자주 사용되지 않습니다. 따라서 대부분의 경우 ‘s’ VM 크기가 지정되면 프로비저닝되는 프리미엄 SSD에 늘어난 비용을 지불할 필요가 없습니다.

### <a name="reserved-virtual-machine-instances"></a>예약 가상 머신 인스턴스

Batch를 장기간 사용하려는 경우 워크로드에 [Azure 예약](../cost-management-billing/reservations/save-compute-costs-reservations.md)을 사용하여 VM 비용을 절감할 수 있습니다. 예약 요금은 종량제 요금보다 훨씬 저렴합니다. 예약 없이 사용되는 가상 머신 인스턴스는 종량제 요금으로 청구됩니다. 예약을 구매하면 예약 할인이 적용되며 종량제 요금이 더 이상 부과되지 않습니다.

### <a name="automatic-scaling"></a>자동 크기 조정

[자동 크기 조정](batch-automatic-scaling.md)은 현재 작업의 요구에 따라 Batch 풀의 VM 수를 동적으로 조정합니다. 자동 크기 조정은 작업 수명에 따라 풀의 크기를 조정하여 수행할 작업이 있는 경우에만 VM이 스케일 업되고 사용되도록 합니다. 작업이 완료되거나 작업이 없으면 VM이 자동으로 스케일 다운되어 컴퓨팅 리소스가 절약됩니다. 스케일링을 사용하면 필요한 리소스만 사용하여 Batch 솔루션의 전체 비용을 낮출 수 있습니다.

자동 크기 조정에 대한 자세한 내용은 [Azure Batch 풀에서 자동으로 컴퓨팅 노드 크기 조정](batch-automatic-scaling.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- Batch 솔루션을 빌드하고 모니터링하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 자세히 알아봅니다.  

- [Batch에서 우선 순위가 낮은 VM을 사용하는 방법](batch-low-pri-vms.md)에 대해 알아보기
