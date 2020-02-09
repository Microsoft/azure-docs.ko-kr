---
title: Azure Site Recovery의 Hyper-v 재해 복구에 대 한 일반적인 질문
description: 이 문서에는 Azure Site Recovery 사이트를 사용하여 온-프레미스 Hyper-V VM과 Azure 간 재해 복구를 설정하는 방법과 관련된 일반적인 질문이 요약되어 있습니다.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 7c5f55fbea67567ddf7a2afa6a61f6c76568d829
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498198"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>일반적인 질문 - Hyper-V와 Azure 간 재해 복구

이 문서에서는 온-프레미스 Hyper-V VM을 Azure로 복제할 때 표시되는 일반적인 질문에 대한 대답을 제공합니다. 

## <a name="general"></a>일반

### <a name="how-is-site-recovery-priced"></a>Site Recovery 비용은 어떻게 책정하는가요?
[Azure Site Recovery 가격](https://azure.microsoft.com/pricing/details/site-recovery/) 세부 정보를 검토하세요.

### <a name="how-do-i-pay-for-azure-vms"></a>Azure VM에 대한 요금을 지불하려면 어떻게 할까요?
복제 중에 데이터는 Azure Storage로 복제되며 어떠한 VM 변경에도 지불하지 않습니다. Azure에 장애 조치를 실행하면 Site Recovery에서 Azure IaaS 가상 머신을 자동으로 만듭니다. 그러면 Azure에서 사용하는 컴퓨팅 리소스에 대한 요금이 청구됩니다.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>범용 v2 저장소 계정으로 복제할 때 비용 차이가 있나요?

GPv2 저장소 Azure Site Recovery 계정에 발생 하는 트랜잭션 비용이 증가 하는 것을 볼 수 있습니다. 변경을 예측 하려면 [자세히](../storage/common/storage-account-upgrade.md#pricing-and-billing) 알아보세요.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Hyper-V에서 Site Recovery를 통해 복제를 오케스트레이션하기 위해 필요한 것은 무엇입니까?

Hyper-V 호스트 서버에서 필요한 사항은 배포 시나리오에 따라 달라집니다. Hyper-V 필수 구성 요소를 확인해 보세요.

* [Azure로 Hyper-V VM 복제(VMM 없이)](site-recovery-hyper-v-site-to-azure.md)
* [Azure로 Hyper-V VM 복제(VMM 사용)](site-recovery-vmm-to-azure.md)
* [보조 데이터 센터에 Hyper-V VM 복제](site-recovery-vmm-to-vmm.md)
* 보조 데이터 센터에 복제하는 경우 [Hyper-V VM에 대해 지원되는 게스트 운영 체제](https://technet.microsoft.com/library/mt126277.aspx)에 대해 알아보세요.
* Azure에 복제하는 경우에는 [Azure에서 지원되는](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)모든 게스트 운영 체제를 Site Recovery에서도 지원합니다.

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Hyper-V가 클라이언트 운영 체제에서 실행 중인 경우 VM을 보호할 수 있습니까?
아니요, VM은 지원되는 Windows 서버 컴퓨터를 실행하는 Hyper-V 호스트 서버에 위치해야 합니다. 클라이언트 컴퓨터를 보호해야 하는 경우 물리적 컴퓨터를 [Azure](site-recovery-vmware-to-azure.md) 또는 [보조 데이터 센터](site-recovery-vmware-to-vmware.md)로 복제할 수 있습니다.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hyper-V 호스트가 VMM 클라우드에 있어야 합니까?
보조 데이터 센터에 복제하려는 경우 Hyper-V VM은 VMM 클라우드에 위치한 Hyper-V 호스트 서버에 있어야 합니다. Azure로 복제하려는 경우 VMM 클라우드 존재 여부와 상관없이 VM을 복제할 수 있습니다. Azure로 Hyper-V 복제에 대해 [자세히 알아보세요](tutorial-hyper-v-to-azure.md).


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Hyper-V 2세대 가상 머신을 Azure로 복제할 수 있습니까?
예. 장애 조치(failover) 동안 Site Recovery가 컴퓨터를 2세대에서 1세대로 변환합니다. 장애 복구 시 컴퓨터가 다시 2세대로 변환됩니다. [자세히 알아보기](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>VMM 서버가 하나밖에 없는 경우 VMM을 사용하여 Site Recovery를 배포할 수 있습니까?

예. VMM 클라우드의 Hyper-V 서버에서 VM을 Azure로 복제하거나 같은 서버의 VMM 클라우드 간에 복제할 수 있습니다. 온-프레미스 간 복제는 기본 사이트와 보조 사이트에 VMM 서버가 있는 경우에 사용하는 것이 좋습니다. 

### <a name="what-do-i-need-in-azure"></a>Azure에는 무엇이 필요한가요?
Azure 구독, Recovery Services 스토리지, 스토리지 계정 및 가상 네트워크가 필요합니다. 자격 증명 모음, 스토리지 계정 및 네트워크는 동일한 지역에 있어야 합니다.

### <a name="what-azure-storage-account-do-i-need"></a>필요한 Azure Storage 계정은 무엇인가요?
LRS 또는 GRS 스토리지 계정이 필요합니다. 지역 정전이 발생하거나 주 지역을 복구할 수 없는 경우에 데이터를 복원할 수 있도록 GRS를 사용하는 것이 좋습니다. Premium Storage가 지원됩니다.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>내 Azure 계정에 VM을 만들 수 있는 권한이 필요한가요?
구독 관리자인 경우 필요한 복제 권한을 갖고 있습니다. 구독 관리자가 아닌 경우 Site Recovery를 구성할 때 지정한 리소스 그룹 및 가상 네트워크에 Azure VM을 만들 수 있는 권한과 선택한 스토리지 계정에 쓸 수 있는 권한이 필요합니다. [자세히 알아보기](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>복제 데이터를 Site Recovery로 보내는가요?
아니요, Site Recovery는 복제된 데이터를 가로채지 않으며, VM에서 실행되는 항목에 대한 정보를 갖지 않습니다. 복제 데이터는 Hyper-V 호스트와 Azure Storage 간에 교환됩니다. Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.  

Site Recovery는 ISO 27001:2013, 27018, HIPAA, DPA 인증을 받았으며, SOC2 및 FedRAMP JAB 평가를 수행하는 중입니다.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>지리적 지역 내에서 온-프레미스 메타 데이터를 유지할 수 있나요?
예. 한 지역에 자격 증명 모음을 만들 때 Site Recovery에서 사용되는 모든 메타데이터가 해당 지역의 지리적 경계 내에 유지되도록 합니다.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery는 복제를 암호화합니까?
예, 전송 중 암호화 및 [Azure의 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption)가 모두 지원됩니다.


## <a name="deployment"></a>배포

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Azure로 복제할 때 Hyper-V를 사용하여 수행할 수 있는 작업은 무엇인가요?

- **재해 복구**: 전체 재해 복구를 설정할 수 있습니다. 이 시나리오에서는 온-프레미스 Hyper-V VM을 Azure Storage에 복제합니다.
    - VM을 Azure에 복제할 수 있습니다. 온-프레미스 인프라를 사용할 수 없는 경우 Azure로 장애 조치(Failover)합니다.
    - 장애 조치를 수행하면 복제된 데이터를 사용하여 Azure VM이 만들어집니다. Azure VM의 앱 및 워크로드에 액세스할 수 있습니다.
    - 온-프레미스 데이터 센터를 다시 사용할 수 있게 되면 Azure에서 온-프레미스 사이트로 다시 장애 복구(Failback)할 수 있습니다.
- **마이그레이션**: Site Recovery를 사용하여 온-프레미스 Hyper-V VM을 Azure Storage로 마이그레이션할 수 있습니다. 그런 다음, 온-프레미스에서 Azure로 장애 조치합니다. 장애 조치(failover) 후 Azure VM에서 앱과 워크로드가 실행되며 사용할 수 있습니다.


### <a name="what-do-i-need-on-premises"></a>온-프레미스에는 무엇이 필요한가요?

하나 이상의 VM을 하나 이상의 독립 실행형 또는 클러스터형 Hyper-V 호스트에서 실행해야 합니다. 또한 System Center VMM(Virtual Machine Manager)에서 관리되는 호스트에서 실행되는 VM을 복제할 수도 있습니다.
- VMM을 실행하지 않는 경우에는 Site Recovery 배포 중에 Hyper-V 호스트와 클러스터를 Hyper-V 사이트에 모읍니다. Site Recovery 에이전트(Azure Site Recovery 공급자와 Recovery Services 에이전트)를 각 Hyper-V 컴퓨터에 설치합니다.
- Hyper-V 호스트가 VMM 클라우드에 있는 경우 VMM에서 복제를 오케스트레이션합니다. VMM 서버에 Site Recovery 공급자를, 각 Hyper-V 호스트에 Recovery Services 에이전트를 설치합니다. VMM 논리/VM 네트워크와 Azure VNet 간을 매핑합니다.
- Hyper-V에서 Azure로 복제 아키텍처에 대해 [자세히 알아보세요](hyper-v-azure-architecture.md).

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Hyper-V 클러스터에 있는 VM을 복제할 수 있나요?

예, Site Recovery는 클러스터형 Hyper-V 호스트를 지원합니다. 다음 사항에 유의하세요.

- 클러스터의 모든 노드는 동일한 자격 증명 모음에 등록되어야 합니다.
- VMM을 사용하지 않는 경우 클러스터의 모든 Hyper-V 호스트를 동일한Hyper-V 사이트에 추가해야 합니다.
- Azure Site Recovery 공급자와 Recovery Services 에이전트를 클러스터의 각 Hyper-V 호스트에 설치하고 각 호스트를 Hyper-V 사이트에 추가합니다.
- 클러스터에서 특정 단계를 수행 해야 하는 것은 아닙니다.
- Hyper-V의 Deployment Planner 도구를 실행하는 경우 이 도구는 해당 도구가 실행되고 있으며 VM이 실행되고 있는 노드에서 프로필 데이터를 수집합니다. 이 도구는 꺼져 있는 노드에서는 데이터를 수집할 수 없고 해당 노드를 추적합니다. 노드가 실행되게 되면 이 도구는 VM 프로필 데이터를 수집하기 시작합니다(VM이 프로필 VM 목록에 속하고 해당 노드에서 실행되는 경우).
- Site Recovery 자격 증명 모음에 있는 Hyper-V 호스트의 VM을 동일한 클러스터의 다른 Hyper-V 호스트 또는 독립 실행형 호스트로 마이그레이션하면 해당 VM에 대한 복제는 영향을 받지 않습니다. Hyper-V 호스트는 [필수 구성 요소](hyper-v-azure-support-matrix.md#on-premises-servers)를 충족하고 Site Recovery 자격 증명 모음에서 구성되어야 합니다. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Hyper-V가 클라이언트 운영 체제에서 실행 중인 경우 VM을 보호할 수 있습니까?
아니요, VM은 지원되는 Windows 서버 컴퓨터를 실행하는 Hyper-V 호스트 서버에 위치해야 합니다. 클라이언트 컴퓨터를 보호해야 하는 경우 물리적 컴퓨터를 [Azure](physical-azure-disaster-recovery.md)로 복제할 수 있습니다.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Hyper-V 2세대 가상 머신을 Azure로 복제할 수 있습니까?
예. 장애 조치(failover) 동안 Site Recovery가 컴퓨터를 2세대에서 1세대로 변환합니다. 장애 복구 시 컴퓨터가 다시 2세대로 변환됩니다.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>SDK와 함께 Site Recovery 시나리오를 자동화할 수 있습니까?
예. Rest API, PowerShell 또는 Azure SDK를 사용하여 Site Recovery 워크플로를 자동화할 수 있습니다. PowerShell을 사용하여 Hyper-V를 Azure로 복제하기 위한 현재 지원되는 시나리오:

- [PowerShell을 사용하여 VMM 없이 Hyper-V 복제](hyper-v-azure-powershell-resource-manager.md)
- [Powershell을 사용하여 VMM으로 Hyper-V 복제](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>복제

### <a name="where-do-on-premises-vms-replicate-to"></a>온-프레미스 VM은 어디에 복제되는가요?
데이터는 Azure Storage에 복제됩니다. 장애 조치를 실행하면 Site Recovery에서 스토리지 계정으로부터 Azure VM을 자동으로 만듭니다.

### <a name="what-apps-can-i-replicate"></a>복제할 수 있는 앱은 무엇인가요?
[복제 요구 사항](hyper-v-azure-support-matrix.md#replicated-vms)을 준수하는 Hyper-V VM에서 실행되는 모든 응용 프로그램 또는 작업을 복제할 수 있습니다. Site Recovery는 애플리케이션 인식 복제를 지원하므로 앱을 지능형 상태로 장애 조치 및 장애 복구할 수 있습니다. SharePoint, Exchange, Dynamics, SQL Server, Active Directory와 같은 Microsoft 애플리케이션과 통합되고, Oracle, SAP, IBM, Red Hat과 같은 선도적인 공급 업체와 긴밀하게 협력합니다. [자세히 알아봅니다](site-recovery-workload.md) .

### <a name="whats-the-replication-process"></a>복제 프로세스란?

1. 초기 복제가 트리거될 때 Hyper-V VM 스냅샷이 만들어집니다.
2. VM의 가상 하드 디스크는 모두 Azure에 복사될 때까지 하나씩 복제됩니다. 이 작업은 VM 크기 및 네트워크 대역폭에 따라 시간이 오래 걸릴 수 있습니다. 네트워크 대역폭을 높이는 방법을 알아봅니다.
3. 초기 복제 진행 중에 디스크가 변경될 경우, Hyper-V 복제 로그(.hrl)로 Hyper-V 복제본 복제 추적자가 이러한 변경 내용을 추적합니다. 이러한 로그 파일은 디스크와 동일한 폴더에 있습니다. 각 디스크에는 보조 스토리지로 전송되는 .hrl 파일이 연결되어 있습니다. 초기 복제 진행 중에는 스냅샷과 로그 파일이 디스크 리소스를 사용합니다.
4. 초기 복제가 완료되면 VM 스냅샷은 삭제됩니다.
5. 로그의 디스크 변경 내용이 동기화되고 부모 디스크로 병합합니다.
6. 초기 복제를 마친 후에는 가상 머신에 대한 보호 완료 작업이 실행됩니다. 네트워크 및 기타 사후 설정을 구성하므로 VM이 보호됩니다.
7. 이 단계에서 VM 설정이 장애 조치에 대비하고 있는지 확인할 수 있습니다. VM에 대한 재해 복구 훈련(장애 조치 테스트)을 실행하여 장애 조치가 기대한 대로 작동하는지 확인할 수 있습니다.
8. 초기 복제 후에는 복제 정책에 따라 델타 복제가 시작됩니다.
9. 변경 내용이 .hrl 파일에 로깅됩니다. 복제를 위해 구성된 각 디스크에는 연결된 .hrl 파일이 있습니다.
10. 로그는 고객의 스토리지 계정으로 전송됩니다. 로그가 Azure로 전송 중인 경우 주 디스크의 변경 내용은 같은 폴더의 다른 로그 파일에 추적됩니다.
11. 초기 및 델타 복제가 진행되는 동안 Azure Portal에서 VM을 모니터링할 수 있습니다.

복제 프로세스에 대해 [자세히 알아봅니다](hyper-v-azure-architecture.md#replication-process).

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>사이트 간 VPN을 사용하여 Azure에 복제할 수 있나요?

Site Recovery는 공용 엔드포인트을 사용 하거나 Express 경로 Microsoft 피어 링을 사용 하 여 온-프레미스에서 Azure storage로 데이터를 복제 합니다. 사이트 간 VPN 네트워크를 통한 복제는 지원되지 않습니다.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>ExpressRoute를 사용하여 Azure에 복제할 수 있나요?

예, ExpressRoute를 사용하여 VM을 Azure로 복제할 수 있습니다. Site Recovery는 공용 엔드포인트을 통해 Azure Storage 계정에 데이터를 복제 하 고 Site Recovery 복제를 위해 [Microsoft 피어 링](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) 을 설정 해야 합니다. VM에서 Azure 가상 네트워크로 장애 조치한 후에는 [프라이빗 피어링](../expressroute/expressroute-circuit-peerings.md#privatepeering)을 사용하여 해당 VM에 액세스할 수 있습니다.


### <a name="why-cant-i-replicate-over-vpn"></a>VPN을 통해 복제할 수 없는 이유는 무엇인가요?

Azure에 복제 하는 경우 복제 트래픽은 Azure Storage 계정의 공용 엔드포인트에 도달 합니다. 따라서 Express 경로 (Microsoft 피어 링)를 사용 하 여 공용 인터넷을 통해서만 복제할 수 있으며 VPN은 작동 하지 않습니다. 

### <a name="what-are-the-replicated-vm-requirements"></a>복제된 VM에 대한 요구 사항은 무엇인가요?

복제의 경우 Hyper-V VM에서 지원되는 운영 체제를 실행해야 합니다. 또한 VM에서 Azure VM에 대한 요구 사항을 충족해야 합니다. 지원 매트릭스에서 [자세히 알아보세요](hyper-v-azure-support-matrix.md#replicated-vms).

### <a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?

Hyper-V VM은 30초(Premium Storage 제외), 5분 또는 15분마다 복제할 수 있습니다.

### <a name="can-i-extend-replication"></a>복제를 확장할 수 있나요?
확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-do-an-offline-initial-replication"></a>오프라인 초기 복제를 수행할 수 있나요?
지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-exclude-disks"></a>디스크를 제외할 수 있나요?
예, 디스크는 복제에서 제외할 수 있습니다. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>동적 디스크를 사용하여 VM을 복제할 수 있나요?
예, 동적 디스크는 복제할 수 있습니다. 운영 체제 디스크는 기본 디스크여야 합니다.



## <a name="security"></a>보안

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Site Recovery에서 Hyper-V 호스트에 액세스하는 데 필요한 권한은 무엇인가요?

Site Recovery는 선택한 VM을 복제할 Hyper-V 호스트에 대한 액세스 권한이 있어야 합니다. Site Recovery는 Hyper-V 호스트에 다음을 설치합니다.

- VMM을 실행하지 않는 경우 Site Recovery 공급자 및 Recovery Services 에이전트가 각 호스트에 설치됩니다.
- VMM을 실행하는 경우 Recovery Services 에이전트가 각 호스트에 설치됩니다. 공급자는 VMM 서버에서 실행됩니다.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Site Recovery는 Hyper-V VM에 무엇을 설치하나요?

Site Recovery는 복제용으로 설정된 Hyper-V VM에서는 어떤 항목도 명시적으로 설치하지 않습니다.




## <a name="failover-and-failback"></a>장애 조치 및 장애 복구


### <a name="how-do-i-fail-over-to-azure"></a>Azure로 장애 조치(Failover)하려면 어떻게 해야 하나요?

온-프레미스 Hyper-V VM에서 Azure로 계획된 또는 계획되지 않은 장애 조치를 실행할 수 있습니다.

- 계획된 장애 조치를 실행할 경우 데이터 손실을 방지하기 위해 원본 VM이 종료됩니다.
- 주 사이트에 액세스할 수 없는 경우 계획되지 않은 장애 조치를 실행할 수 있습니다.
- 단일 컴퓨터에 장애 조치를 수행하거나 복구 계획을 만들어서 여러 컴퓨터의 장애 조치를 오케스트레이션할 수 있습니다.
- 장애 조치 (Failover)는 두 부분으로 구성 됩니다.
    - 첫 번째 장애 조치를 실행한 후에 Azure에서 만들어진 복제본 VM이 표시되어야 합니다. 필요한 경우 VM에 공용 IP 주소를 할당할 수 있습니다.
    - 그런 다음 복제본 Azure VM에서 워크로드에 액세스하려면 장애 조치를 커밋합니다.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>장애 조치 후 Azure VM에 액세스하려면 어떻게 할까요?
장애 조치 후에 보안 인터넷 연결, 사이트 간 VPN 또는 Azure ExpressRoute를 통해 Azure VM에 액세스할 수 있습니다. 연결하려면 여러 가지 사항을 준비해야 합니다. [자세히 알아보기](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>장애 조치된 데이터는 복원되나요?
Azure는 복원을 위해 디자인되었습니다. Site Recovery는 Azure SLA에 따라 보조 Azure 데이터 센터에 장애 조치하도록 설계되었습니다. 장애 조치가 발생하면 메타데이터와 자격 증명 모음이 자격 증명 모음에 대해 선택한 지리적 지역과 동일한 지역에 유지되고 있는지 확인합니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?
[장애 조치](site-recovery-failover.md)는 자동이 아닙니다. 포털에서 한 번의 클릭으로 장애 조치 (failover)를 시작 하거나 [PowerShell](/powershell/module/az.recoveryservices) 을 사용 하 여 장애 조치 (failover)를 트리거할 수 있습니다.

### <a name="how-do-i-fail-back"></a>장애 복구(Failback)하려면 어떻게 하나요?

온-프레미스 인프라를 다시 실행한 후 장애 복구를 수행할 수 있습니다. 장애 복구는 다음 3단계로 수행됩니다.

1. 몇 가지 다른 옵션을 사용하여 Azure에서 온-프레미스 사이트로 계획된 장애 조치(Failover)를 시작합니다.

    - 가동 중지 시간 최소화: 이 옵션을 사용하면 Site Recovery가 장애 조치(Failover) 전에 데이터를 동기화합니다. 변경된 데이터 블록을 확인하고 온-프레미스 사이트에 다운로드하며 Azure VM은 그 동안에도 계속 실행되어 가동 중지 시간을 최소화합니다. 장애 조치 완료를 수동으로 지정해야 하는 경우 Azure VM이 종료되고 최종 델타 변경 내용이 복사되며 장애 조치가 시작됩니다.
    - 전체 다운로드:이 옵션에서는 장애 조치(Failover) 중에 데이터가 동기화됩니다. 이 옵션은 전체 디스크를 다운로드합니다. 체크섬을 계산하지 않아 더 빠르지만 가동 중지 시간은 더 늘어납니다. 상당 시간 동안 복제본 Azure VM을 실행하였거나 온-프레미스 VM이 삭제된 경우에 이 옵션을 사용합니다.

2. 동일한 VM으로 또는 대체 VM으로 장애 복구(Failback)하도록 선택할 수 있습니다. VM이 없는 경우 Site Recovery가 이를 만들도록 지정할 수 있습니다.
3. 초기 동기화가 완료되면 장애 조치를 완료하도록 선택합니다. 완료 되 면 온-프레미스 VM에 로그인 하 여 모든 것이 예상 대로 작동 하는지 확인할 수 있습니다. Azure Portal에서 Azure VM이 중지된 것을 확인할 수 있습니다.
4. 장애 조치(Failover)를 커밋하여 완료하고 다시 온-프레미스 VM에서 워크로드에 액세스합니다.
5. 워크로드가 장애 복구되면 역방향 복제를 활성화하여 온-프레미스 VM이 다시 Azure에 복제되게 합니다.

### <a name="can-i-fail-back-to-a-different-location"></a>다른 위치로 장애 조치할 수 있나요?
예, Azure에 장애 조치한 경우 원본을 사용할 수 없을 때 다른 위치로 장애 복구할 수 있습니다. [자세히 알아보기](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).
