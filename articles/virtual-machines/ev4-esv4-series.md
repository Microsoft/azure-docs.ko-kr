---
title: Ev4 및 Esv4 시리즈-Azure Virtual Machines
description: Ev4 및 Esv4 시리즈 Vm에 대 한 사양입니다.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: b8a6e1013c131778bacb11bd885b9280fb6c6077
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182176"
---
# <a name="ev4-and-esv4-series"></a>Ev4 및 Esv4 시리즈

Ev4 및 Esv4 시리즈는 &reg; &reg; 하이퍼 스레드 구성의 Intel Xeon Platinum 8272CL Cl (캐스케이드 Lake) 프로세서에서 실행 되며, 다양 한 메모리 집약적 엔터프라이즈 응용 프로그램 및 RAM의 최대 504GiB 기능에 적합 합니다. 3.4 GHz의 모든 코어 터보 클록 속도를 제공 합니다.

> [!NOTE]
> 질문과 대답은  [로컬 임시 디스크가 없는 AZURE VM 크기](azure-vms-no-temp-disk.md)를 참조 하세요.

## <a name="ev4-series"></a>Ev4 시리즈

Ev4 시리즈 크기는 Intel Xeon &reg; Platinum 8272CL (캐스케이드 Lake)에서 실행 됩니다. Ev4 시리즈 인스턴스는 메모리를 많이 사용 하는 엔터프라이즈 응용 프로그램에 적합 합니다. Ev4 시리즈 Vm은 Intel &reg; 하이퍼 스레딩 기술을 특징으로 합니다.

원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. Premium storage 디스크를 사용 하려면 Esv4 크기를 사용 합니다. Esv4 크기에 대 한 가격 책정 및 요금 청구 기준은 Ev4 시리즈와 동일 합니다.

ACU: 195 - 210

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | 원격 저장소만 | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | 원격 저장소만 | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | 원격 저장소만 | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | 원격 저장소만 | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | 원격 저장소만 | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | 원격 저장소만 | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | 원격 저장소만 | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | 원격 저장소만 | 32| 8|30000 |


## <a name="esv4-series"></a>Esv4 시리즈

Esv4 시리즈 크기는 Intel &reg; Xeon &reg; Platinum 8272CL (캐스케이드 Lake)에서 실행 됩니다. Esv4 시리즈 인스턴스는 메모리를 많이 사용 하는 엔터프라이즈 응용 프로그램에 적합 합니다. Evs4 시리즈 Vm은 Intel &reg; 하이퍼 스레딩 기술을 특징으로 합니다. 원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다.

ACU: 195-210

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | 원격 저장소만 | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | 원격 저장소만 | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | 원격 저장소만 | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | 원격 저장소만 | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | 원격 저장소만 | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | 원격 저장소만 | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | 원격 저장소만 | 32 | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| 원격 저장소만 | 32 | 80000/1200 | 8|30000 |

<sup>1</sup> [제한 된 코어 크기를 사용할 수](./constrained-vcpu.md)있습니다.

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
