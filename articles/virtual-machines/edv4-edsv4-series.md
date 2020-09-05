---
title: Edv4 및 Edsv4 시리즈
description: Ev4, Edv4, Esv4 및 Edsv4 시리즈 VM의 사양.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 15a53ddaa669f0720632901edfd58eb273518867
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049624"
---
# <a name="edv4-and-edsv4-series"></a>Edv4 및 Edsv4 시리즈

Edv4 및 Edsv4 시리즈는 하이퍼 스레드 구성에서 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 프로세서로 실행되고 다양한 메모리 집약적 엔터프라이즈 애플리케이션에 이상적입니다. RAM 504GiB, [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 및 [Intel&reg; Advanced Vector Extensions 512(Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)가 제공됩니다. 또한 [Intel &reg; 심층 학습 부스트](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)를 지원 합니다. 이러한 새 VM 크기는 50% 더 큰 로컬 저장소 뿐만 아니라 [Gen2 vm](./linux/generation-2.md)의 [Ev3/Esv3](./ev3-esv3-series.md) 크기와 비교 하 여 읽기 및 쓰기 둘 다에 대해 더 나은 로컬 디스크 IOPS를 갖게 됩니다. 3.4 GHz의 모든 코어 터보 클록 속도를 제공 합니다. 

## <a name="edv4-series"></a>Edv4 시리즈

Edv4 시리즈 크기는 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 프로세서에서 실행됩니다. Edv4 가상 머신 크기는 대용량 고속 로컬 SSD 스토리지(최대 2,400GiB) 외에 RAM 504GiB가 탑재됩니다. 이 가상 머신은 메모리 집약적인 엔터프라이즈 애플리케이션과 지연이 낮은 고속 로컬 스토리지가 필요한 애플리케이션에 이상적입니다. 표준 SSD 및 표준 HDD 디스크 스토리지를 Edv4 VM에 연결할 수 있습니다. 

ACU: 195 - 210

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30000 |


## <a name="edsv4-series"></a>Edsv4 시리즈

Edsv4 시리즈 크기는 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 프로세서에서 실행됩니다. Edsv4 가상 머신 크기는 대용량 고속 로컬 SSD 스토리지(최대 2,400GiB) 외에 RAM 504GiB가 탑재됩니다. 이 가상 머신은 메모리 집약적인 엔터프라이즈 애플리케이션과 지연이 낮은 고속 로컬 스토리지가 필요한 애플리케이션에 이상적입니다.

ACU: 195-210

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps (GiB의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120(50) | 3200/48 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242(100) | 6400/96 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485(200) | 12800/192 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968(400) | 25600/384 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211(500)  | 32000/480  | 8|10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936(800) | 51200/768  | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8|24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8|30000 |

<sup>1</sup> [사용 가능한 코어 크기 제한)](./constrained-vcpu.md).


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
