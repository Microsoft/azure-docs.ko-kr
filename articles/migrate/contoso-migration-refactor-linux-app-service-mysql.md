---
title: Contoso Linux 서비스 데스크 앱을 Azure App Service 및 Azure MySQL에 리팩터링 | Microsoft Docs
description: Contoso가 온-프레미스 Linux 앱을 Azure App Service(웹 계층용 GitHub 사용)와 Azure SQL Database에 마이그레이션하여 리팩터링하는 방법을 알아봅니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 4ff3f129838a43bd7684dc10e1653dab969e9c1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60669776"
---
# <a name="contoso-migration-refactor-a-contoso-linux-service-desk-app-to-multiple-regions-with-azure-app-service-traffic-manager-and-azure-mysql"></a>Contoso 마이그레이션: Azure App Service, Traffic Manager 및 Azure MySQL을 사용하여 Contoso Linux 서비스 데스크 앱을 여러 지역에 리팩터링

이 문서에서는 Contoso가 온-프레미스 2계층 Linux 서비스 데스크 앱(osTicket)을 Azure App Service(GitHub 통합을 통해) 및 Azure MySQL로 마이그레이션하여 리팩터링하는 방법을 보여줍니다.

이 문서는 가상 회사 Contoso가 온-프레미스 리소스를 Microsoft Azure 클라우드로 마이그레이션하는 방법을 보여주는 문서 시리즈 중 하나입니다. 시리즈에는 배경 정보 및 마이그레이션 인프라를 설정하고 다양한 유형의 마이그레이션을 실행하는 방법을 보여 주는 시나리오가 포함되어 있습니다. 시나리오가 복잡해지고 있으며, 앞으로 계속해서 문서가 추가될 것입니다.

