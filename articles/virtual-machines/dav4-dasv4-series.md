---
title: Dav4 및 Dasv4 시리즈
description: Dav4 및 Dasv4 시리즈 Vm에 대 한 사양입니다.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 2548f002b2006b5d241c323ae880704bbe95301e
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649927"
---
# <a name="dav4-and-dasv4-series"></a>Dav4 및 Dasv4 시리즈

Dav4 시리즈 및 Dasv4 시리즈는 전체 스레드 구성에서 AMD의 2.35 Ghz EPYC<sup>TM</sup> 7452 프로세서 256를 활용 하는 새로운 크기로, 범용 워크 로드를 실행 하는 데 필요한 고객 옵션을 8 개 코어 마다 늘립니다. Dav4 시리즈와 Dasv4 시리즈는 D 및 Dsv3 시리즈와 동일한 메모리 및 디스크 구성을 가집니다.

## <a name="dav4-series"></a>Dav4 시리즈

ACU: 230-260

Premium Storage: 지원 되지 않음

Premium Storage 캐싱: 지원 되지 않음

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

Dav4 시리즈 크기는 3.35 GHz의 승격 된 최대 빈도를 달성할 수 있는 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 프로세서를 기반으로 합니다. Dav4 시리즈 크기는 대부분의 프로덕션 워크 로드에 대 한 vCPU, 메모리 및 임시 저장소의 조합을 제공 합니다. 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. 프리미엄 SSD를 사용 하려면 Dasv4 크기를 사용 합니다. Dasv4 크기에 대 한 가격 책정 및 요금 청구 기준은 Dav4 시리즈와 동일 합니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 NIC 수 | 예상 네트워크 대역폭 (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 | 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 | 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 | 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 | 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 | 16000 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000/1000/500 | 8 | 24000 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000/1000/500 | 8 | 30000 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000/1000/500 | 8 | 30000 |

## <a name="dasv4-series"></a>Dasv4 시리즈

ACU: 230-260

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

Dasv4 시리즈 크기는 3.35 GHz의 승격 된 최대 주파수를 실현 하 고 프리미엄 SSD를 사용할 수 있는 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 프로세서를 기반으로 합니다. Dasv4 시리즈 크기는 대부분의 프로덕션 워크 로드에 대 한 vCPU, 메모리 및 임시 저장소의 조합을 제공 합니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 | 예상 네트워크 대역폭 (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 (50)|3200/48|2 | 1000 |
| Standard_D4as_v4|4|16|32|8|8000/64 (100)|6400/96|2 | 2000 |
| Standard_D8as_v4|8|32|64|16|16000/128 (200)|12800/192|4 | 4000 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600/384|8 | 8000 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200/768|8 | 16000 |
| Standard_D48as_v4|48|192|384|32|96000/1020 (1200)|76800/1148|8 | 24000 |
| Standard_D64as_v4|64|256|512|32|128000/1020 (1600)|8만/1200|8 | 30000 | 
| Standard_D96as_v4|96|384|768|32|192000/1020 (2400)|8만/1200|8 | 30000 |

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