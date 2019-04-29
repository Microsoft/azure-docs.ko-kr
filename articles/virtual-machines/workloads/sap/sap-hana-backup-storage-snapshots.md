---
title: 저장소 스냅숏에 기반한 SAP HANA Azure 백업 | Microsoft Docs
description: Azure 가상 머신에는 SAP HANA에 대한 두 가지 주요 백업 방법이 있습니다. 이 문서에서는 저장소 스냅숏에 기반한 SAP HANA 백업에 대해 설명합니다
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
ms.openlocfilehash: 74f47344afff630a8633b340ea4ce21db28db7ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60936604"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>저장소 스냅숏에 기반한 SAP HANA 백업

## <a name="introduction"></a>소개

이 문서는 3부로 구성된 SAP HANA 백업 관련 문서 시리즈의 일부입니다. [Azure Virtual Machines의 SAP HANA 백업 가이드](sap-hana-backup-guide.md)에서 시작에 대한 개요와 정보를 제공하고, [파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md)에서 파일 기반 백업 옵션을 설명합니다.

단일 인스턴스 통합(all-in-one) 데모 시스템에 VM 백업 기능을 사용하는 경우 OS 수준에서 HANA 백업을 관리하는 대신 VM 백업을 수행하는 것이 좋습니다. 다른 방법으로는 Azure Blob 스냅숏을 만들어 가상 머신에 연결된 개별 가상 디스크의 복사본을 만들고 HANA 데이터 파일을 유지하는 것입니다. 그러나 중요한 점은 시스템 가동 중에 VM 백업 또는 디스크 스냅숏을 만들 때 앱 일관성을 유지하는 것입니다. [Azure Virtual Machines의 SAP HANA Backup 가이드](sap-hana-backup-guide.md) 관련 문서의 _저장소 스냅숏을 만들 때의 SAP HANA 데이터 일관성_을 참조하세요. SAP HANA에는 이러한 종류의 저장소 스냅숏을 지원하는 기능이 있습니다.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>애플리케이션 일치 백업의 중심 부분인 SAP HANA 스냅숏