**문서** | **세부 정보** | **상태**
--- | --- | ---
[문서 1: 개요](contoso-migration-overview.md) | 문서 시리즈, Contoso의 마이그레이션 전략 및 시리즈에서 사용되는 샘플 앱에 대해 간략히 설명합니다. | 사용 가능
[문서 2: Azure 인프라 배포](contoso-migration-infrastructure.md) | Contoso에서 마이그레이션을 위해 온-프레미스 인프라와 Azure 인프라를 준비합니다. 이 시리즈의 모든 마이그레이션 관련 문서에서 동일한 인프라가 사용됩니다. | 사용 가능
[문서 3: Azure로 마이그레이션하기 위한 온-프레미스 리소스 평가](contoso-migration-assessment.md)  | Contoso가 VMware에서 실행되는 온-프레미스 SmartHotel360 앱의 평가를 실행합니다. Contoso에서 Azure Migrate 서비스를 사용하여 앱 VM을 평가하고, Database Migration Assistant를 사용하여 앱 SQL Server 데이터베이스를 평가합니다. | 사용 가능
[문서 4: Azure VM 및 SQL Database Managed Instance에서 앱 다시 호스트](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso가 온-프레미스 SmartHotel360 앱을 Azure로 리프트 앤 시프트 방식으로 마이그레이션합니다. Contoso에서 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하여 앱 프런트 엔드 VM을 마이그레이션하고, [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)를 사용하여 앱 데이터베이스를 Azure SQL Database Managed Instance로 마이그레이션합니다. | 사용 가능   
[문서 5: Azure VM에서 앱 다시 호스트](contoso-migration-rehost-vm.md) | Contoso에서 Site Recovery 서비스를 사용하여 SmartHotel360 앱 VM을 Azure VM으로 마이그레이션합니다. | 사용 가능
[문서 6: Azure VM 및 SQL Server AlwaysOn 가용성 그룹에서 앱 다시 호스트](contoso-migration-rehost-vm-sql-ag.md) | Contoso가 SmartHotel360 앱을 마이그레이션합니다. Contoso에서 Site Recovery를 사용하여 앱 VM을 마이그레이션하고, Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹으로 보호되는 SQL Server 클러스터로 마이그레이션합니다. | 사용 가능 
[문서 7: Azure VM에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm.md) | Contoso에서 Azure Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 리프트 앤 시프트 방식으로 마이그레이션합니다. | 사용 가능
[문서 8: Azure VM 및 Azure MySQL에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm-mysql.md) | Contoso에서 Azure Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 마이그레이션하고, MySQL Workbench를 사용하여 앱 데이터베이스를 Azure MySQL 서버 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 9: Azure Web Apps 및 Azure SQL Database에서 앱 리팩터링](contoso-migration-refactor-web-app-sql.md) | Contoso에서 SmartHotel360 앱을 Azure Web App으로 마이그레이션하고, Database Migration Assistant를 사용하여 앱 데이터베이스를 Azure SQL Server 인스턴스로 마이그레이션합니다. | 사용 가능
문서 10: Azure Web Apps 및 Azure MySQL에서 Linux 앱 리팩터링 | Contoso에서 지속적인 업데이트를 위해 GitHub와 통합된 Azure Traffic Manager를 사용하여 Linux osTicket 앱을 여러 Azure 지역의 Azure 웹앱으로 마이그레이션합니다. Contoso에서 앱 데이터베이스를 Azure Database for MySQL 인스턴스로 마이그레이션합니다. | 이 문서의 내용
[문서 11: Azure DevOps Services에서 TFS 리팩터링](contoso-migration-tfs-vsts.md) | Contoso에서 온-프레미스 Team Foundation Server 배포를 Azure의 Azure DevOps Services로 마이그레이션합니다. | 사용 가능
[문서 12: Azure 컨테이너 및 Azure SQL Database에서 앱 재설계](contoso-migration-rearchitect-container-sql.md) | Contoso에서 SmartHotel 앱을 Azure로 마이그레이션합니다. 그런 다음, 웹앱 계층을 Azure Service Fabric에서 실행되는 Windows 컨테이너로 재설계하고 Azure SQL Database를 사용하여 데이터베이스를 재설계합니다. | 사용 가능
[문서 13: Azure에서 앱 다시 빌드](contoso-migration-rebuild.md) | Contoso가 다양한 Azure 기능과 서비스(Azure App Service, AKS(Azure Kubernetes Service), Azure Functions, Azure Cognitive Services 및 Azure Cosmos DB 포함)를 사용하여 SmartHotel360 앱을 다시 빌드합니다. | 사용 가능
[문서 14: Azure로의 마이그레이션 확장](contoso-migration-scale.md) | 마이그레이션 조합을 시도한 후 Contoso는 Azure로 전체 마이그레이션을 확장할 준비를 합니다. | 사용 가능

