---
title: 비용 분석 및 예산-Azure Batch
description: Batch 워크 로드를 실행 하는 데 사용 되는 기본 계산 리소스 및 소프트웨어 라이선스에 대한 비용 분석을 하 고 예산을 설정 하는 방법을 알아봅니다.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: labrenne
ms.openlocfilehash: 819b5e16f4730e9a1998234288e181772f7c1996
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022718"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch에 대한 비용 분석 및 예산

일괄 작업을 실행 하는 데 사용 되는 기본 계산 리소스 및 소프트웨어 라이선스에 대한 Azure Batch 자체 요금이 부과 되지 않습니다. 높은 수준에서 비용은 풀의 Vm (가상 머신), VM에서의 데이터 전송 또는 클라우드에 저장 된 입력 또는 출력 데이터에서 발생 합니다. 일괄 처리의 몇 가지 주요 구성 요소를 살펴보고, 비용이 발생 하는 위치, 풀 또는 계정에 대한 예산을 설정 하는 방법 및 Batch 워크 로드를 더 비용 효율적으로 만드는 몇 가지 기법을 살펴보겠습니다.

## <a name="batch-resources"></a>일괄 처리 리소스

가상 머신은 일괄 처리에 사용 되는 가장 중요 한 리소스입니다. 일괄 처리에 대한 Vm 사용 비용은 유형, 수량 및 사용 기간을 기준으로 계산 됩니다. VM 청구 옵션에는 [종 량](https://azure.microsoft.com/offers/ms-azr-0003p/) 제 또는 [예약](../cost-management-billing/reservations/save-compute-costs-reservations.md) (미리 지불)이 포함 됩니다. 두 지불 옵션 모두 계산 워크 로드에 따라 이점이 다르며 두 지불 모델은 모두 청구서에 다르게 영향을 줍니다.

응용 프로그램 [패키지](batch-application-packages.md)를 사용 하 여 응용 프로그램을 Batch 노드 (vm)에 배포 하는 경우 응용 프로그램 패키지에 사용 되는 Azure Storage 리소스에 대해 요금이 청구 됩니다. 또한 리소스 파일 및 기타 로그 데이터와 같은 입력 또는 출력 파일의 저장소에 대한 요금이 청구 됩니다. 일반적으로 Batch와 연결 된 저장소 데이터의 비용은 계산 리소스의 비용 보다 훨씬 낮습니다. **VirtualMachineConfiguration** 를 사용 하 여 만든 풀의 각 VM에는 Azure에서 관리 되는 디스크를 사용 하는 연결 된 OS 디스크가 있습니다. Azure로 관리 되는 디스크에는 추가 비용이 포함 되 고 다른 디스크 성능 계층에는 다른 비용도 있습니다.

Batch 풀은 네트워킹 리소스를 사용 합니다. 특히 **VirtualMachineConfiguration** 풀의 경우 고정 IP 주소가 필요한 표준 부하 분산 장치가 사용 됩니다. Batch에서 사용 하는 부하 분산 장치는 **사용자 구독** 계정에 대해 표시 되지만 **batch 서비스** 계정에는 표시 되지 않습니다. 표준 부하 분산 장치는 Batch 풀 Vm에 전달 되는 모든 데이터에 대해 요금을 부과 합니다. 풀 노드 (예: 작업/노드 파일 가져오기)에서 데이터를 검색 하는 Batch Api, 작업 응용 프로그램 패키지, 리소스/출력 파일 및 컨테이너 이미지를 선택 하면 요금이 발생 합니다.

### <a name="additional-services"></a>추가 서비스

Vm 및 저장소를 포함 하지 않는 서비스는 Batch 계정의 비용을 고려해 야 할 수 있습니다.

Batch와 함께 일반적으로 사용 되는 다른 서비스는 다음과 같습니다.

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- 그래픽 응용 프로그램이 있는 Vm

Batch 솔루션에서 사용 하는 서비스에 따라 추가 요금이 발생할 수 있습니다. 각 추가 서비스의 비용을 확인 하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 참조 하세요.

## <a name="cost-analysis-and-budget-for-a-pool"></a>풀에 대한 비용 분석 및 예산

Azure Portal를 통해 Batch 풀 또는 Batch 계정에 대한 예산 및 지출 경고를 만들 수 있습니다. 예산과 경고는 관련자에 게 과도 한 지출 위험을 알리는 데 유용 합니다. 경고를 지출 하 고 예산을 약간 초과 하는 경우 지연이 발생할 수 있습니다. 이 예에서는 개별 Batch 풀의 비용 분석을 볼 수 있습니다.

1. Azure Portal의 왼쪽 탐색 모음에서 **Cost Management + 청구** 를 선택 합니다.
1. **내 구독** 섹션에서 구독을 선택 합니다.
1. 왼쪽 탐색 모음의 **Cost Management** 섹션에서 **비용 분석** 으로 이동 하 여 다음과 같은 뷰를 표시 합니다.
1. **필터 추가**를 선택 합니다. 첫 번째 드롭다운에서 **리소스** ![선택 하 고 리소스 필터를 선택](./media/batch-budget/resource-filter.png)
1. 두 번째 드롭다운에서 Batch 풀을 선택 합니다. 풀을 선택 하면 비용 분석은 다음과 같이 분석 됩니다.
    풀](./media/batch-budget/pool-cost-analysis.png) ![비용 분석

결과 비용 분석은이 비용에 영향을 주는 리소스 뿐만 아니라 풀의 비용을 보여 줍니다. 이 예에서 풀에서 사용 되는 Vm은 가장 비용이 많이 드는 리소스입니다.

풀에 대한 예산을 만들려면 **예산: 없음**을 선택 하 고 **새 예산 > 만들기**를 선택 합니다. 이제이 창을 사용 하 여 풀에 대해 특별히 예산을 구성 합니다.

예산을 구성 하는 방법에 대한 자세한 내용은 [Azure 예산 만들기 및 관리](../cost-management-billing/costs/tutorial-acm-create-budgets.md)를 참조 하세요.

> [!NOTE]
> Azure Batch는 Azure Cloud Services 및 Azure Virtual Machines 기술을 기반으로 빌드됩니다. **Cloud Services 구성을**선택 하면 Cloud Services 가격 책정 구조에 따라 요금이 청구 됩니다. **가상 컴퓨터 구성을**선택 하면 Virtual Machines 가격 책정 구조에 따라 요금이 청구 됩니다. 이 페이지의 예제에서는 **가상 머신 구성을**사용 합니다.

## <a name="minimize-cost"></a>비용 최소화

오랜 기간 동안 여러 Vm 및 Azure 서비스를 사용 하는 것은 비용이 많이 들 수 있습니다. 다행히 작업의 효율성을 극대화 하기 위한 전략 뿐만 아니라 지출 절감에 도움이 되는 서비스를 사용할 수 있습니다.

### <a name="low-priority-virtual-machines"></a>낮은 우선 순위의 가상 컴퓨터

우선 순위가 낮은 Vm은 Azure에서 여분의 컴퓨팅 용량을 활용 하 여 Batch 워크 로드의 비용을 줄입니다. 풀에서 우선 순위가 낮은 Vm을 지정 하는 경우 Batch는이 나머지를 사용 하 여 워크 로드를 실행 합니다. 전용 Vm 대신 우선 순위가 낮은 Vm을 사용 하 여 상당한 비용을 절감할 수 있습니다.

Batch를 사용 하 [여 우선 순위가 낮은 Vm 사용](batch-low-pri-vms.md)에서 워크 로드에 대해 우선 순위가 낮은 vm을 설정 하는 방법에 대해 자세히 알아보세요.

### <a name="virtual-machine-os-disk-type"></a>가상 컴퓨터 OS 디스크 유형

여러 [VM OS 디스크 유형이](../virtual-machines/windows/disks-types.md)있습니다. 대부분의 VM 시리즈는 프리미엄 및 표준 저장소를 모두 지 원하는 크기를 가집니다. 풀에 대해의 VM 크기를 선택 하면 Batch는 프리미엄 SSD OS 디스크를 구성 합니다. ' 비 s ' VM 크기를 선택 하면 더 저렴 한 표준 HDD 디스크 유형이 사용 됩니다. 예를 들어 프리미엄 SSD OS 디스크는 `Standard_D2s_v3`에 사용 되 고 표준 HDD OS 디스크는 `Standard_D2_v3`에 사용 됩니다.

프리미엄 SSD OS 디스크는 비용이 더 많이 들지만 프리미엄 디스크를 사용 하는 Vm은 표준 HDD OS 디스크를 사용 하는 Vm 보다 약간 더 빠르게 시작할 수 있습니다. Batch를 사용 하면 응용 프로그램 및 태스크 파일이 Vm 임시 SSD 디스크에 있는 것 처럼 OS 디스크는 자주 사용 되지 않습니다. 따라서 대부분의 경우의 VM 크기를 지정할 때 프로 비전 되는 프리미엄 SSD에 대해 증가 된 비용을 지불할 필요가 없습니다.

### <a name="reserved-virtual-machine-instances"></a>예약 가상 머신 인스턴스

장시간 배치를 사용 하려는 경우 작업에 [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) 를 사용 하 여 vm의 비용을 절감할 수 있습니다. 예약 률은 종 량 제 요금 보다 훨씬 낮습니다. 예약 없이 사용 되는 가상 머신 인스턴스는 종 량 제 요금으로 청구 됩니다. 예약을 구매 하는 경우 예약 할인이 적용 되며 더 이상 종 량 제 요금이 부과 되지 않습니다.

### <a name="automatic-scaling"></a>자동 크기 조정

[자동 크기 조정은](batch-automatic-scaling.md) 현재 작업의 요구에 따라 Batch 풀의 vm 수를 동적으로 조정 합니다. 자동 크기 조정은 작업 수명에 따라 풀의 크기를 조정 하 여 수행할 작업이 있는 경우에만 Vm의 크기를 조정 하 고 사용할 수 있도록 합니다. 작업이 완료 되거나 작업이 없으면 Vm이 자동으로 축소 되어 계산 리소스를 저장 합니다. 크기 조정을 사용 하면 필요한 리소스만 사용 하 여 Batch 솔루션의 전체 비용을 낮출 수 있습니다.

자동 크기 조정에 대한 자세한 내용은 [Azure Batch 풀에서 자동으로 계산 노드 크기 조정](batch-automatic-scaling.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- Batch 솔루션을 구축 하 고 모니터링 하는 데 사용할 수 있는 [Batch api 및 도구](batch-apis-tools.md) 에 대해 자세히 알아보세요.  

- [Batch를 사용 하 여 우선 순위가 낮은 vm](batch-low-pri-vms.md)에 대해 알아봅니다.
