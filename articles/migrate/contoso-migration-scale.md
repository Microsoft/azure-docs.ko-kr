---
title: Contoso - Azure 대상 마이그레이션 크기 조정 | Microsoft Docs
description: Contoso가 Azure 대상 크기 조정된 마이그레이션을 처리하는 방법을 알아봅니다.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: raynew
ms.openlocfilehash: 9253051d907a811ffedad3a714112c9b25543a35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60667450"
---
# <a name="contoso---scale-a-migration-to-azure"></a>Contoso - Azure 대상 마이그레이션 크기 조정

이 문서에서 Contoso는 Azure로 대규모 마이그레이션을 수행합니다. 이 회사는 워크로드 3000개 이상, 데이터베이스 8000개, VM 10,000개 이상의 마이그레이션을 계획 및 수행하는 방법을 고려합니다.


이 문서는 가상 회사인 Contoso가 온-프레미스 리소스를 Microsoft Azure 클라우드로 마이그레이션하는 방법을 설명하는 문서 시리즈 중 하나입니다. 이 시리즈에는 배경 및 계획 정보와 마이그레이션 인프라를 설정하고, 마이그레이션에 대한 온-프레미스 리소스의 적합성을 평가하고, 다양한 유형의 마이그레이션을 실행하는 방법을 설명하는 배포 시나리오가 포함되어 있습니다. 시나리오가 복잡해지고 있으며, 시간이 지남에 따라 관련 문서가 시리즈에 추가됩니다.


