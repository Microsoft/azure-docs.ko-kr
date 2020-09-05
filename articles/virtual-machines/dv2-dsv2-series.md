---
title: Dv2 및 Dsv2 시리즈-Azure Virtual Machines
description: Dv2 및 Dsv2 시리즈 Vm에 대 한 사양입니다.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: ede5b84620217e472d930d86eaebfaad2397ca6b
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648431"
---
# <a name="dv2-and-dsv2-series"></a>Dv2 및 DSv2 시리즈

원본 D 시리즈에 대 한 후속 작업 인 Dv2 및 Dsv2 시리즈는 대부분의 프로덕션 워크 로드에 적합 한 강력한 CPU 및 최적의 CPU 메모리 구성을 기능 합니다. Dv2 시리즈는 D 시리즈보다 약 35% 빠릅니다. Dv2 시리즈는 intel® Xeon® 플래티넘 8272CL (케스케이드 Lake), Intel® Xeon® 8171M 2.1 g h z (Skylake), Intel® Xeon® E5-2673 v4 2.3 g h z (Broadwell) 또는 intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 프로세서에서 실행 되며 Intel 터보 부스트 기술 2.0입니다. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.

## <a name="dv2-series"></a>Dv2 시리즈

Dv2 시리즈 크기는 intel® Xeon® 플래티넘 8272CL (케스케이드 Lake), Intel® Xeon® 8171M 2.1 g h z (Skylake) 또는 intel® xeon® E5-2673 v4 2.3 g h z (Broadwell) 또는 intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 프로세서와 Intel 터보 부스트 기술 2.0에 실행 됩니다.

ACU: 210-250

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 저장소 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크 수 | 처리량: IOPS | 최대 NIC 수 | 예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3.5 | 50  | 3000/46/23    | 4  | 4x500  | 2|750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8x500  | 2|1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16x500 | 4|3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32x500 | 8|6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8|12000 |

## <a name="dsv2-series"></a>DSv2 시리즈

DSv2 시리즈 크기는 intel® Xeon® 플래티넘 8272CL (케스케이드 Lake), Intel® Xeon® 8171M 2.1 g h z (Skylake) 또는 intel® xeon® E5-2673 v4 2.3 g h z (Broadwell) 또는 intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 2.0 프로세서에서 실행 되며, 프리미엄 저장소를 사용 합니다.

ACU: 210-250

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps (GiB의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3.5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2|750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2|1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4|3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8|6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8|12000 |

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