---
title: Azure Security and Compliance Blueprint - UK OFFICIAL 워크로드 준수 PaaS 웹 애플리케이션 호스팅
description: Azure Security and Compliance Blueprint - UK OFFICIAL 워크로드 준수 PaaS 웹 애플리케이션 호스팅
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 95e10f4727de239016a2e3c88571e74267e3967b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109330"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Azure 보안 및 규정 준수 청사진: UK OFFICIAL 워크로드 준수 PaaS 웹 애플리케이션 호스팅

## <a name="azure-security-and-compliance-blueprints"></a>Azure 보안 및 규정 준수 청사진

Azure Blueprint는 승인 또는 규정 준수 요구 사항이 있는 시나리오에 대한 솔루션을 제공하기 위해 클라우드 기반 아키텍처를 배포하는 지침 문서와 자동화 템플릿으로 구성되어 있습니다. Azure Blueprint는 Microsoft Azure 고객이 추가 요구 사항에 맞게 확장할 수 있는 기초 아키텍처를 프로비전하여 비즈니스 목표 전달을 가속화할 수 있도록 하는 지침 및 자동화 템플릿 컬렉션입니다.

## <a name="overview"></a>개요

이 Azure Security and Compliance Blueprint는 [UK OFFICIAL](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf)로 분류된 워크로드를 처리하는 데 적합한 Microsoft Azure [PaaS(Platform as a Service)](https://azure.microsoft.com/overview/what-is-paas/) 호스팅 웹 애플리케이션 아키텍처를 제공하기 위한 지침 및 자동화 스크립트를 제공합니다. 이 보안 분류는 공공 부문에서 만들거나 처리하는 대부분의 정보를 포함합니다. 여기에는 일상적인 비즈니스 작업 및 서비스가 포함되며, 미디어에서 분실, 도난 또는 게시되는 경우 그 중 일부는 해로운 결과를 초래할 수 있습니다. OFFICIAL 분류에 대한 일반적인 위협 프로필은 중요한 정보와 서비스를 제공하는 개인 비즈니스와 거의 같습니다. UK OFFICIAL은 제한된 역량과 리소스를 갖춘 공격자의 위협 또는 타협으로부터 영국 정부의 데이터 또는 서비스를 방어해야 할 필요성을 예상합니다. 이러한 공격자는 핵티비스트(hactivist), 단일 주장 압력 단체, 사회 고발 언론인, 유능한 개인 해커, 대다수의 범죄자 및 범죄 집단과 같지만 이에 국한되지는 않습니다.

이 청사진은 영국 NCSC(National Cyber Security Centre)에서 검토되었으며 NCSC 14개 클라우드 보안 원칙에 부합합니다.

아키텍처에서는 Azure [PaaS(Platform as a Service)](https://azure.microsoft.com/overview/what-is-paas/) 구성 요소를 사용하여 고객이 소프트웨어 라이선스를 구입하고 기본 애플리케이션 인프라와 미들웨어 또는 개발 도구 및 기타 리소스를 관리하는 비용과 복잡성을 방지할 수 있는 환경을 제공합니다. 고객은 비즈니스 가치를 제공하는 데 집중하여 개발한 애플리케이션과 서비스를 관리하는 한편, Microsoft Azure는 가상 머신, 저장소 및 네트워킹과 같은 다른 Azure 리소스를 관리하여 인프라 관리에 대한 [책임 분담](https://docs.microsoft.com/azure/security/security-paas-deployments#division-of-responsibility)을 Azure 플랫폼에 더 많이 배치합니다. [Azure App Services](https://azure.microsoft.com/services/app-service/)는 자동 크기 조정과 고가용성을 제공하고, Windows 및 Linux를 지원하며, GitHub, Azure DevOps 또는 모든 Git 리포지토리에서 자동화된 배포를 기본 서비스로 지원할 수 있도록 합니다. 개발자는 App Services를 사용하여 인프라 관리 오버헤드 없이 비즈니스 가치를 제공하는 데 집중할 수 있습니다. 개발 가능한 새로운 Java, PHP, Node.js, Python, HTML 또는 C# 웹 애플리케이션을 구축하거나 기존의 클라우드 또는 온-프레미스 웹 애플리케이션을 Azure App Services로 마이그레이션할 수도 있습니다(성능 확인을 위한 철저한 실사와 테스트가 필요함).

이 청사진은 공용 및 백오피스 사용자를 위한 보안 기반 [PaaS(Platform as a Service)](https://azure.microsoft.com/overview/what-is-paas/) 웹 기반 인터페이스를 프로비저닝하는 데 집중하고 있습니다. 이 청사진 설계 시나리오에서는 공용 사용자가 중요한 데이터를 안전하게 제출, 열람 및 관리할 수 있는 Azure 호스팅 웹 기반 서비스를 사용하는 것이 좋습니다. 또한 백오피스 또는 정부 운영자가 제출된 공용 사용자의 중요한 데이터를 안전하게 처리할 수 있습니다. 이 시나리오에 포함될 수 있는 사용 사례는 다음과 같습니다.

- 소득 신고서를 제출하는 사용자 및 제출된 신고서를 처리하는 정부 운영자,
- 웹 기반 애플리케이션을 통해 서비스를 요청하는 사용자 및 서비스의 유효성을 검사하고 제공하는 백오피스 사용자 또는
- 정부 기관 서비스에 관한 공용 도메인 지원 정보를 찾고 확인하는 사용자

청사진에서는 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 템플릿과 Azure CLI(명령줄 인터페이스) 스크립트를 사용하여 영국 NCSC(National Cyber Security Centre)의 14개 [클라우드 보안 원칙](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) 및 CIS(Center for Internet Security)의 [중요 보안 제어](https://www.cisecurity.org/critical-controls.cfm)에 맞는 환경을 배포합니다. NCSC는 고객이 서비스의 보안 속성을 평가하고 고객과 공급자 간의 책임 분담을 이해하는 데 도움을 주기 위해 해당 클라우드 보안 원칙을 사용하도록 권장하고 있습니다. Microsoft는 이러한 각 원칙에 대한 정보를 제공하여 책임 분담을 더 잘 이해할 수 있도록 했습니다. 이 아키텍처와 해당 Azure Resource Manager 템플릿은 Microsoft 백서 [Microsoft Azure를 사용하여 영국 클라우드용 14개의 클라우드 보안 컨트롤](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1)에서 지원됩니다. 이 아키텍처는 NCSC에서 검토되었으며 영국 NCSC 14개 클라우드 보안 원칙과 부합하므로 공공 부문 조직이 Microsoft Azure 클라우드에서 전 세계 또는 영국에서 클라우드 기반 서비스를 사용하여 규정 준수 의무를 빠르게 충족할 수 있습니다. 이 템플릿은 워크로드에 대한 인프라를 배포합니다. 애플리케이션 코드 및 지원되는 비즈니스 계층과 데이터 계층 소프트웨어는 고객이 설치하고 구성해야 합니다. 자세한 배포 지침은 [여기](https://aka.ms/ukofficial-paaswa-repo/)에 있습니다.

이 청사진은 기초 아키텍처입니다. 고객은 이 청사진을 OFFICIAL 분류 웹 기반 워크로드의 기반으로 사용하고 템플릿 및 리소스를 자체 요구 사항으로 확장할 수 있습니다. 이 청사진은 웹 기반 워크로드를 호스팅하기 위한 [IaaS(Infrastructure as a Service)](https://azure.microsoft.com/overview/what-is-iaas/)와 PaaS 구현 옵션을 고객에게 제공하는 [UK-OFFICAL 3계층 IaaS 웹 애플리케이션 청사진](https://aka.ms/ukofficial-iaaswa)의 원칙을 기반으로 합니다.

이 청사진을 배포하려면 Azure 구독이 필요합니다. Azure 구독이 없으면 평가판으로 빠르고 쉽게 가입할 수 있습니다. Azure를 시작하세요. 배포 지침을 보려면 [여기](https://aka.ms/ukofficial-paaswa-repo/)를 클릭하세요.

## <a name="architecture-and-components"></a>아키텍처 및 구성 요소

이 청사진은 UK OFFICIAL 워크로드를 지원하는 Azure 클라우드 환경에서 웹 애플리케이션 호스팅 솔루션을 제공합니다. 아키텍처에서는 Azure Platform as a Service 기능을 활용하는 보안 환경을 제공합니다. 환경 내에서 웹 프런트 엔드에 대한 비즈니스 서비스를 제공하는 API 앱 계층이 있는 두 개의 App Service 웹앱(공용 사용자용 및 백오피스 사용자용)이 배포됩니다. Azure SQL Database는 애플리케이션에 대한 관리되는 관계형 데이터 저장소로 배포됩니다. 플랫폼 외부에서의 연결 및 이러한 모든 구성 요소 간의 연결은 TLS 1.2를 통해 암호화되어 Azure Active Directory에서 인증된 액세스 권한으로 전송 개인 정보의 데이터를 보장합니다.

![UK OFFICIAL 워크로드 준수 PaaS 웹 애플리케이션 호스팅에 대한 참조 아키텍처 다이어그램](images/ukofficial-paaswa-architecture.png?raw=true "UK OFFICIAL 워크로드를 준수하는 PaaS 웹 애플리케이션 호스팅에 대한 참조 아키텍처 다이어그램")

배포 아키텍처의 일부로 보안 저장소 프로비전, 모니터링 및 로깅, 통합 보안 관리 및 고급 위협 보호, 관리 기능도 배포되어 고객이 이 솔루션에 대한 환경을 보호하고 모니터링하는 데 필요한 모든 도구를 사용할 수 있도록 합니다.

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 배포 아키텍처에 대한 세부 정보는 [배포 아키텍처](#deployment-architecture) 섹션에 있습니다.

- Azure Active Directory
- App Service
- 웹앱
- API 앱
- Azure DNS
- Key Vault
- Azure 모니터 (로그)
- Application Insights
- Azure 리소스 관리자
- Azure Security Center
- Azure SQL Database
- Azure Storage

## <a name="deployment-architecture"></a>배포 아키텍처

다음 섹션에서는 개발 및 구현 요소에 대해 자세히 설명합니다.

### <a name="security"></a>보안

#### <a name="identity-and-authentication"></a>ID 및 인증

이 청사진은 디렉터리 및 ID 관리 서비스를 통해 리소스에 대한 액세스를 보호합니다. 이 아키텍처는 [ID를 보안 경계로](https://docs.microsoft.com/azure/security/security-paas-deployments) 최대한 활용합니다. 

다음 기술은 Azure 환경에서 ID 관리 기능을 제공합니다.

- [Azure AD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. SQL Database에 액세스하는 사용자를 포함하여 모든 솔루션 사용자가 Azure Active Directory에서 만들어졌습니다.
- Azure AD를 사용하여 웹 애플리케이션을 연결하는 운영자와 Azure 리소스 관리에 대한 액세스의 인증이 수행됩니다. 자세한 내용은 [Azure Active Directory와 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요.
- 데이터베이스 열 암호화는 Azure AD를 사용하여 Azure SQL Database에 대한 애플리케이션을 인증합니다. 자세한 내용은 [Always Encrypted: SQL Database의 중요 데이터 보호](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)를 참조하세요.
- 일반 사용자가 연결하는 웹 애플리케이션은 공용 액세스로 구성됩니다. Active Directory 또는 소셜 네트워크 ID 공급자를 통해 계정을 만들거나 인증할 수 있도록 하기 위해 필요한 경우 [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)를 통합할 수 있습니다.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)은 잠재적인 취약성을 감지하고, 위험한 계정에는 조직의 ID 보안 상태를 향상시키는 권장 사항이 제공되며, 조직의 ID와 관련하여 감지된 의심스러운 활동에 대한 자동 응답을 구성하고, 의심스러운 인시던트를 조사하여 해결하기 위한 적절한 조치를 수행합니다.
- [Azure RBAC(Role-based Access Control)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)은 Azure에 대해 세밀하게 초점을 맞춘 액세스 관리를 구현합니다. 구독 액세스는 구독 관리자로 제한되며, Azure Key Vault 액세스는 키 관리 액세스가 필요한 사용자로만 제한됩니다.
- [Azure Active Directory 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)를 활용하여 고객은 위치, 디바이스, 상태 및 로그인 위험과 같은 특정 조건에 따라 환경에서 앱 또는 사용자의 액세스에 대한 추가 보안 제어를 적용할 수 있습니다.
- [Azure DDoS Protection](https://docs.microsoft.com/azure/security/security-paas-deployments#security-advantages-of-a-paas-cloud-service-model)은 애플리케이션 설계 모범 사례와 결합하여 상시 모니터링 트래픽 및 일반적인 네트워크 수준 공격에 대한 실시간 완화를 통해 DDoS 공격 방어를 제공합니다. PaaS 아키텍처에서는 플랫폼 수준의 DDoS 보호가 고객에게 투명하고 플랫폼에 통합되지만 애플리케이션 보안 설계 책임은 고객에게 있다는 점에 유의해야 합니다.

#### <a name="data-in-transit"></a>전송 중 데이터

외부에서 전송 중이거나 Azure 구성 요소 간에 전송 중인 데이터는 [TLS/SSL(전송 계층 보안/Secure Sockets Layer)](https://www.microsoft.com/TrustCenter/Security/Encryption)을 사용하여 보호됩니다. 이 경우 공유 비밀을 기반으로 하는 대칭 암호화를 사용하여 네트워크를 통해 이동할 때 통신을 암호화합니다. 기본적으로 네트워크 트래픽은 TLS 1.2를 사용하여 보호됩니다.

#### <a name="security-and-malware-protection"></a>보안 및 맬웨어 방지

[Azure Security Center](https://azure.microsoft.com/services/security-center/)는 모든 Azure 리소스의 보안 상태에 대한 중앙화된 보기를 제공합니다. 적절한 보안 제어가 준비되고 올바르게 구성되었는지와 주의가 필요한 리소스를 한눈에 빠르게 확인할 수 있습니다.

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview)는 Azure 배포를 최적화하기 위한 모범 사례를 따르는 데 도움이 되는 개인 설정된 클라우드 컨설턴트입니다. 리소스 구성 및 사용량 원격 분석을 수행하고 Azure 리소스의 경제성, 성능, 고가용성 및 보안을 개선하는 데 도움이 되는 해결 방법을 권장합니다.

[Microsoft 맬웨어 방지 프로그램](https://docs.microsoft.com/azure/security/azure-security-antimalware)은 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별하고 제거하는 데 도움이 되는 실시간 보호 기능입니다. 기본적으로이 기본 PaaS 가상 컴퓨터 인프라에 설치 되 고 고객에 게 투명 하 게 Azure 패브릭으로 관리 됩니다.

### <a name="paas-services-in-this-blueprint"></a>이 청사진의 PaaS 서비스

#### <a name="azure-app-service"></a>Azure App Service

Azure App Service는 인프라를 관리할 필요 없이 Java, PHP, Node.js Python, HTML 및 C#으로 개발된 웹 애플리케이션을 위해 완벽하게 관리되는 웹 호스팅 환경을 제공합니다. 여기서는 자동 크기 조정 및 고가용성을 제공하고, Windows 및 Linux를 모두 지원하며 [Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) 또는 Git 기반 리포지토리에서 자동화된 배포를 사용합니다.

App Service는 [ISO, SOC 및 PCI 규격](https://www.microsoft.com/TrustCenter/)이며, [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) 또는 소셜 로그인([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter) 및 [Microsoft 인증](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)을 통해 사용자를 인증합니다.

기본, 표준 및 프리미엄 요금제는 프로덕션 워크로드에 적용되는 요금제이며, 전용 Virtual Machine 인스턴스에서 실행됩니다. 각 인스턴스는 여러 개의 애플리케이션과 도메인을 지원할 수 있습니다. 또한 App Services는 필요한 경우 [IP 주소 제한](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)을 지원하여 신뢰할 수 있는 IP 주소로 전송되는 트래픽을 보호하고, [Key Vault](https://azure.microsoft.com/services/key-vault/) 및 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 같은 다른 PaaS 서비스에 안전하게 연결할 수 있도록 [Azure 리소스에 대한 관리 ID](https://docs.microsoft.com/azure/app-service/overview-managed-identity)를 지원합니다. 추가 보안이 필요한 경우 격리 계층 요금제는 개인 전용 Azure 환경에서 앱을 호스팅하며, 온-프레미스 네트워크와의 보안 연결이 필요하거나 추가 성능과 크기 조정이 필요한 앱에 이상적입니다.

이 템플릿에서 배포하는 App Service 기능은 다음과 같습니다.

- [표준](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) App Service 계획 계층
- 여러 App Service [배포 슬롯](https://docs.microsoft.com/azure/app-service/deploy-staging-slots): 개발, 미리 보기, QA, UAT 및 프로덕션(기본 슬롯).
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)에 연결하는 [Azure 리소스에 대한 관리 ID](https://docs.microsoft.com/azure/app-service/overview-managed-identity)([Azure SQL Database](https://azure.microsoft.com/services/sql-database/)에 대한 액세스를 제공하는 데 사용할 수도 있음) 
- 성능을 모니터링하는 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps)와의 통합
- [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 
- 메트릭 [경고](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

SQL Database는 관계형 데이터, 공간, JSON 및 XML과 같은 구조를 지원하는 Microsoft Azure의 범용 관계형 데이터베이스 관리 서비스입니다. SQL Database는 관리되는 단일 SQL 데이터베이스, [탄력적 풀](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)의 관리되는 SQL 데이터베이스 및 SQL [Managed Instances](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)(공개 미리 보기)를 제공합니다. [동적으로 확장 가능한 성능](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)을 제공하고 고도의 분석 및 보고를 위한 [columnstore 인덱스](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) 및 고도의 트랜잭션 처리를 위한 [메모리 내 OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory)와 같은 옵션을 제공합니다. Microsoft에서는 SQL 코드 베이스를 모두 원활하게 패치하고 업데이트하며 기본 인프라의 모든 관리를 추상화합니다.

이 청사진의 Azure SQL Database

Azure SQL Database 인스턴스는 다음 데이터베이스 보안 조치를 사용합니다.

- [서버 수준 및 데이터베이스 수준 방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) 또는 [가상 네트워크 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)을 사용하는 [가상 네트워크 서비스 엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)를 통해
- [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)는 악의적인 활동의 위협으로부터 Azure SQL Database 및 Azure 데이터웨어 하우스를 보호하는 데 도움이 됩니다. 애플리케이션에 대한 변경 없이 미사용 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행합니다.
- [Azure AD 인증](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)을 사용하면 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 한 곳에서 중앙 집중식으로 관리할 수 있습니다. 중앙 ID 관리는 데이터베이스 사용자 관리를 위한 단일 위치를 제공하며 권한 관리를 간소화합니다.
- 데이터베이스 관리에 Azure Active Directory 사용
- 저장소 계정에 대한 [감사 로그](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)
- 실패한 DB 연결에 대한 메트릭 [경고](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)
- [SQL 위협 감지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Always Encrypted 열](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

Microsoft [Azure Storage](https://azure.microsoft.com/services/storage/)는 가용성, 보안, 내구성, 확장성 및 중복성이 높은 스토리지를 제공하는 Microsoft 관리 클라우드 서비스입니다. Azure Storage는 Blob Storage, File Storage 및 Queue Storage로 구성됩니다.

#### <a name="azure-storage-in-this-blueprint"></a>이 청사진의 Azure Storage

이 템플릿에서 사용하는 Azure Storage 구성 요소는 다음과 같습니다.

- [Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- HTTPS 연결만 허용

#### <a name="data-at-rest"></a>미사용 데이터

[SSE(스토리지 서비스 암호화)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)를 통해 Azure Storage에 쓴 모든 데이터는 사용 가능한 가장 강력한 블록 암호화 중 하나인 256비트 AES 암호화를 통해 암호화됩니다. SSE에서 Microsoft 관리 암호화 키를 사용하거나 [사용자 고유의 암호화 키](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)를 사용할 수 있습니다.

Storage 계정은 [가상 네트워크 규칙](https://docs.microsoft.com/azure/storage/common/storage-network-security)을 사용하여 [Virtual Network 서비스 엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)를 통해 보호할 수 있습니다.

Azure Storage 보안에 대한 자세한 내용은 [보안 가이드](https://docs.microsoft.com/azure/storage/common/storage-security-guide)에서 찾을 수 있습니다.


### <a name="secrets-management"></a>비밀 관리

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)는 제3자가 액세스할 수 없도록 애플리케이션 키와 비밀을 보호하는 데 사용됩니다. Key Vault는 사용자 암호 저장소로 사용할 수 없습니다. 자격 증명 모음이라는 보안 컨테이너를 여러 개 만들 수 있습니다. 이러한 자격 증명 모음은 HSM(하드웨어 보안 모듈)에 의해 백업됩니다. 자격 증명 모음은 애플리케이션 비밀을 중앙 집중식으로 저장하여 보안 정보의 우발적인 손실 가능성을 줄이는 데 도움이 됩니다. Key Vault는 또한 저장된 모든 것에 대한 액세스를 제어하고 기록합니다. Azure Key Vault는 TLS(전송 계층 보안) 인증서의 요청 및 갱신을 처리할 수 있으므로, 강력한 인증서 수명 주기 관리 솔루션에 필요한 기능을 제공합니다.

#### <a name="azure-key-vault-in-this-blueprint"></a>이 청사진의 Azure Key Vault

- 고객이 연결하는 웹앱의 [관리 ID](https://docs.microsoft.com/azure/app-service/overview-managed-identity)에 읽기 액세스 권한이 부여된 저장소 액세스 키를 보유합니다.
- SQL Server DBA 암호를 보유합니다(별도의 자격 증명 모음에 있음).
- 진단 로깅

### <a name="monitoring-logging-and-audit"></a>모니터링, 로깅 및 감사

#### <a name="azure-monitor-logs"></a>Azure Monitor 로그

[Azure Monitor 로그](https://azure.microsoft.com/services/log-analytics/) 는 클라우드 리소스에서 생성 된 데이터 수집 및 분석 하도록 도와주는 Azure의 서비스 및 온-프레미스 환경입니다.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>이 blueprint에서 azure Monitor 로그

- SQL 평가
- Key Vault 진단
- Application Insights 연결
- Azure 동작 로그

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)는 여러 플랫폼의 웹 개발자를 위한 확장성 있는 APM(Application Performance Management) 서비스입니다. 라이브 웹 애플리케이션을 모니터링하는 데 사용되어 성능 이상을 자동으로 감지하고, 성능을 분석하며, 문제를 진단하고, 사용자가 애플리케이션과 상호 작용하는 방식을 이해합니다. Application Insight는 .NET, Node.js 및 Java EE를 포함하여 온-프레미스 또는 클라우드에서 호스팅되는 플랫폼에 배포할 수 있습니다. DevOps 프로세스와 통합되며, 다양한 개발 도구와의 연결 지점을 갖고 있습니다.

#### <a name="application-insights-in-this-blueprint"></a>이 청사진의 Application Insights

이 템플릿에서 사용하는 Application Insight 구성 요소는 다음과 같습니다.

- 사이트(운영자, 고객 및 API)별 Application Insights 대시보드

#### <a name="azure-activity-logs"></a>Azure 활동 로그

[Azure 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs#what-you-can-do-with-the-activity-log)는 구독에 대한 제어 평면 이벤트를 감사합니다. 활동 로그를 통해 구독의 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)에 대한 '누가, 무엇을, 언제'를 판단할 수 있습니다. 또한 작업 및 기타 관련 속성의 상태도 이해할 수 있습니다.

#### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)를 사용하면 메트릭, 활동 로그 및 진단 로그를 수집하도록 허용하여 Azure 서비스에 대한 핵심 모니터링을 수행할 수 있습니다. Azure Monitor는 대부분의 Microsoft Azure 서비스에 대해 기본 수준의 인프라 메트릭과 로그를 제공합니다.

## <a name="threat-model"></a>위협 모델

이 참조 아키텍처에 대한 데이터 흐름 다이어그램은 [다운로드](https://aka.ms/ukofficial-paaswa-tm)에서 사용할 수 있거나 아래에 나와 있습니다. 이 모델을 사용하면 고객이 수정할 때 발생할 수 있는 시스템 인프라의 잠재적 위험 요소를 파악할 수 있습니다.

![UK OFFICIAL 워크로드 위협 모델 준수 PaaS 웹 애플리케이션 호스팅](images/ukofficial-paaswa-threat-model.png?raw=true "UK OFFICIAL 워크로드 위협 모델 준수 PaaS 웹 애플리케이션 호스팅")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC 클라우드 보안 원칙 준수 설명서

Crown Commercial Service(영국 정부의 상업 및 조달 활동을 향상시키기 위해 노력하는 기관)는 Microsoft 범위 내의 엔터프라이즈 클라우드 서비스에 대한 분류를 OFFICIAL 수준의 모든 제품이 포함된 G-Cloud v6으로 갱신했습니다. Azure와 G-Cloud의 세부 정보는 [Azure 영국 G-Cloud 보안 평가 요약](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud)에서 찾을 수 있습니다.

이 청사진은 환경에서 UK-OFFICIAL로 분류된 워크로드를 지원하도록 보장하기 위해 NCSC [클라우드 보안 원칙](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)에서 설명하는 14개 클라우드 보안 원칙에 부합합니다.

[Azure Security and Compliance Blueprint - UK OFFICIAL 고객 책임 매트릭스](https://aka.ms/ukofficial-crm)(Excel 통합 문서)는 14개 클라우드 보안 원칙을 모두 나열하고, 각 원칙(또는 원칙의 하위 항목)에 대해 원칙 구현이 Microsoft, 고객 또는 둘 간에 공유되는 책임인지 여부를 나타냅니다.

[Azure Security and Compliance Blueprint - UK OFFICIAL 원칙 구현 준수 PaaS 웹 애플리케이션 매트릭스](https://aka.ms/ukofficial-paaswa-pim)(Excel 통합 문서)는 14개 클라우드 보안 원칙을 모두 나열하고, 고객 책임 매트릭스에서 고객 책임을 지정한 각 원칙(또는 원칙의 하위 항목)에 대해 1) 청사진에서 원칙을 구현하는지 여부 및 2) 구현이 원칙 요구 사항에 부합하는 방식에 대한 설명을 나타냅니다.  

또한 CSA(Cloud Security Alliance)는 클라우드 공급자에 대한 평가에서 고객을 지원하고 클라우드 서비스로 전환하기 전에 대답해야 하는 질문을 식별할 수 있도록 클라우드 제어 매트릭스를 게시했습니다. 이에 응하여 Microsoft Azure는 [CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)(CSA Consensus Assessment Initiative Questionnaire)에 응답했으며, 여기서는 Microsoft에서 제안된 원칙을 처리하는 방법에 대해 설명하고 있습니다.

## <a name="third-party-assessment"></a>타사 평가

이 청사진은 영국 NCSC(National Cyber Security Centre)에서 검토되었으며 NCSC 14개 클라우드 보안 원칙에 부합합니다.

자동화 템플릿은 UK Customer Success Unit Azure 클라우드 솔루션 설계 팀과 Microsoft 파트너인 [Ampliphae](https://www.ampliphae.com/)에서 테스트되었습니다.


## <a name="deploy-the-solution"></a>솔루션 배포

이 Azure Security 및 Compliance Blueprint Automation은 Azure Resource Manager의 API 서비스에서 Azure 내에 리소스를 배포하기 위해 처리하는 JSON 구성 파일과 PowerShell 스크립트로 구성됩니다. 자세한 배포 지침은 [여기](https://aka.ms/ukofficial-paaswa-repo)에 있습니다.

배포를 위해 세 가지 방법, 즉 빠른 테스트 환경 구축에 적합한 간단한 "기본" [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), 워크로드 환경에 대한 더 나은 구성을 제공하는 매개 변수화된 [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 방법 및 운영자가 Azure Portal을 통해 배포 매개 변수를 지정할 수 있는 Azure Portal 기반 배포가 제공됩니다. 

1.  [이](https://aka.ms/ukofficial-paaswa-repo) GitHub 리포지토리를 로컬 워크스테이션에 복제하거나 다운로드합니다.
2.  [방법 1: Azure CLI 2(Express 버전)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version)를 검토하고 제공된 명령을 실행합니다.
3.  [방법 1a: Azure CLI 2(스크립트 인수를 통한 배포 구성)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments)를 검토하고 제공된 명령을 실행합니다.
4.  [방법 2: Azure Portal 배포 프로세스](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process)를 검토하고 나열된 명령을 실행합니다.

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항

### <a name="api-management"></a>API Management

API App Service 앞에서 [Azure API Management](https://azure.microsoft.com/services/api-management/)를 사용하여 보안 계층, 제한 계층 및 API를 노출, 프록시 및 보호하는 제어 계층을 추가로 제공할 수 있습니다.

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)는 사용자가 등록하고, ID를 만들고, 공용 웹 애플리케이션에 대한 권한 부여 및 액세스를 제어할 수 있게 하는 컨트롤로 구현할 수 있습니다.

## <a name="disclaimer"></a>고지 사항

- 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft는 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다.
- 이 문서는 Microsoft 제품 또는 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다.
- 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.
- 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다.
- 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.
- 이 문서는 참조용으로 작성되었으며 고객이 특정 규정 준수 요구 사항 및 규정을 충족할 수 있는 모든 수단을 정의하는 데 사용되어서는 안됩니다. 고객은 승인된 고객 구현에 대해 자체 조직에서 법률 지원을 받아야 합니다.
