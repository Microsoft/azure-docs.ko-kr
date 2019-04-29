---
title: Azure Web App 및 Azure SQL Database로 마이그레이션하여 Contoso 앱 리팩터링 | Microsoft Docs
description: Contoso가 온-프레미스 앱을 Azure Web App 및 Azure SQL Server 데이터베이스로 마이그레이션하여 다시 호스트하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 271e18d370068e0445f183af0c694b19f0da22f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60672968"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Contoso 마이그레이션: Azure Web App 및 Azure SQL 데이터베이스로 온-프레미스 앱 리팩터링

이 문서에서는 Contoso가 Azure에서 SmartHotel360 앱을 리팩터링하는 방법을 보여줍니다. 앱 프런트 엔드 VM을 Azure 웹앱으로, 앱 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션합니다.

이 문서는 가상 회사 Contoso가 온-프레미스 리소스를 Microsoft Azure 클라우드로 마이그레이션하는 방법을 보여 주는 문서 시리즈 중 하나입니다. 이 문서 시리즈에는 배경 정보와 마이그레이션 인프라 설정, 마이그레이션을 위한 온-프레미스 리소스 평가, 다양한 유형의 마이그레이션 실행을 설명하는 시나리오가 담겨 있습니다. 시나리오가 복잡해지고 있으며, 앞으로 계속해서 문서가 추가될 것입니다.

**문서** | **세부 정보** | **상태**
--- | --- | ---
[문서 1: 개요](contoso-migration-overview.md) | Contoso 마이그레이션 전략, 문서 시리즈 및 사용할 샘플 앱에 대해 간략히 설명합니다. | 사용 가능
[문서 2: Azure 인프라 배포](contoso-migration-infrastructure.md) | Contoso에서 마이그레이션을 위해 온-프레미스 및 Azure 인프라를 준비하는 방법에 대해 설명합니다. 모든 마이그레이션 문서에 동일한 인프라가 사용됩니다. | 사용 가능
[문서 3: 온-프레미스 리소스 평가](contoso-migration-assessment.md)  | Contoso가 VMware에서 실행되는 온-프레미스 2계층 SmartHotel 앱 평가를 실행하는 방법을 보여 줍니다. Contoso가 [Azure Migrate](migrate-overview.md) 서비스로 앱 VM을 평가하고 [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)로 앱 SQL Server 데이터베이스를 평가합니다. | 사용 가능
[문서 4: 앱을 Azure VM 및 SQL Managed Instance에 다시 호스트](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso가 SmartHotel 앱에 대해 Azure로의 리프트 앤 시프트 마이그레이션을 실행하는 방법을 보여 줍니다. Contoso는 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하여 앱 프런트 엔드 VM을 마이그레이션하고 [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)를 사용하여 앱 데이터베이스를 SQL Managed Instance로 마이그레이션합니다. | 사용 가능
[문서 5: 앱을 Azure VM에 다시 호스트](contoso-migration-rehost-vm.md) | Contoso가 Site Recovery만 사용하여 SmartHotel 앱 VM을 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 6: 앱을 Azure VM 및 SQL Server Always On 가용성 그룹에 다시 호스트](contoso-migration-rehost-vm-sql-ag.md) | Contoso에서 SmartHotel 앱을 마이그레이션하는 방법을 보여 줍니다. Contoso가 Site Recovery를 사용하여 앱 VM을 마이그레이션하고 Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹에서 보호하는 SQL Server 클러스터로 마이그레이션합니다. | 사용 가능
[문서 7: Azure VM에 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm.md) | Contoso가 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 리프트 앤 시프트 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 8: Azure VM 및 Azure MySQL 서버에 Linux 앱 다시 호스팅](contoso-migration-rehost-linux-vm-mysql.md) | Contoso가 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 마이그레이션하고 MySQL Workbench를 사용하여 앱 데이터베이스를 Azure MySQL 서버 인스턴스로 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
문서 9: Azure 웹앱 및 Azure SQL 데이터베이스에 앱 리팩터링 | Contoso가 SmartHotel 앱을 Azure 웹앱으로 마이그레이션하고, 앱 데이터베이스를 Azure SQL Server 인스턴스로 마이그레이션하는 방법 시연 | 이 문서의 내용
[문서 10: Azure Web Apps 및 Azure MySQL에 Linux 앱 리팩터링](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso가 지속적인 업데이트를 위해 GitHub와 통합된, 여러 사이트의 Azure Web Apps로 Linux osTicket 앱을 마이그레이션하는 방법을 보여줍니다. Azure MySQL 인스턴스로 앱 데이터베이스를 마이그레이션합니다. | 사용 가능
[문서 11: Azure DevOps Services에서 TFS 리팩터링](contoso-migration-tfs-vsts.md) | Contoso가 온-프레미스 TFS(Team Foundation Server) 배포를 Azure의 Azure DevOps Services로 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 12: Azure 컨테이너 및 Azure SQL Database에서 앱 재설계](contoso-migration-rearchitect-container-sql.md) | Contoso가 SmartHotel 앱을 Azure로 마이그레이션하고 아키텍처를 변경하는 방법을 보여줍니다. 웹앱 계층을 Windows 컨테이너 및 Azure SQL Database의 앱 데이터베이스로 아키텍처를 변경합니다. | 사용 가능
[문서 13: Azure에서 앱 다시 빌드](contoso-migration-rebuild.md) | Contoso가 App Services, Azure Kubernetes, Azure Functions, Cognitive services 및 Cosmos DB를 포함한 다양한 Azure 기능 및 서비스를 사용하여 자체 SmartHotel 앱을 다시 빌드하는 방법을 보여줍니다. | 사용 가능
[문서 14: Azure로의 마이그레이션 확장](contoso-migration-scale.md) | 마이그레이션 조합을 시도한 후 Contoso는 Azure로 전체 마이그레이션을 확장할 준비를 합니다. | 사용 가능

