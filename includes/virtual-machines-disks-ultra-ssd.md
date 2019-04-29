---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4b5d2de2e9ccd44517e083a435e127bd5678f002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564343"
---
# <a name="ultra-disks-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Ultra (미리 보기) managed disks에 대 한 Azure virtual machine 워크 로드

Azure ultra 디스크 (미리 보기)는 Azure IaaS Vm에 대 한 높은 처리량, 높은 IOPS 및 일관 된 짧은 대기 시간 디스크 저장소를 제공 합니다. 이 새 제품은 Microsoft의 기존 디스크 제품과 동일한 가용성 수준에서 최상의 성능을 제공합니다. Ultra 디스크의 추가 혜택에는 가상 컴퓨터를 다시 시작 하지 않고 워크 로드와 함께 디스크의 성능을 동적으로 변경 하는 기능 포함 됩니다. Ultra 디스크는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 작업이 많은 워크 로드와 같은 데이터 집약적인 워크 로드에 적합 합니다.

## <a name="ultra-disk-features"></a>Ultra 디스크 기능

**‘관리 디스크**: Ultra 디스크만 관리 되는 디스크로 사용할 수 있습니다. Ultra 디스크 관리 되지 않는 디스크 또는 페이지 blob으로 배포할 수 없습니다. 관리 디스크를 만드는 동안 디스크 sku UltraSSD_LRS로 입력 하 고 IOPS, 디스크의 크기를 나타낼 만들고 필요한 처리량 및 Azure 디스크 관리를 지정 합니다.  

**Virtual Machines**: Ultra 디스크는 설정 된 Azure 가상 머신 Sku; 모든 프리미엄 SSD 사용 하 여 작동 하도록 설계 그러나 미리 보기에서 현재 이므로 Vm 크기가 조정 됩니다 ES/DS v3로.

**동적 성능 구성**: Ultra 디스크를 사용 하면 가상 컴퓨터를 다시 시작 하지 않고 워크 로드 요구 사항을 함께 디스크의 성능 (IOPS 및 처리량)를 동적으로 변경할 수 있습니다.

## <a name="scalability-and-performance-targets"></a>확장성 및 성능 대상

Ultra는 디스크를 프로 비전 하는 경우에 독립적으로 구성할 용량 및 디스크의 성능 옵션을 해야 합니다. Ultra 디스크 최대 64tib 4 GiB에서 몇 가지 고정 된 크기로 제공 되며 독립적으로 구성할 IOPS 및 처리량 할 수 있는 유연한 성능 구성 모델 기능. Ultra 디스크 데이터 디스크로 활용할 수 있습니다. OS 디스크로 프리미엄 SSD를 사용하는 것이 좋습니다.

일부 키 ultra 디스크의 기능은 다음과 같습니다.

- 디스크 용량: Ultra 디스크 최대 64tib 4 GiB에서 다른 디스크 크기의 범위를 제공 합니다.
- 디스크 IOPS: Ultra 디스크는 300 IOPS/GiB, 160 20,000iops의 디스크당 최대 IOPS 제한을 지원합니다. 프로비전한 IOPS를 달성하려면 선택한 디스크 IOPS가 VM IOPS보다 작은지 확인합니다. 최소 디스크 IOPS는 100IOPS입니다.
- 디스크 처리량: Ultra 디스크를 사용 하 여 단일 디스크의 처리량 한도 256 3:n2}kib/의 2000 MBps 디스크당 최대 IOPS를 프로 비전 된 각 (여기서 MBps = 10 ^6 바이트 수 / 초)입니다. 최소 디스크 처리량은 1MiB입니다.

다음 표에는 다양한 디스크 크기에 지원되는 다양한 구성이 개략적으로 나와 있습니다.  

### <a name="ultra-disks-managed-disk-offerings"></a>Ultra managed 디스크 제품

