---
title: Azure에서 Team Foundation Server 배포를 Azure DevOps Services로 리팩터링 | Microsoft Docs
description: Contoso가 Azure에서 온-프레미스 TFS 배포를 Azure DevOps Services로 마이그레이션하여 리팩터링하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 21396a10543d388b6ac360f426272f1841b2f510
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60674226"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-azure-devops-services"></a>Contoso 마이그레이션:  Team Foundation Server 배포를 Azure DevOps Services로 리팩터링

이 문서에서는 Contoso가 Azure에서 온-프레미스 TFS(Team Foundation Server) 배포를 Azure DevOps Services로 마이그레이션하여 리팩터링하는 방법을 보여 줍니다. Contoso의 개발 팀은 지난 5년 동안 TFS를 팀 협업 및 소스 제어에 사용했습니다. 이제 Contoso는 개발 및 테스트 작업과 소스 제어에 클라우드 기반 솔루션을 사용하도록 바꾸려고 합니다. Contoso가 Azure DevOps 모델로 전환하고 새로운 클라우드 네이티브 앱을 개발하는 과정에서 Azure DevOps Services가 역할을 합니다.

이 문서는 가상 회사 Contoso가 온-프레미스 리소스를 Microsoft Azure 클라우드로 마이그레이션하는 방법을 보여주는 문서 시리즈 중 하나입니다. 시리즈에는 배경 정보 및 마이그레이션 인프라를 설정하고 다양한 유형의 마이그레이션을 실행하는 방법을 보여 주는 시나리오가 포함되어 있습니다. 시나리오가 복잡해지고 있으며, 앞으로 계속해서 문서가 추가될 것입니다.

