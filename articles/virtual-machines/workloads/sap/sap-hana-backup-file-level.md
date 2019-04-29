---
title: 파일 수준의 SAP HANA Azure Backup | Microsoft Docs
description: Azure 가상 머신에는 SAP HANA에 대한 두 가지 주요 백업 방법이 있습니다. 이 문서에서는 파일 수준의 SAP HANA Azure Backup에 대해 설명합니다
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: fc35077e00bc6322a815a52ca6ab3571a4e06d3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60937719"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>파일 수준의 SAP HANA Azure Backup

## <a name="introduction"></a>소개

이 문서는 3부로 구성된 SAP HANA 백업 관련 문서 시리즈의 일부입니다. [Azure Virtual Machines의 SAP HANA Backup 가이드](./sap-hana-backup-guide.md)에서 시작에 대한 개요와 정보를 제공하고, [저장소 스냅숏에 기반한 SAP HANA 백업](./sap-hana-backup-storage-snapshots.md)에서 저장소 스냅숏을 기반으로 하는 백업 옵션을 설명합니다.

Azure의 VM 유형에 따라 서로 다른 개수의 VHD를 연결할 수 있습니다. 정확한 세부 정보는 [Azure에서 Linux 가상 머신에 대한 크기](../../linux/sizes.md)에 문서화되어 있습니다. 이 설명서에서 참조되는 테스트에서는 64개의 연결된 데이터 디스크를 허용하는 GS5 Azure VM을 사용했습니다. 대용량 SAP HANA 시스템의 경우 소프트웨어 스트라이프와 함께 이미 많은 수의 디스크를 데이터 및 로그 파일에 사용하여 디스크 IO 처리량을 최적화할 수 있습니다. Azure VM의 SAP HANA 배포에 제안된 디스크 구성에 대한 자세한 내용은 [Azure의 SAP HANA 운영 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)를 참조하세요. 권장 사항에는 로컬 백업에 대한 디스크 공간 권장 사항도 포함됩니다.

현재 Azure Backup 서비스에서 사용할 수 있는 SAP HANA 백업 통합은 없습니다. 파일 수준에서 백업/복원을 관리하는 표준 방법은 SAP HANA Studio 또는 SAP HANA SQL 문을 통해 파일 기반 백업을 수행하는 것입니다. 자세한 내용은 [SAP HANA SQL 및 시스템 보기 참조](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf)(영문)를 참조하세요.

![SAP HANA Studio 백업 메뉴 항목의 대화 상자를 보여 주는 그림](media/sap-hana-backup-file-level/image022.png)

이 그림에서는 SAP HANA Studio 백업 메뉴 항목의 대화 상자를 보여 줍니다. &quot;파일&quot; 유형을 선택하면 SAP HANA에서 백업 파일을 쓰는 파일 시스템의 경로를 지정해야 합니다. 복원도 동일한 방식으로 작동합니다.

간단하고 직관적인 선택으로 보이지만 몇 가지 고려 사항이 있습니다. 앞에서 설명한 대로 Azure VM에 연결할 수 있는 데이터 디스크의 수는 제한됩니다. 데이터베이스와 디스크 처리량 요구 사항의 크기에 따라 VM의 파일 시스템에 SAP HANA 백업 파일을 저장할 수 있는 용량이 부족할 수 있으므로 여러 데이터 디스크에서 소프트웨어 스트라이프가 포함될 수 있습니다. 테라바이트 단위의 데이터를 처리할 때 이러한 백업 파일을 이동하고 파일 크기 제한과 성능을 관리하기 위한 다양한 옵션이 이 문서의 뒷부분에 나와 있습니다.

File Storage 공유는 표준 SMB 파일 공유이므로 Azure에서 실행되는 응용 프로그램은 파일 시스템 I/O API를 통해 공유의 데이터에 액세스할 수 있습니다. 단일 Blob은 1TB로 제한되지만, 단일 Blob 컨테이너의 총 용량은 현재 500TB입니다. Azure File Storage 서비스를 사용하면 계정이 허용하는 만큼 많은 디렉터리를 만들 수 있습니다. 참조 [Azure Blob Storage: 핫 및 쿨 저장소 계층](../../../storage/blobs/storage-blob-storage-tiers.md) 쿨 blob storage에 대 한 세부 정보에 대 한 합니다.

