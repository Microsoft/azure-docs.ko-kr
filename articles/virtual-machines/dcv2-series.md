---
title: DC 시리즈 - Azure Virtual Machines
description: DC 시리즈 VM의 사양입니다.
author: susaxen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 41dbb42637db98dcd97e3487f252e88f2ba5f9a3
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649893"
---
# <a name="dcsv2-series"></a>DCsv2 시리즈


DCsv2 시리즈는 퍼블릭 클라우드에서 처리되는 동안 데이터 및 코드의 기밀성 및 무결성을 보호할 수 있습니다. 이러한 머신은 SGX 기술이 적용된 최신 세대의 Intel XEON E-2288G 프로세서로 지원됩니다. Intel Turbo Boost 기술을 사용하면 이 머신은 최대 5.0GHz까지 작동할 수 있습니다. DCsv2 시리즈 인스턴스를 사용하면 고객은 사용 중인 코드와 데이터를 보호할 수 있는 보안 Enclave 기반 애플리케이션을 빌드할 수 있습니다.

사용 사례로는 기밀 단체 데이터 공유, 사기 감지, 자금 세탁 방지, 블록체인, 비밀 사용 분석, 인텔리전스 분석 및 기밀 기계 학습이 있습니다.

Premium Storage: 지원됨*

Premium Storage 캐싱: 지원됨*

실시간 마이그레이션: 지원되지 않음

메모리 보존 업데이트: 지원되지 않음

*Standard_DC8_v2 제외



| 크기             | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 NIC 수 / 예상 네트워크 대역폭(MBps) | EPC 메모리(MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- DCsv2 시리즈 VM은 [2세대 VM](./linux/generation-2.md#creating-a-generation-2-vm)이며 `Gen2` 이미지만 지원합니다.
- 현재 [여기](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)에 나열 된 지역에서 사용할 수 있습니다.
- 이전 세대의 기밀 컴퓨팅 VM: [DC 시리즈](sizes-previous-gen.md#preview-dc-series)
- [Azure Portal](./linux/quick-create-portal.md) 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)를 사용하여 DCsv2 VM 만들기



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