**문서** | **세부 정보** | **상태**
--- | --- | ---
[문서 1: 개요](contoso-migration-overview.md) | 문서 시리즈, Contoso의 마이그레이션 전략 및 시리즈에서 사용되는 샘플 앱에 대해 간략히 설명합니다. | 사용 가능
[문서 2: Azure 인프라 배포](contoso-migration-infrastructure.md) | Contoso에서 마이그레이션을 위해 온-프레미스 인프라와 Azure 인프라를 준비합니다. 이 시리즈의 모든 마이그레이션 관련 문서에서 동일한 인프라가 사용됩니다. | 사용 가능.
[문서 3: Azure로 마이그레이션하기 위한 온-프레미스 리소스 평가](contoso-migration-assessment.md)  | Contoso가 VMware에서 실행되는 온-프레미스 SmartHotel360 앱의 평가를 실행합니다. Contoso에서 Azure Migrate 서비스를 사용하여 앱 VM을 평가하고, Database Migration Assistant를 사용하여 앱 SQL Server 데이터베이스를 평가합니다. | 사용 가능
[문서 4: Azure VM 및 SQL Database Managed Instance에서 앱 다시 호스트](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso가 온-프레미스 SmartHotel360 앱을 Azure로 리프트 앤 시프트 방식으로 마이그레이션합니다. Contoso에서 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하여 앱 프런트 엔드 VM을 마이그레이션하고, [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)를 사용하여 앱 데이터베이스를 Azure SQL Database Managed Instance로 마이그레이션합니다. | 사용 가능   
[문서 5: Azure VM에서 앱 다시 호스트](contoso-migration-rehost-vm.md) | Contoso에서 Site Recovery 서비스를 사용하여 SmartHotel360 앱 VM을 Azure VM으로 마이그레이션합니다. | 사용 가능
[문서 6: Azure VM 및 SQL Server AlwaysOn 가용성 그룹에서 앱 다시 호스트](contoso-migration-rehost-vm-sql-ag.md) | Contoso에서 Site Recovery를 사용하여 앱을 마이그레이션하여 앱 VM을 마이그레이션하고, Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹에서 보호되는 SQL Server 클러스터로 마이그레이션합니다. | 사용 가능
[문서 7: Azure VM에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm.md) | Contoso에서 Site Recovery 서비스를 사용하여 Linux osTicket 앱을 Azure VM으로 리프트 앤 시프트 방식으로 마이그레이션합니다. | 사용 가능
[문서 8: Azure VM 및 Azure Database for MySQL에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm-mysql.md) | Contoso에서 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 마이그레이션하고, MySQL Workbench를 사용하여 앱 데이터베이스를 Azure Database for MySQL로 마이그레이션합니다. | 사용 가능
[문서 9: Azure 웹앱 및 Azure SQL Database에서 앱 리팩터링](contoso-migration-refactor-web-app-sql.md) | Contoso에서 SmartHotel360 앱을 Azure Web App으로 마이그레이션하고, Database Migration Assistant를 사용하여 앱 데이터베이스를 Azure SQL Server 인스턴스로 마이그레이션합니다. | 사용 가능    
[문서 10: Azure 웹앱 및 Azure Database for MySQL에서 Linux 앱 리팩터링](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso에서 Linux osTicket 앱을 여러 사이트에 있는 Azure 웹앱으로 마이그레이션합니다. 웹앱은 GitHub와 통합되어 지속적으로 업데이트됩니다. 앱 데이터베이스를 Azure Database for MySQL 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 11: Azure DevOps Services에서 Team Foundation Server 리팩터링](contoso-migration-tfs-vsts.md) | Contoso에서 온-프레미스 Team Foundation Server 배포를 Azure의 Azure DevOps Services로 마이그레이션합니다. | 사용 가능
[문서 12: Azure 컨테이너 및 Azure SQL Database에서 앱 재설계](contoso-migration-rearchitect-container-sql.md) | Contoso에서 SmartHotel 앱을 Azure로 마이그레이션합니다. 그런 다음, 웹앱 계층을 Azure Service Fabric에서 실행되는 Windows 컨테이너로 재설계하고, Azure SQL Database를 사용하여 앱 데이터베이스를 재설계합니다. | 사용 가능    
[문서 13: Azure에서 앱 다시 빌드](contoso-migration-rebuild.md) | Contoso에서 다양한 Azure 기능과 서비스(Azure App Service, AKS(Azure Kubernetes Service), Azure Functions, Azure Cognitive Services 및 Azure Cosmos DB 포함)를 사용하여 SmartHotel 앱을 다시 빌드합니다. | 사용 가능 
문서 14: Azure로의 마이그레이션 확장 | 마이그레이션 조합을 시도한 후 Contoso는 Azure로 전체 마이그레이션을 확장할 준비를 합니다. | 이 문서의 내용

## <a name="business-drivers"></a>비즈니스 영향 요소

IT 리더십 팀은 비즈니스 파트너와의 긴밀한 협력을 통해 다음과 같이 기업이 이러한 마이그레이션으로 달성하고자 하는 바를 잘 이해하고 있습니다.

- **비즈니스 성장 해결**: Contoso는 성장하고 있으며 온-프레미스 시스템과 인프라에 부담을 주고 있습니다.
- **효율성 증대**: Contoso는 불필요한 절차를 제거하고 개발자와 사용자를 위한 프로세스를 간소화해야 합니다. 이 회사는 IT를 빠른 상태로 유지하고 시간이나 비용을 낭비하지 않도록 하여 고객 요구 사항을 더 빠르게 제공해야 합니다.
- **민첩성 향상**: Contoso IT는 비즈니스 요구 사항에 대해 더 빠르게 대응해야 합니다. 글로벌 경제에서 성공할 수 있으려면 시장의 변화에 더 빠르게 대응할 수 있어야 합니다. 사업상의 걸림돌이 되지 않아야 합니다.
- **크기 조정**: 비즈니스가 성공적으로 성장하면 Contoso IT 팀은 그와 동일한 속도로 확장 가능한 시스템을 제공해야 합니다.
- **비용 모델 개선**: Contoso는 IT 예산에서 자본 요구 사항을 줄이려고 합니다.  Contoso는 클라우드 기능을 사용하여 비용이 많이 드는 하드웨어의 필요성을 줄이려고 합니다.
- **저렴한 라이선스 비용**: Contoso는 클라우드 비용을 최소화하려고 합니다.


## <a name="migration-goals"></a>마이그레이션 목표

Contoso 클라우드 팀은 이 마이그레이션에 대한 목표를 확정했습니다. 다음과 같은 목표는 최상의 마이그레이션 방법을 결정하는 데 사용되었습니다.

**요구 사항** | **세부 정보**
--- | --- 
**Azure로 빠르게 이동** | Contoso는 앱과 VM을 Azure로 이동하는 작업을 최대한 빨리 시작하려고 합니다.
**전체 인벤토리 컴파일** | Contoso는 조직에 있는 모든 앱, 데이터베이스 및 VM의 전체 인벤토리를 원합니다.
**앱 평가 및 분류** | Contoso는 클라우드를 완전히 활용하려고 합니다. 기본적으로 Contoso는 모든 서비스가 PaaS로 실행된다고 가정합니다. PaaS가 적합하지 않은 경우 IaaS가 사용됩니다. 
**DevOps 학습 및 DevOps로 이동** | Contoso는 DevOps 모델로 이동하려고 합니다. Contoso는 Azure 및 DevOps 교육을 제공하고 필요에 따라 팀을 다시 구성합니다. 


목표 및 요구 사항을 분명하게 정의한 후 Contoso는 IT 공간을 검토하고 마이그레이션 프로세스를 식별합니다.

## <a name="current-deployment"></a>현재 배포

[Azure 인프라](contoso-migration-infrastructure.md)를 계획 및 설정하고 위의 표에 설명된 대로 여러 가지 POC(개념 증명) 마이그레이션 조합을 시도한 후 Contoso는 대규모로 Azure로 전체 마이그레이션을 시작할 준비가 완료됩니다. Contoso가 마이그레이션하려는 항목은 다음과 같습니다.

**항목** | **볼륨** | **세부 정보**
--- | --- | ---
**워크로드** | 앱 3,000개 초과 | 앱은 VM에서 실행됩니다.<br/><br/>  앱은 Windows, SQL 기반 및 OSS LAMP 앱입니다.
**데이터베이스** | 약 8,500개 | 데이터베이스에는 SQL Server, MySQL, PostgreSQL이 포함됩니다.
**VM** | 10,000개 초과 | VM은 VMware 호스트에서 실행되고 vCenter Server에서 관리합니다.


## <a name="migration-process"></a>마이그레이션 프로세스

Contoso는 비즈니스 영향 요소 및 마이그레이션 목표를 분명하게 정의했으므로, 마이그레이션 프로세스의 4단계 접근 방식을 결정합니다.

- **1단계 - 평가**: 현재 자산을 검색하고 Azure로 마이그레이션에 적합한지 파악합니다.
- **2단계 - 마이그레이션**: 자산을 Azure로 이동합니다. 앱 및 개체를 Azure로 이동하는 방법은 앱 및 목표에 따라 달라집니다.
- **3단계 - 최적화**: 리소스를 Azure로 이동한 후 Contoso는 최대 성능과 효율성을 위해 리소스를 개선하고 간소화해야 합니다.
- **4단계 - 보안 및 관리**: 모든 준비가 완료되면 Contoso는 Azure 보안 및 관리 리소스와 서비스를 사용하여 Azure에서 클라우드 앱을 관리, 보안 및 모니터링합니다.


이러한 단계는 전체 조직에서 순차적으로 진행되지 않습니다. Contoso 마이그레이션 프로젝트의 각 부분은 평가 및 마이그레이션 프로세스의 서로 다른 스테이지에 있습니다. 최적화, 보안 및 관리가 지속적으로 진행됩니다.


## <a name="phase-1-assess"></a>1단계: 평가

Contoso는 온-프레미스 앱, 데이터, 인프라를 검색 및 평가하여 프로세스를 시작합니다. 이러한 측면을 고려하여 Contoso는 다음 작업을 수행합니다.

- Contoso는 앱을 검색하고, 앱 간 종속성을 매핑하며, 마이그레이션 순서 및 우선 순위를 결정해야 합니다.
- 평가 시 Contoso는 앱 및 리소스의 포괄적인 인벤토리를 구축합니다. 새 인벤토리와 함께 Contoso는 기존 CMDB(구성 관리 데이터베이스) 및 서비스 카탈로그를 사용하고 업데이트합니다.
    - CMDB에는 Contoso 앱의 기술적 구성이 포함됩니다.
    - 서비스 카탈로그는 연결된 비즈니스 파트너 및 SLA(서비스 수준 계약)를 포함하여 앱의 운영 세부 정보를 문서화합니다.

### <a name="discover-apps"></a>앱 검색

Contoso는 다양한 서버에서 수천 개의 앱을 실행합니다. Contoso는 CMDB 및 서비스 카탈로그 외에도 검색 및 평가 도구가 필요합니다. 

- 도구는 마이그레이션 프로세스에 평가 데이터를 공급할 수 있는 메커니즘을 제공해야 합니다.
- 평가 도구는 Contoso 실제 및 가상 리소스의 지능형 인벤토리를 구축하는 데 도움이 되는 데이터를 제공해야 합니다. 데이터에는 프로필 정보와 성능 메트릭이 포함되어야 합니다.
- 검색이 완료되면 Contoso에서 자산의 전체 인벤토리 및 자산과 연결된 메타데이터가 준비됩니다. 이 인벤토리는 마이그레이션 계획을 정의하는 데 사용됩니다.

### <a name="identify-classifications"></a>분류 식별

Contoso는 인벤토리에서 자산을 분류하는 몇 가지 일반적인 범주를 식별합니다. 이러한 분류는 마이그레이션을 위한 Contoso의 의사 결정에 중요한 요소입니다. 분류 목록은 마이그레이션 우선 순위를 설정하고 복잡한 문제를 식별하는 데 도움이 됩니다.

**범주** | **할당된 값** | **세부 정보**
--- | --- | ---
비즈니스 그룹 | 비즈니스 그룹 이름 목록 | 인벤토리 항목을 담당하는 그룹은 무엇인가요?
POC 후보 | Y/N | 앱을 클라우드 마이그레이션을 위한 POC 또는 얼리어답터로 사용할 수 있나요?
기술적인 문제 | None/Some/Severe | 인벤토리 항목이 지원되지 않는 제품, 플랫폼 또는 운영 체제를 실행하거나 사용하고 있나요?
방화벽 영향 | Y/N | 앱이 인터넷/외부 트래픽과 통신하나요?  방화벽과 통합되나요?
보안 문제 | Y/N | 앱에 알려진 보안 문제가 있나요?  앱이 암호화되지 않은 데이터나 지원되지 오래된 플랫폼을 사용하나요?


### <a name="discover-app-dependencies"></a>앱 종속성 검색

평가 프로세스의 일부로 Contoso는 앱이 실행 중인 위치를 식별하고 앱 서버 간 종속성 및 연결을 파악해야 합니다. Contoso는 여러 단계로 환경을 매핑합니다.

1. 첫 번째 단계로, Contoso는 서버 및 머신을 개별 앱, 네트워크 위치 및 그룹에 매핑하는 방법을 검색합니다.
2. Contoso는 이 정보를 사용하여 종속성이 거의 없어 빠른 마이그레이션에 적합한 앱을 분명하게 식별할 수 있습니다.
3. Contoso는 매핑을 사용하여 앱이 앱 서버 간에 더 복잡한 종속성과 통신을 식별하는 데 도움을 줄 수 있습니다. Contoso는 이러한 서버를 논리적으로 그룹화하여 앱을 나타내고 이러한 그룹을 기반으로 마이그레이션 전략을 계획할 수 있습니다.


매핑이 완료되면 Contoso는 마이그레이션 계획을 세울 때 모든 앱 구성 요소가 식별되고 고려되었는지 확인할 수 있습니다. 

![종속성 매핑](./media/contoso-migration-scale/dependency-map.png)


### <a name="evaluate-apps"></a>앱 평가

검색 및 평가 프로세스의 마지막 단계로 Contoso는 평가 및 매핑 결과를 평가하여 서비스 카탈로그에서 각 앱을 마이그레이션하는 방법을 파악할 수 있습니다. 

이 평가 프로세스를 캡처하기 위해 인벤토리에 몇 가지 추가 분류를 추가합니다.

**범주** | **할당된 값** | **세부 정보**
--- | --- | ---
비즈니스 그룹 | 비즈니스 그룹 이름 목록 | 인벤토리 항목을 담당하는 그룹은 무엇인가요?
POC 후보 | Y/N | 앱을 클라우드 마이그레이션을 위한 POC 또는 얼리어답터로 사용할 수 있나요?
기술적인 문제 | None/Some/Severe | 인벤토리 항목이 지원되지 않는 제품, 플랫폼 또는 운영 체제를 실행하거나 사용하고 있나요?
방화벽 영향 | Y/N | 앱이 인터넷/외부 트래픽과 통신하나요?  방화벽과 통합되나요?
보안 문제 | Y/N | 앱에 알려진 보안 문제가 있나요?  앱이 암호화되지 않은 데이터나 지원되지 오래된 플랫폼을 사용하나요?
마이그레이션 전략 | 다시 호스트/리팩터링/아키텍처 변경/다시 빌드 | 앱에 어떤 종류의 마이그레이션이 필요한가요? Azure에 앱을 어떤 방법으로 배포하나요? [자세히 알아보기](contoso-migration-overview.md#migration-strategies).
기술 복잡성 | 1-5 | 마이그레이션은 얼마나 복잡한가요? 이 값은 Contoso DevOps 및 관련 파트너가 정의해야 합니다.
비즈니스 중요성 | 1-5 | 비즈니스에 해당 앱이 얼마나 중요한가요? 예를 들어, 작은 작업 그룹 앱에는 점수 1이 할당되지만, 전체 조직에서 사용되는 중요 앱에는 점수 5가 할당됩니다. 이 점수는 마이그레이션 우선 순위 수준에 영향을 줍니다.
마이그레이션 우선 순위 | 1/2/3 | 앱의 마이그레이션 우선 순위는 무엇인가요?
마이그레이션 위험  | 1-5 | 앱 마이그레이션의 위험 수준은 무엇인가요? 이 값은 Contoso DevOps 및 관련 파트너가 동의해야 합니다.




### <a name="figure-out-costs"></a>비용 파악

Azure 마이그레이션의 비용 및 절약 가능 금액을 파악하기 위해 Contoso는 [TCO(총 소유 비용) 계산기](https://azure.microsoft.com/pricing/tco/calculator/)를 사용하여 Azure의 TCO를 계산하고 비교 가능한 온-프레미스 배포에 비교할 수 있습니다.

### <a name="identify-assessment-tools"></a>평가 도구 식별

Contoso는 검색, 평가 및 인벤토리 빌드에 사용할 도구를 결정합니다. Contoso는 Azure 도구와 서비스, 네이티브 앱 도구 및 스크립트, 파트너 도구의 조합을 식별합니다. 특히 Contoso는 대규모로 평가하는 데 Azure Migrate를 사용하는 방법에 관심이 있습니다.


#### <a name="azure-migrate"></a>Azure Migrate


Azure Migrate 서비스를 사용하여 Azure로 마이그레이션을 준비할 때 온-프레미스 VMware VM을 검색하고 평가할 수 있습니다. Azure Migrate의 개념은 다음과 같습니다.

1. 검색: 온-프레미스 VMware VM을 검색합니다.
    - Azure Migrate는 순차적으로 여러 vCenter 서버에서 검색을 지원하고 별도의 Azure Migrate 프로젝트에서 검색을 실행할 수 있습니다.
    - Azure Migrate는 Migrate Collector를 실행하는 VMware VM을 통해 검색을 수행합니다. 동일한 수집기가 다른 vCenter 서버에서 VM을 검색하고 데이터를 다른 프로젝트로 보낼 수 있습니다.
1. 준비 상태 평가: 온-프레미스 머신이 Azure에서 실행하기에 적합한지 여부를 평가합니다. 평가에는 다음이 포함됩니다.
    - 크기 권장 사항: 온-프레미스 VM의 성능 기록을 기반으로 Azure VM에 대한 권장 크기를 확인합니다.
    - 월별 예상 비용: Azure에서 온-프레미스 머신을 실행하는 데 드는 예상 비용을 확인합니다.
2. 종속성 식별:  온-프레미스 머신의 종속성을 시각화하여 평가 및 마이그레이션을 위한 최적의 머신 그룹을 만듭니다.


![Azure Migrate](./media/contoso-migration-scale/azure-migrate.png)


##### <a name="migrate-at-scale"></a>대규모 마이그레이션

Contoso는 Azure Migrate를 사용하여 이 마이그레이션의 크기를 올바르게 제공해야 합니다. 

- Contoso는 Azure Migrate를 통해 앱별 평가를 수행합니다. 이렇게 하면 Azure Migrate는 Azure Portal에 데이터를 시기적절하게 반환합니다.
- Contoso 관리자는 [Azure Migrate 대규모 배포](how-to-scale-assessment.md)를 참조합니다.
- Contoso는 다음 표에 요약된 Azure Migrate 제한에 주의합니다.


**작업** | **제한**
--- | ---
Azure Migrate 프로젝트 만들기 | VM 1500개
검색 | VM 1500개
평가 | VM 1500개

Contoso는 Azure Migrate를 다음과 같이 사용합니다.

- vCenter에서 Contoso는 VM을 폴더로 구성합니다. 이렇게 하면 특정 폴더의 VM에서 평가를 실행할 때 쉽게 집중할 수 있습니다.
- Azure Migrate는 Azure 서비스 맵을 사용하여 머신 간 종속성을 평가합니다. 이 작업을 수행하려면 평가할 VM에 에이전트가 설치되어야 합니다. 
    - Contoso는 자동화된 스크립트를 사용하여 필요한 Windows 또는 Linux 에이전트를 설치합니다.
    - 스크립팅을 통해 Contoso는 vCenter 폴더 내 VM에 설치를 푸시할 수 있습니다.


#### <a name="database-tools"></a>데이터베이스 도구

Azure Migrate 외에도 Contoso는 데이터베이스 평가용 특수 도구를 사용하는 데 중점을 둡니다. [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)와 같은 도구는 마이그레이션을 위해 SQL Server 데이터베이스를 평가하는 데 도움이 됩니다.

DMA(Data Migration Assistant)를 통해 Contoso는 온-프레미스 데이터베이스가 Azure SQL Database, Azure IaaS VM에서 실행 중인 SQL Server 및 Azure SQL Managed Instance와 같은 다양한 Azure 데이터베이스 솔루션과 호환되는지 파악할 수 있습니다. 

DMS 외에도 Contoso는 몇 가지 다른 스크립트를 사용하여 SQL Server 데이터베이스를 검색하고 문서화합니다. 이러한 스크립트는 GitHub 리포지토리에 있습니다.

#### <a name="partner-tools"></a>파트너 도구

Contoso가 Azure로 마이그레이션을 위해 온-프레미스 환경을 평가하는 데 도움이 될 수 있는 몇 가지 다른 파트너 도구가 있습니다. Azure 마이그레이션 파트너를 [자세히 알아보세요](https://azure.microsoft.com/migration/partners/).  

## <a name="phase-2-migrate"></a>2단계: 마이그레이션

평가가 완료되면 Contoso는 Azure로 앱, 데이터 및 인프라를 이동하는 도구를 식별해야 합니다. 




### <a name="migration-strategies"></a>마이그레이션 전략

Contoso가 고려할 수 있는 네 가지 광범위한 마이그레이션 전략이 있습니다. 

**전략** | **세부 정보** | **사용 현황**
--- | --- | ---
**다시 호스트**  | “리프트 앤 시프트” 마이그레이션이라고도 하는 이 전략은 기존 앱을 Azure로 빠르게 마이그레이션하는 코드 없는 옵션입니다.<br/><br/> 코드 변경과 관련된 위험이나 비용 없이 클라우드의 혜택을 이용하여 앱은 현재 상태대로 마이그레이션됩니다. | Contoso는 코드를 변경하지 않고도 덜 전략적인 앱을 다시 호스트할 수 있습니다.
**리팩터링** |  “다시 패키징”이라고도 하는 이 전략에는 앱을 Azure PaaS에 연결하고 클라우드 기능을 더 효율적으로 이용하는 데 필요한 최소한의 앱 코드 또는 구성 변경이 필요합니다. | Contoso는 전략적 앱을 리팩터링하여 동일한 기본 기능을 유지하고 해당 앱을 Azure App Services와 같은 Azure 플랫폼에서 실행되도록 이동할 수 있습니다.<br/><br/> 이를 위해 최소한의 코드 변경이 필요합니다.<br/><br/> 그러나 이 기능은 Microsoft에서 관리하지 않으므로 Contoso가 VM 플랫폼을 유지 관리해야 합니다.
**아키텍처 변경** | 이 전략은 앱 코드 베이스를 수정하거나 확장하여 클라우드 기능 및 확장을 위한 앱 아키텍처를 최적화합니다.<br/><br/> 앱을 복원력이 있고 확장성이 뛰어나며 독립적으로 배포 가능한 아키텍처로 현대화합니다.<br/><br/> Azure 서비스는 프로세스를 가속화하고, 안심하고 애플리케이션을 확장하며, 간편하게 앱을 관리할 수 있습니다.
**다시 빌드** | 이 전략은 클라우드 네이티브 기술을 사용하여 앱을 처음부터 다시 빌드합니다.<br/><br/> Azure PaaS(Platform as a Service)는 클라우드에서 완전한 개발 및 배포 환경을 제공합니다. 소프트웨어 라이선스의 일부 비용과 복잡성을 제거하고 기본 앱 인프라, 미들웨어 및 기타 리소스의 필요성을 제거합니다. | Contoso는 중요 앱을 처음부터 다시 작성하여 서버리스 컴퓨터와 같은 클라우드 기술 또는 마이크로 서비스를 활용할 수 있습니다.<br/><br/> Contoso는 개발하는 앱 및 서비스를 관리하고 Azure는 그 밖의 모든 기능을 관리합니다.


특히 Contoso가 보유한 데이터베이스 볼륨과 함께 데이터도 고려해야 합니다. Contoso의 기본 접근 방식은 Azure SQL Database와 같은 PaaS 서비스를 사용하여 클라우드 기능을 최대한 활용하는 것입니다. 데이터베이스를 위해 PaaS 서비스로 이동하면 기본 플랫폼은 Microsoft에 남아 있고 Contoso는 데이터만 유지 관리하면 됩니다.

### <a name="evaluate-migration-tools"></a>마이그레이션 도구 평가

Contoso는 마이그레이션에 주로 몇 가지 Azure 서비스 및 도구를 사용하고 있습니다.

- [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview): 재해 복구를 오케스트레이션하고 온-프레미스 VM을 Azure로 마이그레이션합니다.
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview): SQL Server, MySQL 및 Oracle과 같은 온-프레미스 데이터베이스를 Azure로 마이그레이션합니다.


#### <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery는 Azure 내에서, 그리고 온-프레미스에서 Azure로 재해 복구 및 마이그레이션을 오케스트레이션하기 위한 기본 Azure 서비스입니다.

1. Site Recovery를 사용하면 온-프레미스 사이트에서 Azure로 복제를 오케스트레이션할 수 있습니다.
2. 복제가 설정되어 실행 중인 경우 온-프레미스 머신은 Azure로 장애 조치(failover)될 수 있으므로 마이그레이션이 완료됩니다.

Contoso는 Site Recovery를 통해 클라우드로 마이그레이션하는 방법을 확인하기 위해 이미 [POC를 완료](contoso-migration-rehost-vm.md)했습니다.

##### <a name="using-site-recovery-at-scale"></a>Site Recovery 대규모 사용

Contoso는 여러 리프트 앤 시프트 마이그레이션을 실행할 계획입니다. 이 작업을 수행하기 위해 Site Recovery는 한 번에 100개 정도의 VM 배치를 복제합니다. 이 작업이 어떻게 진행되는지 파악하기 위해 Contoso는 제안된 Site Recovery 마이그레이션의 용량 계획을 수행해야 합니다.

- Contoso는 트래픽 볼륨 정보를 수집해야 합니다. 특히 다음과 같습니다.
    - Contoso는 복제하려는 VM의 변동률을 결정해야 합니다.
    - 또한 Contoso는 온-프레미스 사이트에서 Azure로 네트워크 연결을 고려해야 합니다.
- 용량 및 볼륨 요구 사항에 맞게 Contoso는 RPO(복구 지점 목표)를 충족하기 위해 필요한 VM의 일일 데이터 변동률을 기반으로 충분한 대역폭을 할당해야 합니다.
- 마지막으로 배포에 필요한 Site Recovery 구성 요소를 실행하는 데 필요한 서버 수를 파악해야 합니다.

###### <a name="gather-on-premises-information"></a>온-프레미스 정보 수집
Contoso는 [Site Recovery Deployment Planner](https://docs.microsoft.com/azure/site-recovery/site-recovery-deployment-planner) 도구를 사용하여 다음 단계를 완료할 수 있습니다.

- Contoso는 이 도구를 사용하여 프로덕션 환경에 영향을 주지 않고 VM을 원격으로 프로파일링할 수 있습니다. 이렇게 하면 복제 및 장애 조치(failover)를 위한 대역폭 및 저장소 요구 사항을 정확히 찾아낼 수 있습니다.
- Contoso는 Site Recovery 구성 요소를 온-프레미스에 설치하지 않고도 도구를 실행할 수 있습니다.
- 이 도구는 호환되거나 호환되지 않는 VM, VM당 디스크 및 디스크당 데이터 변동에 대한 정보를 수집합니다. 또한 네트워크 대역폭 요구 사항과 성공적인 복제 및 장애 조치(failover)에 필요한 Azure 인프라를 식별합니다.
- Contoso는 Site Recovery 구성 서버의 최소 요구 사항과 일치하는 Windows Server 머신에서 플래너 도구를 실행하는지 확인해야 합니다. 구성 서버는 온-프레미스 VMware VM을 복제하는 데 필요한 Site Recovery 머신입니다.


###### <a name="identify-site-recovery-requirements"></a>Site Recovery 요구 사항 식별

복제되는 VM 외에 Site Recovery에는 VMware 마이그레이션의 다양한 구성 요소가 필요합니다.

**구성 요소** | **세부 정보**
--- | ---
**구성 서버** | 일반적으로 VMware VM는 OVF 템플릿을 사용하여 설정됩니다.<br/><br/> 구성 서버 구성 요소는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다.
**프로세스 서버** | 기본적으로 구성 서버에 설치합니다.<br/><br/> 프로세스 서버 구성 요소는 복제 데이터를 수신하고, 캐싱, 압축 및 암호화를 사용하여 최적화하며, Azure Storage로 보냅니다.<br/><br/> 또한 프로세스 서버는 복제하려는 VM에 Azure Site Recovery 모바일 서비스를 설치하고, 온-프레미스 컴퓨터의 자동 검색을 수행합니다.<br/><br/> 확장된 배포에는 대용량 복제 트래픽을 처리하기 위한 추가적인 독립 실행형 프로세스 서버가 필요합니다.
**모바일 서비스** | 모바일 서비스 에이전트는 Site Recovery를 통해 마이그레이션될 각 VMware VM에 설치됩니다.  

Contoso는 용량 고려 사항에 따라 이러한 구성 요소를 배포하는 방법을 파악해야 합니다.

**구성 요소** | **용량 요구 사항**
--- | ---
**최대 일일 변동률** | 단일 프로세스 서버는 최대 2TB의 일일 변동률을 처리할 수 있습니다. VM은 하나의 프로세스 서버만 사용할 수 있으므로 복제된 VM에 지원되는 최대 일일 데이터 변동률은 2TB입니다.
**최대 처리량** | 표준 Azure 저장소 계정은 초당 최대 20,000개의 요청을 처리할 수 있고 전체 복제 VM에서 IOPS(초당 입출력 작업 수)는 이 한도 내로 유지되어야 합니다. 예를 들어, VM의 디스크가 5개이고 각 디스크가 VM에서 120 IOPS(8K 크기)를 생성할 경우 Azure 내에서 디스크당 IOPS 한도인 500을 초과하지 않습니다.<br/><br/> 필요한 저장소 계정 수는 총 원본 머신 IOPS를 20,000으로 나눈 값입니다. 복제된 머신은 Azure에서 단일 저장소 계정에만 속할 수 있습니다.
**구성 서버** | 100~200개 VM을 함께 복제하는 작업에 대한 Contoso의 예상과 [구성 서버 크기 요구 사항](../site-recovery/site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)에 따라 다음과 같은 구성 서버 머신이 필요합니다.<br/><br/> CPU: 16개 vCPU(2개 소켓 * 8코어 @ 2.5GHz)<br/><br/> 메모리: 32GB<br/><br/> 캐시 디스크: 1TB<br/><br/> 데이터 변경률: 1TB ~ 2TB<br/><br/> 크기 요구사항 외에도 Contoso는 구성 서버가 마이그레이션할 VM과 동일한 네트워크 및 LAN 세그먼트에서 최적의 위치에 있는지 확인해야 합니다.
**프로세스 서버** | Contoso는100~200개 VM을 복제하는 기능을 사용하여 독립 실행형 전용 프로세스 서버를 배포합니다.<br/><br/> CPU: 16개 vCPU(2개 소켓 * 8코어 @ 2.5GHz)<br/><br/> 메모리: 32GB<br/><br/> 캐시 디스크: 1TB<br/><br/> 데이터 변경률: 1TB ~ 2TB<br/><br/> 프로세스 서버는 사용량이 매우 많으므로 복제에 필요한 디스크 I/O, 네트워크 트래픽 및 CPU를 처리할 수 있는 ESXi 호스트에 있어야 합니다. Contoso는 이 목적으로 전용 호스트를 고려합니다. 
**네트워킹** | Contoso는 현재 사이트 간 VPN 인프라를 검토했고 Azure ExpressRoute를 구현하기로 결정했습니다. 이 구현은 대기 시간을 줄이고 대역폭을 Contoso의 기본 미국 동부 2 Azure 지역으로 개선하므로 중요합니다.<br/><br/> **모니터링**: Contoso는 프로세스 서버에서 데이터 흐름을 주의 깊게 모니터링해야 합니다. 데이터가 네트워크 대역폭을 오버로드하는 경우 Contoso는 [프로세스 서버 대역폭의 제한](../site-recovery/site-recovery-plan-capacity-vmware.md#control-network-bandwidth)을 고려합니다.
**Azure 저장소** | 마이그레이션을 위해 Contoso는 대상 Azure Storage 계정의 적합한 유형 및 개수를 식별해야 합니다.  Site Recovery는 VM 데이터를 Azure 저장소에 복제합니다.<br/><br/> Site Recovery는 표준 또는 프리미엄(SSD) 저장소 계정으로 복제할 수 있습니다.<br/><br/> Contoso는 저장소를 결정하기 위해 [저장소 한도](../virtual-machines/windows/disks-types.md)를 검토하고 시간에 따른 예상 성장률 및 사용량 증가를 고려해야 합니다. Contoso는 마이그레이션의 속도 및 우선 순위를 고려하여 프리미엄 SSD를 사용하기로 했습니다.<br/><br/>

Contoso는 Azure에 배포된 모든 VM에 관리 디스크를 사용하도록 결정했습니다.  필요한 IOPS에 따라 디스크가 표준 HDD, 표준 SSD 또는 프리미엄(SSD)인지 여부가 결정됩니다.<br/><br/>

#### <a name="data-migration-service"></a>데이터 마이그레이션 서비스

Azure DMS(Database Migration Service)는 가동 중지 시간을 최소화하면서 여러 데이터베이스 원본에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있는 완전히 관리되는 서비스입니다.

- DMS는 기존 도구 및 서비스의 기능을 통합합니다. DMA(Data Migration Assistant)를 사용하여 데이터베이스 호환성 권장 사항 및 필요한 수정 내용을 정확히 찾아내는 평가 보고서를 생성합니다.
- DMS는 마이그레이션하기 전에 잠재적인 문제를 해결하는 데 도움이 되는 지능형 평가가 포함된 간단한 셀프 가이드 마이그레이션 프로세스입니다.
- DMS는 여러 원본에서 대상 Azure 데이터베이스로 대규모로 마이그레이션할 수 있습니다.
- DMS는 SQL Server 2005에서 SQL Server 2017까지 지원을 제공합니다.

DMS가 유일한 Microsoft 데이터베이스 마이그레이션 도구는 아닙니다. [도구 및 서비스 비교](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/)를 확인하세요.

###### <a name="using-dms-at-scale"></a>DMS 대규모 사용

Contoso는 SQL Server에서 마이그레이션할 때 DMS를 사용합니다.  

- DMS를 프로비전할 때 Contoso는 크기가 올바르게 지정되었는지 확인하고 데이터 마이그레이션에 맞게 성능을 최적화하도록 설정해야 합니다. Contoso는 “vCore 4개가 있는 중요 비즈니스용 계층” 옵션을 선택하므로 서비스에서 다중 vCPU를 활용하여 병렬 처리 및 빠른 데이터 전송이 가능합니다.

    ![DMS 크기 조정](./media/contoso-migration-scale/dms.png)

- Contoso의 또 다른 크기 조정 전술은 데이터 마이그레이션 중에 Azure SQL 또는 MySQL 데이터베이스 대상 인스턴스를 프리미엄 계층 SKU로 임시로 강화하는 것입니다. 이렇게 하면 하위 수준 SKU를 사용할 때 데이터 전송 활동에 영향을 줄 수 있는 데이터베이스 제한이 최소화됩니다.



##### <a name="using-other-tools"></a>다른 도구 사용

DMS 외에도 Contoso는 다른 도구와 서비스를 사용하여 VM 정보를 식별할 수 있습니다.

- 수동 마이그레이션에 도움이 되는 스크립트를 사용합니다. 이러한 스크립트는 GitHub 리포지토리에서 사용할 수 있습니다.
- 다양한 [파트너 도구](https://azure.microsoft.com/migration/partners/)를 마이그레이션에 사용할 수도 있습니다.


## <a name="phase-3-optimize"></a>3단계: 최적화

Contoso는 리소스를 Azure로 이동한 후 성능을 개선하기 위해 리소스를 간소화하고 비용 관리 도구로 ROI를 최대화해야 합니다. Azure가 유료 서비스인 경우 Contoso는 시스템의 작동 원리를 이해하고 제대로 크기를 지정했는지 확인해야 합니다.


### <a name="azure-cost-management"></a>Azure Cost Management

클라우드 투자를 최대한 활용하기 위해 Contoso는 무료 Azure Cost Management 도구를 이용합니다.

- Microsoft 자회사인 Cloudyn에서 빌드한 이 라이선스 솔루션을 통해 Contoso는 투명하고 정확하게 클라우드 지출을 관리할 수 있습니다.  이 솔루션은 클라우드 비용을 모니터링, 할당 및 삭감하는 도구를 제공합니다.
- Azure Cost Management는 비용 할당, 쇼백 및 차지백에 도움이 되는 간단한 대시보드 보고서를 제공합니다.
- Cost Management는 Contoso가 관리하고 조정할 수 있는 사용률이 낮은 리소스를 식별하여 클라우드 소비를 최적화할 수 있습니다.
- Azure Cost Management에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/cost-management/overview).

    
![비용 관리](./media/contoso-migration-scale/cost-management.png)  
    
 
### <a name="native-tools"></a>네이티브 도구

Contoso는 사용되지 않는 리소스를 찾는 데도 스크립트를 사용합니다.

- 대규모 마이그레이션 중에는 VHD(가상 하드 드라이브)와 같이 비용이 청구되지만 회사에 중요하지 않은 남은 데이터 조각이 생길 수 있습니다. 스크립트는 GitHub 리포지토리에서 사용할 수 있습니다.
- Contoso는 Microsoft IT 부서에서 수행한 작업을 활용하며 ARO(Azure 리소스 최적화) 도구 키트 구현을 고려합니다.
- Contoso는 미리 구성된 Runbook 및 일정을 사용하여 Azure Automation 계정을 구독에 배포하여 비용 절감을 시작할 수 있습니다. Azure 리소스 최적화는 새 리소스 최적화를 포함하여 일정이 사용 가능하거나 생성된 후에 구독에서 자동으로 이루어집니다.
- 이는 분산형 자동화 기능을 제공하여 비용을 절감합니다. 기능은 다음과 같습니다.
    - 낮은 CPU를 기준으로 VM 자동 다시 알림
    - 다시 알림 및 다시 알림 해제할 Azure VM을 예약합니다.
    - Azure 태그를 사용하여 오름차순 및 내림차순으로 다시 알림 및 다시 알림 해제할 Azure VM을 예약합니다.
    - 요청 시 리소스 그룹 대량 삭제.
- 이 [GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit)에서 ARO 도구 키트를 시작합니다.

### <a name="partner-tools"></a>파트너 도구

[Hanu](https://hanu.com/insight/) 및 [Scalr]( https://www.scalr.com/cost-optimization/)과 같은 파트너 도구를 활용할 수 있습니다.


## <a name="phase-4-secure--manage"></a>4단계: 보안 및 관리

이 단계에서 Contoso는 Azure 보안 및 관리 리소스를 사용하여 Azure에서 클라우드 앱을 관리, 보호 및 모니터링합니다. 이러한 리소스는 Azure Portal에서 제공된 제품을 사용하는 동안 잘 관리된 보안 환경을 실행할 수 있도록 지원합니다. Contoso는 마이그레이션 중에 이러한 서비스 사용을 시작하고, Azure 하이브리드 지원을 통해 이 중 많은 서비스를 계속 사용하여 하이브리드 클라우드에서 일관된 환경을 이용합니다.


### <a name="security"></a>보안
Contoso는 Azure Security Center를 통해 하이브리드 클라우드 워크로드에서 통합 보안 관리 및 고급 위협 방지 기능을 이용합니다.

- Security Center를 사용하면 Azure에서 클라우드 앱의 보안을 완전히 파악하고 제어할 수 있습니다.
- Contoso는 위협을 빠르게 검색하고 조치를 취해 대응하고, 적응형 위협 방지를 사용하여 보안 노출을 줄일 수 있습니다.

Security Center를 [자세히 알아보세요](https://azure.microsoft.com/services/security-center/). 


### <a name="monitoring"></a>모니터링

Contoso는 현재 Azure를 실행하는 새로 마이그레이션된 앱, 인프라 및 데이터의 상태와 성능을 시각적으로 파악해야 합니다. Contoso는 Azure Monitor, Log Analytics 작업 영역 및 Application Insights와 같은 도구를 모니터링 하는 기본 제공 Azure 클라우드를 활용 합니다.
 
- 이러한 도구를 사용하면 Contoso는 원본에서 데이터를 쉽게 수집하고 풍부한 인사이트를 얻을 수 있습니다. 예를 들어, Contoso는 VM의 CPU 디스크 및 메모리 사용률을 측정하고, 여러 VM에서 애플리케이션 및 네트워크 종속성을 확인하고, 애플리케이션 성능을 추적할 수 있습니다.
- Contoso는 이러한 클라우드 모니터링 도구를 사용하여 작업을 수행하고 서비스 솔루션과 통합합니다.
- Azure 모니터링을 [자세히 알아보세요](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview).

### <a name="bcdr"></a>BCDR 

Contoso에는 Azure 리소스의 BCDR(비즈니스 연속성 및 재해 복구) 전략이 필요합니다.

- Azure는 지속적으로 데이터를 보호하고 앱/서비스를 실행할 수 있는 [기본 제공 BCDR 기능](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)을 제공합니다. 
- 기본 제공 기능 외에도 Contoso는 실패로부터 복구하고, 비용이 많이 드는 업무 중단을 방지하고, 준수 목표를 달성하고, 랜섬웨어 및 사람의 오류로부터 데이터를 보호할 수 있는지 확인하려고 합니다. 원하는 작업
    - Contoso는 Azure 리소스 백업을 위한 비용 효율적인 솔루션으로 Azure Backup을 배포합니다. Azure Backup은 기본 제공되므로 Contoso는 간단한 몇 단계만으로 클라우드 백업을 설정할 수 있습니다.
    - Contoso는 지정하는 Azure 지역 간 복제, 장애 조치(failover) 및 장애 복구(failback)에 Azure Site Recovery를 사용하여 Azure VM의 재해 복구를 설정합니다.  이렇게 하면 주 지역에서 중단이 발생하는 경우 Azure VM에서 실행되는 앱을 Contoso가 선택하는 보조 지역에서 계속 사용할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).

 
## <a name="conclusion"></a>결론

이 문서에서 Contoso는 대규모 Azure 마이그레이션을 계획했습니다.  마이그레이션 프로세스를 네 단계로 구분했습니다. 평가 및 마이그레이션부터 마이그레이션이 완료된 후 최적화, 보안 및 관리까지 진행했습니다. 일반적으로 마이그레이션 프로젝트를 전체 프로세스로 계획하지만 집합을 비즈니스에 적합한 분류 및 번호로 구분하여 조직 내에서 시스템을 마이그레이션하는 것이 중요합니다. 데이터를 평가하고 분류를 적용하면 프로젝트를 안전하고 빠르게 실행될 수 있는 일련의 더 작은 마이그레이션으로 구분할 수 있습니다.  이러한 더 작은 마이그레이션이 합해져 빠르게 Azure에 대한 성공한 대규모 마이그레이션이 됩니다.