보안을 강화하려면 지리적 복제된 저장소 계정을 사용하여 SAP HANA 백업을 저장합니다. 스토리지 계정 복제에 대한 자세한 내용은 [Azure Storage 복제](../../../storage/common/storage-redundancy.md)를 참조하세요.

지리적 복제된 전용 백업 저장소 계정에 SAP HANA 백업 전용 VHD를 배치할 수 있습니다. 그렇지 않으면 지리적 복제된 저장소 계정 또는 다른 지역에 있는 저장소 계정에 SAP HANA 백업을 보관하는 VHD를 복사할 수 있습니다.

## <a name="azure-backup-agent"></a>Azure Backup 에이전트

Azure 백업은 전체 VM을 백업할 뿐만 아니라 게스트 OS에 설치해야 하는 백업 에이전트를 통해 파일과 디렉터리를 백업하는 옵션도 제공합니다. 그러나 이 에이전트는 Windows에서만 지원됩니다([Resource Manager 배포 모델을 사용하여 Azure로 Windows 서버 또는 클라이언트 백업](../../../backup/backup-configure-vault.md) 참조).

이 문제를 해결하려면 먼저 SAP HANA 백업 파일을 Azure의 Windows VM에 복사한(예: SAMBA 공유를 통해) 다음, 여기서 Azure Backup 에이전트를 사용하는 것입니다. 기술적으로 가능하지만 Linux와 Windows VM 간의 복사로 인해 더욱 복잡하게 되며 백업 또는 복원 프로세스가 상당히 느려집니다. 따라서 이 해결 방법은 수행하지 않는 것이 좋습니다.

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer 유틸리티 정보

