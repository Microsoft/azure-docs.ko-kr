---
title: Contoso가 Azure로 마이그레이션하기 위한 온-프레미스 워크로드 평가 | Microsoft Docs
description: Contoso에서 Azure Migrate 및 Data Migration Assistant를 사용하여 온-프레미스 머신을 Azure로 마이그레이션하는 방법을 평가하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: raynew
ms.openlocfilehash: 4739308d301291bf88e8ae547ba85f9648339c4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680939"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Contoso 마이그레이션: Azure로의 마이그레이션을 위한 온-프레미스 워크로드 평가

이 문서에서는 Contoso에서 Azure로 마이그레이션하기 위한 온-프레미스 SmartHotel360 앱을 평가합니다.

이 문서는 가상의 회사인 Contoso에서 온-프레미스 리소스를 Microsoft Azure 클라우드로 마이그레이션하는 방법을 설명하는 문서 시리즈 중 하나입니다. 이 시리즈에는 배경 정보와 마이그레이션 인프라를 설정하고, 마이그레이션에 대한 온-프레미스 리소스의 적합성을 평가하고, 다양한 유형의 마이그레이션을 실행하는 방법을 설명하는 자세한 배포 시나리오가 포함되어 있습니다. 시나리오가 복잡해지고 있으며, 시간이 지남에 따라 관련 문서가 시리즈에 추가됩니다.

문서 | 세부 정보 | 상태
--- | --- | ---
[문서 1: 개요](contoso-migration-overview.md) | 문서 시리즈, Contoso의 마이그레이션 전략 및 시리즈에서 사용되는 샘플 앱에 대해 간략히 설명합니다. | 사용 가능
[문서 2: Azure 인프라 배포](contoso-migration-infrastructure.md) | Contoso에서 마이그레이션을 위해 온-프레미스 인프라와 Azure 인프라를 준비합니다. 이 시리즈의 모든 문서에서 동일한 인프라가 사용됩니다. | 사용 가능
문서 3: Azure로 마이그레이션하기 위한 온-프레미스 리소스 평가 | Contoso가 VMware에서 실행되는 온-프레미스 SmartHotel360 앱의 평가를 실행합니다. Contoso에서 Azure Migrate 서비스를 사용하여 앱 VM을 평가하고, Database Migration Assistant를 사용하여 앱 SQL Server 데이터베이스를 평가합니다. | 이 문서의 내용
[문서 4: Azure VM 및 SQL Database Managed Instance에서 앱 다시 호스트](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso가 온-프레미스 SmartHotel360 앱을 Azure로 리프트 앤 시프트 방식으로 마이그레이션합니다. Azure Site Recovery 서비스를 사용하여 앱 프런트 엔드를 마이그레이션하고, Azure Database Migration Service를 사용하여 앱 데이터베이스를 Azure SQL Database Managed Instance로 마이그레이션합니다. | 사용 가능
[문서 5: Azure VM에서 앱 다시 호스트](contoso-migration-rehost-vm.md) | Contoso에서 Site Recovery 서비스를 사용하여 SmartHotel360 앱 VM을 Azure VM으로 마이그레이션합니다. | 사용 가능
[문서 6: Azure VM 및 SQL Server AlwaysOn 가용성 그룹에서 앱 다시 호스트](contoso-migration-rehost-vm-sql-ag.md) | Contoso에서 Site Recovery를 사용하여 SmartHotel360 앱을 마이그레이션하여 앱 VM을 마이그레이션하고, Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹에서 보호되는 SQL Server 클러스터로 마이그레이션합니다. | 사용 가능
[문서 7: Azure VM에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm.md) | Contoso에서 Site Recovery 서비스를 사용하여 Linux osTicket 앱을 Azure VM으로 리프트 앤 시프트 방식으로 마이그레이션합니다. | 사용 가능
[문서 8: Azure VM 및 Azure Database for MySQL에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm-mysql.md) | Contoso에서 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 마이그레이션하고, MySQL Workbench를 사용하여 앱 데이터베이스를 Azure Database for MySQL로 마이그레이션합니다. | 사용 가능
[문서 9: Azure 웹앱 및 Azure SQL Database에서 앱 리팩터링](contoso-migration-refactor-web-app-sql.md) | Contoso에서 SmartHotel360 앱을 Azure Web App으로 마이그레이션하고, Database Migration Assistant를 사용하여 앱 데이터베이스를 Azure SQL Server 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 10: Azure 웹앱 및 Azure Database for MySQL에서 Linux 앱 리팩터링](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso에서 지속적인 업데이트를 위해 GitHub와 통합된 Azure Traffic Manager를 사용하여 Linux osTicket 앱을 여러 Azure 지역의 Azure 웹앱으로 마이그레이션합니다. Contoso에서 앱 데이터베이스를 Azure Database for MySQL 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 11: Azure DevOps Services에서 Team Foundation Server 리팩터링](contoso-migration-tfs-vsts.md) | Contoso에서 온-프레미스 Team Foundation Server 배포를 Azure의 Azure DevOps Services로 마이그레이션합니다. | 사용 가능
[문서 12: Azure 컨테이너 및 Azure SQL Database에서 앱 재설계](contoso-migration-rearchitect-container-sql.md) | Contoso에서 SmartHotel 앱을 Azure로 마이그레이션합니다. 그런 다음, 웹앱 계층을 Azure Service Fabric에서 실행되는 Windows 컨테이너로 재설계하고 Azure SQL Database를 사용하여 데이터베이스를 재설계합니다. | 사용 가능
[문서 13: Azure에서 앱 다시 빌드](contoso-migration-rebuild.md) | Contoso에서 다양한 Azure 기능과 서비스(Azure App Service, AKS(Azure Kubernetes Service), Azure Functions, Azure Cognitive Services 및 Azure Cosmos DB 포함)를 사용하여 SmartHotel 앱을 다시 빌드합니다. | 사용 가능
[문서 14: Azure로의 마이그레이션 확장](contoso-migration-scale.md) | 마이그레이션 조합을 시도한 후 Contoso는 Azure로 전체 마이그레이션을 확장할 준비를 합니다. | 사용 가능


## <a name="overview"></a>개요

