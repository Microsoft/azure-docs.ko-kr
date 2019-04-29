---
title: Contoso - 마이그레이션 인프라 설정 | Microsoft Docs
description: Contoso에서 Azure로 마이그레이션하기 위해 Azure 인프라를 설정하는 방법을 알아봅니다.
services: azure-migrate
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/1/2018
ms.author: raynew
ms.openlocfilehash: 5dfe768ddb3509f896b90f913ffecdf33907357a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60682798"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso - 마이그레이션 인프라 배포

이 문서에서는 Contoso는 마이그레이션을 위한 해당 온-프레미스 인프라를 준비하고, 마이그레이션을 위한 준비 및 하이브리드 환경에서 비즈니스를 운영하기 위해 Azure 인프라를 설정합니다.

- Contoso에만 적용되는 샘플 아키텍처입니다.
- 이 문서에서 설명하는 모든 요소가 필요한지 여부는 마이그레이션 전략에 따라 달라집니다. 예를 들어 Azure에서 클라우드 네이티브 앱만 빌드하는 경우 덜 복잡한 네트워킹 구조가 필요할 수 있습니다.

이 문서는 가상의 회사인 Contoso에서 온-프레미스 리소스를 Microsoft Azure 클라우드로 마이그레이션하는 방법을 설명하는 문서 시리즈 중 일부입니다. 이 시리즈에는 배경 정보와 마이그레이션 인프라를 설정하고, 마이그레이션에 대한 온-프레미스 리소스의 적합성을 평가하고, 다양한 유형의 마이그레이션을 실행하는 방법을 설명하는 일련의 배포 시나리오가 포함되어 있습니다. 시나리오가 복잡해지고 있으며, 시간이 지남에 따라 관련 문서가 시리즈에 추가됩니다.


**문서** | **세부 정보** | **상태**
--- | --- | ---
[문서 1: 개요](contoso-migration-overview.md) | 문서 시리즈, Contoso의 마이그레이션 전략 및 시리즈에서 사용되는 샘플 앱에 대해 간략히 설명합니다. | 사용 가능
문서 2: Azure 인프라 배포 | Contoso에서 마이그레이션을 위해 온-프레미스 인프라와 Azure 인프라를 준비합니다. 이 시리즈의 모든 마이그레이션 관련 문서에서 동일한 인프라가 사용됩니다. | 이 문서의 내용
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
[문서 14: Azure로의 마이그레이션 확장](contoso-migration-scale.md) | 마이그레이션 조합을 시도한 후 Contoso는 Azure로 전체 마이그레이션을 확장할 준비를 합니다. | 사용 가능 

이 문서에서 Contoso는 모든 마이그레이션 시나리오를 완료하는 데 필요한 모든 인프라 요소를 설정합니다. 


## <a name="overview"></a>개요

Azure로 마이그레이션하려면 먼저 Contoso에서 Azure 인프라를 준비해야 합니다.  여기서 일반적으로 고려해야 하는 광범위한 5가지 영역은 다음과 같습니다.

**1단계: Azure 구독**: Contoso에서 Azure를 구입하고 Azure 플랫폼 및 서비스와 상호 작용하려면 어떻게 해야 하나요?  
**2단계: 하이브리드 ID**: 마이그레이션 후에 온-프레미스 및 Azure 리소스에 대한 액세스를 관리하고 제어하려면 어떻게 해야 하나요? Contoso에서 ID 관리를 클라우드로 확장하거나 전환하려면 어떻게 합니까?  
**3단계: 재해 복구 및 복원**: Contoso에서 중단 및 재해 발생 시 앱과 인프라를 복원하려면 어떻게 해야 하나요?  
**4단계: 네트워킹**: Contoso에서 네트워크 인프라를 설계하고 온-프레미스 데이터 센터와 Azure 간에 연결을 설정하려면 어떻게 해야 하나요?  
**5단계: 보안**: 하이브리드/Azure 배포를 보호하려면 어떻게 해야 하나요?  
**6단계: 거버넌스**: Contoso는 보안 및 거버넌스 요구 사항에 맞춰 어떻게 배포를 유지하나요?

## <a name="before-you-start"></a>시작하기 전에

인프라를 살펴보기 전에 이 문서에서 설명하는 Azure 기능에 대한 배경 정보를 참조하는 것이 좋습니다.

