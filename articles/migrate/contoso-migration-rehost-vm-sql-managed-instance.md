---
title: Azure VM 및 Azure SQL Database Managed Instance로 마이그레이션하여 Contoso 온-프레미스 앱 다시 호스트 | Microsoft Docs
description: Azure VM 및 Azure SQL Database Managed Instance에서 Contoso가 온-프레미스 앱을 다시 호스팅하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 832614c46f0269460245d081f20897b591e31fce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60671302"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Contoso 마이그레이션: Azure VM 및 SQL Database Managed Instance에서 온-프레미스 앱 다시 호스트

이 문서에서는 Contoso가 Azure Site Recovery 서비스를 사용하여 SmartHotel360 앱 프런트 엔드 VM을 Azure VM으로 마이그레이션합니다. 또한 Contoso에서 앱 데이터베이스를 Azure SQL Database Managed Instance로 마이그레이션합니다.

> [!NOTE]
> Azure SQL Database Managed Instance는 현재 미리 보기로 있습니다.

이 문서는 가상의 회사인 Contoso에서 온-프레미스 리소스를 Microsoft Azure 클라우드로 마이그레이션하는 방법을 설명하는 문서 시리즈 중 하나입니다. 이 시리즈에는 배경 정보 및 마이그레이션 인프라를 설정하고, 다양한 유형의 마이그레이션을 실행하는 방법을 보여 주는 일련의 시나리오가 포함되어 있습니다. 시나리오가 복잡해지고 있으며, 시간이 지남에 따라 관련 문서가 시리즈에 추가됩니다.


**문서** | **세부 정보** | **상태**
--- | --- | ---
[문서 1: 개요](contoso-migration-overview.md) | 시리즈에서 사용되는 Contoso의 마이그레이션 전략, 문서 시리즈 및 샘플 앱에 대해 간략히 설명합니다. | 사용 가능
[문서 2: Azure 인프라 배포](contoso-migration-infrastructure.md) | Contoso에서 마이그레이션을 위해 온-프레미스 인프라와 Azure 인프라를 준비합니다. 이 시리즈의 모든 마이그레이션 관련 문서에서 동일한 인프라가 사용됩니다. | 사용 가능
[문서 3: Azure로 마이그레이션하기 위한 온-프레미스 리소스 평가](contoso-migration-assessment.md) | Contoso가 VMware에서 실행되는 온-프레미스 2계층 SmartHotel 앱의 평가를 실행합니다. Contoso에서 [Azure Migrate](migrate-overview.md) 서비스를 사용하여 앱 VM을 평가하고, [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)를 사용하여 앱 SQL Server 데이터베이스를 평가합니다. | 사용 가능
문서 4: Azure VM 및 SQL Database Managed Instance에서 앱 다시 호스트 | Contoso가 온-프레미스 SmartHotel 앱을 Azure로 리프트 앤 시프트 방식으로 마이그레이션합니다. Contoso에서 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하여 앱 프런트 엔드 VM을 마이그레이션하고, [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)를 사용하여 앱 데이터베이스를 Azure SQL Database Managed Instance로 마이그레이션합니다. | 이 문서의 내용
[문서 5: Azure VM에서 앱 다시 호스트](contoso-migration-rehost-vm.md) | Contoso에서 Site Recovery 서비스를 사용하여 SmartHotel 앱 VM을 Azure VM으로 마이그레이션합니다. | 사용 가능
[문서 6: Azure VM 및 SQL Server AlwaysOn 가용성 그룹에서 앱 다시 호스트](contoso-migration-rehost-vm-sql-ag.md) | Contoso가 SmartHotel 앱을 마이그레이션합니다. Contoso에서 Site Recovery를 사용하여 앱 VM을 마이그레이션하고, Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹으로 보호되는 SQL Server 클러스터로 마이그레이션합니다. | 사용 가능
[문서 7: Azure VM에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm.md) | Contoso에서 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 리프트 앤 시프트 방식으로 마이그레이션합니다. | 사용 가능
[문서 8: Azure VM 및 Azure Database for MySQL에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm-mysql.md) | Contoso에서 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 마이그레이션하고, MySQL Workbench를 사용하여 앱 데이터베이스를 Azure Database for MySQL로 마이그레이션합니다. | 사용 가능
[문서 9: Azure 웹앱 및 Azure SQL Database에서 앱 리팩터링](contoso-migration-refactor-web-app-sql.md) | Contoso에서 SmartHotel 앱을 Azure 웹앱으로 마이그레이션하고, 앱 데이터베이스를 Azure SQL Server 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 10: Azure 웹앱 및 Azure Database for MySQL에서 Linux 앱 리팩터링](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso에서 Linux osTicket 앱을 여러 사이트에 있는 Azure 웹앱으로 마이그레이션합니다. 웹앱은 GitHub와 통합되어 지속적으로 업데이트됩니다. Contoso에서 앱 데이터베이스를 Azure Database for MySQL 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 11: Azure DevOps Services에서 Team Foundation Server 리팩터링](contoso-migration-tfs-vsts.md) | Contoso에서 온-프레미스 Team Foundation Server 배포를 Azure의 Azure DevOps Services로 마이그레이션합니다. | 사용 가능
[문서 12: Azure 컨테이너 및 Azure SQL Database에서 앱 재설계](contoso-migration-rearchitect-container-sql.md) | Contoso에서 SmartHotel 앱을 Azure로 마이그레이션한 다음, 앱을 재설계합니다. Contoso에서 앱웹 계층을 Windows 컨테이너로 재설계하고, Azure SQL Database를 사용하여 앱 데이터베이스를 재설계합니다. | 사용 가능
[문서 13: Azure에서 앱 다시 빌드](contoso-migration-rebuild.md) | Contoso에서 다양한 Azure 기능과 서비스(Azure App Service, Azure Kubernetes Service, Azure Functions, Azure Cognitive Services 및 Azure Cosmos DB 포함)를 사용하여 SmartHotel 앱을 다시 빌드합니다. | 사용 가능
[문서 14: Azure로의 마이그레이션 확장](contoso-migration-scale.md) | 마이그레이션 조합을 시도한 후 Contoso는 Azure로 전체 마이그레이션을 확장할 준비를 합니다. | 사용 가능




