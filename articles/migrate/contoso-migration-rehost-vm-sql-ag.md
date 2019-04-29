---
title: Contoso 앱을 Azure VM 및 SQL Server AlwaysOn 가용성 그룹으로 마이그레이션하여 다시 호스트 | Microsoft Docs
description: Contoso가 온-프레미스 앱을 Azure VM 및 SQL Server AlwaysOn 가용성 그룹으로 마이그레이션하여 다시 호스트하는 방법을 알아보세요.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 535ba0049e91e09de3d1dcf05fc8ede80ef403ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60672109"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-azure-vms-and-sql-server-alwayson-availability-group"></a>Contoso 마이그레이션: 온-프레미스 앱을 Azure VM 및 SQL Server AlwaysOn 가용성 그룹에 다시 호스트

이 문서에서는 Contoso가 Azure에서 SmartHotel360 앱을 다시 호스트하는 방법을 보여줍니다. Contoso는 앱 프런트 엔드 VM을 Azure VM으로 마이그레이션하고 앱 데이터베이스를 SQL Server AlwaysOn 가용성 그룹을 사용하는 Windows Server 장애 조치(failover) 클러스터에서 실행되는 Azure SQL Server VM으로 마이그레이션합니다.

이 문서는 가상 회사 Contoso가 온-프레미스 리소스를 Microsoft Azure 클라우드로 마이그레이션하는 방법을 보여주는 문서 시리즈 중 하나입니다. 이 문서 시리즈에는 배경 정보와 마이그레이션 인프라 설정, 마이그레이션을 위한 온-프레미스 리소스 평가, 다양한 유형의 마이그레이션 실행을 설명하는 시나리오가 담겨 있습니다. 시나리오가 복잡해지고 있으며, 앞으로 계속해서 문서가 추가될 것입니다.