|디스크 크기(GiB)  |IOPS 용량  |처리량 용량(MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024-65,536(1TiB의 단위로 증가하는 이 범위의 크기)     |160,000         |2,000         |

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Ultra 디스크를 사용 하는 경우 다음과 같은 청구 고려 사항이 적용 됩니다.

- Managed Disks 크기
- Managed Disks 프로비전된 IOPS
- Managed Disks 프로비전된 처리량
- Ultra 디스크 VM 예약 요금

### <a name="managed-disk-size"></a>관리 되는 디스크 크기

Managed Disks는 새 Azure VM을 프로비전하는 동안 선택한 VM 크기에 따라 요금이 청구됩니다. Azure는 프로비전된 크기(반올림됨)를 가장 가까운 디스크 크기 옵션에 매핑합니다. 제공되는 디스크 크기에 대한 자세한 내용은 위의 확장성 및 성능 목표 섹션에 있는 표를 참조하세요. 각각의 디스크는 지원되는 프로비전된 디스크 크기에 매핑되고 시간 단위로 적절하게 요금이 청구됩니다. 예를 들어 200 GiB ultra 디스크를 프로 비전 하 고 20 시간 후 삭제 하는 경우 256 GiB의 디스크 크기 제품에 매핑되는 및 청구 됩니다 256 GiB에 대 한 20 시간에 대 한 합니다. 이러한 청구는 디스크에 실제로 기록된 데이터의 볼륨과 관계없이 사용된 계산 시간을 기반으로 한 것입니다.

### <a name="managed-disk-provisioned-iops"></a>관리 디스크를 프로 비전 된 IOPS

IOPS는 애플리케이션이 1초 동안 디스크로 보내는 요청의 수입니다. 입출력 작업은 순차 읽기 또는 쓰기나 임의 읽기 또는 쓰기가 될 수 있습니다. VM에 연결된 디스크 수 또는 디스크 크기를 기반으로 시간별 평균 IOPS 수를 기준으로 요금이 청구됩니다. 제공되는 디스크 IOPS에 대한 자세한 내용은 위의 확장성 및 성능 목표 섹션에 있는 표를 참조하세요.

### <a name="managed-disk-provisioned-throughput"></a>관리 디스크 프로 비전 된 처리량

처리량은 애플리케이션이 바이트/초로 측정된 지정된 간격의 디스크에 보내는 데이터의 양입니다. 애플리케이션이 대규모 입력/출력 작업을 수행하는 경우 높은 처리량이 필요합니다.  

처리량과 IOPS 간에는 다음 수식에 표시된 관계가 있습니다.  IOPS x IO 크기 = 처리량

따라서 애플리케이션에 필요한 최적의 처리량 및 IOPS 값을 결정하는 것이 중요합니다. 하나를 최적화하려고 할 때 다른 하나도 영향을 받습니다. 16KiB IO 크기에 해당하는 처리량으로 시작하고, 처리량이 더 필요한 경우 조정하는 것이 좋습니다.

Ultra 디스크에 지원 되는 디스크 처리량에 대 한 자세한 내용은 위의 확장성 및 성능 목표 섹션의 표를 참조 합니다. 디스크 크기 및 IOPS와 마찬가지로 프로비전된 처리량은 프로비전된 MBps당 시간 단위로 요금이 청구됩니다.

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra 디스크 VM 예약 요금

VM 디스크가 ultra 호환 여부를 나타내는 VM에서 기능을 도입 됩니다. 울트라 디스크 호환 VM은 컴퓨팅 VM 인스턴스와 블록 스토리지 배율 단위 간에 전용 대역폭 용량을 할당하여 성능을 최적화하고 대기 시간을 줄입니다. VM에 이 기능을 추가하면 울트라 디스크를 연결하지 않고 VM에서 울트라 디스크 기능을 활성화한 경우에만 적용되는 예약 요금이 발생합니다. Ultra 디스크를 ultra 호환 VM에 연결 되 면 대금이 청구는 적용할 수 없습니다. 이 요금은 VM에서 프로비전되는 vCPU당 부과됩니다.

참조를 [Azure Disks 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/) 새 ultra 디스크 가격 세부 정보에 제한 된 미리 보기에서 사용할 수 있습니다.

### <a name="ultra-disk-preview-scope-and-limitations"></a>Ultra 디스크 미리 보기 범위 및 제한 사항

미리 보기에서 ultra 디스크:

- 단일 가용성 영역의 미국 동부 2에서 처음에 지원될 예정  
- 가용성 영역 (가용성 집합 및 울트라 디스크를 연결할 수 없는 영역은 외부에서 단일 VM 배포)를 사용 하 여 에서만 사용할 수 있습니다.
- ES/DS v3 VM에서만 지원됨
- 데이터 디스크로만 사용 가능하며 4k 물리적 섹터 크기만 지원함  
- 빈 디스크로만 만들 수 있음  
- 현재 Resource Manager 템플릿, CLI 및 Python SDK만을 사용하여 배포할 수 있습니다.
- 디스크 스냅숏, VM 이미지, 가용성 집합, Virtual Machine Scale Sets 및 Azure Disk Encryption을 (아직) 지원하지 않습니다.
- Azure Backup 또는 Azure Site Recovery와의 통합을 (아직) 지원하지 않습니다.
-  [대부분의 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)와 마찬가지로, 이 기능은 GA(일반 공금) 전에는 프로덕션 워크로드에 사용할 수 없습니다.
