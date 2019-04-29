---
title: Azure Backup 아키텍처
description: Azure Backup 서비스에서 사용하는 아키텍처, 구성 요소 및 프로세스에 대한 개요를 제공합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 98ffe145103b4be04014627ed04d04dcf7542015
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647404"
---
# <a name="azure-backup-architecture"></a>Azure Backup 아키텍처

사용할 수는 [Azure Backup 서비스](backup-overview.md) Microsoft Azure 클라우드 플랫폼으로 데이터를 백업 합니다. 이 문서에는 Azure Backup 아키텍처, 구성 요소 및 프로세스가 요약되어 있습니다. 

## <a name="what-does-azure-backup-do"></a>Azure Backup의 기능은 무엇인가요?

Azure Backup 데이터, 컴퓨터 상태 및 온-프레미스 컴퓨터 및 Azure VM (가상 머신) 인스턴스에서 실행 중인 워크 로드를 백업 합니다. 여러 Azure Backup 시나리오가 있습니다.

## <a name="how-does-azure-backup-work"></a>Azure Backup은 어떻게 작동하나요?

다양 한 메서드를 사용 하 여 컴퓨터 및 데이터를 백업할 수 있습니다.

- **온-프레미스 머신 백업**:
    - Azure Backup Microsoft Azure Recovery Services (MARS) 에이전트를 사용 하 여 Azure에 직접 온-프레미스 Windows 컴퓨터를 백업할 수 있습니다. Linux 컴퓨터는 지원되지 않습니다.
    - 백업 서버 (System Center Data Protection Manager (DPM) 또는 Microsoft Azure Backup Server (MABS))에 온-프레미스 컴퓨터를 백업할 수 있습니다. 그런 다음 Azure에서 Recovery Services 자격 증명 모음 백업 서버를 백업할 수 있습니다.

- **Azure VM 백업**:
    - Azure VM을 직접 백업할 수 있습니다. Azure Backup VM에서 실행 되는 Azure VM 에이전트에 백업 확장을 설치 합니다. 이 확장은 전체 VM을 백업합니다.
    - MARS 에이전트를 실행하여 Azure VM에서 특정 파일 및 폴더를 백업할 수 있습니다.
    - Azure에서 실행 되는 MABS를 Azure Vm을 백업할 수 있습니다 및 Recovery Services 자격 증명 모음 MABS를 백업할 수 있습니다.

에 대해 자세히 알아보세요 [를 백업할 수](backup-overview.md) 및 약 [지원 되는 백업 시나리오](backup-support-matrix.md)합니다.

## <a name="where-is-data-backed-up"></a>데이터는 어디에 백업되나요?

Azure Backup Recovery Services 자격 증명 모음에 백업 된 데이터를 저장합니다. 자격 증명 모음에는 백업 복사본, 복구 지점 및 백업 정책 같은 데이터를 보관 하는 데 사용 되는 Azure에서 온라인 저장소 엔터티입니다.

Recovery Services 자격 증명 모음에는 다음과 같은 기능이 있습니다.

