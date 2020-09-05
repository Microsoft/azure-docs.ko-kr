---
title: Fsv2 시리즈
description: Fsv2 시리즈 Vm에 대 한 사양입니다.
author: brbell
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 50653b0da2bb488f2a0d4254df2e27ba7c6a5f1f
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648312"
---
# <a name="fsv2-series"></a>Fsv2 시리즈

Fsv2 시리즈는 Intel® Xeon® 플래티넘 8272CL (케스케이드 Lake) 프로세서 및 Intel® Xeon® Platinum 8168 (Skylake) 프로세서에서 실행 됩니다. 3.4 g h z의 모든 코어 터보 클록 속도와 3.7 g h z의 최대 싱글 코어 터보 주파수를 제공 합니다. Intel® AVX-512 지침은 Intel 확장 가능한 프로세서에 새로 있습니다. 이러한 지침은 단일 및 배정밀도 부동 소수점 연산에 대 한 벡터 처리 워크 로드에 대해 최대 2 배의 성능 향상을 제공 합니다. 즉, 계산 워크 로드에 대해 매우 빠릅니다.

Fsv2 시리즈 Vm은 Intel® 하이퍼 스레딩 기술을 기능 합니다.

ACU: 195 - 210

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU 수 | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps (GiB의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2|875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2|1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4|3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4|7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8|14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8|28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8|30000 |

<sup>1</sup> 64 개 이상의 vcpu를 사용 하려면 다음의 지원 되는 게스트 운영 체제 중 하나가 필요 합니다.

- Windows Server 2016 이상
- Ubuntu 16.04 LTS 이상, Azure에서 조정 된 커널 (4.15 kernel 이상)
- SLES 12 SP2 이상
- Microsoft에서 제공 하는 LIS package 4.3.1 이상이 설치 된 RHEL 또는 CentOS 버전 6.7 ~ 6.10
- RHEL 또는 CentOS 버전 7.3, Microsoft에서 제공 하는 LIS 패키지 4.2.1 이상 설치 됨
- RHEL 또는 CentOS 버전 7.6 이상
- UEK4 이상을 사용 하 여 Oracle Linux
- Debian 9 (backports 커널, Debian 10 이상)
- 4.14 커널을 CoreOS 이상

<sup>2</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

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