---
title: AWS 및 기타 플랫폼에서 Azure의 Managed Disks로 마이그레이션 | Microsoft Docs
description: AWS 또는 기타 가상화 플랫폼과 같은 다른 클라우드에서 업로드한 VHD를 사용하여 Azure에서 VM을 만들고 Azure Managed Disks를 활용합니다.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42ad7bc10cb7b93bd4db9260f950ae4ca12aba44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126898"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>AWS(Amazon Web Services) 및 기타 플랫폼에서 Azure의 Managed Disks로 마이그레이션

AWS 또는 온-프레미스 가상화 솔루션에서 Azure로 VHD 파일을 업로드하여 Managed Disks를 활용하는 VM을 만들 수 있습니다. Azure Managed Disks를 사용하면 Azure IaaS VM의 저장소 계정을 관리할 필요가 없습니다. 필요한 디스크의 유형(프리미엄 또는 표준)과 크기를 지정해야 합니다. 그러면 Azure가 알아서 디스크를 만들고 관리해줍니다. 

일반화된 VHD 및 특수한 VHD를 모두 업로드할 수 있습니다. 
- **일반화된 VHD** - Sysprep을 사용하여 제거된 모든 개인 계정 정보가 포함되어 있습니다. 
- **특수한 VHD** - 사용자 계정, 애플리케이션 및 원본 VM의 다른 상태 데이터를 유지 관리합니다. 

> [!IMPORTANT]
> Azure에 VHD를 업로드하기 전에 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 수행해야 합니다.
>
>


