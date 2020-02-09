---
title: Azure Site Recovery 서비스에 대 한 일반적인 질문
description: 이 문서에서는 Azure Site Recovery에 대해 널리 사용 되는 일반적인 질문을 설명 합니다.
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: a9d0ae4a6e60a72bbb1148aca1a75c44506b2e9e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760354"
---
# <a name="general-questions-about-azure-site-recovery"></a>Azure Site Recovery에 대 한 일반적인 질문

이 문서에서는 Azure Site Recovery에 대 한 자주 묻는 질문을 요약 합니다. 특정 시나리오의 경우 다음 문서를 검토 하세요.

- [Azure에 대 한 Azure VM 재해 복구에 대 한 질문](azure-to-azure-common-questions.md)
- [Azure에 대 한 VMware VM 재해 복구에 대 한 질문](vmware-azure-common-questions.md)
- [Azure로의 Hyper-v VM 재해 복구에 대 한 질문](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>일반

### <a name="what-does-site-recovery-do"></a>Site Recovery의 기능은 무엇입니까?
Site Recovery는 온-프레미스 가상 머신 및 물리적 서버에서 Azure로, 그리고 온-프레미스 컴퓨터에서 보조 데이터 센터로의 지역 간 Azure VM 복제 작업을 오케스트레이션 및 자동화하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. [자세히 알아보기](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Docker 디스크가 있는 가상 컴퓨터를 보호할 수 있나요?

아니요, 이것은 지원되지 않는 시나리오입니다.

## <a name="service-providers"></a>서비스 공급 기업

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>서비스 공급자입니다. Site Recovery는 전용 및 공유 인프라 모델에 대해 작동합니까?
예, Site Recovery는 전용 및 공유 인프라 모델 모두에 대해 작동합니다.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>서비스 공급자의 경우 내 테넌트의 ID가 Site Recovery 서비스와 공유됩니까?
아닙니다. 테넌트 ID는 익명으로 유지됩니다. 사용자의 테넌트는 Site Recovery 포털에 액세스할 필요가 없습니다. 서비스 공급자 관리자만 포털과 상호 작용합니다.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>테넌트 애플리케이션 데이터가 Azure로 이동됩니까?
서비스 공급자가 소유하고 있는 사이트 간에 복제할 경우 애플리케이션 데이터가 Azure로 이동되지 않습니다. 데이터는 전송 중에 암호화되어 서비스 공급자 사이트 간에 직접 복제됩니다.

Azure로 복제하는 경우 애플리케이션 데이터가 Azure Storage로 전송되지만 Site Recovery 서비스로는 전송되지 않습니다. 데이터는 전송 중에 암호화되어 Azure에 암호화된 상태로 남아 있습니다.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>내 테넌트로 Azure 서비스에 대한 청구서가 발급됩니까?
아닙니다. Azure의 청구 관계는 서비스 공급자와 직접 유지됩니다. 서비스 공급자는 해당 테넌트에 대한 특정 청구서를 생성하는 일을 담당합니다.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Azure로 복제할 때 항상 Azure에서 가상 머신을 실행해야 합니까?
아니요, 데이터는 구독의 Azure 저장소로 복제 됩니다. 테스트 장애 조치(failover)(DR 드릴) 또는 실제 장애 조치(failover)를 수행하면 Site Recovery가 구독에서 자동으로 가상 머신을 만듭니다.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Azure에 복제할 때 테넌트 수준의 격리가 보장되나요?
예.

### <a name="what-platforms-do-you-currently-support"></a>현재 어떤 플랫폼이 지원됩니까?
Azure Pack, 클라우드 플랫폼 시스템 및 시스템 센터 기반(2012 이상)의 배포가 지원됩니다. [자세히 알아봅니다](https://technet.microsoft.com/library/dn850370.aspx) .

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>단일 Azure Pack 및 단일 VMM 서버 배포가 지원되나요?
예, Hyper-V 가상 머신을 Azure에 복제하거나 서비스 공급자 사이트 간에 복제할 수 있습니다.  서비스 공급자 사이트 간에 복제할 경우 Azure Runbook 통합을 사용할 수 없습니다.

## <a name="pricing"></a>가격 책정

### <a name="where-can-i-find-pricing-information"></a>가격 정보는 어디에서 찾을 수 있나요?
[Site Recovery 가격 책정](https://azure.microsoft.com/pricing/details/site-recovery/) 세부 정보를 검토 합니다.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Site Recovery 사용 중 대략적인 요금을 계산하려면 어떻게 하나요?

[가격 계산기](https://aka.ms/asr_pricing_calculator) 를 사용 하 여 Site Recovery를 사용 하는 동안 비용을 추정할 수 있습니다.

비용에 대 한 자세한 추정치를 보려면 [VMware](https://aka.ms/siterecovery_deployment_planner) 또는 [hyper-v](https://aka.ms/asr-deployment-planner)에 대 한 deployment planner 도구를 실행 하 고 [비용 예측 보고서](https://aka.ms/asr_DP_costreport)를 사용 합니다.


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>이제 관리 디스크는 VMware Vm 및 물리적 서버를 복제 하는 데 사용 됩니다. 관리 디스크를 사용 하는 캐시 저장소 계정에 대 한 추가 요금이 발생 하나요?

아니요, 캐시에 대 한 추가 요금은 없습니다. 표준 저장소 계정에 복제 하는 경우이 캐시 저장소는 동일한 대상 저장소 계정의 일부입니다.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>한 달 이상 Azure Site Recovery를 사용해 왔습니다. 모든 보호된 인스턴스에 대해 계속 처음 31일 무료를 적용받나요?

예. 보호되는 모든 인스턴스에 대해 처음 31일 동안은 Azure Site Recovery 요금이 발생하지 않습니다. 예를 들어 지난 6 개월 동안 인스턴스 10 개를 보호 하 고 11 번째 인스턴스를 Azure Site Recovery에 연결 하는 경우 처음 31 일 동안에는 11 번째 인스턴스에 대 한 요금이 부과 되지 않습니다. 처음 10 개 인스턴스는 31 일 이상 보호 되기 때문에 Azure Site Recovery 요금은 계속 해 서 발생 합니다.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>처음 31일 동안 다른 Azure 요금이 발생하나요?

예, Site Recovery 보호 된 인스턴스의 처음 31 일 동안 무료로 제공 되는 경우에도 Azure Storage, 저장소 트랜잭션 및 데이터 전송에 대 한 요금이 부과 될 수 있습니다. 복구된 가상 머신도 Azure Compute 요금이 발생할 수 있습니다.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>재해 복구 드릴/테스트 장애 조치 (failover)를 수행 하는 데 드는 비용이 있나요?

DR 드릴에 대한 별도의 비용은 없습니다. 테스트 장애 조치 (failover) 후 VM이 생성 된 후 계산 요금이 청구 됩니다.



## <a name="security"></a>보안

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>복제 데이터가 Site Recovery 서비스로 전송되나요?
아니요, Site Recovery는 복제된 데이터를 가로채거나 사용자의 가상 머신 또는 물리적 서버에서 실행 중인 항목에 대한 정보를 보유하지 않습니다.
복제 데이터는 온-프레미스 Hyper-V 호스트, VMware 하이퍼바이저 또는 물리적 서버와 Azure Storage 또는 보조 사이트 간에 교환됩니다. Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.  

Site Recovery는 ISO 27001:2013, 27018, HIPAA, DPA 인증을 받았으며, SOC2 및 FedRAMP JAB 평가를 수행하는 중입니다.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>규정 준수를 위해 온-프레미스 메타데이터까지도 지리적으로 같은 지역에 남아 있어야 합니다. Site Recovery가 도움이 되나요?
예. 사용자가 한 지역에 Site Recovery 자격 증명 모음을 만들면 복제 및 장애 조치(failover)를 활성화하고 오케스트레이션하는 데 필요한 모든 메타데이터가 해당 지역의 지리적 경계 내에 남아 있습니다.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery는 복제를 암호화합니까?
온-프레미스 사이트 간에 가상 머신과 물리적 서버를 복제할 때 전송 중 암호화가 지원됩니다. Azure에 복제되는 가상 머신과 물리적 서버의 경우 전송 중 암호화 및 [정지된 암호화(Azure 내)](https://docs.microsoft.com/azure/storage/storage-service-encryption)가 모두 지원됩니다.

### <a name="how-can-i-enforce-tls-12-on-all-on-premises-azure-site-recovery-components"></a>모든 온-프레미스 Azure Site Recovery 구성 요소에서 TLS 1.2을 적용 하려면 어떻게 해야 하나요?
복제 된 항목에 설치 된 모바일 에이전트는 TLS 1.2 에서만 프로세스 서버와 통신 합니다. 그러나 구성 서버에서 Azure로, 프로세스 서버에서 Azure로의 통신은 TLS 1.1 또는 1.0에 있을 수 있습니다. 모든 구성 서버 및 프로세스 서버에서 TLS 1.2을 적용 하는 [지침](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) 을 따르세요.


## <a name="disaster-recovery"></a>재해 복구

### <a name="what-can-site-recovery-protect"></a>Site Recovery로 무엇을 보호할 수 있습니까?
* **Azure VM**: Site Recovery는 지원되는 Azure VM에서 실행 중인 모든 워크로드를 복제할 수 있습니다.
* **VMware 가상 머신**: Site Recovery는 Hyper-V VM에서 실행 중인 모든 워크로드를 보호할 수 있습니다.
* **물리적 서버**: Site Recovery는 Windows 또는 Linux를 실행하는 물리적 서버를 보호할 수 있습니다.
* **VMware 가상 머신**: Site Recovery는 VMware VM에서 실행 중인 모든 워크로드를 보호할 수 있습니다.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Site Recovery로 어떤 워크로드를 보호할 수 있습니까?
Site Recovery를 사용하여 지원되는 VM 또는 물리적 서버에서 실행되는 대부분의 워크로드를 보호할 수 있습니다. Site Recovery는 애플리케이션 인식 복제를 제공하므로 앱을 지능형 상태로 복구할 수 있습니다. SharePoint, Exchange, Dynamics, SQL Server, Active Directory와 같은 Microsoft 애플리케이션과 통합되고, Oracle, SAP, IBM, Red Hat 등의 선두 공급 업체 제품과 긴밀하게 작동합니다. [자세히 알아봅니다](site-recovery-workload.md) .

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Site Recovery로 지사의 재해 복구를 관리할 수 있습니까?
예. 지사에서 Site Recovery를 사용하여 복제를 오케스트레이션하고 장애 조치(failover)를 수행하면 중앙의 한 위치에 모든 지사 워크로드의 통합되지 않은 오케스트레이션 및 보기가 표시됩니다. 지사를 방문하지 않고 본사에서 간편하게 모든 지사의 장애 조치(failover)를 수행하고 재해 복구를 관리할 수 있습니다.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Azure Vm에 대해 재해 복구가 지원 되나요?

예, Site Recovery는 Azure 지역 간의 Azure Vm에 대 한 재해를 지원 합니다. Azure VM 재해 복구에 대 한 [일반적인 질문을 검토](azure-to-azure-common-questions.md) 합니다.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>VMware Vm에 대해 재해 복구를 지원 하나요?

예, Site Recovery 온-프레미스 VMware Vm의 재해 복구를 지원 합니다. VMware Vm의 재해 복구에 대 한 [일반적인 질문을 검토](vmware-azure-common-questions.md) 합니다.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Hyper-v Vm에 대해 재해 복구를 지원 하나요?
예, Site Recovery 온-프레미스 Hyper-v Vm의 재해 복구를 지원 합니다. Hyper-v Vm의 재해 복구에 대 한 [일반적인 질문을 검토](hyper-v-azure-common-questions.md) 합니다.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>물리적 서버에 대해 재해 복구를 지원 하나요?
예, Site Recovery는 Windows 및 Linux를 실행 하는 온-프레미스 물리적 서버에서 Azure 또는 보조 사이트로의 재해 복구를 지원 합니다. [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)와[보조 사이트](vmware-physical-secondary-support-matrix.md#replicated-vm-support)간 재해 복구를 위한 요구 사항에 대해 알아봅니다.
물리적 서버는 장애 조치 (failover) 후 Azure에서 Vm으로 실행 됩니다. Azure에서 온-프레미스 물리적 서버로 장애 복구 (Failback)는 현재 지원 되지 않습니다. VMware 가상 머신으로만 장애 복구 (failback) 할 수 있습니다.





## <a name="replication"></a>복제

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>사이트 간 VPN을 통해 Azure에 복제할 수 있습니까?
Azure Site Recovery는 공용 엔드포인트을 통해 Azure storage 계정 또는 관리 디스크로 데이터를 복제 합니다. 사이트 간 VPN을 통해 복제되지 않습니다. 

### <a name="why-cant-i-replicate-over-vpn"></a>VPN을 통해 복제할 수 없는 이유는 무엇인가요?

Azure에 복제 하는 경우 복제 트래픽은 Azure Storage의 공용 엔드포인트에 도달 합니다. 따라서 공용 인터넷 이나 Express 경로 (Microsoft 피어 링 또는 기존 공용 피어 링)를 통해서만 복제할 수 있습니다.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>복제에 Riverbed SteelHeads를 사용할 수 있나요?

파트너는 Azure Site Recovery 작업에 대 한 자세한 지침을 제공 합니다. [솔루션 가이드](https://community.riverbed.com/s/article/DOC-4627)를 검토 합니다.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>ExpressRoute를 사용하여 가상 머신을 Azure로 복제할 수 있습니까?
예, [ExpressRoute를 사용](concepts-expressroute-with-site-recovery.md)하여 온-프레미스 가상 머신을 Azure로 복제할 수 있습니다.

- Azure Site Recovery는 공용 엔드포인트을 통해 Azure Storage 데이터를 복제 합니다. Site Recovery 복제를 위한 Express 경로를 사용 하려면 [Microsoft 피어 링](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) 을 설정 하거나 기존 [공용 피어 링](../expressroute/about-public-peering.md) (새 회로에 사용 되지 않음)을 사용 해야 합니다.
- Microsoft 피어링은 복제에 권장되는 라우팅 도메인입니다.
- 프라이빗 피어링에는 복제가 지원되지 않습니다.
- VMware 컴퓨터 또는 물리적 컴퓨터를 보호 하는 경우에는 구성 서버에 대 한 [네트워킹 요구 사항도](vmware-azure-configuration-server-requirements.md#network-requirements) 충족 해야 합니다. Site Recovery 복제 오케스트레이션에 대해 구성 서버에서 특정 Url에 연결 해야 합니다. 이 연결에는 Express 경로를 사용할 수 없습니다.
- 가상 머신이 Azure Virtual Network에 장애 조치된 후 Azure Virtual Network로 [프라이빗 피어링](../expressroute/expressroute-circuit-peerings.md#privatepeering) 설정을 사용하여 액세스할 수 있습니다.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Azure에 복제 하는 경우 어떤 종류의 저장소 계정 또는 관리 디스크가 필요 한가요?

LRS 또는 GRS 저장소가 필요 합니다. 지역 정전이 발생하거나 주 지역을 복구할 수 없는 경우에 데이터를 복원할 수 있도록 GRS를 사용하는 것이 좋습니다. 계정은 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다. Premium Storage는 Azure Portal에서 Site Recovery를 배포할 때 VMware VM, Hyper-V VM 및 물리적 서버 복제에 지원됩니다. Managed disks는 LRS만 지원 합니다.

### <a name="how-often-can-i-replicate-data"></a>데이터를 얼마나 자주 복제할 수 있나요?
* **Hyper-v:** Hyper-v Vm은 30 초 마다 (premium storage 제외), 5 분 또는 15 분 마다 복제할 수 있습니다.
* **Azure vm, VMware vm, 물리적 서버:** 복제 빈도는 여기와 관련이 없습니다. 복제가 계속 됩니다.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>기존 복구 사이트에서 3차 사이트로 복제를 확장할 수 있습니까?
확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Azure에 처음으로 복제할 때 오프라인으로 복제할 수 있습니까?
지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-exclude-specific-disks-from-replication"></a>특정 디스크를 복제에서 제외할 수 있습니까?
이는 Azure Portal을 사용하여 Azure에 VMware VM 및 Hyper-V VM을 복제하는 경우에만 지원됩니다.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>동적 디스크를 사용하여 가상 머신을 복제할 수 있습니까?
동적 디스크는 Hyper-v 가상 컴퓨터를 복제 하는 경우와 VMware Vm 및 물리적 컴퓨터를 Azure에 복제할 때 지원 됩니다. 운영 체제 디스크는 기본 디스크여야 합니다.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>복제 트래픽에 할당 된 대역폭을 제한할 수 있나요?
예. 이러한 문서에서는 대역폭 제한에 대해 자세히 알아볼 수 있습니다.

* [VMware VM 및 물리적 서버를 복제하기 위한 용량 계획](site-recovery-plan-capacity-vmware.md)
* [Azure에 Hyper-V VM을 복제하기 위한 용량 계획](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>장애 조치(Failover)
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Azure로 장애 조치 (failover) 하는 경우 장애 조치 (failover) 후 Azure Vm에 액세스 하려면 어떻게 하나요?

보안 인터넷 연결 또는 사이트 간 VPN 또는 Azure ExpressRoute로 Azure VM에 액세스할 수 있습니다. 연결 하려면 여러 항목을 준비 해야 합니다. [자세히 알아보기](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Azure로 장애 조치(failover)하는 경우 Azure는 어떻게 데이터 복원을 보장합니까?
Azure는 복원을 위해 디자인되었습니다. Site Recovery는 Azure SLA에 따라 보조 Azure 데이터 센터에 대 한 장애 조치 (failover)를 위해 이미 엔지니어링 되었습니다. 이 상황이 발생하면 사용자가 자격 증명 모음에 대해 선택한 지리적으로 동일한 지역에 메타데이터 및 자격 증명 모음이 남아 있습니다.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>두 데이터 센터 간에 복제하는 동안 주 데이터 센터에서 예기치 않게 정전이 발생하면 어떻게 됩니까?
보조 사이트에서 계획되지 않은 장애 조치(failover)를 트리거할 수 있습니다. Site Recovery는 기본 사이트가 연결되지 않아도 장애 조치(failover)를 수행할 수 있습니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?
자동이 아닙니다. 포털에서 클릭 한 번으로 장애 조치(failover)를 시작하거나 [Site Recovery PowerShell](/powershell/module/az.recoveryservices) 을 사용하여 장애 조치(failover)를 트리거할 수 있습니다. 장애 복구(failback) 작업은 Site Recovery 포털에서 간단하게 수행할 수 있습니다.

온-프레미스 Orchestrator 또는 Operations Manager를 사용하여 가상 머신 오류를 감지하면 SDK를 사용하여 장애 조치(failover)를 트리거하도록 자동화할 수 있습니다.

* [자세한](site-recovery-create-recovery-plans.md) 복구 계획을 알아봅니다.
* [자세히 알아보기](site-recovery-failover.md) .
* [자세히 알아보기](site-recovery-failback-azure-to-vmware.md)

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>온-프레미스 호스트가 응답 하지 않거나 충돌 하는 경우 다른 호스트로 장애 복구 (failback) 할 수 있나요?
예, 대체 위치 복구를 사용하여 Azure에서 다른 호스트로 장애 복구(failback)를 수행할 수 있습니다.

* [VMware 가상 머신](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V 가상 머신](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>SDK와 함께 Site Recovery 시나리오를 자동화할 수 있습니까?
예. Rest API, PowerShell 또는 Azure SDK를 사용하여 Site Recovery 워크플로를 자동화할 수 있습니다. 현재 PowerShell을 사용하여 Site Recovery를 배포하기 위한 지원되는 시나리오:

* [VMM 클라우드의 Hyper-V VM을 Azure PowerShell Resource Manager로 복제](hyper-v-vmm-powershell-resource-manager.md)
* [Hyper-V VM을 VMM 없이 Azure PowerShell Resource Manager로 복제](hyper-v-azure-powershell-resource-manager.md)
* [PowerShell 리소스 관리자를 사용하여 VMware에서 Azure로 복제](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>구성 요소/공급자 업그레이드

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Site Recovery 업그레이드의 릴리스 정보/업데이트 롤업을 어디에서 찾을 수 있나요?

새 업데이트에 [대해 알아보고](site-recovery-whats-new.md) [롤업 정보를 가져옵니다](service-updates-how-to.md).

## <a name="next-steps"></a>다음 단계
* [Site Recovery 개요](site-recovery-overview.md)

