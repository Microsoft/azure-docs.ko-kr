---
title: MABS & System Center DPM 지원 매트릭스
description: 이 문서에서는 MABS (Microsoft Azure Backup 서버) 또는 System Center DPM을 사용 하 여 온-프레미스 및 Azure VM 리소스를 백업 하는 경우 지원 Azure Backup를 요약 합니다.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 211a7e39dc9cda9e4bd96e3a66924b2195524be7
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111455"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Microsoft Azure Backup Server 또는 System Center DPM을 사용 하 여 백업에 대 한 지원 매트릭스

[Azure Backup 서비스](backup-overview.md) 를 사용 하 여 온-프레미스 컴퓨터 및 워크 로드와 Azure vm (가상 머신)을 백업할 수 있습니다. 이 문서에는 Microsoft Azure Backup 서버 (MABS) 또는 System Center Data Protection Manager (DPM) 및 Azure Backup를 사용 하 여 컴퓨터를 백업 하는 데 필요한 설정 및 제한 사항이 요약 되어 있습니다.

## <a name="about-dpmmabs"></a>DPM/MABS 정보

[System CENTER DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) 은 엔터프라이즈 컴퓨터와 데이터의 백업 및 복구를 구성 하 고, 용이 하 고, 관리 하는 엔터프라이즈 솔루션입니다. 이 기능은 [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) 제품군에 속해 있습니다.

MABS는 온-프레미스 물리적 서버, Vm 및 해당 서버에서 실행 되는 앱을 백업 하는 데 사용할 수 있는 서버 제품입니다.

MABS는 System Center DPM을 기반으로 하며 다음과 같은 몇 가지 차이점을 포함 하는 유사한 기능을 제공 합니다.

- MABS를 실행하기 위해 System Center 라이선스가 필요하지 않습니다.
- MABS와 DPM 모두 Azure는 장기 백업 저장소를 제공 합니다. 또한 DPM을 사용하면 테이프의 장기 보관할 수 있게 데이터를 백업할 수 있습니다. MABS는 이 기능을 제공하지 않습니다.
- 보조 DPM 서버를 사용 하 여 주 DPM 서버를 백업할 수 있습니다. 보조 서버는 주 서버 데이터베이스와 주 서버에 저장 된 데이터 원본 복제본을 보호 합니다. 주 서버에서 오류가 발생 하는 경우 보조 서버는 주 서버를 다시 사용할 수 있을 때까지 주 서버에 의해 보호 되는 작업을 계속 해 서 보호할 수 있습니다.  MABS는 이 기능을 제공하지 않습니다.

[Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57520)에서 mabs를 다운로드 합니다. 온-프레미스 또는 Azure VM에서 실행할 수 있습니다.

DPM 및 MABS는 다양한 앱, 서버 및 클라이언트 운영 체제의 백업을 지원합니다. 다음과 같은 여러 백업 시나리오를 제공합니다.

- 시스템 상태 백업 또는 완전 백업을 사용하여 컴퓨터 수준에서 백업할 수 있습니다.
- 특정 볼륨, 공유, 폴더 및 파일을 백업할 수 있습니다.
- 최적화 된 앱 인식 설정을 사용 하 여 특정 앱을 백업할 수 있습니다.

## <a name="dpmmabs-backup"></a>DPM/MABS 백업

DPM/MABS 및 Azure Backup를 사용한 백업은 다음과 같이 작동 합니다.

1. DPM/MABS 보호 에이전트가 백업 될 각 컴퓨터에 설치 됩니다.
1. 컴퓨터와 앱은 DPM/MABS의 로컬 저장소에 백업 됩니다.
1. MARS(Microsoft Azure Recovery Services) 에이전트가 DPM 서버/MABS에 설치됩니다.
1. MARS 에이전트는 Azure Backup를 사용 하 여 DPM/MABS 디스크를 Azure의 백업 Recovery Services 자격 증명 모음에 백업 합니다.

자세한 내용은 다음을 참조하세요.