SAP HANA에는 저장소 스냅숏 만들기를 지원하는 기능이 있습니다. 단일 컨테이너 시스템에는 제한이 있습니다. 둘 이상의 테넌트가 포함된 SAP HANA MCS를 다루는 시나리오에서는 이러한 종류의 SAP HANA 데이터베이스 스냅숏을 지원하지 않습니다([Create a Storage Snapshot (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)(스토리지 스냅숏 만들기(SAP HANA Studio)) 참조).

다음과 같이 작동합니다.

- SAP HANA 스냅숏을 시작하여 저장소 스냅숏 준비
- 저장소 스냅숏 실행(예: Azure Blob 스냅숏)
- SAP HANA 스냅숏 확인

![SQL 문을 통해 SAP HANA 데이터 스냅숏을 만들 수 있음을 보여 주는 스크린샷](media/sap-hana-backup-storage-snapshots/image011.png)

이 스크린샷에서는 SQL 문을 통해 SAP HANA 데이터 스냅숏을 만들 수 있음을 보여 줍니다.

![SAP HANA Studio의 백업 카탈로그에 표시된 스냅숏](media/sap-hana-backup-storage-snapshots/image012.png)

또한 스냅숏은 SAP HANA Studio의 백업 카탈로그에도 표시됩니다.

![디스크의 SAP HANA 데이터 디렉터리에 표시된 스냅숏](media/sap-hana-backup-storage-snapshots/image013.png)

스냅숏은 디스크의 SAP HANA 데이터 디렉터리에 표시됩니다.

SAP HANA가 스냅숏 준비 모드에 있는 동안 저장소 스냅숏을 실행하기 전에 파일 시스템 일관성도 보장되는지 확인해야 합니다. [Azure Virtual Machines의 SAP HANA Backup 가이드](sap-hana-backup-guide.md) 관련 문서의 _저장소 스냅숏을 만들 때의 SAP HANA 데이터 일관성_을 참조하세요.

저장소 스냅숏이 완료되면 SAP HANA 스냅숏을 확인하는 것이 중요합니다. 실행할 SQL 문은 BACKUP DATA CLOSE SNAPSHOT입니다([BACKUP DATA CLOSE SNAPSHOT 문(백업 및 복구)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm) 참조).

> [!IMPORTANT]
> HANA 스냅숏을 확인합니다. &quot;Copy-on-Write&quot;로 인해 SAP HANA에는 스냅숏 준비 모드에서 추가 디스크 공간이 필요할 수 있으며, 먼저 SAP HANA 스냅숏이 확인되어야 새 백업을 시작할 수 있습니다.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Azure Backup 서비스를 통한 HANA VM 백업

Azure Backup 서비스의 백업 에이전트는 Linux VM에서 사용할 수 없습니다. 또한 Windows에서 VSS를 통해 제공하는 것과 유사한 기능이 Linux에는 없습니다.  파일/디렉터리 수준에서 Azure Backup을 사용하려면 SAP HANA 백업 파일을 Windows VM에 복사한 다음 백업 에이전트를 사용합니다. 

그렇지 않으면 전체 Linux VM 백업만 Azure Backup 서비스를 통해 수행할 수 있습니다. 자세한 내용은 [Azure Backup의 기능에 대한 개요](../../../backup/backup-introduction-to-azure-backup.md)를 참조하세요.

Azure Backup 서비스는 VM을 백업하고 복원하는 옵션을 제공합니다. 이 서비스 및 작동 방법에 대한 자세한 내용은 [Azure에서 VM 백업 인프라 계획](../../../backup/backup-azure-vms-introduction.md) 문서에 있습니다.

이 문서에 따라 다음 두 가지의 중요한 고려 사항이 있습니다.

_&quot;Linux 가상 컴퓨터의 경우 VSS에 해당하는 플랫폼이 Linux에 없기 때문에 파일 일치 백업만 가능합니다.&quot;_

_&quot;애플리케이션은 복원된 데이터에 대해 고유한 &quot;수정&quot; 메커니즘을 구현해야 합니다.&quot;_

따라서 백업이 시작될 때 SAP HANA가 일관된 디스크 상태에 있는지 확인해야 합니다. 이 문서의 앞부분에서 설명한 _SAP HANA 스냅숏_을 참조하세요. 그러나 SAP HANA에서 이 스냅숏 준비 모드를 유지할 때 잠재적인 문제가 있습니다. 자세한 내용은 [저장소 스냅숏 만들기(SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)(영문)을 참조하세요.

이 문서에서는 다음과 같이 설명하고 있습니다.

_&quot;저장소 스냅숏을 만든 후 가능한 한 빨리 확인하거나 중단하는 것이 좋습니다. 저장소 스냅숏을 준비하거나 만드는 동안 스냅숏 관련 데이터는 고정(freeze)됩니다. 스냅숏 관련 데이터가 고정된 상태에서도 데이터베이스에서 변경할 수 있습니다. 이러한 변경으로 인해 고정된 스냅숏 관련 데이터가 변경되지는 않습니다. 대신 변경 내용은 저장소 스냅숏과 별도로 데이터 영역의 위치에 기록됩니다. 변경 내용은 로그에도 기록됩니다. 그러나 스냅숏 관련 데이터가 고정된 상태로 유지될수록 데이터 볼륨이 커질 수 있습니다.&quot;_

Azure Backup은 Azure VM 확장을 통해 파일 시스템 일관성을 처리합니다. 이러한 VM 확장은 독립 실행형으로 제공되지 않으며 Azure Backup 서비스와 함께 사용해야 합니다. 그럼에도 불구하고 SAP HANA 스냅숏을 만들고 삭제하는 스크립트를 제공하여 앱 일관성을 보장해야 합니다.

Azure Backup에는 다음 네 가지 주요 단계가 있습니다.

- 준비 스크립트 실행 - 스크립트가 SAP HANA 스냅숏을 만들어야 함
- 스냅숏 만들기
- 사후 스냅숏 스크립트 실행 - 스크립트가 준비 스크립트에서 생성된 SAP HANA를 삭제해야 함
- 자격 증명 모음에 데이터 전송

이러한 스크립트를 복사할 위치 및 Azure Backup의 정확한 작동 방식에 대한 자세한 내용은 다음 문서를 확인하세요.

- [Azure에서 VM 백업 인프라 계획](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Azure Linux VM의 애플리케이션 일치 백업](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



이 시점에 Microsoft는 SAP HANA용으로 준비 스크립트 및 사후 스냅숏 스크립트를 게시하지 않았습니다. 고객 또는 시스템 통합자가 해당 스크립트를 만들고 위에 참조된 문서를 기반으로 절차를 구성해야 합니다.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>VM에 대한 애플리케이션 일치 백업에서 복원
Azure Backup에서 사용된 애플리케이션 일치 백업의 복원 프로세스는 [Azure Virtual Machine 백업에서 파일 복구](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) 문서에 설명되어 있습니다. 

> [!IMPORTANT]
> [Azure Virtual Machine 백업에서 파일 복구](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) 문서에는 디스크 스트라이프 집합을 사용할 때 나열된 예외 및 단계 목록이 있습니다. 스트라이프 디스크는 SAP HANA용 일반 VM 구성일 수 있습니다. 따라서 문서를 읽고 문서에 나열된 경우에 대한 복원 프로세스를 테스트해야 합니다. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Azure Backup 서비스를 통한 HANA 라이선스 키 및 VM 복원

Azure Backup 서비스는 복원 중에 새 VM을 만들도록 설계되었습니다. 현재는 기존 Azure VM의 &quot;내부&quot; 복원을 수행하는 계획이 없습니다.

![그림: Azure Portal의 Azure 서비스 복원 옵션](media/sap-hana-backup-storage-snapshots/image019.png)

이 그림에서는 Azure Portal에서 Azure 서비스의 복원 옵션을 보여 줍니다. 복원 중에 VM 만들기와 디스크 복원 중에서 선택할 수 있습니다. 디스크를 복원한 후에도 그 위에 새 VM을 만들어야 합니다. Azure에서 새 VM을 만들었을 때마다 고유한 VM ID가 변경됩니다([Azure VM 고유 ID 액세스 및 사용](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)(영문) 참조).

![그림: Azure Backup 서비스를 통한 복원 전후의 Azure VM 고유 ID](media/sap-hana-backup-storage-snapshots/image020.png)

이 그림에서는 Azure Backup 서비스를 통한 복원 전후의 Azure VM 고유 ID를 보여 줍니다. 이 고유한 VM ID는 SAP 라이선스에 사용되는 SAP 하드웨어 키에 사용됩니다. 따라서 VM을 복원한 후에는 새 SAP 라이선스를 설치해야 합니다.

이 백업 가이드를 만드는 중에 미리 보기 모드에서 새로운 Azure Backup 기능이 제공되었습니다. 여기서는 VM 백업을 위해 만든 VM 스냅숏을 기반으로 하여 파일 수준에서 복원할 수 있습니다. 이렇게 하면 새 VM을 배포할 필요가 없으므로 고유한 VM ID가 그대로 유지되며 새로운 SAP HANA 라이선스 키도 필요하지 않습니다. 이 기능에 대한 자세한 설명서는 완전히 테스트한 후에 제공될 것입니다.

결국에는 Azure Backup에서 개별 Azure 가상 디스크 및 VM 내의 파일과 디렉터리를 백업하도록 할 것입니다. Azure Backup의 가장 큰 장점은 모든 백업을 관리하여 고객이 백업하지 않아도 된다는 것입니다. 복원이 필요할 경우 Azure Backup에서 올바른 백업을 사용하도록 선택합니다.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>수동 디스크 스냅숏을 통한 SAP HANA VM 백업

Azure Backup 서비스를 사용하는 대신 Azure VHD의 Blob 스냅숏을 PowerShell을 통해 수동으로 만들어 개별 백업 솔루션을 구성할 수 있습니다. 단계에 대한 설명은 [PowerShell과 함께 Blob 스냅숏 사용](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)(영문)을 참조하세요.

이 방법은 더욱 유연하지만 다음과 같이 이 문서의 앞부분에서 설명한 문제를 해결하지는 않습니다.

- SAP HANA 스냅숏을 만들어 SAP HANA가 일치 상태인지 확인해야 합니다.
- 임대가 있다고 알리는 오류로 인해 VM 할당이 취소된 경우에도 OS 디스크를 덮어쓸 수 없습니다. VM을 삭제한 후에만 작동하므로 새 고유 VM ID를 가져오고 새 SAP 라이선스를 설치해야 합니다.

![Azure VM의 데이터 디스크만 복원할 수 있습니다.](media/sap-hana-backup-storage-snapshots/image021.png)

다음과 같이 Azure VM의 데이터 디스크만 복원하여 새 고유 VM ID를 가져오는 문제를 방지하므로 SAP 라이선스를 무효화할 수 있습니다.

- 테스트를 위해 두 개의 Azure 데이터 디스크를 VM에 연결하고 그 위에 소프트웨어 RAID를 정의했습니다. 
- SAP HANA 스냅숏 기능을 통해 SAP HANA가 일관된 상태에 있음을 확인했습니다.
- 파일 시스템을 고정했습니다([Azure Virtual Machines의 SAP HANA Backup 가이드](sap-hana-backup-guide.md) 관련 문서의 _저장소 스냅숏을 만들 때의 SAP HANA 데이터 일관성_ 참조).
- 두 데이터 디스크에서 Blob 스냅숏을 만들었습니다.
- 파일 시스템을 고정 취소했습니다.
- SAP HANA 스냅숏을 확인했습니다.
- 데이터 디스크를 복원하기 위해 VM을 종료하고 두 디스크를 분리했습니다.
- 디스크를 분리한 후 이전의 Blob 스냅숏으로 덮어썼습니다.
- 그런 다음 복원된 가상 디스크를 VM에 다시 연결했습니다.
- VM을 시작한 후 소프트웨어 RAID의 모든 파일이 잘 작동하고 Blob 스냅숏 시간으로 다시 설정했습니다.
- HANA를 HANA 스냅숏으로 다시 설정했습니다.

Blob 스냅숏 이전에 SAP HANA를 종료할 수 있다면 절차가 덜 복잡하게 됩니다. 이 경우 HANA 스냅숏을 건너뛸 수 있으며, 시스템에서 다른 작업이 수행되고 있지 않으면 파일 시스템 고정도 건너뛸 수 있습니다. 모든 것이 온라인 상태인 동안에 스냅숏을 만들어야 하는 경우 더 복잡하게 됩니다. [Azure Virtual Machines의 SAP HANA Backup 가이드](sap-hana-backup-guide.md) 관련 문서의 _저장소 스냅숏을 만들 때의 SAP HANA 데이터 일관성_을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Virtual Machines의 SAP HANA Backup 가이드](sap-hana-backup-guide.md) - 시작에 대한 개요 및 정보를 제공합니다.
* [파일 수준 기반 SAP HANA 백업](sap-hana-backup-file-level.md)에서는 파일 기반 백업 옵션에 대해 다룹니다.
* [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md) - Azure의 SAP HANA(큰 인스턴스)에 대한 고가용성 및 재해 복구 계획을 설정하는 방법을 알아봅니다.
