---
title: Mv2 시리즈-Azure Virtual Machines
description: Mv2 시리즈 Vm에 대 한 사양입니다.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: c91e9832e4b415a59c12b71964a0da580e9fda21
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651134"
---
# <a name="mv2-series"></a>Mv2 시리즈

Mv2 시리즈는 2.5 GHz의 모든 코어 기본 주파수와 3.8 g h z의 최대 터보 주파수를 사용 하 여 하이퍼 스레드 Intel® Xeon® 플래티넘 8180M 2.5 g h z (Skylake) 프로세서에서 실행 되는 높은 처리량, 짧은 대기 시간 플랫폼을 갖추고 있습니다. 모든 Mv2 시리즈 가상 머신 크기는 standard 및 premium 영구 디스크를 모두 사용할 수 있습니다. Mv2 시리즈 인스턴스는 메모리 내 데이터베이스 및 워크 로드를 지원 하기 위해 뛰어난 계산 성능을 제공 하는 메모리 액세스에 최적화 된 VM 크기 이며, 관계형 데이터베이스 서버, 큰 캐시 및 메모리 내 분석에 적합 한 높은 메모리 대 CPU 비율을 제공 합니다.

Mv2 시리즈 VM의 기능 Intel® 하이퍼 스레딩 기술

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

실시간 마이그레이션: 지원되지 않음

메모리 보존 업데이트: 지원되지 않음

쓰기 가속기: [지원 됨](./how-to-enable-write-accelerator.md)

|크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 | 예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 8만/800 (7040) | 4만/1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 8만/800 (7040) | 4만/1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 25만/1600 (14080) | 8만/2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 25만/1600 (14080) | 8만/2000 | 8 | 32000 |

<sup>1</sup> Mv2 시리즈 vm은 2 세대 전용 이며 지원 되는 2 세대 이미지의 하위 집합을 지원 합니다. Mv2 시리즈에 대해 지원 되는 이미지의 전체 목록에 대해서는 아래를 참조 하세요. Linux를 사용 하는 경우 이미지를 찾고 선택 하는 방법에 대 한 지침은 [Azure의 2 세대 vm에 대 한 지원](./linux/generation-2.md) 을 참조 하세요. Windows를 사용 하는 경우 이미지를 찾고 선택 하는 방법에 대 한 지침은 [Azure의 2 세대 vm에 대 한 지원](./windows/generation-2.md) 을 참조 하세요. 

- Windows Server 2019 이상
- SUSE Linux Enterprise Server 12 SP4 이상 또는 SUSE Linux Enterprise Server 15 SP1 이상
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 이상 
- Oracle Enterprise Linux 7.7 이상



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>기타 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

디스크 유형에 대 한 자세한 정보: [디스크 유형](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.