---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: da46687517dbfe189571286087d4ef29d50d1246
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564279"
---
# <a name="standard-ssd-managed-disks-for-azure-virtual-machine-workloads"></a>Azure 가상 머신 워크로드용 표준 SSD Managed Disks

Azure 표준 SSD(Solid State Drives) Managed Disks는 더 낮은 IOPS 수준에서 일관된 성능을 필요로 하는 워크로드에 최적화되고, 비용 효율적인 저장소 옵션입니다. 특히 표준 SSD는 온-프레미스 HDD 솔루션에서 실행 중인 다양한 워크로드에 문제가 발생해서 클라우드로 전환하고자 하는 경우에 적합한 입문용 환경을 제공합니다. 표준 SSD는 HDD 디스크보다 가용성, 일관성, 안정성, 대기 시간 면에서 더 나으며, 웹 서버, IOPS가 낮은 애플리케이션 서버, 사용량이 적은 엔터프라이즈 애플리케이션, 개발/테스트 워크로드에 적합합니다.

## <a name="standard-ssd-features"></a>표준 SSD 기능

**Managed Disks**: 표준 SSD는 Managed Disks로만 사용할 수 있습니다. Unmanaged Disks 및 페이지 Blob은 표준 SSD에서 지원되지 않습니다. Managed Disks를 만들 때 디스크 유형을 표준 SSD로 지정하고, 필요한 디스크 크기를 지정하면 Azure가 알아서 디스크를 만들고 관리해줍니다.
표준 SSD는 Managed Disks에서 제공하는 모든 클래식 배포 모델 작업을 지원합니다. 예를 들어 표준 SSD Managed Disks 생성, 복사, 스냅샷 생성 방법은 Managed Disks와 동일합니다.

**Virtual Machines**: 표준 SSD는 Premium Disks를 지원하지 않는 VM 유형을 포함한 모든 Azure VM에서 사용할 수 있습니다. 예를 들어 A 시리즈 VM , N 시리즈 VM, DS 시리즈 또는 다른 Azure VM 시리즈를 사용 중인 경우 표준 SSD를 해당 VM과 함께 사용할 수 있습니다. 표준 SSD가 도입됨에 따라 이전에 HDD 기반 디스크를 사용했던 광범위한 워크로드가 SSD 기반 디스크로 전환되어 SSD가 제공하는 일관적인 성능, 더 높은 가용성, 더 짧은 대기 시간, 전반적으로 개선된 환경을 경험할 수 있습니다.

**뛰어난 내구성 및 가용성**: 표준 SSD는 동일한 Azure Disks 플랫폼에 구축되어 고가용성과 내구성을 일관성 있게 디스크에 제공합니다. Azure 디스크는 99.999% 가용성을 위해 설계되었습니다. 모든 Managed Disks와 마찬가지로, 표준 SSD도 LRS(로컬 중복 저장소)를 제공합니다. LRS가 탑재된 이 플랫폼은 업계 최고의 0% 연간 실패율로 IaaS 디스크에 엔터프라이즈급 내구성을 일관되게 제공하고 있습니다.

**스냅숏**: 모든 Managed Disks와 마찬가지로 표준 SSD도 스냅숏 만들기를 지원합니다. 스냅숏 유형은 표준(HDD) 또는 프리미엄(SSD)일 수 있습니다. 비용 절감을 위해 모든 Azure 디스크 유형에 표준(HDD) 스냅숏 유형을 권장합니다. 스냅숏으로 관리 디스크를 만들 때는 항상 표준 SSD 또는 프리미엄 SSD와 같은 상위 계층을 선택할 수 있기 때문입니다.

## <a name="scalability-and-performance-targets"></a>확장성 및 성능 대상

다음 표에는 현재 표준 SSD에 제공되는 디스크 크기가 나와 있습니다. 별표로 표시되는 크기는 현재 미리 보기로 제공됩니다.

|표준 SSD 디스크 유형  |디스크 크기  |디스크당 IOPS  |디스크당 처리량  |
|---------|---------|---------|---------|
|E4     |32GiB         |최대 120         |초당 최대 25MiB         |
|E6     |64GiB         |최대 240         |초당 최대 50MiB         |
|E10     |128GiB         |최대 500         |초당 최대 60MiB         |
|E15     |256GiB         |최대 500         |초당 최대 60MiB         |
|E20     |512GiB         |최대 500         |초당 최대 60MiB         |
|E30     |1,024GiB       |최대 500         |초당 최대 60MiB         |
|E40     |2,048GiB       |최대 500         |초당 최대 60MiB         |
|E50     |4,095GiB       |최대 500         |초당 최대 60MiB         |
|E60 *     |8,192GiB       |최대 1,300       |초당 최대 300MiB        |
|E70 *    |16,384GiB      |최대 2,000       |초당 최대 500MiB        |
|E80 *    |32,767GiB      |최대 2,000       |초당 최대 500MiB        |

