---
title: Azure VM Backup의 지원 매트릭스
description: Azure Backup 서비스를 사용하여 Azure VM을 백업할 때의 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 990e97274d9e35201dfb7930167dc4f9da975d83
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988127"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM Backup의 지원 매트릭스

[Azure Backup 서비스](backup-overview.md) 를 사용 하 여 온-프레미스 컴퓨터 및 워크 로드와 Azure vm (가상 머신)을 백업할 수 있습니다. 이 문서에서는 Azure Backup를 사용 하 여 Azure Vm을 백업할 때 지원 설정 및 제한 사항을 요약 합니다.

기타 지원 매트릭스:

- Azure Backup에 대한 [일반 지원 매트릭스](backup-support-matrix.md)
- Azure Backup server/System Center Data Protection Manager (DPM) 백업에 대한 [지원 매트릭스](backup-support-matrix-mabs-dpm.md)
- Microsoft Azure Recovery Services (MARS) 에이전트를 사용 하 여 백업을 위한 [지원 매트릭스](backup-support-matrix-mars-agent.md)

## <a name="supported-scenarios"></a>지원되는 시나리오

다음은 Azure Backup 서비스를 사용하여 Azure VM을 백업 및 복원하는 방법입니다.

**시나리오** | **Backup** | **에이전트** |**복원**
--- | --- | --- | ---
Azure VM의 직접 백업  | 전체 VM을 백업 합니다.  | Azure VM에 추가 에이전트가 필요 하지 않습니다. Azure Backup는 VM에서 실행 되는 [AZURE vm 에이전트](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) 에 대한 확장을 설치 하 고 사용 합니다. | 다음과 같이 복원합니다.<br/><br/> - **기본 VM 만들기**. VM에 여러 IP 주소와 같은 특수 구성이 없는 경우에 유용 합니다.<br/><br/> - **VM 디스크 복원**. 디스크를 복원합니다. 그런 다음 기존 VM에 연결 하거나 PowerShell을 사용 하 여 디스크에서 새 VM을 만듭니다.<br/><br/> - **VM 디스크 바꾸기**. VM이 있으며 Managed Disks(암호화되지 않음)를 사용하는 경우 디스크를 복원하고 VM에서 기존 디스크 대신 사용할 수 있습니다.<br/><br/> - **특정 파일/폴더 복원**. 전체 VM이 아닌 VM에서 파일/폴더를 복원할 수 있습니다.
Azure Vm의 직접 백업 (Windows에만 해당)  | 특정 파일/폴더/볼륨을 백업 합니다. | [Azure Recovery Services 에이전트](backup-azure-file-folder-backup-faq.md)를 설치 합니다.<br/><br/> 파일/폴더 수준에서 VM을 백업하려면 Azure VM 에이전트에 대한 백업 확장과 함께 MARS 에이전트를 실행할 수 있습니다. | 특정 폴더/파일을 복원합니다.
Backup server에 Azure VM 백업  | 파일/폴더/볼륨 백업 시스템 상태/운영 체제 미 설치 파일 응용 프로그램 데이터를 System Center DPM 또는 MABS (Microsoft Azure Backup 서버)에 연결할 수 있습니다.<br/><br/> 그런 다음 DPM/MABS는 백업 자격 증명 모음에 백업 합니다. | VM에 DPM/MABS 보호 에이전트를 설치 합니다. MARS 에이전트는 DPM/MABS에 설치됩니다.| 파일/폴더/볼륨, 시스템 상태/완전 파일 및 앱 데이터를 복원합니다.

