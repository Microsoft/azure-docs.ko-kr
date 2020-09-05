---
title: Azure Migrate의 물리적 서버 마이그레이션 지원
description: Azure Migrate의 물리적 서버 마이그레이션 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.custom: fasttrack-edit
ms.date: 06/14/2020
ms.openlocfilehash: 30c96b11fb15ca1783b6ffc574d9d258dff9da06
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051137"
---
# <a name="support-matrix-for-physical-server-migration"></a>물리적 서버 마이그레이션을 위한 지원 매트릭스

이 문서에서는 Azure Migrate로 물리적 서버를 마이그레이션하는 데 필요한 지원 설정 및 제한 사항을 요약 합니다. [서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool) . Azure로의 마이그레이션을 위해 물리적 서버를 평가 하는 방법에 대 한 정보를 찾고 있는 경우 [평가 지원 매트릭스](migrate-support-matrix-physical.md)를 검토 하세요.

## <a name="migrating-machines-as-physical"></a>컴퓨터를 물리적으로 마이그레이션

에이전트 기반 복제를 사용 하 여 온-프레미스 컴퓨터를 물리적 서버로 마이그레이션할 수 있습니다. 이 도구를 사용하면 다음과 같은 다양한 머신을 Azure로 마이그레이션할 수 있습니다.

- 온-프레미스 물리적 서버.
- Xen, KVM 등의 플랫폼에서 가상화 된 Vm.
- Hyper-v Vm 또는 VMware Vm (어떤 이유로 인해 표준 [hyper-v](tutorial-migrate-hyper-v.md) 또는 [vmware](server-migrate-overview.md) 흐름을 사용 하지 않으려는 경우).
- 사설 클라우드에서 실행 되는 Vm
- Amazon Web Services (AWS) 또는 Google Cloud Platform (GCP)와 같은 공용 클라우드에서 실행 되는 Vm.


## <a name="migration-limitations"></a>마이그레이션 제한 사항

복제를 위해 한 번에 최대 10 개의 컴퓨터를 선택할 수 있습니다. 더 많은 컴퓨터를 마이그레이션하려는 경우 10 그룹으로 복제 합니다.


## <a name="physical-server-requirements"></a>물리적 서버 요구 사항

이 표에는 에이전트 기반 마이그레이션을 사용 하 여 마이그레이션하려는 물리적 서버에 대 한 지원이 요약 되어 있습니다.