이 문서에서 사용되는 SmartHotel360 앱 샘플은 [GitHub](https://github.com/Microsoft/SmartHotel360)에서 다운로드할 수 있습니다.



## <a name="business-drivers"></a>비즈니스 영향 요소

Contoso의 IT 리더십 팀은 회사의 비즈니스 파트너와 긴밀히 협력하여 회사에서 이러한 마이그레이션을 통해 달성하려는 목표와 관련하여 다음과 같이 파악했습니다.

- **비즈니스 성장 해결**: Contoso는 성장하고 있습니다. 그 결과 회사의 온-프레미스 시스템과 인프라에 대한 압박이 커졌습니다.
- **효율성 증대**: Contoso는 불필요한 절차를 제거하고 개발자와 사용자를 위한 프로세스를 간소화해야 합니다. 고객의 요구 사항에 대해 서비스를 더 빠르게 제공할 수 있도록 회사에서 IT 속도를 높이고 시간이나 비용을 낭비하지 않도록 해야 합니다.
- **민첩성 향상**:  Contoso IT는 비즈니스 요구 사항에 대해 더 빠르게 대응해야 합니다. 글로벌 경제에서 성공하기 위해 회사에서 시장의 변화보다 더 빨리 대응할 수 있어야 합니다. Contoso의 IT는 사업상의 걸림돌이 되지 않아야 합니다.
- **크기 조정**: 회사의 비즈니스가 성공적으로 성장함에 따라 Contoso IT는 동일한 속도로 크기를 조정할 수 있는 시스템을 제공해야 합니다.

## <a name="migration-goals"></a>마이그레이션 목표

Contoso 클라우드 팀은 이 마이그레이션에 대한 목표를 확인했습니다. 회사에서 마이그레이션 목표를 사용하여 최선의 마이그레이션 방법을 결정합니다.

- 마이그레이션한 후에 Azure의 앱은 Contoso의 온-프레미스 VMWare 환경에서 현재 확보하고 있는 앱과 동일한 성능 기능을 갖추어야 합니다. 클라우드로 전환한다고 해서 앱 성능이 덜 중요한 것은 아닙니다.
- Contoso는 앱에 투자하려고 하지 않습니다. 이 앱은 비즈니스에 매우 중요하지만 Contoso는 단순히 현재 형태의 앱을 클라우드로 이동하려고만 합니다.
- 앱이 마이그레이션되면 데이터베이스 관리 작업을 최소화해야 합니다.
- Contoso는 이 앱에 Azure SQL Database를 사용하지 않으려고 하며, 대안을 찾고 있습니다.


## <a name="solution-design"></a>솔루션 디자인

해당 목표 및 요구 사항을 고정한 후 Contoso는 배포 솔루션을 디자인 및 검토하고, 마이그레이션에 사용할 Azure 서비스를 포함한 마이그레이션 프로세스를 식별합니다.

### <a name="current-architecture"></a>현재 아키텍처 

- Contoso에는 하나의 주 데이터 센터(**contoso-datacenter**)가 있습니다. 데이터 센터는 미국 동부의 뉴욕시에 위치하고 있습니다.
- Contoso는 미국 전역에 세 개의 추가 지점을 갖추고 있습니다.
- 주 데이터 센터는 파이버 메트로 이더넷 연결(500Mbps)을 통해 인터넷에 연결됩니다.
- 각 지점은 IPSec VPN 터널을 통한 비즈니스 수준 연결을 주 데이터 센터에 다시 사용하여 인터넷에 로컬로 연결됩니다. 이렇게 설정하면 Contoso의 전체 네트워크가 영구적으로 연결되고 인터넷 연결을 최적화할 수 있습니다.
- 주 데이터 센터는 VMware를 통해 완벽하게 가상화됩니다. Contoso에는 vCenter Server 6.5에서 관리되는 두 개의 ESXi 6.5 가상화 호스트가 있습니다.
- Contoso는 ID 관리를 위해 Active Directory를 사용합니다. Contoso는 내부 네트워크에서 DNS 서버를 사용합니다.
- Contoso에는 온-프레미스 도메인 컨트롤러(**contosodc1**)가 있습니다.
- 도메인 컨트롤러는 VMware VM에서 실행됩니다. 지점의 도메인 컨트롤러는 실제 서버에서 실행됩니다.
- SmartHotel360 앱은 VMware ESXi 호스트 버전 6.5(**contosohost1.contoso.com**)에 있는 두 개의 VM(**WEBVM** 및 **SQLVM**)에 걸쳐 계층화되어 있습니다. 
- VMware 환경은 VM에서 실행되는 vCenter Server 6.5(**vcenter.contoso.com**)를 통해 관리됩니다.

![현재 Contoso 아키텍처](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

### <a name="proposed-architecture"></a>제안된 아키텍처

이 시나리오에서 Contoso는 다음과 같이 2계층 온-프레미스 여행 앱을 마이그레이션하려고 합니다.

- 앱 데이터베이스(**SmartHotelDB**)를 Azure SQL Database Managed Instance로 마이그레이션합니다.
- 프런트 엔드 **WebVM**을 Azure VM으로 마이그레이션합니다.
- 마이그레이션이 완료되면 Contoso 데이터 센터의 온-프레미스 VM이 서비스 해제됩니다.

![시나리오 아키텍처](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="database-considerations"></a>데이터베이스 고려 사항

Contoso는 솔루션 디자인 프로세스의 일부로 Azure SQL Database와 SQL Server Managed Instance 간의 기능을 비교했습니다. Managed Instance를 사용하기로 결정하는 데 도움이 된 고려 사항은 다음과 같습니다.

- Managed Instance는 최신 온-프레미스 SQL Server 버전과 거의 100%의 호환성을 제공하는 것을 목표로 합니다. 온-프레미스 또는 IaaS VM에서 SQL Server를 실행하는 고객이 디자인을 최소한으로 변경하면서 앱을 완전히 관리되는 서비스로 마이그레이션하려는 경우 Microsoft는 Managed Instance를 권장합니다.
- Contoso는 많은 수의 앱을 온-프레미스에서 IaaS로 마이그레이션하려고 합니다. 이러한 앱 중 대부분이 ISV에서 제공됩니다. Contoso는 Managed Instance를 사용하면 지원되지 않을 수 있는 SQL Database를 사용하는 것이 아니라 이러한 앱에 대한 데이터베이스 호환성을 보장할 수 있음을 알게 되었습니다.
- Contoso는 완전히 자동화된 DMS(Data Migration Service)를 사용하여 Managed Instance로의 리프트 앤 시프트 마이그레이션을 간단히 수행할 수 있습니다. 이 서비스를 사용하면 Contoso에서 향후의 데이터베이스 마이그레이션에 이 서비스를 다시 사용할 수 있습니다.
- SQL Managed Instance는 SmartHotel360 앱의 중요한 핵심인 SQL Server 에이전트를 지원합니다. Contoso에는 이러한 호환성이 필요합니다. 그렇지 않으면 앱에서 요구하는 유지 관리 계획을 다시 설계해야 합니다.
- Software Assurance가 있는 경우 Contoso는 SQL Server에 대한 Azure 하이브리드 혜택을 사용하여 Azure SQL Database Managed Instance에서 기존 라이선스를 할인된 가격으로 교환할 수 있습니다. Contoso는 이를 통해 Managed Instance에서 최대 30%를 절약할 수 있습니다.
- Managed Instance는 가상 네트워크에 완전히 포함되므로 Contoso의 데이터에 대해 높은 수준의 격리 및 보안을 제공합니다. Contoso는 공용 인터넷과 격리된 환경을 유지하면서 공용 클라우드를 활용할 수 있습니다.
- Managed Instance는 항상 암호화, 동적 데이터 마스킹, 행 수준 보안 및 위협 탐지를 포함한 다양한 보안 기능을 지원합니다.


### <a name="solution-review"></a>솔루션 검토

Contoso는 장점 및 단점 목록을 함께 조합하여 제안된 디자인을 평가합니다.

**고려 사항** | **세부 정보**
--- | ---
**장점** |  WEBVM이 변경 없이 Azure로 이동하여 마이그레이션을 간소화합니다.<br/><br/> SQL Managed Instance는 Contoso의 기술 요구 사항과 목표를 지원합니다.<br/><br/> Managed Instance는 현재 배포와의 100% 호환성을 제공하는 한편 SQL Server 2008 R2로부터 벗어나게 합니다.<br/><br/>  Software Assurance에 대한 투자를 활용하고, SQL Server 및 Windows Server에 대한 Azure 하이브리드 혜택을 사용할 수 있습니다.<br/><br/> 향후의 추가적인 마이그레이션에 Database Migration Service를 다시 사용할 수 있습니다.<br/><br/> SQL Managed Instance에는 Contoso에서 구성할 필요가 없는 내결함성이 기본적으로 제공됩니다. 따라서 데이터 계층은 더 이상 단일 장애 조치(failover) 지점이 아닙니다.
**단점** | WEBVM에서 Windows Server 2008 R2를 실행합니다.  이 운영 체제는 Azure에서 지원하지만 더 이상 지원되지 않는 플랫폼입니다. [자세히 알아보기](https://support.microsoft.com/en-us/help/956893).<br/><br/> 웹 계층은 서비스를 제공하는 WEBVM만으로 장애 극복의 단일 지점을 유지합니다.<br/><br/> Contoso는 Azure App Service와 같이 관리되는 서비스로 이동하는 것이 아니라 앱 웹 계층을 VM으로 계속 지원해야 합니다.<br/><br/> 데이터 계층의 경우 Contoso에서 운영 체제 또는 데이터베이스 서버를 사용자 지정하거나 SQL Server와 함께 타사 앱을 실행하려고 하면 Managed Instance가 최상의 솔루션이 아닐 수도 있습니다. IaaS VM에서 SQL Server를 실행하면 이러한 유연성을 제공할 수 있습니다. 

### <a name="migration-process"></a>마이그레이션 프로세스

Contoso는 다음 단계를 수행하여 SmartHotel360 앱의 웹 계층과 데이터 계층을 Azure로 마이그레이션합니다.

1. Contoso는 이미 Azure 인프라를 갖추고 있으므로 이 시나리오를 위해 몇 가지 특정 Azure 구성 요소를 추가하기만 하면 됩니다.
2. 데이터 계층은 Data Migration Service를 사용하여 마이그레이션됩니다. Data Migration Service는 Contoso 데이터 센터와 Azure 사이의 사이트 간 VPN 연결을 통해 온-프레미스 SQL Server VM에 연결합니다. 그런 다음, Data Migration Service에서 데이터베이스를 마이그레이션합니다.
3. 웹 계층은 Site Recovery를 사용하여 리프트 앤 시프트 마이그레이션 방식으로 마이그레이션됩니다. 이 프로세스에는 온-프레미스 VMware 환경을 준비하고, 복제를 설정 및 활성화하고, VM을 Azure로 장애 조치하여 마이그레이션하는 작업이 수반됩니다.

     ![마이그레이션 아키텍처](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

### <a name="azure-services"></a>Azure 서비스

서비스 | 설명 | 비용
--- | --- | ---
[Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Database Migration Service를 사용하면 최소한의 가동 중지 시간으로 여러 데이터베이스 원본에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있습니다. | [지원되는 지역](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) 및 [Database Migration Service 가격](https://azure.microsoft.com/pricing/details/database-migration/)에 대해 자세히 알아보세요.
[Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance는 Azure 클라우드에서 완벽하게 관리되는 SQL Server 인스턴스를 나타내는 관리되는 데이터베이스 서비스입니다. 최신 버전의 SQL Server 데이터베이스 엔진과 동일한 코드를 사용하고 최신 기능, 향상된 성능 및 보안 패치를 포함하고 있습니다. | Azure에서 실행되는 SQL Database Managed Instance를 사용하면 용량에 따라 요금이 발생합니다. [Managed Instance 가격](https://azure.microsoft.com/pricing/details/sql-database/managed/)에 대해 자세히 알아보세요. 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Site Recovery 서비스는 Azure VM, 온-프레미스 VM 및 물리적 서버에 대한 마이그레이션과 재해 복구를 오케스트레이션하고 관리합니다.  | Azure로 복제하는 동안 Azure Storage 비용이 청구됩니다.  Azure VM이 만들어지고, 장애 조치가 발생하는 경우 요금이 발생합니다. [Site Recovery 비용 및 가격](https://azure.microsoft.com/pricing/details/site-recovery/)에 대해 자세히 알아보세요.

 

## <a name="prerequisites"></a>필수 조건

이 시나리오에서는 Contoso 및 다른 사용자가 다음 필수 조건을 충족해야 합니다.

요구 사항 | 세부 정보
--- | ---
**Managed Instance 미리 보기에 등록** | SQL Database Managed Instance 제한된 공개 미리 보기에 등록해야 합니다. [등록](https://portal.azure.com#create/Microsoft.SQLManagedInstance)하려면 Azure 구독이 필요합니다. 등록을 완료하는 데 며칠이 걸릴 수 있으므로 이 시나리오를 배포하기 전에 등록해야 합니다.
**Azure 구독** | 이 시리즈의 첫 번째 문서에서 평가를 수행했을 때 이미 구독이 만들어졌어야 합니다. Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.<br/><br/> 체험 계정을 만들면 구독 관리자로서 모든 작업을 수행할 수 있습니다.<br/><br/> 기존 구독을 사용하고 있고 구독의 관리자가 아닌 경우 관리자와 협력하여 소유자 또는 기여자 권한을 할당받아야 합니다.<br/><br/> 더 세부적인 권한이 필요하면 [역할 기반 액세스 제어를 사용하여 Site Recovery 액세스 관리](../site-recovery/site-recovery-role-based-linked-access-control.md)를 참조하세요. 
**Site Recovery(온-프레미스)** | 온-프레미스 vCenter Server 인스턴스에서 5.5, 6.0 또는 6.5 버전을 실행해야 합니다.<br/><br/> 5.5, 6.0 또는 6.5 버전을 실행하는 ESXi 호스트<br/><br/> ESXi 호스트에서 실행되는 하나 이상의 VMware VM<br/><br/> VM은 [Azure 요구 사항](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)을 충족해야 합니다.<br/><br/> 지원되는 [네트워크](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) 및 [저장소](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) 구성입니다.
**Database Migration Service** | Database Migration Service의 경우, [호환 가능한 온-프레미스 VPN 디바이스](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)가 필요합니다.<br/><br/> 온-프레미스 VPN 디바이스를 구성할 수 있어야 합니다. 외부 연결 공용 IPv4 주소가 있어야 합니다. 주소는 NAT 디바이스 뒤에 배치할 수 없습니다.<br/><br/> 온-프레미스 SQL Server 데이터베이스에 대한 권한이 있는지 확인합니다.<br/><br/> Windows 방화벽은 원본 데이터베이스 엔진에 액세스할 수 있어야 합니다. [데이터베이스 엔진 액세스에 대한 Windows 방화벽 구성](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)에 대해 알아보세요.<br/><br/> 데이터베이스 머신 앞에 방화벽이 있는 경우 445 SMB 포트를 통해 데이터베이스 및 파일에 대한 액세스를 허용하는 규칙을 추가합니다.<br/><br/> 원본 SQL Server 인스턴스에 연결하는 데 사용되는 자격 증명과 대상 Managed Instance는 sysadmin 서버 역할의 구성원이야 합니다.<br/><br/> Database Migration Service에서 원본 데이터베이스를 백업하는 데 사용할 수 있는 온-프레미스 데이터베이스에는 네트워크 공유가 필요합니다.<br/><br/> 원본 SQL Server 인스턴스를 실행하는 서비스 계정에 네트워크 공유에 대한 쓰기 권한이 있는지 확인합니다.<br/><br/> 네트워크 공유에 대한 모든 권한이 있는 Windows 사용자 및 암호를 적어 둡니다. Database Migration Service는 이러한 사용자 자격 증명을 가장하여 Azure Storage 컨테이너에 백업 파일을 업로드합니다.<br/><br/> SQL Server Express 설치 프로세스는 TCP/IP 프로토콜을 기본적으로 **사용 안 함**으로 설정합니다. 활성화되어 있는지 확인합니다.

## <a name="scenario-steps"></a>시나리오 단계

Contoso에서 배포를 설정하려는 방법은 다음과 같습니다.

> [!div class="checklist"]
> * **1단계: SQL Database Managed Instance 설정**: Contoso에는 온-프레미스 SQL Server 데이터베이스에서 마이그레이션할 미리 만들어진 Managed Instance가 필요합니다.
> * **2단계: Database Migration Service 준비**: Contoso는 데이터베이스 마이그레이션 공급 기업을 등록하고, 인스턴스를 만든 다음, Database Migration Service 프로젝트를 만들어야 합니다. 또한 Contoso에서 Database Migration Service에 대한 SAS(공유 액세스 서명) URI(Uniform Resource Identifier)도 설정해야 합니다. SAS URI는 Contoso의 저장소 계정에 있는 리소스에 대한 위임된 액세스를 제공하므로 Contoso에서 저장소 개체에 제한된 권한을 부여할 수 있습니다. Contoso에서 SAS URI를 설정하므로 Database Migration Service는 SQL Server 백업 파일을 업로드하는 저장소 계정 컨테이너에 액세스할 수 있습니다.
> * **3단계: Site Recovery를 위한 Azure 준비**: Contoso는 Site Recovery를 위해 복제된 데이터를 보관할 스토리지 계정을 만들어야 합니다. 또한 Azure Recovery Services 자격 증명 모음도 만들어야 합니다.
> * **4단계: Site Recovery를 위한 온-프레미스 VMware 준비**: Contoso에서 장애 조치 후에 Azure VM에 연결하기 위해 VM 검색 및 에이전트 설치를 위한 계정을 준비합니다.
> * **5단계: VM 복제**: Contoso에서 복제를 설정하기 위해 Site Recovery 원본 및 대상 환경을 구성하고, 복제 정책을 설정하고, Azure Storage로의 VM 복제를 시작합니다.
> * **6단계: Database Migration Service를 사용하여 데이터베이스 마이그레이션**: Contoso에서 데이터베이스를 마이그레이션합니다.
> * **7단계: Site Recovery를 사용하여 VM 마이그레이션**: Contoso에서 모든 것이 제대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다. 그런 다음, Contoso에서 전체 장애 조치를 실행하여 VM을 Azure로 마이그레이션합니다.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>1단계: SQL Database Managed Instance 준비

Azure SQL Database Managed Instance를 설정하려면 Contoso에 다음 요구 사항을 충족하는 서브넷이 필요합니다.

- 서브넷은 전용이어야 합니다. 비어 있어야 하며, 다른 클라우드 서비스를 포함할 수 없습니다. 이 서브넷은 게이트웨이 서브넷일 수 없습니다.
- Managed Instance가 만들어지면 Contoso에서 리소스를 서브넷에 추가하면 안됩니다.
- 서브넷에는 연결된 네트워크 보안 그룹이 있을 수 없습니다.
- 서브넷에는 UDR(사용자 정의 경로) 경로 테이블이 있어야 합니다. 할당되는 유일한 경로는 0.0.0.0/0 다음 홉 인터넷이어야 합니다. 
- 선택적 사용자 지정 DNS: 사용자 지정 DNS가 Azure 가상 네트워크에 지정되는 경우 Azure의 재귀 확인자 IP 주소(예: 168.63.129.16)를 목록에 추가해야 합니다. [Managed Instance에 대한 사용자 지정 DNS를 구성하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)에 대해 알아보세요.
- 서브넷에는 연결된 서비스 엔드포인트(저장소 또는 SQL)가 없어야 합니다. 서비스 엔드포인트는 가상 네트워크에서 비활성화되어야 합니다.
- 서브넷에는 16개 이상의 IP 주소가 있어야 합니다. [Managed Instance 서브넷의 크기를 조정하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration)에 대해 알아보세요.
- Contoso의 하이브리드 환경에서는 사용자 지정 DNS 설정이 필요합니다. Contoso에서 회사의 Azure DNS 서버 중 하나 이상을 사용하도록 DNS 설정을 구성합니다. [DNS 사용자 지정](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)에 대해 자세히 알아보세요.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Managed Instance에 대한 가상 네트워크 설정

Contoso 관리자는 다음과 같이 가상 네트워크를 설정합니다. 

1. 미국 동부 2 주 지역에 새 가상 네트워크(**VNET-SQLMI-EU2**)를 만듭니다. 가상 네트워크를 **ContosoNetworkingRG** 리소스 그룹에 추가합니다.
2. 주소 공간을 10.235.0.0/24로 할당하여 범위가 회사의 다른 네트워크와 겹치지 않도록 합니다.
3. 네트워크에 다음 두 개의 서브넷을 추가합니다.
    - **SQLMI-DS-EUS2**(10.235.0.0.25)
    - **SQLMI-SAW-EUS2**(10.235.0.128/29). 이 서브넷은 디렉터리를 Managed Instance에 연결하는 데 사용됩니다.

      ![Managed Instance - 가상 네트워크 만들기](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

4. 가상 네트워크와 서브넷이 배포되면 다음과 같이 네트워크를 피어링합니다.

    - **VNET-SQLMI-EUS2**를 **VNET-HUB-EUS2**(미국 동부 2에 대한 허브 가상 네트워크)와 피어링합니다.
    - **VNET-SQLMI-EUS2**를 **VNET-PROD-EUS2**(프로덕션 네트워크)와 피어링합니다.

      ![네트워크 피어링](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

5. 사용자 지정 DNS 설정을 지정합니다. DNS는 먼저 Contoso의 Azure 도메인 컨트롤러를 가리킵니다. Azure DNS는 보조 DNS입니다. Contoso Azure 도메인 컨트롤러가 있는 위치는 다음과 같습니다.

    - 미국 동부 2 프로덕션 네트워크(**VNET-PROD-EUS2**)의 **PROD-DC-EUS2** 서브넷에 있습니다.
    - **CONTOSODC3** 주소: 10.245.42.4
    - **CONTOSODC4** 주소: 10.245.42.5
    - Azure DNS 확인자: 168.63.129.16

      ![네트워크 DNS 서버](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*도움이 더 필요하세요?*

- [SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)에 대한 개요를 참조합니다.
- [SQL Database Managed Instance에 대한 가상 네트워크를 만드는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration)을 알아봅니다.
- [피어링을 설정하는 방법](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)을 알아봅니다.
- [Azure Active Directory DNS 설정을 업데이트하는 방법](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns)을 알아봅니다.

### <a name="set-up-routing"></a>라우팅 설정

Managed Instance는 사설 가상 네트워크에 배치됩니다. Contoso에는 가상 네트워크에서 Azure 관리 서비스와 통신하기 위한 경로 테이블이 필요합니다. 가상 네트워크에서 이를 관리하는 서비스와 통신할 수 없으면 가상 네트워크에 액세스하지 못하게 됩니다.

Contoso에서 고려해야 하는 요소는 다음과 같습니다.

- 경로 테이블에는 Managed Instance에서 보낸 패킷을 가상 네트워크에서 라우팅하는 방법을 지정하는 규칙(경로) 집합이 포함됩니다.
- 경로 테이블은 Managed Instance가 배포된 서브넷과 연결됩니다. 서브넷에서 나가는 각 패킷은 연결된 경로 테이블에 따라 처리됩니다.
- 서브넷은 단일 경로 테이블에만 연결할 수 있습니다.
- Microsoft Azure에서 경로 테이블을 만드는 데는 추가 요금이 부과되지 않습니다.

  라우팅을 설정하기 위해 Contoso 관리자는 다음을 수행합니다.

1. **ContosoNetworkingRG** 리소스 그룹에 UDR(경로) 테이블을 만듭니다.

    ![경로 테이블](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Managed Instance 요구 사항을 준수하기 위해 경로 테이블(**MIRouteTable**)이 배포되면 주소 접두사가 0.0.0.0/0인 경로를 추가합니다. **다음 홉 유형** 옵션은 **인터넷**으로 설정됩니다.

    ![경로 테이블 접두사](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. 경로 테이블을 **SQLMI-DB-EUS2** 서브넷(**VNET-SQLMI-EUS2** 네트워크에 속함)에 연결합니다. 

    ![경로 테이블 서브넷](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*도움이 더 필요하세요?*

[Managed Instance에 대한 경로를 설정하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal)을 알아봅니다.

### <a name="create-a-managed-instance"></a>Managed Instance 만들기

이제 Contoso 관리자는 SQL Database Managed Instance를 프로비전할 수 있습니다.

1. Managed Instance에서 비즈니스 앱을 제공하므로 회사의 미국 동부 2 주 지역에 Managed Instance를 배포합니다. Managed Instance를 **ContosoRG** 리소스 그룹에 추가합니다.
2. 인스턴스에 대한 가격 책정 계층, 크기 계산 및 스토리지를 선택합니다. [Managed Instance 가격](https://azure.microsoft.com/pricing/details/sql-database/managed/)에 대해 자세히 알아보세요.

    ![관리되는 인스턴스](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Managed Instance가 배포되면 **ContosoRG** 리소스 그룹에 다음 두 개의 새 리소스가 표시됩니다.

    - Contoso에 여러 Managed Instance가 있는 경우의 가상 클러스터
    - SQL Server Database Managed Instance 

      ![관리되는 인스턴스](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*도움이 더 필요하세요?*

[Managed Instance를 프로비전하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal)을 알아봅니다.

## <a name="step-2-prepare-the-database-migration-service"></a>2단계: Database Migration Service 준비

Database Migration Service를 준비하기 위해 Contoso 관리자는 다음 몇 가지 작업을 수행해야 합니다.

- Azure에서 Database Migration Service 공급자를 등록합니다.
- 데이터베이스를 마이그레이션하는 데 사용되는 백업 파일을 업로드하기 위해 Azure Storage에 대한 액세스 권한을 Database Migration Service에 제공합니다. Azure Storage에 대한 액세스를 제공하기 위해 Azure Blob 스토리지 컨테이너를 만들고, 이 Blob Storage 컨테이너에 대한 SAS URI를 생성합니다. 
- Database Migration Service 프로젝트를 만듭니다.

그런 다음, 다음 단계를 수행합니다.

1. 데이터베이스 마이그레이션 공급자를 구독에 등록합니다.
    ![Database Migration Service - 등록](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Blob Storage 컨테이너를 만듭니다. Contoso는 Database Migration Service에서 액세스할 수 있도록 SAS URI를 생성합니다.

    ![Database Migration Service - SAS URI 생성](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Database Migration Service 인스턴스를 만듭니다. 

    ![Database Migration Service - 인스턴스 만들기](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Database Migration Service 인스턴스를 **VNET-PROD-DC-EUS2** 가상 네트워크의 **PROD-DC-EUS2** 서브넷에 배치합니다.
    - Database Migration Service에서 VPN 게이트웨이를 통해 온-프레미스 SQL Server VM에 액세스할 수 있는 가상 네트워크에 있어야 하므로 이 서비스를 여기에 배치합니다.
    - **VNET-PROD-EUS2**는 **VNET-HUB-EUS2**에 피어링되고 원격 게이트웨이를 사용하도록 허용됩니다. **원격 게이트웨이 사용** 옵션은 Database Migration Service에서 필요에 따라 통신할 수 있도록 합니다.

        ![Database Migration Service - 네트워크 구성](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*도움이 더 필요하세요?*

- [Database Migration Service를 설정하는 방법](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal)을 알아봅니다.
- [SAS를 만들고 사용하는 방법](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)을 알아봅니다.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>3단계: Site Recovery 서비스를 위한 Azure 준비

웹 계층 VM(**WEBMV**)을 마이그레이션하도록 Site Recovery를 설정하기 위해 Contoso에 필요한 몇 가지 Azure 요소는 다음과 같습니다.

- 장애 조치된 리소스가 있는 가상 네트워크
- 복제된 데이터를 보관할 저장소 계정 
- Azure의 Recovery Services 자격 증명 모음

Contoso 관리자는 다음과 같이 Site Recovery를 설정합니다.

1. VM이 SmartHotel360 앱의 웹 프런트 엔드이므로 Contoso는 VM을 기존 프로덕션 네트워크(**VNET-PROD-EUS2**) 및 서브넷(**PROD-FE-EUS2**)으로 장애 조치합니다. 네트워크와 서브넷은 미국 동부 2 주 지역에 있습니다. 네트워크는 Contoso에서 [Azure 인프라를 배포](contoso-migration-infrastructure.md)할 때 설정했습니다.
2. 저장소 계정(**contosovmsacc20180528**)을 만듭니다. Contoso에서 범용 계정을 사용하고, 표준 저장소 및 로컬 중복 저장소 복제를 선택합니다.

    ![Site Recovery - 저장소 계정 만들기](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. 준비된 네트워크와 저장소 계정을 사용하여 자격 증명 모음(**ContosoMigrationVault**)을 만듭니다. Contoso는 미국 동부 2 주 지역의 **ContosoFailoverRG** 리소스 그룹에 자격 증명 모음을 배치합니다.

    ![Recovery Services - 자격 증명 모음 만들기](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*도움이 더 필요하세요?*

[Site Recovery를 위해 Azure를 설정하는 방법](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)을 알아봅니다.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>4단계: Site Recovery를 위한 온-프레미스 VMware 준비

Site Recovery를 위해 VMware를 준비하려면 Contoso 관리자가 다음 작업을 수행해야 합니다.

- vCenter Server 인스턴스 또는 vSphere ESXi 호스트에서 계정을 준비합니다. 이 계정은 VM 검색을 자동화합니다.
- Contoso에서 복제하려는 VMware VM에 모바일 서비스를 자동 설치할 수 있는 계정을 준비합니다.
- 장애 조치 후에 Azure VM이 만들어질 때 온-프레미스 VM에 연결되도록 준비합니다.


### <a name="prepare-an-account-for-automatic-discovery"></a>자동 검색용 계정 준비

Site Recovery에서는 다음 작업을 위해 VMware 서버에 액세스해야 합니다.

- VM을 자동으로 검색합니다. 최소한 읽기 전용 계정이 필요합니다.
- 복제, 장애 조치 및 장애 복구를 오케스트레이션합니다. Contoso에는 디스크 만들기 및 제거, VM 전원 켜기와 같은 작업을 실행할 수 있는 계정이 필요합니다.

Contoso 관리자는 다음 작업을 수행하여 계정을 설정합니다.

1. vCenter 수준에서 역할을 만듭니다.
2. 해당 역할에 필요한 권한을 할당합니다.

*도움이 더 필요하세요?*

[자동 검색을 위한 역할을 만들고 할당하는 방법](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)을 알아봅니다.

### <a name="prepare-an-account-for-mobility-service-installation"></a>모바일 서비스 설치를 위한 계정 준비

Contoso에서 복제하려는 VM에 모바일 서비스를 설치해야 합니다. Contoso에서 모바일 서비스에 대해 고려해야 하는 요소는 다음과 같습니다.

- Contoso에서 VM 복제를 사용하도록 설정하면 Site Recovery에서 이 구성 요소의 자동 강제 설치를 수행할 수 있습니다.
- 자동 강제 설치의 경우 Contoso는 Site Recovery에서 VM에 액세스하는 데 사용하는 계정을 준비해야 합니다.
- 이 계정은 Azure 콘솔에서 복제를 구성할 때 지정됩니다.
- Contoso에는 VM에 설치할 수 있는 권한이 있는 도메인 또는 로컬 계정이 있어야 합니다.

*도움이 더 필요하세요?*

[모바일 서비스의 강제 설치를 위한 계정을 만드는 방법](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)을 알아봅니다.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>장애 조치(Failover) 후 Azure VM에 연결할 준비

Azure로 장애 조치되면 Contoso는 Azure에서 복제된 VM에 연결하려고 합니다. Azure에서 복제된 VM에 연결하기 위해 Contoso 관리자는 마이그레이션하기 전에 온-프레미스 VM에 대해 다음 몇 가지 작업을 수행해야 합니다. 

1. 인터넷을 통해 액세스하려면 장애 조치 전에 온-프레미스 VM에서 RDP를 활성화합니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 RDP가 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 허용되는지 확인합니다.
2. Contoso의 사이트 간 VPN을 통해 액세스하려면 온-프레미스 머신에서 RDP를 사용하도록 설정합니다. **Windows 방화벽** > **허용되는 앱 및 기능**에서 **도메인 및 개인** 네트워크에 대해 RDP를 허용합니다.
3. 온-프레미스 VM에서 운영 체제의 SAN 정책을 **OnlineAll**로 설정합니다.

또한 Contoso 관리자는 장애 조치를 실행할 때 다음 항목을 확인해야 합니다.

- 장애 조치가 트리거될 때 VM에 보류 중인 Windows 업데이트가 없어야 합니다. Windows 업데이트가 보류 중인 경우 업데이트가 완료될 때까지 Contoso 사용자는 가상 머신에 로그인할 수 없습니다.
- 장애 조치 후 관리자는 **부트 진단**을 확인하여 VM에 대한 스크린샷을 확인해야 합니다. 부트 진단을 확인할 수 없는 경우 VM이 실행되는지 확인한 다음, [문제 해결 팁](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)을 검토해야 합니다.

## <a name="step-5-replicate-the-on-premises-vms-to-azure"></a>5단계: 온-프레미스 VM을 Azure로 복제

Azure로 마이그레이션을 실행하기 전에 Contoso 관리자는 온-프레미스 VM에 대한 복제를 설정하고 활성화해야 합니다.

### <a name="set-a-replication-goal"></a>복제 목표 설정

1. 자격 증명 모음의 자격 증명 모음 이름(**ContosoVMVault**) 아래에서 복제 목표를 설정합니다(**시작** > **Site Recovery** > **인프라 준비**).
2. 머신이 온-프레미스에 있고, VMware VM이 되고, Azure로 복제하도록 지정합니다.

    ![인프라 준비 - 보호 목표](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>배포 계획 확인

계속 진행하기 위해 Contoso 관리자는 배포 계획을 완료했는지 확인합니다. **예, 완료함**을 선택합니다. 이 배포에서 Contoso는 단일 VM만 마이그레이션하므로 배포 계획이 필요하지 않습니다.

### <a name="set-up-the-source-environment"></a>원본 환경 설정

이제 Contoso 관리자는 원본 환경을 구성합니다. 원본 환경을 설정하려면 OVF 템플릿을 다운로드하고, 이를 사용하여 구성 서버 및 관련 구성 요소를 고가용성 온-프레미스 VMware VM으로 배포합니다. 서버에서 구성 요소는 다음과 같습니다.

- 온-프레미스 인프라와 Azure 간의 통신을 조정하는 구성 서버. 구성 서버는 데이터 복제를 관리합니다.
- 프로세스 서버 - 복제 게이트웨이의 역할을 합니다. 프로세스 서버에서 수행하는 작업은 다음과 같습니다.
    - 복제 데이터를 받습니다.
    - 캐싱, 압축 및 암호화를 사용하여 복제 날짜를 최적화합니다.
    - 복제 날짜를 Azure Storage로 보냅니다.
- 또한 프로세스 서버는 복제될 VM에 모바일 서비스를 설치하며, 온-프레미스 VMware VM의 자동 검색을 수행합니다.
- 구성 서버 VM이 만들어지고 시작되면 Contoso는 이 서버를 자격 증명 모음에 등록합니다.

원본 환경을 설정하기 위해 Contoso 관리자는 다음을 수행합니다.

1. Azure Portal(**인프라 준비** > **원본** > **구성 서버**)에서 OVF 템플릿을 다운로드합니다.
    
    ![구성 서버 추가](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. 템플릿을 VMware로 가져와서 VM을 만들고 배포합니다.

    ![OVF 템플릿 배포](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  VM을 처음 작동하면 Windows Server 2016 설치 환경에서 시작됩니다. 사용권 계약에 동의하고 관리자 암호를 입력합니다.
4. 설치가 완료되면 VM에 관리자 권한으로 로그인합니다. 처음 로그인하면 Azure Site Recovery 구성 도구가 자동으로 실행됩니다.
5. Site Recovery 구성 도구에서 구성 서버를 자격 증명 모음에 등록하는 데 사용할 이름을 입력합니다.
6. 이 도구에서 Azure에 대한 VM의 연결을 확인합니다. 연결이 설정되면 **로그인**을 선택하여 Azure 구독에 로그인합니다. 자격 증명에는 구성 서버가 등록된 자격 증명 모음에 대한 액세스 권한이 있어야 합니다. 

    ![구성 서버 등록](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. 도구에서 몇 가지 구성 작업을 수행한 후 다시 부팅합니다. 머신에 다시 로그인합니다. 구성 서버 관리 마법사가 자동으로 시작됩니다.
8. 마법사에서 복제 트래픽을 받을 NIC를 선택합니다. 이 설정은 구성된 후에 변경할 수 없습니다.
9. 구성 서버를 등록할 구독, 리소스 그룹 및 Recovery Services 자격 증명 모음을 선택합니다.

    ![Recovery Services 자격 증명 모음 선택](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. MySQL 서버 및 VMWare PowerCLI를 다운로드하여 설치합니다. 그런 다음, 서버 설정의 유효성을 검사합니다.
11. 유효성 검사가 완료되면 vCenter Server 인스턴스 또는 vSphere 호스트의 FQDN 또는 IP 주소를 입력합니다. 기본 포트를 유지하고, Azure에서 vCenter Server 인스턴스에 대한 표시 이름을 입력합니다.
12. Site Recovery에서 복제에 사용할 수 있는 VMware VM을 자동으로 검색할 수 있도록 앞에서 만든 계정을 지정해야 합니다. 
13. 복제를 사용하도록 설정하면 모바일 서비스가 자동으로 설치되도록 자격 증명을 입력합니다. Windows 머신의 경우 VM에 대한 로컬 관리자 권한이 계정에 필요합니다. 

    ![vCenter Server 구성](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. 등록이 완료되면 Azure Portal에서 구성 서버와 VMware 서버가 자격 증명 모음의 **원본** 페이지에 나열되어 있는지 다시 확인합니다. 검색하는 데 15분 이상 걸릴 수 있습니다. 
8. Site Recovery는 지정한 설정을 사용하여 VMware 서버에 연결하고 VM을 검색합니다.

### <a name="set-up-the-target"></a>대상 설정

이제 Contoso 관리자는 대상 복제 환경을 구성합니다.

1. **인프라 준비** > **대상**에서 대상 설정을 선택합니다.
2. Site Recovery에서 지정된 대상에 저장소 계정과 네트워크가 있는지 확인합니다.

### <a name="create-a-replication-policy"></a>복제 정책 만들기

원본과 대상이 설정되면 Contoso 관리자는 복제 정책을 만들고 이 정책을 구성 서버와 연결합니다.

1. **인프라 준비** > **복제 설정** > **복제 정책** >  **만들기 및 연결**에서 **ContosoMigrationPolicy** 정책을 만듭니다.
2. 다음 기본 설정을 사용합니다.
   - **RPO 임계값**: 기본값은 60분입니다. 이 값은 복구 지점을 만드는 빈도를 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
   - **복구 지점 보존**: 기본값은 24시간입니다. 이 값은 각 복구 지점에 대한 보존 기간을 지정합니다. 복제된 VM은 하나의 시간대에서 임의의 시점으로 복구할 수 있습니다.
   - **앱 일치 스냅숏 빈도**: 1시간이 기본값입니다. 이 값은 애플리케이션 일치 스냅숏이 만들어지는 빈도를 지정합니다.
 
     ![복제 정책 - 만들기](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. 정책은 구성 서버와 자동으로 연결됩니다. 

    ![복제 정책 - 연결](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*도움이 더 필요하세요?*

- 이러한 단계에 대한 전체 연습은 [온-프레미스 VMware VM 재해 복구 설정](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)에서 참조할 수 있습니다.
- [원본 환경 설정](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [구성 서버 배포](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), [복제 설정 구성](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)에서 자세한 지침을 사용할 수 있습니다.

### <a name="enable-replication"></a>복제 사용

이제 Contoso 관리자는 WebVM 복제를 시작할 수 있습니다.

1. **애플리케이션 복제** > **원본** > **복제**에서 원본 설정을 선택합니다.
2. 가상 머신을 사용하도록 설정하고, vCenter Server 인스턴스를 선택하며, 구성 서버를 설정하려고 함을 나타냅니다.

    ![복제 사용 - 원본](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. 장애 조치 후에 Azure VM을 배치할 리소스 그룹 및 네트워크를 포함하여 대상 설정을 지정합니다. 복제된 데이터를 저장할 저장소 계정을 지정합니다.

    ![복제 사용 - 대상](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. 복제할 **WebVM**을 선택합니다. 복제를 사용하도록 설정하면 Site Recovery에서 각 VM에 모바일 서비스를 설치합니다. 

    ![복제 사용 - VM 선택](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. 올바른 복제 정책이 선택되어 있는지 확인하고 **WEBVM**에 대한 복제를 사용하도록 설정합니다. **작업**에서 복제 진행 상황을 추적합니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.
6. Azure Portal의 **기본 정보**에서 Azure로 복제되는 VM에 대한 상태를 확인할 수 있습니다.

    ![인프라 보기](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*도움이 더 필요하세요?*

이러한 단계의 전체 연습은 [복제를 사용하도록 설정](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)에서 확인할 수 있습니다.

## <a name="step-6-migrate-the-database"></a>6단계: 데이터베이스 마이그레이션 

Contoso 관리자는 Database Migration Service 프로젝트를 만든 다음, 데이터베이스를 마이그레이션해야 합니다.

### <a name="create-a-database-migration-service-project"></a>Database Migration Service 프로젝트 만들기

1. Database Migration Service 프로젝트를 만듭니다. **SQL Server** 원본 서버 유형을 선택하고, **Azure SQL Database Managed Instance**를 대상으로 선택합니다.

     ![Database Migration Service - 새 마이그레이션 프로젝트](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. 마이그레이션 마법사가 열립니다.

### <a name="migrate-the-database"></a>데이터베이스 마이그레이션 

1. 마이그레이션 마법사에서 온-프레미스 데이터베이스가 있는 원본 VM을 지정합니다. 데이터베이스에 액세스하기 위한 자격 증명을 입력합니다.

    ![Database Migration Service - 원본 세부 정보](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. 마이그레이션할 데이터베이스(**SmartHotel.Registration**)를 선택합니다.

    ![Database Migration Service - 원본 데이터베이스 선택](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. 대상에 대해 Azure에 Managed Instance의 이름과 액세스 자격 증명을 입력합니다.

    ![Database Migration Service - 대상 세부 정보](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. **새 활동** > **마이그레이션 실행**에서 마이그레이션을 실행할 다음 설정을 지정합니다.
    - 원본 및 대상 자격 증명
    - 마이그레이션할 데이터베이스
    - 온-프레미스 VM에서 만든 네트워크 공유. Database Migration Service에서 원본 백업을 이 공유로 가져옵니다. 
        - 원본 SQL Server 인스턴스를 실행하는 서비스 계정에는 이 공유에 대한 쓰기 권한이 있어야 합니다.
        - 공유에 대한 FQDN 경로를 사용해야 합니다.
    - 서비스에서 마이그레이션을 위해 백업 파일을 업로드하는 저장소 계정 컨테이너에 대한 액세스 권한을 Database Migration Service에 제공하는 SAS URI

        ![Database Migration Service - 마이그레이션 설정 구성](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. 마이그레이션 설정을 저장한 다음, 마이그레이션을 실행합니다.
6. **개요**에서 마이그레이션 상태를 모니터링합니다.

    ![Database Migration Service - 모니터링](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. 마이그레이션이 완료되면 대상 데이터베이스가 Managed Instance에 있는지 확인합니다.

    ![Database Migration Service - 데이터베이스 마이그레이션 확인](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm"></a>7단계: VM 마이그레이션

Contoso 관리자는 빠른 테스트 장애 조치를 실행한 다음, VM을 마이그레이션합니다.

### <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

WEBVM을 마이그레이션하기 전에 테스트 장애 조치를 사용하면 모든 항목이 예상대로 작동하는지 확인할 수 있습니다. 관리자는 다음 단계를 수행합니다.

1. 사용할 수 있는 최근 특정 시점(**가장 최근에 처리됨**)으로 테스트 장애 조치를 실행합니다.
2. **장애 조치를 시작하기 전에 머신을 종료합니다.** 를 선택합니다. 이 옵션이 선택되면 Site Recovery에서 장애 조치를 트리거하기 전에 원본 VM을 종료하려고 합니다. 종료가 실패하는 경우에도 장애 조치는 계속됩니다. 
3. 테스트 장애 조치가 다음과 같이 실행됩니다. 
    1. 마이그레이션에 필요한 모든 조건이 충족되는지 확인하기 위해 필수 구성 요소 확인이 실행됩니다.
    2. 장애 조치(failover)는 데이터를 처리하며 이 데이터에서 Azure VM이 만들어질 수 있습니다. 최신 복구 지점을 선택하면 해당 데이터에서 복구 지점이 만들어집니다.
    3.  이전 단계에서 처리된 데이터를 사용하여 Azure VM이 만들어집니다.
3. 장애 조치가 완료되면 Azure Portal에 Azure VM 복제본이 표시됩니다. 모든 항목이 제대로 작동하는지, 즉 VM이 적절한 크기이고 올바른 네트워크에 연결되어 실행되고 있는지 확인합니다. 
4. 테스트 장애 조치를 확인한 후 장애 조치를 정리하고 모든 관찰 결과를 기록합니다. 

### <a name="migrate-the-vm"></a>VM 마이그레이션

1. Contoso 관리자는 테스트 장애 조치가 예상대로 작동했는지 확인한 후 마이그레이션을 위한 복구 계획을 만들고 WEBVM을 계획에 추가합니다.

     ![복구 계획 만들기 - 항목 선택](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. 최신 복구 지점을 선택하여 계획에서 장애 조치를 실행합니다. Site Recovery에서 장애 조치를 트리거하기 전에 온-프레미스 VM을 종료하도록 지정합니다.

    ![장애 조치(failover)](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. 장애 조치(failover) 후 Azure Portal에 예상대로 Azure VM이 표시되는지 확인합니다.

   ![복구 계획 세부 정보](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. 확인한 후 마이그레이션을 완료하여 마이그레이션 프로세스를 마치고, VM 복제를 중지하고, VM에 대한 Site Recovery 청구를 중지합니다.

    ![장애 조치 - 마이그레이션 완료](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>연결 문자열 업데이트

마이그레이션 프로세스의 마지막 단계로, Contoso 관리자는 Contoso의 Managed Instance에서 실행되는 마이그레이션된 데이터베이스를 가리키도록 애플리케이션의 연결 문자열을 업데이트합니다.

1. Azure Portal에서 **설정** > **연결 문자열**을 차례로 선택하여 연결 문자열을 찾습니다.

    ![연결 문자열](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. 문자열을 SQL Database Managed Instance의 사용자 이름과 암호로 업데이트합니다.
3. 문자열이 구성되면 해당 애플리케이션의 web.config 파일에 있는 현재 연결 문자열을 바꿉니다.
4. 파일이 업데이트되고 저장되면 명령 프롬프트 창에서 `IISRESET /RESTART`를 실행하여 WEBVM에서 IIS를 다시 시작합니다.
5. IIS가 다시 시작되면 응용 프로그램이 SQL Database Managed Instance에서 실행되는 데이터베이스를 사용합니다.
6. 이 시점에서 온-프레미스에서 SQLVM 머신을 종료할 수 있습니다. 마이그레이션이 완료되었습니다.

*도움이 더 필요하세요?*

- [테스트 장애 조치를 실행하는 방법](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)을 알아봅니다. 
- [복구 계획을 만드는 방법](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)을 알아봅니다.
- [Azure로 장애 조치하는 방법](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)을 알아봅니다.

## <a name="clean-up-after-migration"></a>마이그레이션 후 정리

마이그레이션이 완료되면 SmartHotel360 앱은 Azure VM에서 실행되고, SmartHotel360 데이터베이스는 Azure SQL Database Managed Instance에서 사용할 수 있습니다.  

이제 Contoso에서 수행해야 하는 정리 작업은 다음과 같습니다.  

- vCenter Server 인벤토리에서 WEBVM 머신을 제거합니다.
- vCenter Server 인벤토리에서 SQLVM 머신을 제거합니다.
- 로컬 백업 작업에서 WEBVM 및 SQLVM을 제거합니다.
- 내부 설명서를 업데이트하여 WEBVM에 대한 새 위치와 IP 주소를 표시합니다.
- 내부 설명서에서 SQLVM을 제거합니다. 또는 Contoso에서 SQLVM을 삭제된 것으로 표시하고 VM 인벤토리에 더 이상 표시하지 않도록 설명서를 수정할 수 있습니다.
- 서비스 해제된 VM과 상호 작용하는 모든 리소스를 검토합니다. 새 구성을 반영하도록 모든 관련 설정 또는 설명서를 업데이트합니다.

## <a name="review-the-deployment"></a>배포 검토

Contoso는 Azure에서 마이그레이션된 리소스를 사용하여 새 인프라를 완벽하게 운영하고 보호해야 합니다.

### <a name="security"></a>보안

Contoso 보안 팀에서 Azure VM 및 SQL Database Managed Instance를 검토하여 구현과 관련된 모든 보안 문제를 확인합니다.

- 팀은 VM에 대한 액세스를 제어하는 데 사용되는 네트워크 보안 그룹을 검토합니다. 네트워크 보안 그룹은 앱에 허용되는 트래픽만 전달할 수 있도록 합니다.
- Contoso의 보안 팀은 Azure Disk Encryption 및 Azure Key Vault를 사용하여 디스크의 데이터를 보호하는 것도 고려하고 있습니다.
- 팀은 Managed Instance에서 위협 탐지를 사용하도록 설정합니다. 위협이 탐지되면 Contoso의 보안 팀/서비스 데스크 시스템에 경고를 보내 티켓을 엽니다. [Managed Instance 위협 탐지](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection)에 대해 자세히 알아보세요.

     ![Managed Instance 보안 - 위협 탐지](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

VM 보안 사례에 대한 자세한 내용은 [Azure의 IaaS 작업에 대한 보안 모범 사례](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms)를 참조하세요.

### <a name="bcdr"></a>BCDR

BCDR(비즈니스 연속성 및 재해 복구)을 위해 Contoso는 다음 작업을 수행합니다.

- 데이터를 안전하게 유지: Contoso는 Azure Backup 서비스를 사용하여 VM의 데이터를 백업합니다. [자세히 알아보기](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- 앱 가동 및 실행 유지: Contoso는 Site Recovery를 사용하여 Azure의 앱 VM을 보조 지역에 복제합니다. [자세히 알아보기](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).
- Contoso는 [데이터베이스 백업](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)을 포함하여 SQL Managed Instance 관리에 대해 자세히 알아봅니다.


### <a name="licensing-and-cost-optimization"></a>라이선스 및 비용 최적화

- Contoso에는 WEBVM에 대한 기존 라이선스가 있습니다. Azure 하이브리드 혜택이 있는 가격 책정을 활용하기 위해 Contoso는 기존 Azure VM을 변환합니다.
- Contoso는 Microsoft 자회사인 Cloudyn에서 허가된 Azure Cost Management를 사용하도록 설정합니다. Cost Management는 Contoso에서 Azure 및 다른 클라우드 리소스를 사용하고 관리하는 데 도움이 되는 다중 클라우드 비용 관리 솔루션입니다. [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview)에 대해 자세히 알아보세요. 


## <a name="conclusion"></a>결론

이 문서에서는 Contoso에서 Site Recovery 서비스를 통해 앱 프런트 엔드 VM을 Azure로 마이그레이션하여 Azure에서 SmartHotel360 앱을 다시 호스팅했습니다. Contoso는 Azure Database Migration Service를 사용하여 온-프레미스 데이터베이스를 Azure SQL Database Migration Instance로 마이그레이션합니다.

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 문서에서는 Contoso에서 Azure Site Recovery 서비스를 사용하여 [SmartHotel360 앱을 Azure VM에 다시 호스팅](contoso-migration-rehost-vm.md)합니다.

