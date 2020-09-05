---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c0627dd0833e3b20468eb5f50fbeb9fd9d9ae2b3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864882"
---
**아웃바운드 데이터 전송**: [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용량에 대해 청구가 발생합니다.

**트랜잭션**: 표준 관리 디스크에서 수행 하는 트랜잭션 수에 대 한 요금이 청구 됩니다. 표준 Ssd의 경우 256 KiB 처리량 보다 작거나 같은 각 i/o 작업은 단일 i/o 작업으로 간주 됩니다. KiB의 처리량이 256 보다 큰 i/o 작업은 크기 256 KiB의 여러 i/o로 간주 됩니다. 표준 Hdd 경우 각 IO 작업은 i/o 크기에 관계 없이 단일 트랜잭션으로 간주 됩니다.

트랜잭션 비용을 포함 하 여 Managed Disks에 대 한 가격 책정에 대 한 자세한 내용은 [가격 책정 Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks)를 참조 하세요.

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra disk VM 예약 요금

Azure Vm은 ultra disks와 호환 되는지 여부를 나타내는 기능을 포함 합니다. 울트라 디스크 호환 VM은 컴퓨팅 VM 인스턴스와 블록 스토리지 배율 단위 간에 전용 대역폭 용량을 할당하여 성능을 최적화하고 대기 시간을 줄입니다. VM에 이 기능을 추가하면 울트라 디스크를 연결하지 않고 VM에서 울트라 디스크 기능을 활성화한 경우에만 적용되는 예약 요금이 발생합니다. 울트라 디스크 호환 VM에 울트라 디스크가 연결되면 이 요금이 적용되지 않습니다. 이 요금은 VM에서 프로비전되는 vCPU당 부과됩니다. 

> [!Note]
> [제약 된 코어 VM 크기](../articles/virtual-machines/constrained-vcpu.md)의 경우 예약 요금은 제약 된 코어가 아닌 실제 vcpus 수를 기반으로 합니다. Standard_E32 8s_v3의 경우 예약 요금은 32 코어를 기준으로 합니다. 

울트라 디스크 가격 책정에 대 한 자세한 내용은 [Azure 디스크 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/) 를 참조 하세요.

### <a name="azure-disk-reservation"></a>Azure 디스크 예약

디스크 예약은 할인에 따라 1 년간의 디스크 저장소를 구매 하 여 총 비용을 절감할 수 있는 옵션입니다. 디스크 예약을 구입 하는 경우 1 년 동안 미국 동부 2 지역의 10 P30 (1TiB) premium Ssd와 같은 대상 지역에서 특정 디스크 SKU를 선택 합니다. 예약 환경은 예약 된 VM (가상 머신) 인스턴스와 유사 합니다. VM 및 디스크 예약을 번들로 묶어 절감 액을 최대화할 수 있습니다. 현재 Azure 디스크 예약은 모든 프로덕션 지역의 P30 (1TiB)에서 P80 (32 TiB)까지 프리미엄 SSD Sku에 대 한 1 년 약정 계획을 제공 합니다. 예약 된 디스크 가격 책정에 대 한 자세한 내용은 [Azure 디스크 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조 하세요.