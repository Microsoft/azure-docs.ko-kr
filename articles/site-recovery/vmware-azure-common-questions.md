---
title: Azure Site Recovery 사용 하는 VMware 재해 복구에 대 한 일반적인 질문
description: Azure Site Recovery를 사용 하 여 Azure에 온-프레미스 VMware Vm의 재해 복구에 대 한 일반적인 질문에 대 한 답을 얻을 수 있습니다.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 1d61b8556038959f6acab447fc0510830b1dd943
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89054974"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>VMware에서 Azure로 복제에 대한 일반적인 질문

이 문서에서는 온-프레미스 VMware Vm (가상 머신)의 재해 복구를 Azure에 배포할 때 나타날 수 있는 일반적인 질문에 답변 합니다.

## <a name="general"></a>일반

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM 재해 복구에는 무엇이 필요 한가요?

VMware Vm의 재해 복구 [와 관련 된 구성 요소에 대해 알아봅니다](vmware-azure-architecture.md) .

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Site Recovery를 사용 하 여 VMware Vm을 Azure로 마이그레이션할 수 있나요?

예. Site Recovery를 사용 하 여 VMware Vm에 대 한 전체 재해 복구를 설정 하는 것 외에도 Site Recovery를 사용 하 여 온-프레미스 VMware Vm을 Azure로 마이그레이션할 수 있습니다. 이 시나리오에서는 온-프레미스 VMware Vm을 Azure Storage에 복제 합니다. 그런 다음, 온-프레미스에서 Azure로 장애 조치합니다. 장애 조치(failover) 후 Azure VM에서 앱과 워크로드가 실행되며 사용할 수 있습니다. 이 과정은 전체 재해 복구를 설정 하는 것과 비슷합니다. 단, 마이그레이션 시에는 Azure에서 장애 복구 (failback)를 수행할 수 없습니다.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>내 Azure 계정에 VM을 만들 수 있는 권한이 필요한가요?

구독 관리자인 경우 필요한 복제 권한을 갖고 있습니다. 관리자가 아닌 경우 다음 작업을 수행할 수 있는 권한이 필요 합니다.

- Site Recovery를 구성할 때 지정한 리소스 그룹 및 가상 네트워크에서 Azure VM을 만듭니다.
- 구성에 따라 선택한 저장소 계정 또는 관리 디스크에 씁니다.

필요한 권한에 [대해 자세히 알아보세요](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) .

### <a name="what-applications-can-i-replicate"></a>어떤 애플리케이션을 복제할 수 있나요?