**지원** | **세부 정보**
--- | ---
**머신 워크로드** | Azure Migrate 지원 되는 컴퓨터에서 실행 되는 모든 작업 (예를 들어 Active Directory, SQL server 등)의 마이그레이션을 지원 합니다.
**운영 체제** | 최신 정보는 Site Recovery에 대 한 [운영 체제 지원](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) 을 참조 하세요. Azure Migrate는 동일한 운영 체제를 지원 합니다.
**Linux 파일 시스템/게스트 저장소** | 최신 정보는 Site Recovery에 대 한 [Linux 파일 시스템 지원](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) 을 참조 하세요. Azure Migrate는 동일한 Linux 파일 시스템을 지원 합니다.
**네트워크/저장소** | 최신 정보는 Site Recovery에 대 한 [네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#network) 및 [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#storage) 필수 구성 요소를 검토 하세요. Azure Migrate는 동일한 네트워크/저장소 요구 사항을 제공 합니다.
**Azure 요구 사항** | 최신 정보는 Site Recovery에 대 한 [Azure 네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)및 [계산](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) 요구 사항을 검토 하세요. Azure Migrate는 물리적 서버 마이그레이션과 동일한 요구 사항이 있습니다.
**모바일 서비스** | 마이그레이션할 각 컴퓨터에 모바일 서비스 에이전트를 설치 해야 합니다.
**UEFI 부팅** | Azure에서 마이그레이션된 컴퓨터는 자동으로 BIOS 부팅 Azure VM으로 변환 됩니다. Windows Server 2012 이상을 실행 하는 서버만 지원 됩니다.<br/><br/> OS 디스크에는 최대 4 개의 파티션이 있어야 하 고 볼륨은 NTFS로 포맷 되어야 합니다.
**UEFI-보안 부팅**         | 마이그레이션에 지원 되지 않습니다.
**대상 디스크** | Azure에서 컴퓨터는 관리 되는 디스크 (표준 HDD, 프리미엄 SSD)로만 마이그레이션할 수 있습니다.
**디스크 크기** | 2TB OS 디스크 데이터 디스크의 경우 8TB입니다.
**디스크 제한** |  컴퓨터 당 최대 63 디스크
**암호화 된 디스크/볼륨** |  암호화 된 디스크/볼륨의 컴퓨터는 마이그레이션에 지원 되지 않습니다.
**공유된 디스크 클러스터** | 지원 안 됨
**독립 디스크** | 지원됨.
**통과 디스크** | 지원됨.
**NFS** | 컴퓨터에 볼륨으로 탑재 된 NFS 볼륨이 복제 되지 않습니다.
**iSCSI 대상** | ISCSI 대상을 사용 하는 컴퓨터는 에이전트 없는 마이그레이션에 대해 지원 되지 않습니다.
**다중 경로 IO** | 지원 안 됨
**저장소 vMotion** | 지원됨
**팀 Nic** | 지원 안 됨
**IPv6** | 지원 안 됨



## <a name="replication-appliance-requirements"></a>복제 어플라이언스 요구 사항

물리적 서버에서 복제 어플라이언스를 수동으로 설정 하는 경우 테이블에 요약 되어 있는 요구 사항을 준수 하는지 확인 합니다. Azure Migrate 허브에 제공 된 OVA 템플릿을 사용 하 여 Azure Migrate 복제 어플라이언스를 VMware VM으로 설정 하는 경우 어플라이언스는 Windows Server 2016로 설정 되 고 지원 요구 사항을 준수 합니다. 

- [복제 어플라이언스 요구 사항](migrate-replication-appliance.md#appliance-requirements)에 대해 알아봅니다.
- MySQL은 어플라이언스에 설치되어야 합니다. [설치 옵션](migrate-replication-appliance.md#mysql-installation)에 대해 알아봅니다.
- 복제 어플라이언스에서 액세스 해야 하는 [url](migrate-replication-appliance.md#url-access) 에 대해 알아봅니다.

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure로 복제 된 모든 온-프레미스 Vm은이 표에 요약 된 Azure VM 요구 사항을 충족 해야 합니다. Site Recovery에서 복제에 대 한 필수 구성 요소 확인을 실행 하면 일부 요구 사항이 충족 되지 않은 경우 검사가 실패 합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
게스트 운영 체제 | 지원 되는 운영 체제를 확인 합니다.<br/> 지원 되는 운영 체제에서 실행 되는 모든 워크 로드를 마이그레이션할 수 있습니다. | 지원되지 않는 경우 확인이 실패합니다.
게스트 운영 체제 아키텍처 | 64비트. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 크기 | 최대 2,048GB. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 수 | 64개 이하. | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 크기 | 최대 4,095GB | 지원되지 않는 경우 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
공유 VHD | 지원 안 됨 | 지원되지 않는 경우 확인이 실패합니다.
FC 디스크 | 지원 안 됨 | 지원되지 않는 경우 확인이 실패합니다.
BitLocker | 지원 안 됨 | 컴퓨터의 복제를 사용하도록 설정하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다.
VM 이름 | 1~63자 사이입니다.<br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |  Site Recovery에서 컴퓨터 속성의 값을 업데이트합니다.
마이그레이션 후 연결-Windows | 마이그레이션 후 Windows를 실행 하는 Azure Vm에 연결 하려면 다음을 수행 합니다.<br/> -마이그레이션하기 전에 온-프레미스 VM에서 RDP를 사용 하도록 설정 합니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 허용되는지 확인합니다.<br/> 사이트 간 VPN 액세스의 경우 rdp를 사용 하도록 설정 하 고, **Windows Firewall**  ->  **도메인 및 개인** 네트워크의 Windows 방화벽**허용 되는 앱 및 기능** 에서 rdp를 허용 합니다. 또한 운영 체제의 SAN 정책이 **OnlineAll**으로 설정 되어 있는지 확인 합니다. [자세히 알아보기](prepare-for-migration.md). |
마이그레이션 후 연결-Linux | SSH를 사용 하 여 마이그레이션한 후 Azure Vm에 연결 하려면:<br/> 마이그레이션 전에 온-프레미스 컴퓨터에서 Secure Shell 서비스가 시작으로 설정 되어 있고 방화벽 규칙에서 SSH 연결을 허용 하는지 확인 합니다.<br/> 장애 조치 (failover) 후에 Azure VM에서 장애 조치 (failover) 된 VM의 네트워크 보안 그룹 규칙에 대 한 SSH 포트 및 연결 된 Azure 서브넷에 대 한 들어오는 연결을 허용 합니다. 또한 VM에 대 한 공용 IP 주소를 추가 합니다. |  


## <a name="next-steps"></a>다음 단계

물리적 서버 [마이그레이션](tutorial-migrate-physical-virtual-machines.md)