Azure 저장소에 디렉터리와 파일을 저장하려면 CLI 또는 PowerShell을 사용하거나 [Azure SDK](https://azure.microsoft.com/downloads/) 중 하나를 사용하여 도구를 개발할 수 있습니다. 또한 Azure 저장소에 데이터를 복사하는 데 즉시 사용할 수 있는 유틸리티인 AzCopy도 있지만, 이는 Windows 전용입니다([AzCopy 명령줄 유틸리티로 데이터 전송](../../../storage/common/storage-use-azcopy.md) 참조).

따라서 blobxfer 유틸리티가 SAP HANA 백업 파일을 복사하는 데 사용되었습니다. 이 유틸리티는 프로덕션 환경에서 많은 고객이 사용하는 오픈 소스이며 [GitHub](https://github.com/Azure/blobxfer)에서 사용할 수 있습니다. Azure File Storage는 클라우드 기반 SMB 파일 공유를 제공하므로 파일 공유에 의존하는 레거시 응용 프로그램을 비경제적인 다시 쓰기 작업 없이 신속하게 Azure로 마이그레이션할 수 있습니다. 또한 여러 파일이 있는 디렉터리를 복사하는 경우 md5 해시 또는 자동 병렬 처리와 같은 유용한 기능을 다양하게 제공합니다.

## <a name="sap-hana-backup-performance"></a>SAP HANA 백업 성능

![SAP HANA Studio의 SAP HANA 백업 콘솔 스크린샷](media/sap-hana-backup-file-level/image023.png)

이 스크린샷은 SAP HANA Studio의 SAP HANA 백업 콘솔입니다. XFS 파일 시스템을 사용하여 HANA VM에 연결된 단일 Azure 표준 저장소 디스크에서 230GB의 백업을 수행하는 데 약 42분이 걸렸습니다.

![SAP HANA 테스트 VM의 YaST 스크린샷](media/sap-hana-backup-file-level/image024.png)

이 스크린샷은 SAP HANA 테스트 VM의 YaST입니다. 앞에서 설명한 대로 SAP HANA 백업을 위한 1TB 단일 디스크를 확인할 수 있습니다. 230GB를 백업하는 데 약 42분이 걸렸습니다. 또한 5개의 200GB 디스크가 연결되었고, 소프트웨어 RAID md0이 만들어졌으며, 이러한 5개의 Azure 데이터 디스크에 스트라이핑이 사용되었습니다.

![연결된 5개 Azure 표준 저장소 데이터 디스크에 대한 스트라이핑으로 소프트웨어 RAID에서 동일한 백업 반복](media/sap-hana-backup-file-level/image025.png)

연결된 5개 Azure 표준 저장소 데이터 디스크에 스트라이핑을 사용하여 소프트웨어 RAID에서 동일한 백업을 반복하면 백업 시간이 42분에서 10분으로 줄었습니다. 디스크는 캐싱 없이 VM에 연결되었습니다. 따라서 디스크 쓰기 처리량이 백업 시간에 매우 중요하다는 것이 분명합니다. 게다가 Azure Premium Storage로 전환하여 프로세스를 더욱 가속화함으로써 성능을 최적화할 수 있습니다. 일반적으로 프로덕션 시스템에는 Azure Premium Storage를 사용해야 합니다.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>File Storage 정의

SAP HANA 백업 파일을 빠르게 저장하는 또 다른 옵션은 Azure Blob Storage입니다. 단일 Blob 컨테이너는 500TB 크기로 제한되지만, 일부 소형 SAP HANA 시스템에 충분한 크기이며 Azure의 M32ts, M32ls, M64ls 및 GS5 VM 유형을 사용하여 SAP HANA 백업을 충분하게 유지할 수 있습니다. 고객 간에 선택할 수 &quot;핫&quot; 하 고 &quot;콜드&quot; blob 저장소 (참조 [Azure Blob Storage: 핫 및 쿨 저장소 계층](../../../storage/blobs/storage-blob-storage-tiers.md)).

Azure File Storage - 일부 HPC 클러스터 솔루션에 필요한 표준 SMB 프로토콜을 사용하여 Azure에 일반적인 파일 및 데이터를 공유합니다.

![SAP HANA 파일 전체 백업의 파일](media/sap-hana-backup-file-level/image026.png)

여기서 SAP HANA 파일 전체 백업의 파일을 볼 수 있습니다. 4개의 파일이 있으며, 가장 큰 파일은 약 230GB입니다.

![Azure 표준 저장소 계정 Blob 컨테이너에 230GB를 복사하는 데 약 3,000초 소요](media/sap-hana-backup-file-level/image027.png)

초기 테스트에서 md5 해시를 사용하지 않고서 Azure 표준 저장소 계정 Blob 컨테이너에 230GB를 복사하는 데 약 3,000초가 걸렸습니다.

![Azure Portal에 표시된 화면에서 캡처한 스크린샷](media/sap-hana-backup-file-level/image028.png)

Azure Portal에 표시된 화면에서 캡처한 스크린샷입니다. &quot;sap-hana-backups&quot;라는 이름의 Blob 컨테이너를 만들었으며, SAP HANA 백업 파일을 나타내는 4개의 Blob이 포함되어 있습니다. 그 중 하나는 약 230GB 크기입니다.

HANA Studio 백업 콘솔을 사용하면 HANA 백업 파일의 최대 파일 크기를 제한할 수 있습니다. 샘플 환경에서는 하나의 230GB 대용량 파일 대신 여러 개의 작은 백업 파일을 갖출 수 있도록 하여 성능이 향상되었습니다.

![백업 시간이 향상되지 않는 HANA 쪽 백업 파일 크기 제한 설정](media/sap-hana-backup-file-level/image029.png)

HANA 쪽에서 백업 파일 크기 제한을 설정하는 경우 이 그림과 같이 파일을 순차적으로 쓰기 때문에 백업 시간이 향상되지 않습니다. 파일 크기 제한을 60GB로 설정했으므로 백업에서 하나의 230GB 파일 대신 4개의 큰 데이터 파일을 만들었습니다. 여러 백업 파일 사용은 M64s, M64ms, M128s 및 M128ms와 같은 더 큰 Azure VM의 메모리를 활용하는 HANA 데이터베이스를 백업하는 데 필요합니다.

![blobxfer 도구의 병렬 처리를 테스트하기 위해 HANA 백업 파일의 최대 크기를 15GB로 설정](media/sap-hana-backup-file-level/image030.png)

blobxfer 도구의 병렬 처리를 테스트하기 위해 HANA 백업 파일의 최대 크기를 15GB로 설정하여 19개의 백업 파일을 만들었습니다. 이 가이드에서는 Microsoft Azure File Storage 서비스에 대한 기본 작업을 수행하는 방법에 대해 알아봅니다.

이 결과는 Azure Blob 쓰기에 대한 60MB/초 제한 설정에 따른 것입니다. 클라이언트가 참여하는 프로젝트인 경우 클라이언트에서 자체의 Azure 구독으로 Azure File Storage를 만들어 프로젝트 데이터와 기능을 공유할 수 있습니다. HANA 시스템의 작업에 영향을 미칩니다.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>백업 소프트웨어 RAID에서 전용 Azure 데이터 디스크의 Blob 복사

수동 VM 데이터 디스크 백업과 달리, 이 방법에서는 HANA 데이터, HANA 로그 파일 및 구성 파일을 포함하여 전체 SAP 설치를 저장하기 위해 VM의 모든 데이터 디스크를 백업하지 않습니다. 대신 SAP HANA 파일 전체 백업을 저장하기 위해 여러 Azure 데이터 VHD에 스트라이핑을 사용하여 소프트웨어 RAID를 전용으로 배치하는 것이 좋습니다. SAP HANA 백업이 있는 이러한 디스크만 복사합니다. 이러한 디스크는 전용 HANA 백업 저장소 계정에 쉽게 보관하거나 추가 처리를 위해 전용 &quot;관리 VM 백업&quot;에 쉽게 연결할 수 있습니다.

![관련된 모든 VHD는 **start-azurestorageblobcopy** PowerShell 명령으로 복사되었습니다.](media/sap-hana-backup-file-level/image031.png)

로컬 소프트웨어 RAID에 대한 백업을 완료한 후에 **start-azurestorageblobcopy** PowerShell 명령([Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) 참조)을 사용하여 관련된 모든 VHD를 복사했습니다. 백업 파일을 유지하기 위한 전용 파일 시스템에만 영향을 미치기 때문에 디스크의 SAP HANA 데이터 또는 로그 파일 일관성에 대한 문제는 없습니다. 이 명령의 이점은 VM이 온라인 상태로 유지되는 동안 작동한다는 것입니다. 어떤 프로세스도 백업 스트라이프 세트에 쓰지 못하도록 하기 위해 Blob 복사하기 전에 분리하고 나중에 다시 탑재합니다. 또는 파일 시스템을 &quot;고정&quot;(freeze)하는 적절한 방법을 사용할 수 있습니다. 예를 들어 XFS 파일 시스템의 경우 xfs\_freeze를 사용합니다.

![Azure Portal의 vhds 컨테이너에 있는 Blob의 목록을 보여 주는 스크린샷](media/sap-hana-backup-file-level/image032.png)

이 스크린샷은 Azure Portal의 &quot;vhds&quot; 컨테이너에 있는 Blob의 목록을 보여 줍니다. SAP HANA 백업 파일을 유지하기 위해 소프트웨어 RAID 역할을 수행하는 SAP HANA 서버 VM에 5개의 VHD가 연결되어 있음을 확인할 수 있습니다. 또한 Blob 복사 명령을 통해 수행된 5개의 복사본도 보여 줍니다.

![테스트하기 위해 앱 서버 VM에 연결된 SAP HANA 백업 소프트웨어 RAID 디스크 복사본](media/sap-hana-backup-file-level/image033.png)

테스트하기 위해 SAP HANA 백업 소프트웨어 RAID 디스크의 복사본이 앱 서버 VM에 연결되었습니다.

![디스크 복사본을 연결하기 위해 종료된 앱 서버 VM](media/sap-hana-backup-file-level/image034.png)

디스크 복사본을 연결하기 위해 앱 서버 VM이 종료되었습니다. VM을 시작한 후 디스크와 RAID가 올바르게 검색되었습니다(UUID를 통해 탑재됨). YaST 파티셔너를 통해 만들어진 탑재 지점만 누락되었습니다. 나중에 SAP HANA 백업 파일 복사본이 OS 수준에서 표시되도록 했습니다.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>NFS 공유에 SAP HANA 백업 파일 복사

성능 또는 디스크 공간 관점에서 SAP HANA 시스템에 미치는 잠재적 영향을 줄이려면 NFS 공유에 SAP HANA 백업 파일을 저장하는 것이 좋습니다. 기술적으로는 작동하지만, 두 번째 Azure VM을 NFS 공유의 호스트로 사용한다는 것을 의미합니다. VM 네트워크 대역폭으로 인해 VM 크기가 작아지면 안됩니다. 그렇다면 이 &quot;백업 VM&quot;을 종료하고 SAP HANA 백업을 실행하기만 하면 됩니다. NFS 공유를 작성하면 네트워크에 부하가 걸리고 SAP HANA 시스템에 영향을 미치지만, 나중에 &quot;백업 VM&quot;에서 백업 파일을 관리하는 것만으로 SAP HANA 시스템에 전혀 영향을 미치지 않습니다.

![다른 Azure VM의 NFS 공유를 탑재한 SAP HANA 서버 VM](media/sap-hana-backup-file-level/image035.png)

NFS 사용 사례를 확인하기 위해 SAP HANA 서버 VM에 다른 Azure VM의 NFS 공유를 탑재했습니다. 특별한 NFS 튜닝을 적용하지 않았습니다.

![백업을 직접 수행하는 데 1시간 46분이 걸렸습니다.](media/sap-hana-backup-file-level/image036.png)

NFS 공유는 SAP HANA 서버와 같이 빠른 스트라이프 세트였습니다. 그럼에도 불구하고 로컬 스트라이프 세트에 쓸 때 NFS 공유에서 백업을 직접 수행하는 데 10분이 아닌 1시간 46분이 걸렸습니다.

![1시간 43분으로 그다지 빠르지 않은 대안](media/sap-hana-backup-file-level/image037.png)

로컬 스트라이프 세트로 백업하고 OS 수준에서 NFS 공유로 복사하는 대안(간단한 **cp -avr** 명령 사용)은 그다지 빠르지 않았습니다. 1시간 43분이 걸렸습니다.

이 방법은 효과가 있으나 230GB 백업 테스트의 성능은 좋지 않았습니다. 다중 테라바이트 크기로 작업할 경우에는 더욱 나쁠 것입니다.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Azure Files에 SAP HANA 백업 파일 복사

Azure Linux VM 내에 Azure Files 공유를 탑재할 수 있습니다. [Linux에서 Azure File Storage를 사용하는 방법](../../../storage/files/storage-how-to-use-files-linux.md) 문서에서 작업을 수행하는 방법에 대해 자세히 설명합니다. 현재 Azure 파일 공유당 5TB의 할당량 제한이 있으며, 파일당 파일 크기 제한은 1TB입니다. 스토리지 제한에 대한 내용은 [Azure Storage 확장성 및 성능 목표](../../../storage/common/storage-scalability-targets.md)를 참조하세요.

그러나 테스트한 결과 SAP HANA 백업이 현재 이러한 종류의 CIFS 탑재와 직접 작동하지 않습니다. 또한 [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529)에서도 CIFS를 권장하지 않는다고 명시하고 있습니다.

![SAP HANA Studio의 백업 대화 상자에서 오류를 보여 주는 그림](media/sap-hana-backup-file-level/image038.png)

이 그림에서는 CIFS 탑재 Azure 파일 공유에 직접 백업하려고 할 때 SAP HANA Studio의 백업 대화 상자에서 오류가 있음을 보여 줍니다. 따라서 먼저 표준 SAP HANA 백업을 VM 파일 시스템으로 수행한 다음 Azure 파일 서비스에 백업 파일을 복사해야 합니다.

![19개 SAP HANA 백업 파일을 복사하는 데 929초 정도 걸렸음을 보여 주는 그림](media/sap-hana-backup-file-level/image039.png)

이 그림에서는 총 크기가 약 230GB인 19개의 SAP HANA 백업 파일을 Azure 파일 공유에 복사하는 데 929초 정도 걸렸음을 보여 줍니다.

![SAP HANA VM의 원본 디렉터리 구조가 복사된 Azure 파일 공유](media/sap-hana-backup-file-level/image040.png)

이 스크린샷에서는 SAP HANA VM의 원본 디렉터리 구조, 즉 1개 디렉터리(hana\_backup\_fsl\_15gb) 및 19개 개별 백업 파일이 Azure 파일 공유에 복사되었음을 알 수 있습니다.

Azure파일에 SAP HANA 백업 파일을 저장할 때 SAP HANA 파일 백업에서 이를 직접 지원하는 경우 이 작업은 향후 흥미로운 옵션이 될 수 있습니다. 또는 NFS를 통해 Azure 파일을 탑재할 수 있는 경우 최대 할당량 제한이 5TB보다 훨씬 큽니다.

## <a name="next-steps"></a>다음 단계
* [Azure Virtual Machines의 SAP HANA Backup 가이드](sap-hana-backup-guide.md) - 시작에 대한 개요 및 정보를 제공합니다.
* [저장소 스냅숏에 기반한 SAP HANA 백업](sap-hana-backup-storage-snapshots.md) - 저장소 스냅숏을 기반으로 하는 백업 옵션을 설명합니다.
* [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md) - Azure의 SAP HANA(큰 인스턴스)에 대한 고가용성 및 재해 복구 계획을 설정하는 방법을 알아봅니다.