이 문서에서 Contoso는 2계층 Linux Apache MySQL PHP(LAMP) 서비스 데스크 앱(osTicket)을 Azure로 마이그레이션합니다. 이 오픈 소스 앱을 사용하려면 [GitHub](https://github.com/osTicket/osTicket)에서 다운로드할 수 있습니다.


## <a name="business-drivers"></a>비즈니스 영향 요소

IT 리더십 팀은 비즈니스 파트너와의 긴밀한 협력을 통해 다음과 같이 기업이 달성하고자 하는 바를 잘 이해하고 있습니다.

- **비즈니스 성장 해결**: Contoso는 성장하면서 새로운 시장에 진입하고 있습니다. 고객 서비스 에이전트가 추가로 필요합니다. 
- **크기 조정**: 비즈니스 확장으로 인해 Contoso가 고객 서비스 에이전트를 더 많이 추가할 수 있도록 솔루션을 구축해야 합니다.
- **복원력 향상**:  과거에는 시스템 문제가 내부 사용자에게만 영향을 미쳤습니다. 새로운 비즈니스 모델에서는 외부 사용자가 영향을 받기 때문에 Contoso는 앱이 항상 실행되도록 해야 합니다.

## <a name="migration-goals"></a>마이그레이션 목표

Contoso 클라우드 팀은 최상의 마이그레이션 방법을 확인하기 위해 이 마이그레이션의 목표를 정했습니다.

- 현재 온-프레미스 용량 및 성능을 능가하도록 애플리케이션을 확장해야 합니다.  Contoso는 Azure의 주문형 크기 조정 기능을 활용하기 위해 애플리케이션을 옮기고 있습니다.
- Contoso는 앱 코드 기반을 지속적인 업데이트 파이프라인으로 이동하려고 합니다.  앱 변경 내용이 GitHub에 푸시될 때, Contoso는 운영 담당자가 작업을 수행하지 않고 이러한 변경 사항을 배포하려고 합니다.
- 애플리케이션은 확장 및 장애 조치(failover) 기능과 함께 복원력이 있어야 합니다. Contoso는 서로 다른 Azure 지역 두 곳에 앱을 배포하고 자동으로 규모가 조정되도록 설정하려고 합니다.
- Contoso는 앱이 클라우드로 이동되면 데이터베이스 관리 작업을 최소화하려고 합니다.

## <a name="solution-design"></a>솔루션 디자인
해당 목표 및 요구 사항을 고정한 후 Contoso는 배포 솔루션을 디자인 및 검토하고, 마이그레이션에 사용할 Azure 서비스를 포함한 마이그레이션 프로세스를 식별합니다.


## <a name="current-architecture"></a>현재 아키텍처

- 앱이 두 개의 VM인 OSTICKETWEB 및 OSTICKETMYSQL에 계층화되어 있습니다.
- VM은 VMware ESXi 호스트 **contosohost1.contoso.com**(버전 6.5)에 있습니다.
- VMware 환경은 VM에서 실행되는 vCenter Server 6.5(**vcenter.contoso.com**)에서 관리합니다.
- Contoso는 온-프레미스 데이터 센터(contoso-datacenter)와 온-프레미스 도메인 컨트롤러(**contosodc1**)를 갖고 있습니다.

![현재 아키텍처](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png) 


## <a name="proposed-architecture"></a>제안된 아키텍처

다음은 제안된 아키텍처입니다.

- OSTICKETWEB의 웹 계층 앱은 Azure 지역 두 곳에 Azure App Service를 구축하여 마이그레이션됩니다. Linux용 Azure App Service가 PHP 7.0 Docker 컨테이너를 사용하여 구현됩니다.
- 앱 코드는 GitHub로 옮겨지고, Azure Web App은 GitHub를 통해 지속적으로 업데이트되도록 구성됩니다.
- Azure App Servers가 주(미국 동부 2) 지역 및 보조(미국 중부) 지역 모두에 배포됩니다.
- 두 지역에 있는 두 App Service Web Apps 앞에 Traffic Manager가 설치됩니다.
- 트래픽이 미국 동부 2를 통과하도록 강제 적용하기 위해 Traffic Manager가 우선 순위 모드로 구성됩니다.
- 미국 동부 2에 있는 Azure 앱 서버가 오프라인이 되면 사용자는 미국 중부에 있는 장애 조치(failover)된 앱에 액세스할 수 있습니다.
- 앱 데이터베이스가 MySQL Workbench 도구를 사용하여 Azure MySQL PaaS 서비스로 마이그레이션됩니다. 온-프레미스 데이터베이스가 로컬에 백업되고 Azure MySQL에 직접 복원됩니다.
- 데이터베이스는 주 미국 동부 2 지역의 프로덕션 네트워크(VNET-PROD-EUS2)에 있는 데이터베이스 서브넷(PROD-DB-EUS2)에 상주합니다.
- 프로덕션 워크로드를 마이그레이션하기 때문에, 앱에 대한 Azure 리소스가 프로덕션 리소스 그룹 **ContosoRG**에 상주합니다.
- Traffic Manager는 Contoso의 인프라 리소스 그룹인 **ContosoInfraRG**에 배포됩니다.
- 마이그레이션이 완료되면 Contoso 데이터 센터의 온-프레미스 VM은 서비스 해제됩니다.


![시나리오 아키텍처](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png) 


## <a name="migration-process"></a>마이그레이션 프로세스

Contoso는 다음과 같이 마이그레이션 프로세스를 완료합니다.

1. 첫 단계에서 Contoso 관리자는 Azure App Services 프로비전, Traffic Manager 설정 및 Azure MySQL 인스턴스 프로비전을 비롯한 Azure 인프라를 설정합니다.
2. Azure가 준비되면 MySQL Workbench를 사용하여 데이터베이스를 마이그레이션합니다. 
3. 데이터베이스가 Azure에서 실행되면, Azure App Service에 대한 GitHub 개인 리포지토리를 지속적인 업데이트로 설정하고 osTicket 앱을 통해 로드합니다.
4. Azure Portal에서 GitHub의 앱을 Azure App Service를 실행하는 Docker 컨테이너에 로드합니다. 
5. DNS 설정을 조정하고 앱에 대한 자동 크기 조정을 구성합니다.

![마이그레이션 프로세스](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure 서비스

**서비스** | **설명** | **비용**
--- | --- | ---
[Azure App Service](https://azure.microsoft.com/services/app-service/) | 이 서비스는 웹 사이트에 대해 Azure PaaS 서비스를 사용하여 애플리케이션을 실행하고 규모를 조정합니다.  | 인스턴스의 크기와 필요한 기능에 따라 가격이 책정됩니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/app-service/windows/).
[Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) | DNS를 사용하여 사용자를 Azure 또는 외부 웹 사이트 및 서비스로 보내는 부하 분산 장치입니다. | 수신한 DNS 쿼리의 수와 모니터링되는 엔드포인트 수에 따라 가격이 책정됩니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/traffic-manager/).
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | 데이터베이스는 오픈 소스 MySQL 서버 엔진을 기반으로 합니다. 완전히 관리되는 엔터프라이즈용 커뮤니티 MySQL 데이터베이스를 앱 개발 및 배포용 서비스로 제공합니다. | 계산, 스토리지 및 백업 요구 사항에 따라 가격이 책정됩니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/mysql/).

 
## <a name="prerequisites"></a>필수 조건

이 시나리오를 실행하기 위해 Contoso에 필요한 항목은 다음과 같습니다.

**요구 사항** | **세부 정보**
--- | ---
**Azure 구독** | Contoso는 이 문서 시리즈의 앞에서 구독을 만들었습니다. Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.<br/><br/> 체험 계정을 만들면 구독 관리자로서 모든 작업을 수행할 수 있습니다.<br/><br/> 기존 구독을 사용하고 관리자가 아닌 경우 관리자와 협력하여 소유자 또는 기여자 권한을 할당받아야 합니다. 
**Azure 인프라** | Contoso는 [마이그레이션을 위한 Azure 인프라](contoso-migration-infrastructure.md)에 설명된 대로 Azure 인프라를 설정합니다.



## <a name="scenario-steps"></a>시나리오 단계

Contoso에서 마이그레이션을 수행하는 방법은 다음과 같습니다.

> [!div class="checklist"]
> * **1단계: Azure App Services 프로비전**: Contoso 관리자는 주 지역 및 보조 지역에 Web Apps를 프로비전합니다.
> * **2단계: Traffic Manager 설정**: 라우팅 및 부하 분산 트래픽을 위해 Web Apps 앞에 Traffic Manager를 설정합니다.
> * **3단계: MySQL 프로비전**: Azure에서 Azure MySQL 데이터베이스 인스턴스를 프로비전합니다.
> * **4단계: 데이터베이스 마이그레이션**: MySQL Workbench를 사용하여 데이터베이스를 마이그레이션합니다. 
> * **5단계: GitHub 설정**: 앱 웹 사이트/코드용 로컬 GitHub 리포지토리를 설정합니다.
> * **6단계: 웹앱 배포**: GitHub에서 웹앱을 배포합니다.




## <a name="step-1-provision-azure-app-services"></a>1단계: Azure App Services 프로비전

Contoso 관리자는 Azure App Services를 사용하여 Web Apps를 두 개(지역마다 하나씩) 프로비전합니다.

1. Azure Marketplace에서 주 미국 동부 2 지역(**osticket-eus2**)에 웹앱 리소스를 만듭니다.
2. 리소스를 프로덕션 리소스 그룹인 **ContosoRG**에 배치합니다.

    ![Azure 앱](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png) 

3. 표준 크기를 사용하여 주 지역(**APP-SVP-EUS2**)에 App Service 계획을 새로 만듭니다.

     ![Azure 앱](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png) 
    
4. Docker 컨테이너인 PHP 7.0 런타임 스택을 사용하여 Linux OS를 선택합니다.

    ![Azure 앱](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png) 

5. 미국 중부 지역에 대한 두 번째 웹앱(**osticket-cus**)과 App Service 계획을 만듭니다.

    ![Azure 앱](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png) 


**도움이 더 필요하세요?**

- [Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview)에 대해 알아봅니다.
- [Linux의 Azure App Service](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)에 대해 알아봅니다.


## <a name="step-2-set-up-traffic-manager"></a>2단계: Traffic Manager 설정

Contoso 관리자는 인바운드 웹 요청을 osTicket 웹 계층에서 실행 중인 Web Apps로 보내도록 Traffic Manager를 설정합니다.

1. Azure Marketplace에서 Traffic Manager 리소스(**osticket.trafficmanager.net**)를 만듭니다. 미국 동부 2가 주 사이트가 되도록 우선 순위 라우팅을 사용합니다. 리소스는 자체 인프라 리소스 그룹(**ContosoInfraRG**)에 배치합니다. Traffic Manager는 전역을 대상으로 하며 특정 위치에 바인딩되지 않습니다.

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png) 