- MABS 아키텍처에 대해 [자세히 알아봅니다](backup-architecture.md#architecture-back-up-to-dpmmabs).
- MARS 에이전트에 대해 [지원 되는 기능을 검토](backup-support-matrix-mars-agent.md) 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

**시나리오** | **에이전트** | **위치**
--- | --- | ---
**온-프레미스 머신/워크로드 백업**: | DPM/MABS 보호 에이전트는 백업 하려는 컴퓨터에서 실행 됩니다.<br/><br/> DPM/MABS 서버의 MARS 에이전트<br/> 이 기능을 사용하도록 설정하는 데 필요한 Microsoft Azure Recovery Services 에이전트 또는 Azure Backup 에이전트의 최소 버전은 2.0.8719.0입니다.  | DPM/MABS가 온-프레미스에서 실행 되 고 있어야 합니다.

## <a name="supported-deployments"></a>지원되는 배포

다음 표에 요약 된 대로 DPM/MABS를 배포할 수 있습니다.

**배포** | **지원** | **세부 정보**
--- | --- | ---
**온-프레미스에 배포** | 물리적 서버<br/><br/>Hyper-V VM<br/><br/> VMware VM | DPM/MABS를 VMware VM으로 설치 하는 경우 해당 Vm에서 실행 되는 VMware Vm 및 워크 로드만 백업 합니다.
**Azure Stack VM으로 배포** | MABS만 | DPM은 Azure Stack VM을 백업하는 데 사용할 수 없습니다.
**Azure VM으로 배포** | 해당 Vm에서 실행 되는 Azure Vm 및 워크 로드를 보호 합니다. | Azure에서 실행 되는 DPM/MABS는 온-프레미스 컴퓨터를 백업할 수 없습니다.

## <a name="supported-mabs-and-dpm-operating-systems"></a>지원되는 MABS 및 DPM 운영 체제

Azure Backup는 다음 운영 체제 중 하나를 실행 하는 DPM/MABS 인스턴스를 백업할 수 있습니다. 운영 체제는 최신 서비스 팩 및 업데이트를 실행하고 있어야 합니다.

**시나리오** | **DPM/MABS**
--- | ---
**Azure VM의 MABS** | Windows Server 2012 R2입니다.<br/><br/> Windows 2016 데이터 센터.<br/><br/> Windows 2019 데이터 센터.<br/><br/> Marketplace에서 이미지를 시작 하는 것이 좋습니다.<br/><br/> 코어 2 개와 3.5 GB의 RAM이 있는 최소 A2 표준입니다.
**Azure VM의 DPM** | System Center 2012 R2 업데이트 3 이상<br/><br/> [System Center에 필요한](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) Windows 운영 체제<br/><br/> Marketplace에서 이미지를 시작 하는 것이 좋습니다.<br/><br/> 코어 2 개와 3.5 GB의 RAM이 있는 최소 A2 표준입니다.
**MABS 온-프레미스** | 지원되는 64비트 운영 체제:<br/><br/> MABS v3 이상: Windows Server 2019 (Standard, Datacenter, Essentials). <br/><br/> MABS v2 이상: Windows Server 2016 (Standard, Datacenter, Essentials).<br/><br/> 모든 MABS 버전: Windows Server 2012 R2.<br/><br/>모든 MABS 버전: Windows Storage Server 2012 R2.
**DPM 온-프레미스** | 물리적 서버/Hyper-v VM: System Center 2012 SP1 이상<br/><br/> VMware VM: System Center 2012 R2 업데이트 5 이상

>[!NOTE]
>Azure Backup Server 설치는 Windows Server Core 또는 Microsoft Hyper-V Server에서 지원 되지 않습니다.

## <a name="management-support"></a>관리 지원

**문제점** | **세부 정보**
--- | ---
**설치** | 단일 용도의 컴퓨터에 DPM/MABS를 설치 합니다.<br/><br/> DPM/MABS를 도메인 컨트롤러에 설치 하지 마세요. 응용 프로그램 서버 역할이 설치 된 컴퓨터, Microsoft Exchange Server 또는 System Center Operations Manager를 실행 하는 컴퓨터 또는 클러스터 노드에서 DPM/MABS를 설치 하지 마세요.<br/><br/> [모든 DPM 시스템 요구 사항을 검토](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)합니다.
**도메인** | DPM/MABS는 도메인에 가입 되어 있어야 합니다. DPM/MABS를 먼저 설치한 후 도메인에 가입합니다. 배포가 지원되지 않으면 DPM/MABS를 새 도메인으로 이동
**스토리지** | 최신 백업 저장소 (MB)는 DPM 2016/MABS v2 이상에서 지원 됩니다. MABS v1에는 사용할 수 없습니다.
**MABS 업그레이드** | MABS v3를 직접 설치하거나 MABS v2에서 MABS v3로 업그레이드할 수 있습니다. [자세히 알아봅니다](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**MABS 이동** | MBS를 사용하는 경우 스토리지 보존이 지원될 때 MABS를 새 서버로 이동<br/><br/> 새 서버는 원래 서버와 이름이 같아야 합니다. 동일한 스토리지 풀을 유지하고 동일한 MABS 데이터베이스를 사용하여 데이터 복구 지점을 저장하려는 경우에는 이름을 변경할 수 없습니다.<br/><br/> MABS 데이터베이스를 복원해야 하므로 백업이 필요합니다.

## <a name="mabs-support-on-azure-stack"></a>Azure Stack의 MABS 지원

단일 위치에서 Azure Stack VM 및 워크로드 백업을 관리할 수 있도록 Azure Stack VM에 MABS를 배포할 수 있습니다.

**구성 요소** | **세부 정보**
--- | ---
**Azure Stack VM의 MABS** | 크기 A2 이상 Azure Marketplace에서 Windows Server 2012 R2 또는 Windows Server 2016 이미지를 사용 하 여 시작 하는 것이 좋습니다.<br/><br/> MABS VM에 다른 항목을 설치 하지 마세요.
**MABS 스토리지** | MABS VM에 별도의 저장소 계정을 사용 합니다. MABS에서 실행 되는 MARS 에이전트에는 캐시 위치에 대 한 임시 저장소가 필요 하며 클라우드에서 복원 된 데이터를 보관 해야 합니다.
**MABS 스토리지 풀** | MABS 저장소 풀의 크기는 MABS VM에 연결 된 디스크의 수와 크기에 따라 결정 됩니다. 각 Azure Stack VM 크기는 최대 디스크 수를 갖습니다. 예를 들어 A2는 4개의 디스크입니다.
**MABS 보존** | 5 일 넘게 로컬 MABS 디스크에 백업 된 데이터를 보존 하지 않습니다.
**MABS 강화** | 배포를 강화하기 위해 MABS VM의 크기를 늘릴 수 있습니다. 예를 들어 A에서 D 시리즈로 변경할 수 있습니다.<br/><br/> 정기적으로 백업을 사용 하 여 데이터를 오프 로드 하 고 있는지도 확인할 수 있습니다. 필요한 경우 추가 MABS 서버를 배포할 수 있습니다.
**MABS의 .NET Framework** | MABS VM에 .NET Framework 3.3 SP1 이상이 설치 되어 있어야 합니다.
**MABS 도메인** | MABS VM은 도메인에 가입되어 있어야 합니다. 관리자 권한이 있는 도메인 사용자는 VM에 MABS를 설치해야 합니다.
**Azure Stack VM 데이터 백업** | 파일, 폴더 및 앱을 백업할 수 있습니다.
**지원되는 백업** | 이러한 운영 체제는 백업 하려는 Vm에 대해 지원 됩니다.<br/><br/> Windows Server 반기 채널 (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Azure Stack Vm에 대 한 SQL Server 지원** | SQL Server 2016, SQL Server 2014 SQL Server 2012 SP1을 백업 합니다.<br/><br/> 데이터베이스 백업 및 복구
**Azure Stack VM에 대한 SharePoint 지원** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> 팜, 데이터베이스, 프런트 엔드 및 웹 서버를 백업 하 고 복구 합니다.
**백업한 VM에 대한 네트워크 요구 사항** | Azure Stack 워크 로드의 모든 Vm은 동일한 가상 네트워크에 속해야 하며 동일한 구독에 속해야 합니다.

## <a name="dpmmabs-networking-support"></a>DPM/MABS 네트워킹 지원

### <a name="url-access"></a>URL 액세스

DPM 서버/MABS는 다음 URL에 액세스할 수 있어야 합니다.

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Azure Backup에 대한 DPM/MABS 연결

백업이 제대로 작동하고 Azure 구독이 활성화되려면 Azure Backup 서비스에 연결되어야 합니다. 다음 표에서는 이러한 두 가지 조건이 충족되지 않을 경우에 발생하는 동작을 보여 줍니다.

**MABS-Azure 연결** | **구독** | **백업/복원**
--- | --- | ---
연결됨 | Active | DPM/MABS 디스크에 백업 합니다.<br/><br/> Azure에 백업 합니다.<br/><br/> 디스크에서 복원<br/><br/> Azure에서 복원.
연결됨 | 만료됨/프로비전 해제됨 | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 구독이 만료 된 경우 디스크 또는 Azure에서 복원할 수 있습니다.<br/><br/> 구독을 해제 하는 경우 디스크 또는 Azure에서 복원할 수 없습니다. Azure 복구 지점이 삭제됩니다.
15일 넘게 연결되지 않음 | Active | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 디스크 또는 Azure에서 복원할 수 있습니다.
15일 넘게 연결되지 않음 | 만료됨/프로비전 해제됨 | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 구독이 만료 된 경우 디스크 또는 Azure에서 복원할 수 있습니다.<br/><br/> 구독을 해제 하는 경우 디스크 또는 Azure에서 복원할 수 없습니다. Azure 복구 지점이 삭제됩니다.

## <a name="dpmmabs-storage-support"></a>DPM/MABS 스토리지 지원

DPM/MABS에 백업 되는 데이터는 로컬 디스크 저장소에 저장 됩니다.

**스토리지** | **세부 정보**
--- | ---
**MBS** | 최신 백업 저장소 (MB)는 DPM 2016/MABS v2 이상에서 지원 됩니다. MABS v1에는 사용할 수 없습니다.
**Azure VM의 MABS 스토리지** | 데이터는 DPM/MABS VM에 연결 되 고 DPM/MABS에서 관리 되는 Azure 디스크에 저장 됩니다. DPM/MABS 저장소 풀에 사용할 수 있는 디스크 수는 VM의 크기로 제한 됩니다.<br/><br/> A2 VM: 4 개 디스크 A3 VM: 8 개 디스크 A4 VM: 각 디스크에 대해 최대 크기가 1tb 인 16 개의 디스크 이는 사용 가능한 총 백업 저장소 풀을 결정 합니다.<br/><br/> 백업할 수 있는 데이터 양은 연결된 디스크의 수와 크기에 따라 달라집니다.
**Azure VM의 MABS 데이터 보존** | DPM/MABS Azure 디스크에 데이터를 하루 동안 보존 하 고 더 긴 보존을 위해 DPM/MABS에서 자격 증명 모음으로 백업 하는 것이 좋습니다. 이 경우 대량의 데이터를 Azure Backup으로 오프로드하여 보호할 수 있습니다.

### <a name="modern-backup-storage-mbs"></a>MBS(최신 백업 스토리지)

DPM 2016/MABS v2 (Windows Server 2016에서 실행) 이상에서 최신 백업 저장소 (MB)를 활용할 수 있습니다.

- MBS 백업은 ReFS(Resilient File System) 디스크에 저장됩니다.
- MB는 ReFS 블록 복제를 사용 하 여 백업 속도를 향상 하 고 저장소 공간을 보다 효율적으로 사용 합니다.
- 로컬 DPM/MABS 저장소 풀에 볼륨을 추가 하는 경우 드라이브 문자를 사용 하 여 구성 합니다. 그런 후 여러 다른 볼륨에 워크로드 스토리지를 구성할 수 있습니다.
- DPM/MABS로 데이터를 백업하기 위해 보호 그룹을 만드는 경우 사용할 드라이브를 선택합니다. 예를 들어, SQL 또는 기타 높은 IOPS 워크 로드에 대 한 백업을 고성능 드라이브에 저장 하 고 더 적은 수의 백업 작업을 낮은 성능 드라이브에 저장 하는 경우가 있습니다.

## <a name="supported-backups-to-mabs"></a>MABS에 대해 지원되는 백업

Azure Backup Server로 보호할 수 있는 다양 한 서버 및 작업에 대 한 자세한 내용은 [Azure Backup Server Protection 매트릭스](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix)를 참조 하세요.

## <a name="supported-backups-to-dpm"></a>DPM에 대해 지원되는 백업

Data Protection Manager를 사용 하 여 보호할 수 있는 다양 한 서버 및 작업에 대 한 자세한 내용은 [DPM이 백업할 수 있는 항목](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019)을 참조 하세요.

- DPM/MABS에 의해 백업 된 클러스터형 작업은 DPM/MABS와 동일한 도메인 또는 자식/트러스트 된 도메인에 있어야 합니다.
- 신뢰할 수 없는 도메인 또는 작업 그룹의 데이터를 백업하려면 NTLM/인증서 인증을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- MABS 아키텍처에 대해 [자세히 알아봅니다](backup-architecture.md#architecture-back-up-to-dpmmabs).
- MARS 에이전트에 대한 지원되는 기능을 [검토](backup-support-matrix-mars-agent.md)합니다.
- MABS 서버를 [설정](backup-azure-microsoft-azure-backup.md)합니다.
- [DPM을 설정](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180)합니다.