- 종량제, EA(기업계약), Microsoft 재판매인이나 CSP(클라우드 솔루션 공급자)로 알려진 Microsoft 파트너의 오픈 라이선스를 포함하여 Azure 액세스를 구입하는 데 사용할 수 있는 다양한 옵션이 있습니다. [구매 옵션](https://azure.microsoft.com/pricing/purchase-options/)에 대해 알아보고 [Azure 구독을 구성하는 방법](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/)을 참조하세요.
- Azure [ID 및 액세스 관리](https://www.microsoft.com/trustcenter/security/identity)에 대한 개요를 확인합니다. 특히 [Azure AD 및 온-프레미스 AD를 클라우드로 확장](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)에 대해 알아보세요. [하이브리드 환경의 IAM(ID 및 액세스 관리)](https://azure.microsoft.com/resources/hybrid-cloud-identity/)에 대해 다운로드할 수 있는 유용한 전자책이 있습니다.
- Azure는 하이브리드 연결 옵션을 갖춘 강력한 네트워킹 인프라를 제공합니다. [네트워킹 및 네트워크 액세스 제어](https://docs.microsoft.com/azure/security/security-network-overview)에 대한 개요를 확인하세요.
- [Azure 보안](https://docs.microsoft.com/azure/security/azure-security)에 대한 소개를 확인하고 [거버넌스](https://docs.microsoft.com/azure/security/governance-in-azure) 계획을 만듭니다.


## <a name="on-premises-architecture"></a>온-프레미스 아키텍처

다음은 현재의 Contoso 온-프레미스 인프라를 보여 주는 다이어그램입니다.

 ![Contoso 아키텍처](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso에는 미국 동부의 뉴욕 시에 위치한 하나의 주 데이터 센터가 있습니다.
- 미국 전역에 세 개의 지점이 추가로 있습니다.
- 주 데이터 센터는 파이버 메트로 이더넷 연결(500mbps)을 통해 인터넷에 연결됩니다.
- 각 지점은 IPSec VPN 터널을 통해 주 데이터 센터로 다시 연결되는 비즈니스 클래스 연결을 통해 인터넷에 로컬로 연결됩니다. 이렇게 하면 전체 네트워크가 영구적으로 연결되고 인터넷 연결이 최적화됩니다.
- 주 데이터 센터는 VMware를 통해 완벽하게 가상화됩니다. Contoso에는 vCenter Server 6.5에서 관리되는 두 개의 ESXi 6.5 가상화 호스트가 있습니다.
- Contoso에서 ID 관리에는 Active Directory를 사용하고, 내부 네트워크에는 DNS 서버를 사용합니다.
- 데이터 센터의 도메인 컨트롤러는 VMware VM에서 실행됩니다. 지점의 도메인 컨트롤러는 실제 서버에서 실행됩니다.


## <a name="step-1-buy-and-subscribe-to-azure"></a>1단계: Azure 구입 및 구독

Contoso에서 Azure를 구입하는 방법, 구독을 설계하는 방법 및 서비스 및 리소스에 라이선스를 부여하는 방법을 파악해야 합니다.

### <a name="buy-azure"></a>Azure 구입

Contoso에서 [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)를 체결합니다. 여기서는 Azure에 대한 선불 약정 금액, 유연한 청구 옵션 및 최적화된 가격 책정을 포함한 많은 혜택을 누릴 수 있는 자격 등을 부과하고 있습니다.

- Contoso는 Azure에 대한 연간 지출을 추정합니다. 계약을 체결할 때 Contoso는 1차 연도에 대한 전체 금액을 완전히 지불했습니다.
- Contoso는 해당 연도를 마감하기 전에 약정 금액을 모두 사용해야 합니다. 그렇지 않으면 이러한 잔액에 대한 금전적 가치가 손실됩니다.
- 어떤 이유로든 Contoso가 약정 금액을 초과하여 사용하는 경우 Microsoft는 차액에 대한 청구서를 보냅니다.
- 약정 금액을 초과하는 모든 비용은 Contoso 계약 시의 약정에 따라 동일한 요율로 부과됩니다. 초과에 따른 위약금은 없습니다.

### <a name="manage-subscriptions"></a>구독 관리

Azure에 대한 약정 금액이 지불된 후에는 Contoso에서 Azure 구독을 관리하는 방법을 파악해야 합니다. Contoso는 EA를 유지하고 있으므로 설정할 수 있는 Azure 구독의 수는 제한되지 않습니다.

- Azure 기업 등록계약은 회사에서 Azure 서비스를 만들고 사용하는 방법을 정의하고 핵심 거버넌스 구조를 정의합니다.
- 첫 번째 단계로서, Contoso는 기업 등록 계약을 위한 엔터프라이즈 등록이라고 하는 구조를 결정했습니다. Contoso는 스캐폴드를 이해하고 설계하는 데 도움을 받기 위해 [이 문서](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance)를 사용했습니다.
- 지금은 Contoso에서 구독을 관리하기 위해 기능적 접근 방식을 사용하도록 결정했습니다.
  - 회사 내부에서 Azure 예산을 통제하는 단일 IT 부서를 사용합니다. 이 부서는 구독이 있는 유일한 그룹이 됩니다.
  - Contoso는 앞으로 이 모델을 확장하여 다른 회사 그룹도 기업 등록 계약에 속한 부서로 조인할 수 있습니다.
  - Contoso는 IT 부서 내에 프로덕션 및 개발이라는 두 개의 구독을 구성했습니다.
  - 나중에 구독이 추가로 필요한 경우 Contoso에서 해당 구독에 대한 액세스, 정책 및 준수를 관리해야 합니다. Contoso는 [Azure 관리 그룹](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview)을 구독 위의 추가 계층으로 도입하여 이러한 작업을 수행합니다.

    ![회사 구조](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>라이선스 검토

구독이 구성되면 Contoso에서 Microsoft 라이선스를 볼 수 있습니다. 라이선스 전략은 Contoso에서 Azure로 마이그레이션하려는 리소스와 Azure VM 및 서비스를 선택하고 배포하는 방법에 따라 달라집니다. 

#### <a name="azure-hybrid-benefit"></a>Azure 하이브리드 혜택

Azure에 VM을 배포하는 경우 표준 이미지에는 사용하는 소프트웨어에 대한 분당 요금을 Contoso에 청구하는 라이선스가 포함되어 있습니다. 그러나 Contoso는 장기 Microsoft 고객이었으며 SA(Software Assurance)가 포함된 EA 및 오픈 라이선스를 유지했습니다. 

Azure 하이브리드 혜택은 SA에 포함된 Windows Server Datacenter 및 Standard 버전 라이선스를 변환하거나 다시 사용하여 Azure VM 및 SQL Server 워크로드를 절약할 수 있게 함으로써 Contoso 마이그레이션에 비용 효율적인 방법을 제공합니다. 이렇게 하면 Contoso에서 VM 및 SQL Server에 대해 더 낮은 기준의 계산 요율로 지불할 수 있습니다. [자세히 알아보기](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>라이선스 이동

SA를 통한 라이선스 이동은 Contoso와 같은 Microsoft Volume Licensing 고객이 Azure에 활성 SA가 있는 적합한 서버 응용 프로그램을 배포할 수 있는 유연성을 제공합니다. 이렇게 하면 새 라이선스를 구입할 필요가 없습니다. 관련된 이동 요금이 없으므로 기존 라이선스를 Azure에 쉽게 배포할 수 있습니다. [자세히 알아보기](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>예측 가능한 워크로드에 대한 인스턴스 예약

예측 가능한 워크로드는 실행되는 VM에서 항상 사용할 수 있어야 합니다. 예를 들어 SAP ERP 시스템과 같은 LOB(기간 업무) 앱이 있습니다.  반면에 예측 불가능한 워크로드는 가변적입니다. 예를 들어 수요가 많을 때는 작동하고, 사용량이 많지 않을 때는 중단되는 VM이 있습니다.

![예약 인스턴스](./media/contoso-migration-infrastructure/reserved-instance.png) 

오랜 시간 동안 유지해야 하는 특정 VM 인스턴스에 예약 인스턴스를 사용하는 대신, 콘솔에서 할인 용량 및 우선 순위가 지정된 용량을 모두 얻을 수 있습니다. Contoso는 Azure 하이브리드 혜택과 함께 [Azure Reserved Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/)를 사용하여 일반 종량제 가격을 최대 82%까지 절약할 수 있습니다(2018년 4월 현재).


## <a name="step-2-manage-hybrid-identity"></a>2단계: 하이브리드 ID 관리

IAM(ID 및 액세스 관리)을 사용하여 Azure 리소스에 대한 사용자 액세스 권한을 부여하고 제어하는 것은 Azure 인프라를 통합하는 데 있어 중요한 단계입니다.  

- Contoso는 Azure에서 별도의 새 시스템을 구축하는 대신, 온-프레미스 Active Directory를 클라우드로 확장하도록 결정했습니다.
- 이러한 작업을 수행하기 위해 Azure 기반 Active Directory를 만듭니다.
- Contoso는 Office 365를 사용하지 않으므로 새 Azure AD를 프로비전해야 합니다.
- Office 365는 사용자 관리를 위해 Azure AD를 사용합니다. Contoso에서 Office 365를 사용하고 있었다면 이미 Azure AD 테넌트가 있으며 이를 기본 AD로 사용할 수 있습니다.
- Office 365용 Azure AD에 대해 [자세히 알아보고](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9), 기존 Azure AD 테넌트에 [구독을 추가하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory)에 대해 알아보세요.

### <a name="create-an-azure-ad"></a>Azure AD 만들기

Contoso는 Azure 구독에 포함된 Azure AD Free 버전을 사용하고 있습니다. Contoso 관리자는 다음과 같이 AD 디렉터리를 설정합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **리소스 만들기** > **ID** > **Azure Active Directory**로 차례로 이동합니다.
2. **디렉터리 만들기**에서 디렉터리 이름, 초기 도메인 이름 및 Azure AD 디렉터리를 만들어야 하는 지역을 지정합니다.

    ![Azure AD 만들기](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > 만들어진 디렉터리에는 **domainname.onmicrosoft.com** 형식의 초기 도메인 이름이 있습니다. 이 이름은 변경하거나 삭제할 수 없습니다. 대신 Azure AD에 등록된 도메인 이름을 추가해야 합니다.

### <a name="add-the-domain-name"></a>도메인 이름 추가

표준 도메인 이름을 사용하려면 Contoso 관리자가 해당 이름을 Azure AD에 사용자 지정 도메인 이름으로 추가해야 합니다. 이 옵션을 사용하면 관리자가 친숙한 사용자 이름을 지정할 수 있습니다. 예를 들어 사용자는 billg@contosomigration.microsoft.com을 사용할 필요 없이 billg@contoso.com 이메일 주소를 사용하여 로그인할 수 있습니다. 

사용자 지정 도메인 이름을 설정하려면 해당 이름을 디렉터리에 추가하고 DNS 항목을 추가한 다음 Azure AD에서 이름을 확인합니다.

1. **사용자 지정 도메인 이름** > **사용자 지정 도메인 추가**에서 도메인을 추가합니다.
2. Azure에서 DNS 항목을 사용하려면 도메인 등록 기관에 해당 항목을 등록해야 합니다. 

    - **사용자 지정 도메인 이름** 목록에서 이름에 대한 DNS 정보를 적어 둡니다. 현재 MX 항목을 사용하고 있습니다.
    - 이렇게 하려면 이름 서버에 액세스해야 합니다. Contoso.com 도메인에 로그인하고, 적어 둔 세부 정보를 사용하여 Azure AD에서 제공한 DNS 항목에 대한 새 MX 레코드를 만듭니다.  
1. DNS 레코드가 전파되면 도메인에 대한 세부 정보 이름에서 **확인**을 클릭하여 사용자 지정 도메인 이름을 확인합니다.

     ![Azure AD DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>온-프레미스 및 Azure 그룹/사용자 설정

Azure AD가 가동되어 실행되고 있으므로 Contoso 관리자에서 Azure AD와 동기화할 온-프레미스 AD 그룹에 직원을 추가해야 합니다. Azure의 리소스 그룹 이름과 일치하는 온-프레미스 그룹 이름을 사용해야 합니다. 이렇게 하면 일치하는 항목을 쉽게 식별하여 동기화할 수 있습니다.

#### <a name="create-resource-groups-in-azure"></a>Azure에서 리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스도 함께 수집합니다. 리소스 그룹 ID를 사용하면 Azure에서 그룹 내 리소스에 대한 작업을 수행할 수 있습니다.

- Azure 구독에는 여러 리소스 그룹이 있을 수 있지만 리소스 그룹은 단일 구독 내에만 있을 수 있습니다.
- 또한 단일 리소스 그룹에는 여러 리소스가 있을 수 있지만 리소스는 단일 리소스 그룹에만 속할 수 있습니다.

Contoso 관리자는 다음 표에서 요약한 대로 Azure 리소스 그룹을 설정합니다.

**리소스 그룹** | **세부 정보**
--- | ---
**ContosoCobRG** | 이 그룹에는 COB(무중단 업무 방식)과 관련된 모든 리소스가 포함됩니다.  Contoso에서 Azure Site Recovery 서비스 및 Azure Backup 서비스에 사용할 자격 증명 모음을 포함합니다.<br/><br/> 또한 Azure Migrate 및 Database Migration Service를 포함하여 마이그레이션에 사용되는 리소스도 포함됩니다.
**ContosoDevRG** | 이 그룹에는 개발 및 테스트 리소스가 포함됩니다.
**ContosoFailoverRG** | 이 그룹은 장애 조치된 리소스에 대한 착륙 영역 역할을 합니다.
**ContosoNetworkingRG** | 이 그룹에는 모든 네트워킹 리소스가 포함됩니다.
**ContosoRG** | 이 그룹에는 프로덕션 응용 프로그램 및 데이터베이스와 관련된 리소스가 포함됩니다.

리소스 그룹은 다음과 같이 만듭니다.

1. Azure Portal > **리소스 그룹**에서 그룹을 추가합니다.
2. 각 그룹에 대해 이름, 그룹이 속한 구독 및 지역을 지정합니다.
3. 리소스 그룹은 **리소스 그룹** 목록에 표시됩니다.

    ![리소스 그룹](./media/contoso-migration-infrastructure/resource-groups.png) 

##### <a name="scaling-resource-groups"></a>리소스 그룹 크기 조정

나중에 Contoso는 필요에 따라 다른 리소스 그룹을 추가합니다. 예를 들어 독립적으로 보호하고 관리할 수 있도록 각 앱 또는 서비스에 대한 리소스 그룹을 정의할 수 있습니다.

#### <a name="create-matching-security-groups-on-premises"></a>온-프레미스에 일치하는 보안 그룹 만들기

1. Contoso 관리자는 온-프레미스 Active Directory에서 보안 그룹을 Azure 리소스 그룹의 이름과 일치하는 이름으로 설정합니다.
 
    ![온-프레미스 AD 보안 그룹](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. 관리를 위해 다른 모든 그룹에 추가할 추가 그룹을 만듭니다. 이 그룹에는 Azure의 모든 리소스 그룹에 대한 권한이 있으며, 제한된 수의 전역 관리자가 추가됩니다.

### <a name="synchronize-ad"></a>AD 동기화

Contoso에서 온-프레미스 및 클라우드에서 리소스에 액세스하기 위한 일반적인 ID를 제공하려고 합니다. 이를 위해 온-프레미스 Active Directory를 Azure AD와 통합합니다. 이 모델의 경우 다음과 같습니다.

- 사용자와 조직에서는 단일 ID를 활용하여 Office 365 또는 인터넷상의 수천 개의 다른 사이트와 같은 온-프레미스 애플리케이션과 클라우드 서비스에 액세스할 수 있습니다.
- 관리자는 AD의 그룹을 활용하여 Azure에서 [RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)를 구현할 수 있습니다.

통합을 용이하게 하기 위해 Contoso는 [Azure AD Connect 도구](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)를 사용합니다. 도메인 컨트롤러에 도구를 설치하고 구성하면 로컬 온-프레미스 AD ID가 Azure AD와 동기화됩니다. 

### <a name="download-the-tool"></a>도구 다운로드


1. Azure Portal에서 Contoso 관리자는 **Azure Active Directory** > **Azure AD Connect**로 차례로 이동하여 동기화에 사용되는 서버에 최신 버전의 도구를 다운로드합니다.

    ![AD Connect 다운로드](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. **기본 설정 사용**을 사용하여 **AzureADConnect.msi** 설치를 시작합니다. 이는 가장 일반적인 설치이며 인증을 위해 암호 해시 동기화를 사용하는 단일 포리스트 토폴로지에 사용할 수 있습니다.

    ![AD Connect 마법사](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. **Azure AD**에서 Azure AD에 연결하기 위한 자격 증명을 지정합니다(CONTOSO\admin 또는 contoso.com\admin 형식).

    ![AD Connect 마법사](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. **AD DS에 연결**에서 온-프레미스 AD에 대한 자격 증명을 지정합니다.

     ![AD Connect 마법사](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. **구성 준비 완료**에서 **구성이 완료되면 동기화 프로세스 시작**을 클릭하여 동기화를 즉시 시작합니다. 그런 다음, 설치합니다.

다음 사항에 유의하세요.
- Contoso가 Azure에 직접 연결됩니다. 온-프레미스 AD가 프록시 뒤에 있는 경우 이 [문서](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity)를 참조하세요.
- 첫 번째 동기화 후에 온-프레미스 AD 개체가 Azure AD에 표시됩니다.

    ![Azure의 온-프레미스 AD](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- Contoso IT 팀은 해당 역할에 따라 각 그룹에 표시됩니다.

    ![Azure의 온-프레미스 AD 구성원](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>RBAC 설정

Azure [RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)를 사용하면 Azure에 대한 액세스를 자세히 관리할 수 있습니다. RBAC를 사용하여 사용자가 작업을 수행하는 데 필요한 액세스 만큼 권한을 부여할 수 있습니다. 범위 수준에서 사용자, 그룹 및 애플리케이션에 적절한 RBAC 역할을 할당합니다. 역할 할당의 범위는 구독, 리소스 그룹 또는 단일 리소스일 수 있습니다. 

이제 Contoso 관리자는 온-프레미스에서 동기화된 AD 그룹에 역할을 할당합니다.

1. **ControlCobRG** 리소스 그룹에서 **액세스 제어(IAM)** > **역할 할당 추가**를 차례로 클릭합니다.
2. **역할 할당 추가** > **역할** > **기여자**로 이동한 후 목록에서 **ContosoCobRG** AD 그룹을 선택합니다. 그러면 해당 그룹이 **선택한 구성원** 목록에 표시됩니다. 
3. 리소스 그룹과 일치하는 AD 계정에 기여자 권한을 추가하여 다른 리소스 그룹(**ContosoAzureAdmins** 제외)에 대해 동일한 권한으로 이 작업을 반복합니다.
4. **ContosoAzureAdmins** AD 그룹에 대해 **소유자** 역할을 할당합니다.

    ![Azure의 온-프레미스 AD 구성원](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>3단계: 복원 및 재해 설계

### <a name="set-up-regions"></a>지역 설정

Azure 리소스는 지역 내에 배포됩니다.

- 지역은 지리적으로 구성되며 데이터 상주, 주권, 준수 및 복원 요구 사항이 지리적 경계 내에서 적용됩니다.
- 지역은 일단의 데이터 센터로 구성됩니다. 이러한 데이터 센터는 대기 시간이 정의된 경계 내에 배포되며 대기 시간이 짧은 전용 지역 네트워크를 통해 연결됩니다.
- 각 Azure 지역은 복원을 위해 다른 지역과 쌍으로 연결됩니다.
- [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)에 대해 알아보고, [지역이 쌍으로 연결되는 방법](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 이해하세요.


Contoso는 미국 동부 2(버지니아 소재)를 주 지역으로, 미국 중부(아이오와 소재)를 보조 지역으로 수용하도록 결정했습니다. 이 결정에 대한 몇 가지 이유는 다음과 같습니다.

- Contoso 데이터 센터는 뉴욕에 있으며, 가장 가까운 데이터 센터에 대한 대기 시간을 고려했습니다.
- 미국 동부 2 지역에는 Contoso에서 사용해야 하는 모든 서비스와 제품이 있습니다. 사용 가능한 제품 및 서비스 측면에서 모든 Azure 지역이 동일하지는 않습니다. [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/)을 검토할 수 있습니다.
- 미국 중부는 미국 동부 2와 Azure 쌍으로 연결된 지역입니다.

Contoso는 자체의 하이브리드 환경에 대해 생각하면서 지역 설계에 복원과 재해 복구 전략을 구축하는 방법을 고려해야 합니다. 대체로 전략은 장애 도메인 및 복원에 대한 지역적 페어링과 같은 Azure 플랫폼 기능을 사용하는 단일 지역 배포에서 클라우드 서비스와 데이터베이스가 배포되고 두 지역의 사용자에게 서비스를 제공하는 전체 Active-Active(활성-활성) 모델에 이르기까지 다양합니다.

Contoso는 중간 정도의 수준으로 유지하도록 결정했습니다. 즉, 전체 앱 재해 또는 지역 장애가 발생할 경우 전체 백업으로 작동할 수 있도록 앱과 리소스는 기본 지역에 배포하고 인프라의 전체 복사본은 보조 지역에 보관합니다.

### <a name="set-up-availability-zones"></a>가용성 영역 설정

가용성 영역은 데이터 센터 오류로부터 앱 및 데이터를 보호하는 데 도움이 됩니다.

- 각 가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다.
- 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 
- 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다.
- 지역 내에서 영역의 물리적 구분은 애플리케이션 및 데이터를 데이터 센터 오류로부터 보호할 수 있습니다.

Contoso는 확장성, 고가용성 및 복원력에 대한 앱 호출로 가용성 영역을 배포합니다. [자세히 알아보기](https://docs.microsoft.com/azure/availability-zones/az-overview). 


## <a name="step-4-design-a-network-infrastructure"></a>4단계: 네트워크 인프라 설계

Contoso에서 지역 설계를 통해 네트워킹 전략을 고려할 준비가 되었습니다. 이제는 온-프레미스 데이터 센터와 Azure가 서로 연결하고 통신하는 방법 및 Azure에서 네트워크 인프라를 디자인할 방법을 고려해야 합니다. 특히 Contoso는 다음을 수행해야 합니다.

- **하이브리드 네트워크 연결 계획**: 온-프레미스와 Azure 간에 네트워크를 연결하는 방법을 알아봅니다.
- **Azure 네트워크 인프라 설계**: 네트워크를 어떻게 지역에 배포할지 결정합니다. 네트워크에서 동일한 지역 내 및 지역 간에 통신하는 방법을 결정합니다.
- **Azure 네트워크 설계 및 설정**: Azure 네트워크와 서브넷을 설정하고, 해당 영역 내에 상주할 항목을 결정합니다.

### <a name="plan-hybrid-network-connectivity"></a>하이브리드 네트워크 연결 계획

Contoso는 Azure와 온-프레미스 데이터 센터 간의 하이브리드 네트워킹에 대한 [아키텍처의 수](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)를 고려했습니다. 비교 옵션에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations).

참고로, Contoso의 온-프레미스 네트워크 인프라는 현재 뉴욕의 데이터 센터와 미국 동부의 지점으로 구성됩니다.  모든 위치에는 인터넷에 대한 비즈니스 클래스 연결이 있습니다.  각 분기는 다음 IPSec VPN 터널을 통해 데이터 센터에 인터넷을 통해 연결 됩니다.

![Contoso 네트워크](./media/contoso-migration-infrastructure/contoso-networking.png) 

Contoso에서 하이브리드 연결을 구현하도록 결정한 방법은 다음과 같습니다.

1. 뉴욕의 Contoso 데이터 센터와 미국 동부 2 및 미국 중부의 두 Azure 지역 간에 새 사이트 간 VPN 연결을 설정합니다.
2. Azure 가상 네트워크에 바인딩된 지점 트래픽은 Contoso의 주요 데이터 센터를 통해 라우팅됩니다. 
3. Contoso에서 Azure 배포를 강화하면 데이터 센터와 Azure 지역 간에 ExpressRoute 연결을 설정합니다. 이 경우 Contoso는 장애 조치 전용의 VPN 사이트 간 연결을 유지합니다.
    - VPN 및 ExpressRoute 하이브리드 솔루션 중에서 선택하는 방법에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations).
    - [ExpressRoute 위치 및 지원](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers)을 확인하세요.


**VPN만**

![Contoso VPN](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**VPN 및 ExpressRoute**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Azure 네트워크 인프라 설계

Contoso는 하이브리드 배포를 안전하고 확장성 있게 만드는 방식으로 네트워크를 배치해야 합니다. 이를 위해 Contoso는 장기적인 접근 방식을 취하고 있으며 복원력 있고 회사 수준에 맞는 VNet(가상 네트워크)을 설계합니다. VNet 계획에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).

Contoso는 두 지역을 연결하기 위해 허브-허브 네트워크 모델을 구현하도록 결정했습니다.

- Contoso는 각 지역 내에서 허브 및 스포크 모델을 사용합니다.
- Contoso는 네트워크와 허브를 연결하기 위해 Azure 네트워크 피어링을 사용합니다.

#### <a name="network-peering"></a>네트워크 피어링

Azure는 VNet과 허브를 연결하는 네트워크 피어링을 제공합니다. 글로벌 피어링을 사용하면 여러 지역의 VNet/허브 간에 연결할 수 있습니다. 로컬 피어링은 동일한 지역에 있는 VNet을 연결합니다. VNet 피어링에서 제공하는 많은 이점은 다음과 같습니다.

- 피어링된 VNet 간의 네트워크 트래픽은 비공개입니다.
- VNet 간 트래픽이 Microsoft 백본 네트워크에서 유지됩니다. VNet 간 통신에는 공용 인터넷, 게이트웨이 또는 암호화가 필요하지 않습니다.
- 피어링은 다른 VNet의 리소스 간에 대기 시간이 짧고 대역폭이 높은 기본 연결을 제공합니다.

네트워크 피어링에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

#### <a name="hub-to-hub-across-regions"></a>지역 간 허브-허브

Contoso는 각 지역에 허브를 배포합니다. 허브는 사용자의 온-프레미스 네트워크에 대한 연결의 중심점 역할을 하는 Azure의 VNet(가상 네트워크)입니다. 허브 VNet은 글로벌 VNet 피어링을 사용하여 서로 연결됩니다. 글로벌 VNet 피어링은 Azure 지역 간에 VNet을 연결합니다.

- 각 지역의 허브는 다른 지역의 파트너 허브와 피어링됩니다.
- 허브는 해당 지역의 모든 네트워크에 피어링되며 모든 네트워크 리소스에 연결할 수 있습니다.

    ![글로벌 피어링](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>지역 내 허브 및 스포크

Contoso는 각 지역 내에서 지역 허브의 스포크 네트워크와 같이 다양한 용도로 VNet을 배포합니다. 한 지역 내의 VNet은 피어링을 사용하여 허브 및 서로에 연결합니다.

#### <a name="design-the-hub-network"></a>허브 네트워크 설계

Contoso에서 선택한 허브 및 스포크 모델 내에서 자체의 온-프레미스 데이터 센터 및 인터넷의 트래픽을 라우팅하는 방법을 고려해야 합니다. Contoso에서 미국 동부 2 및 미국 중부 허브 모두에 대한 라우팅을 처리하도록 결정한 방법은 다음과 같습니다.

- Contoso는 패킷이 인바운드 네트워크에서 아웃바운드 네트워크로 따라가는 경로인 "역방향 c"라고 하는 네트워크를 설계합니다.
- 네트워크 아키텍처에는 신뢰할 수 없는 프런트 엔드 경계 영역과 신뢰할 수 있는 백 엔드 영역이라는 두 개의 경계가 있습니다.
- 방화벽에는 각 영역에서 신뢰할 수 있는 영역에 대한 액세스를 제어하는 네트워크 어댑터가 있습니다.
- 인터넷에서:
    - 인터넷 트래픽은 경계 네트워크의 부하 분산된 공용 IP 주소에 도달합니다.
    - 이 트래픽은 방화벽을 통해 라우팅되며 방화벽 규칙이 적용됩니다.
    - 네트워크 액세스 제어가 구현되면 트래픽이 신뢰할 수 있는 영역의 적절한 위치로 전달됩니다.
    - VNet의 아웃바운드 트래픽은 UDR(사용자 정의 경로)을 사용하여 인터넷으로 라우팅됩니다. 트래픽은 방화벽을 통과해야 하며 Contoso 정책에 따라 검사됩니다.
- Contoso 데이터 센터에서:
    - VPN 사이트 간(또는 ExpressRoute) 연결을 통해 들어오는 트래픽은 Azure VPN 게이트웨이의 공용 IP 주소에 도달합니다.
    - 트래픽은 방화벽을 통해 라우팅되며 방화벽 규칙이 적용됩니다.
    - 방화벽 규칙이 적용되면 트래픽이 신뢰할 수 있는 내부 영역 서브넷의 내부 부하 분산 장치(표준 SKU)로 전달됩니다.
    - VPN을 통해 신뢰할 수 있는 서브넷에서 온-프레미스 데이터 센터로 흐르는 아웃바운드 트래픽은 방화벽을 통해 라우팅되고 VPN 사이트 간 연결을 통과하기 전에 규칙이 적용됩니다.



### <a name="design-and-set-up-azure-networks"></a>Azure 네트워크 설계 및 설정

Contoso에서 네트워크 및 라우팅 토폴로지를 사용하여 Azure 네트워크 및 서브넷을 설정할 준비가 되었습니다.

- Contoso는 Azure(0.0.0.0 ~ 127.255.255.255)에 클래스 A 사설망을 구현합니다. 현재 Contoso의 온-프레미스에서 172.160.0/16 클래스 B 사설 주소 공간을 사용하여 주소 범위 간에 겹치지 않도록 할 수 있기 때문에 이 방법은 작동합니다.
- VNet을 자체의 주 지역 및 보조 지역에 배포합니다.
- Contoso는 **VNET** 접두사 및 **EUS2** 또는 **CUS** 지역 약어가 포함되는 명명 규칙을 사용합니다. 이 표준을 사용하는 경우 허브 네트워크의 이름은 **VNET-HUB-EUS2**(미국 동부 2) 및 **VNET-HUB-CUS**(미국 중부)입니다.
- Contoso에는 [IPAM 솔루션](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top)이 없으므로 NAT 없이 네트워크 라우팅을 계획해야 합니다.


#### <a name="virtual-networks-in-east-us-2"></a>미국 동부 2의 가상 네트워크

미국 동부 2는 Contoso에서 리소스와 서비스를 배포하는 데 사용할 주 지역입니다. Contoso에서 이 지역 내에 네트워크를 설계하는 방법은 다음과 같습니다.

- **허브**: 미국 동부 2의 허브 VNet은 온-프레미스 데이터 센터에 대한 기본 연결의 중심점입니다.
- **VNet**: 필요한 경우 미국 동부 2의 스포크 VNet을 사용하여 워크로드를 격리할 수 있습니다. Contoso는 허브 VNet 외에도 미국 동부 2 지역에 다음 2개의 VNet을 갖게 됩니다.
    - **VNET-DEV-EUS2**: 이 VNet은 개발 및 테스트 팀에게 dev(개발) 프로젝트를 위한 모든 기능을 갖춘 네트워크를 제공합니다. 이는 프로덕션 파일럿 영역의 역할을 수행하며 프로덕션 인프라를 통해 작동합니다.
    - **VNET-PROD-EUS2**: Azure IaaS 프로덕션 구성 요소가 이 네트워크에 배치됩니다. 
    -  각 VNet에는 겹치지 않는 고유한 주소 공간이 있습니다. Contoso는 NAT를 요구하지 않고 라우팅을 구성하려고 합니다.
- **서브넷**:
    - 각 응용 프로그램 계층의 각 네트워크에 서브넷이 있습니다.
    - 프로덕션 네트워크의 각 서브넷은 개발 VNet에서 일치하는 서브넷을 갖게 됩니다.
    - 프로덕션 네트워크에는 도메인 컨트롤러에 대한 서브넷도 있습니다.

미국 동부 2의 VNet은 다음 표에 요약되어 있습니다.

**VNet** | **Range** | **피어**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2, VNET-DEV-EUS2, VNET-PROD-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-PROD-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2, VNET-PROD-CUS

![주 지역의 허브/스포크](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>미국 동부 2 허브 네트워크(VNET-HUB-EUS2)의 서브넷

**서브넷/영역** | **CIDR** | **사용 가능한 IP 주소
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**OB-UntrustZone** | 10.240.2.0/24 | 251
**OB-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>미국 동부 2 Dev 네트워크(VNET-DEV-EUS2)의 서브넷

개발 VNet은 개발 팀에서 프로덕션 파일럿 영역으로 사용합니다. 세 개의 서브넷이 있습니다.

**서브넷** | **CIDR** | **주소** | **서브넷에 있는 리소스**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | 프런트 엔드/웹 계층 VM
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | 응용 프로그램 계층 VM
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | 데이터베이스 VM


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>미국 동부 2 프로덕션 네트워크(VNET-PROD-EUS2)의 서브넷

Azure IaaS 구성 요소는 프로덕션 네트워크에 있습니다. 각 응용 프로그램 계층에는 자체 서브넷이 있습니다. 도메인 컨트롤러용 서브넷을 추가하면 서브넷이 개발 네트워크의 서브넷과 일치하게 됩니다.

**서브넷** | **CIDR** | **주소** | **서브넷에 있는 리소스**
--- | --- | --- | ---
**PROD-FE-EUS2** | 10.245.32.0/22 | 1019 | 프런트 엔드/웹 계층 VM
**PROD-APP-EUS2** | 10.245.36.0/22 | 1019 | 응용 프로그램 계층 VM
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | 데이터베이스 VM
**PROD-DC-EUS2** | 10.245.42.0/24 | 251 | 도메인 컨트롤러 VM


![허브 네트워크 아키텍처](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>미국 중부(보조 지역)의 가상 네트워크

미국 중부는 Contoso의 보조 지역입니다. Contoso에서 이 지역 내에 네트워크를 설계하는 방법은 다음과 같습니다.

- **허브**: 미국 동부 2의 허브 VNet은 온-프레미스 데이터 센터에 대한 연결의 중심점이며, 필요한 경우 미국 동부 2의 스포크 VNet을 사용하여 다른 스포크와 별도로 관리되는 워크로드를 격리할 수 있습니다.
- **VNet**: Contoso는 미국 중부에 다음 2개의 VNet을 갖게 됩니다.
    - VNET-PROD-CUS: 이 VNet은 VNET-PROD_EUS2와 비슷한 프로덕션 네트워크입니다. 
    - VNET-ASR-CUS: 이 VNet은 온-프레미스 환경에서 장애 조치한 후 VM을 만드는 위치 또는 주 지역에서 보조 지역으로 장애 조치되는 Azure VM에 대한 위치로 작동합니다. 이 네트워크는 프로덕션 네트워크와 비슷하지만 도메인 컨트롤러가 없습니다.
    -  지역의 각 VNet에는 겹치지 않는 고유한 주소 공간이 있습니다. Contoso는 NAT 없이 라우팅을 구성합니다.
- **서브넷**: 서브넷은 미국 동부 2의 서브넷과 비슷한 방식으로 설계됩니다. 예외적으로 Contoso에는 도메인 컨트롤러에 대한 서브넷이 필요하지 않습니다.

미국 중부의 VNet은 다음 표에 요약되어 있습니다.

**VNet** | **Range** | **피어**
--- | --- | ---
**VNET-HUB-CUS** | 10.250.0.0/20 | VNET-HUB-EUS2, VNET-ASR-CUS, VNET-PROD-CUS
**VNET-ASR-CUS** | 10.255.16.0/20 | VNET-HUB-CUS, VNET-PROD-CUS
**VNET-PROD-CUS** | 10.255.32.0/20 | VNET-HUB-CUS, VNET-ASR-CUS, VNET-PROD-EUS2  


![페어링된 지역의 허브/스포크](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>미국 중부 허브 네트워크(VNET-HUB-CUS)의 서브넷

**서브넷** | **CIDR** | **사용 가능한 IP 주소**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**OB-UntrustZone** | 10.250.2.0/24 | 251
**OB-TrustZone** | 10.250.3.0/24 | 251
**GatewaySubnet** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>미국 중부 프로덕션 네트워크(VNET-PROD-CUS)의 서브넷

미국 동부 2 주 지역의 프로덕션 네트워크와 병행하여 미국 중부 보조 지역에 프로덕션 네트워크가 있습니다. 

**서브넷** | **CIDR** | **주소** | **서브넷에 있는 리소스**
--- | --- | --- | ---
**PROD-FE-CUS** | 10.255.32.0/22 | 1019 | 프런트 엔드/웹 계층 VM
**PROD-APP-CUS** | 10.255.36.0/22 | 1019 | 응용 프로그램 계층 VM
**PROD-DB-CUS** | 10.255.40.0/23 | 507 | 데이터베이스 VM
**PROD-DC-CUS** | 10.255.42.0/24 | 251 | 도메인 컨트롤러 VM

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>미국 중부에 있는 미국 중부 장애 조치/복구 네트워크(VNET-ASR-CUS)의 서브넷

VNET-ASR-CUS 네트워크는 지역 간 장애 조치를 위해 사용됩니다. Site Recovery를 사용하여 지역 간 Azure VM을 복제하고 장애 조치합니다. 또한 온-프레미스에서 유지되지만 재해 복구를 위해 Azure로 장애 조치하는 보호된 워크로드에 대한 Azure 네트워크에서 Contoso 데이터 센터로 작동합니다.

VNET-ASR-CUS는 미국 동부 2의 프로덕션 VNet과 동일한 기본 서브넷이지만 도메인 컨트롤러 서브넷이 필요하지 않습니다.

**서브넷** | **CIDR** | **주소** | **서브넷에 있는 리소스**
--- | --- | --- | ---
**ASR-FE-CUS** | 10.255.16.0/22 | 1019 | 프런트 엔드/웹 계층 VM
**ASR-APP-CUS** | 10.255.20.0/22 | 1019 | 응용 프로그램 계층 VM
**ASR-DB-CUS** | 10.255.24.0/23 | 507 | 데이터베이스 VM

![허브 네트워크 아키텍처](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>피어링 연결 구성

각 지역의 허브는 다른 지역의 허브 및 허브 지역 내의 모든 VNet에 피어링됩니다. 이렇게 하면 허브에서 통신하고 한 지역 내의 모든 VNet을 볼 수 있습니다. 다음 사항에 유의하세요.

- 피어링은 양쪽 연결을 만듭니다. 즉, 첫 번째 VNet의 시작 피어에서 한 연결을 만들고, 두 번째 VNet에서 또 하나의 연결을 만듭니다.
- 하이브리드 배포에서는 온-프레미스 데이터 센터와 Azure 간 VPN 연결에서 피어 간에 전달되는 트래픽을 확인할 필요가 있습니다. 이렇게 하려면 피어링 연결에 설정해야 하는 몇 가지 특정 설정이 있습니다.

Contoso는 허브를 통한 스포크 VNet에서 온-프레미스 데이터 센터로의 모든 연결에 대해 트래픽을 전달하고 VPN 게이트웨이를 가로지르도록 허용해야 합니다.

##### <a name="domain-controller"></a>도메인 컨트롤러

VNET-PROD-EUS2 네트워크의 도메인 컨트롤러의 경우 Contoso는 트래픽이 EUS2 허브/프로덕션 네트워크 간에 그리고 VPN 연결을 통해 온-프레미스로 흐르게 하려고 합니다. 이를 위해 Contoso 관리자는 다음을 허용해야 합니다.

1. 피어링 연결에서 **전달된 트래픽 허용** 및 **게이트웨이 전송 허용** 구성을 설정합니다. 다음 예의 VNET-HUB-EUS2에서 VNET-PROD-EUS2로의 연결이 이에 해당합니다.

    ![피어링](./media/contoso-migration-infrastructure/peering1.png)

2. 피어링 연결의 다른 쪽인 VNET-PROD-EUS2에서 VNET-HUB-EUS2로의 연결에서 **전달된 트래픽 허용** 및 **원격 게이트웨이 사용** 구성을 설정합니다.

    ![피어링](./media/contoso-migration-infrastructure/peering2.png)

3. 온-프레미스에서 VPN 터널을 통해 VNet으로 라우팅하도록 로컬 트래픽을 전달하는 고정 경로를 설정합니다. Contoso에서 Azure로 VPN 터널을 제공하는 게이트웨이에서 구성이 완료됩니다. 여기에는 RRAS가 사용됩니다.

    ![피어링](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>프로덕션 네트워크 

스포크 피어 네트워크는 허브를 통해 다른 지역의 스포크 피어 네트워크를 볼 수 없습니다.

두 지역에 있는 Contoso의 프로덕션 네트워크에서 서로를 확인할 수 있게 하려면 Contoso 관리자는 VNET-PROD-EUS2 및 VENT-PROD-CUS에 대한 직접 피어링 연결을 만들어야 합니다. 

![피어링](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>DNS 설정

가상 네트워크에 리소스를 배포하는 경우 도메인 이름 확인에 대한 몇 가지 선택 사항이 있습니다. Azure에서 제공하는 이름 확인을 사용하거나 확인을 위한 DNS 서버를 제공할 수 있습니다. 어떤 방법으로 이름을 확인할지는 사용하는 리소스가 서로 어떻게 통신해야 하는지에 따라 다릅니다. Azure DNS 서비스에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution).

Contoso 관리자는 Azure DNS 서비스가 하이브리드 환경에 적합하지 않다고 결정했습니다. 대신 온-프레미스 DNS 서버를 활용할 것입니다.

- 하이브리드 네트워크이므로 온-프레미스 및 Azure의 모든 VM이 제대로 작동하려면 이름을 확인할 수 있어야 합니다. 즉, 모든 VNet에 사용자 지정 DNS 설정을 적용해야 합니다.
- Contoso는 현재 Contoso 데이터 센터와 지점에 DC를 배포했습니다. 주 DNS 서버는 CONTOSODC1(172.16.0.10) 및 CONTOSODC2(172.16.0.1)입니다.
- VNet이 배포되면 온-프레미스 도메인 컨트롤러가 네트워크에서 DNS 서버로 사용되도록 설정됩니다. 
- 이렇게 구성하려면 VNet에서 사용자 지정 DNS를 사용할 때 Azure의 재귀 확인자 IP 주소(예: 168.63.129.16)를 DNS 목록에 추가해야 합니다.  이를 위해 Contoso는 각 VNet에 DNS 서버 설정을 구성합니다. 예를 들어 VNET-HUB-EUS2 네트워크에 대한 사용자 지정 DNS 설정은 다음과 같습니다.
    
    ![사용자 지정 DNS](./media/contoso-migration-infrastructure/custom-dns.png)

Contoso는 자체의 온-프레미스 도메인 컨트롤러 외에도 Azure 네트워크를 지원하기 위해 각 지역마다 두 개씩 4개 이상을 추가로 구현하려고 합니다. Contoso는 Azure에 다음과 같이 배포합니다.

**지역** | **DC** | **VNet** | **서브넷** | **IP 주소**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.5
CUS | CONTOSODC5 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4
CUS | CONTOSODC6 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4

온-프레미스 도메인 컨트롤러가 배포되면 Contoso는 DNS 서버 목록에 새 도메인 컨트롤러가 포함되도록 두 지역의 네트워크에서 DNS 설정을 업데이트해야 합니다.



#### <a name="set-up-domain-controllers-in-azure"></a>Azure에서 도메인 컨트롤러 설정

네트워크 설정이 업데이트되면 Contoso 관리자는 Azure에서 도메인 컨트롤러를 구축할 준비가 되었습니다.

1. Azure Portal에서 새 Windows Server VM을 새 VNet에 배포합니다.
2. VM의 각 위치에 가용성 집합을 만듭니다. 가용성 집합에서 수행하는 작업은 다음과 같습니다.
   - Azure 패브릭에서 Azure 지역의 다른 인프라로 VM을 분리하는지 확인합니다. 
   - Contoso는 Azure의 VM에 99.95% SLA를 적용할 수 있습니다.  [자세히 알아보기](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).

     ![가용성 그룹](./media/contoso-migration-infrastructure/availability-group.png) 
3. VM이 배포되면 VM에 대한 네트워크 인터페이스를 엽니다. 사설 IP 주소를 정적으로 설정하고 유효한 주소를 지정합니다.

    ![VM NIC](./media/contoso-migration-infrastructure/vm-nic.png)

4. 이제 새 데이터 디스크를 VM에 연결합니다. 이 디스크에는 Active Directory 데이터베이스와 sysvol 공유가 포함됩니다. 
   - 디스크의 크기에 따라 지원되는 IOPS 수가 결정됩니다.
   - 시간이 지남에 따라 환경이 커질수록 디스크 크기가 증가해야 할 수도 있습니다.
   - 호스트 캐싱을 위해 드라이브를 읽기/쓰기로 설정하면 안됩니다. Active Directory 데이터베이스는 이 기능을 지원하지 않습니다.

     ![Active Directory 디스크](./media/contoso-migration-infrastructure/ad-disk.png)

5. 디스크가 추가되면 [원격 데스크톱]을 통해 VM에 연결하고 [서버 관리자]를 엽니다.
6. 그런 다음, **파일 및 저장소 서비스**에서 [새 볼륨 마법사]를 실행하여 드라이브에 로컬 VM에서 F: 이상의 문자가 지정되도록 합니다.

     ![새 볼륨 마법사](./media/contoso-migration-infrastructure/volume-wizard.png)

7. [서버 관리자]에서 **Active Directory Domain Services** 역할을 추가합니다. 그런 다음, VM을 도메인 컨트롤러로 구성합니다.

      ![서버 역할](./media/contoso-migration-infrastructure/server-role.png)  

9. VM을 DC로 구성하고 다시 부팅한 후 [DNS 관리자]를 열고 Azure DNS 확인자를 전달자로 구성합니다.  이렇게 하면 DC가 Azure DNS에서 확인할 수 없는 DNS 쿼리를 전달할 수 있습니다.

    ![DNS 전달자](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. 이제 각 VNet에 대한 사용자 지정 DNS 설정을 VNet 지역에 해당하는 도메인 컨트롤러로 업데이트합니다. 여기서는 온-프레미스 DC가 목록에 포함됩니다.

### <a name="set-up-active-directory"></a>Active Directory 설정

AD는 네트워킹에서 중요한 서비스이며 올바르게 구성되어야 합니다. Contoso 관리자는 Contoso 데이터 센터와 EUS2 및 CUS 지역에 대한 AD 사이트를 구축합니다.  

1. 데이터 센터 사이트(ContosoDatacenter)와 함께 두 개의 새 사이트(AZURE-EUS2 및 AZURE-CUS)를 만듭니다.
2. 사이트가 만들어지면 사이트에서 VNet 및 데이터 센터와 일치하는 서브넷을 만듭니다.

    ![DC 서브넷](./media/contoso-migration-infrastructure/dc-subnets.png)

3. 다음으로, 두 개의 사이트 링크를 만들어 모든 항목을 연결합니다. 그런 다음, 도메인 컨트롤러를 해당 위치로 이동해야 합니다.

    ![DC 링크](./media/contoso-migration-infrastructure/dc-links.png)

4. 모든 구성 작업이 완료되면 Active Directory 복제 토폴로지가 적용됩니다.
    
    ![DC 복제](./media/contoso-migration-infrastructure/ad-resolution.png)

5. 모든 작업이 완료되면 도메인 컨트롤러 및 사이트 목록이 온-프레미스 Active Directory 관리 센터에 표시됩니다.

    ![AD 관리 센터](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>5단계: 거버넌스 계획

Azure는 서비스와 Azure 플랫폼 전반에 걸친 거버넌스 제어 범위를 제공합니다. 옵션의 기본 사항에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/security/governance-in-azure).

Contoso는 ID 및 액세스 제어를 구성하면서 이미 거버넌스와 보안의 일부 측면을 배치하기 시작했습니다. 대체로 다음 세 가지 영역을 고려해야 합니다.

- **정책**: Azure의 정책은 리소스에서 회사 요구 사항 및 SLA를 준수하도록 리소스에 대한 규칙과 효과를 적용하고 시행합니다.
- **잠금**: 권한이 있는 사용자만 수정할 수 있도록 Azure를 통해 구독, 리소스 그룹 및 기타 리소스를 잠글 수 있습니다.
- **태그**: 리소스는 태그를 사용하여 제어, 감사 및 관리할 수 있습니다. 태그는 메타데이터를 리소스에 연결하여 리소스 또는 소유자에 대한 정보를 제공합니다.

### <a name="set-up-policies"></a>정책 설정

Azure Policy 서비스는 리소스를 평가하여 현재 보유하고 있는 정책 정의를 준수하지 않는 리소스를 검색합니다. 예를 들어 특정 유형의 VM만 허용하거나 리소스에 특정 태그가 필요한 정책이 있을 수 있습니다. 

Azure 정책은 정책 정의를 지정하고, 정책 할당은 정책을 적용해야 하는 범위를 지정합니다. 범위는 관리 그룹에서 리소스 그룹에 이르기까지 다양할 있습니다. 정책을 만들고 관리하는 방법에 대해 [자세히 알아보세요](../governance/policy/tutorials/create-and-manage.md).

Contoso는 다음 두 가지 정책을 먼저 사용하려고 합니다.

- EUS2 및 CUS 지역에만 리소스를 배포할 수 있는 정책이 필요합니다.
- VM SKU를 승인된 SKU로만 제한하려고 합니다. 이 정책은 비용이 많이 드는 VM SKU가 사용되지 않도록 하기 위한 것입니다.

#### <a name="limit-resources-to-regions"></a>지역으로 리소스 제한

Contoso는 **허용되는 위치** 기본 제공 정책 정의를 사용하여 리소스 지역을 제한합니다.

1. Azure Portal에서 **모든 서비스**를 클릭하고 **정책**을 검색합니다.
2. **할당** > **정책 할당**을 차례로 선택합니다.
3. 정책 목록에서 **허용되는 위치**를 선택합니다.
4. **범위**를 Azure 구독의 이름으로 설정하고, 허용 목록에서 두 지역을 선택합니다.

    ![정책이 허용되는 지역](./media/contoso-migration-infrastructure/policy-region.png)

5. 정책은 기본적으로 **거부**로 설정됩니다. 즉, 사용자가 EUS2 또는 CUS에 없는 구독에서 배포를 시작하면 배포가 실패하게 됩니다. Contoso 구독에 속한 사용자가 미국 서부에 배포를 설정하려고 하면 다음과 같은 상황이 발생합니다.

    ![정책 실패](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>특정 VM SKU 허용

Contoso는 **가상 머신 SKU 허용** 기본 제공 정책 정의를 사용하여 구독에서 만들 수 있는 VM 유형을 제한합니다. 

![정책 SKU](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>정책 준수 확인

정책은 즉시 적용되며 Contoso는 리소스에서 규정을 준수하는지 확인할 수 있습니다. 

1. Azure Portal에서 **준수** 링크를 클릭합니다.
2. 준수 대시보드가 표시됩니다. 자세한 세부 정보를 드릴다운할 수 있습니다.

    ![정책 준수](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>잠금 설정

Contoso는 오랫동안 ITIL 프레임워크를 사용하여 시스템을 관리해 왔습니다. 프레임워크의 가장 중요한 측면 중 하나는 변경 제어이며, Contoso는 Azure 배포에서 변경 제어를 구현하려고 합니다.

Contoso에서 구현하려는 잠금은 다음과 같습니다.

- 모든 프로덕션 또는 장애 조치 구성 요소는 읽기 전용 잠금이 있는 리소스 그룹에 있어야 합니다.  즉, 프로덕션 항목을 수정하거나 삭제하려면 잠금을 제거해야 합니다. 
- 비프로덕션 리소스 그룹에는 CanNotDelete 잠금이 있습니다. 즉, 권한이 있는 사용자가 리소스를 읽거나 수정할 수 있지만 삭제할 수는 없습니다.

잠금에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="set-up-tagging"></a>태그 지정 설정

추가되는 리소스를 추적하기 위해 Contoso에서 리소스를 적절한 부서, 고객 및 환경과 연결하는 것이 점점 더 중요하게 됩니다. 

태그를 사용하면 리소스와 소유자에 대한 정보를 제공할 뿐만 아니라 리소스를 집계하고, 그룹화하고, 환불을 위해 해당 데이터를 사용할 수도 있습니다.

Contoso는 비즈니스에 적합한 방식으로 Azure 자산을 시각화해야 합니다. 예를 들어 역할이나 부서를 기준으로 자산을 시각화할 수 있습니다. 리소스는 태그를 공유하기 위해 동일한 리소스 그룹에 있을 필요가 없습니다. Contoso는 모든 사용자가 동일한 태그를 사용하도록 간단한 태그 분류를 만들려고 합니다.

**태그 이름** | **값**
--- | ---
CostCenter | 12345: SAP의 유효한 비용 센터여야 합니다.
BusinessUnit | SAP의 비즈니스 단위 이름입니다. CostCenter와 일치합니다.
ApplicationTeam | 응용 프로그램에 대한 지원을 담당하는 팀의 이메일 별칭입니다.
CatalogName | 리소스에서 지원하는 서비스 카탈로그당 응용 프로그램 또는 ShareServices의 이름입니다.
ServiceManager | 리소스에 대한 ITIL 서비스 관리자의 이메일 별칭입니다.
COBPriority | 회사에서 설정한 BCDR에 대한 우선 순위입니다. 값은 1-5입니다.
ENV | 가능한 값은 DEV, STG, PROD입니다. 이러한 값은 순서대로 개발, 준비 및 프로덕션을 나타냅니다.

예를 들면 다음과 같습니다. 

 ![Azure 태그](./media/contoso-migration-infrastructure/azure-tag.png)

태그가 만들어지면 Contoso는 돌아가서 새 Azure 정책 정의와 할당을 만들어 조직 전체에 필요한 태그를 사용하도록 합니다.


## <a name="step-6-consider-security"></a>6단계: 보안에 대한 고려

보안은 클라우드에서 매우 중요하며, Azure는 다양한 보안 도구와 기능을 제공합니다. 이는 안전한 Azure 플랫폼에서 보안 솔루션을 만드는 데 도움이 됩니다. Azure 보안에 대해 자세히 알아보려면 [신뢰할 수 있는 클라우드의 신뢰도](https://azure.microsoft.com/overview/trusted-cloud/)를 참조하세요.

Contoso에서 고려해야 할 몇 가지 측면이 있습니다.

- **Azure Security Center**: Azure Security Center는 하이브리드 클라우드 워크로드에 통합 보안 관리 및 고급 위협 방지를 제공합니다. Security Center를 사용하여 워크로드에 보안 정책을 적용하고, 위협에 대한 노출을 제한하고, 공격을 검색 및 대응할 수 있습니다.  [자세히 알아보기](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **NSG(네트워크 보안 그룹)**: NSG는 적용되는 경우 Azure VNet에 연결된 리소스에 대한 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함된 필터(방화벽)입니다. [자세히 알아보기](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **데이터 암호화**: Azure Disk Encryption은 Windows 및 Linux IaaS 가상 머신 디스크를 암호화할 수 있도록 하는 기능입니다. [자세히 알아보기](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Azure Security Center 사용

Contoso는 새 하이브리드 클라우드 및 특히 Azure 워크로드의 보안 태세에 대한 빠른 보기를 찾고 있습니다.  이에 따라 Contoso는 다음 기능으로 시작하는 Azure Security Center를 구현하도록 결정했습니다. 

- 중앙 집중식 정책 관리
- 지속적인 평가
- 실행 가능한 권장 사항 

#### <a name="centralize-policy-management"></a>정책 관리 중앙 집중화

Contoso는 중앙 집중식 정책 관리를 통해 전체 환경에서 보안 정책을 중앙에서 관리하여 보안 요구 사항을 준수할 수 있도록 합니다. 모든 Azure 리소스에 적용되는 정책을 간단하고 빠르게 구현할 수 있습니다.

![보안 정책](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>평가 및 작업

Contoso는 컴퓨터, 네트워크, 저장소, 데이터 및 애플리케이션의 보안을 모니터링하는 지속적인 보안 평가를 활용하여 잠재적인 보안 문제를 검색합니다. 

- Security Center는 Contoso의 컴퓨팅, 인프라 및 데이터 리소스와 Azure 응용 프로그램 및 서비스에 대한 보안 상태를 분석합니다.
- 지속적인 평가를 통해 Contoso 운영 팀에서 보안 업데이트가 누락된 시스템 또는 노출된 네트워크 포트와 같은 잠재적인 보안 문제를 검색할 수 있습니다. 
- 특히 Contoso는 모든 VM이 보호되고 있는지 확인하려고 합니다. Security Center는 이를 지원하고, VM 상태를 확인하며, 보안 취약성을 악용하기 전에 이를 수정하기 위한 우선 순위를 지정하고 실행 가능한 권장 사항을 제시합니다.

![모니터링](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>NSG 사용

Contoso는 네트워크 보안 그룹을 사용하여 네트워크 트래픽을 가상 네트워크의 리소스로 제한할 수 있습니다.

- 네트워크 보안 그룹에는 원본 또는 대상 IP 주소, 포트 및 프로토콜에 따라 인바운드 또는 아웃바운드 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다.
- 규칙이 서브넷에 적용되면 서브넷에 있는 모든 리소스에 적용됩니다. 네트워크 인터페이스 외에도 서브넷에 배포된 Azure 서비스의 인스턴스도 포함됩니다.
- ASG(애플리케이션 보안 그룹)를 사용하면 네트워크 보안을 애플리케이션 구조의 자연스러운 확장으로 구성할 수 있으므로 VM을 그룹화하고 이러한 그룹에 따라 네트워크 보안 정책을 정의할 수 있습니다.
    - 애플리케이션 보안 그룹을 사용하는 경우 Contoso는 명시적 IP 주소를 수동으로 유지 관리하지 않고도 보안 정책을 광범위하게 다시 사용할 수 있습니다. 플랫폼은 명시적 IP 주소 및 여러 규칙 집합의 복잡성을 처리하여 비즈니스 논리에 집중할 수 있도록 합니다.
    - Contoso는 애플리케이션 보안 그룹을 보안 규칙의 원본 및 대상으로 지정할 수 있습니다. 보안 정책이 정의되면 Contoso는 VM을 만들고 VM NIC를 그룹에 할당할 수 있습니다. 


Contoso는 NSG와 ASG를 혼합하여 구현하며, NSG 관리를 수행하고자 합니다. 또한 NSG의 남용 및 운영자에 대한 추가된 복잡성을 걱정합니다. 이러한 측면을 고려하여 Contoso는 다음 작업을 수행합니다.

- 모든 서브넷(남-북)에서 들어오고 나가는 모든 트래픽에는 NSG 규칙이 적용됩니다(허브 네트워크의 GatewaySubnets 제외).
- 모든 방화벽이나 도메인 컨트롤러는 서브넷 NSG와 NIC NSG 모두에서 보호됩니다.
- 모든 프로덕션 애플리케이션에는 ASG가 적용됩니다.


이를 위해 Contoso는 애플리케이션을 찾는 방법에 대한 모델을 구축했습니다.

![보안](./media/contoso-migration-infrastructure/asg.png)


ASG와 연결된 NSG는 허용된 패킷만 네트워크의 한 부분에서 대상으로 흐를 수 있도록 최소 권한으로 구성됩니다.

**작업** | **Name** | **원본** | **대상** | **포트**
--- | --- | --- | --- | --- 
허용 | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | APP1-FE 80, 443
허용 | AllowWebToApp | APP1-FE | APP1-DB | 1433
허용 | AllowAppToDB | APP1-APP | 모두 | 모두
거부 | DenyAllInbound | 모두 | 모두 | 모두

### <a name="encrypt-data"></a>데이터 암호화

Azure Disk Encryption은 Azure Key Vault와 통합되어 키 자격 증명 모음 구독에서 디스크 암호화 키와 비밀을 제어하고 관리 할 수 있습니다. Azure Storage에서 VM 디스크의 모든 데이터가 사용되지 않을 때 암호화되도록 합니다.  

- Contoso는 특정 VM에 암호화가 필요하다고 결정했습니다.
- Contoso는 고객, 기밀 또는 PPI 데이터가 포함된 VM에 암호화를 적용합니다.


## <a name="conclusion"></a>결론

이 문서에서 Contoso는 Azure 구독, 하이브리드 식별, 재해 복구, 네트워킹, 거버넌스 및 보안에 대한 Azure 인프라와 정책을 설정했습니다. 

Contoso에서 여기서 완료한 모든 단계가 클라우드로 마이그레이션하는 데 필요하지는 않습니다. 여기서는 모든 유형의 마이그레이션에 사용할 수 있고, 복원력과 확장성이 있는, 안전한 네트워크 인프라를 계획하려고 했습니다. 

이 인프라가 구축되면 Contoso는 마이그레이션으로 이동하여 시도할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

첫 번째 마이그레이션 시나리오로, Contoso는 [Azure로 마이그레이션하기 위해 온-프레미스 SmartHotel360 2계층 앱을 평가](contoso-migration-assessment.md)하려고 합니다. 