2. 이제 Traffic Manager에 엔드포인트를 구성합니다. 미국 동부 2 웹앱을 주 사이트(**osticket-eus2**)로, 미국 중부 앱을 보조 사이트(**osticket-cus**)로 추가합니다.

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png) 

3. 엔드포인트를 추가한 후에는 모니터링이 가능합니다.

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**도움이 더 필요하세요?**

- [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)에 대해 알아봅니다.
- [우선 순위 엔드포인트로 트래픽 라우팅](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method)에 대해 알아봅니다.
 
## <a name="step-3-provision-azure-database-for-mysql"></a>3단계: Azure Database for MySQL 프로비전

Contoso 관리자는 주 지역인 미국 동부 2 지역에 MySQL 데이터베이스 인스턴스를 프로비전합니다.

1. Azure Portal에서 Azure Database for MySQL 리소스를 만듭니다. 

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. Azure 데이터베이스에 대해 **contosoosticket** 이름을 추가합니다. 프로덕션 리소스 그룹 **ContosoRG**에 데이터베이스를 추가하고 해당 자격 증명을 지정합니다.
3. 온-프레미스 MySQL 데이터베이스가 버전 5.7이므로 호환성을 위해 이 버전을 선택합니다. 데이터베이스 요구 사항과 일치하는 기본 크기를 사용합니다.

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. **백업 중복성 옵션**에서 **지역 중복**을 사용하도록 선택합니다. 이 옵션을 사용하면 가동이 중단될 경우 보조 지역인 미국 중부 지역의 데이터베이스를 복원할 수 있습니다. 데이터베이스를 프로비전하는 경우에만 이 옵션을 구성할 수 있습니다.

    ![중복](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

4. 연결 보안을 설정합니다. 데이터베이스 > **연결 보안**에서 데이터베이스가 Azure 서비스에 액세스할 수 있도록 방화벽 규칙을 설정합니다.
5. 시작 및 끝 IP 주소에 로컬 워크스테이션 클라이언트 IP 주소를 추가합니다. 이렇게 하면 웹앱이 마이그레이션을 수행하는 데이터베이스 클라이언트와 함께 MySQL 데이터베이스에 액세스할 수 있습니다.

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)



