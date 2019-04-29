---
title: Azure 보안 및 규정 준수 청사진 - NIST SP 800-171 준수 IaaS 웹 애플리케이션
description: Azure 보안 및 규정 준수 청사진 - NIST SP 800-171 준수 IaaS 웹 애플리케이션
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: b30094e264086f018acbf84144300df46c60ac4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610257"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Azure 보안 및 규정 준수 청사진 - NIST SP 800-171 준수 IaaS 웹 애플리케이션

## <a name="overview"></a>개요
[NIST 특별 발행물 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf)은 비연방 정보 시스템 및 조직에 있는 CUI(통제 미분류 정보)를 보호하기 위한 지침을 제공하고 있습니다. NIST SP 800-171에는 CUI 기밀성을 보호하기 위한 14개 보안 요구 사항이 규정되어 있습니다.

이 Azure 보안 및 규정 준수 청사진에서는 고객이 Azure에서 NIST SP 800-171 제어의 하위 집합을 구현하는 웹 애플리케이션 아키텍처를 배포하는 데 도움이 되는 지침을 제공합니다. 이 솔루션은 고객이 특정 보안 및 규정 준수 요구 사항을 충족할 수 있는 방식을 제시합니다. 또한 고객이 Azure에서 웹 애플리케이션을 직접 빌드하고 구성할 수 있는 기준으로도 사용됩니다.

이 참조 아키텍처, 관련 구현 가이드 및 위협 모델은 고객이 특정 요구 사항에 맞게 작업 방식을 조정할 수 있는 기준으로 사용하기 위해 제공되는 것으로, 프로덕션 환경에서 있는 그대로 사용해서는 안 됩니다. 이 아키텍처를 수정하지 않고 배포하는 것만으로는 NIST SP 800-171 요구 사항을 완전히 충족시킬 수 없습니다. 고객은 이 아키텍처를 사용하여 빌드된 솔루션에 대해 적절한 보안 및 규정 준수 평가를 수행해야 합니다. 요구 사항은 각 고객의 구체적인 구현에 따라 달라질 수 있습니다.

## <a name="architecture-diagram-and-components"></a>아키텍처 다이어그램 및 구성 요소
이 Azure 보안 및 규정 준수 청사진은 SQL Server 백 엔드가 있는 IaaS 웹 애플리케이션에 대한 참조 아키텍처를 배포합니다. 이 아키텍처는 웹 계층, 데이터 계층, Active Directory 인프라, Azure Application Gateway 및 Azure Load Balancer를 포함합니다. 웹 및 데이터 계층에 배포되는 VM(가상 머신)은 가용성 집합에서 구성됩니다. SQL Server 인스턴스는 고가용성을 제공하기 위해 AlwaysOn 가용성 그룹에서 구성됩니다. VM은 도메인에 조인되며 Active Directory 그룹 정책을 사용하여 운영 체제 수준에서 보안 및 규정 준수 구성을 적용합니다.

전체 솔루션은 고객이 Azure Portal에서 구성하는 Azure Storage를 기반으로 합니다. Storage는 스토리지 서비스 암호화를 통해 모든 데이터를 암호화하여 미사용 데이터의 기밀을 유지합니다. 또한 지역 중복 저장소가 사용되므로 고객의 기본 데이터 센터에서 사고가 발생해도 데이터가 손실되지 않습니다. 두 번째 복사본은 매우 멀리 떨어진 별도의 위치에 저장됩니다.

보안 향상을 위해 이 솔루션의 모든 리소스는 Azure Resource Manager를 통해 리소스 그룹으로 관리됩니다. 그리고 Azure AD(Active Directory) RBAC(역할 기반 액세스 제어)를 사용하여 배포된 리소스 및 Azure Key Vault의 키 액세스를 제어합니다. 시스템 상태는 Azure Monitor를 통해 모니터링됩니다. 고객은 로그를 캡처하도록 두 모니터링 서비스를 구성합니다. 시스템 상태는 쉽게 사용할 수 있는 단일 대시보드에 표시됩니다.

관리 요새 호스트는 관리자가 배포된 리소스에 액세스할 수 있는 보안 연결을 제공합니다. *참조 아키텍처 서브넷으로 데이터를 가져오는 작업 및 관리 작업용으로 VPN 또는 Azure ExpressRoute 연결을 구성하는 것이 좋습니다.*


