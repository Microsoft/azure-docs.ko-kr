---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 06e6e491fa1e9a047527efb78149855b125771ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543791"
---
# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>증분 스냅숏을 사용하여 Azure 관리되지 않는 VM 디스크 백업
## <a name="overview"></a>개요
Azure Storage는 Blob의 스냅숏을 만드는 기능을 제공합니다. 스냅숏은 해당 시점에 Blob 상태를 캡처합니다. 이 문서에서는 스냅숏을 사용하여 가상 머신 디스크의 백업을 유지 관리할 수 있는 시나리오에 대해 설명합니다. Azure Backup 및 복구 서비스를 사용하지 않도록 선택하고 가상 컴퓨터 디스크에 대한 사용자 지정 백업 전략을 만들 때 이 방법론을 사용할 수 있습니다.

Azure 가상 머신 디스크는 Azure Storage에 페이지 Blob으로 저장됩니다. 이 문서에서 가상 머신 디스크에 대한 백업 전략을 설명했으므로 페이지 Blob의 컨텍스트에서 스냅숏을 참조합니다. 스냅숏에 대한 자세한 내용은 [Blob의 스냅숏 만들기](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)를 참조하세요.

## <a name="what-is-a-snapshot"></a>스냅숏은 무엇입니까?
Blob 스냅숏은 특정 시점에 캡처된 Blob의 읽기 전용 버전입니다. 스냅숏이 생성된 후에는 읽거나 복사하거나 삭제할 수 있지만 수정할 수는 없습니다. 스냅숏을 사용하면 특정 시점에서 표시된 대로 Blob을 백업할 수 있습니다. REST 버전 2015-04-05까지 전체 스냅숏을 복사하는 기능이 있었습니다. REST 버전 2015-07-08 이상에서는 증분 스냅숏을 복사할 수도 있습니다.

## <a name="full-snapshot-copy"></a>전체 스냅숏 복사
스냅숏은 기본 Blob의 백업을 유지하도록 Blob으로 다른 저장소 계정에 복사될 수 있습니다. 또한 Blob을 이전 버전으로 복원하는 것과 같은 해당 기본 Blob에 대해 스냅숏을 복사할 수 있습니다. 스냅숏이 하나의 저장소 계정에서 다른 저장소 계정으로 복사되면 기본 페이지 Blob과 동일한 공간을 차지합니다. 따라서 하나의 저장소 계정에서 다른 저장소 계정으로 전체 스냅숏을 복사하는 작업이 느려지고 대상 저장소 계정에서 많은 공간을 사용하게 됩니다.

> [!NOTE]
> 기본 Blob을 다른 대상으로 복사하는 경우 Blob의 스냅숏은 함께 복사되지 않습니다. 마찬가지로 복사본으로 기본 Blob을 덮어쓴 경우 기본 Blob와 연결된 스냅숏은 영향을 받지 않고 기본 Blob 이름으로 그대로 유지됩니다.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>스냅숏을 사용하여 디스크 백업
가상 머신 디스크에 대한 백업 전략으로 디스크 또는 페이지 Blob의 스냅숏을 주기적으로 작성할 수 있으며 [Blob 복사](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) 작업 또는 [AzCopy](../articles/storage/common/storage-use-azcopy.md)와 같은 도구를 사용하여 스냅숏을 다른 저장소 계정에 복사할 수 있습니다. 스냅숏을 다른 이름으로 대상 페이지 Blob에 복사할 수 있습니다. 그러면 생성되는 대상 페이지 Blob은 스냅숏이 아닌 쓰기 가능한 페이지 Blob입니다. 이 문서 뒷부분에서는 스냅숏을 사용하여 가상 컴퓨터 디스크를 백업하는 단계에 대해 설명합니다.

### <a name="restore-disks-using-snapshots"></a>스냅숏을 사용하여 디스크 복원
디스크를 백업 스냅숏 중 하나에서 이전에 캡처된 안정적인 버전으로 복원할 때 기본 페이지 Blob에 대해 스냅숏을 복사할 수 있습니다. 스냅숏이 기본 페이지 Blob으로 올라간 후 스냅숏은 유지되지만 읽고 쓰기가 가능한 복사본으로 해당 원본을 덮어쓰게 됩니다. 이 문서의 뒷부분에서는 해당 스냅숏에서 디스크의 이전 버전을 복원하는 단계에 대해 설명합니다.

### <a name="implementing-full-snapshot-copy"></a>전체 스냅숏 복사 구현
다음을 수행하여 전체 스냅숏 복사를 구현할 수 있습니다.

