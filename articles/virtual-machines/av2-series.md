---
title: Av2 시리즈
description: Av2 시리즈 Vm에 대 한 사양입니다.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: ac3f31fecd53ddf1dd8bb81229c67cb4bb60c34e
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650641"
---
# <a name="av2-series"></a>Av2 시리즈

다양한 하드웨어 유형 및 프로세서에 Av2 시리즈 VM을 배포할 수 있습니다. Av2 시리즈 Vm에는 개발 및 테스트와 같은 항목 수준 작업에 가장 적합 한 CPU 성능 및 메모리 구성이 있습니다. 크기는 배포 된 하드웨어에 관계 없이 실행 중인 인스턴스에 대 한 일관 된 프로세서 성능을 제공 하도록 제한 됩니다. 이 크기가 배포되는 실제 하드웨어를 확인하려면 Virtual Machine 내에서 가상 하드웨어를 쿼리합니다. 사용 사례에는 개발 및 테스트 서버, 트래픽이 적은 웹 서버, 중소 규모의 데이터베이스, 개념 증명 및 코드 리포지토리가 있습니다.

ACU: 100

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨


| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 저장소 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 | 예상 네트워크 대역폭 (Mbps)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |

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