Contoso는 Azure로 마이그레이션하도록 고려함에 따라 회사에서 기술 및 재무 평가를 실행하여 온-프레미스 워크로드가 클라우드로 마이그레이션하는 데 적합한지 여부를 결정하려고 합니다. 특히 Contoso 팀은 마이그레이션에 대한 머신 및 데이터베이스 호환성을 평가하려고 합니다. Azure에서 Contoso의 리소스를 실행하는 데 필요한 용량과 비용을 예측하려고 합니다.

Contoso는 관련 기술을 시작하고 더 잘 이해하기 위해 다음 표에 요약된 두 가지 온-프레미스 앱을 평가합니다. 회사에서 마이그레이션을 위해 앱을 다시 호스팅하고 리팩터링하는 마이그레이션 시나리오를 평가합니다. [Contoso 마이그레이션 개요](contoso-migration-overview.md)에서 다시 호스트 및 리팩터링에 대해 자세히 알아보세요.

앱 이름 | 플랫폼 | 앱 계층 | 세부 정보
--- | --- | --- | ---
SmartHotel360<br/><br/> (Contoso 여행 요구 사항 관리) | SQL Server 데이터베이스가 있는 Windows에서 실행됩니다. | 2계층 앱입니다. 한 VM(**WEBVM**)에서 프런트 엔드 ASP.NET 웹 사이트가 실행되고, 다른 VM(**SQLVM**)에서 SQL Server가 실행됩니다. | VM은 vCenter Server를 통해 관리되는 ESXi 호스트에서 실행되는 VMware입니다.<br/><br/> 샘플 앱은 [GitHub](https://github.com/Microsoft/SmartHotel360)에서 다운로드할 수 있습니다.
osTicket<br/><br/> (Contoso 서비스 데스크 앱) | MySQL PHP(LAMP)가 있는 Linux/Apache에서 실행됩니다. | 2계층 앱입니다. 한 VM(**OSTICKETWEB**)에서 프런트 엔드 PHP 웹 사이트가 실행되고, 다른 VM(**OSTICKETMYSQL**)에서 MySQL 데이터베이스가 실행됩니다. | 이 앱은 고객 서비스 앱에서 내부 직원 및 외부 고객의 문제를 추적하는 데 사용됩니다.<br/><br/> 샘플은 [GitHub](https://github.com/osTicket/osTicket)에서 다운로드할 수 있습니다.

## <a name="current-architecture"></a>현재 아키텍처

이 다이어그램에서는 현재의 Contoso 온-프레미스 인프라를 보여 줍니다.

![현재 Contoso 아키텍처](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso에는 하나의 주 데이터 센터가 있습니다. 데이터 센터는 미국 동부의 뉴욕시에 위치하고 있습니다.
- Contoso는 미국 전역에 세 개의 추가 지점을 갖추고 있습니다.
- 주 데이터 센터는 파이버 메트로 이더넷 연결(500Mbps)을 통해 인터넷에 연결됩니다.
- 각 지점은 IPSec VPN 터널을 통한 비즈니스 수준 연결을 주 데이터 센터에 다시 사용하여 인터넷에 로컬로 연결됩니다. 이렇게 설정하면 Contoso의 전체 네트워크가 영구적으로 연결되고 인터넷 연결을 최적화할 수 있습니다.
- 주 데이터 센터는 VMware를 통해 완벽하게 가상화됩니다. Contoso에는 vCenter Server 6.5에서 관리되는 두 개의 ESXi 6.5 가상화 호스트가 있습니다.
- Contoso는 ID 관리를 위해 Active Directory를 사용합니다. Contoso는 내부 네트워크에서 DNS 서버를 사용합니다.
- 데이터 센터의 도메인 컨트롤러는 VMware VM에서 실행됩니다. 지점의 도메인 컨트롤러는 실제 서버에서 실행됩니다.

## <a name="business-drivers"></a>비즈니스 영향 요소

Contoso의 IT 리더십 팀은 회사의 비즈니스 파트너와 긴밀히 협력하여 회사에서 이러한 마이그레이션을 통해 달성하려는 목표와 관련하여 다음과 같이 파악했습니다.

- **비즈니스 성장 해결**: Contoso는 성장하고 있습니다. 그 결과 회사의 온-프레미스 시스템과 인프라에 대한 압박이 커졌습니다.
- **효율성 증대**: Contoso는 불필요한 절차를 제거하고 개발자와 사용자를 위한 프로세스를 간소화해야 합니다. 고객의 요구 사항에 대해 서비스를 더 빠르게 제공할 수 있도록 회사에서 IT 속도를 높이고 시간이나 비용을 낭비하지 않도록 해야 합니다.
- **민첩성 향상**:  Contoso IT는 비즈니스 요구 사항에 대해 더 빠르게 대응해야 합니다. 글로벌 경제에서 성공하기 위해 회사에서 시장의 변화보다 더 빨리 대응할 수 있어야 합니다. Contoso의 IT는 사업상의 걸림돌이 되지 않아야 합니다.
- **크기 조정**: 회사의 비즈니스가 성공적으로 성장함에 따라 Contoso IT는 동일한 속도로 크기를 조정할 수 있는 시스템을 제공해야 합니다.

## <a name="assessment-goals"></a>평가 목표

Contoso 클라우드 팀은 마이그레이션 평가 목표를 확인했습니다.

- 마이그레이션한 후에 Azure의 앱은 Contoso의 온-프레미스 VMWare 환경에서 현재 확보하고 있는 앱과 동일한 성능 기능을 갖추어야 합니다. 클라우드로 전환한다고 해서 앱 성능이 덜 중요한 것은 아닙니다.
- Contoso는 앱 및 데이터베이스와 Azure 요구 사항의 호환성을 이해해야 합니다. Azure에서의 호스팅 옵션도 이해해야 합니다.
- 앱이 클라우드로 이동된 후에는 Contoso의 데이터베이스 관리가 최소화되어야 합니다.  
- Contoso는 마이그레이션 옵션뿐만 아니라 클라우드로 이동한 후의 인프라 관련 비용에 대해서도 이해하려고 합니다.

## <a name="assessment-tools"></a>평가 도구

Contoso는 마이그레이션 평가를 위해 Microsoft 도구를 사용합니다. 이 도구는 회사의 목표와 부합하며 Contoso에 필요한 모든 정보를 제공해야 합니다.

기술 | 설명 | 비용
--- | --- | ---
[데이터 Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso는 Data Migration Assistant를 사용하여 Azure의 데이터베이스 기능에 영향을 줄 수 있는 호환성 문제를 평가하고 검색합니다. Data Migration Assistant는 SQL 원본과 대상 간의 기능 패리티를 평가하고, 성능과 안정성을 향상시킬 수 있습니다. | Data Migration Assistant는 평가판으로 다운로드할 수 있는 도구입니다.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso는 Azure Migrate 서비스를 사용하여 VMware VM을 평가합니다. Azure Migrate는 머신의 마이그레이션 적합성을 평가하고, Azure에서 실행하기 위한 크기 조정 및 예상 비용을 제공합니다.  | Azure Migrate는 2018년 5월 현재 평가판 서비스입니다.
[서비스 맵](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate는 서비스 맵을 사용하여 회사에서 마이그레이션하려는 머신 간의 종속성을 보여 줍니다. | 서비스 맵은 Azure Monitor 로그의 일부입니다. 현재 Contoso는 요금을 부담하지 않고 180일 동안 서비스 맵을 사용할 수 있습니다.

이 시나리오에서 Contoso는 Data Migration Assistant를 다운로드하고 실행하여 여행 앱에 사용할 온-프레미스 SQL Server 데이터베이스를 평가합니다. Contoso는 Azure Migrate와 종속성 매핑을 사용하여 앱 VM을 평가한 후에 Azure로 마이그레이션합니다.

## <a name="assessment-architecture"></a>평가 아키텍처

![마이그레이션 평가 아키텍처를 설치합니다.](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso는 전형적인 기업 조직을 나타내는 가상의 이름입니다.
- Contoso에는 온-프레미스 데이터 센터(**contoso-datacenter**)와 온-프레미스 도메인 컨트롤러(**CONTOSODC1**, **CONTOSODC2**)가 있습니다.
- VMware VM은 6.5 버전을 실행하는 VMware ESXi 호스트(**contosohost1**, **contosohost2**)에 있습니다.
- VMware 환경은 VM에서 실행되는 vCenter Server 6.5(**vcenter.contoso.com**)에서 관리됩니다.
- SmartHotel360 여행 앱의 특징은 다음과 같습니다.
    - 앱이 두 개의 VMware VM(**WEBVM** 및 **SQLVM**)에 걸쳐 계층화되어 있습니다.
    - VM은 VMware ESXi 호스트 **contosohost1.contoso.com**에 있습니다.
    - VM은 Windows Server 2008 R2 Datacenter SP1을 실행하고 있습니다.
- VMware 환경은 VM에서 실행되는 VCenter Server(**vcenter.contoso.com**)를 통해 관리됩니다.
- osTicket 서비스 데스크 앱의 특징은 다음과 같습니다.
    - 앱이 두 개의 VM(**OSTICKETWEB** 및 **OSTICKETMYSQL**)에 걸쳐 계층화되어 있습니다.
    - VM은 Ubuntu Linux Server 16.04-LTS를 실행하고 있습니다.
    - **OSTICKETWEB**은 Apache 2 및 PHP 7.0을 실행하고 있습니다.
    - **OSTICKETMYSQL**은 MySQL 5.7.22를 실행하고 있습니다.

## <a name="prerequisites"></a>필수 조건

Contoso 및 다른 사용자는 평가를 위해 다음 필수 조건을 충족해야 합니다.

- Azure 구독 또는 Azure 구독의 리소스 그룹에 대한 소유자 또는 기여자 권한
- 6.5, 6.0 또는 5.5 버전을 실행하는 온-프레미스 vCenter Server
- vCenter Server의 읽기 전용 계정 또는 이러한 계정을 만들 수 있는 권한
- .ova 템플릿을 사용하여 vCenter Server 인스턴스에 VM을 만들 수 있는 권한
- 5.5 이상 버전을 실행하는 하나 이상의 ESXi 호스트
- 온-프레미스 VMware VM 두 대 이상(그 중 하나는 SQL Server 데이터베이스 실행).
- 각 VM에 Azure Migrate 에이전트를 설치할 권한.
- VM이 인터넷에 직접 연결되어야 합니다.  
    - 인터넷 액세스는 [필수 URL](https://docs.microsoft.com/azure/migrate/concepts-collector)로 제한할 수 있습니다.  
    - VM에 인터넷 연결이 없는 경우 Azure [Log Analytics Gateway](../azure-monitor/platform/gateway.md)를 설치하고, 이를 통해 에이전트 트래픽을 전달해야 합니다.
- SQL Server 인스턴스를 실행하는 VM의 FQDN(데이터베이스 평가용).
- SQL Server VM에서 실행되는 Windows 방화벽은 1433 TCP 포트(기본값)에서 외부 연결을 허용해야 합니다. 이 설정을 통해 Data Migration Assistant를 연결할 수 있습니다.

## <a name="assessment-overview"></a>평가 개요

Contoso에서 평가를 수행하는 방법은 다음과 같습니다.

> [!div class="checklist"]
> * **1단계: Data Migration Assistant 다운로드 및 설치**: Contoso는 온-프레미스 SQL Server 데이터베이스를 평가할 Data Migration Assistant를 준비합니다.
> * **2단계: Data Migration Assistant를 사용하여 데이터베이스 평가**: Contoso는 데이터베이스 평가를 실행하고 분석합니다.
> * **3단계: Azure Migrate를 사용하여 VM 평가 준비**: Contoso는 온-프레미스 계정을 설정하고 VMware 설정을 조정합니다.
> * **4단계: Azure Migrate를 사용하여 온-프레미스 VM 검색**: Contoso는 Azure Migrate 수집기 VM을 만듭니다. 그런 다음, Contoso는 수집기를 실행하여 평가할 VM을 검색합니다.
> * **5단계: Azure Migrate를 사용하여 종속성 분석 준비**: Contoso는 회사가 VM 간의 종속성 매핑을 볼 수 있도록 VM에 Azure Migrate 에이전트를 설치합니다.
> * **6단계: Azure Migrate를 사용하여 VM 평가**: Contoso는 종속성을 확인하고, VM을 그룹화하고, 평가를 실행합니다. 평가할 준비가 되면 Contoso는 마이그레이션을 준비하기 위해 평가를 분석합니다.

## <a name="step-1-download-and-install-data-migration-assistant"></a>1단계: Data Migration Assistant 다운로드 및 설치

1. Contoso는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53595)에서 Data Migration Assistant를 다운로드합니다.
    - Data Migration Assistant는 SQL Server 인스턴스에 연결할 수 있는 모든 머신에 설치할 수 있습니다. Contoso는 SQL Server 머신에서 이 도구를 실행할 필요가 없습니다.
    - Data Migration Assistant는 SQL Server 호스트 머신에서 실행하면 안됩니다.
2. Contoso는 다운로드한 설치 파일(DownloadMigrationAssistant.msi)을 실행하여 설치를 시작합니다.
3. Contoso는 **마침** 페이지에서 **Microsoft Data Migration Assistant 시작**을 선택한 후에 마법사를 끝냅니다.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel360"></a>2단계: SmartHotel360에 대한 데이터베이스 평가 실행 및 분석

이제 Contoso는 평가를 실행하여 SmartHotel360 앱용 온-프레미스 SQL Server 데이터베이스를 분석할 수 있습니다.

1. Contoso는 Data Migration Assistant에서 **새로 만들기** > **평가**를 차례로 선택한 다음, 평가에 프로젝트 이름을 지정합니다.
2. Contoso는 **원본 서버 유형**으로 **Azure Virtual Machines의 SQL Server**를 선택합니다.

    ![Data Migration Assistant - 원본 선택](./media/contoso-migration-assessment/dma-assessment-1.png)

   > [!NOTE]
   >    Data Migration Assistant는 현재 Azure SQL Database Managed Instance로의 마이그레이션에 대한 평가를 지원하지 않습니다. 이 문제를 해결하기 위해 Contoso는 Azure VM의 SQL Server를 가상의 평가 대상으로 사용합니다.

3. Contoso는 **대상 버전 선택**에서 SQL Server 2017을 대상 버전으로 선택합니다. SQL Database Managed Instance에서 사용하는 버전이므로 Contoso는 이 버전을 선택해야 합니다.
4. Contoso는 호환성 및 새로운 기능에 대한 정보를 찾는 데 도움이 되는 보고서를 선택합니다.
   - **호환성 문제**는 마이그레이션을 중단시킬 수 있거나 마이그레이션하기 전에 약간의 조정이 필요한 변경 내용에 대해 알려줍니다. 이 보고서는 현재 사용 중이지만 더 이상 사용되지 않는 기능에 대해서도 Contoso에 계속 알려줍니다. 문제는 호환성 수준에 따라 구성됩니다.
   - **새 권장 기능**은 마이그레이션 후 데이터베이스에 사용할 수 있는 대상 SQL Server 플랫폼의 새 기능에 대해 알려줍니다. 새로운 기능 권장 사항은 **성능**, **보안**, 및 **저장소** 머리글로 구성됩니다.

     ![Data Migration Assistant - 호환성 문제 및 새로운 기능](./media/contoso-migration-assessment/dma-assessment-2.png)

2. Contoso는 **서버에 연결**에서 데이터베이스를 실행하는 VM의 이름과 이 데이터베이스에 액세스하기 위한 자격 증명을 입력합니다. Contoso는 VM에서 SQL Server에 액세스할 수 있도록 **서버 인증서 신뢰**를 선택합니다. 그런 다음, Contoso는 **연결**을 선택합니다.

    ![Data Migration Assistant - 서버에 연결](./media/contoso-migration-assessment/dma-assessment-3.png)

3. Contoso는 **원본 추가**에서 평가하려는 데이터베이스를 추가하고, **다음**을 선택하여 평가를 시작합니다.
4. 평가가 만들어집니다.

    ![Data Migration Assistant - 평가 만들기](./media/contoso-migration-assessment/dma-assessment-4.png)

5. Contoso는 **결과 검토**에서 평가 결과를 봅니다.

### <a name="analyze-the-database-assessment"></a>데이터베이스 평가 분석

결과를 사용할 수 있게 되는 즉시 결과가 표시됩니다. Contoso에서 문제를 해결하면 **평가 다시 시작**을 선택하여 평가를 다시 실행해야 합니다.

1. Contoso는 **호환성 문제** 보고서에서 각 호환성 수준에서 문제가 있는지 확인합니다. 호환성 수준은 다음과 같이 SQL Server 버전에 매핑됩니다.

   - 100: SQL Server 2008/Azure SQL Database
   - 110: SQL Server 2012/Azure SQL Database
   - 120: SQL Server 2014/Azure SQL Database
   - 130: SQL Server 2016/Azure SQL Database
   - 140: SQL Server 2017/Azure SQL Database

     ![Data Migration Assistant - 호환성 문제 보고서](./media/contoso-migration-assessment/dma-assessment-5.png)

2. Contoso는 **기능 권장 사항** 보고서에서 마이그레이션 후 평가에서 권장하는 성능, 보안 및 저장소 기능을 확인합니다. 메모리 내 OLTP 및 columnstore 인덱스, Stretch Database, Always Encrypted, 동적 데이터 마스킹, 투명한 데이터 암호화를 포함하여 다양한 기능이 권장됩니다.

    ![Data Migration Assistant - 기능 권장 사항 보고서](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Contoso는 모든 SQL Server 데이터베이스에 대해 [투명 데이터 암호화를 사용하도록 설정](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)해야 합니다. 이는 데이터베이스가 온-프레미스에서 호스팅되는 경우보다 클라우드에 있는 경우에 훨씬 더 중요합니다. 투명한 데이터 암호화는 마이그레이션 후에만 사용하도록 설정해야 합니다. 투명 데이터 암호화를 이미 사용하도록 설정한 경우 Contoso는 인증서 또는 비대칭 키를 대상 서버의 마스터 데이터베이스로 이동해야 합니다. [투명한 데이터 암호화 보호된 데이터베이스를 다른 SQL Server 인스턴스로 이동하는 방법](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)에 대해 알아보세요.

2. Contoso는 평가를 JSON 또는 CSV 형식으로 내보낼 수 있습니다.

> [!NOTE]
> 대규모 평가의 경우:
> - 여러 평가를 동시에 실행하고, **모든 평가** 페이지에서 평가 상태를 확인합니다.
> - 평가를 [SQL Server 데이터베이스](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017)에 통합합니다.
> - 평가를 [Power BI 보고서](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017)에 통합합니다.

## <a name="step-3-prepare-for-vm-assessment-by-using-azure-migrate"></a>3단계: Azure Migrate를 사용하여 VM 평가 준비

Contoso는 Azure Migrate에서 평가할 VM을 자동으로 검색하고, VM을 만들 수 있는 권한을 확인하고, 열어야 하는 포트를 기록하고, 통계 설정 수준을 지정하는 데 사용할 수 있는 VMware 계정을 만들어야 합니다.

### <a name="set-up-a-vmware-account"></a>VMware 계정 설정

VM을 검색하려면 vCenter Server에서 다음과 같은 속성이 있는 읽기 전용 계정이 필요합니다.

- **사용자 유형**: 읽기 전용 사용자(최소)
- **사용 권한**: 데이터 센터 개체의 경우 **자식 개체에 전파** 확인란을 선택합니다. **역할**에 대해 **읽기 전용**을 선택합니다.
- **세부 정보**: 사용자는 데이터 센터 수준에서 할당되어 데이터 센터의 모든 개체에 액세스할 수 있습니다.
- 액세스를 제한하려면 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.

### <a name="verify-permissions-to-create-a-vm"></a>VM을 만들 권한이 있는지 확인

Contoso는 파일을 .ova 형식으로 가져와서 VM을 만들 수 있는 권한이 있는지 확인합니다. [권한 있는 역할을 만들고 할당하는 방법](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1)에 대해 알아보세요.

### <a name="verify-ports"></a>포트 확인

Contoso 평가에서는 종속성 매핑을 사용합니다. 종속성 매핑을 사용하려면 평가할 VM에 에이전트를 설치해야 합니다. 에이전트는 각 VM의 443 TCP 포트에서 Azure에 연결할 수 있어야 합니다. [연결 요구 사항](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid)에 대해 알아보세요.

### <a name="set-statistics-settings"></a>통계 설정 지정

Contoso는 배포를 시작하기 전에 vCenter Server에 대한 통계 설정 수준을 3으로 설정해야 합니다.

> [!NOTE]
> - 수준을 설정한 후 평가를 실행하려면 Contoso는 적어도 하루 정도 기다려야 합니다. 그렇지 않으면 평가가 예상대로 작동하지 않을 수 있습니다.
> - 수준이 3보다 높으면 평가가 작동하지만 다음 사항을 고려해야 합니다.
>    - 디스크 및 네트워킹에 대한 성능 데이터가 수집되지 않습니다.
>    - 저장소의 경우 Azure Migrate는 Azure에서 온-프레미스 디스크와 크기가 같은 표준 디스크를 권장합니다.
>    - 네트워킹의 경우 각 온-프레미스 네트워크 어댑터에 대해 Azure에서 네트워크 어댑터를 사용하는 것이 좋습니다.
>    - 계산의 경우 Azure Migrate에서 VM 코어 수와 메모리 크기를 살펴보고 동일한 구성의 Azure VM을 사용하는 것이 좋습니다. 적합한 Azure VM 크기가 여러 개인 경우 비용이 가장 낮은 크기가 권장됩니다.
> - 수준 3을 사용하여 크기를 조정하는 방법에 대한 자세한 내용은 [크기 조정](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)을 참조하세요.

수준을 설정하려면 다음을 수행합니다.

1. Contoso는 vSphere 웹 클라이언트에서 vCenter Server 인스턴스를 엽니다.
2. Contoso는 **관리** > **설정** > **일반** > **편집**을 차례로 선택합니다.
3. Contoso는 **통계**에서 통계 수준 설정을 **수준 3**으로 설정합니다.

    ![vCenter Server 통계 수준](./media/contoso-migration-assessment/vcenter-statistics-level.png)

## <a name="step-4-discover-vms"></a>4단계: VM 검색

VM을 검색하기 위해 Contoso는 Azure Migrate 프로젝트를 만듭니다. Contoso는 수집기 VM을 다운로드하고 설정합니다. 그런 다음, Contoso는 수집기를 실행하여 온-프레미스 VM을 찾습니다.

### <a name="create-a-project"></a>프로젝트 만들기

1. Contoso는 [Azure Portal](https://portal.azure.com)에서 **Azure Migrate**를 검색합니다. 그런 다음, Contoso 프로젝트를 만듭니다.
2. Contoso는 프로젝트 이름(**ContosoMigration**)과 Azure 구독을 지정합니다. 새 Azure 리소스 그룹(**ContosoFailoverRG**)을 만듭니다.
    > [!NOTE]
    > - Azure Migrate 프로젝트는 미국 중서부 또는 미국 동부 지역에서만 만들 수 있습니다.
    > - 모든 대상 위치에 대해 마이그레이션을 계획할 수 있습니다.
    > - 프로젝트 위치는 온-프레미스 VM에서 수집된 메타데이터를 저장하는 데에만 사용됩니다.

    ![Azure Migrate - 마이그레이션 프로젝트 만들기](./media/contoso-migration-assessment/project-1.png)

### <a name="download-the-collector-appliance"></a>수집기 어플라이언스를 다운로드 합니다.

Azure Migrate는 *수집기 어플라이언스*로 알려진 온-프레미스 VM을 만듭니다. 이 VM은 온-프레미스 VMware VM을 검색하고, VM에 대한 메타데이터를 Azure Migrate 서비스로 보냅니다. Contoso는 수집기 어플라이언스를 설정하기 위해 OVA 템플릿을 다운로드한 다음, 온-프레미스 vCenter 서버 인스턴스로 가져와서 VM을 만듭니다.

1. Contoso는 Azure Migrate 프로젝트에서 **시작** > **검색 및 평가** > **머신 검색**을 차례로 선택하여 OVA 템플릿 파일을 다운로드합니다.
2. Contoso는 프로젝트 ID와 키를 복사합니다. 프로젝트와 ID는 수집기를 구성하는 데 필요합니다.

    ![Azure Migrate - OVA 파일 다운로드](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>수집기 어플라이언스를 확인합니다.

VM을 배포하기 전에 Contoso는 OVA 파일이 안전한지 확인합니다.

1. Contoso는 파일을 다운로드한 머신에서 관리자 명령 프롬프트 창을 엽니다.
2. Contoso는 다음 명령을 실행하여 OVA 파일에 대한 해시를 생성합니다.

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    **예제**

    ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 생성된 해시는 [여기](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#continuous-discovery)에 나와 있는 해시 값과 일치해야 합니다.

### <a name="create-the-collector-appliance"></a>수집기 어플라이언스 만들기

이제 Contoso는 다운로드한 파일을 vCenter Server 인스턴스로 가져오고, 수집기 어플라이언스 VM을 프로비전할 수 있습니다.

1. Contoso는 vSphere Client 콘솔에서 **파일** > **OVF 템플릿 배포**를 차례로 선택합니다.

    ![vSphere 웹 클라이언트 - OVF 템플릿 배포](./media/contoso-migration-assessment/vcenter-wizard.png)

2. Contoso는 OVF 템플릿 배포 마법사에서 **원본**을 선택한 다음, OVA 파일의 위치를 지정합니다.
3. Contoso는 **이름 및 위치**에서 수집기 VM에 대한 표시 이름을 지정합니다. 그런 다음, VM을 호스팅할 인벤토리 위치를 선택합니다. 또한 Contoso는 수집기 어플라이언스를 실행할 호스트 또는 클러스터도 지정합니다.
4. Contoso는 **저장소**에서 저장소 위치를 지정합니다. Contoso는 **디스크 형식**에서 저장소를 프로비전하려는 방법을 선택합니다.
5. Contoso는 **네트워크 매핑**에서 수집기 VM을 연결할 네트워크를 지정합니다. 메타데이터를 Azure로 전송하려면 네트워크에 인터넷 연결이 필요합니다.
6. Contoso는 설정을 검토한 다음, 배포 후 **배포 후 전원 켜기** > **마침**을 차례로 선택합니다. 어플라이언스가 만들어지면 성공적으로 완료되었음을 알리는 확인 메시지가 표시됩니다.

### <a name="run-the-collector-to-discover-vms"></a>VM을 검색하려면 수집기를 실행합니다.

이제 Contoso는 수집기를 실행하여 VM을 검색합니다. 수집기는 현재 운영 체제 언어 및 수집기 인터페이스 언어로 **영어(미국)** 만 지원합니다.

1. Contoso는 vSphere Client 콘솔에서 **콘솔 열기**를 선택합니다. Contoso는 수집기 VM에 대한 언어, 표준 시간대 및 암호 기본 설정을 지정합니다.
2. Contoso는 바탕 화면에서 **수집기 실행** 바로 가기를 선택합니다.

    ![vSphere Client 콘솔 - 수집기 바로 가기](./media/contoso-migration-assessment/collector-shortcut.png)

3. Contoso는 Azure Migrate 수집기에서 **필수 구성 요소 설정**을 선택합니다. Contoso는 사용 조건을 수락하고 타사 정보를 읽습니다.
4. 수집기는 VM이 인터넷에 액세스했는지, 시간이 동기화되었는지 및 수집기 서비스가 실행되고 있는지 확인합니다. (수집기 서비스는 기본적으로 VM에 설치됩니다.) 또한 Contoso는 VMware PowerCLI도 설치합니다.

    > [!NOTE]
    > VM에서 프록시를 사용하지 않고 인터넷에 직접 액세스할 수 있다고 가정합니다.

    ![Azure Migrate 수집기 - 필수 구성 요소 확인](./media/contoso-migration-assessment/collector-verify-prereqs.png)

5. Contoso는 **vCenter Server 세부 정보 지정**에서 vCenter Server 인스턴스의 이름(FQDN) 또는 IP 주소와 검색에 사용되는 읽기 전용 자격 증명을 입력합니다.
6. Contoso는 VM 검색 범위를 선택합니다. 수집기는 지정된 범위 내에 있는 VM만 검색할 수 있습니다. 범위는 특정 폴더, 데이터 센터 또는 클러스터로 설정할 수 있습니다. 범위에는 1,500개를 초과하는 VM이 포함되면 안됩니다.

    ![vCenter Server 세부 정보 지정](./media/contoso-migration-assessment/collector-connect-vcenter.png)

7. Contoso는 **마이그레이션 프로젝트 지정**에서 포털에서 복사한 Azure Migrate 프로젝트 ID와 키를 입력합니다. Contoso는 프로젝트 ID와 키를 가져오기 위해 프로젝트 **개요** 페이지 > **머신 검색**으로 차례로 이동할 수 있습니다.  

    ![마이그레이션 프로젝트 지정](./media/contoso-migration-assessment/collector-connect-azure.png)

8. Contoso는 **수집 진행 상황 보기**에서 검색을 모니터링하고, VM에서 수집한 메타데이터가 범위 내에 있는지 확인할 수 있습니다. 수집기는 대략적인 검색 시간을 제공합니다.

    ![수집 진행 상황 보기](./media/contoso-migration-assessment/collector-collection-process.png)

### <a name="verify-vms-in-the-portal"></a>포털에서 VM 확인

수집이 완료되면 Contoso에서 VM이 포털에 표시되는지 확인합니다.

1. Contoso는 Azure Migrate 프로젝트에서 **관리** > **머신**을 차례로 선택합니다. Contoso는 검색하려는 VM이 표시되는지 확인합니다.

    ![Azure Migrate - 검색된 머신](./media/contoso-migration-assessment/discovery-complete.png)

2. 현재 Azure Migrate 에이전트는 머신에 설치되어 있지 않습니다. Contoso는 종속성을 보려면 에이전트를 설치해야 합니다.

    ![Azure Migrate - 에이전트 설치 필요](./media/contoso-migration-assessment/machines-no-agent.png)

## <a name="step-5-prepare-for-dependency-analysis"></a>5단계: 종속성 분석 준비

평가하려는 VM 간의 종속성을 확인하려면 Contoso에서 앱 VM에 에이전트를 다운로드하여 설치합니다. Contoso는 Windows 및 Linux용 앱에 대한 모든 VM에 에이전트를 설치합니다.

### <a name="take-a-snapshot"></a>스냅숏 만들기

복사본을 유지한 후에 VM을 수정하려면 Contoso에서 에이전트를 설치하기 전에 스냅숏을 만듭니다.

![컴퓨터 스냅숏](./media/contoso-migration-assessment/snapshot-vm.png)

### <a name="download-and-install-the-vm-agents"></a>VM 에이전트 다운로드 및 설치

1. Contoso는 **머신**에서 해당 머신을 선택합니다. Contoso는 **종속성** 열에서 **설치 필요**를 선택합니다.
2. Contoso는 **머신 검색** 창에서 다음을 수행합니다.
    - 각 Windows VM용 MMA(Microsoft Monitoring Agent) 및 Dependency Agent를 다운로드합니다.
    - 각 Linux VM용 MMA 및 Dependency Agent를 다운로드합니다.
3. Contoso에서 작업 영역 ID와 키를 복사합니다. 작업 영역 ID와 키는 Contoso에서 MMA를 설치할 때 필요합니다.

    ![에이전트 다운로드](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Windows VM에 에이전트 설치

Contoso는 각 VM에서 설치를 실행합니다.

#### <a name="install-the-mma-on-windows-vms"></a>Windows VM에 MMA 설치

1. Contoso는 다운로드한 에이전트를 두 번 클릭합니다.
2. Contoso는 **대상 폴더**에서 기본 설치 폴더를 유지하고, **다음**을 선택합니다.
3. Contoso는 **에이전트 설치 옵션**에서 **Azure Log Analytics에 에이전트 연결** > **다음**을 차례로 선택합니다.

    ![Microsoft Monitoring Agent 설치 - 에이전트 설치 옵션](./media/contoso-migration-assessment/mma-install.png)

4. Contoso는 **Azure Log Analytics**에서 포털로부터 복사한 작업 영역 ID와 키를 붙여넣습니다.

    ![Microsoft Monitoring Agent 설치 - Azure Log Analytics](./media/contoso-migration-assessment/mma-install2.png)

5. Contoso는 **설치 준비 완료**에서 MMA를 설치합니다.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Windows VM에 Dependency Agent 설치

1. Contoso는 다운로드한 Dependency Agent를 두 번 클릭합니다.
2. Contoso는 사용 조건을 수락하고 설치가 완료될 때까지 기다립니다.

    ![Dependency Agent 설치 - 설치](./media/contoso-migration-assessment/dependency-agent.png)

### <a name="install-the-agents-on-linux-vms"></a>Linux VM에 에이전트 설치

Contoso는 각 VM에서 설치를 실행합니다.

#### <a name="install-the-mma-on-linux-vms"></a>Linux VM에 MMA 설치

1. Contoso는 다음 명령을 사용하여 각 VM에 Python C 형식 라이브러리를 설치합니다.

    `sudo apt-get install python-ctypeslib`
2. Contoso는 MMA 에이전트를 루트로 설치하는 명령을 실행해야 합니다. 루트가 되도록 하려면 Contoso는 다음 명령을 실행하고, 루트 암호를 입력합니다.

    `sudo -i`
3. Contoso는 MMA를 설치합니다.
   - Contoso는 명령에 작업 영역 ID와 키를 입력합니다.
   - 명령은 64비트용입니다.
   - 작업 영역 ID 및 기본 키는 Azure Portal의 Log Analytics 작업 영역에 있습니다. **설정**, **연결된 원본** 탭을 차례로 선택합니다.
   - 다음 명령을 실행하여 Log Analytics 에이전트를 다운로드하고, 체크섬의 유효성을 검사하고, 에이전트를 설치/등록합니다.

     ```
     wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
     ```

#### <a name="install-the-dependency-agent-on-linux-vms"></a>Linux VM에 Dependency Agent 설치

MMA가 설치되면 Contoso에서 Linux VM에 Dependency Agent를 설치할 수 있습니다.

1. Dependency Agent는 자동 압축 풀기 이진 파일이 있는 셸 스크립트인 InstallDependencyAgent-Linux64.bin을 사용하여 Linux 컴퓨터에 설치됩니다. Contoso는 sh를 사용하여 파일을 실행하거나 파일 자체에 실행 권한을 추가합니다.

2. Contoso는 Linux Dependency Agent를 루트로 설치합니다.

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```

## <a name="step-6-run-and-analyze-the-vm-assessment"></a>6단계: VM 평가 실행 및 분석

이제 Contoso는 머신 종속성을 확인하고 그룹을 만들 수 있습니다. 그런 다음, 그룹에 대한 평가를 실행합니다.

### <a name="verify-dependencies-and-create-a-group"></a>종속성 확인 및 그룹 만들기

1. Contoso는 분석할 머신을 결정하기 위해 **종속성 보기**를 선택합니다.

    ![Azure Migrate - 머신 종속성 보기](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. SQLVM의 경우 종속성 맵에 다음과 같은 세부 정보가 표시됩니다.

    - 지정된 기간(기본적으로 1시간) 동안 SQLVM에서 실행되는 활성 네트워크 연결이 있는 프로세스 그룹 또는 프로세스
    - 모든 종속 컴퓨터와 주고 받는 인바운드(클라이언트) 및 아웃바운드(서버) TCP 연결.
    - Azure Migrate 에이전트가 설치된 종속 머신은 별도의 상자에 표시됩니다.
    - 에이전트가 설치되지 않은 머신에는 포트 및 IP 주소 정보가 표시됩니다.

3. 에이전트가 설치된 머신(WEBVM)의 경우 Contoso는 해당 머신 상자를 선택하여 자세한 정보를 확인합니다. 이 정보에는 FQDN, 운영 체제 및 MAC 주소가 포함되어 있습니다.

    ![Azure Migrate - 그룹 종속성 보기](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Contoso는 그룹에 추가할 VM(SQLVM 및 WEBVM)을 선택합니다. Contoso는 Ctrl+클릭을 사용하여 여러 VM을 선택합니다.
5. Contoso는 **그룹 만들기**를 선택한 다음, 이름(**smarthotelapp**)을 입력합니다.

    > [!NOTE]
    > 시간 범위를 확장하면 보다 세부적인 종속성을 볼 수 있습니다. 특정 기간을 선택하거나 시작 및 종료 날짜를 선택할 수 있습니다.

### <a name="run-an-assessment"></a>평가 실행

1. Contoso는 **그룹**에서 그룹(**smarthotelapp**)을 연 다음, **평가 만들기**를 선택합니다.

    ![Azure Migrate - 평가 만들기](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Contoso는 평가를 보기 위해 **관리** > **평가**를 차례로 선택합니다.

Contoso에서 기본 평가 설정을 사용했지만, [설정은 사용자 지정](how-to-modify-assessment.md)할 수 있습니다.

### <a name="analyze-the-vm-assessment"></a>VM 평가 분석

Azure Migrate 평가에는 Azure와의 온-프레미스 호환성, Azure VM에 권장되는 적절한 크기 조정 및 월별 예상 Azure 비용에 대한 정보가 포함됩니다.

![Azure Migrate - 평가 보고](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>신뢰 등급 검토

![Azure Migrate - 평가 표시](./media/contoso-migration-assessment/assessment-display.png)

평가에는 1-5개의 별로 지정된 신뢰 등급이 있습니다(별 1개가 가장 낮고, 별 5개가 가장 높음).
- 신뢰 등급은 평가를 계산하는 데 필요한 데이터 요소의 가용성에 따라 평가에 할당됩니다.
- 등급은 Azure Migrate에서 제공하는 크기 권장 사항의 신뢰성을 추정하는 데 도움이 됩니다.
- 신뢰 등급은 *성능 기반 크기 조정*을 수행할 때 유용합니다. Azure Migrate에는 사용률 기반 크기 조정을 위한 데이터 요소가 충분하지 않을 수 있습니다. *온-프레미스로* 크기 조정의 경우 Azure Migrate에는 VM 크기를 조정하는 데 필요한 모든 데이터 요소가 있으므로 신뢰 등급은 항상 별 5개입니다.
- 사용 가능한 데이터 요소의 백분율에 따라 평가의 신뢰 등급이 제공됩니다.

   데이터 요소 가용성 | 신뢰 등급
   --- | ---
   0%-20% | 별 1개
   21%-40% | 별 2개
   41%-60% | 별 3개
   61%-80% | 별 4개
   81%-100% | 별 5개

#### <a name="verify-azure-readiness"></a>Azure 준비 상태 확인

![Azure Migrate - 평가 준비 상태](./media/contoso-migration-assessment/azure-readiness.png)  

평가 보고서에는 테이블에 요약된 정보가 표시됩니다. Azure Migrate에서 성능 기반 크기 조정을 표시하는 데 필요한 정보는 다음과 같습니다. 이러한 정보를 수집할 수 없는 경우 평가 크기 조정이 정확하지 않을 수 있습니다.

- CPU 및 메모리 사용률 데이터.
- VM에 연결된 각 디스크의 읽기/쓰기 IOPS 및 처리량.
- VM에 연결된 각 네트워크 어댑터의 네트워크 입/출력 정보.

설정 | 표시 | 세부 정보
--- | --- | ---
**Azure VM 준비 상태** | VM이 마이그레이션할 준비가 되었는지 여부를 나타냅니다. | 가능한 상태:</br><br/>- Azure 준비 완료<br/><br/>- 조건부 준비 완료 <br/><br/>- Azure를 사용할 준비 안 됨<br/><br/>- 준비 상태 알 수 없음<br/><br/> VM이 준비되지 않은 경우 Azure Migrate에 몇 가지 수정 단계가 표시됩니다.
**Azure VM 크기** | 준비된 VM의 경우 Azure Migrate에서 Azure VM 크기 권장 사항을 제공합니다. | 권장 크기는 평가 속성에 따라 달라집니다.<br/><br/>- 성능 기반 크기 조정을 사용한 경우 크기 조정 시 VM의 성능 기록을 고려합니다.<br/><br/>- *온-프레미스로* 크기 조정을 사용한 경우 크기 조정은 온-프레미스 VM 크기를 기반으로 하지만, 사용률 데이터는 사용되지 않습니다.
**추천 도구** | Azure 머신에서 에이전트를 실행하므로 Azure Migrate가 머신 내부에서 실행되는 프로세스를 살펴보고, 머신이 데이터베이스 머신인지 여부를 식별합니다.
**VM 정보** | 이 보고서에는 운영 체제, 부팅 유형, 디스크 및 저장소 정보를 포함하여 온-프레미스 VM에 대한 설정이 표시됩니다.

#### <a name="review-monthly-cost-estimates"></a>월별 예상 비용 검토

이 보기에서는 Azure에서 VM을 실행하는 데 소요되는 총 계산 및 스토리지 비용을 표시합니다. 또한 각 머신에 대한 세부 정보도 표시합니다.

![Azure Migrate - Azure 비용](./media/contoso-migration-assessment/azure-costs.png)

- 예상 비용은 머신에 대한 크기 권장 사항을 사용하여 계산됩니다.
- 계산 및 스토리지에 대한 월별 예상 비용은 그룹의 모든 VM에 대해 집계됩니다.

## <a name="clean-up-after-assessment"></a>평가 후 정리

- 평가가 완료되면 Contoso는 향후 평가에 사용하기 위해 Azure Migrate 어플라이언스를 유지합니다.
- Contoso는 VMware VM을 해제합니다. 이 VM은 Contoso에서 추가 VM을 평가할 때 다시 사용됩니다.
- Contoso는 Azure에서 **Contoso 마이그레이션** 프로젝트를 유지합니다. 이 프로젝트는 현재 미국 동부 Azure 지역의 **ContosoFailoverRG** 리소스 그룹에 배포되어 있습니다.
-  수집기 VM에는 180일 평가판 라이선스가 있습니다. 이 제한이 만료되면 Contoso에서 수집기를 다운로드하여 다시 설정해야 합니다.

## <a name="conclusion"></a>결론

이 시나리오에서는 Contoso에서 데이터 마이그레이션 평가 도구를 사용하여 SmartHotel360 앱 데이터베이스를 평가하고, Azure Migrate 서비스를 사용하여 온-프레미스 VM을 평가합니다. Contoso에서 온-프레미스 리소스를 Azure로 마이그레이션할 준비가 되었는지 확인하기 위해 평가를 검토합니다.

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 문서에서는 Contoso에서 리프트 앤 시프트 마이그레이션을 사용하여 Azure에서 SmartHotel360 앱을 다시 호스트합니다. Contoso에서 Azure Site Recovery를 사용하여 앱용 프런트 엔드 WEBVM을 마이그레이션하고, Database Migration Service를 사용하여 앱 데이터베이스를 Azure SQL Database Managed Instance로 마이그레이션합니다. 이 배포로 [시작](contoso-migration-rehost-vm-sql-managed-instance.md)하세요.