| 시나리오                                                                                                                         | 문서화                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| 기존 AWS EC2 인스턴스를 관리되는 디스크를 사용하여 Azure VM에 마이그레이션하려고 합니다.                              | [AWS(Amazon Web Services)에서 Azure로 VM 이동](aws-to-azure.md)                           |
| 사용하려는 다른 가상화 플랫폼의 VM이 있어서 여러 Azure VM을 만들기 위해 이미지로 사용합니다. | [일반화된 VHD를 업로드하고 사용하여 Azure에서 새 VM 만들기](upload-generalized-managed.md) |
| 고유하게 사용자 지정된 VM을 Azure에서 다시 만들려고 합니다.                                                      | [Azure에 전문화된 VHD 업로드 및 새 VM 만들기](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Managed Disks 개요

Azure Managed Disks는 저장소 계정을 관리하지 않아도 되기 때문에 VM 관리를 간소화합니다. Managed Disks는 가용성 집합에서 VM의 안정성을 향상시킨다는 장점도 있습니다. 가용성 집합에서 여러 VM의 디스크는 단일 실패 지점을 방지하기 위해 충분히 서로 격리되어야 합니다. 서로 다른 저장소 배율 단위(스탬프)인 자동 가용성 집합에서 다른 VM의 디스크를 자동으로 배치합니다. 그러면 하드웨어 및 소프트웨어 오류로 인해 발생한 단일 저장소 배율 단위 오류의 영향을 제한합니다.
필요에 따라 4가지 유형의 스토리지 옵션 중에 하나를 선택할 수 있습니다. 사용 가능한 디스크 유형에 대한 자세한 내용은 [디스크 유형 선택](disks-types.md) 문서를 참조하세요.

## <a name="plan-for-the-migration-to-managed-disks"></a>Managed Disks로 마이그레이션 계획 수립

이 섹션을 통해 VM 및 디스크 유형에 대한 최선의 결정을 내릴 수 있습니다.

관리되지 않는 디스크에서 관리 디스크로 마이그레이션하려는 경우 [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할의 사용자는 VM 크기를 변경할 수 없습니다(사전 변환이 가능하기 때문). 이는 관리 디스크가 있는 VM의 경우 사용자에게 OS 디스크에 대한 Microsoft.Compute/disks/write 권한이 있어야 하기 때문입니다.

### <a name="location"></a>Location

Azure Managed Disks를 사용할 수 있는 위치를 선택합니다. 프리미엄 Managed Disks를 마이그레이션하는 경우에도 마이그레이션하려고 계획한 지역에서 Premium Storage를 사용할 수 있는지 확인합니다. 사용 가능한 위치에 대한 최신 정보는 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services)를 참조하세요.

### <a name="vm-sizes"></a>VM 크기

프리미엄 Managed Disks를 마이그레이션하는 경우 VM 크기를 VM이 위치한 지역에서 제공하는 Premium Storage 지원 가능 크기로 업데이트해야 합니다. Premium Storage를 사용할 수 있는 VM 크기를 검토합니다. Azure VM 크기 사양은 [가상 머신의 크기](sizes.md)에 나열되어 있습니다.
Premium Storage와 작동하는 가상 머신의 성능 특징을 검토하고 워크로드에 가장 적합한 VM 크기를 선택합니다. VM에서 디스크 트래픽을 제어하기에 충분한 대역폭을 사용할 수 있는지 확인합니다.

### <a name="disk-sizes"></a>디스크 크기

**프리미엄 Managed Disks**

VM에서 사용할 수 있는 프리미엄 관리 디스크에는 7가지 형식이 있으며 각 형식에는 특정 IOP 및 처리량 한도가 있습니다. 용량, 성능, 확장성 및 최대 로드 측면에서 애플리케이션의 필요에 따라 VM에 대한 프리미엄 디스크 유형을 선택할 때 이 제한을 고려해야 합니다.

| 프리미엄 디스크 유형  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| 디스크 크기           | 32GB| 64GB| 128GB| 256GB|512 GB | 1,024GB(1TB)    | 2,048GB(2TB)    | 4,095GB(4TB)    | 
| 디스크당 IOPS       | 120   | 240   | 500   | 1100  |2,300              | 5,000              | 7,500              | 7,500              | 
| 디스크당 처리량 | 초당 25MB  | 초당 50MB  | 초당 100MB | 초당 125MB |초당 150MB | 초당 200MB | 초당 250MB | 초당 250MB |

**표준 Managed Disks**

VM에서 사용할 수 있는 표준 관리 디스크에는 7가지 형식이 있습니다. 각각은 용량이 다르지만 동일한 IOPS 및 처리량이 제한됩니다. 애플리케이션의 용량 요구 사항에 따라 표준 Managed Disks의 종류를 선택합니다.

| 표준 디스크 유형  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| 디스크 크기           | 30GB            | 64GB            | 128GB           | 256GB           |512 GB           | 1,024GB(1TB)   | 2,048GB(2TB)    | 4,095GB(4TB)   | 
| 디스크당 IOPS       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| 디스크당 처리량 | 60 MB per second | 60 MB per second | 60 MB per second | 60 MB per second |60 MB per second | 60 MB per second | 60 MB per second | 60 MB per second | 

### <a name="disk-caching-policy"></a>디스크 캐싱 정책 

**프리미엄 Managed Disks**

기본적으로 디스크 캐싱 정책은 VM에 연결된 프리미엄 운영 체제 디스크에 대한 *읽기 / 쓰기* 및 모든 프리미엄 데이터 디스크에 대한 *읽기 전용*입니다. 애플리케이션의 IO에 대한 최적의 성능을 얻으려면 이 구성 설정이 좋습니다. 쓰기가 많거나 쓰기 전용인 디스크의 경우(예: SQL Server 로그 파일) 더 나은 애플리케이션 성능을 얻기 위해 디스크 캐싱을 사용하지 않도록 설정합니다.

### <a name="pricing"></a>가격

[Managed Disks에 대한 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 검토합니다. 프리미엄 Managed Disks의 가격 책정은 관리되지 않는 프리미엄 디스크와 같습니다. 하지만 표준 Managed Disks의 가격 책정은 관리되지 않는 표준 디스크와 다릅니다.


## <a name="next-steps"></a>다음 단계

- Azure에 VHD를 업로드하기 전에 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 수행해야 합니다.