**문서** | **세부 정보** | **상태**
--- | --- | ---
[문서 1: 개요](contoso-migration-overview.md) | Contoso 마이그레이션 전략, 문서 시리즈 및 사용할 샘플 앱에 대해 간략히 설명합니다. | 사용 가능
[문서 2: Azure 인프라 배포](contoso-migration-infrastructure.md) | Contoso에서 마이그레이션을 위해 온-프레미스 및 Azure 인프라를 준비하는 방법에 대해 설명합니다. 동일한 인프라가 모든 Contoso 마이그레이션 시나리오에 사용됩니다. | 사용 가능
[문서 3: 온-프레미스 리소스 평가](contoso-migration-assessment.md)  | VMware에서 실행되는 온-프레미스 2계층 SmartHotel 앱 평가를 Contoso에서 실행하는 방법을 보여 줍니다. [Azure Migrate](migrate-overview.md) 서비스를 사용하여 앱 VM을 평가하고, [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)를 사용하여 앱 SQL Server 데이터베이스를 평가합니다. | 사용 가능
[문서 4: Azure VM 및 SQL Managed Instance에 다시 호스트](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso에서 SmartHotel 앱을 Azure로 마이그레이션하는 방법을 보여 줍니다. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하여 앱 웹 VM을 마이그레이션하고 [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) Service를 사용하여 앱 데이터베이스를 SQL Managed Instance로 마이그레이션합니다. | 사용 가능
[문서 5: Azure VM에 다시 호스트](contoso-migration-rehost-vm.md) | Contoso가 Site Recovery 서비스를 사용하여 SmartHotel을 Azure IaaS VM으로 마이그레이션하는 방법을 보여 줍니다.
[문서 6: Azure VM 및 SQL Server 가용성 그룹에 다시 호스트](contoso-migration-rehost-vm-sql-ag.md) | Contoso에서 SmartHotel 앱을 마이그레이션하는 방법을 보여 줍니다. Site Recovery와 Database Migration Service를 사용하여 각각 앱 VM과 앱 데이터베이스를 SQL Server 가용성 그룹으로 마이그레이션합니다. | 사용 가능
[문서 7: Azure VM에 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm.md) | Contoso가 Azure Site Recovery를 사용하여 osTicket Linux 앱을 Azure IaaS VM으로 마이그레이션하는 방법을 보여 줍니다.
[문서 8: Azure VM 및 Azure MySQL 서버에 Linux 앱 다시 호스팅](contoso-migration-rehost-linux-vm-mysql.md) | Contoso가 osTicket Linux 앱을 마이그레이션하는 방법을 보여 줍니다. Site Recovery를 사용하여 VM을 마이그레이션하고 MySQL Workbench를 사용하여 Azure MySQL 서버 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 9: 앱을 Azure 웹앱 및 Azure SQL Database로 리팩터링](contoso-migration-refactor-web-app-sql.md) | Contoso가 SmartHotel 앱을 Azure 컨테이너 기반 웹앱으로 마이그레이션하고, 앱 데이터베이스를 Azure SQL Server로 마이그레이션하는 방법을 보여줍니다. | 사용 가능
[문서 10: Linux 앱을 Azure App Service 및 Azure MySQL Server로 리팩터링](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso가 PHP 7.0 Docker 컨테이너를 사용하여 osTicket Linux 앱을 Azure App Service로 마이그레이션하는 방법을 보여줍니다. 배포용 코드 베이스는 GitHub로 마이그레이션됩니다. 앱 데이터는 Azure MySQL로 마이그레이션됩니다. | 사용 가능
문서 11: Azure DevOps Services에서 TFS 배포 리팩터링 | Azure에서 개발 앱 TFS를 Azure DevOps Services로 마이그레이션 | 이 문서의 내용
[문서 12: Azure 컨테이너 및 Azure SQL Database에서 앱 재설계](contoso-migration-rearchitect-container-sql.md) | Contoso가 SmartHotel 앱을 Azure로 마이그레이션하고 아키텍처를 변경하는 방법을 보여줍니다. 웹앱 계층을 Windows 컨테이너 및 Azure SQL Database의 앱 데이터베이스로 아키텍처를 변경합니다. | 사용 가능
[문서 13: Azure에서 앱 다시 빌드](contoso-migration-rebuild.md) | Contoso가 App Services, Azure Kubernetes, Azure Functions, Cognitive services 및 Cosmos DB를 포함한 다양한 Azure 기능 및 서비스를 사용하여 자체 SmartHotel 앱을 다시 빌드하는 방법을 보여줍니다. | 사용 가능
[문서 14: Azure로의 마이그레이션 확장](contoso-migration-scale.md) | 마이그레이션 조합을 시도한 후 Contoso는 Azure로 전체 마이그레이션을 확장할 준비를 합니다. | 사용 가능


## <a name="business-drivers"></a>비즈니스 영향 요소

IT 리더십 팀은 비즈니스 파트너와 긴밀하게 협력하여 향후 목표를 식별했습니다. 파트너는 개발 도구 및 기술에 별로 관심이 없지만, 다음과 같은 사항을 포착했습니다.

- **소프트웨어**: Contoso를 포함한 모든 회사는 핵심 비즈니스와 무관하게 소프트웨어 회사입니다. 회사 경영진은 IT가 어떻게 사용자에 대한 새로운 작업 관례 및 고객 경험의 측면에서 회사에 도움이 될 수 있는지에 관심이 있습니다.
- **효율성**: Contoso는 프로세스를 간소화하고 개발자 및 사용자에 대한 불필요한 절차를 제거해야 합니다. 이렇게 하면 회사에서 고객 요구 사항에 더 효율적으로 부응할 수 있습니다. 회사에서는 시간 또는 비용 낭비 없는 빠른 IT를 원합니다.
- **민첩성**:  글로벌 경제에서 성공을 거두려면 Contoso IT는 마켓플레이스보다 빠르게 비즈니스 요구 사항에 대응하고 반응해야 합니다. IT는 비즈니스의 방해 요소가 되어서는 안 됩니다.

## <a name="migration-goals"></a>마이그레이션 목표

Contoso 클라우드 팀은 Azure DevOps Services로 마이그레이션한다는 목표를 세웠습니다.

- 팀에는 데이터를 클라우드로 마이그레이션하기 위한 도구가 필요합니다. 수동 프로세스가 거의 없어야 합니다.
- 작년의 작업 항목 데이터 및 기록을 마이그레이션해야 합니다.
- 사용자 이름 및 암호를 새로 설정하기를 원하지 않습니다. 모든 현재 시스템 할당이 유지되어야 합니다.
- TFVC(Team Foundation 버전 제어)에서 소스 제어용 Git으로 전환하기를 원합니다.
- Git으로의 전환은 최신 버전의 소스 코드만 가져오는 "팁 마이그레이션"이 될 것입니다. 코드 베이스가 변경되면 모든 작업이 중단되는 가동 중지 시간에 마이그레이션이 진행됩니다. Contoso는 이동 후에는 현재 마스터 분기 기록만 사용할 수 있다는 것을 이해합니다.
- 변화에 대해 염려하고 전체 이동을 수행하기 전에 테스트하기를 원합니다. Azure DevOps Services로 이동한 후에도 TFS에 계속 액세스하기를 바랍니다.
- 여러 컬렉션을 갖고 있으며, 프로세스를 보다 정확하게 이해하기 위해 프로젝트 몇 개만 있는 컬렉션으로 시작하려 합니다.
- TFS 컬렉션이 Azure DevOps Services 조직과 일대일 관계이므로 URL이 여러 개라는 것을 이해합니다. 하지만 이것은 코드 베이스 및 프로젝트에 대한 현재 모델 분류와 일치합니다.


## <a name="proposed-architecture"></a>제안된 아키텍처

- Contoso는 TFS 프로젝트를 클라우드로 이동할 것이며, 더 이상 프로젝트 또는 소스 제어를 온-프레미스에 호스트하지 않습니다.
- TFS는 Azure DevOps Services로 마이그레이션됩니다.
- 현재 Contoso는 **ContosoDev**라는 TFS 컬렉션을 갖고 있으며, 이 컬렉션은 **contosodevmigration.visualstudio.com**이라는 Azure DevOps Services 조직으로 마이그레이션됩니다.
- 작년의 프로젝트, 작업 항목, 버그 및 반복은 Azure DevOps Services로 마이그레이션됩니다.
- Contoso는 마이그레이션 계획 초기에 [Azure 인프라를 배포](contoso-migration-infrastructure.md)할 때 설정한 Azure Active Directory를 활용할 예정입니다. 


![시나리오 아키텍처](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>마이그레이션 프로세스

Contoso는 다음과 같이 마이그레이션 프로세스를 완료합니다.

1. 준비할 것이 많습니다. 첫 번째 단계로, Contoso는 TFS 구현을 지원되는 수준으로 업그레이드해야 합니다. Contoso는 현재 TFS 2017 업데이트 3를 실행 중이지만, 데이터베이스 마이그레이션을 사용하려면 최신 업데이트가 설치된 지원되는 2018 버전을 실행해야 합니다.
2. 업그레이드 후에는 Contoso에서 TFS 마이그레이션 도구를 실행하고 컬렉션의 유효성을 검사할 것입니다.
3. Contoso는 일련의 준비 파일을 빌드하고, 테스트를 위한 마이그레이션 시험 실행을 수행할 것입니다.
4. 그 후에는 Contoso에서 작업 항목, 버그, 스프린트 및 코드가 포함된 전체 마이그레이션을 실행할 것입니다.
5. 마이그레이션 후에는 Contoso는 TFVC에서 Git으로 코드를 이동할 것입니다.

![마이그레이션 프로세스](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>시나리오 단계

Contoso에서 마이그레이션을 수행하는 방법은 다음과 같습니다.

> [!div class="checklist"]
> * **1단계: Azure Storage 계정 만들기**: 이 스토리지 계정은 마이그레이션 프로세스에서 사용됩니다.
> * **2단계: TFS 업그레이드**: Contoso는 배포를 TFS 2018 업그레이드 2로 업그레이드합니다. 
> * **3단계: 컬렉션 유효성 검사**: Contoso는마이그레이션 준비 과정으로 TFS 컬렉션의 유효성을 검사합니다.
> * **4단계: 준비 파일 빌드**: Contoso는 TFS 마이그레이션 도구를 사용하여 마이그레이션 파일을 만듭니다. 


## <a name="step-1-create-a-storage-account"></a>1단계: 저장소 계정 만들기

1. Azure Portal에서 Contoso 관리자는 저장소 계정(**contosodevmigration**)을 만듭니다.
2. 장애 조치(failover)에 사용하는 보조 지역인 미국 중부에 계정을 배치합니다. 로컬 중복 스토리지에서 범용 표준 계정을 사용합니다.

    ![Storage 계정](./media/contoso-migration-tfs-vsts/storage1.png) 


**도움이 더 필요하세요?**

- [Azure 저장소 소개](https://docs.microsoft.com/azure/storage/common/storage-introduction)
- [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)


## <a name="step-2-upgrade-tfs"></a>2단계: TFS 업그레이드

Contoso 관리자는 TFS 서버를 TFS 2018 업데이트 2로 업그레이드합니다. 시작하기 전에 할 일:

- [TFS 2018 업데이트 2](https://visualstudio.microsoft.com/downloads/)를 다운로드합니다.
- [하드웨어 요구 사항](https://docs.microsoft.com/tfs/server/requirements)을 확인하고, [릴리스 정보](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes) 및 [업그레이드 문제](https://docs.microsoft.com/tfs/server/upgrade/get-started#before-you-upgrade-to-tfs-2018)를 읽어봅니다.

다음과 같이 업그레이드합니다.

1. 시작하기 위해, TFS 서버(VMware vM에서 실행 중인)를 백업하고 VMware 스냅숏을 만듭니다.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png) 

2. TFS 설치 관리자가 시작되고, Contoso는 설치 위치를 선택합니다. 설치 관리자가 인터넷에 액세스할 수 있어야 합니다.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png) 

3. 설치가 완료되면 서버 구성 마법사가 시작됩니다.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png) 

4. 확인을 마치면 마법사가 업그레이드를 완료합니다.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png) 

5. 프로젝트, 작업 항목 및 코드를 검토하여 TFS 설치를 확인합니다.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> 일부 TFS 업그레이드는 업그레이드 완료 후 기능 구성 마법사를 실행해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/devops/reference/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts).

**도움이 더 필요하세요?**

[TFS 업그레이드](https://docs.microsoft.com/tfs/server/upgrade/get-started)에 대해 알아보세요.

## <a name="step-3-validate-the-tfs-collection"></a>3단계: TFS 컬렉션 유효성 검사

Contoso 관리자는 마이그레이션 전에 ContosoDev 컬렉션 데이터베이스를 대상으로 TFS 마이그레이션 도구를 실행하여 유효성을 검사합니다.

1. [TFS 마이그레이션 도구](https://www.microsoft.com/download/details.aspx?id=54274)를 다운로드하고 압축을 풉니다. 실행 중인 TFS 업데이트에 맞는 버전을 다운로드해야 합니다. 버전은 관리 콘솔에서 확인할 수 있습니다.

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. Contoso는 도구를 실행하고 프로젝트 컬렉션의 URL을 지정하여 유효성을 검사합니다.

   **TfsMigrator validate /collection:http:\//contosotfs:8080/tfs/ContosoDev
**


3. 도구가 오류를 표시합니다.

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. 로그 파일이 도구 위치 바로 앞인 **Logs** 폴더에 있다는 것을 확인합니다. 주요 유효성 검사마다 로그 파일이 생성됩니다. **TfsMigration.log**는 주요 정보를 보관합니다.

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. ID와 관련된 이 항목을 찾습니다.

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. 명령줄에서 **TfsMigration validate /help**를 실행하고, ID의 유효성을 검사하려면 **/tenantDomainName**이 필요한 것을 확인합니다.

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. 유효성 검사 명령을 다시 실행하는데, 이번에는 Azure AD 이름과 함께 다음 값을 포함합니다. **TfsMigrator validate /collection:http:\//contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com.
**

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. Azure AD 로그인 화면이 나타나고, Contoso는 전역 관리 사용자의 자격 증명을 입력합니다.

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. 유효성 검사를 통과하고 도구에 의해 확인됩니다.

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>4단계: 마이그레이션 파일 만들기

유효성 검사가 완료되면 Contoso 관리자는 TFS 마이그레이션 도구를 사용하여 마이그레이션 파일을 빌드할 수 있습니다.

1. 도구에서 준비 단계를 실행합니다.

    **TfsMigrator prepare /collection:http:\//contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus
**

     ![준비](./media/contoso-migration-tfs-vsts/prep1.png)

    준비 작업은 다음과 같습니다.
    - 컬렉션을 검사하고 모든 사용자 목록을 찾아서 ID 맵 로그(**IdentityMapLog.csv**])를 채웁니다.
    - Azure Active Directory에 대한 연결을 준비하고 각 ID와 일치하는 항목을 찾습니다.
    - Contoso는 이미 AD Connect를 사용하여 Azure AD를 배포하고 동기화했기 때문에 준비 과정에서 일치하는 ID를 찾아서 활성으로 표시할 수 있습니다.

2. Azure AD 로그인 화면이 표시되면 글로벌 관리자의 자격 증명을 입력합니다.

    ![준비](./media/contoso-migration-tfs-vsts/prep2.png)

3. 준비가 완료되고, 도구에서는 가져오기 파일이 성공적으로 생성되었다고 보고합니다.

    ![준비](./media/contoso-migration-tfs-vsts/prep3.png)

4. 이제 IdentityMapLog.csv 및 import.json 파일이 새 폴더에 만들어진 것을 볼 수 있습니다.

    ![준비](./media/contoso-migration-tfs-vsts/prep4.png)

5. Import.json 파일은 가져오기 설정을 제공합니다. 이 파일에는 원하는 조직 이름, 저장소 계정 정보 등의 정보가 포함됩니다. 대부분의 필드가 자동으로 채워집니다. 일부 필드에는 사용자 입력이 필요합니다. Contoso는 파일을 열고, 만들려는 Azure DevOps Services 조직 이름 **contosodevmigration**을 추가합니다. 이 이름을 추가하면 Azure DevOps Services URL은 **contosodevmigration.visualstudio.com**이 됩니다.

    ![준비](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > 마이그레이션 전에 조직을 만들어야 하며, 마이그레이션이 완료되면 변경할 수 있습니다.

6. 가져오기를 수행하는 동안 Azure DevOps Services로 가져올 계정을 표시하는 ID 로그 맵 파일을 검토합니다. 

   - 활성 ID는 가져오기 후 Azure DevOps Services에서 사용자가 될 ID를 참조합니다.
   - Azure DevOps Services에서 이러한 ID는 라이선스가 발급되고, 마이그레이션 후 조직에서 사용자로 표시됩니다.
   - 이러한 ID는 파일의 **예상 가져오기 상태** 열에 **활성**으로 표시됩니다.

     ![준비](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-azure-devops-services"></a>5단계: Azure DevOps Services로 마이그레이션

준비가 완료되면 Contoso 관리자는 이제 마이그레이션에 집중할 수 있습니다. 마이그레이션 후 버전 제어에 TFVC 대신 Git을 사용하도록 전환할 것입니다.

시작하기 전에 관리자는 개발 팀과 의논하여 마이그레이션 동안 컬렉션을 오프라인으로 전환할 가동 중지 시간을 예약합니다. 다음은 마이그레이션 프로세스의 단계입니다.

1. **컬렉션 분리**: 컬렉션이 연결되어 온라인 상태인 동안에는 컬렉션의 ID 데이터가 TFS 서버 구성 데이터베이스에 상주합니다. 컬렉션은 TFS 서버에서 분리되면 해당 ID 데이터의 복사본을 만들고, 전송할 컬렉션과 함께 패키징합니다. 이 데이터가 없으면 가져오기의 ID 부분을 실행할 수 없습니다. 가져오는 동안 발생한 변경 내용을 가져올 수 있는 방법이 없으므로 가져오기가 완료될 때까지는 컬렉션을 분리 상태로 유지하는 것이 좋습니다.
2. **백업 생성**: 마이그레이션 프로세스의 그다음 단계는 Azure DevOps Services로 가져올 수 있는 백업을 생성하는 것입니다. DACPAC(데이터 계층 애플리케이션 패키지)는 데이터베이스 변경 내용을 단일 파일에 패키징하고 다른 SQL 인스턴스에 배포할 수 있는 SQL Server 기능입니다. Azure DevOps Services에 직접 복원할 수도 있으며, 따라서 컬렉션 데이터를 클라우드로 가져오는 패키징 방법으로 사용됩니다. Contoso는 SqlPackage.exe 도구를 사용하여 DACPAC을 생성합니다. 이 도구는 SQL Server Data Tools에 포함되어 있습니다.
3. **스토리지에 업로드**: DACPAC가 만들어지면 Azure Storage에 업로드합니다. 업로드가 완료되면 SAS(공유 액세스 서명)를 얻게 되므로 TFS 마이그레이션 도구가 저장소에 액세스할 수 있습니다.
4. **가져오기 채우기**: 이제 Contoso는 DACPAC 설정을 포함하여 가져오기 파일에서 누락된 필드를 채울 수 있습니다. 시작하기에 앞서 **DryRun** 가져오기를 수행하고 싶다고 지정하여 모든 것이 정상적으로 작동하는지 확인한 후 전체 마이그레이션을 진행합니다.
5. **시험 실행**: 시험 실행 가져오기를 통해 컬렉션 마이그레이션을 테스트할 수 있습니다. 시험 실행은 수명이 제한되어 있고, 프로덕션 마이그레이션 실행 전에 삭제됩니다. 설정된 시간이 지나면 자동으로 삭제됩니다. 시험 실행이 삭제되는 시점에 대한 내용은 가져오기가 완료된 후 도착하는 성공 이메일에 포함되어 있습니다. 내용을 살펴보고 그에 따라 계획을 세우면 됩니다.
6. **프로덕션 마이그레이션 완료**: 시험 실행 마이그레이션이 완료되면 Contoso 관리자는 import.json을 업데이트하고 가져오기를 다시 실행하여 최종 마이그레이션을 수행합니다.



### <a name="detach-the-collection"></a>컬렉션 분리

시작하기에 앞서, Contoso 관리자는 컬렉션을 분리하기 전에 로컬 SQL Server 백업 및 TFS 서버의 VMware 스냅숏을 만듭니다.

1.  TFS 관리 콘솔에서 분리할 컬렉션(**ContosoDev**)을 선택합니다.

    ![마이그레이션](./media/contoso-migration-tfs-vsts/migrate1.png)

2. **일반**에서 **컬렉션 분리**를 선택합니다.

    ![마이그레이션](./media/contoso-migration-tfs-vsts/migrate2.png)

3. 팀 프로젝트 컬렉션 분리 마법사 > **서비스 메시지**에서, 컬렉션의 프로젝트에 연결하려고 시도하는 사용자에게 표시할 메시지를 입력합니다.

    ![마이그레이션](./media/contoso-migration-tfs-vsts/migrate3.png)

4. **분리 진행률**에서 진행률을 모니터링하다가 프로세스가 완료되면 **다음**을 클릭합니다.

    ![마이그레이션](./media/contoso-migration-tfs-vsts/migrate4.png)

5. **준비 검사**에서 검사가 완료되면 **분리**를 클릭합니다.

    ![마이그레이션](./media/contoso-migration-tfs-vsts/migrate5.png)

6. **닫기**를 클릭하여 마칩니다.

    ![마이그레이션](./media/contoso-migration-tfs-vsts/migrate6.png)

6. 컬렉션이 더 이상 TFS 관리 콘솔에서 참조되지 않습니다.

    ![마이그레이션](./media/contoso-migration-tfs-vsts/migrate7.png)


### <a name="generate-a-dacpac"></a>DACPAC 생성

Contoso는 Azure DevOps Services로 가져올 백업(DACPAC)을 만듭니다.

- SQL Server Data Tools의 SqlPackage.exe는 DACPAC을 만드는 데 사용됩니다. SQL Server Data Tools에 설치된 여러 SqlPackage.exe 버전이 있으며 120, 130, 140 같은 이름의 폴더 아래에 있습니다. 올바른 버전을 사용하여 DACPAC을 준비 하는 것이 반드시 합니다.
- TFS 2018 imports 140 폴더에서 이상 SqlPackage.exe를 사용 해야 합니다.  CONTOSOTFS의 경우 이 파일은 다음 폴더에 있습니다. **C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140**.


Contoso 관리자는 다음과 같이 DACPAC를 생성합니다.

1. 명령 프롬프트를 열고 SQLPackage.exe 위치로 이동합니다. 다음 명령을 입력하여 DACPAC를 생성합니다.

    **SqlPackage.exe /sourceconnectionstring:"Data Source=SQLSERVERNAME\INSTANCENAME;Initial Catalog=Tfs_ContosoDev;Integrated Security=True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData=true /p:IgnoreUserLoginMappings=true /p:IgnorePermissions=true /p:Storage=Memory** 

    ![Backup](./media/contoso-migration-tfs-vsts/backup1.png)

2. 명령을 실행하면 다음과 같은 메시지가 나타납니다.

    ![Backup](./media/contoso-migration-tfs-vsts/backup2.png)

3. DACPAC 파일의 속성을 확인합니다.

    ![Backup](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>파일을 저장소에 업데이트

DACPAC이 생성되면 Contoso는 DACPAC을 Azure Storage에 업로드합니다.

1. Azure Storage 탐색기를 [다운로드하고 설치](https://azure.microsoft.com/features/storage-explorer/)합니다.

    ![업로드](./media/contoso-migration-tfs-vsts/backup5.png)

4. 구독에 연결하며 마이그레이션용으로 만든 저장소 계정(**contosodevmigration**)을 찾습니다. 새 Blob 컨테이너 **azuredevopsmigration**을 만듭니다.

    ![업로드](./media/contoso-migration-tfs-vsts/backup6.png)

5. 블록 Blob으로 업로드할 DACPAC 파일을 지정합니다.

    ![업로드](./media/contoso-migration-tfs-vsts/backup7.png)
    
7. 파일이 업로드되면 파일 이름 > **SAS 생성**을 클릭합니다. 저장소 계정에서 Blob 컨테이너를 확장하고, 가져오기 파일이 포함된 컨테이너를 선택하고, **공유 액세스 서명 가져오기**를 클릭합니다.

    ![업로드](./media/contoso-migration-tfs-vsts/backup8.png)

8. 기본값을 수락하고 **만들기**를 클릭합니다. 이렇게 하면 24시간 동안 액세스할 수 있습니다.

    ![업로드](./media/contoso-migration-tfs-vsts/backup9.png)

9. TFS 마이그레이션 도구에서 사용할 수 있도록 공유 액세스 서명 URL을 복사합니다.

    ![업로드](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> 허용된 시간 또는 권한이 만료되기 전에 마이그레이션이 발생해야 합니다.
> Azure Portal에서 SAS 키를 생성하지 마세요. 그렇게 생성된 키는 범위가 계정으로 한정되므로 가져오기가 작동하지 않습니다.

### <a name="fill-in-the-import-settings"></a>가져오기 설정 입력

앞에서 Contoso 관리자는 가져오기 사양 파일(import.json)의 일부를 입력했습니다. 이제 나머지 설정을 추가해야 합니다.

Import.json 파일을 열고 다음 필드를 채웁니다. • Location: 위에서 생성된 SAS 키의 위치입니다.
• Dacpac: 스토리지 계정에 업로드한 DACPAC 파일로 이름을 설정합니다. ".dacpac" 확장명을 포함해야 합니다.
• ImportType: 지금은 DryRun으로 설정합니다.


![설정 가져오기](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>시험 실행 마이그레이션 수행

Contoso 관리자는 시험 실행 마이그레이션을 시작하여 모든 것이 예상대로 작동하는지 확인합니다.

1. 명령 프롬프트를 열고 TfsMigration 위치(C:\TFSMigrator)를 찾습니다.
2. 첫 번째 단계로 가져오기 파일의 유효성을 검사합니다. 파일 형식이 올바르게 지정되었는지, SAS 키가 제대로 작동하는지 확인해야 합니다.

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

3. 유효성 검사 결과, SAS 키의 만료 시간이 더 길어야 한다는 오류가 반환됩니다.

    ![시험 실행](./media/contoso-migration-tfs-vsts/test1.png)

3. Azure Storage 탐색기를 사용하여 만료 시간이 7일로 설정된 새 SAS 키를 만듭니다.

    ![시험 실행](./media/contoso-migration-tfs-vsts/test2.png)

3. import.json 파일을 업데이트하고 유효성 검사를 다시 실행합니다. 이번에는 성공적으로 완료됩니다.

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

    ![시험 실행](./media/contoso-migration-tfs-vsts/test3.png)
    
7. 시험 실행을 시작합니다.

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json**

8. 마이그레이션을 확인하는 메시지가 표시됩니다. 시험 실행 후 준비된 데이터가 유지되는 시간의 길이를 잘 보세요.

    ![시험 실행](./media/contoso-migration-tfs-vsts/test4.png)

9. Azure AD 로그인 화면이 나타나고, Contoso 관리자 로그인을 완료해야 합니다.

    ![시험 실행](./media/contoso-migration-tfs-vsts/test5.png)

10. 메시지는 가져오기에 대한 정보를 보여줍니다.

    ![시험 실행](./media/contoso-migration-tfs-vsts/test6.png)

11. 약 15분 후 URL로 이동하여 다음 정보를 봅니다.

     ![시험 실행](./media/contoso-migration-tfs-vsts/test7.png)

12. 마이그레이션이 완료된 후 Contoso 수석 개발자는 Azure DevOps Services에 로그인하여 시험 실행이 올바르게 진행되었는지 확인합니다. 인증 후 Azure DevOps Services는 조직을 확인하는 데 몇 가지 세부 정보가 필요합니다.

    ![시험 실행](./media/contoso-migration-tfs-vsts/test8.png)

13. Azure DevOps Services에서 수석 개발자는 프로젝트가 Azure DevOps Services로 마이그레이션되었음을 확인할 수 있습니다. 15일 후 조직이 삭제된다는 알림이 있습니다.

    ![시험 실행](./media/contoso-migration-tfs-vsts/test9.png)

14. 수석 개발자는 프로젝트 중 하나를 열고 **작업 항목** > **내게 할당됨**을 엽니다. ID와 함께 작업 항목 데이터가 마이그레이션되었다고 표시됩니다.

    ![시험 실행](./media/contoso-migration-tfs-vsts/test10.png)

15. 또한 수석 개발자는 다른 프로젝트와 코드도 검사하여 소스 코드 및 기록이 마이그레이션된 것을 확인합니다.

    ![시험 실행](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>프로덕션 마이그레이션 실행

시험 실행이 완료되면 Contoso 관리자는 프로덕션 마이그레이션으로 이동합니다. 시험 실행을 삭제하고, 가져오기 설정을 업데이트하고, 가져오기를 다시 실행합니다.

1. Azure DevOps Services 포털에서 시험 실행 조직을 삭제합니다.
2. import.json 파일을 업데이트하여 **ImportType**을 **ProductionRun**으로 설정합니다.

    ![프로덕션](./media/contoso-migration-tfs-vsts/full1.png)

3. 시험 실행에서 했던 것처럼 마이그레이션을 시작합니다. **TfsMigrator import /importFile:C:\TFSMigrator\import.json**.
4. 마이그레이션을 확인하는 메시지가 표시되고, 데이터가 최대 7일 동안 안전한 위치에 준비 영역으로 보관될 수 있다고 경고합니다.

    ![프로덕션](./media/contoso-migration-tfs-vsts/full2.png)

5. Azure AD 로그인에서 Contoso 관리자 로그인을 지정합니다.

    ![프로덕션](./media/contoso-migration-tfs-vsts/full3.png)

6. 메시지는 가져오기에 대한 정보를 보여줍니다.

    ![프로덕션](./media/contoso-migration-tfs-vsts/full4.png)

7. 약 15분 후 URL로 이동하여 다음 정보를 살펴봅니다.

    ![프로덕션](./media/contoso-migration-tfs-vsts/full5.png)

8. 마이그레이션이 완료된 후 Contoso 수석 개발자는 Azure DevOps Services에 로그온하여 마이그레이션이 올바르게 진행되었는지 확인합니다. 로그인 후 수석 개발자는 프로젝트가 마이그레이션된 것을 볼 수 있습니다.

    ![프로덕션](./media/contoso-migration-tfs-vsts/full6.png)

8. 수석 개발자는 프로젝트 중 하나를 열고 **작업 항목** > **내게 할당됨**을 엽니다. ID와 함께 작업 항목 데이터가 마이그레이션되었다고 표시됩니다.

    ![프로덕션](./media/contoso-migration-tfs-vsts/full7.png)

9. 수석 개발자는 다른 작업 항목을 검사하고 확인합니다.

    ![프로덕션](./media/contoso-migration-tfs-vsts/full8.png)

15. 또한 수석 개발자는 다른 프로젝트와 코드도 검사하여 소스 코드 및 기록이 마이그레이션된 것을 확인합니다.

    ![프로덕션](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>소스 제어를 TFVC에서 GIT으로 이동

마이그레이션이 완료된 후 Contoso는 소스 코드 관리를 TFVC에서 Git으로 이동하려 합니다. 현재 Azure DevOps Services 조직에 있는 소스 코드를 같은 조직의 Git 리포지토리로 가져와야 합니다.

1. Azure DevOps Services 포털에서 TFVC 리포지토리(**$/ PolicyConnect**) 중 하나를 열고 검토합니다.

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. **원본** 드롭다운 > **가져오기**를 클릭합니다.

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. **원본 유형**에서 **TFVC**를 선택하고 리포지토리 경로를 지정합니다. 기록은 마이그레이션하지 않기로 결정했습니다.

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > TFVC와 Git에서 버전 제어 정보를 저장하는 방법이 다르기 때문에 Contoso에서 기록을 마이그레이션하지 않는 것이 좋습니다. Microsoft가 Windows 및 기타 제품을 중앙 집중식 버전 제어에서 Git으로 마이그레이션할 때 이 방법을 사용합니다.

4. 가져온 후에 관리자는 코드를 검토합니다.

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. 두 번째 리포지토리(**$/ SmartHotelContainer**)에 대해 같은 프로세스를 반복합니다.

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. 소스 검토를 마친 후 수석 개발자는 Azure DevOps Services로 마이그레이션이 완료되었음에 동의합니다. 이제 Azure DevOps Services는 마이그레이션에 관련된 팀 내의 모든 개발에 대한 소스가 됩니다.

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)



**도움이 더 필요하세요?**

TFVC에서 가져오기에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/devops/repos/git/import-from-TFVC?view=vsts).

##  <a name="clean-up-after-migration"></a>마이그레이션 후 정리

마이그레이션이 완료된 후 Contoso는 다음을 수행해야 합니다.

- [가져오기 이후](https://docs.microsoft.com/azure/devops/articles/migration-post-import?view=vsts) 문서를 검토하여 추가 가져오기 작업에 대한 정보를 확인합니다.
- TFVC 리포지토리를 삭제하거나 읽기 전용 모드에 배치합니다. 코드 베이스를 사용하면 안 되고, 기록에 참조할 수는 있습니다.

## <a name="next-steps"></a>다음 단계

Contoso는 관련 팀 구성원에게 Azure DevOps Services 및 Git을 교육시켜야 합니다.



