---
title: 일반적인 질문 - Azure Site Recovery를 사용하여 VMware에서 Azure로 재해 복구 | Microsoft Docs
description: 이 문서에서는 일반적인 질문 fir 온-프레미스 VMware Vm의 Azure로 재해 복구 Azure Site Recovery를 사용 하 여 요약
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 04/23/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: dffbb2c52b4e43eefe6b4f377bd7af529bae8cc5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125562"
---
# <a name="common-questions---vmware-to-azure-replication"></a>일반적인 질문 - VMware에서 Azure로 복제

이 문서에서는 Azure에 온-프레미스 VMware Vm의 재해 복구를 배포 하는 경우 일반적인 질문과 대답을 제공 합니다. 

## <a name="general"></a>일반 
### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM 재해 복구를 위한 하나요?

[에 대 한 자세한](vmware-azure-architecture.md) VMware Vm의 재해 복구에 관련 된 구성 요소입니다. 

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Site Recovery를 사용 하 여 Azure로 VMware Vm을 마이그레이션할 수 있습니다?

예, VMware Vm에 대 한 전체 재해 복구를 설정 하려면 Site Recovery를 사용 하는 것 외에도 사용할 수 있습니다도 Site Recovery 온-프레미스 VMware Vm을 Azure로 마이그레이션할 합니다. 이 시나리오에서는 온-프레미스 VMware VM을 Azure 저장소에 복제합니다. 그런 다음, 온-프레미스에서 Azure로 장애 조치합니다. 장애 조치(failover) 후 Azure VM에서 앱과 워크로드가 실행되며 사용할 수 있습니다. 프로세스는 비슷합니다 전체 재해 복구 설정 제외 하 고 마이그레이션에서 있습니다 수 없습니다. Azure에서 장애 복구 합니다.


### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>내 Azure 계정에 VM을 만들 수 있는 권한이 필요한가요?
구독 관리자인 경우 필요한 복제 권한을 갖고 있습니다. 아닌 경우 리소스 그룹 및 가상 네트워크를 지정할 때 Site Recovery 및 선택한 저장소 계정에 쓸 수 있는 권한이 구성 또는 관리 되는 디스크 구성에 따라 Azure VM을 만들 수 있는 권한이 필요 합니다. [자세히 알아보기](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="what-applications-can-i-replicate"></a>어떤 애플리케이션을 복제할 수 있나요?
[복제 요구 사항](vmware-physical-azure-support-matrix.md##replicated-machines)을 준수하는 VMware VM에서 실행되는 모든 응용 프로그램 또는 작업을 복제할 수 있습니다.
- Site Recovery는 애플리케이션 인식 복제를 지원하므로 앱을 지능형 상태로 장애 조치 및 장애 복구할 수 있습니다.
- SharePoint, Exchange, Dynamics, SQL Server, Active Directory와 같은 Microsoft 애플리케이션과 통합되고, Oracle, SAP, IBM, Red Hat과 같은 선도적인 공급 업체와 긴밀하게 협력합니다.
- [자세히 알아봅니다](site-recovery-workload.md) .

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Azure 게스트 OS 서버 라이선스를 사용할 수 있습니까?
예, Microsoft Software Assurance 고객은 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 활용해서 Azure로 마이그레이션되는 **Windows Server 머신**에 대한 라이선스 비용을 절감하거나 재해 복구에 Azure를 사용할 수 있습니다.

## <a name="security"></a>보안

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Site Recovery에서 VMware 서버에 액세스하는 데 필요한 권한은 무엇인가요?
Site Recovery에서는 다음 작업을 위해 VMware 서버에 액세스해야 합니다.

- Site Recovery 구성 서버를 실행 하는 VMware VM 설정
- 복제할 VM을 자동으로 검색합니다. 


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Site Recovery에서 VMware VM에 액세스하는 데 필요한 권한은 무엇인가요?

- 복제하려면 VMware VM에 Site Recovery 모바일 서비스가 설치되어 실행되고 있어야 합니다. 도구를 수동으로 배포하거나, VM에 대한 복제를 사용하도록 설정할 때 Site Recovery에서 서비스의 푸시 설치를 수행하도록 지정할 수 있습니다. 
- 복제 중에 VM은 Site Recovery와 다음과 같이 통신합니다.
    - VM은 복제 관리를 위해 HTTPS 443 포트에서 구성 서버와 통신합니다.
    - VM은 복제 데이터를 HTTPS 9443 포트(수정 가능)에서 프로세스 서버로 보냅니다.
    - 다중 VM 일관성을 사용하도록 설정하면 VM에서 20004 포트를 통해 서로 통신합니다.


### <a name="is-replication-data-sent-to-site-recovery"></a>복제 데이터를 Site Recovery로 보내는가요?
아니요, Site Recovery는 복제된 데이터를 가로채지 않으며, VM에서 실행되는 항목에 대한 정보를 갖지 않습니다. 복제 데이터는 VMware 하이퍼바이저와 Azure 저장소 간에 교환됩니다. Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.  

Site Recovery는 ISO 27001:2013, 27018, HIPAA, DPA 인증을 받았으며, SOC2 및 FedRAMP JAB 평가를 수행하는 중입니다.


## <a name="pricing"></a>가격
### <a name="how-can-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>VMware 재해 복구에 대 한 대략적인 요금을 계산할 수는 방법

사용할 수는 [가격 책정 계산기](https://aka.ms/asr_pricing_calculator) Site Recovery를 사용 하는 동안 비용을 예측 합니다.

비용에 자세한 예상치를 위한 deployment planner 도구 실행 [VMware](https://aka.ms/siterecovery_deployment_planner)를 사용 하 여 합니다 [비용 예측 보고서](https://aka.ms/asr_DP_costreport)합니다.

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>모든 비용 차이 storage 또는 managed disks로 직접 복제?

관리 디스크는 저장소 계정 보다 약간 다른 요금이 청구 됩니다. [자세한](https://azure.microsoft.com/pricing/details/managed-disks/) 관리 디스크 가격 책정에 대 한 합니다.

## <a name="mobility-service"></a>모바일 서비스

### <a name="where-can-i-find-the-mobility-service-installers"></a>모바일 서비스 설치 관리자는 어디서 찾을 수 있나요?
설치 관리자에는 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** 구성 서버의 폴더입니다.

## <a name="how-do-i-install-the-mobility-service"></a>모바일 서비스를 설치하려면 어떻게 할까요?
다양 한 메서드를 사용 하 여를 복제 하려는 각 VM에 설치 합니다.
- [강제 설치](vmware-physical-mobility-service-overview.md#push-installation)
- [수동 설치](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) UI 또는 Powershell에서 합니다.
- 와 같은 배포 도구를 사용 하 여 배포 [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)합니다.

## <a name="managed-disks"></a>관리 디스크

### <a name="where-does-site-recovery-replicate-data-to"></a>Site Recovery에 대 한 데이터를 복제 하는 위치

Site Recovery는 Azure의 managed disks로 온-프레미스 VMware Vm 및 물리적 서버를 복제 합니다.
- Site Recovery 프로세스 서버는 대상 지역의 캐시 저장소 계정에 복제 로그를 기록합니다.
- 이러한 로그는 관리 되는 디스크에서 복구 지점을 만들 사용 됩니다.
- 장애 조치가 발생 하는 경우 선택한 복구 지점이 대상 관리 되는 디스크를 만드는 데 사용 됩니다.
- 저장소 계정 (이전 2019 년 3 월) 이전에 복제 된 Vm 영향을 받지 않습니다.


### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>저장소 계정에 새 컴퓨터를 복제할 수 있나요?

아니요, 포털에서 3 월 2019부터로 복제할 수 있습니다만 Azure 디스크를 관리 합니다. 

저장소 계정에 새 Vm의 복제만 PowerShell 또는 REST API (버전 2018-01-10 또는 2016-08-10)를 사용 하 여 제공 됩니다.

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Managed disks로 복제 하는 데 이점은 무엇 인가요?

[에 대해 알아봅니다 어떻게](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery 관리 디스크를 사용 하 여 재해 복구를 간소화 합니다.


### <a name="can-i-change-the-managed-disk-type-after-machine-is-protected"></a>변경 관리 되는 디스크 유형을 컴퓨터를 보호

예, 쉽게 수행할 수 있습니다 [관리 되는 디스크의 형식을 변경](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage)합니다. 종류를 변경 하기 전에 Azure portal에서 관리 되는 디스크 리소스를 이동 하 여 디스크에 대 한 SAS URL을 해지 하는 확인 합니다. 개요 블레이드에서 모든 진행 중인 내보내기를 취소 합니다. SAS URL이 해지 되 면 몇 분 내에서 디스크의 형식을 변경 합니다. 그러나 관리 디스크 유형을 변경 하는 경우 Azure Site Recovery에 의해 생성 될 새 복구 지점에 대 한 대기 합니다. 앞으로 장애 조치 또는 테스트 장애 조치에 대해 새 복구 지점을 사용 합니다.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>복제 관리 디스크에서 관리 되지 않는 디스크를 전환할 수 있나요?

아니요,에서 전환에 관리 되지 않는 관리는 지원 되지 않습니다.

## <a name="replication"></a>복제


### <a name="what-are-the-replicated-vm-requirements"></a>복제된 VM에 대한 요구 사항은 무엇인가요?

[자세한](vmware-physical-azure-support-matrix.md##replicated-machines) vmware v M/물리적 서버 요구 사항 및 지원에 대 한 합니다.

### <a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?
VMware VM을 Azure에 복제하는 경우에는 복제가 계속됩니다.


### <a name="can-i-extend-replication"></a>복제를 확장할 수 있나요?
확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-do-an-offline-initial-replication"></a>오프라인 초기 복제를 수행할 수 있나요?
지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-exclude-disks-from-replication"></a>복제에서 디스크 제외
예, 디스크를 제외할 수 있습니다.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>동적 디스크를 사용하여 VM을 복제할 수 있나요?
예, 동적 디스크는 복제할 수 있습니다. 운영 체제 디스크는 기본 디스크여야 합니다.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>다중 VM 일관성에 대해 복제 그룹을 사용하는 경우 새 VM을 기존 복제 그룹에 추가할 수 있나요?
예, 복제를 활성화하면 새 VM을 기존 복제 그룹에 추가할 수 있습니다.
- 복제 시작 된 후 기존 복제 그룹에 VM을 추가할 수 없습니다.
- 기존 Vm에 대 한 복제 그룹을 만들 수 없습니다.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>디스크를 추가하거나 크기를 조정하여 복제 중인 VM을 수정할 수 있나요?

Azure로 VMware 복제의 경우 디스크 크기를 수정할 수 있습니다. 새 디스크를 추가하려는 경우 디스크를 추가하고 VM에 대한 보호를 다시 활성화해야 합니다.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>마이그레이션할 수 온-프레미스 컴퓨터를 새 vCenter Server에 진행 중인 복제에 영향을 주지 않고?
아니요, Vcenter를 변경하거나 마이그레이션하면 진행 중인 복제에 영향을 줍니다. 새 vCenter Server 사용 하 여 Site Recovery를 설정 하 고 컴퓨터에 대 한 복제를 다시 사용 하도록 설정 해야 합니다.

### <a name="can-i-replicate-to-a-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>VNet (Azure storage 방화벽)에 구성 된 캐시/대상 저장소 계정에 복제할 수 있습니까?
아니요, Site Recovery Vnet에서 저장소 복제를 지원 하지 않습니다.





## <a name="component-upgrade"></a>구성 요소 업그레이드

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>내 모바일 서비스 에이전트 또는 구성 서버 버전이 이전 및 내 업그레이드 하지 못했습니다. 어떻게 해야 합니까?  

Site Recovery는 N-4 지원 모델을 따릅니다. [자세한](https://aka.ms/asr_support_statement) 아주 오래 된 버전에서 업그레이드 하는 방법에 대 한 합니다.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Azure Site Recovery의 릴리스 정보/업데이트 롤업을 어디서 찾을 수 있나요?

[에 대해 알아봅니다](site-recovery-whats-new.md) 최신 업데이트에 대 하 고 [롤업 정보를 가져올](service-updates-how-to.md)합니다.

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Azure로 재해 복구에 대 한 업그레이드 정보는 어디서 찾을 수 있습니까?

[에 대 한 자세한](https://aka.ms/asr_vmware_upgrades) 업그레이드 합니다.

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>각 업그레이드에 대 한 원본 컴퓨터를 다시 부팅 해야 하나요?

다시 부팅하는 것이 좋지만, 필수는 아닙니다. [자세히 알아보기](https://aka.ms/asr_vmware_upgrades).


## <a name="configuration-server"></a>구성 서버

### <a name="what-does-the-configuration-server-do"></a>구성 서버에서 수행하는 작업은 무엇인가요?

구성 서버는 다음을 포함하여 온-프레미스 Site Recovery 구성 요소를 실행합니다.
- 구성 서버 자체를 온-프레미스와 Azure 간의 통신을 조정 하 고 데이터 복제를 관리 합니다.
- 프로세스 서버 - 복제 게이트웨이의 역할을 합니다. 복제 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화하며 복제 데이터를 Azure 저장소로 전송합니다. 프로세스 서버 Vm에 모바일 서비스의 푸시 설치를도 않으며 온-프레미스 VMware Vm의 자동 검색을 수행 합니다.
- 마스터 대상 서버 - Azure에서 장애 복구 중에 복제 데이터를 처리합니다.

구성 서버 구성 요소 및 프로세스에 대해 [자세히 알아보세요](vmware-azure-architecture.md).

### <a name="where-do-i-set-up-the-configuration-server"></a>구성 서버를 설정해야 하는 위치는 어떻게 되나요?
구성 서버에는 단일 고가용성 온-프레미스 VMware VM이 필요합니다. 물리적 서버 재해 복구를 위해 물리적 컴퓨터에서 구성 서버를 설치할 수 있습니다.

### <a name="what-do-i-need-for-the-configuration-server"></a>구성 서버에 대 한 하나요?

[필수 구성 요소](vmware-azure-deploy-configuration-server.md#prerequisites)를 검토하세요.

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>구성 서버는 템플릿을 사용하는 대신 수동으로 설정할 수 있나요?
좋습니다 있습니다 [구성 서버 VM을 만들려면](vmware-azure-deploy-configuration-server.md) 최신 버전의 OVF 템플릿 사용 하 여 합니다. 수 없는 이유로, 예를 들어가 없는 VMware 서버에 액세스할 수 있습니다 [다운로드](physical-azure-set-up-source.md) 설치 파일을 포털에서 구성 서버를 설정 합니다.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>구성 서버에서 둘 이상의 지역에 복제할 수 있나요?
아니요. 이 작업을 수행 하려면 각 지역에서 구성 서버를 해야 합니다.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Azure에서 구성 서버를 호스팅할 수 있나요?
가능한 경우 구성 서버를 실행하는 Azure VM에서 온-프레미스 VMware 인프라 및 VM과 통신해야 합니다. 이 대기 시간을 추가 하 고 진행 중인 복제에 영향을 줍니다.

### <a name="how-do-i-update-the-configuration-server"></a>구성 서버를 업데이트하려면 어떻게 할까요?

[에 대해 알아봅니다](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) 구성 서버를 업데이트 하는 방법입니다.
- 최신 업데이트 정보를 찾을 수 있습니다 합니다 [Azure 업데이트 페이지](https://azure.microsoft.com/updates/?product=site-recovery)합니다.
- 포털에서 최신 버전을 다운로드할 수 있습니다. 최신 버전의에서 구성 서버를 직접 다운로드할 수 또는 합니다 [Microsoft 다운로드 센터](https://aka.ms/asrconfigurationserver)합니다.
- 현재 버전 보다 오래 된 4 개 버전 버전을 사용 하는 경우 참조 우리의 [문을 지원](https://aka.ms/asr_support_statement) 업그레이드 지침에 대 한 합니다.

### <a name="should-i-back-up-the-configuration-server"></a>구성 서버를 백업 해야 하나요?
구성 서버의 예약된 정기 백업을 수행하는 것이 좋습니다.
- 성공적으로 장애 복구 되 장애 복구 VM 구성 서버 데이터베이스에 존재 해야 합니다.
- 구성 서버는 실행 및 연결 된 상태에서 여야 합니다.
- [자세한](vmware-azure-manage-configuration-server.md) 일반적인 구성 서버 관리 작업에 대 한 합니다.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>구성 서버를 설정할 때 MySQL을 수동으로 다운로드하여 설치할 수 있나요?

예. MySQL을 다운로드하여 **C:\Temp\ASRSetup** 폴더에 넣습니다. 그런 다음, 수동으로 설치합니다. 구성 서버 VM을 설정하고 조건에 동의하면 MySQL이 **다운로드 및 설치**에 **이미 설치됨**으로 표시됩니다.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>MySQL을 다운로드하지 않고 Site Recovery에서 설치할 수 있나요?

예. MySQL 설치 관리자를 다운로드하여 **C:\Temp\ASRSetup** 폴더에 넣습니다.  구성 서버 VM 설정한 경우 조건에 동의 하 고 클릭 **다운로드 및 설치**합니다. 포털에는 MySQL을 설치 하 고 추가한 설치 관리자를 사용 합니다.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>다른 용도로 구성 서버 VM을 사용할 수 있나요?
아니요, 구성 서버로만 VM을 사용해야 합니다. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>구성 서버를 복제하고 오케스트레이션에 사용할 수 있나요?
아니요, 등록 문제를 방지 하려면 새 구성 서버를 설정 해야 합니다.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>구성 서버 등록 된 자격 증명 모음을 변경할 수 있나요?
아니요. 자격 증명 모음을 구성 서버에 연결 된 후에 변경할 수 없습니다. 검토 [이 문서에서는](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) 다시 등록 하는 방법에 대 한 자세한 합니다.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>VMware VM 및 실제 서버의 재해 복구에 동일한 구성 서버를 사용할 수 있나요?
예, 하지만 해당 물리적 컴퓨터는 VMware VM에 다시 실패할 수 있습니다.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>구성 서버의 암호는 어디서 다운로드할 수 있나요?
[에 대해 알아봅니다 방법](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) 암호를 다운로드 합니다.

### <a name="where-can-i-download-vault-registration-keys"></a>자격 증명 모음 등록 키는 어디서 다운로드할 수 있나요?

Recovery Services 자격 증명 모음에서 클릭 **구성 서버** 에 **Site Recovery 인프라** > **관리**합니다. 그런 다음 **서버**를 선택 **등록 키 다운로드** 자격 증명 모음 자격 증명 파일을 다운로드 합니다.

## <a name="process-server"></a>프로세스 서버

### <a name="unable-to-select-process-server-during-enable-replication"></a>복제 활성화 하는 동안 프로세스 서버를 선택할 수 없습니다.

9.24 버전에서 향상 된 기능 제공에 내용이 [제품 설명서](vmware-azure-manage-process-server.md#process-server-selection-guidance) 스케일 아웃 프로세스 서버를 설정 하는 경우에 합니다. 프로세스 서버 제한을 방지 하 고 비정상 프로세스 서버의 사용을 방지 하기 위해입니다.

### <a name="what-should-i-do-to-obtain-accurate-health-status-of-process-server"></a>프로세스 서버의 정확한 상태를 가져오려면 어떻게 해야 합니까?

Site Recovery 구성 요소를 업그레이드 합니다 [최신 버전](service-updates-how-to.md#links-to-currently-supported-update-rollups) (적어도 9.24 이상).

## <a name="failover-and-failback"></a>장애 조치 및 장애 복구
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>사용할 수 있습니까 프로세스 서버가 온-프레미스에 장애 복구에 대 한?
데이터 전송 대기 시간을 방지 하기 위해 장애 복구를 위해 Azure에서 프로세스 서버를 만드는 것이 좋습니다. 또한 구성 서버에 Azure 공용 네트워크를 사용 하 여 원본 Vm 네트워크를 구분 하는 경우 반드시 Azure에서 장애 복구를 위해 만든 프로세스 서버를 사용 합니다.

### <a name="can-i-retain-the-ip-address-on-failover"></a>장애 조치(failover) 시 IP 주소를 유지할 수 있나요?
예, 장애 조치(failover) 시 IP 주소를 유지할 수 있습니다. 장애 조치 하기 전에 VM에 대 한 [계산 및 네트워크] 설정에서 대상 IP 주소를 지정 하는 것을 확인 합니다. 또한 장애 복구 하는 동안 IP 주소 충돌을 방지 하려면 장애 조치 시 컴퓨터를 종료 합니다.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>대상 VM 크기 또는 장애 조치 하기 전에 VM 유형을 변경할 수 있나요?
예, 형식 또는 포털에서 복제 된 Vm의 계산 및 네트워크 설정에 대 한 장애 조치 전에 언제 든 지 VM의 크기를 변경할 수 있습니다.

### <a name="how-far-back-can-i-recover"></a>복구할 수 있는 가장 오랜 복구 지점은 어떻게 되나요?
VMware에서 Azure로 복구하는 경우 사용할 수 있는 가장 오래된 복구 지점은 72시간입니다.

### <a name="how-do-i-access-azure-vms-after-failover"></a>장애 조치 후 Azure VM에 액세스하려면 어떻게 할까요?
장애 조치 후에 보안 인터넷 연결, 사이트 간 VPN 또는 Azure ExpressRoute를 통해 Azure VM에 액세스할 수 있습니다. 연결하려면 여러 가지 사항을 준비해야 합니다. [자세히 알아보기](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>장애 조치된 데이터는 복원되나요?

Azure는 복원을 위해 디자인되었습니다. Site Recovery는 Azure SLA에 따라 보조 Azure 데이터 센터에 장애 조치하도록 설계되었습니다. 장애 조치가 발생하면 메타데이터와 자격 증명 모음이 자격 증명 모음에 대해 선택한 지리적 지역과 동일한 지역에 유지되고 있는지 확인합니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?
[장애 조치](site-recovery-failover.md)는 자동이 아닙니다. 포털에서 번의 클릭으로 장애 조치를 시작 하거나 사용할 수 있습니다 [PowerShell](/powershell/module/az.recoveryservices) 장애 조치를 트리거할 수 있습니다.

### <a name="can-i-fail-back-to-a-different-location"></a>다른 위치로 장애 조치할 수 있나요?
예, Azure에 장애 조치한 경우 원본을 사용할 수 없을 때 다른 위치로 장애 복구할 수 있습니다. [자세히 알아보기](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>장애 복구하는 데 VPN 또는 ExpressRoute가 필요한 이유는 무엇인가요?
Azure에서 장애 복구하는 경우 Azure의 데이터가 온-프레미스 VM으로 다시 복사되고 개인 액세스가 필요합니다.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>장애 조치(failover) 후 Azure VM의 크기를 조정할 수 있나요?
아니요, 장애 조치(failover) 후에 대상 VM의 크기 또는 형식을 변경할 수 없습니다.


## <a name="automation-and-scripting"></a>자동화 및 스크립팅

### <a name="can-i-set-up-replication-with-scripting"></a>스크립팅을 사용하여 복제를 설정할 수 있나요?
예. Rest API, PowerShell 또는 Azure SDK를 사용하여 Site Recovery 워크플로를 자동화할 수 있습니다. [자세한 정보](vmware-azure-disaster-recovery-powershell.md)

## <a name="performance-and-capacity"></a>성능 및 용량
### <a name="can-i-throttle-replication-bandwidth"></a>복제 대역폭을 제한할 수 있나요?
예. [자세히 알아보기](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>다음 단계
* 지원 요구 사항을 [검토](vmware-physical-azure-support-matrix.md)합니다.
* VMware에서 Azure로의 복제를 [설정](vmware-azure-tutorial.md)합니다.
