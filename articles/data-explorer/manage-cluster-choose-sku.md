---
title: Azure 데이터 탐색기 클러스터에 올바른 VM SKU를 선택 합니다.
description: 이 문서에서는 Azure 데이터 탐색기 클러스터에 대해 최적의 SKU 크기를 선택 하는 방법을 설명 합니다.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 8293fd2d84189cc1f1df3564abbfdcbf86e3543e
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186740"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 올바른 VM SKU를 선택 합니다. 

새 클러스터를 만들거나 변경 작업에 대한 클러스터를 최적화 하는 경우 Azure 데이터 탐색기는 여러 VM (가상 머신) Sku를 제공 하 여 선택할 수 있습니다. Vm은 작업에 가장 적합 한 비용을 제공 하기 위해 신중 하 게 선택 했습니다. 

데이터 관리 클러스터의 크기 및 VM SKU는 Azure 데이터 탐색기 서비스에 의해 완벽 하 게 관리 됩니다. 엔진의 VM 크기 및 수집 워크 로드와 같은 요인에 따라 결정 됩니다. 

언제 든 지 [클러스터를 확장](manage-cluster-vertical-scaling.md)하 여 엔진 클러스터에 대한 VM SKU를 변경할 수 있습니다. 초기 시나리오에 맞는 가장 작은 SKU 크기로 시작 하는 것이 가장 좋습니다. 클러스터를 확장 하면 새 VM SKU를 사용 하 여 클러스터를 다시 만드는 동안 최대 30 분의 가동 중지 시간이 발생 합니다.

