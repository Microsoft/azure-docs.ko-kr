---
title: Dv3 및 Dsv3 시리즈
description: Dv3 및 Dsv3 시리즈 Vm에 대 한 사양입니다.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 7d6f9d69f04444c01f35437e5300c3033daed5ea
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648448"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 및 Dsv3 시리즈

Dv3 시리즈는 Intel® Xeon® 플래티넘 827272CL (Cascade Lake)에서 실행 됩니다. Intel® Xeon® 8171M (Skylake), Intel® Xeon® E5-2673 v4 2.3 g h z (Broadwell) 또는 하이퍼 스레드 구성의 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 프로세서를 제공 하므로 대부분의 범용 워크 로드에 대해 더 나은 가치를 제공 합니다. 하이퍼 스레딩으로 이동하기 위해 디스크 및 네트워크 제한이 코어 단위로 조정되는 동안 메모리가 확장되었습니다(~ 3.5GiB/vCPU에서 4GiB/vCPU로). Dv3 시리즈는 더 이상 D/Dv2 시리즈의 높은 메모리 VM 크기를 갖지 않으며, 메모리 최적화 [Ev3 및 Esv3 시리즈](ev3-esv3-series.md)로 이동되었습니다.

예제 D 시리즈 사용 사례에는 엔터프라이즈급 응용 프로그램, 관계형 데이터베이스, 메모리 내 캐싱 및 분석이 포함 됩니다.

## <a name="dv3-series"></a>Dv3 시리즈

Dv3 시리즈 크기는 Intel® Xeon® 플래티넘 8272CL (케스케이드 Lake), Intel® Xeon® 8171M 2.1 g h z (Skylake), Intel® Xeon® E5-2673 v4 2.3 g h z (Broadwell) 또는 intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 프로세서에서 실행 되는 Intel 터보 부스트 기술 2.0입니다. Dv3 시리즈 크기는 대부분의 프로덕션 워크로드에 적합한 vCPU, 메모리 및 임시 스토리지의 조합을 제공합니다.

데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. Premium Storage 디스크를 사용하려면 Dsv3 크기를 사용합니다. Dsv3 크기의 가격 및 요금 청구 기준은 Dv3 시리즈와 동일합니다.

Dv3 시리즈 Vm은 Intel® 하이퍼 스레딩 기술을 기능 합니다.

ACU: 160-190

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 저장소 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 NIC 수|네트워크 대역폭 |
|---|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2|1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2|2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4|4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8|8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8|16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8|24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8|30000 |

## <a name="dsv3-series"></a>Dsv3 시리즈

Dsv3 시리즈 크기는 intel® Xeon® 플래티넘 8272CL (케스케이드 Lake), Intel® Xeon® 8171M 2.1 g h z (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 또는 intel 터보 부스트 기술 2.0을 사용 하는 intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 프로세서에서 실행 되며 premium storage를 사용 합니다. Dsv3 시리즈 크기는 대부분의 프로덕션 워크로드에 적합한 vCPU, 메모리 및 임시 스토리지의 조합을 제공합니다.

Dsv3 시리즈 Vm은 Intel® 하이퍼 스레딩 기술을 기능 합니다.

ACU: 160-190

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps (GiB의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2|1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2|2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4|4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8|8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8|24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |

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