## <a name="step-4-migrate-the-database"></a>4단계: 데이터베이스 마이그레이션

Contoso 관리자는 MySQL 도구를 통해 백업 및 복원을 사용하여 데이터베이스를 마이그레이션합니다. MySQL Workbench를 설치하고, OSTICKETMYSQL에서 데이터베이스를 백업한 다음, Azure Database for MySQL 서버에 복원합니다.

### <a name="install-mysql-workbench"></a>MySQL Workbench 설치

1. [필수 조건을 확인하고 MySQL Workbench를 다운로드](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool)합니다.
2. [설치 지침](https://dev.mysql.com/doc/workbench/en/wb-installing.html)에 따라 Windows용 MySQL Workbench를 설치합니다. 프로그램이 설치되는 머신은 인터넷을 통해 OSTICKETMYSQL VM과 Azure에 액세스할 수 있어야 합니다.
3. MySQL Workbench에서 OSTICKETMYSQL에 대한 MySQL 연결을 만듭니다. 

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. 데이터베이스를 **osticket**으로 로컬 자체 포함 파일에 내보냅니다.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. 데이터베이스가 로컬에 백업된 후 Azure Database for MySQL 인스턴스에 대한 연결을 만듭니다.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. 이제 자체 포함 파일에서 Azure MySQL 인스턴스의 데이터베이스를 가져올(복원) 수 있습니다. 인스턴스에 대해 새 스키마(osticket)가 생성됩니다.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. 데이터가 복원되면 Workbench를 사용하여 이 데이터를 쿼리할 수 있고 Azure Portal에도 나타납니다.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. 마지막으로 웹앱에서 데이터베이스 정보를 업데이트해야 합니다. MySQL 인스턴스에서 **연결 문자열**을 엽니다. 

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. 문자열 목록에서 웹앱 설정을 찾아서 클릭하여 복사합니다.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. 메모장을 열어서 새 파일에 문자열을 붙여넣은 다음, osticket 데이터베이스, MySQL 인스턴스 및 자격 증명 설정과 일치하도록 업데이트합니다.

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Azure Portal의 MySQL 인스턴스의 **개요**에서 서버 이름과 로그인 정보를 확인할 수 있습니다.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)