백업 [서버를 사용 하는](backup-architecture.md#architecture-back-up-to-dpmmabs) 백업 및 [지원 요구 사항](backup-support-matrix-mabs-dpm.md)에 대해 자세히 알아보세요.

>[!NOTE]
> **이제 Azure Backup는 Azure 가상 컴퓨터 백업 솔루션을 사용 하 여 선택적 디스크 백업 및 복원을 지원 합니다.**
>
>현재 Azure Backup에서는 가상 컴퓨터 백업 솔루션을 사용 하 여 VM의 모든 디스크 (운영 체제 및 데이터)를 백업 하는 작업을 지원 합니다. 디스크 제외 기능을 사용 하면 VM의 여러 데이터 디스크에서 하나 또는 몇 개를 백업 하는 옵션을 사용할 수 있습니다. 이는 백업 및 복원 요구에 효율적이 고 비용 효율적인 솔루션을 제공 합니다. 각 복구 지점에는 백업 작업에 포함 된 디스크의 데이터가 포함 되어 있으므로 복원 작업을 수행 하는 동안 지정 된 복구 지점에서 복원 된 디스크의 하위 집합을 사용할 수 있습니다. 이는 스냅숏과 자격 증명 모음 둘 다의 복원에 적용 됩니다.
>
>**미리 보기에 등록 하려면 AskAzureBackupTeam@microsoft.com에 씁니다.**

## <a name="supported-backup-actions"></a>지원되는 백업 작업

**동작** | **지원**
--- | ---
Microsoft Azure VM을 만들 때 백업 사용 | 지원 대상: <br/><br/> -Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> -Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> -Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> -Windows Server 2008 R2 (RTM 및 SP1 Standard)
Linux VM을 만들 때 백업 사용 | 지원 대상:<br/><br/> -Ubuntu 서버: 18.04, 17.10, 17.04, 16.04 (LTS), 14.04 (LTS)<br/><br/> -Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> -SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> -Debian: 8, 9<br/><br/> -CentOS: 6.9, 7.3<br/><br/> -Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
종료/오프 라인 VM 인 VM 백업 | 지원됩니다.<br/><br/> 스냅샷이 충돌 일치 특성만 있고 앱 일치 특성은 없습니다.
관리 디스크로 마이그레이션한 후 디스크 백업 | 지원됩니다.<br/><br/> 백업이 계속 작동합니다. 아무런 작업도 필요하지 않습니다.
리소스 그룹 잠금을 사용하도록 설정한 후 Managed Disks를 백업합니다. | 지원되지 않습니다.<br/><br/> 이전 복원 지점은 삭제할 수 없는 Azure Backup 복원 지점의 최대 제한에 도달 하면 백업이 실패 하기 시작 합니다.
VM의 백업 정책 수정 | 지원됩니다.<br/><br/> 새 정책의 일정 및 보존 설정을 사용 하 여 VM을 백업 합니다. 보존 설정이 연장될 경우 기존 복구 지점이 표시되고 유지됩니다. 축소 된 경우 기존 복구 지점은 다음 정리 작업에서 정리 되 고 최종적으로 삭제 됩니다.
백업 작업 취소| 스냅샷 프로세스 동안 지원됩니다.<br/><br/> 스냅샷이 자격 증명 모음으로 전송될 때는 지원되지 않습니다.
다른 지역 또는 구독에 VM 백업 |지원되지 않습니다.
매일 백업(Azure VM 확장을 통해) | 매일 1회 백업 예약<br/><br/>Azure Backup 서비스는 하루에 최대 9 번의 주문형 백업을 지원 하지만 최상의 성능을 보장 하기 위해 최대 4 개의 매일 주문형 백업을 지원 하지 않는 것이 좋습니다.
매일 백업(MARS 에이전트를 통해) | 매일 3회 백업 예약
매일 백업(DPM/MABS를 통해) | 매일 2회 백업 예약
월별/연도별 백업| Azure VM 확장을 사용하여 백업하는 경우에는 지원되지 않습니다. 일별 및 주별만 지원됩니다.<br/><br/> 월별/연도별 보존 기간에 대해 일별/주별 백업을 유지하도록 정책을 설정할 수 있습니다.
자동 클록 조정 | 지원되지 않습니다.<br/><br/> VM을 백업 하는 경우 일광 절약 시간제 변경 내용에 대한 Azure Backup 자동으로 조정 되지 않습니다.<br/><br/>  필요에 따라 정책을 수동으로 수정합니다.
[하이브리드 백업에 대한 보안 기능](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |보안 기능을 사용 하지 않도록 설정할 지원 되지 않습니다.
컴퓨터 시간이 변경 된 VM 백업 | 지원되지 않습니다.<br/><br/> 해당 VM에 대한 백업을 사용 하도록 설정한 후 컴퓨터 시간이 이후 날짜-시간으로 변경 되는 경우 그러나 시간 변경이 되돌아간 경우에도 성공적인 백업이 보장 되지 않습니다.  

## <a name="operating-system-support-windows"></a>운영 체제 지원(Windows)

다음 표에서는 Windows Azure Vm을 백업할 때 지원 되는 운영 체제를 요약 합니다.

**시나리오** | **OS 지원**
--- | ---
Azure VM 에이전트 확장을 사용하는 백업 | -Windows 10 클라이언트 (64 비트만 해당) <br/><br/>-Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> -Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> -Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> -Windows Server 2008 R2 (RTM 및 SP1 Standard)
MARS 에이전트를 사용하여 백업 | [지원되는](backup-support-matrix-mars-agent.md#support-for-direct-backups) 운영 체제
DPM/MABS를 사용 하 여 백업 | [MABS](backup-mabs-protection-matrix.md) 및 [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807)을 사용하는 백업이 지원되는 운영 체제

Azure Backup은 32비트 운영 체제를 지원하지 않습니다.

## <a name="support-for-linux-backup"></a>Linux 백업 지원

Linux 머신을 백업하려는 경우 지원되는 사항은 다음과 같습니다.

**동작** | **지원**
--- | ---
Linux Azure VM 에이전트를 사용하여 Linux Azure VM 백업 | 파일 일치 백업입니다.<br/><br/> [사용자 지정 스크립트](backup-azure-linux-app-consistent.md)를 사용하는 앱 일치 백업입니다.<br/><br/> 복원 중에는 새 VM을 만들고, 디스크를 복원 하 고이를 사용 하 여 VM을 만들거나, 디스크를 복원 하 고이를 사용 하 여 기존 VM의 디스크를 교체할 수 있습니다. 또한 개별 파일 및 폴더를 복원할 수도 있습니다.
MARS 에이전트를 사용하여 Linux Azure VM 백업 | 지원되지 않습니다.<br/><br/> MARS 에이전트는 Windows 머신에만 설치할 수 있습니다.
DPM/MABS를 사용하여 Linux Azure VM 백업 | 지원되지 않습니다.

## <a name="operating-system-support-linux"></a>운영 체제 지원(Linux)

Azure VM Linux 백업의 경우 Azure Backup은 [Azure 인증 Linux 배포 목록](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)을 지원합니다. 다음 사항에 유의하세요.

- Azure Backup은 Core OS Linux를 지원하지 않습니다.
- Azure Backup은 32비트 운영 체제를 지원하지 않습니다.
- VM에서 [linux 용 AZURE vm 에이전트](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 를 사용할 수 있고 Python이 지원 되는 경우에는 다른 사용자의 linux 배포가 작동할 수 있습니다.
- Azure Backup는 Python 버전 2.7이 설치 되어 있지 않은 경우 프록시 구성 Linux VM을 지원 하지 않습니다.

## <a name="backup-frequency-and-retention"></a>백업 빈도 및 보존

**설정** | **제한**
--- | ---
보호 된 인스턴스당 최대 복구 시점 (컴퓨터/작업) | 9999.
복구 지점에 대한 최대 만료 시간 | 제한이 없습니다.
자격 증명 모음에 대한 최대 백업 빈도(Azure VM 확장) | 하루에 한 번
자격 증명 모음에 대한 최대 백업 빈도(MARS 에이전트) | 매일 3회 백업
DPM/MABS에 대한 최대 백업 빈도 | SQL Server에 대해 15 분 마다<br/><br/> 다른 작업에 대해 한 시간에 한 번
복구 지점 보존 | 매일, 매주, 매월 및 매년
최대 보존 기간 | 백업 빈도에 따라 다름
DPM/MABS 디스크의 복구 지점 수 | 파일 서버의 경우 64이 고, 앱 서버의 경우 448입니다.<br/><br/> 온-프레미스 DPM에 대한 테이프 복구 지점 수는 제한되지 않습니다.

## <a name="supported-restore-methods"></a>지원되는 복원 방법

**복원 방법** | **세부 정보**
--- | ---
새 VM 만들기 | 복원 프로세스 동안 VM을 만들 수 있습니다. <br/><br/> 이 옵션은 기본 VM을 작동 및 실행합니다. VM 이름, 리소스 그룹, 가상 네트워크, 서브넷 및 스토리지를 지정할 수 있습니다.  
디스크 복원 | 디스크를 복원 하 고이를 사용 하 여 VM을 만들 수 있습니다.<br/><br/> 이 옵션을 선택하면 Azure Backup은 자격 증명 모음의 데이터를 선택한 스토리지 계정으로 복사합니다. 복원 작업은 템플릿을 생성합니다. 이 템플릿을 다운로드 하 고,이 템플릿을 사용 하 여 사용자 지정 VM 설정을 지정 하 고, VM을 만들 수 있습니다.<br/><br/> 이 옵션을 사용하면 이전 옵션보다 더 많은 설정을 지정하여 VM을 만들 수 있습니다.<br/><br/>
기존 디스크 바꾸기 | 디스크를 복원하고 복원된 디스크를 사용하여 현재 VM에 있는 디스크를 바꿀 수 있습니다.
파일 복원 | 선택한 복구 지점에서 파일을 복구할 수 있습니다. 복구 지점에서 VM 디스크를 탑재하는 스크립트를 다운로드합니다. 그런 다음 디스크 볼륨을 탐색 하 여 복구 하려는 파일/폴더를 찾고 완료 되 면 디스크를 분리 합니다.

## <a name="support-for-file-level-restore"></a>파일 수준 복원에 대한 지원

**복원** | **지원됨**
--- | ---
운영 체제에서 파일 복원 | 백업된 VM과 동일한(또는 호환되는) OS가 있는 모든 컴퓨터에서 파일을 복원할 수 있습니다. [호환 되는 OS 표](backup-azure-restore-files-from-vm.md#system-requirements)를 참조 하세요.
클래식 VM에서 파일 복원 | 지원되지 않습니다.
암호화된 VM에서 파일 복원 | 지원되지 않습니다.
네트워크 제한 스토리지 계정에서 파일 복원 | 지원되지 않습니다.
Windows 스토리지 공간을 사용하여 VM에서 파일 복원 | 동일한 VM에서 복원이 지원되지 않습니다.<br/><br/> 대신, 호환되는 VM에서 파일을 복원합니다.
LVM/RAID 배열을 사용하여 Linux VM에서 파일 복원 | 동일한 VM에서 복원이 지원되지 않습니다.<br/><br/> 호환 가능한 VM에서 복원합니다.
특수한 네트워크 설정을 사용하여 파일 복원 | 동일한 VM에서 복원이 지원되지 않습니다. <br/><br/> 호환 가능한 VM에서 복원합니다.

## <a name="support-for-vm-management"></a>VM 관리 지원

다음 표에는 vm 디스크 추가 또는 교체와 같은 VM 관리 작업을 수행 하는 동안 백업에 대한 지원이 요약 되어 있습니다.

**복원** | **지원됨**
--- | ---
구독/지역/영역에서의 복원 | 지원되지 않습니다.
기존 VM에 복원 | 디스크 바꾸기 옵션을 사용합니다.
Azure SSE(스토리지 서비스 암호화)가 설정된 스토리지 계정을 사용하여 디스크 복원 | 지원되지 않습니다.<br/><br/> SSE를 사용하도록 설정하지 않은 계정으로 복원합니다.
혼합된 스토리지 계정으로 복원 |지원되지 않습니다.<br/><br/> 스토리지 계정 유형에 따라, 복원된 모든 디스크가 프리미엄 또는 표준 중 하나가 되며 혼합되지 않습니다.
가용성 집합에 VM 직접 복원 | 관리 디스크의 경우 디스크를 복원 하 고 템플릿에서 가용성 집합 옵션을 사용할 수 있습니다.<br/><br/> 비관리형 디스크에서는 지원되지 않습니다. 비관리형 디스크의 경우, 디스크를 복원하고 가용성 집합에 VM을 만듭니다.
관리 되는 VM으로 업그레이드 한 후 관리 되지 않는 Vm의 백업 복원| 지원됩니다.<br/><br/> 디스크를 복원하고 관리형 VM을 만들 수 있습니다.
VM이 Managed Disks로 마이그레이션되기 전에 VM을 복원 지점으로 복원 | 지원됩니다.<br/><br/> 비관리형 디스크(기본값)로 복원하고, 복원된 디스크를 Managed Disks로 변환한 후 Managed Disks를 사용하여 VM을 만듭니다.
삭제된 VM을 복원합니다. | 지원됩니다.<br/><br/> 복구 지점에서 VM을 복원할 수 있습니다.
포털을 통해 다중 DC 구성의 일부인 DC(도메인 컨트롤러) VM 복원 | PowerShell을 사용 하 여 디스크를 복원 하 고 VM을 만드는 경우 지원 됩니다.
다른 가상 네트워크에서 VM 복원 |지원됩니다.<br/><br/> 가상 네트워크는 동일한 구독 및 지역에 있어야 합니다.

## <a name="vm-compute-support"></a>VM 컴퓨팅 지원

**컴퓨팅** | **지원**
--- | ---
VM 크기 |CPU 코어가 2개 이상이고 1GB 이상의 RAM이 탑재된 모든 Azure VM<br/><br/> [자세한 정보](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
[가용성 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets)의 VM 백업 | 지원됩니다.<br/><br/> VM을 신속 하 게 만드는 옵션을 사용 하 여 사용 가능한 집합에서 VM을 복원할 수 없습니다. 대신 VM을 복원 하는 경우 디스크를 복원 하 고이를 사용 하 여 VM을 배포 하거나 디스크를 복원 하 여 기존 디스크를 교체 하는 데 사용 합니다.
하이브리드 사용 혜택을 사용 하 여 배포 된 Vm 백업 [(허브)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | 지원됩니다.
[확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) 에 배포 된 vm 백업 |지원되지 않습니다.
[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images) 에서 배포 된 vm 백업<br/><br/> (Microsoft에서 게시, 제 3 자) |지원됩니다.<br/><br/> VM에서 지원되는 운영 체제도 실행하고 있어야 합니다.<br/><br/> VM에서 파일을 복구할 경우 호환되는 OS(이전 또는 이후 OS 아님)로만 복원할 수 있습니다. Vm으로 지원 되는 Azure Marketplace Vm은 구입 정보는 필요 하지만 디스크로는 복원 하지 않습니다.
사용자 지정 이미지에서 배포 된 Vm 백업 (타사) |지원됩니다.<br/><br/> VM에서 지원되는 운영 체제도 실행하고 있어야 합니다.<br/><br/> VM에서 파일을 복구할 경우 호환되는 OS(이전 또는 이후 OS 아님)로만 복원할 수 있습니다.
Azure로 마이그레이션된 Vm 백업| 지원됩니다.<br/><br/> VM을 백업하려면 VM 에이전트를 마이그레이션된 컴퓨터에 설치해야 합니다.
다중 VM 일관성 백업 | Azure Backup는 여러 Vm에서 데이터 및 응용 프로그램 일관성을 제공 하지 않습니다.
[진단 설정을](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) 사용 하 여 백업  | 지원 안 됨 <br/><br/> [새로 만들기](backup-azure-arm-restore-vms.md#create-a-vm) 옵션을 사용 하 여 진단 설정을 사용 하는 Azure VM의 복원이 트리거되면 복원에 실패 합니다.
영역 고정 Vm 복원 | 지원 됨 (1 월 2019 일 이후에 백업 되는 VM의 경우 [가용성 영역](https://azure.microsoft.com/global-infrastructure/availability-zones/) 을 사용할 수 있는 경우).<br/><br/>현재 Vm에 고정 되어 있는 동일한 영역으로 복원을 지원 합니다. 그러나 영역을 사용할 수 없는 경우 복원이 실패 합니다.
Gen2 Vm | 지원됨 <br> Azure Backup [Gen2 vm](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)의 백업 및 복원을 지원 합니다. 이러한 Vm은 복구 지점에서 복원 되는 경우 [Gen2 vm](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)으로 복원 됩니다.

## <a name="vm-storage-support"></a>VM 스토리지 지원

**구성 요소** | **지원**
--- | ---
Azure VM 데이터 디스크 수 | 16 개 이하의 데이터 디스크를 사용 하 여 VM을 백업 합니다.<BR> 16개 이상의 디스크(최대 32개 디스크)를 사용하여 VM의 프라이빗 미리 보기에 등록하려면 AskAzureBackupTeam@microsoft.com으로 이메일을 보내주세요.
데이터 디스크 크기 | 개별 디스크 크기는 최대 32 TB 이며 VM의 모든 디스크에 대해 최대 256 TB를 결합할 수 있습니다.
스토리지 유형 | 표준 HDD, 표준 SSD, 프리미엄 SSD.
Managed Disks | 지원됩니다.
암호화된 디스크 | 지원됩니다.<br/><br/> Azure Disk Encryption에서 사용 하도록 설정 된 azure Vm은 Azure AD 앱을 사용 하거나 사용 하지 않고 백업할 수 있습니다.<br/><br/> 암호화된 VM은 파일/폴더 수준에서 복구할 수 없습니다. 전체 VM을 복구 해야 합니다.<br/><br/> Azure Backup에서 이미 보호되는 VM에 암호화를 사용하도록 설정할 수 있습니다.
Write Accelerator가 설정된 디스크 | 지원되지 않습니다.<br/><br/> Azure backup은 백업 하는 동안 쓰기 가속기 사용 하도록 설정 된 디스크를 자동으로 제외 합니다. 이러한 디스크는 백업 되지 않으므로 VM의 복구 점에서 복원할 수 없습니다.
중복 제거 된 Vm/디스크 백업 & 복원 | Azure Backup은 중복 제거를 지원 하지 않습니다. 자세한 내용은이 [문서](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) 를 참조 하세요. <br/> <br/>  -Azure Backup Recovery Services 자격 증명 모음의 Vm 간에 중복 제거 되지 않습니다. <br/> <br/>  -복원 중에 중복 제거 상태의 Vm이 있는 경우 자격 증명 모음에서 형식을 인식 하지 않으므로 파일을 복원할 수 없습니다. 그러나 전체 VM 복원을 성공적으로 수행할 수 있습니다.
보호된 VM에 디스크 추가 | 지원됩니다.
보호된 VM에서 디스크 크기 조정 | 지원됩니다.
공유 스토리지| CSV (클러스터 공유 볼륨 또는 스케일 아웃 파일 서버를 사용 하 여 Vm을 백업할 수 없습니다. CSV 기록기는 백업 중에 실패할 수 있습니다. 복원 시 CSV 볼륨을 포함 하는 디스크가 제공 되지 않을 수 있습니다.

## <a name="vm-network-support"></a>VM 네트워크 지원

**구성 요소** | **지원**
--- | ---
Nic (네트워크 인터페이스) 수 | 특정 Azure VM 크기에서 지원되는 최대 NIC 수입니다.<br/><br/> 복원 프로세스 동안 VM이 만들어지면 NIC가 만들어집니다.<br/><br/> 복원된 VM의 NIC 수는 보호를 사용하도록 설정한 VM의 NIC 수를 미러링합니다. 보호를 사용 하도록 설정한 후에는 Nic를 제거 해도 개수에 영향을 주지 않습니다.
외부/내부 부하 분산 장치 |지원됩니다. <br/><br/> 특수한 네트워크 설정을 사용하여 VM을 복원하는 방법에 대해 [자세히 알아봅니다](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations).
여러 예약 된 IP 주소 |지원됩니다. <br/><br/> 특수한 네트워크 설정을 사용하여 VM을 복원하는 방법에 대해 [자세히 알아봅니다](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations).
여러 네트워크 어댑터를 사용하는 VM| 지원됩니다. <br/><br/> 특수한 네트워크 설정을 사용하여 VM을 복원하는 방법에 대해 [자세히 알아봅니다](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations).
공용 IP 주소가 있는 VM| 지원됩니다.<br/><br/> 기존 공용 IP 주소를 NIC와 연결 하거나, 복원을 완료 한 후 주소를 만들고 NIC와 연결 합니다.
NIC/서브넷의 NSG(네트워크 보안 그룹)입니다. |지원됩니다.
고정 IP 주소 | 지원되지 않습니다.<br/><br/> 복원 지점에서 만든 새 VM에는 동적 IP 주소가 할당 됩니다.<br/><br/> 클래식 Vm의 경우 예약 된 IP 주소를 사용 하는 VM을 백업할 수 없으며 엔드포인트이 정의 되어 있지 않습니다.
동적 IP 주소 |지원됩니다.<br/><br/> 원본 VM의 NIC가 동적 IP 주소 지정을 사용 하는 경우 기본적으로 복원 된 VM의 NIC가이를 사용 합니다.
Azure Traffic Manager| 지원됩니다.<br/><br/>백업 된 VM이 Traffic Manager에 있는 경우 복원 된 VM을 동일한 Traffic Manager 인스턴스에 수동으로 추가 합니다.
Azure DNS |지원됩니다.
사용자 지정 DNS |지원됩니다.
HTTP 프록시를 통한 아웃바운드 연결 | 지원됩니다.<br/><br/> 인증된 프록시는 지원되지 않습니다.
가상 네트워크 서비스 엔드포인트| 지원됩니다.<br/><br/> 방화벽 및 가상 네트워크 저장소 계정 설정에 모든 네트워크에서의 액세스를 허용 해야 합니다.

## <a name="vm-security-and-encryption-support"></a>VM 보안 및 암호화 지원

Azure Backup은 전송 중 및 정지 상태의 데이터에 대한 암호화를 지원합니다.

Azure로의 네트워크 트래픽:

- AES(Advanced Encryption Standard) 256를 사용 하 여 서버에서 Recovery Services 자격 증명 모음으로의 백업 트래픽을 암호화 합니다.
- 백업 데이터는 보안 HTTPS 링크를 통해 전송됩니다.
- 백업 데이터는 암호화된 형식으로 Recovery Services 자격 증명 모음에 저장됩니다.
- 사용자만 이 데이터의 잠금을 해제할 수 있는 암호를 보유합니다. Microsoft는 어떠한 경우에도 백업 데이터의 암호를 해독할 수 없습니다.

  > [!WARNING]
  > 자격 증명 모음을 설정한 후에는 암호화 키에만 액세스할 수 있습니다. Microsoft는 복사본을 유지 관리할 수 없으며, 키에 대한 액세스 권한도 없습니다. 키를 잃어버리면 Microsoft에서 백업 데이터를 복구할 수 없습니다.

데이터 보안:

- Azure Vm을 백업 하는 경우 가상 머신 *내에서* 암호화를 설정 해야 합니다.
- Azure Backup는 Windows 가상 컴퓨터에서 BitLocker를 사용 하 고 Linux 가상 컴퓨터에서 us **dm-자리** 를 사용 하는 Azure Disk Encryption를 지원 합니다.
- 백 엔드에서 Azure Backup는 [Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 사용하여 미사용 데이터를 보호합니다.

**머신** | **전송 중** | **저장**
--- | --- | ---
온-프레미스 Windows 머신(DPM/MABS 사용 안 함) | ![예][green] | ![예][green]
Azure VM | ![예][green] | ![예][green]
온-프레미스/Azure VM(DPM 사용) | ![예][green] | ![예][green]
온-프레미스/Azure VM(MABS 사용) | ![예][green] | ![예][green]

## <a name="vm-compression-support"></a>VM 압축 지원

Backup은 다음 표에 요약 된 것 처럼 백업 트래픽의 압축을 지원 합니다. 다음 사항에 유의하세요.

- Azure Vm의 경우 VM 확장은 저장소 네트워크를 통해 Azure storage 계정에서 직접 데이터를 읽습니다. 이 트래픽을 압축할 필요는 없습니다.
- DPM 또는 MABS를 사용 하는 경우 데이터를 DPM/MABS에 백업 하기 전에 압축 하 여 대역폭을 절약할 수 있습니다.

**머신** | **MABS/DPM에 압축(TCP)** | **자격 증명 모음으로 압축 (HTTPS)**
--- | --- | ---
온-프레미스 Windows 머신(DPM/MABS 사용 안 함) | 해당 없음 | ![예][green]
Azure VM | 해당 없음 | 해당 없음
온-프레미스/Azure VM(DPM 사용) | ![예][green] | ![예][green]
온-프레미스/Azure VM(MABS 사용) | ![예][green] | ![예][green]

## <a name="next-steps"></a>다음 단계

- [Azure vm을 백업](backup-azure-arm-vms-prepare.md)합니다.
- 백업 서버를 사용하지 않고 [Windows 머신을 직접 백업](tutorial-backup-windows-server-to-azure.md)합니다.
- Azure에 백업하도록 [MABS를 설정](backup-azure-microsoft-azure-backup.md)한 다음, 워크로드를 MABS에 백업합니다.
- Azure에 백업하도록 [DPM을 설정](backup-azure-dpm-introduction.md)한 다음, 워크로드를 DPM에 백업합니다.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