이 문서에서 Contoso는 2계층 Windows를 마이그레이션합니다. VMware VM에서 실행되는 NET SmartHotel360 앱을 Azure로 마이그레이션합니다. 이 앱을 사용하려는 경우 오픈 소스로 제공되므로 [GitHub](https://github.com/Microsoft/SmartHotel360)에서 다운로드할 수 있습니다.

## <a name="business-drivers"></a>비즈니스 영향 요소

IT 리더십 팀은 비즈니스 파트너와의 긴밀한 협력을 통해 다음과 같이 기업이 이러한 마이그레이션으로 달성하고자 하는 바를 잘 이해하고 있습니다.

- **비즈니스 성장 해결**: Contoso는 성장하고 있으며 온-프레미스 시스템과 인프라에 부담을 주고 있습니다.
- **효율성 증대**: Contoso는 불필요한 절차를 제거하고 개발자와 사용자를 위한 프로세스를 간소화해야 합니다.  이 회사는 IT를 빠른 상태로 유지하고 시간이나 비용을 낭비하지 않도록 하여 고객 요구 사항을 더 빠르게 제공해야 합니다.
- **민첩성 향상**:  Contoso IT는 비즈니스 요구 사항에 대해 더 빠르게 대응해야 합니다. 글로벌 경제에서 성공할 수 있으려면 시장의 변화에 더 빠르게 대응할 수 있어야 합니다.  사업상의 걸림돌이 되지 않아야 합니다.
- **크기 조정**: 비즈니스가 성공적으로 성장함에 따라 Contoso IT에서 동일한 속도로 크기를 조정할 수 있는 시스템을 제공해야 합니다.
- **비용**: Contoso는 라이선스 비용을 최소화하려고 합니다.

## <a name="migration-goals"></a>마이그레이션 목표

Contoso 클라우드 팀은 이 마이그레이션에 대한 목표를 확정했습니다. 다음과 같은 목표는 최상의 마이그레이션 방법을 결정하는 데 사용되었습니다.

**요구 사항** | **세부 정보**
--- | --- 
**앱** | Azure의 앱은 지금처럼 중요하게 유지됩니다.<br/><br/> 앞으로도 현재 VMWare에서 수행하는 것과 동일한 성능을 갖추어야 합니다.<br/><br/> 팀은 앱에 투자하려고 하지 않습니다. 지금으로선 관리자는 앱을 안전하게 클라우드로 이동시키기만 하면 됩니다.<br/><br/> 앱이 현재 실행하는 Windows Server 2008 R2 지원을 팀이 중지하려고 합니다.<br/><br/> 팀은 SQL Server 2008 R2에서 관리에 대한 필요성을 최소화하는 최신 PaaS 데이터베이스 플랫폼으로 이동하려고 합니다.<br/><br/> Contoso는 가능한 경우 SQL Server 라이선싱 및 Software Assurance에 대한 투자를 활용하려고 합니다.<br/><br/> 또한 Contoso는 웹 계층에서 단일 실패 지점을 완화하려고 합니다.
**제한 사항** | 앱은 동일한 VM에서 실행되는 ASP.NET 앱 및 WCF 서비스로 구성됩니다. Contoso는 Azure App Service를 사용하여 두 개의 웹앱에서 분할하려고 합니다. 
**Azure** | Contoso는 앱을 Azure로 이동하려고 하지만 VM에서 실행하지 않으려고 합니다. Contoso는 웹 및 데이터 계층에 대해 Azure PaaS 서비스를 활용하려고 합니다. 
**DevOps** | Contoso는 빌드 및 릴리스 파이프라인에 Azure DevOps를 사용하여 DevOps 모델로 이동하려고 합니다.

## <a name="solution-design"></a>솔루션 디자인

목표 및 요구 사항을 고정한 후 Contoso는 배포 솔루션을 디자인 및 검토하고, 마이그레이션에 사용할 Azure 서비스를 포함한 마이그레이션 프로세스를 식별합니다.

### <a name="current-app"></a>현재 앱

- SmartHotel360 온-프레미스 앱은 두 개의 VM(WEBVM 및 SQLVM)에 계층화되어 있습니다.
- VM은 VMware ESXi 호스트 **contosohost1.contoso.com**(버전 6.5)에 있습니다.
- VMware 환경은 VM에서 실행되는 vCenter Server 6.5(**vcenter.contoso.com**)에서 관리합니다.
- Contoso는 온-프레미스 데이터 센터(contoso-datacenter)와 온-프레미스 도메인 컨트롤러(**contosodc1**)를 갖고 있습니다.
- 마이그레이션이 완료되면 Contoso 데이터 센터의 온-프레미스 VM은 서비스 해제됩니다.


### <a name="proposed-solution"></a>제안된 솔루션

- 앱의 데이터베이스 계층의 경우 Contoso는 [이 문서](https://docs.microsoft.com/azure/sql-database/sql-database-features)를 사용하여 Azure SQL Database를 SQL Server와 비교했습니다. Contoso는 몇 가지 이유로 Azure SQL Database로 이동하기로 결정했습니다.
    - Azure SQL Database는 관리되는 관계형 데이터베이스 서비스입니다. 0에 가까운 관리를 사용하여 여러 서비스 수준에서 예측 가능한 성능을 제공합니다. 이점으로 가동 중지 시간 없는 동적 확장성, 기본 제공 지능형 최적화 및 글로벌 확장성 및 가용성을 포함합니다.
    - Contoso는 간단한 DMA(Data Migration Assistant)를 활용하여 온-프레미스 데이터베이스를 Azure SQL로 평가 및 마이그레이션할 수 있습니다.
    - Software Assurance와 함께 사용하면 Contoso는 SQL Database에서 SQL Server에 대한 Azure 하이브리드 혜택을 사용하여 기존 라이선스를 할인된 가격에 교환할 수 있습니다. 최대 30%까지 비용 절감을 제공할 수 있습니다.
    - SQL Database는 상시 암호화, 동적 데이터 마스킹 및 행 수준 보안/위협 검색을 포함하는 다양한 보안 기능을 제공합니다.
- 앱 웹 계층의 경우 Contoso는 Azure App Service를 사용하기로 결정했습니다. 이 PaaS 서비스를 사용하면 몇 가지 구성 변경 내용만으로 앱을 배포할 수 있습니다. Contoso는 Visual Studio를 사용하여 내용을 변경하고, 두 개의 웹앱을 배포합니다. 각각 웹 사이트 및 WCF 서비스에 대한 것입니다.
- DevOps 파이프라인 요구 사항을 충족하기 위해 Contoso는 Git 리포지토리와 함께 SCM(소스 코드 관리)용 Azure DevOps를 사용하기로 선택했습니다. 자동화된 빌드 및 릴리스를 사용하여 코드를 작성하고 Azure Web Apps에 배포합니다.
  
### <a name="solution-review"></a>솔루션 검토
Contoso는 장점 및 단점 목록을 함께 조합하여 제안된 디자인을 평가합니다.

**고려 사항** | **세부 정보**
--- | ---
**장점** | Azure로 마이그레이션하기 위해 SmartHotel360 앱 코드를 변경할 필요가 없습니다.<br/><br/> Contoso는 SQL Server와 Windows Server에 대한 Azure 하이브리드 혜택을 사용하여 Software Assurance에 대한 투자를 활용할 수 있습니다.<br/><br/> 마이그레이션 후 Windows Server 2008 R2는 지원되지 않아도 됩니다. [자세히 알아보기](https://support.microsoft.com/lifecycle).<br/><br/> Contoso는 더 이상 단일 실패 지점이 되지 않도록 여러 인스턴스를 사용하여 앱의 웹 계층을 구성할 수 있습니다.<br/><br/> 데이터베이스는 더 이상 오래된 SQL Server 2008 R2에 의존할 필요가 없습니다.<br/><br/> SQL Database는 기술 요구 사항을 지원합니다. Contoso는 Database Migration Assistant를 사용하여 온-프레미스 데이터베이스를 평가하고 호환된다는 것을 확인했습니다.<br/><br/> SQL Database에는 Contoso에서 설정할 필요가 없는 기본 제공 내결함성이 있습니다. 따라서 데이터 계층은 더 이상 단일 장애 조치(failover) 지점이 아닙니다.
**단점** | Azure App Services는 각 웹앱에 대해 하나의 앱 배포만 지원합니다. 즉, 두 개의 Web Apps가 프로비전되어야 합니다(각각 웹 사이트 및 WCF 서비스에 대해).<br/><br/> Contoso에서 해당 데이터베이스를 마이그레이션하는 데 Data Migration Service 대신 Data Migration Assistant를 사용하는 경우 Contoso에는 대규모 데이터베이스 마이그레이션을 위해 준비된 인프라가 없습니다. Contoso는 주 지역을 사용할 수 없는 경우 장애 조치(failover)되도록 다른 지역을 빌드해야 합니다.

## <a name="proposed-architecture"></a>제안된 아키텍처

![시나리오 아키텍처](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>마이그레이션 프로세스

1. Contoso는 Azure SQL 인스턴스를 프로비전하고, 거기에 SmartHotel360 데이터베이스를 마이그레이션합니다.
2. Contoso는 웹앱을 프로비전 및 구성하고 SmartHotel360 앱을 배포합니다.

    ![마이그레이션 프로세스](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Azure 서비스

**서비스** | **설명** | **비용**
--- | --- | ---
[DMA(Database Migration Assistant)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso는 DMA를 사용하여 Azure의 데이터베이스 기능에 영향을 줄 수 있는 호환성 문제를 평가하고 검색합니다. DMA는 SQL 원본과 대상 간의 기능 패리티를 평가하고 성능 및 안정성을 향상하기 위한 방안을 제시합니다. | DMA는 무료로 다운로드할 수 있는 도구입니다.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | 완전히 관리되는 지능적인 관계형 클라우드 데이터베이스 서비스입니다. | 기능, 처리량 및 크기에 따른 비용입니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Azure App Services - Web Apps](https://docs.microsoft.com/azure/app-service/overview) | 완전히 관리되는 플랫폼을 사용하여 강력한 클라우드 앱 만들기 | 크기, 위치 및 사용 기간에 따른 비용입니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/app-service/windows/).
[Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | 앱 개발을 위한 CI/CD(연속 통합 및 연속 배포) 파이프라인을 제공합니다. 이 파이프라인은 앱 코드 관리용 Git 리포지토리, 패키지 및 기타 빌드 아티팩트 생성용 빌드 시스템, 그리고 개발/테스트/프로덕션 환경에서 변경 내용을 배포하기 위한 릴리스 관리 시스템을 초기 구성 요소로 포함합니다. 

## <a name="prerequisites"></a>필수 조건

이 시나리오를 실행하기 위해 Contoso에 필요한 항목은 다음과 같습니다.

**요구 사항** | **세부 정보**
--- | ---
**Azure 구독** | Contoso는 이전 문서에서 구독을 만들었습니다. Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.<br/><br/> 체험 계정을 만들면 구독 관리자로서 모든 작업을 수행할 수 있습니다.<br/><br/> 기존 구독을 사용하고 관리자가 아닌 경우 관리자와 협력하여 소유자 또는 기여자 권한을 할당받아야 합니다.
**Azure 인프라** | Contoso가 Azure 인프라를 설정하는 [방법에 대해 알아보세요](contoso-migration-infrastructure.md).


## <a name="scenario-steps"></a>시나리오 단계

Contoso가 마이그레이션을 실행하는 방법은 다음과 같습니다.

> [!div class="checklist"]
> * **1단계: Azure에서 SQL Database 인스턴스 프로비전**: Contoso는 Azure에 SQL 인스턴스를 프로비전합니다. 앱 웹 사이트를 Azure로 마이그레이션한 후 WCF 서비스 웹앱은 이 인스턴스를 가리킵니다.
> * **2단계: DMA를 사용하여 데이터베이스 마이그레이션**: Contoso는 Database Migration Assistant를 사용하여 앱 데이터베이스를 마이그레이션합니다.
> * **3단계: Web Apps 프로비전**: Contoso는 두 개의 웹앱을 프로비전합니다.
> * **4단계: Azure DevOps 설정**: Contoso는 새 Azure DevOps 프로젝트를 만들고 Git 리포지토리를 가져옵니다.
> * **5단계: 연결 문자열 구성**: Contoso는 웹 계층 웹앱, WCF 서비스 웹앱 및 SQL 인스턴스가 통신할 수 있도록 연결 문자열을 구성합니다.
> * **6단계: 빌드 및 릴리스 파이프라인 설정**: 최종 단계로, Contoso는 빌드 및 릴리스 파이프라인을 설정하여 앱을 만들고, 두 개의 별도 Azure Web Apps에 배포합니다.


## <a name="step-1-provision-an-azure-sql-database"></a>1단계: Azure SQL Database 프로비전

1. Contoso 관리자가 Azure에서 SQL Database를 만들기로 선택합니다. 

    ![SQL 프로비전](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. 온-프레미스 VM에서 실행되는 데이터베이스와 일치하는 데이터베이스 이름을 지정합니다(**SmartHotel.Registration**). ContosoRG 리소스 그룹에 데이터베이스를 배치합니다. Azure에서 프로덕션 리소스에 사용하는 리소스 그룹입니다.

    ![SQL 프로비전](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. 주 지역에 새 SQL Server 인스턴스를 설정합니다(**sql-smarthotel-eus2**).

    ![SQL 프로비전](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. 해당 서버 및 데이터베이스 요구 사항과 일치하도록 가격 책정 계층을 설정합니다. SQL Server 라이선스가 이미 있으므로 Azure 하이브리드 혜택으로 비용을 절약하도록 선택합니다.
5. 크기 조정의 경우 V 코어 기반 구매를 사용하며, 필요한 요구 사항에 대한 제한을 설정합니다.

    ![SQL 프로비전](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. 그런 다음, 데이터베이스 인스턴스를 만듭니다.

    ![SQL 프로비전](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. 인스턴스가 만들어진 후 데이터베이스를 열고, 마이그레이션에 Database Migration Assistance를 사용할 때 필요한 세부 정보를 기록합니다.

    ![SQL 프로비전](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**도움이 더 필요하세요?**

- SQL Database 프로비전에 대한 [도움말을 보세요](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- V 코어 리소스 제한에 [대해 자세히 알아봅니다](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools).


## <a name="step-2-migrate-the-database-with-dma"></a>2단계: DMA를 사용하여 데이터베이스 마이그레이션

Contoso 관리자는 이제 DMA를 사용하여 SmartHotel360 데이터베이스를 마이그레이션할 수 있습니다.

### <a name="install-dma"></a>DMA 설치

1. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53595)에서 온-프레미스 SQL Server VM(**SQLVM**)으로 도구를 다운로드합니다.
2. VM에서 설치(DownloadMigrationAssistant.msi)를 실행합니다.
3. **마침** 페이지에서 마법사를 마치기 전에 **Microsoft Data Migration Assistant 시작**을 선택합니다.

### <a name="migrate-the-database-with-dma"></a>DMA를 사용하여 데이터베이스 마이그레이션

1. DMA에서 새 프로젝트(**SmartHotelDB**)를 만들고 **마이그레이션**을 선택합니다. 
2. 원본 서버 유형을 **SQL Server**로 선택하고, 대상을 **Azure SQL Database**로 지정합니다. 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. 마이그레이션 세부 정보에서 **SQLVM**을 원본 서버로 추가하고 **SmartHotel.Registration** 데이터베이스를 추가합니다. 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. 인증으로 연결된 것처럼 보이는 오류가 발생합니다. 그러나 문제를 조사한 후 문제는 데이터베이스 이름의 마침표(.)입니다. 해결 방법으로 문제를 해결하기 위해 **SmartHotel-Registration**이라는 이름을 사용하여 새 SQL 데이터베이스를 프로비전하도록 결정했습니다. DMA를 다시 실행할 때 **SmartHotel-Registration**을 선택하고, 마법사를 계속해서 사용할 수 있습니다.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. **개체 선택**에서 데이터베이스 테이블을 선택하고, SQL 스크립트를 생성합니다.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. DMS에서 스크립트를 만든 후 **스키마 배포**를 클릭합니다.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. DMA는 배포가 성공했는지 확인합니다.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. 이제 마이그레이션을 시작합니다.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. 마이그레이션이 완료된 후 Contoso 관리자는 데이터베이스가 Azure SQL 인스턴스에서 실행되는지 확인할 수 있습니다.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Azure Portal에서 추가 SQL 데이터베이스 **SmartHotel.Registration**을 삭제합니다.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>3단계: Web Apps 프로비전

마이그레이션된 데이터베이스를 사용하여 Contoso 관리자는 이제 두 개의 웹앱을 프로비전할 수 있습니다.

1. 포털에서 **웹앱**을 선택합니다.

    ![웹앱](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. 앱 이름(**SHWEB-EUS2**)을 제공하고, Windows에서 실행하고, 프로덕션 리소스 그룹 **ContosoRG**에 배치합니다. 새 앱 서비스 및 계획을 만듭니다.

    ![웹앱](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. 웹앱이 프로비전된 후 WCF 서비스에 대한 웹앱을 만드는 프로세스를 반복합니다(**SHWCF-EUS2**).

    ![웹앱](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. 작업이 완료되면 성공적으로 생성되었는지 확인하도록 앱의 주소로 이동합니다.


## <a name="step-4-set-up-azure-devops"></a>4단계: Azure DevOps 설정


Contoso는 애플리케이션에 대한 DevOps 인프라 및 파이프라인을 빌드해야 합니다.  이를 위해 Contoso 관리자는 새 DevOps 프로젝트를 만들고 코드를 가져온 다음 빌드 및 릴리스 파이프라인을 설정합니다.

1. Contoso Azure DevOps 계정에서 새 프로젝트(**ContosoSmartHotelRefactor**)를 만들고 버전 제어용 **Git**을 선택합니다.

   ![새 프로젝트](./media/contoso-migration-refactor-web-app-sql/vsts1.png)
2. 현재 해당 앱 코드를 포함하는 Git 리포지토리를 가져옵니다. [공용 리포지토리](https://github.com/Microsoft/SmartHotel360-internal-booking-apps)에 있으며 다운로드할 수 있습니다.

    ![앱 코드 다운로드](./media/contoso-migration-refactor-web-app-sql/vsts2.png)
    
3. 코드를 가져온 후 Visual Studio를 리포지토리를 연결하고, 팀 탐색기를 사용하여 코드를 복제합니다.

    ![프로젝트에 연결](./media/contoso-migration-refactor-web-app-sql/devops1.png)

4. 개발자 머신에 리포지토리가 복제된 후 해당 앱에 대한 솔루션 파일을 엽니다. 웹앱 및 wcf 서비스는 각각 파일 내에서 별도의 프로젝트를 갖습니다.

    ![솔루션 파일](./media/contoso-migration-refactor-web-app-sql/vsts4.png)
    

## <a name="step-5-configure-connection-strings"></a>5단계: 연결 문자열 구성

Contoso 관리자는 웹앱 및 데이터베이스가 모두 통신할 수 있도록 해야 합니다. 이렇게 하려면 코드 및 웹앱에서 연결 문자열을 구성합니다.

1. WCF 서비스에 대한 웹앱(**SHWCF-EUS2**) > **설정** > **응용 프로그램 설정**에서 **DefaultConnection**이라는 새 연결 문자열을 추가합니다.
2. **SmartHotel-Registration** 데이터베이스에서 연결 문자열을 끌어오며, 올바른 자격 증명을 사용하여 업데이트되어야 합니다.

    ![연결 문자열](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Visual Studio를 사용하여 솔루션 파일에서 **SmartHotel.Registration.wcf** 프로젝트를 엽니다. WCF 서비스 SmartHotel.Registration.Wcf에 대한 web.config 파일의 **connectionStrings** 섹션은 연결 문자열로 업데이트되어야 합니다.

     ![연결 문자열](media/contoso-migration-refactor-web-app-sql/string2.png)

4. SmartHotel.Registration.Web에 대한 web.config 파일의 **client** 섹션은 WCF 서비스의 새 위치를 가리키도록 변경되어야 합니다. 서비스 엔드포인트를 호스팅하는 WCF 웹앱의 URL입니다.

    ![연결 문자열](media/contoso-migration-refactor-web-app-sql/strings3.png)

5. 코드에 변경 사항이 있으면 관리자는 변경 내용을 커밋해야 합니다. 팀 탐색기를 사용 하 여 Visual Studio에서, 이러한 커밋하고 동기화 합니다.


## <a name="step-6-set-up-build-and-release-pipelines-in-azure-devops"></a>6단계: Azure DevOps에서 빌드 및 릴리스 파이프라인 설정

Contoso 관리자는 이제 빌드 및 릴리스 프로세스를 수행하도록 Azure DevOps를 구성합니다.

1. 이를 위해 Azure DevOps에서 **빌드 및 릴리스** > **새 파이프라인**을 클릭합니다.

    ![새 파이프라인](./media/contoso-migration-refactor-web-app-sql/pipeline1.png)

2. **Azure Repos Git** 및 관련 리포지토리를 선택합니다.

    ![Git 및 리포지토리](./media/contoso-migration-refactor-web-app-sql/pipeline2.png)

3. **템플릿을 선택**에서 해당 빌드에 대한 ASP.NET 템플릿을 선택합니다.

     ![ASP.NET 템플릿](./media/contoso-migration-refactor-web-app-sql/pipeline3.png)
    
4. 빌드 이름으로는 **ContosoSmartHotelRefactor-ASP.NET-CI**가 사용됩니다. **저장 및 큐에 넣기**를 클릭합니다.

     ![저장 및 큐](./media/contoso-migration-refactor-web-app-sql/pipeline4.png)

5. 첫 번째 빌드가 시작됩니다. 프로세스를 보려면 빌드 번호를 클릭합니다. 빌드가 완료되면 프로세스 피드백을 확인한 후 **아티팩트**를 클릭하여 빌드 결과를 확인할 수 있습니다.

    ![검토](./media/contoso-migration-refactor-web-app-sql/pipeline5.png)

6. **Drop** 폴더에 빌드 결과가 포함되어 있습니다.

   - 두 zip 파일은 앱을 포함하는 패키지입니다.
   - 이러한 파일은 Azure Web Apps에 배포하기 위한 릴리스 파이프라인에서 사용됩니다.

     ![아티팩트](./media/contoso-migration-refactor-web-app-sql/pipeline6.png)

7. **릴리스** > **+새 파이프라인**을 클릭합니다.

    ![새 파이프라인](./media/contoso-migration-refactor-web-app-sql/pipeline7.png)

8. Azure App Service 배포 템플릿을 선택합니다.

    ![Azure App Service 템플릿](./media/contoso-migration-refactor-web-app-sql/pipeline8.png)

9. 릴리스 파이프라인의 이름을 **ContosoSmartHotel360Refactor**로 지정하고 **단계** 이름으로는 WCF 웹앱의 이름(SHWCF-EUS2)을 지정합니다.

    ![Environment](./media/contoso-migration-refactor-web-app-sql/pipeline9.png)

10. 단계 아래에서 **1개 작업, 1개 태스크**를 클릭하여 WCF 서비스의 배포를 구성합니다.

    ![WCF 배포](./media/contoso-migration-refactor-web-app-sql/pipeline10.png)

11. 구독이 선택되고 권한이 부여되었는지 확인하고 **앱 서비스 이름**을 선택합니다.

     ![앱 서비스 선택](./media/contoso-migration-refactor-web-app-sql/pipeline11.png)

12. 파이프라인 > **아티팩트**에서 **+아티팩트 추가**를 선택하고 **ContosoSmarthotel360Refactor** 파이프라인으로 빌드하도록 선택합니다.

     ![빌드](./media/contoso-migration-refactor-web-app-sql/pipeline12.png)

15. 아티팩트에서 번개를 클릭하여 지속적인 배포 트리거를 사용하도록 설정합니다.

     ![번개](./media/contoso-migration-refactor-web-app-sql/pipeline13.png)

16. 또한 연속 배포 트리거가 **사용**으로 설정되어야 합니다.

    ![연속 배포 사용](./media/contoso-migration-refactor-web-app-sql/pipeline14.png) 

17. 이제 단계 1개 작업, 1개 태스크로 돌아가서 **Azure App Service 배포**를 클릭합니다.

    ![앱 서비스 배포](./media/contoso-migration-refactor-web-app-sql/pipeline15.png)

18. **파일 또는 폴더 선택**에서 빌드 중 생성된 **SmartHotel.Registration.Wcf.zip** 파일을 찾고 **저장**을 클릭합니다.

    ![WCF 저장](./media/contoso-migration-refactor-web-app-sql/pipeline16.png)

19. **파이프라인** > **단계** **+추가**를 클릭하여 **SHWEB-EUS2**용 환경을 추가합니다. 그리고 다른 Azure App Service 배포를 선택합니다.

    ![환경 추가](./media/contoso-migration-refactor-web-app-sql/pipeline17.png)

20. 웹앱(**SmartHotel.Registration.Web.zip**) 파일을 올바른 웹앱에 게시하는 프로세스를 반복합니다.

    ![웹앱 게시](./media/contoso-migration-refactor-web-app-sql/pipeline18.png)

21. 저장된 후 릴리스 파이프라인이 다음과 같이 표시됩니다.

     ![릴리스 파이프라인 요약](./media/contoso-migration-refactor-web-app-sql/pipeline19.png)

22. 다시 **빌드**로 이동하여 **트리거** > **지속적인 통합 사용**을 클릭합니다. 그러면 파이프라인이 사용하도록 설정되어 코드에 변경 내용이 커밋되는 경우 전체 빌드 및 릴리스가 발생합니다.

    ![지속적인 통합 사용](./media/contoso-migration-refactor-web-app-sql/pipeline20.png)

23. **저장 및 큐**를 클릭하여 전체 파이프라인을 실행합니다. 결과적으로 Azure App Service에 앱의 첫 번째 릴리스를 만드는 새 빌드가 트리거됩니다.

    ![파이프라인 저장](./media/contoso-migration-refactor-web-app-sql/pipeline21.png)

24. Contoso 관리자는 Azure DevOps에서 빌드 및 릴리스 파이프라인 프로세스를 수행할 수 있습니다. 빌드가 완료되면 릴리스가 시작됩니다.

    ![빌드 및 릴리스 앱](./media/contoso-migration-refactor-web-app-sql/pipeline22.png)

25. 파이프라인이 완료된 후 두 사이트가 배포되고 앱이 작동되고 온라인에서 실행됩니다.

    ![릴리스 완료](./media/contoso-migration-refactor-web-app-sql/pipeline23.png)

이 시점에서 앱은 Azure로 성공적으로 마이그레이션되었습니다.



## <a name="clean-up-after-migration"></a>마이그레이션 후 정리

마이그레이션 후 Contoso는 다음 정리 단계를 완료해야 합니다.  

- vCenter 인벤토리에서 온-프레미스 VM을 제거합니다.
- 로컬 백업 작업에서 VM을 제거합니다.
- 내부 설명서를 업데이트하여 SmartHotel360 앱의 새 위치를 표시합니다. Azure SQL 데이터베이스에서 실행되는 데이터베이스 및 두 개의 웹앱에서 실행되는 프런트 엔드를 표시합니다.
- 서비스 해제된 VM과 상호 작용하는 모든 리소스를 검토하고, 새 구성을 반영하도록 모든 관련 설정 또는 설명서를 업데이트합니다.


## <a name="review-the-deployment"></a>배포 검토

Azure에서 마이그레이션된 리소스를 사용하여 Contoso는 새 인프라를 완전하게 운영하고 보호해야 합니다.

### <a name="security"></a>보안

- Contoso는 새 **SmartHotel-Registration** 데이터베이스가 안전한지 확인해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- 특히 Contoso는 인증서로 SSL을 사용하도록 웹앱을 업데이트해야 합니다.

### <a name="backups"></a>Backup

- Contoso는 Azure SQL Database에 대한 백업 요구 사항을 검토해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso는 또한 SQL 데이터베이스 백업 및 복원 관리에 대해 학습해야 합니다. 자동 작업에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso는 데이터베이스에 대한 지역별 장애 조치(failover)를 제공하는 장애 조치(failover) 그룹 구현을 고려해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso는 복원력을 위해 기본 미국 동부 2 및 미국 중부 지역에서 웹앱 배포를 고려해야 합니다. Contoso는 지역 가동 중단 발생 시 장애 조치(failover)하도록 Traffic Manager를 구성할 수 있습니다.

### <a name="licensing-and-cost-optimization"></a>라이선스 및 비용 최적화

- 모든 리소스가 배포된 후 Contoso는 해당 [인프라 계획](contoso-migration-infrastructure.md#set-up-tagging)을 따라 Azure 태그를 할당해야 합니다.
- 모든 라이선스는 Contoso가 사용하는 PaaS 서비스의 비용으로 빌드됩니다. 이 요금은 EA에서 공제됩니다.
- Contoso는 Microsoft 자회사인 Cloudyn에서 허가된 Azure Cost Management를 사용하도록 설정합니다. Azure 및 기타 클라우드 리소스를 활용하고 관리하는 데 도움이 되는 다중 클라우드 비용 관리 솔루션입니다.  Azure Cost Management에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/cost-management/overview).

## <a name="conclusion"></a>결론

이 문서에서 Contoso는 앱 프런트 엔드 VM을 두 개의 Azure Web Apps로 마이그레이션하여 Azure에서 SmartHotel360 앱을 리팩터링했습니다. 앱 데이터베이스는 Azure SQL 데이터베이스로 마이그레이션되었습니다.