- 자격 증명 모음을 사용하면 관리 오버헤드를 최소화하면서 백업 데이터를 쉽게 구성할 수 있습니다.
- 각 Azure 구독에서 최대 500개의 자격 증명 모음을 만들 수 있습니다.
- Azure Vm 및 온-프레미스 컴퓨터를 포함 하 여 자격 증명에서 백업한 항목을 모니터링할 수 있습니다.
- Azure [RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)를 사용하여 자격 증명 모음 액세스를 관리할 수 있습니다.
- 자격 증명 모음의 데이터가 중복성을 위해 복제되는 방법을 지정합니다.
    - **LRS(로컬 중복 스토리지)**: 데이터 센터에 오류를 방지 하려면 LRS를 사용할 수 있습니다. LRS는 스토리지 배율 단위에 데이터를 복제합니다. [자세히 알아보기](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **GRS(지역 중복 스토리지)**: 전체 지역 가동 중단을 방지 하기 GRS를 사용할 수 있습니다. GRS는 보조 지역에 데이터를 복제 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - 기본적으로 Recovery Services 자격 증명 모음 GRS를 사용 합니다. 

## <a name="backup-agents"></a>백업 에이전트

Azure Backup 다른 백업 에이전트에서는 어떤 유형의 컴퓨터에 따라 백업 되 고:

**에이전트** | **세부 정보** 
--- | --- 
**MARS 에이전트** | <ul><li>파일, 폴더 및 시스템 상태를 백업할 개별 온-프레미스 Windows Server 컴퓨터에서 실행 됩니다.</li> <li>파일, 폴더 및 시스템 상태 백업에 Azure Vm에서 실행 됩니다.</li> <li>DPM/MABS 로컬 저장소 디스크를 Azure에 백업할 DPM/MABS 서버에서 실행 됩니다.</li></ul> 
**Azure VM 확장** | 자격 증명 모음에 백업하기 위해 Azure VM에서 실행됩니다.

## <a name="backup-types"></a>Backup 유형

다음 표에서 다른 형식의 백업을 사용 하는 경우를 설명 합니다.

**백업 유형** | **세부 정보** | **사용 현황**
--- | --- | ---
**전체** | 전체 데이터 원본을 포함 하는 전체 백업입니다. 증분 또는 차등 백업 보다 더 많은 네트워크 대역폭을 사용합니다. | 초기 백업에 사용됩니다.
**차등** |  차등 백업은 초기 전체 백업 이후에 변경 된 블록을 저장 합니다. 네트워크 및 저장소를 더 작은 크기를 사용 하 고 변경 되지 않은 데이터의 중복 복사본을 유지 하지 않습니다.<br/><br/> 비효율적인 이후 백업이 간에 변경 되지 않는 데이터 블록 전송 되 고 저장 되기 때문입니다. | Azure Backup에서 사용되지 않습니다.
**증분** | 증분 백업은 이전 백업 이후에 변경 된 데이터 블록만 저장 합니다. 스토리지 및 네트워크 효율성이 높습니다. <br/><br/> 증분 백업을 사용 하 여 전체 백업으로 보완 하기 위해 않아도가 됩니다. | DPM/MABS에서 디스크 백업에 사용되며 Azure에 대한 모든 백업에서 사용됩니다.

## <a name="sql-server-backup-types"></a>SQL Server 백업 유형

다음 표에서 다양 한 SQL Server 데이터베이스와 얼마나 자주 사용 하는 데 백업:

**백업 유형** | **세부 정보** | **사용 현황**
--- | --- | ---
**전체 백업** | 전체 데이터베이스 백업은 전체 데이터베이스를 백업합니다. 특정 데이터베이스 또는 파일 그룹 또는 파일 집합의 모든 데이터를 포함 하는 것입니다. 전체 백업에는 해당 데이터를 복구 하려면 충분 한 로그 포함 되어 있습니다. | 많으면, 하루에 하나의 전체 백업을 트리거할 수 있습니다.<br/><br/> 일별 또는 주별 간격으로 전체 백업을 수행 하도록 선택할 수 있습니다.
**차등 백업** | 차등 백업은 가장 최근의 이전 전체 데이터 백업을 기반으로 합니다.<br/><br/> 이 백업은 전체 백업 이후 변경된 데이터만 캡처합니다. |  많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다.<br/><br/> 전체 백업과 차등 백업을 같은 날에 구성할 수 없습니다.
**트랜잭션 로그 백업** | 로그 백업을 사용하면 특정 시간(초 단위)까지의 특정 시점 복원이 가능합니다. | 많으면, 15분마다 트랜잭션 로그 백업을 구성할 수 있습니다.

### <a name="comparison-of-backup-types"></a>백업 유형 비교

스토리지 사용량, RTO(복구 시간 목표) 및 네트워크 사용량은 각 백업 유형마다 다릅니다. 다음 이미지에는 백업 유형의 비교를 보여 줍니다.

- 데이터 소스는 10 개의 저장소 블록 A1-A10으로 매달 백업 되는 구성 됩니다.
- 블록 A2, A3, A4 및 A9는 첫 번째 달에 변경되고 블록 A5는 다음 달에 변경됩니다.
- 차등 백업의 경우 두 번째 달에 변경된 블록 A2, A3, A4 및 A9가 백업됩니다. 세 번째 달에 이와 동일한 블록이 변경된 블록 A5와 함께 다시 백업됩니다. 변경된 블록은 다음 전체 백업이 발생할 때가지 계속 백업됩니다.
- 두 번째 달에 증분 백업에 대 한 블록 A2, A3, A4 및 A9로 표시 된 변경 및 전송 합니다. 세 번째 달에는 변경된 블록 A5만 표시되고 전송됩니다. 

![백업 방법에 대 한 비교를 보여주는 이미지](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>백업 기능

다음 표에서 다양 한 유형의 백업에 대 한 지원 되는 기능을 보여 줍니다.

**기능** | **온-프레미스 Windows Server 컴퓨터 (직접)** | **Azure VM** | **컴퓨터 또는 DPM/MABS를 사용 하 여 앱**
--- | --- | --- | ---
자격 증명 모음에 백업 | ![예][green] | ![예][green] | ![예][green] 
그런 다음 Azure에 DPM/MABS 디스크에 백업 | | | ![예][green] 
백업을 위해 전송된 데이터 압축 | ![예][green] | 데이터를 전송할 때 압축이 사용되지 않습니다. 스토리지가 약간 증가하지만 복원 속도가 더 빠릅니다.  | ![예][green] 
증분 백업 실행 |![예][green] |![예][green] |![예][green] 
중복 제거된 디스크 백업 | | | ![부분적으로][yellow]<br/><br/> 온-프레미스에 배포된 DPM/MABS 서버에만 해당합니다. 

![테이블 키](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>아키텍처: Azure VM의 직접 백업

1. Azure VM에 대 한 백업을 사용 하도록 설정 하면 지정 된 일정에 따라 백업을 실행 합니다.
1. 첫 번째 백업 하는 동안 VM을 실행 하는 경우 백업 확장은 VM에 설치 됩니다.
    - Windows vm의 경우 VMSnapshot 확장이 설치 됩니다.
    - Linux Vm의 경우 VMSnapshot Linux 확장 설치 됩니다.
1. 확장은 저장소 수준의 스냅숏을 만듭니다. 
    - 실행 하는 Windows Vm에 대 한 백업을 사용 하 여는 Windows 섀도 복사본 서비스 VSS (볼륨) vm 앱 일치 스냅숏을 조정 합니다. 기본적으로 백업에는 전체 VSS 백업을 수행합니다. Backup에서 앱 일관성이 있는 스냅숏을 만들 수 없는 경우에는 일관성 있는 파일 스냅숏을 만듭니다.
    - Linux Vm에 대 한 백업 파일에 일관 된 스냅숏을 만듭니다. 앱 일치 스냅숏을 수동으로 사전/사후 스크립트를 사용자 지정 해야 합니다.
    - 각 VM 디스크를 병렬로 백업하여 백업이 최적화됩니다. 백업 중인 각 디스크에 대해 Azure Backup은 디스크의 블록을 읽고 변경된 데이터만 저장합니다. 
1. 스냅숏을 만든 후에는 데이터가 자격 증명 모음으로 전송됩니다. 
    - 마지막 백업 복사 되므로 변경 데이터의만 차단 합니다.
    - 데이터가 암호화되지 않습니다. Azure Backup은 Azure Disk Encryption을 사용 하 여 암호화 된 Azure Vm에 백업할 수 있습니다.
    - 스냅숏 데이터가 자격 증명 모음에 즉시 복사되지 않을 수 있습니다. 사용량이 많은 시간에 백업이 몇 시간이 걸릴 수 있습니다. VM에 대 한 백업 시간을 총 일별 백업 정책의 24 시간 미만 됩니다.
1. 자격 증명 모음에 데이터를 보낸 후 스냅숏이 제거 되 고 복구 지점이 생성 됩니다.

Azure Vm에는 제어 명령에 대 한 인터넷 액세스가 필요 합니다. VM (예: SQL Server 데이터베이스 백업) 내에서 작업을 백업 하는 경우 백 엔드 데이터도 인터넷에 액세스를 해야 합니다. 

![Azure VM의 백업](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>아키텍처: 온-프레미스 Windows Server 컴퓨터 또는 Azure VM 파일 또는 폴더의 직접 백업

1. 시나리오를 설정 하려면 다운로드 하 고 컴퓨터의 MARS 에이전트를 설치 합니다. 다음 백업 대상, 백업 실행 시기 및 선택 있습니다 됩니다 수 유지 Azure에서.
1. 백업 설정에 따라 초기 백업을 실행합니다.
1. MARS 에이전트는 VSS를 사용 하 여 백업을 위해 선택한 볼륨의 특정 시점 스냅숏을 만듭니다.
    - MARS 에이전트 스냅숏을 캡처하는 Windows 시스템 쓰기 작업을 사용 합니다.
    - 에이전트는 모든 응용 프로그램 VSS 기록기를 사용 하지 않는 때문에 앱 일관성 스냅숏 포착 하지는 않습니다.
1. VSS 사용 하 여 스냅숏을 후 MARS 에이전트는 backup을 구성할 때 지정한 캐시 폴더에 가상 하드 디스크 (VHD)을 만듭니다. 에이전트는 또한 각 데이터 블록에 대 한 체크섬을 저장합니다.
1. 임시 백업을 실행 하지 않는 경우, 지정 된 일정에 따라 증분 백업을 실행 합니다.
1. 증분 백업에서는 변경된 파일이 식별되고 새 VHD가 만들어집니다. VHD는 압축 및 암호화 및 다음 자격 증명 모음으로 전송 됩니다.
1. 증분 백업이 완료 되 면 새 VHD는 초기 복제 후 만든 VHD를 사용 하 여 병합 됩니다. 병합된이 VHD는 진행 중인 백업에 대 한 비교에 사용 되는 최신 상태를 제공 합니다.

![MARS 에이전트를 사용 하 여 온-프레미스 Windows Server 컴퓨터의 백업](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>아키텍처: DPM/MABS에 백업

1. 보호 하려는 컴퓨터에서 DPM 또는 MABS 보호 에이전트를 설치 합니다. 그런 다음 DPM 보호 그룹에 컴퓨터를 추가 합니다.
    - 온-프레미스 머신을 보호하려면 DPM 또는 MABS 서버가 온-프레미스에 있어야 합니다.
    - Azure VM을 보호하려면 Azure VM으로 실행되는 MABS 서버가 Azure에 있어야 합니다.
    - DPM/MABS를 사용 하면 백업 볼륨, 공유, 파일 및 폴더를 보호할 수 있습니다. 컴퓨터의 시스템 상태 (완전 복구)를 보호할 수 있습니다 하 고 응용 프로그램 인식 백업 설정을 사용 하 여 특정 앱을 보호할 수 있습니다.
1. 컴퓨터 또는 DPM/MABS에서 앱에 대 한 보호를 설정 하는 경우 단기 저장용 MABS/DPM 로컬 디스크 및 온라인 보호를 위해 Azure를 백업 하려면 선택 합니다. 로컬 DPM/MABS 저장소에 백업 실행 시기 및 Azure로 온라인 백업 작업을 실행할지 지정할 수도 있습니다.
1. 보호 되는 작업의 디스크는 지정한 일정에 따라 로컬 MABS/DPM 디스크로 백업 됩니다.
4. DPM/MABS 디스크는 DPM/MABS 서버에서 실행 되는 MARS 에이전트에서 자격 증명 모음에 백업 됩니다.

![컴퓨터 및 DPM 또는 MABS 보호 하는 워크 로드 백업](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM 스토리지

Azure VM은 디스크를 사용하여 운영 체제, 앱 및 데이터를 저장합니다. 각 Azure VM에 두 개 이상의 디스크가: 운영 체제와 임시 디스크에 대 한 디스크입니다. Azure Vm에서 앱 데이터에 대 한 데이터 디스크를 가질 수도 있습니다. 디스크는 VHD로 저장됩니다.

- Vhd는 Azure에서 표준 또는 프리미엄 저장소 계정에 페이지 blob으로 저장 됩니다.
    - **표준 저장소:** 대기 시간이 중요하지 않은 워크로드를 실행하는 VM에 대한 신뢰할 수 있는 저비용 디스크 지원입니다. 표준 저장소는 표준 반도체 드라이브 (SSD) 디스크 또는 표준 하드 디스크 드라이브 (HDD) 디스크에 사용할 수 있습니다.
    - **Premium storage:** 고성능 디스크 지원입니다. 프리미엄 SSD 디스크를 사용합니다.
- 디스크에는 다음과 같은 여러 성능 계층이 있습니다.
    - **표준 HDD 디스크:** HDD를 통해 지원되며 경제적인 스토리지에 사용됩니다.
    - **표준 SSD 디스크:** Premium SSD와 HDD 디스크를 표준의 요소를 결합합니다. 더 일관 된 성능과 HDD 보다 하지만 여전히 비용 효율적인 안정성을 제공합니다.
    - **Premium SSD 디스크:** Ssd에 의해 지원 및 O 집약적 워크 로드를 실행 중인 Vm에 대 한 고성능 및 짧은 대기 시간을 제공 합니다.
- 다음과 같은 관리 또는 비관리 디스크가 있습니다.
    - **관리 되지 않는 디스크:** Vm에서 사용 되는 디스크의 기존 형식입니다. 이러한 디스크의 경우 사용자 고유의 스토리지 계정을 만든 다음, 디스크를 만들 때 지정합니다. 그런 다음 Vm에 대 한 저장소 리소스를 극대화 하는 방법을 파악 해야 합니다.
    - **관리형 디스크:** Azure가 만들고 저장소 계정을 관리 합니다. 디스크 크기와 성능 계층을 지정 하 고 Azure를 관리 되는 디스크를 만듭니다. 디스크를 추가 하 고 Vm 크기 조정, Azure 저장소 계정을 처리 합니다.

Vm에 대 한 디스크 저장소 및 사용 가능한 디스크 유형에 대 한 자세한 내용은 다음이 문서를 참조 하세요.

- [Windows Vm 용 azure 관리 디스크](../virtual-machines/windows/managed-disks-overview.md)
- [Linux Vm 용 azure 관리 디스크](../virtual-machines/linux/managed-disks-overview.md)
- [Vm에 대 한 사용 가능한 디스크 형식](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>백업 하 고 premium storage를 사용 하 여 Azure Vm 복원 

Azure Backup을 사용 하 여 premium storage를 사용 하 여 Azure Vm을 백업할 수 있습니다.

- Premium storage Vm 백업 과정에서 Backup 서비스는 이라는 임시 준비 위치를 만듭니다 *AzureBackup-*, 저장소 계정에 있습니다. 준비 위치의 크기를 복구 지점 스냅숏의 크기를 같습니다.
- Premium Storage 계정에 임시 준비 위치를 수용할 충분한 여유 공간이 있어야 합니다. [자세히 알아보기](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits). 준비 위치를 수정하지 마세요.
- 백업 작업이 완료되면 준비 위치가 삭제됩니다.
- 준비 위치에 사용되는 스토리지의 가격은 모든 [Premium Storage 가격 책정](../virtual-machines/windows/disks-types.md#billing)과 일관성이 있습니다.

Premium storage를 사용 하 여 Azure Vm을 복원한 경우에 프리미엄 또는 표준 저장소로 복원할 수 있습니다. 일반적으로 premium storage로 해당 복원할 수 있습니다. 하지만 VM에서 파일의 하위 집합만 할 경우 표준 저장소에 복원 하는 비용 효과적일 수 있습니다.

### <a name="back-up-and-restore-managed-disks"></a>백업 및 복원 관리 디스크

관리 디스크를 사용 하 여 Azure Vm을 백업할 수 있습니다.

- 다른 Azure VM과 동일한 방식으로 관리 디스크를 사용하여 VM을 백업합니다. 가상 머신 설정에서 직접 VM을 백업하거나, Recovery Services 자격 증명 모음에서 VM에 대한 백업을 사용하도록 설정할 수 있습니다.
- Managed Disks에 VM 백업은 Managed Disks를 기반으로 구축된 RestorePoint 컬렉션을 통해 지원됩니다.
- Azure Backup은 Azure Disk Encryption을 사용 하 여 암호화 된 managed disks가 있는 Vm을 백업도 지원 합니다.

관리 디스크가 있는 Vm을 복원 하면 관리 디스크를 사용 하 여 전체 VM 또는 저장소 계정을 복원할 수 있습니다.

- 복원 과정에서 Azure managed disks를 처리합니다. 저장소 계정 옵션을 사용 하는 경우 복원 프로세스 중에 만든 저장소 계정을 관리 합니다.
- 암호화 된 관리 되는 VM을 복원 하는 경우 VM의 키를 확인 한 복원 프로세스를 시작 하기 전에 key vault에 비밀 존재 합니다.

## <a name="next-steps"></a>다음 단계

- 지원 매트릭스를 검토 [지원 되는 기능 및 백업 시나리오에 대 한 제한 사항을 알아봅니다](backup-support-matrix.md)합니다.
- 이러한 시나리오 중 하나에 대 한 백업을 설정 합니다.
    - [Azure Vm 백업](backup-azure-arm-vms-prepare.md)합니다.
    - 백업 서버를 사용하지 않고 [Windows 머신을 직접 백업](tutorial-backup-windows-server-to-azure.md)합니다.
    - Azure에 백업하도록 [MABS를 설정](backup-azure-microsoft-azure-backup.md)한 다음, 워크로드를 MABS에 백업합니다.
    - Azure에 백업하도록 [DPM을 설정](backup-azure-dpm-introduction.md)한 다음, 워크로드를 DPM에 백업합니다.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