[복제 요구 사항을](vmware-physical-azure-support-matrix.md#replicated-machines)충족 하는 VMware VM에서 실행 중인 모든 앱 또는 워크 로드를 복제할 수 있습니다.

- Site Recovery는 응용 프로그램 인식 복제를 지원 하므로 앱이 지능형 상태로 장애 조치 (failover) 및 장애 복구 (failover) 될 수 있습니다.
- Site Recovery는 SharePoint, Exchange, Dynamics, SQL Server 및 Active Directory와 같은 Microsoft 응용 프로그램과 통합 됩니다. Oracle, SAP, IBM 및 Red Hat을 비롯 한 선두 공급 업체와 긴밀 하 게 연동 됩니다.

[자세히 알아봅니다](site-recovery-workload.md) .

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Azure에서 게스트 OS 서버 라이선스를 사용할 수 있나요?

예, Microsoft 소프트웨어 보증 고객은 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 를 사용 하 여 azure로 마이그레이션된 Windows Server 컴퓨터에 대 한 라이선스 비용을 절감 하거나 재해 복구를 위해 azure를 사용할 수 있습니다.

## <a name="security"></a>보안

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>VMware 서버에 대 한 액세스 Site Recovery 필요 한가요?

Site Recovery에서는 다음 작업을 위해 VMware 서버에 액세스해야 합니다.

- Site Recovery 구성 서버를 실행 하는 VMware VM을 설정 합니다.
- 복제할 VM을 자동으로 검색합니다.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>VMware Vm에 대 한 액세스 Site Recovery 필요 한가요?

- 복제 하려면 VMware VM에 Site Recovery 모바일 서비스가 설치 되어 실행 중 이어야 합니다. 도구를 수동으로 배포 하거나 VM에 대 한 복제를 사용 하도록 설정할 때 서비스의 강제 설치를 수행 하도록 Site Recovery 지정할 수 있습니다.
- 복제 중에 VM은 Site Recovery와 다음과 같이 통신합니다.
    - Vm은 복제 관리를 위해 HTTPS 포트 443의 구성 서버와 통신 합니다.
    - Vm은 HTTPS 포트 9443에서 프로세스 서버로 복제 데이터를 보냅니다. 이 설정은 수정할 수 있습니다.
    - 다중 VM 일관성을 사용하도록 설정하면 VM에서 20004 포트를 통해 서로 통신합니다.

### <a name="is-replication-data-sent-to-site-recovery"></a>복제 데이터를 Site Recovery로 보내는가요?

아니요, Site Recovery는 복제 된 데이터를 가로채 않으며 Vm에서 실행 되는 항목에 대 한 정보를 포함 하지 않습니다. 복제 데이터는 VMware 하이퍼바이저와 Azure Storage 간에 교환 됩니다. Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.

Site Recovery는 ISO 27001:2013 및 27018, HIPAA 및 DPA에 대해 인증 됩니다. SOC2 및 FedRAMP JAB 평가 프로세스에 있습니다.

## <a name="pricing"></a>가격 책정

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>VMware 재해 복구에 대 한 대략적인 요금을 계산 하는 어떻게 할까요??

[가격 계산기](https://aka.ms/asr_pricing_calculator) 를 사용 하 여 Site Recovery 사용 하는 동안 비용을 계산할 수 있습니다.

자세한 비용을 보려면 [VMware](https://aka.ms/siterecovery_deployment_planner) 용 deployment planner 도구를 실행 하 고 [비용 예측 보고서](https://aka.ms/asr_DP_costreport)를 사용 합니다.

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>저장소에 대 한 복제와 관리 디스크에 대 한 직접 복제 간의 차이가 있나요?

관리 디스크는 저장소 계정과 약간 다르게 청구 됩니다. 관리 디스크 가격 책정에 [대해 자세히 알아보세요](https://azure.microsoft.com/pricing/details/managed-disks/) .

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>범용 v2 저장소 계정으로 복제할 때 비용 차이가 있나요?

GPv2 저장소 Azure Site Recovery 계정에 발생 하는 트랜잭션 비용이 증가 하는 것을 볼 수 있습니다. 변경을 예측 하려면 [자세히](../storage/common/storage-account-upgrade.md#pricing-and-billing) 알아보세요.

## <a name="mobility-service"></a>모바일 서비스

### <a name="where-can-i-find-the-mobility-service-installers"></a>모바일 서비스 설치 관리자는 어디서 찾을 수 있나요?

설치 관리자는 구성 서버의%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 폴더에 있습니다.

## <a name="how-do-i-install-the-mobility-service"></a>모바일 서비스를 설치하려면 어떻게 할까요?

복제 하려는 각 VM에서 다음 몇 가지 방법 중 하나로 서비스를 설치 합니다.

- [강제 설치](vmware-physical-mobility-service-overview.md#push-installation)
- UI 또는 PowerShell에서 [수동 설치](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui)
- [Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) 와 같은 배포 도구를 사용 하 여 배포

## <a name="managed-disks"></a>관리 디스크

### <a name="where-does-site-recovery-replicate-data-to"></a>Site Recovery에서 데이터를 복제 하는 위치

Site Recovery 온-프레미스 VMware Vm 및 물리적 서버를 Azure의 managed disks에 복제 합니다.

- Site Recovery 프로세스 서버는 대상 지역의 캐시 저장소 계정에 복제 로그를 씁니다.
- 이러한 로그는 **asrseeddisk**접두사가 있는 Azure 관리 디스크에서 복구 위치를 만드는 데 사용 됩니다.
- 장애 조치 (failover)가 발생 하면 선택 하는 복구 지점이 새 대상 관리 디스크를 만드는 데 사용 됩니다. 이 관리 디스크는 Azure의 VM에 연결 되어 있습니다.
- 이전에 저장소 계정에 복제 된 Vm (3 월 2019 일 이전)에는 영향을 주지 않습니다.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>저장소 계정에 새 컴퓨터를 복제할 수 있나요?

아니요. 3 월 2019 부터는 Azure Portal에서 Azure managed disks에만 복제할 수 있습니다.

저장소 계정에 새 Vm을 복제 하는 것은 PowerShell ([Az. RecoveryServices module version 1.4.5](https://www.powershellgallery.com/packages/Az.RecoveryServices/1.4.5)) 또는 REST API (버전 2018-01-10 또는 2016-08-10)을 사용 하는 경우에만 사용할 수 있습니다. PowerShell 명령을 사용 하 여 복제를 설정 하 [는 방법을 알아봅니다](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell) .

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>관리 디스크로 복제할 때의 이점은 무엇 인가요?

[방법 알아보기](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery 관리 디스크를 사용 하 여 재해 복구를 간소화 합니다.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>컴퓨터가 보호 된 후 관리 디스크 유형을 변경할 수 있나요?

예, 지속적인 복제를 위해 [관리 디스크의 유형을 쉽게 변경할](../virtual-machines/windows/convert-disk-storage.md) 수 있습니다. 형식을 변경 하기 전에 관리 디스크에서 공유 액세스 서명 URL이 생성 되지 않도록 합니다.

1. Azure Portal에서 **관리 디스크** 리소스로 이동 하 고 **개요** 블레이드에서 공유 액세스 서명 URL 배너가 있는지 확인 합니다.
1. 배너가 있는 경우 선택 하 여 진행 중인 내보내기를 취소 합니다.
1. 다음 몇 분 내에 디스크의 유형을 변경 합니다. 관리 디스크 유형을 변경 하는 경우 새 복구 지점이 Azure Site Recovery 생성 될 때까지 기다립니다.
1. 이후에 테스트 장애 조치 (failover) 또는 장애 조치 (failover)를 위해 새 복구 지점이 사용 됩니다.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>관리 디스크에서 관리 되지 않는 디스크로 복제를 전환할 수 있나요?

아니요. 관리 됨에서 관리 되지 않음으로의 전환은 지원 되지 않습니다.

## <a name="replication"></a>복제

### <a name="what-are-the-replicated-vm-requirements"></a>복제된 VM에 대한 요구 사항은 무엇인가요?

VMware Vm 및 물리적 서버에 대 한 지원 요구 사항에 대해 [자세히 알아보세요](vmware-physical-azure-support-matrix.md#replicated-machines) .

### <a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?

VMware VM을 Azure에 복제하는 경우에는 복제가 계속됩니다.

### <a name="can-i-extend-replication"></a>복제를 확장할 수 있나요?

확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-do-an-offline-initial-replication"></a>오프라인 초기 복제를 수행할 수 있나요?

오프 라인 복제는 지원 되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="what-is-asrseeddisk"></a>Asrseeddisk 란?

모든 원본 디스크에 대해 Azure의 관리 디스크에 데이터가 복제 됩니다. 이 디스크에는 **asrseeddisk**접두사가 있습니다. 원본 디스크의 복사본과 모든 복구 지점 스냅숏을 저장 합니다.

### <a name="can-i-exclude-disks-from-replication"></a>디스크를 복제에서 제외할 수 있나요?

예, 디스크를 제외할 수 있습니다.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>동적 디스크가 있는 Vm을 복제할 수 있나요?

예, 동적 디스크는 복제할 수 있습니다. 운영 체제 디스크는 기본 디스크여야 합니다.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>다중 VM 일관성에 대해 복제 그룹을 사용하는 경우 새 VM을 기존 복제 그룹에 추가할 수 있나요?

예, 복제를 활성화하면 새 VM을 기존 복제 그룹에 추가할 수 있습니다. 단,

- 복제가 시작 된 후에는 기존 복제 그룹에 VM을 추가할 수 없습니다.
- 기존 Vm에 대 한 복제 그룹을 만들 수 없습니다.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>디스크를 추가하거나 크기를 조정하여 복제 중인 VM을 수정할 수 있나요?

Azure에 대 한 VMware 복제의 경우 원본 Vm의 디스크 크기를 수정할 수 있습니다. 새 디스크를 추가 하려면 디스크를 추가 하 고 VM에 대 한 보호를 다시 활성화 해야 합니다.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>진행 중인 복제에 영향을 주지 않고 온-프레미스 컴퓨터를 새 vCenter Server 마이그레이션할 수 있나요?

아니요. VMware Vcenter 또는 migration이 변경 되 면 진행 중인 복제에 영향을 줍니다. 새 vCenter Server를 사용 하 여 Site Recovery를 설정 하 고 컴퓨터에 대 한 복제를 다시 사용 하도록 설정 합니다.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>가상 네트워크 (Azure 방화벽 포함)가 구성 된 캐시 또는 대상 저장소 계정에 복제할 수 있나요?

아니요, Site Recovery는 가상 네트워크에서 Azure Storage에 대 한 복제를 지원 하지 않습니다.

### <a name="what-is-the-frequency-of-generation-of-crash-consistent-recovery-points"></a>크래시 일치 복구 지점의 생성 빈도는 어떻게 되나요?

Site Recovery는 5 분 마다 크래시 일치 복구 지점이 생성 됩니다.

## <a name="component-upgrade"></a>구성 요소 업그레이드

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>이전 버전의 모바일 서비스 에이전트 또는 구성 서버가 이전 버전 이며 업그레이드가 실패 했습니다. 어떻게 해야 합니까?

Site Recovery는 N-1 지원 모델을 따릅니다. 이전 버전에서 업그레이드 하는 방법에 [대해 자세히 알아보세요](https://aka.ms/asr_support_statement) .

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Azure Site Recovery에 대 한 릴리스 정보 및 업데이트 롤업을 어디에서 찾을 수 있나요?

[새 업데이트에 대해 알아보고](site-recovery-whats-new.md) [롤업 정보를 가져옵니다](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Azure로 재해 복구에 대 한 업그레이드 정보는 어디서 찾을 수 있나요?

[업그레이드에 대해 알아봅니다](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>업그레이드 마다 원본 컴퓨터를 다시 부팅 해야 하나요?

다시 부팅 하는 것이 좋지만 각 업그레이드에 반드시 필요한 것은 아닙니다. [자세히 알아보기](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>구성 서버

### <a name="what-does-the-configuration-server-do"></a>구성 서버에서 수행하는 작업은 무엇인가요?

구성 서버는 다음을 포함하여 온-프레미스 Site Recovery 구성 요소를 실행합니다.

- 구성 서버 자체입니다. 서버는 온-프레미스 구성 요소와 Azure 간의 통신을 조정 하 고 데이터 복제를 관리 합니다.
- 복제 게이트웨이의 역할을 하는 프로세스 서버. 이 서버:
    1. 복제 데이터를 받습니다.
    2. 캐싱, 압축 및 암호화를 사용하여 데이터를 최적화합니다.
    3. 데이터를 Azure Storage 보냅니다.
  또한 프로세스 서버는 Vm에서 모바일 서비스의 푸시 설치를 수행 하 고 온-프레미스 VMware Vm의 자동 검색을 수행 합니다.
- Azure에서 장애 복구 (failback) 중에 복제 데이터를 처리 하는 마스터 대상 서버입니다.

구성 서버 구성 요소 및 프로세스에 대해 [자세히 알아보세요](vmware-azure-architecture.md).

### <a name="where-do-i-set-up-the-configuration-server"></a>구성 서버를 설정해야 하는 위치는 어떻게 되나요?

구성 서버에 대해 항상 사용 가능한 단일 온-프레미스 VMware VM이 필요 합니다. 물리적 서버 재해 복구의 경우 물리적 컴퓨터에 구성 서버를 설치 합니다.

### <a name="what-do-i-need-for-the-configuration-server"></a>구성 서버에는 무엇이 필요 한가요?

[필수 구성 요소](vmware-azure-deploy-configuration-server.md#prerequisites)를 검토 합니다.

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>구성 서버는 템플릿을 사용하는 대신 수동으로 설정할 수 있나요?

최신 버전의 Open Virtualization Format (파일) 템플릿을 사용 하 여 [구성 서버 VM을 만드는](vmware-azure-deploy-configuration-server.md) 것이 좋습니다. 템플릿을 사용할 수 없는 경우 (예: VMware 서버에 대 한 액세스 권한이 없는 경우) 포털에서 설치 파일을 [다운로드](physical-azure-set-up-source.md) 하 고 구성 서버를 설정 합니다.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>구성 서버에서 둘 이상의 지역에 복제할 수 있나요?

아니요. 둘 이상의 지역에 복제 하려면 각 지역에 구성 서버가 필요 합니다.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Azure에서 구성 서버를 호스팅할 수 있나요?

가능 하더라도 구성 서버를 실행 하는 Azure VM은 온-프레미스 VMware 인프라 및 Vm과 통신 해야 합니다. 이 통신은 대기 시간을 추가 하 고 진행 중인 복제에 영향을 줍니다.

### <a name="how-do-i-update-the-configuration-server"></a>구성 서버를 업데이트하려면 어떻게 할까요?

구성 서버를 업데이트 하는 방법을 [알아봅니다](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) .

- 최신 업데이트 정보는 [Azure 업데이트 페이지](https://azure.microsoft.com/updates/?product=site-recovery)에서 찾을 수 있습니다.
- 포털에서 최신 버전을 다운로드할 수 있습니다. 또는 [Microsoft 다운로드 센터](https://aka.ms/asrconfigurationserver)에서 직접 최신 버전의 구성 서버를 다운로드할 수 있습니다.
- 버전이 현재 버전 보다 이전 버전이 4 개 이상인 경우에는 업그레이드 지침에 대 한 [지원 문을](https://aka.ms/asr_support_statement) 참조 하세요.

### <a name="should-i-back-up-the-configuration-server"></a>구성 서버를 백업 해야 하나요?

구성 서버의 예약된 정기 백업을 수행하는 것이 좋습니다.

- 장애 복구를 성공적으로 수행 하려면 장애 복구 중인 VM이 구성 서버 데이터베이스에 있어야 합니다.
- 구성 서버가 실행 중이 고 연결 된 상태 여야 합니다.
- 일반적인 구성 서버 관리 작업에 [대해 자세히 알아보세요](vmware-azure-manage-configuration-server.md) .

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>구성 서버를 설정할 때 MySQL을 수동으로 다운로드하여 설치할 수 있나요?

예. MySQL을 다운로드하여 C:\Temp\ASRSetup 폴더에 넣습니다. 그런 다음 수동으로 설치 합니다. 구성 서버 VM을 설정하고 조건에 동의하면 MySQL이 **다운로드 및 설치**에 **이미 설치됨**으로 표시됩니다.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>MySQL을 다운로드하지 않고 Site Recovery에서 설치할 수 있나요?

예. MySQL 설치 관리자를 다운로드하여 C:\Temp\ASRSetup 폴더에 넣습니다. 구성 서버 VM을 설정 하는 경우 사용 약관에 동의 하 고 **다운로드 및 설치**를 선택 합니다. 포털에서 추가 된 설치 관리자를 사용 하 여 MySQL을 설치 합니다.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>다른 용도로 구성 서버 VM을 사용할 수 있나요?

아니요. 구성 서버에만 VM을 사용 합니다.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>구성 서버를 복제하고 오케스트레이션에 사용할 수 있나요?

아니요. 등록 문제를 방지 하기 위해 새로운 구성 서버를 설정 합니다.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>구성 서버가 등록 된 자격 증명 모음을 변경할 수 있나요?

아니요. 자격 증명 모음이 구성 서버와 연결 된 후에는 변경할 수 없습니다. 다른 자격 증명 모음에 구성 서버를 등록 하는 방법에 [대해 알아봅니다](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) .

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>VMware Vm과 물리적 서버의 재해 복구에 동일한 구성 서버를 사용할 수 있나요?

예. 단, 물리적 컴퓨터는 VMware VM 으로만 장애 복구 될 수 있습니다.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>구성 서버의 암호는 어디서 다운로드할 수 있나요?

암호를 다운로드 하는 방법을 [알아봅니다](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) .

### <a name="where-can-i-download-vault-registration-keys"></a>자격 증명 모음 등록 키는 어디서 다운로드할 수 있나요?

Recovery Services 자격 증명 모음에서 **Site Recovery 인프라**관리에서 **구성 서버** 를 선택  >  **Manage**합니다. 그런 다음 **서버**에서 **등록 키 다운로드** 를 선택 하 여 자격 증명 모음 자격 증명 파일을 다운로드 합니다.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>단일 구성 서버를 사용 하 여 여러 vCenter 인스턴스를 보호할 수 있나요?

예, 단일 구성 서버는 여러 vCenters에서 Vm을 보호할 수 있습니다.  구성 서버에 추가할 수 있는 vCenter 인스턴스 수에는 제한이 없지만 단일 구성 서버가 보호할 수 있는 Vm 수에 대 한 제한이 적용 됩니다.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>단일 구성 서버가 vCenter 내에서 여러 클러스터를 보호할 수 있나요?

예, Azure Site Recovery는 여러 클러스터에서 Vm을 보호할 수 있습니다.

## <a name="process-server"></a>프로세스 서버

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>복제를 사용 하도록 설정할 때 프로세스 서버를 선택할 수 없는 이유는 무엇입니까?

9.24 이상 버전의 업데이트는 이제 [복제를 사용 하도록 설정할 때 프로세스 서버의 상태](vmware-azure-enable-replication.md#enable-replication)를 표시 합니다. 이 기능은 프로세스 서버 제한을 방지 하 고 비정상 프로세스 서버 사용을 최소화 하는 데 도움이 됩니다.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>정확한 상태 정보를 보려면 프로세스 서버를 9.24 이상 버전으로 업데이트 어떻게 할까요??

[버전 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups)부터 프로세스 서버의 상태를 나타내는 추가 경고가 추가 되었습니다. 모든 경고가 생성 되도록 [Site Recovery 구성 요소를 9.24 이상 버전으로 업데이트](service-updates-how-to.md#links-to-currently-supported-update-rollups) 합니다.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>프로세스 서버의 고가용성을 보장 하려면 어떻게 해야 하나요?

두 개 이상의 프로세스 서버를 구성 하면 비정상 프로세스 서버에서 작업 중인 프로세스 서버로 보호 된 컴퓨터를 유연 하 게 이동할 수 있습니다. 프로세스 서버 [간에 vm 이동](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)에서 정의 된 단계를 통해 명시적으로 또는 수동으로 컴퓨터를 시작 해야 합니다.

## <a name="failover-and-failback"></a>장애 조치(failover) 및 장애 복구(failback)

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>장애 복구 (failback)를 위해 온-프레미스 프로세스 서버를 사용할 수 있나요?

데이터 전송 대기 시간을 방지 하기 위해 장애 복구를 위해 Azure에서 프로세스 서버를 만드는 것이 좋습니다. 또한 원본 Vm 네트워크를 구성 서버에 있는 Azure 연결 네트워크와 분리 한 경우 장애 복구를 위해 Azure에서 만든 프로세스 서버를 사용 하는 것이 필수적입니다.

### <a name="can-i-keep-the-ip-address-on-failover"></a>장애 조치 (failover) 시 IP 주소를 유지할 수 있나요?

예, 장애 조치 (failover) 시 IP 주소를 유지할 수 있습니다. 장애 조치 (failover) 전에 VM에 대 한 **계산 및 네트워크** 설정에서 대상 IP 주소를 지정 했는지 확인 합니다. 또한 장애 조치 (failover) 중에 IP 주소 충돌을 방지 하기 위해 장애 조치 (failover) 시 컴퓨터를 종료 합니다.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>장애 조치 (failover) 전에 대상 VM 크기 또는 VM 유형을 변경할 수 있나요?

예, 장애 조치 (failover) 전에 언제 든 지 VM의 유형 또는 크기를 변경할 수 있습니다. 포털에서 복제 된 VM에 대 한 **계산 및 네트워크** 설정을 사용 합니다.

### <a name="how-far-back-can-i-recover"></a>복구할 수 있는 가장 오랜 복구 지점은 어떻게 되나요?

VMware에서 Azure로의 경우 사용할 수 있는 가장 오래 된 복구 지점은 72 시간입니다.

### <a name="how-do-i-access-azure-vms-after-failover"></a>장애 조치 후 Azure VM에 액세스하려면 어떻게 할까요?

장애 조치 (failover) 후에는 보안 인터넷 연결, 사이트 간 VPN 또는 Azure Express 경로를 통해 Azure Vm에 액세스할 수 있습니다. 연결 하려면 여러 항목을 준비 해야 합니다. [자세히 알아보기](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>장애 조치 (failover) 데이터 복원 력이 있나요?

Azure는 복원을 위해 디자인되었습니다. Site Recovery는 Azure SLA (서비스 수준 계약)에서 요구 하는 대로 보조 Azure 데이터 센터에 장애 조치 (failover)를 위해 설계 되었습니다. 장애 조치 (failover)가 발생 하는 경우 메타 데이터 및 자격 증명 모음이 자격 증명 모음에 대해 선택한 것과 동일한 지역에 유지 되는지 확인 합니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?

[장애 조치 (Failover)](site-recovery-failover.md) 는 자동이 아닙니다. 포털에서 한 번만 선택 하 여 장애 조치 (failover)를 시작 하거나 [PowerShell](/powershell/module/az.recoveryservices) 을 사용 하 여 장애 조치 (failover)를 트리거할 수 있습니다.

### <a name="can-i-fail-back-to-a-different-location"></a>다른 위치로 장애 조치할 수 있나요?

예. Azure로 장애 조치 (failover) 한 경우 원래 위치를 사용할 수 없는 경우 다른 위치로 장애 복구 (failback) 할 수 있습니다. [자세히 알아보기](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>장애 복구를 위해 개인 피어 링이 있는 VPN 또는 Express 경로를 사용 해야 하는 이유는 무엇 인가요?

Azure에서 장애 복구 (failback) 하는 경우 Azure의 데이터가 온-프레미스 VM에 다시 복사 되 고 개인 액세스가 필요 합니다.


## <a name="automation-and-scripting"></a>자동화 및 스크립팅

### <a name="can-i-set-up-replication-with-scripting"></a>스크립팅을 사용하여 복제를 설정할 수 있나요?

예. Rest API, PowerShell 또는 Azure SDK를 사용 하 여 Site Recovery 워크플로를 자동화할 수 있습니다. [자세히 알아보기](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>성능 및 용량

### <a name="can-i-throttle-replication-bandwidth"></a>복제 대역폭을 제한할 수 있나요?

예. [자세히 알아보기](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>다음 단계

- 지원 요구 사항을 [검토](vmware-physical-azure-support-matrix.md)합니다.
- VMware에서 Azure로의 복제를 [설정](vmware-azure-tutorial.md)합니다.