## <a name="step-5-set-up-github"></a>5단계: GitHub 설정

Contoso 관리자는 개인 GitHub 리포지토리를 새로 만들어서 Azure MySQL의 osTicket 데이터베이스에 대한 연결을 설정합니다. 그런 다음, 앱을 사용하여 Azure 웹앱을 로드합니다.  

1.  OsTicket 소프트웨어 공용 GitHub 리포지토리로 이동하여 Contoso GitHub 계정에 포크합니다.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. 포크를 수행한 후 **include** 폴더로 이동하여 **ost-config.php** 파일을 찾습니다.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)


3. 브라우저에서 파일이 열리면 파일을 편집합니다.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. 편집기에서 데이터베이스 세부 정보 특히, **DBHOST**와 **DBUSER**를 업데이트합니다. 

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. 그런 다음, 변경 내용을 커밋합니다.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. 각 웹앱(**osticket-eus2** 및 **osticket-cus**)에 대해 Azure Portal에서 **애플리케이션 설정**을 수정합니다.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. **osticket**이라는 이름의 연결 문자열을 입력하고 이 문자열을 메모장에서 **값 영역**에 붙여넣습니다. 문자열 옆의 드롭다운 목록에서 **MySQL**을 선택하고 설정을 저장합니다.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>6단계: Web Apps 구성

마이그레이션 프로세스의 마지막 단계로, Contoso 관리자는 osTicket 웹 사이트를 사용하여 웹앱을 구성합니다.