![NIST SP 800-171 준수 IaaS 웹 애플리케이션 참조 아키텍처 다이어그램](images/nist171-iaaswa-architecture.png "NIST SP 800-171 준수 IaaS 웹 애플리케이션 참조 아키텍처 다이어그램")

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 자세한 내용은 [배포 아키텍처](#deployment-architecture) 섹션을 참조하세요.

- Azure Virtual Machines
    - (1) 관리/요새(Windows Server 2016 Datacenter)
    - (2) Active Directory 도메인 컨트롤러(Windows Server 2016 Datacenter)
    - (2) SQL Server 클러스터 노드(Windows Server 2016의 SQL Server 2017)
    - (2) Web/IIS(Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1) /16 네트워크
    - (5) /24 네트워크
    - (5) 네트워크 보안 그룹
- 가용성 집합
    - (1) Active Directory 도메인 컨트롤러
    - (1) SQL 클러스터 노드
    - (1) 웹/IIS
- Azure Application Gateway
    - (1) 웹 애플리케이션 방화벽
        - 방화벽 모드: 방지
        - 규칙 집합: OWASP 3.0
        - 수신기 포트: 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Azure 모니터 (로그)
- Azure 리소스 관리자
- Azure Security Center
- Azure Storage
- Azure Automation
- 클라우드 감시
- Recovery Services 자격 증명 모음

## <a name="deployment-architecture"></a>배포 아키텍처
다음 섹션에서는 개발 및 구현 요소에 대해 자세히 설명합니다.

**요새 호스트**: 요새 호스트에는 사용자가이 환경에서 배포 된 리소스에 액세스 하는 데 사용할 수 있는 항목의 단일 지점입니다. 배스천 호스트는 수신 허용 목록에 있는 공용 IP 주소의 원격 트래픽만 허용하여 배포된 리소스에 대한 보안 연결을 제공합니다. 원격 데스크톱 트래픽을 허용하려면 트래픽의 원본을 NSG(네트워크 보안 그룹)에 정의해야 합니다.

이 솔루션은 다음 구성을 사용하여 도메인 조인 배스천 호스트로 VM을 만듭니다.
-   [맬웨어 방지 확장](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Diagnostics 확장](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   Key Vault를 사용하는 [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   사용하지 않을 때 VM 리소스 사용량을 줄이기 위한 [자동 종료 정책](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)
-   자격 증명과 기타 비밀이 실행 중인 운영 체제에서 격리되는 보호된 환경에서 실행되도록 [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) 사용

### <a name="virtual-network"></a>가상 네트워크
아키텍처는 10.200.0.0/16 주소 공간으로 개인 가상 네트워크를 정의합니다.

**네트워크 보안 그룹**: 이 솔루션은 웹, 데이터베이스, Active Directory 및 가상 네트워크 내의 관리에 대 한 별도 서브넷이 있는 아키텍처에 리소스를 배포 합니다. 서브넷은 개별 서브넷에 적용된 NSG 규칙에 의해 논리적으로 구분됩니다. 이러한 규칙은 시스템 및 관리 기능에 필요한 트래픽만 서브넷 간에 전송되도록 제한합니다.

이 솔루션으로 배포된 [NSG](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json)에 대한 구성을 참조하세요. 조직은 [이 문서](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)를 참조해서 위의 파일을 편집하여 NSG를 구성할 수 있습니다.

각 서브넷에는 다음과 같이 NSG가 있습니다.
- Application Gateway용 NSG 1개(LBNSG)
- 배스천 호스트용 NSG 1개(MGTNSG)
- 기본 및 백업 도메인 컨트롤러용 NSG 1개(ADNSG)
- SQL Server 및 클라우드 감시용 NSG 1개(SQLNSG)
- 웹 계층용 NSG 1개(WEBNSG)

### <a name="data-in-transit"></a>전송 중 데이터
Azure는 기본적으로 Azure 데이터 센터와의 모든 통신을 암호화합니다. 또한 Azure Portal을 통한 Storage에 대한 모든 트랜잭션은 HTTPS를 통해 발생합니다.

### <a name="data-at-rest"></a>미사용 데이터
이 아키텍처는 여러 조치를 통해 미사용 데이터를 보호합니다. 이러한 조치에는 암호화 및 데이터베이스 감사가 포함됩니다.

**Azure Storage**: 모든 [스토리지](https://azure.microsoft.com/services/storage/)에서는 암호화된 미사용 데이터 관련 요구 사항을 충족하기 위해 [스토리지 서비스 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption)를 사용합니다. 이 기능을 사용하면 NIST SP 800-171을 통해 정의된 조직의 보안 약정 및 규정 준수 요구 사항을 지원하기 위해 데이터를 보호할 수 있습니다.

**Azure Disk Encryption**: 디스크 암호화는 암호화 된 Windows IaaS VM 디스크에 사용 됩니다. [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)은 Windows의 BitLocker 기능을 사용하여 운영 체제 및 데이터 디스크에 볼륨 암호화를 제공합니다. 이 솔루션은 디스크 암호화 키를 쉽게 제어 및 관리할 수 있도록 Key Vault와 통합됩니다.

**SQL Server**: SQL Server 인스턴스에서 사용하는 데이터베이스 보안 조치는 다음과 같습니다.
-   [SQL Server 감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017)는 데이터베이스 이벤트를 추적하고 감사 로그에 기록합니다.
-   [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)는 데이터베이스, 관련 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행하여 미사용 정보를 보호합니다. 투명한 데이터 암호화를 사용하는 경우 저장된 데이터에 무단으로 액세스할 수 없습니다.
-   [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)은 적절한 권한이 부여될 때까지 데이터베이스 서버에 대한 모든 액세스를 차단합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다.
-   [암호화된 열](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017)을 사용하면 중요한 데이터가 데이터베이스 시스템 내에서 일반 텍스트로 표시되지 않습니다. 데이터 암호화를 사용하도록 설정하면 키에 액세스할 수 있는 클라이언트 애플리케이션 또는 애플리케이션 서버만 일반 텍스트 데이터에 액세스할 수 있습니다.
- [동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017)은 권한이 없는 사용자 또는 애플리케이션을 대상으로 데이터를 마스킹하여 중요한 데이터가 노출되지 않도록 제한합니다. 이 기능은 중요할 가능성이 있는 데이터를 자동으로 검색하고 적용할 적절한 마스크를 제안할 수 있습니다. 동적 데이터 마스킹을 사용하면 무단 액세스로 인해 중요한 데이터가 데이터베이스 외부로 유출되지 않도록 액세스 횟수를 줄일 수 있습니다. *고객은 데이터베이스 스키마를 준수하도록 설정을 조정할 책임이 있습니다.*

### <a name="identity-management"></a>ID 관리
Azure 환경에서 데이터에 대한 액세스를 관리하는 기능을 제공하는 기술은 다음과 같습니다.
-   [Azure AD](https://azure.microsoft.com/services/active-directory/)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. 이 솔루션의 모든 사용자(SQL Server 인스턴스에 액세스하는 사용자 포함)는 Azure AD에서 생성됩니다.
-   Azure AD를 사용하여 애플리케이션에 대한 인증을 수행합니다. 자세한 내용은 [Azure AD와 애플리케이션을 통합하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요.
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 사용하면 관리자가 세분화된 액세스 권한을 정의하여 사용자가 자신의 작업을 수행하는 데 필요한 액세스 권한만 부여할 수 있습니다. 관리자는 모든 사용자에게 Azure 리소스에 대한 무제한 권한을 부여하는 대신, 데이터에 액세스하기 위한 특정 작업만 허용할 수 있습니다. 구독 액세스는 구독 관리자로 제한됩니다.
- 고객은 [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started)를 사용하여 특정 리소스에 액세스할 수 있는 사용자 수를 최소화할 수 있습니다. 관리자는 Azure AD Privileged Identity Management를 사용하여 권한 있는 ID와 이러한 ID의 리소스 액세스 권한을 검색, 제한 및 모니터링할 수 있습니다. 필요한 경우 이 기능을 사용하여 요청 시 JIT(Just-In-Time) 관리 액세스를 적용할 수도 있습니다.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)은 조직 ID에 영향을 주는 잠재적 취약성을 검색하며, 조직 ID와 관련하여 검색된 의심스러운 작업에 대한 자동화된 응답을 구성합니다. 또한 의심스러운 인시던트를 조사하며 해결을 위해 적절한 작업을 수행합니다.

### <a name="security"></a>보안
**비밀 관리**: 솔루션 사용 [Key Vault](https://azure.microsoft.com/services/key-vault/) 키 및 비밀 관리에 대 한 합니다. Key Vault를 사용하면 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호할 수 있습니다. 고객은 다음 Key Vault 기능을 통해 데이터를 보호할 수 있습니다.
- 필요에 따라 고급 액세스 정책이 구성됩니다.
- Key Vault 액세스 정책은 키와 비밀에 대한 최소 필수 권한으로 정의됩니다.
- Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
- Key Vault의 모든 키는 특별한 하드웨어 보안 모듈을 통해 보호됩니다. 키 유형은 하드웨어 보안 모듈로 보호되는 2,048비트 RSA 키입니다.
- RBAC를 통해 모든 사용자와 ID에 최소 필수 권한이 부여됩니다.
- Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
- 키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다
- 이 솔루션은 Key Vault와 통합되어 IaaS VM 디스크 암호화 키와 비밀을 관리합니다.

**패치 관리**: 이 참조 아키텍처의 일부분으로 배포 하는 Windows Vm은 Windows 업데이트 서비스에서 자동 업데이트를 수신 하려면 기본적으로 구성 됩니다. 또한 이 솔루션에는 필요한 경우 VM을 패치하기 위해 업데이트된 배포를 만들 수 있는 [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) 서비스가 포함되어 있습니다.

**맬웨어 방지**: [Microsoft 맬웨어 방지 프로그램](https://docs.microsoft.com/azure/security/azure-security-antimalware) Vm에 대 한 실시간 보호 기능을 통해 식별 하 고 바이러스, 스파이웨어 및 기타 악성 소프트웨어 제거를 제공 합니다. 고객은 알려진 악성 소프트웨어 또는 원치 않는 소프트웨어가 보호된 VM에서 설치 또는 실행을 시도할 때 생성되는 경고를 구성할 수 있습니다.

**Azure Security Center**: 고객은 [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)를 통해 여러 워크로드에 걸쳐 보안 정책을 중앙에서 적용 및 관리하고, 위협에 대한 노출을 제한하고, 공격을 검색하여 대응할 수 있습니다. 또한 Security Center는 Azure 서비스의 기존 구성에 액세스하여 보안 태세를 개선하고 데이터를 보호하는 데 유용한 구성 및 서비스 권장 사항을 제공합니다.

Security Center는 다양한 검색 기능을 사용하여 고객에게 환경을 대상으로 하는 잠재적인 공격에 대해 경고합니다. 이러한 경고는 트리거된 경고, 대상으로 지정된 리소스 및 공격의 출처에 대한 중요한 정보를 포함합니다. Security Center에는 위협 또는 의심스러운 활동이 발생할 때 트리거되는 미리 정의된 [보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) 집합이 있습니다. 고객은 [사용자 지정 경고 규칙](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)을 사용하여 환경에서 이미 수집된 데이터를 기반으로 하는 새 보안 경고를 정의할 수 있습니다.

Security Center는 우선 순위가 지정된 보안 경고 및 인시던트를 제공합니다. 고객은 Security Center를 통해 잠재적 보안 문제를 더 간편하게 검색하고 해결할 수 있습니다. 검색된 각 위협에 대해 [위협 인텔리전스 보고서](https://docs.microsoft.com/azure/security-center/security-center-threat-report)가 생성됩니다. 인시던트 대응 팀은 위협을 조사하고 수정할 때 해당 보고서를 사용할 수 있습니다.

이 참조 아키텍처는 Security Center에서 [취약성 평가](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) 기능을 사용합니다. 이 아키텍처를 구성하고 나면 Qualys 등의 파트너 에이전트가 파트너 관리 플랫폼에 취약성 데이터를 보고합니다. 차례로 파트너의 관리 플랫폼은 취약성 및 상태 모니터링 데이터를 Security Center에 다시 제공합니다. 고객은 이 정보를 사용하여 취약한 VM을 빠르게 파악할 수 있습니다.

**Azure Application Gateway**: 아키텍처는 웹 응용 프로그램 방화벽 구성 및 사용 하도록 설정 하는 OWASP 규칙 집합을 사용 하 여 응용 프로그램 게이트웨이 사용 하 여 보안 취약성의 위험을 줄입니다. 추가적인 기능은 다음과 같습니다.

- [종단 간 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL 오프로드](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal) 사용
- [TLS v1.0 및 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) 사용 안 함
- [웹 애플리케이션 방화벽](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)(방지 모드)
- [방지 모드](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal), OWASP 3.0 규칙 집합 사용
- [진단 로깅](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) 사용
- [사용자 지정 상태 프로브](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- 추가적인 보호 및 알림 기능을 제공하는 [Security Center](https://azure.microsoft.com/services/security-center) 및 [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations). Security Center는 평판 시스템도 제공합니다.

### <a name="business-continuity"></a>비즈니스 연속성

**고가용성**: 솔루션 배포의 모든 Vm을 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)합니다. 가용성 집합을 사용하면 VM을 격리된 여러 하드웨어 클러스터에 분산하여 가용성을 높일 수 있습니다. 계획되거나 계획되지 않은 유지 관리 이벤트 중에 99.95% Azure SLA를 충족하는 VM을 하나 이상 사용할 수 있습니다.

**Recovery Services 자격 증명 모음**: [Recovery Services 자격 증명 모음](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)은 백업 데이터를 저장하며, 이 아키텍처에 있는 Azure Virtual Machines의 모든 구성을 보호합니다. Recovery Services 자격 증명 모음을 사용하면 전체 VM을 복원하지 않고도 IaaS VM에서 파일과 폴더를 복원할 수 있습니다. 이 프로세스를 수행하면 복원 시간을 줄일 수 있습니다.

**클라우드 감시**: [클라우드 감시](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) 장애 조치 클러스터 쿼럼 감시는 중재 지점으로 Azure를 사용 하는 Windows Server 2016에서의 형식입니다. 다른 모든 쿼럼 감시와 마찬가지로 클라우드 감시는 투표를 받고 쿼럼 계산에 참여할 수 있으며 공개적으로 사용 가능한 표준 Azure Blob Storage를 사용합니다. 이러한 방식이 사용되므로 공용 클라우드에서 호스팅되는 VM에 대한 추가적인 유지 관리 오버헤드가 필요하지 않습니다.

### <a name="logging-and-auditing"></a>로깅 및 감사

Azure 서비스는 시스템 및 사용자 활동, 시스템 상태를 광범위하게 기록합니다.
- **활동 로그**: [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 인사이트를 제공합니다. 활동 로그는 작업의 개시 장치, 발생 시간 및 상태를 결정하는 데 도움이 될 수 있습니다.
- **진단 로그**: [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)는 모든 리소스에서 내보낸 모든 로그를 포함합니다. 이러한 로그에는 Windows 이벤트 시스템 로그, Storage 로그, Key Vault 감사 로그, Application Gateway 액세스 및 방화벽 로그가 포함됩니다. 모든 진단 로그는 보관을 위해 암호화된 중앙 집중식 Azure Storage 계정에 기록됩니다. 사용자는 특정 요구 사항에 맞게 보존 기간을 최대 730일까지 구성할 수 있습니다.

**Azure Monitor 로그**: 이러한 로그에 통합 됩니다 [Azure Monitor 로그](https://azure.microsoft.com/services/log-analytics/) 처리, 저장 및 대시보드를 보고 합니다. 수집된 데이터는 Log Analytics 작업 영역 내에서 각 데이터 형식용 개별 테이블로 구성됩니다. 이러한 방식으로 인해 원래 원본에 관계없이 모든 데이터를 함께 분석할 수 있습니다. Security Center는 Azure Monitor 로그와 통합 됩니다. 고객에 게 Kusto 쿼리를 사용 하 여 보안 이벤트 데이터를 액세스 하 고 다른 서비스의 데이터와 결합 하 수 있습니다.

다음 Azure [모니터링 솔루션](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) 이 아키텍처의 일부로 포함 됩니다.
-   [Active Directory 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory 상태 검사 솔루션은 일정한 간격으로 서버 환경의 위험 및 상태를 평가합니다. 이 솔루션은 배포된 서버 인프라 관련 우선 순위가 지정된 권장 사항 목록을 제공합니다.
- [SQL 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL 상태 검사 솔루션은 일정한 간격으로 서버 환경의 위험 및 상태를 평가합니다. 이 솔루션은 배포된 서버 인프라 관련 우선 순위가 지정된 권장 사항 목록을 고객에게 제공합니다.
- [에이전트 상태](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): 에이전트 상태 솔루션은 배포된 에이전트의 수와 에이전트의 지리적 분산을 보고합니다. 또한 응답하지 않는 에이전트의 수와 작동 데이터를 제출하는 에이전트의 수도 보고합니다.
-   [활동 로그 분석](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): 활동 로그 분석 솔루션은 고객에 대한 모든 구독에서 활동 로그를 분석하는 데 도움을 줍니다.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)은 Runbook을 저장, 실행 및 관리합니다. 이 솔루션에서 Runbook은 SQL Server에서 로그를 수집하는 데 도움이 됩니다. 고객은 Automation [변경 내용 추적](https://docs.microsoft.com/azure/automation/automation-change-tracking) 솔루션을 사용하여 환경의 변경 내용을 쉽게 파악할 수 있습니다.

**Azure Monitor**: 고객은 [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)를 통해 성능을 추적하고 보안을 유지하고 추세를 파악할 수 있습니다. 조직에서는 Monitor를 사용하여 감사를 수행하고 경고를 작성하고 데이터를 보관할 수 있습니다. 또한 Azure 리소스의 API 호출을 추적할 수도 있습니다.

## <a name="threat-model"></a>위협 모델

이 참조 아키텍처에 대한 데이터 흐름 다이어그램은 아래에 나와 있으며 [다운로드](https://aka.ms/nist171-iaaswa-tm)할 수도 있습니다. 고객은 이 모델을 통해 수정 시 시스템 인프라에서 발생 가능한 위험 요소를 파악할 수 있습니다.

![NIST SP 800-171 준수 IaaS 웹 애플리케이션 위협 모델](images/nist171-iaaswa-threat-model.png "NIST SP 800-171 준수 IaaS 웹 애플리케이션 위협 모델")

## <a name="compliance-documentation"></a>규정 준수 설명서

[Azure 보안 및 규정 준수 청사진 - NIST SP 800-171 고객 책임 매트릭스](https://aka.ms/nist171-crm)에는 NIST SP 800-171에서 요구하는 모든 보안 제어가 나와 있습니다. 이 매트릭스는 각 제어의 구현이 Microsoft, 고객 또는 둘 모두의 책임인지 여부를 자세히 설명합니다.

[Azure 보안 및 규정 준수 청사진 - NIST SP 800-171 IaaS 웹 애플리케이션 컨트롤 구현 매트릭스](https://aka.ms/nist171-iaaswa-cim)는 IaaS 웹 애플리케이션 아키텍처를 통해 적용되는 NIST SP 800-171 컨트롤에 대한 정보를 제공합니다. 이 매트릭스에는 포함되는 각 컨트롤의 요구 사항을 구현이 충족하는 방식에 대한 상세 설명이 포함되어 있습니다.

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항
### <a name="vpn-and-expressroute"></a>VPN 및 ExpressRoute
이 IaaS 웹 애플리케이션 참조 아키텍처의 일부로 배포된 리소스에 대한 연결을 안전하게 설정하도록 보안 VPN 터널 또는 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 구성해야 합니다. VPN 또는 ExpressRoute를 적절히 설정하면 고객이 전송 중인 데이터에 대한 보호 계층을 추가할 수 있습니다.

Azure를 통해 보안 VPN 터널을 구현하면 온-프레미스 네트워크와 Azure Virtual Network 간의 가상 사설 연결을 만들 수 있습니다. 이 연결은 인터넷을 통해 이루어지며, 고객은 이 연결을 사용하여 고객의 네트워크와 Azure 간에 암호화된 링크 내에서 정보를 안전하게 "터널링"할 수 있습니다. 사이트 간 VPN은 수십 년 동안 모든 규모의 기업에서 배포해 온 안전하고 완성도 높은 기술입니다. [IPsec 터널 모드](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))는 이 옵션에서 암호화 메커니즘으로 사용됩니다.

VPN 터널 내의 트래픽은 사이트 간 VPN을 사용하여 인터넷을 통과하므로 Microsoft는 훨씬 더 안전한 또 다른 연결 옵션을 제공합니다. ExpressRoute는 Azure 및 온-프레미스 위치 또는 Exchange 호스팅 공급자 간의 전용 WAN 링크입니다. ExpressRoute 연결은 고객이 사용하는 통신 서비스 공급자에 직접 연결됩니다. 그러므로 데이터가 인터넷을 통해 전송되지 않으며 인터넷에 노출되지 않습니다. 이러한 연결은 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다. 

온-프레미스 네트워크를 Azure로 확장하는 보안 하이브리드 네트워크를 구현하는 모범 사례를 [사용할 수 있습니다](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>고지 사항

- 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft는 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다. 
- 이 문서는 Microsoft 제품 또는 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다. 
- 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다. 
- 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다. 
- 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.
- 이 문서는 참조용으로 작성되었으며 고객이 특정 규정 준수 요구 사항 및 규정을 충족할 수 있는 모든 수단을 정의하는 데 사용되어서는 안됩니다. 고객은 승인된 고객 구현에 대해 자체 조직에서 법률 지원을 받아야 합니다.