**문서** | **세부 정보** | **상태**
--- | --- | ---
[문서 1: 개요](contoso-migration-overview.md) | 문서 시리즈, Contoso의 마이그레이션 전략 및 시리즈에서 사용되는 샘플 앱에 대해 간략히 설명합니다. | 사용 가능
[문서 2: Azure 인프라 배포](contoso-migration-infrastructure.md) | Contoso에서 마이그레이션을 위해 온-프레미스 인프라와 Azure 인프라를 준비합니다. 이 시리즈의 모든 마이그레이션 관련 문서에서 동일한 인프라가 사용됩니다. | 사용 가능
[문서 3: Azure로 마이그레이션하기 위한 온-프레미스 리소스 평가](contoso-migration-assessment.md)  | Contoso가 VMware에서 실행되는 온-프레미스 SmartHotel360 앱의 평가를 실행합니다. Contoso에서 Azure Migrate 서비스를 사용하여 앱 VM을 평가하고, Database Migration Assistant를 사용하여 앱 SQL Server 데이터베이스를 평가합니다. | 사용 가능
[문서 4: Azure VM 및 SQL Database Managed Instance에서 앱 다시 호스트](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso가 온-프레미스 SmartHotel360 앱을 Azure로 리프트 앤 시프트 방식으로 마이그레이션합니다. Contoso에서 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하여 앱 프런트 엔드 VM을 마이그레이션하고, [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)를 사용하여 앱 데이터베이스를 Azure SQL Database Managed Instance로 마이그레이션합니다. | 사용 가능   
[문서 5: Azure VM에서 앱 다시 호스트](contoso-migration-rehost-vm.md) | Contoso에서 Site Recovery 서비스를 사용하여 SmartHotel360 앱 VM을 Azure VM으로 마이그레이션합니다. | 사용 가능
문서 6: Azure VM 및 SQL Server AlwaysOn 가용성 그룹에서 앱 다시 호스트 | Contoso가 SmartHotel360 앱을 마이그레이션합니다. Contoso에서 Site Recovery를 사용하여 앱 VM을 마이그레이션하고, Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹으로 보호되는 SQL Server 클러스터로 마이그레이션합니다. | 이 문서의 내용
[문서 7: Azure VM에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm.md) | Contoso에서 Azure Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 리프트 앤 시프트 방식으로 마이그레이션합니다. | 사용 가능
[문서 8: Azure VM 및 Azure MySQL 서버에 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm-mysql.md) | Contoso에서 Azure Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 마이그레이션하고, MySQL Workbench를 사용하여 앱 데이터베이스를 Azure MySQL 서버 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 9: Azure Web Apps 및 Azure SQL Database에서 앱 리팩터링](contoso-migration-refactor-web-app-sql.md) | Contoso에서 SmartHotel360 앱을 Azure Web App으로 마이그레이션하고, Database Migration Assistant를 사용하여 앱 데이터베이스를 Azure SQL Server 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 10: Azure Web Apps 및 Azure MySQL에서 Linux 앱 리팩터링](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso에서 지속적인 업데이트를 위해 GitHub와 통합된 Azure Traffic Manager를 사용하여 Linux osTicket 앱을 여러 Azure 지역의 Azure 웹앱으로 마이그레이션합니다. Contoso에서 앱 데이터베이스를 Azure Database for MySQL 인스턴스로 마이그레이션합니다. | 사용 가능 
[문서 11: Azure DevOps Services에서 TFS 리팩터링](contoso-migration-tfs-vsts.md) | Contoso에서 온-프레미스 Team Foundation Server 배포를 Azure의 Azure DevOps Services로 마이그레이션합니다. | 사용 가능
[문서 12: Azure 컨테이너 및 Azure SQL Database에서 앱 재설계](contoso-migration-rearchitect-container-sql.md) | Contoso에서 SmartHotel 앱을 Azure로 마이그레이션합니다. 그런 다음, 웹앱 계층을 Azure Service Fabric에서 실행되는 Windows 컨테이너로 재설계하고 Azure SQL Database를 사용하여 데이터베이스를 재설계합니다. | 사용 가능
[문서 13: Azure에서 앱 다시 빌드](contoso-migration-rebuild.md) | Contoso에서 다양한 Azure 기능과 서비스(Azure App Service, AKS(Azure Kubernetes Service), Azure Functions, Azure Cognitive Services 및 Azure Cosmos DB 포함)를 사용하여 SmartHotel 앱을 다시 빌드합니다. | 사용 가능
[문서 14: Azure로의 마이그레이션 확장](contoso-migration-scale.md) | 마이그레이션 조합을 시도한 후 Contoso는 Azure로 전체 마이그레이션을 확장할 준비를 합니다. | 사용 가능


이 문서에서 Contoso는 VMware VM에서 실행되는 2계층 Windows .NET SmartHotel360 앱을 Azure로 마이그레이션합니다. 이 앱을 사용하려는 경우 오픈 소스로 제공되므로 [GitHub](https://github.com/Microsoft/SmartHotel360)에서 다운로드할 수 있습니다.

## <a name="business-drivers"></a>비즈니스 영향 요소

IT 리더십 팀은 비즈니스 파트너와의 긴밀한 협력을 통해 다음과 같이 기업이 이러한 마이그레이션으로 달성하고자 하는 바를 잘 이해하고 있습니다.

- **비즈니스 성장 해결**: Contoso는 성장하고 있으며, 이에 따라 온-프레미스 시스템과 인프라에 부담을 주고 있습니다.
- **효율성 증대**: Contoso는 불필요한 절차를 제거하고 개발자와 사용자를 위한 프로세스를 간소화해야 합니다.  이 회사는 IT를 빠른 상태로 유지하고 시간이나 비용을 낭비하지 않도록 하여 고객 요구 사항을 더 빠르게 제공해야 합니다.
- **민첩성 향상**:  Contoso IT는 비즈니스 요구 사항에 대해 더 빠르게 대응해야 합니다. 글로벌 경제에서 성공할 수 있으려면 시장의 변화에 더 빠르게 대응할 수 있어야 합니다.  IT가 사업상의 걸림돌이 되지 않아야 합니다.
- **크기 조정**: 비즈니스가 성공적으로 성장함에 따라 Contoso IT에서 동일한 속도로 크기를 조정할 수 있는 시스템을 제공해야 합니다.

## <a name="migration-goals"></a>마이그레이션 목표

Contoso 클라우드 팀은 이 마이그레이션의 목표를 확실하게 정했습니다. 다음과 같은 목표는 최상의 마이그레이션 방법을 결정하는 데 사용되었습니다.

- 마이그레이션 후 Azure의 앱은 현재 VMware에서와 동일한 성능 기능이 있어야 합니다.  앱은 온-프레미스에서와 마찬가지로 클라우드에서도 매우 중요하게 유지됩니다.
- Contoso는 이 앱에 투자하려고 하지 않습니다.  비즈니스에 중요하기는 하지만 Contoso는 단지 현재 형태로 안전하게 클라우드로 이동하고 싶어 합니다.
- 앱의 온-프레미스 데이터베이스에 가용성 문제가 있습니다. Contoso는 장애 조치(failover) 기능이 있는 고가용성 클러스터로 Azure에 배포하기를 원합니다.
- Contoso는 현재의 SQL Server 2008 R2 플랫폼에서 SQL Server 2017로 업그레이드하려고 합니다.
- Contoso는 이 앱에 Azure SQL Database를 사용하지 않으려고 하며, 대안을 찾고 있습니다.


## <a name="solution-design"></a>솔루션 디자인

해당 목표 및 요구 사항을 고정한 후 Contoso는 배포 솔루션을 디자인 및 검토하고, 마이그레이션에 사용할 Azure 서비스를 포함한 마이그레이션 프로세스를 식별합니다.

### <a name="current-architecture"></a>현재 아키텍처

- 앱은 두 개의 VM(WEBVM 및 SQLVM)에 계층화되어 있습니다.
- VM은 VMware ESXi 호스트 **contosohost1.contoso.com**(버전 6.5)에 있습니다.
- VMware 환경은 VM에서 실행되는 vCenter Server 6.5(**vcenter.contoso.com**)에서 관리합니다.
- Contoso는 온-프레미스 데이터 센터(contoso-datacenter)와 온-프레미스 도메인 컨트롤러(**contosodc1**)를 갖고 있습니다.


### <a name="proposed-architecture"></a>제안된 아키텍처

이 시나리오에서는

- Contoso는 앱 프런트 엔드 WEBVM을 Azure IaaS VM으로 마이그레이션할 것입니다.
    - Azure의 프런트 엔드 VM은 ContosoRG 리소스 그룹(프로덕션 리소스에 사용됨)에 배포됩니다.
    -  기본 지역인 미국 동부 2 지역의 Azure 프로덕션 네트워크(VNET-PROD-EUS2)에 배치됩니다.
- 앱 데이터베이스는 Azure SQL Server VM으로 마이그레이션됩니다.
    - 기본 지역인 미국 동부 2 지역에 있는 Contoso의 Azure database 네트워크(PROD-DB-EUS2)에 배치됩니다.
    - 두 개의 노드가 있고 SQL Server Always On 가용성 그룹을 사용하는 Windows Server 장애 조치(failover) 클러스터에 배치됩니다.
    - Azure에서 클러스터에 있는 두 SQL Server VM 노드는 ContosoRG 리소스 그룹에 배포됩니다.
    - VM 노드는 기본 지역인 미국 동부 2 지역의 Azure 프로덕션 네트워크(VNET-PROD-EUS2)에 배치됩니다.
    - VM은 SQL Server 2017 Enterprise Edition이 포함된 Windows Server 2016을 실행합니다. Contoso는 이 운영 체제에 대한 라이선스가 없으므로 Azure EA 약정에 비용을 부과하여 라이선스를 제공하는 Azure Marketplace의 이미지를 사용합니다.
    - 고유 이름을 제외하고는 VM 둘 다 동일한 설정을 사용합니다.
- Contoso는 클러스터에서 트래픽을 수신 대기하고 적절한 클러스터 노드로 전달하는 내부 부하 분산 장치를 배포합니다.
    - 내부 부하 분산 장치는 ContosoNetworkingRG(네트워킹 리소스에 사용됨)에 배포됩니다.
- 마이그레이션이 완료되면 Contoso 데이터 센터의 온-프레미스 VM은 서비스 해제됩니다.

![시나리오 아키텍처](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="database-considerations"></a>데이터베이스 고려 사항

Contoso는 솔루션 디자인 프로세스의 일부로 Azure SQL Database와 SQL Server의 기능을 비교했습니다. 다음 고려 사항은 SQL Server를 실행하는 Azure IaaS VM으로 결정하는 데 도움이 되었습니다.

 - Contoso가 운영 체제 또는 데이터베이스 서버를 사용자 지정해야 하거나 타사 앱을 동일한 VM에 함께 배치하고 실행하기를 원하는 경우 SQL Server를 실행하는 Azure VM을 사용하는 것이 가장 적절한 솔루션으로 보입니다.
 - Contoso는 Data Migration Assistant를 사용하여 간단하게 평가하고 Azure SQL Database로 마이그레이션할 수 있습니다.
 

### <a name="solution-review"></a>솔루션 검토

Contoso는 장점 및 단점 목록을 함께 조합하여 제안된 디자인을 평가합니다.

**고려 사항** | **세부 정보**
--- | ---
**장점** | WEBVM이 변경 없이 Azure로 이동하여 마이그레이션을 간소화합니다.<br/><br/> SQL Server 계층은 SQL Server 2017 및 Windows Server 2016에서 실행됩니다. 이것으로 현재 Windows Server 2008 R2 운영 체제는 사용 중지되고, 실행 중인 SQL Server 2017은 Contoso의 기술 요구 사항 및 목표를 지원합니다. IT는 SQL Server 2008 R2에서 전환하는 동안 100% 호환성을 제공합니다.<br/><br/> Contoso는 Azure 하이브리드 혜택을 사용하여 Software Assurance에 대한 투자를 활용할 수 있습니다.<br/><br/> Azure에 고가용성 SQL Server를 배포하면 앱 데이터 계층이 더 이상 단일 장애 조치(failover) 지점이 아니게 되므로 내결함성이 향상됩니다.
**단점** | WEBVM이 Windows Server 2008 R2를 실행합니다. 운영 체제는 특정 역할을 위한 Azure에서 지원됩니다(2018년 7월). [자세히 알아보기](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).<br/><br/> 앱의 웹 계층은 단일 장애 조치(failover) 지점을 유지합니다.</br><br/> Contoso는 Azure App Service와 같이 관리되는 서비스로 이동하는 것이 아니라 웹 계층을 Azure VM으로 계속 지원해야 합니다.<br/><br/> 선택한 솔루션을 사용하면 Contoso에서 Azure SQL Database Managed Instance와 같은 관리되는 플랫폼으로 전환하는 것이 아니라 두 SQL Server VM을 계속 관리해야 합니다. 또한 Software Assurance를 사용하면 Contoso에서 기존 라이선스를 Azure SQL Database Managed Instance의 할인된 가격으로 교환할 수 있습니다.


### <a name="azure-services"></a>Azure 서비스

**서비스** | **설명** | **비용**
--- | --- | ---
[Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA는 온-프레미스 SQL Server 머신에서 로컬로 실행되며, 사이트 간 VPN의 데이터베이스를 Azure로 마이그레이션합니다. | DMA는 무료로 다운로드할 수 있는 도구입니다.
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Site Recovery는 Azure VM, 온-프레미스 VM 및 물리적 서버에 대한 마이그레이션 및 재해 복구를 오케스트레이션하고 관리합니다.  | Azure로 복제하는 동안 Azure Storage 비용이 청구됩니다.  장애 조치(failover) 발생 시 Azure VM이 생성되고, 요금이 발생합니다. 요금 및 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/).

 

## <a name="migration-process"></a>마이그레이션 프로세스

Contoso 관리자는 앱 VM을 Azure로 마이그레이션합니다.

- 다음과 같이 Site Recovery를 사용하여 프런트 엔드 VM을 Azure VM으로 마이그레이션합니다.
    - 첫 번째 단계로 Azure 구성 요소를 준비하고 설정하며 온-프레미스 VMware 인프라를 준비합니다.
    - 모두 준비되면 VM 복제를 시작할 수 있습니다.
    - VM이 복제되어 작동하면 Azure로 장애 조치(failover)하여 VM을 마이그레이션합니다.
- DMA(Data Migration Assistant)를 사용하여 데이터베이스를 Azure의 SQL Server 클러스터로 마이그레이션합니다.
    - 첫 번째 단계로 Azure에서 SQL Server VM을 프로비전하고, 클러스터 및 내부 부하 분산 장치를 설정하고, AlwaysOn 가용성 그룹을 구성해야 합니다.
    - 이러한 준비가 완료되면 데이터베이스를 마이그레이션할 수 있습니다.
- 마이그레이션 후에는 해당 데이터베이스에 대해 AlwaysOn 보호를 사용하도록 설정합니다.

![마이그레이션 프로세스](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>필수 조건

이 시나리오를 위해 Contoso가 해야 할 일은 다음과 같습니다.

**요구 사항** | **세부 정보**
--- | ---
**Azure 구독** | Contoso는 이미 이 시리즈의 전반부 문서에서 구독을 만들었습니다. Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.<br/><br/> 체험 계정을 만들면 구독 관리자로서 모든 작업을 수행할 수 있습니다.<br/><br/> 기존 구독을 사용하고 관리자가 아닌 경우 관리자와 협력하여 소유자 또는 기여자 권한을 할당받아야 합니다.<br/><br/> 보다 세부적인 권한이 필요한 경우 [이 문서](../site-recovery/site-recovery-role-based-linked-access-control.md)를 검토합니다. 
**Azure 인프라** | Contoso가 Azure 인프라를 설정하는 [방법에 대해 알아보세요](contoso-migration-infrastructure.md).<br/><br/> Site Recovery 고유의 [네트워크](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) 및 [저장소](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) 요구 사항에 대해 자세히 알아보세요.
**사이트 복구(온-프레미스)** | 온-프레미스 vCenter 서버에서 5.5, 6.0 또는 6.5 버전을 실행해야 합니다.<br/><br/> 5.5, 6.0 또는 6.5 버전을 실행하는 ESXi 호스트<br/><br/> ESXi 호스트에서 실행되는 하나 이상의 VMware VM<br/><br/> VM은 [Azure 요구 사항](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)을 충족해야 합니다.<br/><br/> 지원되는 [네트워크](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) 및 [저장소](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) 구성입니다.<br/><br/> 복제하려는 VM은 [Azure 요구 사항](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)을 충족해야 합니다.



## <a name="scenario-steps"></a>시나리오 단계

Contoso가 마이그레이션을 실행하는 방법은 다음과 같습니다.

> [!div class="checklist"]
> * **1단계: 클러스터 준비**: Azure에서 두 SQL Server VM 노드를 배포할 클러스터를 만듭니다.
> * **2단계: 클러스터 배포 및 설정**: Azure SQL Server 클러스터를 준비합니다.  데이터베이스는 미리 만들어진 이 클러스터로 마이그레이션됩니다.
> * **3단계: 부하 분산 장치 배포**: SQL Server 노드 간에 트래픽을 분산할 부하 분산 장치를 배포합니다.
> * **4단계: Site Recovery를 위한 Azure 준비**: 복제된 데이터를 저장할 Azure Storage 계정 및 Recovery Services 자격 증명 모음을 만듭니다. 
> * **5단계: Site Recovery를 위한 온-프레미스 VMware 준비**: VM 검색 및 에이전트 설치를 위한 계정을 준비합니다. 마이그레이션 후에 사용자가 Azure VM에 연결할 수 있도록 온-프레미스 VM을 준비합니다.
> * **6단계: VM 복제**: VM을 Azure로 복제할 수 있도록 설정합니다.
> * **7단계: DMA 설치**: Database Migration Assistant를 다운로드하여 설치합니다.
> * **7단계: DMA를 사용하여 데이터베이스 마이그레이션**: 데이터베이스를 Azure로 마이그레이션합니다.
> * **9단계: 데이터베이스 보호**: 클러스터에 사용할 Always On 가용성 그룹을 만듭니다.
> * **10단계: 웹앱 VM 마이그레이션**: 모든 것이 예상대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다. 그런 후 Azure로 전체 장애 조치(failover)를 실행합니다. 


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>1단계: SQL Server AlwaysOn 가용성 그룹 클러스터 준비

Contoso 관리자는 다음과 같이 클러스터를 설정합니다.

1. Azure Marketplace에서 SQL Server 2017 Enterprise Windows Server 2016 이미지를 선택하여 두 개의 SQL Server VM을 만듭니다. 

    ![SQL VM SKU](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. **가상 머신 만들기 마법사** > **기본 사항**에서 다음을 구성합니다.

    - VM에 대한 이름: **SQLAOG1** 및 **SQLAOG2**.
    - 중요 비즈니스용 머신이므로 VM 디스크 유형에 대해 SSD를 사용하도록 설정합니다.
    - 머신 자격 증명을 지정합니다.
    - ContosoRG 리소스 그룹의 주 지역인 미국 동부 2에 VM을 배포합니다.

3. **크기**에서는 VM 둘 다 D2s_V3 SKU로 시작합니다. 필요에 따라 나중에 크기를 확장합니다.
4. **설정**에서 다음을 수행합니다.

    - 이러한 VM은 앱의 중요한 데이터베이스이므로 관리 디스크를 사용합니다.
    - 주 지역인 미국 동부 2 프로덕션 네트워크(**VNET-PROD-EUS2**)의 데이터베이스 서브넷(**PROD-DB-EUS2**)에 VM을 배치합니다.
    - 두 개의 장애 도메인과 다섯 개의 업데이트 도메인으로 새 가용성 세트인 **SQLAOGAVSET**를 만듭니다.

      ![SQL VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. **SQL Server 설정**에서는 기본 포트 1433의 가상 네트워크(개인)에 대한 SQL 연결을 제한합니다. 인증을 위해 온사이트에서 사용하는 것과 동일한 자격 증명(**contosoadmin**)을 사용합니다.

    ![SQL VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**도움이 더 필요하세요?**

- SQL Server VM 프로비저닝에 대한 [도움말을 보세요](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings).
- 다양한 SQL Server SKU의 VM 구성에 대해 [알아보세요](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms).

## <a name="step-2-deploy-and-set-up-the-cluster"></a>2단계: 클러스터 배포 및 설정

Contoso 관리자는 다음과 같이 클러스터를 설정합니다.

1. 클라우드 감시 역할을 할 Azure Storage 계정을 설정합니다.
2. Contoso 온-프레미스 데이터 센터의 Active Directory 도메인에 SQL Server VM을 추가합니다.
3. Azure에 클러스터를 만듭니다.
4. 클라우드 감시를 구성합니다.
5. 마지막으로 SQL Always On 가용성 그룹을 사용하도록 설정합니다.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>저장소 계정을 클라우드 감시로 설정

Contoso가 클라우드 감시를 설정하려면 클러스터 중재에 사용된 Blob 파일을 저장할 Azure Storage 계정이 있어야 합니다. 여러 클러스터에 동일한 저장소 계정을 사용하여 클라우드 감시를 설정할 수 있습니다. 

Contoso 관리자는 다음과 같이 저장소 계정을 만듭니다.

1. 계정(**contosocloudwitness**)에 대해 인식 가능한 이름을 지정합니다.
2. LRS를 사용하여 일반적인 다용도 계정을 배포합니다.
3. 세 번째 지역인 미국 중남부에 계정을 배치합니다. 지역 오류가 발생하는 경우에도 계속 사용할 수 있도록 주 지역 및 보조 지역이 아닌 지역에 배치합니다.
4. 인프라 리소스가 저장되는 리소스 그룹(**ContosoInfraRG**)에 배치합니다.

    ![클라우드 감시](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. 저장소 계정을 만들면 해당 계정에 대해 기본 및 보조 액세스 키가 생성됩니다. 클라우드 감시를 만들려면 기본 액세스 키가 있어야 합니다. 저장소 계정 이름 > **액세스 키** 아래에 키가 표시됩니다.

    ![액세스 키](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Contoso 도메인에 SQL Server VM 추가

1. Contoso는 contoso.com 도메인에 SQLAOG1 및 SQLAOG2를 추가합니다.
2. 그런 다음, 각 VM에 Windows 장애 조치(failover) 클러스터 기능 및 도구를 설치합니다.

### <a name="set-up-the-cluster"></a>클러스터 설정

Contoso 관리자는 클러스터를 설정하기 전에 각 머신의 OS 디스크 스냅숏을 작성합니다.

![스냅숏](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

1. 그런 다음, 준비한 스크립트를 실행하여 Windows 장애 조치(failover) 클러스터를 만듭니다.

    ![클러스터 만들기](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

2. 클러스터를 만든 후에는 VM이 클러스터 노드로 표시되는지 확인합니다.

     ![클러스터 만들기](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>클라우드 감시 구성

1. Contoso 관리자는 장애 조치(Failover) 클러스터 관리자에서 **쿼럼 구성 마법사**를 사용하여 클라우드 감시를 구성합니다.
2. 마법사에서 저장소 계정을 사용하여 클라우드 감시를 만들도록 선택합니다.
3. 클라우드 감시가 구성되면 장애 조치(Failover) 클러스터 관리자 스냅인에 표시됩니다.

    ![클라우드 감시](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

### <a name="enable-sql-server-always-on-availability-groups"></a>SQL Server Always On 가용성 그룹 사용

Contoso 관리자는 이제 다음과 같이 Always On을 사용하도록 설정할 수 있습니다.

1. SQL Server 구성 관리자에서 **SQL Server(MSSQLSERVER)** 서비스에 대해 **AlwaysOn 가용성 그룹**을 사용하도록 설정합니다.

    ![AlwaysOn 사용](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. 변경 내용이 적용되도록 서비스를 다시 시작합니다.

AlwaysOn을 사용할 수 있게 되면 Contoso가 SmartHotel360 데이터베이스를 보호할 AlwaysOn 가용성 그룹을 설정할 수 있습니다.

**도움이 더 필요하세요?**

- 클라우드 감시 및 클라우드 감시에 사용할 저장소 계정 설정에 대해 [읽어보세요](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).
- 클러스터 설정 및 가용성 그룹 만들기에 대한 [지침을 확인해보세요](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial).

## <a name="step-3-deploy-the-azure-load-balancer"></a>3단계: Azure Load Balancer 배포

Contoso 관리자는 이제 클러스터 노드 앞에 있는 내부 부하 분산 장치를 배포하려고 합니다. 부하 분산 장치는 트래픽을 수신 대기하고 적절한 노드로 전달합니다.

![부하 분산](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

다음과 같이 부하 분산 장치를 만듭니다.

1. Azure Portal > **네트워킹** > **부하 분산 장치**에서 새 내부 부하 분산 장치 **ILB-PROD-DB-EUS2-SQLAOG**를 설정합니다.
2. 프로덕션 네트워크 **VNET-PROD-EUS2**의 데이터베이스 서브넷 **PROD-DB-EUS2**에 부하 분산 장치를 배치합니다.
3. 정적 IP 주소 10.245.40.100을 할당합니다.
4. 이 부하 분산 장치를 네트워킹 리소스 그룹 **ContosoNetworkingRG**에 네트워킹 요소로 배포합니다.

    ![부하 분산](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

내부 부하 분산 장치가 배포되면 부하 분산 장치를 설정해야 합니다. 백 엔드 주소 풀을 만들고, 상태 프로브를 설정하고, 부하 분산 규칙을 구성합니다.

### <a name="add-a-backend-pool"></a>백 엔드 풀 추가

클러스터의 VM으로 트래픽을 분산하기 위해 Contoso 관리자는 부하 분산 장치의 네트워크 트래픽을 수신할 VM의 NIC IP 주소가 포함된 백 엔드 주소 풀을 설정합니다.

1. 포털의 부하 분산 장치 설정에서 Contoso는 백 엔드 풀 **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**을 추가합니다.
2. 이 풀을 가용성 집합 SQLAOGAVSET와 연결합니다. 이 집합의 VM(**SQLAOG1** 및 **SQLAOG2**)이 풀에 추가됩니다.

    ![백 엔드 풀](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>상태 프로브 만들기

Contoso 관리자는 부하 분산 장치가 앱 상태를 모니터링할 수 있도록 상태 프로브를 만듭니다. 프로브는 상태 검사에 대응하는 방식에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다.

다음과 같이 프로브를 만듭니다. 

1. 포털의 부하 분산 장치 설정에서 Contoso는 상태 프로브 **SQLAlwaysOnEndPointProbe**를 만듭니다.
2. TCP 포트 59999에서 VM을 모니터링하도록 프로브를 설정합니다.
3. 프로브 사이의 간격을 5초로 설정하고 임계값을 2로 설정합니다. 두 개의 프로브가 실패하면 VM이 비정상 상태로 간주됩니다.

    ![프로브](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>트래픽을 수신할 부하 분산 장치 구성


이제 Contoso 관리자는 VM으로 트래픽이 분산되는 방법을 정의하는 부하 분산 장치 규칙이 있어야 합니다.

- 프런트 엔드 IP 주소는 들어오는 트래픽을 처리합니다.
- 백 엔드 IP 풀은 트래픽을 수신합니다.

다음과 같이 규칙을 만듭니다.

1. 포털의 부하 분산 장치 설정에서 새 부하 분산 규칙 **SQLAlwaysOnEndPointListener**를 추가합니다.
2. Contoso는 TCP 1433에서 들어오는 SQL 클라이언트 트래픽을 수신하도록 프런트 엔드 수신기를 설정합니다.
3. 트래픽이 경로 지정될 백 엔드 풀과 VM이 트래픽을 수신 대기할 포트를 지정합니다.
4. 부동 IP(Direct Server Return)를 사용하도록 설정합니다. SQL AlwaysOn에 항상 필요합니다.

    ![프로브](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**도움이 더 필요하세요?**

- Azure Load Balancer에 대한 [개요를 보세요](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).
- 부하 분산 장치 만들기에 대해 [알아보세요](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal).



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>4단계: Site Recovery 서비스를 위한 Azure 준비

Contoso가 Site Recovery를 배포하는 데 필요한 Azure 구성 요소는 다음과 같습니다.

- 장애 조치(failover) 중 VM이 만들어지면 배치될 VNet.
- 복제된 데이터를 저장할 Azure Storage 계정. 
- Azure의 Recovery Services 자격 증명 모음

Contoso 관리자는 다음과 같이 설정합니다.

1.  Contoso는 [Azure 인프라를 배포](contoso-migration-rehost-vm-sql-ag.md)할 때 Site Recovery에 사용할 수 있는 네트워크/서브넷을 이미 만들었습니다.

    - SmartHotel360 앱은 프로덕션 앱이며 WEBVM은 주 지역인 미국 동부 2의 Azure 프로덕션 네트워크(VNET-PROD-EUS2)로 마이그레이션됩니다.
    - WEBVM은 프로덕션 서브넷(PROD-FE-EUS2)에 있으며 프로덕션 리소스에 사용되는 ContosoRG 리소스 그룹에 배치됩니다.

2. Contoso 관리자는 주 지역에 Azure Storage 계정(contosovmsacc20180528)을 만듭니다.

    - 범용 계정과 표준 저장소 및 LRS 복제를 사용합니다.
    - 계정은 자격 증명 모음과 동일한 지역에 있어야 합니다.

      ![Site Recovery 저장소](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. 네트워크 및 저장소 계정이 준비되면 이제 Recovery Services 자격 증명 모음(**ContosoMigrationVault**)을 만들어 주 지역인 미국 동부 2의 **ContosoFailoverRG** 리소스 그룹에 배치합니다.

    ![Recovery Services 자격 증명 모음](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**도움이 더 필요하세요?**

Azure에서 Site Recovery를 설정하는 방법에 대해 [알아보세요](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-5-prepare-on-premises-vmware-for-site-recovery"></a>5단계: Site Recovery를 위한 온-프레미스 VMware 준비

Contoso 관리자가 준비하는 온-프레미스 항목은 다음과 같습니다.

- VM 검색을 자동화하기 위한 vCenter 서버 또는 vSphere ESXi 호스트의 계정.
- 복제하려는 VMware VM에서 모바일 서비스 자동 설치를 허용하는 계정.
- Contoso가 장애 조치(failover) 후 복제된 Azure VM에 연결할 수 있도록 하기 위한 온-프레미스 VM 설정.


### <a name="prepare-an-account-for-automatic-discovery"></a>자동 검색용 계정 준비

Site Recovery에서는 다음 작업을 위해 VMware 서버에 액세스해야 합니다.

- VM을 자동으로 검색합니다. 
- 복제, 장애 조치 및 장애 복구를 오케스트레이션합니다.
- 최소한 읽기 전용 계정이 필요합니다. 디스크 만들기 및 제거, VM 전원 켜기와 같은 작업을 실행할 수 있는 계정이 필요합니다.

Contoso 관리자는 다음과 같이 계정을 설정합니다.

1. vCenter 수준에서 역할을 만듭니다.
2. 그런 다음, 해당 역할에 필요한 권한을 할당합니다.



### <a name="prepare-an-account-for-mobility-service-installation"></a>모바일 서비스 설치를 위한 계정 준비

모바일 서비스는 각 VM에 설치되어야 합니다.

- VM에 대해 복제를 사용하도록 설정한 경우, Site Recovery가 이 구성 요소의 자동 강제 설치를 수행할 수 있습니다.
- 강제 설치를 사용하려는 경우 Site Recovery가 VM에 액세스하는 데 사용할 수 있는 계정이 있어야 합니다. Azure 콘솔에서 복제를 설정할 때 이 계정을 지정합니다.
- 계정은 VM에 설치할 수 있는 권한이 있는 도메인 또는 로컬 계정일 수 있습니다.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>장애 조치(Failover) 후 Azure VM에 연결할 준비

장애 조치(failover) 후 Contoso는 Azure VM에 연결할 수 있기를 원합니다. 이를 위해 Contoso 관리자는 마이그레이션 전에 다음을 수행합니다.

1. 인터넷을 통해 액세스하려면 다음을 수행합니다.

   - 장애 조치(failover) 전 온-프레미스 VM에서 RDP를 사용하도록 설정합니다.
   - **공개** 프로필에 대해 TCP 및 UDP 규칙이 추가되었는지 확인합니다.
   - **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 RDP가 허용되는지 확인합니다.
 
2. 사이트 간 VPN을 통해 액세스하려면 다음을 수행합니다.

   - 온-프레미스 머신에서 RDP를 사용하도록 설정합니다.
   - **Windows 방화벽** -> **허용되는 앱 및 기능**에서 **도메인 및 개인** 네트워크에 대해 RDP를 허용합니다.
   - 온-프레미스 VM의 운영 체제 SAN 정책을 **OnlineAll**로 설정합니다.

또한 장애 조치(failover)를 실행하는 경우, 다음을 확인해야 합니다.

- 장애 조치를 트리거할 때 VM에 보류 중인 Windows 업데이트가 없어야 합니다. 있는 경우 업데이트가 완료될 때까지 사용자가 VM에 로그인할 수 없습니다.
- 장애 조치(failover) 후 **부트 진단**을 확인하여 VM에 대한 스크린샷을 볼 수 있습니다. 볼 수 없는 경우 VM이 실행 중인지 확인하고 해당 [문제 해결 팁](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)을 검토합니다.


**도움이 더 필요하세요?**

- 자동 검색을 위한 역할 만들기와 할당에 대해 [알아보세요](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).
- 모바일 서비스 강제 설치를 위한 계정을 만드는 방법에 대해 [알아보세요](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).


## <a name="step-6-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>6단계: Site Recovery를 사용하여 온-프레미스 VM을 Azure로 복제

Contoso 관리자는 Azure로 마이그레이션을 실행하려면 먼저 복제를 설정하고 사용하도록 설정해야 합니다.

### <a name="set-a-replication-goal"></a>복제 목표 설정

1. 자격 증명 모음의 자격 증명 모음 이름(ContosoVMVault) 아래에서 복제 목표를 선택합니다(**시작** > **Site Recovery** > **인프라 준비**).
2. 해당 머신이 온-프레미스에 있고 VMware에서 실행되며 Azure로 복제하도록 지정합니다.

    ![복제 목표](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>배포 계획 확인

계속하려면 **예, 완료함**을 선택하여 배포 계획을 완료했는지 확인해야 합니다. 이 시나리오에서는 Contoso가 한 개의 VM만 마이그레이션하므로 배포 계획이 필요하지 않습니다.

### <a name="set-up-the-source-environment"></a>원본 환경 설정

Contoso 관리자는 원본 환경을 구성해야 합니다. 이 작업을 위해 OVF 템플릿을 다운로드하여 Site Recovery 구성 서버를 고가용성 온-프레미스 VMware VM으로 배포하는 데 사용합니다. 구성 서버가 작동하여 실행되면 자격 증명 모음에 등록합니다.

구성 서버는 다음과 같이 여러 구성 요소를 실행합니다.

- 구성 서버 구성 요소 - 온-프레미스와 Azure 간의 통신을 조정하고 데이터 복제를 관리합니다.
- 프로세스 서버 - 복제 게이트웨이의 역할을 합니다. 복제 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화하며 복제 데이터를 Azure 저장소로 전송합니다.
- 또한 프로세스 서버는 복제하려는 VM에 모바일 서비스를 설치하고 온-프레미스 VMware VM의 자동 검색을 수행합니다.

Contoso 관리자는 다음 단계를 수행합니다.


1. 자격 증명 모음의 **인프라 준비** > **원본** > **구성 서버**에서 OVF 템플릿을 다운로드합니다.
    
    ![OVF 다운로드](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. 템플릿을 VMware로 가져와서 VM을 만들고 배포합니다.

    ![OVF 템플릿](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. VM을 처음 작동하면 Windows Server 2016 설치 환경으로 부팅됩니다. 사용권 계약에 동의하고 관리자 암호를 입력합니다.
4. 설치가 완료되면 VM에 관리자 권한으로 로그인합니다. 처음 로그인하면 Azure Site Recovery 구성 도구가 기본적으로 실행됩니다.
5. 도구에서 자격 증명 모음에 구성 서버를 등록하는 데 사용할 이름을 지정합니다.
6. VM이 Azure에 연결할 수 있는지 도구에서 확인합니다. 연결이 설정되면 Azure 구독에 로그인합니다. 구성 서버를 등록하려는 자격 증명 모음에 자격 증명이 액세스할 수 있어야 합니다.

    ![구성 서버 등록](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. 도구에서 몇 가지 구성 작업을 수행한 후 다시 부팅합니다.
8. 머신에 다시 로그인하면 구성 서버 관리 마법사가 자동으로 시작됩니다.
9. 마법사에서 복제 트래픽을 받을 NIC를 선택합니다. 이 설정은 구성된 후에 변경할 수 없습니다.
10. 구성 서버를 등록할 구독, 리소스 그룹 및 자격 증명 모음을 선택합니다.
        ![자격 증명 모음](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. MySQL 서버 및 VMWare PowerCLI를 다운로드하여 설치합니다. 
11. 유효성 검사 이후 vCenter 서버나 vSphere 호스트의 FQDN 또는 IP 주소를 지정합니다. 기본 포트는 그대로 두고 vCenter 서버의 이름을 지정합니다.
12. 자동 검색을 위해 만든 계정과 모바일 서비스를 자동으로 설치하는 데 사용되는 자격 증명을 지정합니다. Windows 컴퓨터의 경우 VM에 대한 로컬 관리자 권한이 계정에 필요합니다.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. 등록이 완료되면 Azure Portal에서 구성 서버 및 VMware 서버가 자격 증명 모음의 **원본** 페이지에 표시되는지 확인합니다. 검색하는 데 15분 이상 걸릴 수 있습니다. 
8. 그런 다음, Site Recovery가 지정한 설정을 사용하여 VMware 서버에 연결하고 VM을 검색합니다.

### <a name="set-up-the-target"></a>대상 설정

이제 Contoso 관리자는 대상 복제 설정을 지정합니다.

1. **인프라 준비** > **대상**에서 대상 설정을 선택합니다.
2. Site Recovery에서 지정된 대상에 Azure 저장소 계정과 네트워크가 있는지 확인합니다.

### <a name="create-a-replication-policy"></a>복제 정책 만들기

이제 Contoso 관리자가 복제 정책을 만들 수 있습니다.

1. **인프라 준비** > **복제 설정** > **복제 정책** >  **만들기 및 연결**에서 **ContosoMigrationPolicy** 정책을 만듭니다.
2. 다음 기본 설정을 사용합니다.
    - **RPO 임계값**: 기본값은 60분입니다. 이 값은 복구 지점을 만드는 빈도를 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
    - **복구 지점 보존**: 기본값은 24시간입니다. 이 값은 각 복구 지점에 대한 보존 기간을 지정합니다. 복제된 VM은 하나의 시간대에서 임의의 시점으로 복구할 수 있습니다.
    - **앱 일치 스냅숏 빈도**: 기본값은 1시간입니다. 이 값은 애플리케이션 일치 스냅숏이 만들어지는 빈도를 지정합니다.
 
        ![복제 정책 만들기](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. 정책은 구성 서버와 자동으로 연결됩니다. 

    ![복제 정책 연결](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>복제 사용

이제 Contoso 관리자는 WebVM 복제를 시작할 수 있습니다.

1. **애플리케이션 복제** > **원본** > **+복제**에서 원본 설정을 선택합니다.
2. VM 사용을 표시하고 vCenter 서버 및 구성 서버를 선택합니다.

    ![복제 사용](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. 이제 VNet 및 리소스 그룹과 복제된 데이터가 저장되는 저장소 계정을 비롯한 대상 설정을 지정합니다.

     ![복제 사용](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Contoso는 복제를 위한 WebVM을 선택하고, 복제 정책을 확인하고, 복제를 사용하도록 설정합니다. 복제를 사용하도록 설정하면 Site Recovery가 VM에 모바일 서비스를 설치합니다.
 
    ![복제 사용](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. **작업**에서 복제 진행 상황을 추적합니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.
5. Contoso는 Azure Portal의 **기본 정보**에서 Azure로 복제되는 VM의 구조를 확인할 수 있습니다.

    ![인프라 보기](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**도움이 더 필요하세요?**

- [온-프레미스 VMware VM 재해 복구 설정](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)에서 이러한 모든 단계에 대한 전체 연습을 확인할 수 있습니다.
- [원본 환경 설정](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [구성 서버 배포](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), [복제 설정 구성](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)에 도움이 되는 자세한 지침이 제공됩니다.
- [복제를 사용하도록 설정](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)하는 방법에 대해 자세히 알아볼 수 있습니다.


## <a name="step-7-install-the-database-migration-assistant-dma"></a>7단계: DMA(Database Migration Assistant) 설치

Contoso 관리자는 DMA를 사용하여 SmartHotel360 데이터베이스를 Azure VM **SQLAOG1**로 마이그레이션합니다. DMA는 다음과 같이 설정합니다.

1. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53595)에서 온-프레미스 SQL Server VM(**SQLVM**)으로 도구를 다운로드합니다.
2. VM에서 설치(DownloadMigrationAssistant.msi)를 실행합니다.
3. **마침** 페이지에서 마법사를 마치기 전에 **Microsoft Data Migration Assistant 시작**을 선택합니다.

## <a name="step-8-migrate-the-database-with-dma"></a>8단계: DMA를 사용하여 데이터베이스 마이그레이션

1. DMA에서 새 마이그레이션, **SmartHotel**을 실행합니다.
2. **대상 서버 유형**을 **Azure Virtual Machines의 SQL Server**로 선택합니다. 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. 마이그레이션 세부 정보에서 **SQLVM**을 소스 서버로 추가하고 **SQLAOG1**을 대상으로 추가합니다. 각 머신의 자격 증명을 지정합니다.

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. 데이터베이스 및 구성 정보에 대한 로컬 공유를 만듭니다. SQLVM 및 SQLAOG1에서 SQL 서비스 계정의 쓰기 액세스를 통해 액세스할 수 있어야 합니다.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso는 마이그레이션해야 하는 로그인을 선택하고 마이그레이션을 시작합니다. 완료되면 DMA가 마이그레이션을 성공으로 표시합니다.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. **SQLAOG1**에서 데이터베이스가 실행 중인지 확인합니다.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

DMS는 Contoso 데이터 센터와 Azure 사이의 사이트 간 VPN 연결을 통해 온-프레미스 SQL Server VM에 연결한 다음, 데이터베이스를 마이그레이션합니다.

## <a name="step-7-protect-the-database-with-alwayson"></a>7단계: AlwaysOn으로 데이터베이스 보호

앱 데이터베이스가 **SQLAOG1**에서 실행되는 경우 Contoso 관리자는 AlwaysOn 가용성 그룹을 사용하여 보호할 수 있습니다. SQL Management Studio를 사용하여 AlwaysOn을 구성한 다음, Windows 클러스터링을 사용하여 수신기를 할당합니다. 

### <a name="create-an-alwayson-availability-group"></a>AlwaysOn 가용성 그룹 만들기

1. SQL Management Studio에서 **Always On 고가용성**을 마우스 오른쪽 단추로 클릭하여 **새 가용성 그룹 마법사**를 시작합니다.
2. **옵션 지정**에서 가용성 그룹 이름을 **SHAOG**로 지정합니다. **데이터베이스 선택**에서 SmartHotel360 데이터베이스를 선택합니다.

    ![AlwaysOn 가용성 그룹](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. **복제본 지정**에서 두 개의 SQL 노드를 가용성 복제본으로 추가하고 동기 커밋을 사용하여 자동 장애 조치(failover)를 제공하도록 구성합니다.

     ![AlwaysOn 가용성 그룹](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. 그룹(**SHAOG**) 및 포트에 대한 수신기를 구성합니다. 내부 부하 분산 장치의 IP 주소가 정적 IP 주소(10.245.40.100)로 추가됩니다.

    ![AlwaysOn 가용성 그룹](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. **데이터 동기화 선택**에서 자동 시딩을 사용하도록 설정합니다. 이 옵션을 사용하면 SQL Server가 자동으로 그룹의 모든 데이터베이스 보조 복제본을 만들기 때문에 Contoso가 수동으로 백업하고 복원할 필요가 없습니다. 유효성 검사 후 가용성 그룹이 만들어집니다.

    ![AlwaysOn 가용성 그룹](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso가 그룹을 만들 때 문제가 발생했습니다. Active Directory Windows 통합 보안을 사용하지 않으므로 Windows 장애 조치(failover) 클러스터 역할을 만들려면 SQL 로그인에 권한을 부여해야 합니다.

    ![AlwaysOn 가용성 그룹](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. 그룹이 만들어지면 Contoso가 SQL Management Studio에서 볼 수 있습니다.

### <a name="configure-a-listener-on-the-cluster"></a>클러스터의 수신기 구성

SQL 배포를 설정하는 마지막 단계로, Contoso 관리자는 내부 부하 분산 장치를 클러스터의 수신기로 구성하고 수신기를 온라인 상태로 설정합니다. 이 작업은 스크립트를 사용하여 수행합니다.

![클러스터 수신기](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>구성 확인

Contoso가 모든 설정을 완료하면 마이그레이션된 데이터베이스를 사용하는 기능 가용성 그룹이 Azure에 생성됩니다. 관리자는 SQL Management Studio에서 내부 부하 분산 장치에 연결하여 이를 확인합니다.

![ILB 연결](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**도움이 더 필요하세요?**
- [가용성 그룹](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) 및 [수신기](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener) 만들기에 대해 알아보세요.
- 수동으로 [부하 분산 장치 IP 주소를 사용하도록 클러스터를 설정](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address)합니다.
- SAS를 만들고 사용하는 방법에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>8단계: Site Recovery로 VM 마이그레이션

Contoso 관리자는 빠른 테스트 장애 조치를 실행한 다음, VM을 마이그레이션합니다.

### <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

테스트 장애 조치(failover)를 실행하면 마이그레이션 전에 모든 요소가 예상대로 작동하는지 확인하는 데 도움이 됩니다. 

1. 사용할 수 있는 최근 특정 시점(**가장 최근에 처리됨**)으로 테스트 장애 조치를 실행합니다.
2. 장애 조치를 트리거하기 전에 Site Recovery에서 원본 VM 종료를 시도하도록 **장애 조치를 시작하기 전에 머신 종료**를 선택합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. 
3. 테스트 장애 조치가 다음과 같이 실행됩니다. 

    - 필수 구성 요소 확인은 마이그레이션에 필요한 모든 조건이 충족되었는지 확인하기 위해 실행합니다.
    - 장애 조치(failover)는 데이터를 처리하며 이 데이터에서 Azure VM이 만들어질 수 있습니다. 최신 복구 지점을 선택하는 경우 해당 데이터에서 복구 지점이 만들어집니다.
    - 이전 단계에서 처리한 데이터를 사용하여 Azure VM이 만들어집니다.

3. 장애 조치가 완료되면 Azure Portal에 Azure VM 복제본이 표시됩니다. VM의 크기가 적당하고, VM이 올바른 네트워크에 연결되어 있으며 실행 중인지 확인합니다. 
4. 확인한 후 장애 조치(failover)를 정리하고 모든 관찰 내용을 기록 및 저장합니다. 

### <a name="run-a-failover"></a>장애 조치(Failover) 실행

1. Contoso 관리자는 테스트 장애 조치가 예상대로 작동했는지 확인한 후 마이그레이션을 위한 복구 계획을 만들고 WEBVM을 계획에 추가합니다.

     ![복구 계획](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. 계획에 대해 장애 조치(failover)를 실행합니다. 최근 복구 지점을 선택하고 Site Recovery에서 장애 조치를 트리거하기 전에 온-프레미스 VM을 종료하도록 지정합니다.

    ![장애 조치(failover)](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. 장애 조치(failover) 후 Azure Portal에 예상대로 Azure VM이 표시되는지 확인합니다.

    ![복구 계획](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Azure에서 VM이 확인되면 마이그레이션을 완료하여 마이그레이션 프로세스를 마치고, VM에 대한 복제를 중지하고, VM에 대한 Site Recovery 청구를 중지합니다.

    ![장애 조치(failover)](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>연결 문자열 업데이트

마이그레이션 프로세스의 최종 단계로, Contoso 관리자는 SHAOG 수신기에서 실행 중인 마이그레이션된 데이터베이스를 가리키도록 애플리케이션의 연결 문자열을 업데이트합니다. 현재 Azure에서 실행 중인 WEBVM에서는 이 구성이 변경됩니다.  이 구성은 ASP 애플리케이션의 web.config에 있습니다. 

1. C:\inetpub\SmartHotelWeb\web.config에서 파일을 찾습니다.  AOG FQDN(shaog.contoso.com)을 반영하도록 서버의 이름을 변경합니다.

    ![장애 조치(failover)](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. 파일이 업데이트되고 저장되면 WEBVM에서 IIS를 다시 시작합니다. 이 작업은 cmd 프롬프트에서 IISRESET/RESTART를 사용하여 수행합니다.
2. IIS를 다시 시작한 후에는 애플리케이션이 SQL MI에서 실행되는 데이터베이스를 사용합니다.


**도움이 더 필요하세요?**

- 테스트 장애 조치를 실행하는 방법에 대해 [알아보세요](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- 복구 계획을 만드는 방법에 대해 [알아보세요](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Azure로 장애 조치하는 방법에 대해 [알아보세요](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>마이그레이션 후 정리

마이그레이션 후 SmartHotel360 앱은 Azure VM에서 실행되고 SmartHotel360 데이터베이스는 Azure SQL 클러스터에 있습니다.

이제 Contoso는 다음 정리 단계를 완료해야 합니다.  

- vCenter 인벤토리에서 온-프레미스 VM을 제거합니다.
- 로컬 백업 작업에서 VM을 제거합니다.
- 내부 설명서를 업데이트하여 VM의 새 위치 및 IP 주소를 표시합니다.
- 해제된 VM과 상호 작용하는 모든 리소스를 검토하고 모든 관련 설정이나 설명서를 업데이트하여 새 구성을 반영합니다.
- 두 개의 새 VM(SQLAOG1 및 SQLAOG2)이 프로덕션 모니터링 시스템에 추가되어야 합니다.

## <a name="review-the-deployment"></a>배포 검토

Azure에서 마이그레이션된 리소스를 사용하여 Contoso는 새 인프라를 완전하게 운영하고 보호해야 합니다.

### <a name="security"></a>보안

Contoso 보안 팀은 Azure VM WEBVM, SQLAOG1 및 SQLAOG2를 검토하여 보안 문제를 확인합니다. 

- 그리고 액세스를 제어할 VM에 대한 NSG(네트워크 보안 그룹)를 검토합니다. NSG는 애플리케이션에 허용된 트래픽만 통과할 수 있도록 제한하는 데 사용됩니다.
- 팀에서는 Azure Disk Encryption 및 KeyVault를 사용하여 디스크의 데이터를 보안하는 것도 고려합니다.
- 팀에서는 TDE(투명한 데이터 암호화)를 평가하고 새 SQL AOG에서 실행되는 SmartHotel360 데이터베이스에서 사용하도록 설정해야 합니다. [자세히 알아보기](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017).

VM 보안 모범 사례에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms).


## <a name="bcdr"></a>BCDR

 BCDR(비즈니스 연속성 및 재해 복구)을 위해 Contoso는 다음 작업을 수행합니다.
- 데이터를 안전하게 유지: Contoso는 Azure Backup 서비스를 사용하여 WEBVM, SQLAOG1 및 SQLAOG2 VM의 데이터를 백업합니다. [자세한 정보].
  (https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  - Contoso는 Azure Storage를 사용하여 SQL Server를 BLOB 스토리지에 직접 백업하는 방법도 알아볼 것입니다. [자세히 알아보기](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-use-storage-sql-server-backup-restore).
  - 앱 가동 및 실행 유지: Contoso는 Site Recovery를 사용하여 Azure의 앱 VM을 보조 지역에 복제합니다. [자세히 알아보기](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).


### <a name="licensing-and-cost-optimization"></a>라이선스 및 비용 최적화

1. Contoso는 해당 WEBVM에 대한 기존 라이선스가 있으나 Azure 하이브리드 혜택을 활용하려고 합니다.  Contoso는 이 가격 책정을 활용하도록 기존 Azure VM을 변환할 것입니다.
2. Contoso는 Microsoft 자회사인 Cloudyn에서 허가된 Azure Cost Management를 사용하도록 설정합니다. Azure 및 기타 클라우드 리소스를 활용하고 관리하는 데 도움이 되는 다중 클라우드 비용 관리 솔루션입니다.  Azure Cost Management에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/cost-management/overview). 

## <a name="conclusion"></a>결론

이 문서에서 Contoso는 Site Recovery 서비스를 통해 앱 프런트 엔드 VM을 Azure로 마이그레이션하여 Azure에서 SmartHotel360 앱을 다시 호스팅했습니다. Contoso는 앱 데이터베이스를 Azure에 프로비전된 SQL Server 클러스터로 마이그레이션했고 SQL Server AlwaysOn 가용성 그룹에서 보호했습니다.

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 문서에서는 Contoso가 Linux에서 실행되며 MySQL 데이터베이스를 통해 배포된 서비스 데스크 osTicket 앱을 다시 호스트하는 방법을 보여 줍니다.