> [!TIP]
> 계산 [예약 인스턴스 (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) 는 Azure 데이터 탐색기 클러스터에 적용 됩니다.  

이 문서에서는 다양 한 VM SKU 옵션을 설명 하 고 가장 적합 한 기술 세부 정보를 제공 합니다.

## <a name="select-a-cluster-type"></a>클러스터 유형 선택

Azure 데이터 탐색기는 두 가지 유형의 클러스터를 제공 합니다.

* **Production**: 프로덕션 클러스터는 엔진 및 데이터 관리 클러스터에 대한 두 개의 노드를 포함 하며 Azure 데이터 탐색기 [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)로 작동 합니다.

* **개발/테스트 (SLA 없음)** : 개발/테스트 클러스터에는 엔진 클러스터에 대한 단일 데이터 v2 노드와 데이터 관리 클러스터에 대한 단일 D1 노드가 있습니다. 이 클러스터 유형은 낮은 인스턴스 수 및 엔진 마크업 요금이 없으므로 가장 저렴 한 비용 구성입니다. 중복성이 부족 하기 때문에이 클러스터 구성에 대한 SLA는 없습니다.

## <a name="sku-types"></a>SKU 유형

Azure 데이터 탐색기 클러스터를 만들 때 계획 된 워크 로드에 대한 *최적의* VM SKU를 선택 합니다. 다음 두 가지 Azure 데이터 탐색기 SKU 제품군 중에서 선택할 수 있습니다.

* **D v2**: D SKU는 계산에 최적화 되 고 다음과 같은 두 가지 특성으로 제공 됩니다.
    * VM 자체
    * Premium storage 디스크와 함께 제공 되는 VM

* **LS**: L SKU는 저장소에 최적화 되어 있습니다. 비슷한 가격 책정 D SKU 보다 훨씬 더 큰 SSD 크기를 가집니다.

사용 가능한 SKU 유형 간의 주요 차이점은 다음 테이블에 설명 되어 있습니다.
 
| 특성 | D SKU | L SKU |
|---|---|---
|**소형 Sku**|최소 크기는 2 개 코어를 사용 합니다.|최소 크기는 4 개 코어를 사용 하는 L4입니다. |
|**Availability**|모든 지역에서 사용 가능 (DS + PS 버전의 가용성이 제한 됨)|몇 개 지역에서 사용 가능 |
|**코어 당&nbsp;GB 당 캐시 비용**|D SKU와 함께 사용, DS + PS 버전의 낮은 수준|종 량 제 옵션으로 가장 낮음 |
|**예약 인스턴스 (RI) 가격 책정**|높은 할인율 (3 년&nbsp;약정의 경우 55% 이상)|낮은 할인율 (3&nbsp;년 약정의 경우 20%) |  

## <a name="select-your-cluster-vm"></a>클러스터 VM 선택 

클러스터 VM을 선택 하려면 [수직 크기 조정을 구성](manage-cluster-vertical-scaling.md#configure-vertical-scaling)합니다. 

선택할 수 있는 다양 한 VM SKU 옵션을 사용 하 여 시나리오에 대한 성능 및 핫 캐시 요구 사항에 대한 비용을 최적화할 수 있습니다. 
* 높은 쿼리 볼륨에 가장 적합 한 성능이 필요한 경우에는 적절 한 SKU가 계산에 최적화 되어야 합니다. 
* 비교적 낮은 쿼리 로드를 사용 하 여 대량의 데이터를 쿼리해야 하는 경우 저장소에 최적화 된 SKU를 사용 하면 비용을 줄이고 여전히 뛰어난 성능을 제공할 수 있습니다.

Small Sku에 대한 클러스터 당 인스턴스 수가 제한 되므로 RAM이 더 큰 Vm을 사용 하는 것이 좋습니다. 을 사용 `joins`하는 쿼리와 같이 ram 리소스에 더 많은 수요를 주는 일부 쿼리 형식에는 더 많은 ram이 필요 합니다. 따라서 크기 조정 옵션을 고려 하는 경우 더 많은 인스턴스를 추가 하 여 확장 하는 대신 더 큰 SKU로 확장 하는 것이 좋습니다.

## <a name="vm-options"></a>VM 옵션

다음 표에서는 Azure 데이터 탐색기 클러스터 Vm에 대한 기술 사양을 설명 합니다.

|**이름**| **범주** | **SSD 크기** | **코어 수** | **RAM** | **Premium storage 디스크 (&nbsp;1tb)**| **클러스터당 최소 인스턴스 수** | **클러스터당 최대 인스턴스 수**
|---|---|---|---|---|---|---|---
|D11 v2| 계산에 최적화 | 75&nbsp;GB    | 2 | 14GB&nbsp; | 0 | 1 | 8 (개발/테스트 SKU (1) 제외)
|D12 v2| 계산에 최적화 | 150&nbsp;GB   | 4 | 28GB&nbsp; | 0 | 2 | 16
|D13 v2| 계산에 최적화 | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|D14 v2| 계산에 최적화 | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|DS13 v2 +&nbsp;&nbsp;1tb PS| 저장소 최적화 | &nbsp;1TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|DS13 v2 + 2tb&nbsp;PS&nbsp;| 저장소 최적화 | &nbsp;2TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 +&nbsp;3tb&nbsp;PS| 저장소 최적화 | 3TB&nbsp; | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 +&nbsp;4tb&nbsp;PS| 저장소 최적화 | 4TB&nbsp; | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|L4s v1| 저장소 최적화 | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| 저장소 최적화 | 1.3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|L16s_1| 저장소 최적화 | 2.6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* Azure 데이터 탐색기 [LISTSKUS API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)를 사용 하 여 지역별 업데이트 된 VM SKU 목록을 볼 수 있습니다. 
* [다양 한 sku](/azure/virtual-machines/windows/sizes)에 대해 자세히 알아보세요. 

## <a name="next-steps"></a>다음 단계

* 서로 다른 요구 사항에 따라 VM SKU를 변경 하 여 언제 든 지 엔진 클러스터를 [확장 하거나](manage-cluster-vertical-scaling.md) 축소할 수 있습니다. 

* 변화 하는 수요에 따라 용량을 변경 하기 위해 엔진 클러스터 크기를 [확장 하거나 축소할](manage-cluster-horizontal-scaling.md) 수 있습니다.

