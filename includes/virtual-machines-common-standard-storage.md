---
title: 포함 파일
description: 포함 파일
services: storage
author: yuemlu
ms.service: storage
ms.topic: include
ms.date: 01/08/2019
ms.author: yuemlu
ms.custom: include file
ms.openlocfilehash: ad57d373422e0fc310e51ac31f2a2e76999abf22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127160"
---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>비용 효율적인 Standard Storage와 관리되지 않는 Azure VM 디스크 및 관리 Azure VM 디스크

Azure Standard Storage는 대기 시간에 영향을 받지 않는 워크로드를 실행하는 VM에 대해 안정적인 저비용 디스크 지원을 제공합니다. 또한 Blob, 테이블, 큐 및 파일을 지원합니다. Standard Storage를 사용하면 데이터가 HDD(하드 디스크 드라이브)에 저장됩니다. VM 작업 시 개발/테스트 시나리오 및 덜 중요한 워크로드에는 표준 SSD 및 HDD 디스크를 사용하고 중요 업무용 프로덕션 애플리케이션에는 프리미엄 SSD 디스크를 사용할 수 있습니다. Standard Storage는 모든 Azure 지역에서 사용할 수 있습니다. 

이 문서는 표준 SSD 및 HDD 디스크 사용에 중점을 두고 있습니다. Blob, 테이블, 큐 및 파일이 포함된 저장소 사용에 대한 자세한 내용은 [저장소 소개](../articles/storage/common/storage-introduction.md)를 참조하세요.

## <a name="disk-types"></a>디스크 유형

Azure VM에 표준 디스크를 만드는 방법은 두 가지입니다.

**비관리 디스크**: 이 유형의 디스크는 VM 디스크에 해당하는 VHD 파일을 저장하는 데 사용되는 스토리지 계정을 관리하는 원래 방법입니다. VHD 파일은 저장소 계정에 페이지 Blob으로 저장됩니다. 관리되지 않는 디스크는 DSv2 및 GS 시리즈와 같은 Premium Storage를 주로 사용하는 VM을 비롯한 모든 Azure VM 크기에 연결될 수 있습니다. Azure VM은 여러 표준 디스크 연결을 지원하며 VM당 최대 256TiB의 저장소를 허용합니다. 미리 보기 디스크 크기를 사용하는 경우 VM 당 최대 약 2PiB의 저장소를 가질 수 있습니다.

[**Azure Managed Disks**](../articles/virtual-machines/windows/managed-disks-overview.md): 이 기능은 VM 디스크에 사용되는 스토리지 계정을 자동으로 관리합니다. 필요한 디스크의 유형(프리미엄 SSD, 표준 SSD 또는 표준 HDD)과 크기를 지정하면 Azure가 알아서 디스크를 만들고 관리해줍니다. 저장소 계정에 대한 확장성 한도 내에 머무르기 위해 다수의 저장소 계정에 디스크를 배치하려고 고심할 필요가 없습니다. Azure가 알아서 처리해 드립니다.

두 가지 유형의 디스크를 모두 사용할 수 있지만 많은 기능의 이점을 활용하려면 Managed Disks를 사용하는 것이 좋습니다.