1. 주 웹앱(**osticket-eus2**)에서 **배포 옵션**을 열고 소스를 **GitHub**로 설정합니다.

    ![앱 구성](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. 배포 옵션을 선택합니다.

    ![앱 구성](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. 옵션을 설정하면 구성이 Azure Portal에 보류 중으로 표시됩니다.

    ![앱 구성](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. 구성이 업데이트되고 osTicket 웹앱이 GitHub에서 Azure App Service를 실행 중인 Docket 컨테이너로 로드되면 사이트가 활성으로 표시됩니다.

    ![앱 구성](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. 보조 웹앱(**osticket-cus**)에 대해 위의 단계를 반복합니다.
6. 사이트가 구성된 후에는 Traffic Manager 프로필을 통해 사이트에 액세스할 수 있습니다. DNS 이름은 osTicket 앱의 새로운 위치입니다. [자세히 알아보기](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

    ![앱 구성](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)
    
7. Contoso는 기억하기 쉬운 DNS 이름을 원합니다. Traffic Manager 이름을 가리키는 별칭 레코드(CNAME) **osticket.contoso.com**을 DNS 또는 자체 도메인 컨트롤러에 만듭니다.

    ![앱 구성](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. 앱을 사용자 지정 호스트 이름을 허용하도록 **osticket-eus2**와 **osticket-cus** 웹앱을 모두 구성합니다.

    ![앱 구성](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>자동 크기 조정 설정

마지막으로 앱에 대한 자동 크기 조정을 설정합니다. 이렇게 하면 에이전트가 앱을 사용하는 동안 비즈니스 요구에 따라 앱 인스턴스가 증가되고 감소됩니다. 

1. App Service **APP-SRV-EUS2**에서 **배율 단위**를 엽니다.
2. 현재 인스턴스에 대한 CPU 백분율이 10분 동안 70%를 초과하면 인스턴스 수를 하나씩 증가시키는 단일 규칙을 사용하여 자동 크기 조정 설정을 새로 구성합니다.

    ![자동 크기 조정](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. **APP-SRV-CUS**에 동일한 설정을 구성하여 앱이 보조 지역으로 장애 조치(failover)되면 동일한 동작이 적용되도록 합니다. 유일한 차이는 인스턴스 제한을 1로 설정하는 것입니다. 보조 지역은 장애 조치(failover) 전용이기 때문입니다.

   ![자동 크기 조정](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

##  <a name="clean-up-after-migration"></a>마이그레이션 후 정리

마이그레이션이 완료되면 osTicket 앱은 개인 GitHub 리포지토리를 사용하여 지속적인 업데이트를 통해 Azure 웹앱에서 실행되도록 리팩토링됩니다. 앱은 복원력을 높이기 위해 두 지역에서 실행됩니다. osTicket 데이터베이스는 PaaS 플랫폼으로 마이그레이션된 후 Azure Database for MySQL에서 실행됩니다.

정리를 위해 Contoso는 다음과 같은 작업을 수행해야 합니다. 
- vCenter 인벤토리에서 VMware VM을 제거합니다.
- 로컬 백업 작업에서 온-프레미스 VM을 제거합니다.
- 업데이트 내부 문서에 새 위치 및 IP 주소가 표시됩니다. 
- 온-프레미스 VM과 상호 작용하는 모든 리소스를 검토하고 모든 관련 설정이나 문서를 업데이트하여 새 구성을 반영합니다.
- osticket-trafficmanager.net URL을 가리키도록 모니터링을 재구성하여 앱이 실행되고 있는지 추적합니다.

## <a name="review-the-deployment"></a>배포 검토

앱이 현재 실행되고 있으므로 Contoso는 새 인프라를 완전히 운용하고 보안을 유지해야 합니다.

### <a name="security"></a>보안

Contoso 보안 팀에서 보안 문제가 있는지 확인하기 위해 앱을 검토했습니다. osTicket 앱과 MySQL 데이터베이스 인스턴스 간의 통신이 SSL에 대해 구성되지 않은 것을 파악했습니다. 데이터베이스 트래픽을 해킹할 수 없도록 하려면 이 작업을 수행해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/mysql/howto-configure-ssl).

### <a name="backups"></a>Backup

- osTicket 웹앱에는 상태 데이터가 없기 때문에 백업할 필요가 없습니다.
- 데이터베이스에 대한 백업을 구성할 필요가 없습니다. Azure Database for MySQL에서 자동으로 서버 백업을 만들고 저장합니다. 데이터베이스에 대해 지리적 중복을 사용하도록 선택했으므로 복원력이 높고 프로덕션 준비가 완료되었습니다. 백업을 사용하여 특정 시점의 서버를 복원할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/mysql/concepts-backup).


### <a name="licensing-and-cost-optimization"></a>라이선스 및 비용 최적화

- PaaS 배포에 대한 라이선스 문제는 없습니다.
- Contoso는 Microsoft 자회사인 Cloudyn에서 허가된 Azure Cost Management를 사용하도록 설정합니다. Azure 및 기타 클라우드 리소스를 활용하고 관리하는 데 도움이 되는 다중 클라우드 비용 관리 솔루션입니다.  Azure Cost Management에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/cost-management/overview).



