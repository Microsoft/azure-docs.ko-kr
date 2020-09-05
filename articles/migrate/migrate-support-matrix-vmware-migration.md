---
title: Azure Migrate에서 VMware 마이그레이션 지원
description: Azure Migrate에서 VMware VM 마이그레이션에 대 한 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 4c9ae6a5c3ed0d38b6abc952458422c7789fef8f
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051120"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware 마이그레이션을 위한 지원 매트릭스

이 문서에서는 Azure Migrate로 VMware Vm을 마이그레이션하기 위한 지원 설정 및 제한 사항을 요약 합니다. [서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool) . Azure로의 마이그레이션을 위해 VMware Vm을 평가 하는 방법에 대 한 정보를 찾고 있는 경우 [평가 지원 매트릭스](migrate-support-matrix-vmware.md)를 검토 하세요.


## <a name="migration-options"></a>마이그레이션 옵션

VMware Vm은 몇 가지 방법으로 마이그레이션할 수 있습니다.

- **에이전트 없는 마이그레이션 사용**: vm에 아무것도 설치 하지 않고도 vm을 마이그레이션합니다. 에이전트 없는 마이그레이션을 위해 [Azure Migrate 어플라이언스](migrate-appliance.md) 를 배포 합니다.
- **에이전트 기반 마이그레이션 사용**: 복제를 위해 VM에 에이전트를 설치 합니다. 에이전트 기반 마이그레이션의 경우 [복제 어플라이언스](migrate-replication-appliance.md)를 배포 합니다.

[이 문서](server-migrate-overview.md) 를 검토 하 여 사용 하려는 방법을 파악 합니다.

## <a name="migration-limitations"></a>마이그레이션 제한 사항

- 복제를 위해 한 번에 최대 10 개의 Vm을 선택할 수 있습니다. 더 많은 컴퓨터를 마이그레이션하려는 경우 10 그룹으로 복제 합니다.
- VMware 에이전트 없는 마이그레이션의 경우 최대 300 복제를 동시에 실행할 수 있습니다.

## <a name="agentless-migration"></a>에이전트 없는 마이그레이션 

이 섹션에서는 에이전트 없는 마이그레이션에 대 한 요구 사항을 요약 합니다.

### <a name="vmware-requirements-agentless"></a>VMware 요구 사항 (에이전트 없는)

이 표에는 VMware 하이퍼바이저 요구 사항이 요약 되어 있습니다.

**VMware** | **세부 정보**
--- | ---
**VMware vCenter Server** | 버전 5.5, 6.0, 6.5 또는 6.7입니다.
**VMware vSphere ESXI 호스트** | 버전 5.5, 6.0, 6.5 또는 6.7입니다.
**vCenter Server 권한** | 에이전트 없는 마이그레이션은 마이그레이션 [어플라이언스](migrate-appliance.md)를 사용 합니다. 어플라이언스는 vCenter Server에서 다음 권한이 필요 합니다.<br/><br/> - **데이터 저장소. 찾아보기**: VM 로그 파일을 검색 하 여 스냅숏 만들기 및 삭제 문제를 해결할 수 있습니다.<br/><br/> - **데이터 저장소. LowLevelFileOperations**: 데이터 저장소 브라우저에서 읽기/쓰기/삭제/이름 바꾸기 작업을 허용 하 여 스냅숏 생성 및 삭제 문제를 해결 합니다.<br/><br/> - ** Uration를VirtualMachine.Config합니다. DiskChangeTracking**: VM 디스크의 변경 내용 추적을 사용 하거나 사용 하지 않도록 설정 하 여 스냅숏 간에 변경 된 데이터 블록을 가져옵니다.<br/><br/> - ** Uration를VirtualMachine.Config합니다. DiskLease**: VM에 대 한 디스크 임대 작업을 허용 하 VMWARE VSPHERE VDDK (가상 디스크 개발 키트)를 사용 하 여 디스크를 읽습니다.<br/><br/> - **VirtualMachine**: (특히 vsphere 6.0 이상) VDDK를 사용 하 여 디스크에서 임의 읽기 액세스를 위해 VM에서 디스크를 열 수 있도록 허용 합니다.<br/><br/> - **VirtualMachine. ReadOnlyDiskAccess**: VM에서 디스크를 열고 VDDK를 사용 하 여 디스크를 읽을 수 있습니다.<br/><br/> - **VirtualMachine. DiskRandomAccess**: VM에서 디스크를 열고 VDDK를 사용 하 여 디스크를 읽을 수 있습니다.<br/><br/> - **VirtualMachine 다운로드**: VM과 연결 된 파일에 대 한 읽기 작업을 허용 하 여 로그를 다운로드 하 고 오류가 발생 하는 경우 문제를 해결 합니다.<br/><br/> - **VirtualMachine. SnapshotManagement \* **: 복제를 위한 VM 스냅숏 만들기 및 관리를 허용 합니다.<br/><br/> - **가상 컴퓨터. 상호 작용: 전원 끄기**: Azure로 마이그레이션하는 동안 VM의 전원을 끌 수 있습니다.