Azure Standard Storage를 시작하려면 [무료로 시작 하기](https://azure.microsoft.com/pricing/free-trial/)를 방문하세요. 

Managed Disks로 VM을 만드는 방법에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

* [Resource Manager 및 PowerShell을 사용하여 VM 만들기](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Azure CLI를 사용하여 Linux VM 만들기](../articles/virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Standard Storage 기능

Standard Storage의 기능 중 일부를 살펴보겠습니다. 자세한 내용은 [Azure Storage 소개](../articles/storage/common/storage-introduction.md)를 참조하세요.

**Standard Storage**: Azure Standard Storage는 Azure Disks, Azure Blobs, Azure Files, Azure Tables 및 Azure Queues를 지원합니다. 표준 스토리지 서비스를 사용하려면 [Azure Storage 계정 만들기](../articles/storage/common/storage-quickstart-create-account.md)에서 시작하세요.

**표준 SSD 디스크:** 표준 HDD 디스크보다 더 안정적인 성능을 제공하는 표준 SSD 디스크를 현재 사용할 수 있습니다. 표준 SSD 디스크의 지역 가용성에 대한 자세한 내용은 [표준 SSD 디스크의 지역 가용성](../articles/virtual-machines/windows/faq-for-disks.md#standard-ssds-azure-regions)을 참조하세요.

**표준 HDD 디스크:** 표준 HDD 디스크는 Premium Storage에 사용되는 시리즈-사이즈(예: DSv2 및 GS 시리즈) VM을 비롯한 모든 Azure VM에 연결할 수 있습니다. 표준 HDD 디스크는 하나의 VM에만 연결될 수 있습니다. 하지만 VM에는 이러한 디스크를 한 개 이상, 해당 VM 크기에 정의된 최대 디스크 개수만큼 연결할 수 있습니다. 사양에 대한 자세한 내용은 표준 저장소 확장성 및 성능 목표에 관한 다음 섹션에서 설명하겠습니다.

**표준 페이지 Blob**: 표준 페이지 Blob은 VM용 영구 디스크를 보관하는 데 사용되며 다른 유형의 Azure Blob처럼 REST를 통해 직접 액세스할 수도 있습니다. [페이지 Blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)은 임의 읽기 및 쓰기 작업용으로 최적화된 512바이트 페이지 컬렉션입니다. 

**스토리지 복제:** 대부분의 지역에서 Standard Storage 계정의 데이터는 로컬로 복제하거나 여러 데이터 센터에 걸쳐 지역에서 복제할 수 있습니다. 사용 가능한 네 가지의 복제 유형은 LRS(로컬 중복 저장소), ZRS(영역 중복 저장소), GRS(지역 중복 저장소) 및 RA-GRS(읽기 액세스 지역 중복 저장소)입니다. Standard Storage의 Managed Disks는 현재 LRS(로컬 중복 스토리지)만 지원합니다. 자세한 내용은 [저장소 복제](../articles/storage/common/storage-redundancy.md)를 참조하세요.

## <a name="scalability-and-performance-targets"></a>확장성 및 성능 대상

이 섹션에서는 표준 저장소 사용 시 고려해야 하는 확장성 및 성능 목표를 설명합니다.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>계정 한도 – 관리 디스크에 적용되지 않음

| **리소스** | **기본 제한** |
|--------------|-------------------|
| 저장소 계정당 TB  | 500TB |
| 스토리지 계정당<sup>1</sup> 최대 수신(미국 지역) | GRS/ZRS를 사용하는 경우 10Gbps, LRS의 경우 20Gbps |
| 스토리지 계정당<sup>1</sup> 최대 송신(미국 지역) | RA-GRS/GRS/ZRS를 사용하는 경우 20Gbps, LRS의 경우 30Gbps |
| 스토리지 계정당<sup>1</sup> 최대 수신(유럽 및 아시아 지역) | GRS/ZRS를 사용하는 경우 5Gbps, LRS의 경우 10Gbps |
| 스토리지 계정당<sup>1</sup> 최대 송신(유럽 및 아시아 지역) | RA-GRS/GRS/ZRS를 사용하는 경우 10Gbps, LRS의 경우 15Gbps |
| 저장소 계정당 총 요청 빈도(개체 크기는 1KB로 가정함) | 최대 20,000 개의 IOPS, 초 당 엔터티 또는 초 당 메시지 |

<sup>1</sup> 수신은 저장소 계정으로 전송되는 모든 데이터(요청)를 가리킵니다. 송신은 저장소 계정에서 수신되는 모든 데이터(응답)를 가리킵니다.

자세한 내용은 [Azure Storage 확장성 및 성능 목표](../articles/storage/common/storage-scalability-targets.md)를 참조하세요.

애플리케이션의 요구가 단일 저장소 계정의 확장성 목표를 초과하는 경우, 애플리케이션에서 여러 저장소 계정을 사용하도록 빌드하고 이러한 저장소 계정에 데이터를 분할합니다. 아니면 Azure Managed Disks를 사용하여 Azure가 데이터의 분할 및 배치를 알아서 관리하도록 할 수 있습니다.

### <a name="standard-disks-limits"></a>표준 디스크 한도

프리미엄 디스크와 달리 표준 디스크의 처리량(대역폭) 및 IOPS(초당 입/출력 작업 수)는 프로비전되지 않습니다. 표준 디스크의 성능은 디스크가 연결되는 VM의 크기와 디스크의 크기에 따라 달라집니다.

워크로드에 대기 시간이 짧은 고성능 디스크 지원이 필요한 경우 Premium Storage를 사용하는 것이 좋습니다. Premium Storage의 이점을 자세히 알아보려면 [고성능 Premium Storage 및 Azure VM 디스크](../articles/virtual-machines/windows/premium-storage.md)를 참조하세요.

## <a name="snapshots-and-copy-blob"></a>스냅숏 및 Blob 복사

Storage 서비스에서 VHD 파일은 페이지 Blob입니다. 페이지 Blob의 스냅숏을 만들어서 다른 위치(예: 다른 저장소 계정)에 복사할 수 있습니다.

### <a name="unmanaged-disks"></a>관리되지 않는 디스크

관리되지 않는 표준 디스크에 대한 [증분 스냅숏](../articles/virtual-machines/windows/incremental-snapshots.md)은 Standard Storage에서 스냅숏을 사용하는 것과 같은 방식으로 만들 수 있습니다. 원본 디스크가 로컬 중복 저장소에 있는 경우 스냅숏을 만든 후 이 스냅숏을 지역 중복 표준 저장소 계정에 복사하는 것이 좋습니다. 자세한 내용은 [Azure Storage 중복 옵션](../articles/storage/common/storage-redundancy.md)을 참조하세요.

디스크가 VM에 연결되어 있으면 특정 API 작업이 디스크에 허용되지 않습니다. 예를 들어 디스크가 VM에 연결되어 있는 한 해당 Blob에 대해 [Blob 복사](/rest/api/storageservices/Copy-Blob) 작업을 수행할 수 없습니다. 대신, 먼저 [Blob 스냅숏 생성](/rest/api/storageservices/Snapshot-Blob) REST API 메서드를 사용하여 해당 Blob의 스냅숏을 만든 후 스냅숏의 [Blob 복사](/rest/api/storageservices/Copy-Blob)를 수행하여 연결된 디스크를 복사합니다. 또는 디스크를 분리한 후 필요한 작업을 수행할 수 있습니다.

스냅숏의 지역 중복 복사본을 유지하려면 AzCopy 또는 Blob 복사를 사용하여 지역 중복 저장소 계정에서 지역 중복 표준 저장소 계정으로 스냅숏을 복사할 수 있습니다. 자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](../articles/storage/common/storage-use-azcopy.md) 및 [Blob 복사](/rest/api/storageservices/Copy-Blob)를 참조하세요.

표준 스토리지 계정에서 페이지 Blob에 대한 REST 작업을 수행하는 방법에 대한 자세한 내용은 [Azure Storage 서비스 REST API](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference)를 참조하세요.

### <a name="managed-disks"></a>관리 디스크

관리 디스크에 대한 스냅숏은 관리 디스크의 읽기 전용 복사본이며 표준 관리 디스크로 저장됩니다. 증분 스냅숏은 현재 Managed Disks에 지원되지 않지만 향후 지원될 예정입니다.

관리 디스크가 VM에 연결되어 있으면 특정 API 작업이 디스크에 허용되지 않습니다. 예를 들어 디스크가 VM에 연결되어 있는 동안 공유 액세스 서명(SAS)을 생성하여 복사 작업을 수행할 수 없습니다. 대신 디스크 스냅숏을 먼저 만든 다음 스냅샷 복사를 수행합니다. 또는 디스크를 분리한 다음 공유 액세스 서명(SAS)을 생성하여 복사 작업을 수행할 수 있습니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Standard Storage를 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.

* 표준 저장소 관리되지 않는 디스크/데이터 크기
* 표준 관리 디스크
* 표준 저장소 스냅숏
* 아웃바운드 데이터 전송
* 트랜잭션

**비관리 스토리지 데이터 및 디스크 크기:** 비관리 디스크 및 기타 데이터(Blob, 테이블, 큐 및 파일)의 경우 사용하는 공간의 양에 대해서만 요금이 청구됩니다. 예를 들어 VM의 페이지 Blob이 127GB로 프로비전되었는데 VM이 실제로 10GB의 공간만을 사용한다면 10GB의 공간에 대해서만 요금이 청구됩니다. 표준 저장소를 최대 8191GB까지, 표준 관리되지 않는 디스크를 최대 4095GB까지 지원합니다. 

**관리형 디스크:** 표준 관리형 디스크의 경우 프로비전된 디스크 크기에 따라 요금이 청구됩니다. Azure는 프로비전된 크기(올림)를 아래 테이블에 지정된 대로 가장 가까운 Managed Disks 옵션에 매핑합니다. 각각의 관리 디스크는 지원되는 프로비전된 크기 중 하나에 매핑되고 그에 따라 요금이 청구됩니다. 예를 들어 표준 관리 디스크를 만들고 프로비전된 크기를 200GiB로 지정하면 S15 디스크 유형의 가격에 따라 요금이 청구됩니다.

별표로 표시되는 크기는 현재 미리 보기로 제공됩니다.

| **표준 HDD 관리 <br>디스크 유형** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** | **S60*** | **S70*** | **S80*** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| 디스크 크기        | 32GiB  | 64GiB  | 128GiB | 256GiB | 512GiB | 1,024GiB(1TiB) | 2,048GiB(2TiB) | 4,095GiB(4TiB) | 8,192GiB(8TiB) | 16,385GiB(16TiB) | 32,767GiB(32TiB) |


**스냅숏**: 표준 디스크 스냅숏의 경우 스냅숏에 사용되는 추가 용량에 해당하는 요금이 청구됩니다. 스냅숏에 대한 자세한 내용은 [Blob의 스냅숏 만들기](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)를 참조하세요.

**아웃바운드 데이터 전송**:  [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/data-transfers/) (Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용량에 대한 요금이 청구됩니다.

**트랜잭션**: Azure에서는 표준 스토리지 트랜잭션 10만 건당 $0.0036가 청구됩니다. 트랜잭션에는 저장소에 대한 읽기 및 쓰기 작업이 모두 포함됩니다.

Standard Storage, Virtual Machines 및 Managed Disks 가격 책정에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Storage 가격](https://azure.microsoft.com/pricing/details/storage/)
* [Virtual Machines 가격](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Azure Backup 서비스 지원

관리되지 않는 디스크가 포함된 가상 머신은 Azure Backup을 사용하여 백업할 수 있습니다. [자세한 내용](../articles/backup/backup-azure-vms-first-look-arm.md).

Azure Backup 서비스를 Managed Disks와 함께 사용하면 시간 기반 백업, 손쉬운 VM 복원 및 백업 보존 정책을 사용하여 백업 작업을 만들 수도 있습니다. 자세한 내용은 [Managed Disks로 VM에 Azure Backup 서비스 사용](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Storage 소개](../articles/storage/common/storage-introduction.md)

* [저장소 계정을 만드는](../articles/storage/common/storage-quickstart-create-account.md)

* [Managed Disks 개요](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Resource Manager 및 PowerShell을 사용하여 VM 만들기](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Azure CLI를 사용하여 Linux VM 만들기](../articles/virtual-machines/linux/quick-create-cli.md)
