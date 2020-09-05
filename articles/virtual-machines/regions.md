---
title: Azure 지역
description: Azure에서 가상 머신을 실행 하기 위한 지역에 대해 알아봅니다.
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/28/2019
ms.openlocfilehash: 094b78fa35da451101f4f5664cca46292d78d146
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650556"
---
# <a name="regions-for-virtual-machines-in-azure"></a>Azure에서 가상 머신의 지역

성능, 가용성 및 중복성을 최대화하는 옵션을 함께 Azure에서 VMs(가상 머신)가 작동하는 위치와 방법을 이해하는 것이 중요합니다. 이 문서에서는 Azure의 가용성 및 중복성 기능에 대 한 개요를 제공합니다.


## <a name="what-are-azure-regions"></a>Azure 지역이란?
Azure는 전 세계 여러 데이터 센터에서 작동합니다. 이러한 데이터 센터는 지리적 영역으로 그룹화되므로 애플리케이션을 빌드할 위치를 유연하게 선택할 수 있습니다. 

'미국 서부', '북유럽' 또는 '동남 아시아'와 같이 정의된 지역에 Azure 리소스를 만듭니다. [지역 및 위치 목록](https://azure.microsoft.com/regions/)을 검토할 수 있습니다. 각 지역 내에는 중복성 및 가용성을 제공하기 위한 여러 데이터 센터가 존재합니다. 이 방법은 사용자에게 가장 가까운 VM을 만들고 법률, 규정 준수 또는 세금 목적을 충족할 수 있도록 애플리케이션을 설계할 때 유연성을 제공합니다.

## <a name="special-azure-regions"></a>특수 Azure 지역
Azure에는 규정 준수 또는 법적 목적에 맞게 애플리케이션을 빌드할 때 사용할 수 있는 특별한 Azure 지역이 있습니다. 이 특수 지역에는 다음이 포함됩니다.

* **US Gov 버지니아** 및 **US Gov 아이오와**
  * 미국 정부 기관 및 파트너를 위한 물리적 및 논리적 네트워크로 격리된 Azure 인스턴스로, 선별된 미국인이 운영합니다. [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) 및 [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA)와 같은 추가 규정 준수 인증서를 포함합니다. [Azure Government](https://azure.microsoft.com/features/gov/)에 대해 자세히 알아보세요.
* **중국 동부** 및 **중국 북부**
  * 이러한 지역은 Microsoft 및 21Vianet 간의 고유한 파트너십을 통해 사용할 수 있으며, Microsoft에서 데이터 센터를 직접 관리하지 않습니다. [Azure 중국 21vianet](https://www.windowsazure.cn/)에 대 한 자세한 내용을 참조 하세요.
* **독일 중부** 및 **독일 북동부**
  * 이러한 지역은 데이터 트러스티 모델을 통해 사용할 수 있으며, 이로 인해 독일 데이터 트러스티 역할을 수행하는 독일 통신국 회사인 T-Systems의 관리 하에 고객 데이터가 독일에서 유지됩니다.

## <a name="region-pairs"></a>지역 쌍
각 Azure 지역은 동일한 지리적 위치 내의 다른 지역(예: 미국, 유럽 또는 아시아)과 쌍을 이룹니다. 이 방법을 통해 지역에서 VM 스토리지와 같은 리소스를 복제하여 자연 재해, 내전, 정전 또는 물리적 네트워크 중단이 두 지역 모두에 영향을 미칠 가능성을 줄일 수 있습니다. Azure 지역 쌍의 추가적인 이점은 다음과 같습니다.

* 좀 더 광범위한 Azure 중단 시, 모든 쌍 중에서 한 지역에 더 높은 우선 순위를 두어 애플리케이션 복원 시간을 단축할 수 있습니다. 
* 계획된 Azure 업데이트는 가동 중지 및 애플리케이션 중단 위험을 최소화하기 위해 한 번에 한 쌍의 지역으로 롤아웃됩니다.
* 데이터는 세금 및 법률 집행 관할 구역에 사용될 수 있게 동일한 지리적 위치 내에 쌍으로(브라질 남부 제외) 상주합니다.

지역 쌍 예제는 다음과 같습니다.

| 주 | 보조 |
|:--- |:--- |
| 미국 서부 |미국 동부 |
| 북유럽 |서유럽 |
| 동남아시아 |동아시아 |

[여기에서 지역 쌍의 전체 목록을](../best-practices-availability-paired-regions.md#what-are-paired-regions)볼 수 있습니다.

## <a name="feature-availability"></a>기능 가용성
일부 서비스 또는 VM 기능(예: 특정 VM 크기 또는 스토리지 형식)은 특정 지역에서만 사용할 수 있습니다. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), [Traffic Manager](../traffic-manager/traffic-manager-overview.md) 또는 [Azure DNS](../dns/dns-overview.md)와 같이 특정 지역을 선택하지 않아도 되는 전역 Azure 서비스도 있습니다. 애플리케이션 환경 설계에 도움이 되도록 [각 지역의 Azure 서비스 가용성](https://azure.microsoft.com/regions/#services)을 확인할 수 있습니다. [각 지역에서 지원되는 VM 크기 및 제한을 프로그래밍 방식으로 쿼리](../azure-resource-manager/templates/error-sku-not-available.md)할 수도 있습니다.

## <a name="storage-availability"></a>스토리지 가용성
사용 가능한 스토리지 복제 옵션을 고려할 때 Azure 지역 및 지리적 위치를 이해하는 것이 중요합니다. 스토리지 형식에 따라 여러 복제 옵션이 있습니다.

**Azure Managed Disks**
* LRS(로컬 중복 스토리지)
  * 스토리지 계정을 만든 지역 내에서 데이터를 3번 복제합니다.

**Storage 계정 기반 디스크**
* LRS(로컬 중복 스토리지)
  * 스토리지 계정을 만든 지역 내에서 데이터를 3번 복제합니다.
* ZRS(영역 중복 스토리지)
  * 단일 지역 내에서 또는 2개 지역에 걸쳐 2~3개 시설에서 데이터를 3번 복제합니다.
* GRS(지역 중복 스토리지)
  * 기본 지역에서 수백 마일 떨어져 있는 보조 영역에 데이터를 복제합니다.
* RA-GRS(읽기 액세스 지역 중복 스토리지)
  * GRS를 사용할 경우와 마찬가지로 보조 지역에 데이터를 복제하지만 보조 위치의 데이터에 대해 읽기 전용 액세스를 제공합니다.

다음 테이블에서는 스토리지 복제 형식 간 차이점을 간략히 요약해서 보여 줍니다.

| 복제 전략 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 데이터가 여러 시설에 걸쳐 복제됩니다. |아니요 |예 |예 |예 |
| 기본 위치와 보조 위치에서 데이터를 읽을 수 있습니다. |아니요 |아니요 |아니요 |예 |
| 별도 노드에서 유지 관리되는 데이터 복사본 수입니다. |3 |3 |6 |6 |

[여기에서 Azure Storage 복제 옵션](../storage/common/storage-redundancy.md)에 대해 자세히 알아볼 수 있습니다. 관리 디스크에 대한 자세한 내용은 [Azure Managed Disks 개요](./managed-disks-overview.md)를 참조하세요.

### <a name="storage-costs"></a>스토리지 비용
가격은 선택한 스토리지 형식 및 가용성에 따라 달라집니다.

**Azure Managed Disks**
* 프리미엄 Managed Disks는 SSD(반도체 드라이브)로 지원되며, 표준 Managed Disks는 일반 회전 디스크로 지원됩니다. 프리미엄 및 표준 Managed Disks는 모두 디스크에 프로비전된 용량에 따라 요금이 청구됩니다.

**관리되지 않는 디스크**
* Premium Storage는 SSD(반도체 드라이브)로 지원되며, 디스크 용량에 따라 요금이 청구됩니다.
* 표준 스토리지는 일반적인 회전 디스크로 지원되며, 사용 중인 용량 및 원하는 스토리지 가용성을 기준으로 요금이 청구됩니다.
  * RA-GRS의 경우 해당 데이터를 또 다른 Azure 지역에 복제하는 대역폭에 대해 추가적인 지역 복제 데이터 전송 비용이 부과됩니다.

다양한 스토리지 형식 및 가용성에 대한 가격 책정 정보를 보려면 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)을 참조하세요.