* 먼저 [Blob 스냅숏](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) 작업을 사용하여 기본 Blob의 스냅숏을 만듭니다.
* 그런 다음 [Blob 복사](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob)를 사용하여 스냅숏을 대상 저장소 계정에 복사합니다.
* 기본 Blob의 백업 복사본을 유지하려면 이 프로세스를 반복합니다.

## <a name="incremental-snapshot-copy"></a>증분 스냅숏 복사
[GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API의 새로운 기능은 페이지 Blob 또는 디스크의 스냅숏을 백업하는 더 좋은 방법을 제공합니다. API는 기본 Blob와 스냅숏 간의 변경 내용 목록을 반환하며 이로 인해 백업 계정에 사용되는 저장소 공간의 양이 줄어듭니다. API는 Premium Storage 뿐만 아니라 Standard Storage에 페이지 Blob을 지원합니다. 이 API를 사용하여 Azure VM에 대한 빠르고 효율적인 백업 솔루션을 빌드할 수 있습니다. 이 API는 REST 버전 2015-07-08 이상에서 사용할 수 있습니다.

증분 스냅숏 복사를 통해 다음 간의 차이를 하나의 저장소 계정에서 다른 저장소 계정으로 복사할 수 있습니다.

* 기본 Blob과 해당 스냅숏 또는
* 기본 Blob의 두 스냅숏 모두

다음과 같은 조건이 충족된 경우

* 2016년 1월 1일 또는 그 이후에 Blob을 만든 경우
* [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) 또는 [Blob 복사](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob)를 사용하여 두 스냅숏 간에 Blob을 덮어쓰지 않은 경우

**참고**: 이 기능은 프리미엄 및 표준 Azure 페이지 Blob에 사용할 수 있습니다.

스냅숏을 사용하는 사용자 지정 백업 전략이 있는 경우 하나의 저장소 계정에서 다른 저장소 계정으로 스냅숏을 복사하는 작업이 느려지고 저장소 공간을 많이 사용할 수 있습니다. 전체 스냅숏을 백업 저장소 계정에 복사하는 대신 백업 페이지 Blob에 연속 스냅숏 간의 차이를 기록할 수 있습니다. 이러한 방식으로 복사하는 시간 및 백업을 저장하는 공간이 상당히 줄어듭니다.

### <a name="implementing-incremental-snapshot-copy"></a>증분 스냅숏 복사 구현
다음을 수행하여 증분 스냅숏 복사를 구현할 수 있습니다.

* [Blob 스냅숏](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob)을 사용하여 기본 Blob의 스냅숏을 만듭니다.
* [복사 Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob)을 사용하여 스냅숏을 동일한 Azure 지역 또는 다른 Azure 지역의 대상 백업 저장소 계정에 복사합니다. 이것이 백업 페이지 Blob입니다. 백업 페이지 Blob의 스냅숏을 만들고 백업 계정에 저장합니다.
* Blob 스냅숏을 사용하여 기본 Blob의 또 다른 스냅숏을 만듭니다.
* [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges)를 사용하여 기본 Blob의 첫 번째 스냅숏과 두 번째 스냅숏 간의 차이를 만듭니다. 새 매개 변수 **prevsnapshot**을 사용하여 차이를 만들려는 스냅숏의 날짜/시간 값을 지정합니다. 이 매개 변수가 존재하는 경우 REST 응답에는 페이지 지우기를 포함하여 대상 스냅숏과 이전 스냅숏 간의 변경된 페이지만 포함됩니다.
* [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) 를 사용하여 이러한 변경 내용을 백업 페이지 Blob에 적용합니다.
* 마지막으로, 백업 페이지 Blob의 스냅숏을 만들고 백업 저장소 계정에 저장합니다.

다음 섹션에서는 증분 스냅숏 복사를 사용하여 디스크의 백업을 유지 관리할 수 있는 방법을 보다 자세히 설명합니다.

## <a name="scenario"></a>시나리오
이 섹션에서는 스냅숏을 사용하여 가상 컴퓨터 디스크에 대한 사용자 지정 백업 전략을 포함하는 시나리오에 대해 설명합니다.

Premium Storage P30 디스크가 연결된 DS 시리즈 Azure VM을 고려합니다. *mypremiumdisk*라는 P30 디스크는 *mypremiumaccount*라는 Premium Storage 계정에 저장됩니다. *mybackupstdaccount*라는 표준 저장소 계정은 *mypremiumdisk*의 백업을 저장하는 데 사용됩니다. 12시간마다 *mypremiumdisk* 의 스냅숏을 유지하려고 합니다.

저장소 계정을 만드는 방법에 대해 알아보려면 [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)를 참조하세요.

Azure VM 백업에 대해 알아보려면 [Azure VM 백업 계획](../articles/backup/backup-azure-vms-introduction.md)을 참조하세요.

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>증분 스냅숏을 사용하여 디스크의 백업을 유지 관리하는 단계
다음 단계는 *mypremiumdisk*의 스냅숏을 만들고 *mybackupstdaccount*에서 백업을 유지 관리하는 방법을 설명합니다. 이 백업은 *mybackupstdpageblob*이라는 표준 페이지 Blob입니다. 백업 페이지 Blob은 항상 *mypremiumdisk*라는 마지막 스냅숏과 동일한 상태를 반영합니다.

1. *mypremiumdisk_ss1*이라는 *mypremiumdisk* 스냅숏을 만들어서 Premium Storage 디스크에 대한 백업 페이지 Blob을 만듭니다.
2. 이 스냅숏을 mybackupstdaccount에 *mybackupstdpageblob*이라는 페이지 Blob으로 복사합니다.
3. [스냅숏 Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob)을 사용하여 *mybackupstdpageblob_ss1*이라는 *mybackupstdpageblob*의 스냅숏을 만들고 *mybackupstdaccount*에 저장합니다.
4. 백업 창에 있는 동안 *mypremiumdisk_ss2*라는 *mypremiumdisk*의 또 다른 스냅숏을 만들고 *mypremiumaccount*에 저장합니다.
5. **prevsnapshot** 매개 변수가 *mypremiumdisk_ss1*의 타임스탬프로 설정된 *mypremiumdisk_ss2*의 [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges)를 사용하여 두 스냅숏 *mypremiumdisk_ss2*와 *mypremiumdisk_ss1* 간의 증분 변경 내용을 가져옵니다. 이러한 증분 변경 내용을 *mybackupstdaccount*의 백업 페이지 Blob *mybackupstdpageblob*에 작성합니다. 증분 변경 내용에 삭제된 범위가 있는 경우 백업 페이지 Blob에서 지워야 합니다. [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) 를 사용하여 증분 변경 내용을 백업 페이지 Blob에 작성합니다.
6. *mybackupstdpageblob_ss2*라는 백업 페이지 Blob *mybackupstdpageblob*의 스냅숏을 만듭니다. Premium Storage 계정에서 이전 스냅숏 *mypremiumdisk_ss1*을 삭제합니다.
7. 백업 창마다 4~6단계를 반복합니다. 이러한 방식으로 표준 저장소 계정에서 *mypremiumdisk* 의 백업을 유지할 수 있습니다.

![증분 스냅숏을 사용하여 디스크 백업](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>스냅숏에서 디스크를 복원하는 단계
다음 단계는 프리미엄 디스크 *mypremiumdisk*를 백업 저장소 계정 *mybackupstdaccount*에서 이전 스냅숏으로 복원하는 방법을 설명합니다.

1. 프리미엄 디스크를 복원하려는 시점을 식별합니다. 백업 저장소 계정 *mybackupstdaccount*에 저장되는 스냅숏 *mybackupstdpageblob_ss2*라고 가정해 봅니다.
2. mybackupstdaccount에서 새 백업 기본 페이지 Blob *mybackupstdpageblobrestored*로 스냅숏 *mybackupstdpageblob_ss2*를 승격합니다.
3. *mybackupstdpageblobrestored_ss1*이라는 이 복원된 백업 페이지 Blob의 스냅숏을 만듭니다.
4. 새로운 프리미엄 디스크 *mypremiumdiskrestored*로 복원된 페이지 Blob *mybackupstdpageblobrestored*를 *mybackupstdaccount*에서 *mypremiumaccount*로 복사합니다.
5. 향후 증분 백업을 만들기 위해 *mypremiumdiskrestored_ss1*이라는 *mypremiumdiskrestored*의 스냅숏을 만듭니다.
6. DS 시리즈 VM을 복원된 디스크 *mypremiumdiskrestored*로 가리키고 VM에서 이전 *mypremiumdisk*를 분리합니다.
7. 백업 페이지 Blob으로 *mybackupstdpageblobrestored*를 사용하여 복원된 디스크 *mypremiumdiskrestored*에 대해 이전 섹션에서 설명한 백업 프로세스를 시작합니다.

![스냅숏에서 디스크 복원](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>다음 단계
다음 링크를 사용하여 Blob의 스냅숏을 만들고 VM 백업 인프라를 계획하는 방법을 알아봅니다.

* [Blob의 스냅숏 만들기](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [VM Backup 인프라 계획](../articles/backup/backup-azure-vms-introduction.md)