표준 SSD는 대부분의 IO 작업에 수 밀리초의 대기 시간을 제공하고, 대부분 위의 표에 나와 있는 99% 시간 한도까지 IOPS와 처리량을 제공하도록 설계되었습니다. 트래픽 패턴에 따라 실제 IOPS 및 처리량은 달라질 수 있습니다. 표준 SSD는 HDD 디스크보다 더 일관적인 성능과 더 짧은 대기 시간을 제공합니다.

반면, 프리미엄 SSD는 표준 SSD보다 성능이 뛰어나며, 대기 시간이 더 짧고, IOPS/처리량이 높으며, 프로비전된 디스크 성능과 훨씬 더 높은 일관성을 지원합니다. 프리미엄 SSD는 중요한 프로덕션 워크로드에 권장되는 디스크 형식입니다. 워크로드에 대기 시간이 짧은 고성능 디스크 지원이 필요한 경우 Premium Storage를 사용하는 것이 좋습니다.

프리미엄 SSD와 마찬가지로 표준 SSD도 256KiB의 IO 단위 크기를 사용합니다. 전송되는 데이터가 256KiB 미만일 경우 하나의 I/O 단위로 간주됩니다. 더 큰 I/O 크기는 256KiB 크기의 여러 I/O로 계산됩니다. 예를 들어, 1,100KiB I/O는 I/O 단위 5개로 계산됩니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

표준 SSD를 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.

- Managed Disks 크기
- 스냅숏
- 아웃바운드 데이터 전송
- 트랜잭션

**Managed Disks 크기**: Managed Disks의 요금은 프로비전되는 크기에 따라 청구됩니다. Azure는 프로비전된 크기(반올림됨)를 가장 가까운 디스크 크기 옵션에 매핑합니다. 제공되는 디스크 크기에 대한 자세한 내용은 위의 확장성 및 성능 목표 섹션에 있는 표를 참조하세요. 각각의 디스크는 지원되는 프로비전된 디스크 크기에 매핑되고 그에 따라 요금이 청구됩니다. 예를 들어 200GiB 표준 SSD를 프로비전한 경우 E15(256GiB)의 디스크 크기 옵션에 매핑됩니다. 프로비전된 디스크에 대한 청구는 Premium Storage 제품의 월별 가격을 사용하여 시간당 비례합니다. 예를 들어 E10 디스크를 프로비전하고 20시간 후 삭제한 경우 20시간에 비례하여 E10 제품에 대해 청구됩니다. 이는 디스크에 기록되는 실제 데이터 양에 관계없이 적용됩니다.

**스냅숏**: Managed Disks 스냅숏의 요금은 대상과 원본(있는 경우)의 스냅숏에서 사용하는 용량에 따라 청구됩니다. 스냅숏에 대한 자세한 내용은 [Managed Disks 스냅숏](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#managed-disk-snapshots)을 참조하세요.

**아웃바운드 데이터 전송**: [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용에 대해 청구가 발생합니다.

**트랜잭션**: 표준 HDD와 마찬가지로, 표준 SSD의 트랜잭션에도 요금이 청구됩니다. 트랜잭션에는 디스크에 대한 읽기 및 쓰기 작업이 모두 포함됩니다. 표준 SSD의 트랜잭션을 계산하는 데 사용되는 I/O 단위 크기는 256KiB입니다. 더 큰 I/O 크기는 256KiB 크기의 여러 I/O로 계산됩니다.

Virtual Machines 및 Managed Disks 가격 책정에 대한 자세한 내용은 다음을 참조하세요.

- [Virtual Machines 가격](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="next-steps"></a>다음 단계

표준 SSD를 만드는 방법에 대해 자세히 알아보려면 여러 표준 SSD로 VM을 만드는 방법을 보여주는 샘플을 참조하세요.

> [!div class="nextstepaction"]
> [여러 표준 SSD를 사용하여 VM 만들기](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)
