---
title: 메모리 액세스에 최적화 된 Dv2 및 DSv2 시리즈 Vm-Azure Virtual Machines
description: Dv2 및 DSv2 시리즈 Vm에 대 한 사양입니다.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: aee88420fe8b88de9af9a2397316dd065144e610
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685292"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>메모리 액세스에 최적화 된 Dv2 및 Dsv2 시리즈

Dv2 및 Dsv2 시리즈는 원본 D 시리즈에 대 한 후속 기능으로, 더 강력한 CPU를 제공 합니다. DSv2 시리즈 크기는 Intel® Xeon® 플래티넘 8272CL (케스케이드 Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake) 또는 Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 또는 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 프로세서에서 실행 됩니다. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.

## <a name="dv2-series-11-15"></a>Dv2 시리즈 11-15

Dv2 시리즈 크기는 Intel® Xeon® 플래티넘 8272CL (케스케이드 Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake) 또는 Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 또는 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 프로세서에서 실행 됩니다.

ACU: 210-250

Premium Storage: 지원 되지 않음

Premium Storage 캐싱: 지원 되지 않음

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 저장소 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2|1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4|3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8|6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8|12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8|25000 <sup>2</sup> |

<sup>1</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.
<sup>2</sup> 가속 네트워킹 사용 시 25,000Mbps

## <a name="dsv2-series-11-15"></a>DSv2 시리즈 11-15

DSv2 시리즈 크기는 Intel® Xeon® 플래티넘 8272CL (케스케이드 Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake) 또는 Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 또는 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 프로세서에서 실행 됩니다.

ACU: 210 - 250 <sup>1</sup>

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps (GiB의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |---|
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2|1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4|3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8|6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8|12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8|25000 <sup>4</sup> |

<sup>1</sup> DSv2 시리즈 VM에서 제공 가능한 최대 디스크 처리량(IOPS 또는 MBps)은 연결된 디스크의 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다.  자세한 내용은 [고성능을 위한 디자인](./premium-storage-performance.md)을 참조하세요.
<sup>2</sup>  인스턴스는 Intel haswell 기반 하드웨어로 격리 되며 단일 고객 전용입니다.  
<sup>3</sup> 제한 된 코어 크기를 사용할 수 있습니다.  
<sup>4</sup> 가속 네트워킹 사용 시 25,000Mbps

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