### <a name="vm-requirements-agentless"></a>VM 요구 사항 (에이전트 없는)

이 표에는 VMware Vm에 대 한 에이전트 없는 마이그레이션 요구 사항이 요약 되어 있습니다.

**지원** | **세부 정보**
--- | ---
**지원되는 운영 체제** | Azure에서 지 원하는 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](../virtual-machines/linux/endorsed-distros.md) 운영 체제를 마이그레이션할 수 있습니다.
**Azure의 Windows Vm** | 마이그레이션하기 전에 Vm에서 [일부를 변경](prepare-for-migration.md#verify-required-changes-before-migrating) 해야 할 수도 있습니다. 
**Azure의 Linux Vm** | 일부 VM은 Azure에서 실행될 수 있도록 변경해야 할 수 있습니다.<br/><br/> Linux의 경우 이러한 운영 체제에 대 한 변경 내용이 자동으로 Azure Migrate.<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 이상<br/> -CentOS 6.5 이상, 7.0 이상</br> -SUSE Linux Enterprise Server 12 SP1 이상<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8. 다른 운영 체제의 경우 [필요한 변경](prepare-for-migration.md#verify-required-changes-before-migrating) 작업을 수동으로 수행 합니다.
**Linux 부팅** | /Boot는 전용 파티션에 있는 경우 OS 디스크에 상주해 야 하며 여러 디스크에 분산 되 면 안 됩니다.<br/> /Boot가 루트 (/) 파티션의 일부인 경우 '/' 파티션은 OS 디스크에 있어야 하며 다른 디스크에 걸쳐 있지 않아야 합니다.
**UEFI 부팅** | UEFI 부팅이 포함 된 Vm은 마이그레이션을 지원 하지 않습니다.
**디스크 크기** | 2TB OS 디스크 데이터 디스크의 경우 8TB입니다.
**디스크 제한** |  VM 당 최대 60 디스크
**암호화 된 디스크/볼륨** | 암호화 된 디스크/볼륨이 있는 Vm은 마이그레이션을 지원 하지 않습니다.
**공유된 디스크 클러스터** | 지원 안 됨
**독립 디스크** | 지원 안 됨
**RDM/통과 디스크** | Vm에 RDM 또는 통과 디스크가 있는 경우 이러한 디스크는 Azure에 복제 되지 않습니다.
**NFS** | Vm에 볼륨으로 탑재 된 NFS 볼륨이 복제 되지 않습니다.
**iSCSI 대상** | ISCSI 대상을 사용 하는 Vm은 에이전트 없는 마이그레이션에 대해 지원 되지 않습니다.
**다중 경로 IO** | 지원 안 됨
**저장소 vMotion** | 지원 안 됨 VM에서 저장소 vMotion를 사용 하는 경우 복제가 작동 하지 않습니다.
**팀 Nic** | 지원 안 됨
**IPv6** | 지원 안 됨
**대상 디스크** | Vm은 Azure에서 관리 되는 디스크 (표준 HDD, 프리미엄 SSD)로만 마이그레이션할 수 있습니다.
**동시 복제** | vCenter Server 당 Vm 300 더 많은 경우 300의 일괄 처리로 마이그레이션합니다.


### <a name="appliance-requirements-agentless"></a>어플라이언스 요구 사항 (에이전트 없는)

에이전트 없는 마이그레이션은 [Azure Migrate 어플라이언스](migrate-appliance.md)를 사용 합니다. VCenter Server로 가져오거나 [PowerShell 스크립트](deploy-appliance-script.md)를 사용 하 여 ova 템플릿을 사용 하 여 장치를 VMware VM으로 배포할 수 있습니다.

- VMware에 대한 [어플라이언스 요구 사항](migrate-appliance.md#appliance---vmware)에 대해 알아봅니다.
- 어플라이언스가 [퍼블릭](migrate-appliance.md#public-cloud-urls) 클라우드 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 URL에 대해 알아봅니다.
- Azure Government에서는 [스크립트](deploy-appliance-script-government.md)를 사용하여 어플라이언스를 배포해야 합니다.

### <a name="port-requirements-agentless"></a>포트 요구 사항 (에이전트 없는)

**디바이스** | **연결**
--- | ---
어플라이언스 | 포트 443의 아웃 바운드 연결을 사용 하 여 복제 된 데이터를 Azure에 업로드 하 고, 복제 및 오케스트레이션 서비스를 사용 하 여 Azure Migrate 서비스와 통신 합니다.
vCenter Server | 어플라이언스에서 복제를 오케스트레이션 할 수 있도록 허용 하는 포트 443의 인바운드 연결-스냅숏 만들기, 데이터 복사, 릴리스 스냅숏
vSphere/ESXI 호스트 | 어플라이언스의 TCP 포트 902에 대 한 인바운드는 스냅숏에서 데이터를 복제 합니다.

## <a name="agent-based-migration"></a>에이전트 기반 마이그레이션 


이 섹션에서는 에이전트 기반 마이그레이션에 대 한 요구 사항을 요약 합니다.


### <a name="vmware-requirements-agent-based"></a>VMware 요구 사항 (에이전트 기반)

이 표에는 VMware 가상화 서버에 대 한 평가 지원 및 제한이 요약 되어 있습니다.

**VMware 요구 사항** | **세부 정보**
--- | ---
**VMware vCenter Server** | 버전 5.5, 6.0, 6.5 또는 6.7입니다.
**VMware vSphere ESXI 호스트** | 버전 5.5, 6.0, 6.5 또는 6.7입니다.
**vCenter Server 권한** | VCenter Server에 대 한 읽기 전용 계정입니다.

### <a name="vm-requirements-agent-based"></a>VM 요구 사항 (에이전트 기반)

이 표에는 에이전트 기반 마이그레이션을 사용 하 여 마이그레이션하려는 VMware vm에 대 한 VMware VM 지원이 요약 되어 있습니다.

**지원** | **세부 정보**
--- | ---
**머신 워크로드** | Azure Migrate 지원 되는 컴퓨터에서 실행 되는 모든 작업 (예를 들어 Active Directory, SQL server 등)의 마이그레이션을 지원 합니다.
**운영 체제** | 최신 정보는 Site Recovery에 대 한 [운영 체제 지원](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) 을 참조 하세요. Azure Migrate는 동일한 VM 운영 체제를 지원 합니다.
**Linux 파일 시스템/게스트 저장소** | 최신 정보는 Site Recovery에 대 한 [Linux 파일 시스템 지원](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) 을 참조 하세요. Azure Migrate는 동일한 Linux 파일 시스템을 지원 합니다.
**네트워크/저장소** | 최신 정보는 Site Recovery에 대 한 [네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#network) 및 [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#storage) 필수 구성 요소를 검토 하세요. Azure Migrate는 동일한 네트워크/저장소 요구 사항을 제공 합니다.
**Azure 요구 사항** | 최신 정보는 Site Recovery에 대 한 [Azure 네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)및 [계산](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) 요구 사항을 검토 하세요. Azure Migrate는 VMware 마이그레이션에 대 한 요구 사항이 동일 합니다.
**모바일 서비스** | 마이그레이션하려는 각 VM에 모바일 서비스 에이전트를 설치 해야 합니다.
**UEFI 부팅** | 지원됨.
**UEFI-보안 부팅**         | 마이그레이션에 지원 되지 않습니다.
**대상 디스크** | Vm은 Azure에서 관리 되는 디스크 (표준 HDD, 프리미엄 SSD)로만 마이그레이션할 수 있습니다.
**디스크 크기** | 2TB OS 디스크 데이터 디스크의 경우 8TB입니다.
**디스크 제한** |  VM 당 최대 63 디스크
**암호화 된 디스크/볼륨** | 암호화 된 디스크/볼륨이 있는 Vm은 마이그레이션을 지원 하지 않습니다.
**공유된 디스크 클러스터** | 지원 안 됨
**독립 디스크** | 지원됨.
**통과 디스크** | 지원됨.
**NFS** | Vm에 볼륨으로 탑재 된 NFS 볼륨이 복제 되지 않습니다.
**iSCSI 대상** | 지원됨.
**다중 경로 IO** | 지원 안 됨
**저장소 vMotion** | 지원됨
**팀 Nic** | 지원 안 됨
**IPv6** | 지원 안 됨




### <a name="appliance-requirements-agent-based"></a>어플라이언스 요구 사항 (에이전트 기반)

Azure Migrate 허브에 제공 된 OVA 템플릿을 사용 하 여 복제 어플라이언스를 설정 하는 경우 어플라이언스는 Windows Server 2016를 실행 하 고 지원 요구 사항을 준수 합니다. 물리적 서버에서 복제 어플라이언스를 수동으로 설정 하는 경우 요구 사항에 부합 하는지 확인 합니다.

- VMware에 대 한 [복제 어플라이언스 요구 사항](migrate-replication-appliance.md#appliance-requirements) 에 대해 알아봅니다.
- MySQL은 어플라이언스에 설치되어야 합니다. [설치 옵션](migrate-replication-appliance.md#mysql-installation)에 대해 알아봅니다.
- 복제 어플라이언스에서 [공용](migrate-replication-appliance.md#url-access) 및 [정부](migrate-replication-appliance.md#azure-government-url-access) 클라우드에서 액세스 해야 하는 url에 대해 알아봅니다.
- 복제 어플라이언스에서 액세스 해야 하는 [포트](migrate-replication-appliance.md#port-access) 를 검토 합니다.

### <a name="port-requirements-agent-based"></a>포트 요구 사항 (에이전트 기반)

**디바이스** | **연결**
--- | ---
VM | Vm에서 실행 되는 모바일 서비스는 복제 관리를 위해 HTTPS 443 인바운드 포트의 온-프레미스 복제 어플라이언스 (구성 서버)와 통신 합니다.<br/><br/> VM은 HTTPS 9443 인바운드 포트의 프로세스 서버(구성 서버 컴퓨터에서 실행)로 복제 데이터를 전송합니다. 이 포트는 수정할 수 있습니다.
복제 어플라이언스 | 복제 어플라이언스는 HTTPS 443 아웃 바운드 포트를 통해 Azure를 사용 하 여 복제를 오케스트레이션 합니다.
프로세스 서버 | 프로세스 서버는 복제 데이터를 수신 하 고, 최적화 하 고, 암호화 하 고 포트 443 아웃 바운드를 통해 Azure storage로 보냅니다.<br/> 기본적으로 프로세스 서버는 복제 어플라이언스에서 실행 됩니다.

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

에이전트 없이 또는 에이전트 기반 마이그레이션을 사용 하 여 Azure에 복제 된 모든 온-프레미스 Vm은이 표에 요약 된 Azure VM 요구 사항을 충족 해야 합니다. 

**구성 요소** | **요구 사항** 
--- | --- | ---
게스트 운영 체제 | 마이그레이션에 대해 지원 되는 VMware VM 운영 체제를 확인 합니다.<br/> 지원 되는 운영 체제에서 실행 되는 모든 워크 로드를 마이그레이션할 수 있습니다. 
게스트 운영 체제 아키텍처 | 64비트. 
운영 체제 디스크 크기 | 최대 2,048GB. 
운영 체제 디스크 수 | 1 
데이터 디스크 수 | 64개 이하. 
데이터 디스크 크기 | 최대 8095
네트워크 어댑터 | 여러 어댑터가 지원됩니다.
공유 VHD | 지원 안 됨 
FC 디스크 | 지원 안 됨 
BitLocker | 지원 안 됨<br/><br/> 컴퓨터를 마이그레이션하기 전에 BitLocker를 사용 하지 않도록 설정 해야 합니다.
VM 이름 | 1~63자 사이입니다.<br/><br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. 
마이그레이션 후 연결-Windows | 마이그레이션 후 Windows를 실행 하는 Azure Vm에 연결 하려면 다음을 수행 합니다.<br/><br/> -마이그레이션하기 전에 온-프레미스 VM에서 RDP를 사용 하도록 설정 합니다.<br/><br/> **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 허용되는지 확인합니다.<br/><br/> 사이트 간 VPN 액세스의 경우 rdp를 사용 하도록 설정 하 고, **Windows Firewall**  ->  **도메인 및 개인** 네트워크의 Windows 방화벽**허용 되는 앱 및 기능** 에서 rdp를 허용 합니다.<br/><br/> 또한 운영 체제의 SAN 정책이 **OnlineAll**으로 설정 되어 있는지 확인 합니다. [자세히 알아보기](prepare-for-migration.md).
마이그레이션 후 연결-Linux | SSH를 사용 하 여 마이그레이션한 후 Azure Vm에 연결 하려면:<br/><br/> 마이그레이션 전에 온-프레미스 컴퓨터에서 Secure Shell 서비스가 시작으로 설정 되어 있고 방화벽 규칙에서 SSH 연결을 허용 하는지 확인 합니다.<br/><br/> 장애 조치 (failover) 후에 Azure VM에서 장애 조치 (failover) 된 VM의 네트워크 보안 그룹 규칙에 대 한 SSH 포트 및 연결 된 Azure 서브넷에 대 한 들어오는 연결을 허용 합니다.<br/><br/> 또한 VM에 대 한 공용 IP 주소를 추가 합니다.  


## <a name="next-steps"></a>다음 단계

VMware 마이그레이션 옵션을 [선택](server-migrate-overview.md) 합니다.
