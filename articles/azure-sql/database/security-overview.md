---
title: 보안 개요
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: SQL Server와 어떻게 다른 지를 포함 하 여 Azure SQL Database 및 Azure SQL Managed Instance의 보안에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: 39119f62fa938f5f4f6529539d4ca9a84bdf8fd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94989193"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Azure SQL Database 및 SQL Managed Instance 보안 기능 개요
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

이 문서에서는 [Azure SQL Database](sql-database-paas-overview.md), [azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)및 [azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)를 사용 하 여 응용 프로그램의 데이터 계층을 보호 하는 기본 사항을 간략하게 설명 합니다. 여기서 설명하는 보안 전략은 아래 그림에 나와 있는 계층형 심층 방어 방식을 따르며 외부에서 내부로 적용됩니다.

![계층화 된 심층 방어 다이어그램입니다. 고객 데이터는 네트워크 보안, 액세스 관리, 위협 및 정보 보호 계층으로 되어 있습니다.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>네트워크 보안

Microsoft Azure SQL Database, SQL Managed Instance 및 Azure Synapse Analytics는 클라우드 및 엔터프라이즈 응용 프로그램에 대 한 관계형 데이터베이스 서비스를 제공 합니다. 방화벽은 고객 데이터를 보호 하기 위해 IP 주소 또는 Azure 가상 네트워크 트래픽 원본에 따라 액세스가 명시적으로 부여 될 때까지 서버에 대 한 네트워크 액세스를 차단 합니다.

### <a name="ip-firewall-rules"></a>IP 방화벽 규칙

IP 방화벽 규칙은 각 요청이 시작된 IP 주소를 기준으로 하여 데이터베이스 액세스 권한을 부여합니다. 자세한 내용은 [Azure SQL Database 개요 및 Azure Synapse Analytics 방화벽 규칙](firewall-configure.md)을 참조 하세요.

### <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙

[가상 네트워크 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)는 Azure 백본을 통해 가상 네트워크 연결을 확장하며, 트래픽이 생성되는 가상 네트워크 서브넷을 Azure SQL Database가 식별할 수 있도록 합니다. 트래픽이 Azure SQL Database로 전송되도록 하려면 SQL [서비스 태그](../../virtual-network/network-security-groups-overview.md)를 사용해 네트워크 보안 그룹을 통한 아웃바운드 트래픽을 허용합니다.

Azure SQL Database는 [가상 네트워크 규칙](vnet-service-endpoint-rule-overview.md)을 통해 Virtual Network 내의 선택한 서브넷에서 전송된 통신만 수락할 수 있습니다.

> [!NOTE]
> 방화벽 규칙을 사용 하 여 액세스를 제어 하는 것은 **SQL Managed Instance** 에는 적용 *되지* 않습니다. 필요한 네트워킹 구성에 대 한 자세한 내용은 [관리 되는 인스턴스에 연결](../managed-instance/connect-application-instance.md) 을 참조 하세요.

## <a name="access-management"></a>액세스 관리

> [!IMPORTANT]
> Azure 내에서 데이터베이스와 서버를 관리하는 작업은 포털 사용자 계정의 역할 할당을 통해 제어됩니다. 이 문서에 대 한 자세한 내용은 [Azure Portal의 Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 참조 하세요.

### <a name="authentication"></a>인증

인증은 사용자의 신원을 증명하는 과정입니다. Azure SQL Database 및 SQL Managed Instance는 두 가지 유형의 인증을 지원 합니다.

- **SQL 인증**:

    SQL 인증은 사용자 이름 및 암호를 사용 하 여 Azure SQL Database 또는 Azure SQL Managed Instance에 연결할 때 사용자의 인증을 나타냅니다. 서버를 만들 때 사용자 이름 및 암호를 사용 하는 **서버 관리자** 로그인을 지정 해야 합니다. **서버 관리자** 는 이러한 자격 증명을 사용 하 여 해당 서버 또는 인스턴스의 모든 데이터베이스를 데이터베이스 소유자로 인증할 수 있습니다. 그리고 나면 서버 관리자는 추가 SQL 로그인 및 사용자를 만들 수 있으며, 그러면 사용자가 사용자 이름과 암호를 사용하여 연결할 수 있습니다.

- **Azure Active Directory 인증**:

    Azure Active Directory 인증은 Azure Active Directory (Azure AD)에서 id를 사용 하 여 [Azure SQL Database](sql-database-paas-overview.md), [azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 및 [azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 에 연결 하는 메커니즘입니다. 관리자는 azure AD 인증을 사용 하 여 하나의 중앙 위치에서 다른 Azure 서비스와 함께 데이터베이스 사용자의 id 및 사용 권한을 중앙에서 관리할 수 있습니다. 그러면 스토리지해야 하는 암호를 최소화하고 중앙 집중식 암호 순환 정책을 사용할 수 있습니다.

     SQL Database를 사용한 Azure AD 인증을 사용하려면 서버 관리자 **Active Directory 관리자** 를 만들어야 합니다. 자세한 내용은 [Azure Active Directory 인증을 사용하여 SQL Database에 연결](authentication-aad-overview.md)을 참조하세요. Azure AD 인증에서는 관리 계정과 페더레이션된 계정이 모두 지원됩니다. 페더레이션된 계정은 Azure AD와 페더레이션된 고객 도메인용 Windows 사용자 및 그룹을 지원합니다.

    사용 가능한 추가 Azure AD 인증 옵션으로는 [다단계 인증](../../active-directory/authentication/concept-mfa-howitworks.md) 및 [조건부 액세스](conditional-access-configure.md)를 비롯한 [SQL Server Management Studio용 Active Directory 유니버설 인증](authentication-mfa-ssms-overview.md) 연결이 있습니다.

> [!IMPORTANT]
> Azure 내에서 데이터베이스와 서버를 관리하는 작업은 포털 사용자 계정의 역할 할당을 통해 제어됩니다. 이 문서에 대 한 자세한 내용은 [Azure Portal의 Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 참조 하세요. 방화벽 규칙을 사용 하 여 액세스를 제어 하는 것은 **SQL Managed Instance** 에는 적용 *되지* 않습니다. 필요한 네트워킹 구성에 대 한 자세한 내용은 [관리 되는 인스턴스에 연결 하](../managed-instance/connect-application-instance.md) 는 다음 문서를 참조 하세요.

## <a name="authorization"></a>권한 부여

권한 부여는 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스 내에서 사용자에 게 할당 된 권한을 나타내며 사용자가 수행할 수 있는 작업을 결정 합니다. 권한은 [데이터베이스 역할](/sql/relational-databases/security/authentication-access/database-level-roles) 에 사용자 계정을 추가 하 고 해당 역할에 데이터베이스 수준 사용 권한을 할당 하거나 사용자에 게 특정 [개체 수준 사용 권한을](/sql/relational-databases/security/permissions-database-engine)부여 하 여 제어 됩니다. 자세한 내용은 [로그인 및 사용자](logins-create-manage.md)를 참조하세요.

필요한 경우 사용자 지정 역할을 만드는 것이 가장 좋습니다. 작업 기능을 수행 하는 데 필요한 최소한의 권한만 가진 사용자를 역할에 추가 합니다. 사용자에 게 직접 사용 권한을 할당 하지 마십시오. 서버 관리자 계정은 다양 한 권한이 있는 기본 제공 db_owner 역할의 구성원이 며, 관리 업무를 사용 하는 소수의 사용자 에게만 부여 되어야 합니다. 응용 프로그램의 경우 [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) 를 사용 하 여 호출 된 모듈의 실행 컨텍스트를 지정 하거나 제한 된 권한으로 [응용 프로그램 역할](/sql/relational-databases/security/authentication-access/application-roles) 을 사용 합니다. 이렇게 하면 데이터베이스에 연결 하는 응용 프로그램에 응용 프로그램에 필요한 최소한의 권한만 부여 됩니다. 이러한 모범 사례를 따르면 발전시키도 구분 됩니다.

### <a name="row-level-security"></a>행 수준 보안

RLS(행 수준 보안)를 사용하면 고객이 쿼리를 실행하는 사용자의 특성(예: 그룹 멤버 자격 또는 실행 컨텍스트)에 따라 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. Row-Level 보안을 사용 하 여 사용자 지정 레이블 기반 보안 개념을 구현할 수도 있습니다. 자세한 내용은 [행 수준 보안](/sql/relational-databases/security/row-level-security)을 참조 하세요.

![Row-Level 보안이 클라이언트 앱을 통해 사용자가 액세스할 수 없도록 SQL 데이터베이스의 개별 행을 방패 함을 보여 주는 다이어그램입니다.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>위협 보호

SQL Database 및 SQL Managed Instance 감사 및 위협 검색 기능을 제공 하 여 고객 데이터를 보호 합니다.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Azure Monitor 로그 및 Event Hubs의 SQL 감사

SQL Database 및 SQL Managed Instance 감사는 데이터베이스 작업을 추적 하 고, 고객 소유 Azure storage 계정의 감사 로그에 데이터베이스 이벤트를 기록 하 여 보안 표준 준수를 유지 하는 데 도움이 됩니다. 사용자는 감사를 통해 진행 중인 데이터베이스 활동을 모니터링하고 이전 활동을 분석 및 조사하여 잠재적 위협이나 악용 의심 사례 및 보안 위반을 식별할 수 있습니다. 자세한 내용은 [SQL Database 감사 시작](../../azure-sql/database/auditing-overview.md)을 참조하세요.  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Advanced Threat Protection은 로그를 분석 하 여 비정상적인 동작을 감지 하 고 잠재적으로 유해한 데이터베이스 액세스 또는 악용 시도를 감지 합니다. 경고는 SQL 삽입, 잠재적 데이터 침입 및 무차별 암호 대입 공격과 같은 의심 스러운 활동에 대해 만들어지거나 권한 상승 및 위반 된 자격 증명 사용을 포착 하기 위한 액세스 패턴의 이상에서 발생 합니다. 경고는  [Azure Security Center](https://azure.microsoft.com/services/security-center/)에서 볼 수 있습니다. 여기에서 의심 스러운 활동에 대 한 세부 정보를 제공 하 고, 위협을 완화 하는 작업과 함께 제공 된 추가 조사에 대 한 권장 사항을 제공 합니다. 추가 요금을 위해 서버당 Advanced Threat Protection을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [SQL Database Advanced Threat Protection 시작](threat-detection-configure.md)을 참조 하세요.

![외부 공격자와 악의적인 참가자의 웹 앱에 대 한 SQL database에 대 한 sql 데이터베이스 액세스 모니터링 액세스를 보여 주는 다이어그램입니다.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

### <a name="transport-layer-security-encryption-in-transit"></a>전송 계층 보안 (암호화 전송 중)

[TLS (Transport Layer Security)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)를 사용 하 여 동작 중인 데이터를 암호화 하 여 고객 데이터를 보호 하는 SQL DATABASE, SQL Managed Instance 및 Azure Synapse 분석

SQL Database, SQL Managed Instance 및 Azure Synapse Analytics는 모든 연결에 대해 항상 암호화 (SSL/TLS)를 적용 합니다. 이렇게 하면 연결 문자열에서 **Encrypt** 또는 **trustservercertificate** 의 설정에 관계 없이 모든 데이터가 클라이언트와 서버 간에 "전송 중"으로 암호화 됩니다.

응용 프로그램에서 사용 하는 연결 문자열에서 암호화 된 연결을 지정 하 고 서버 인증서를 신뢰 _**하지**_ 않는 것이 좋습니다. 이렇게 하면 응용 프로그램이 서버 인증서를 확인 하 여 응용 프로그램이 중간 유형의 공격을 받을 수 없게 됩니다.

예를 들어 ADO.NET 드라이버를 사용 하는 경우  **Encrypt = True** 및 **Trustservercertificate = False** 를 통해이를 수행 합니다. Azure Portal에서 연결 문자열을 얻는 경우 올바른 설정이 사용됩니다.

> [!IMPORTANT]
> 일부 타사 드라이버는 기본적으로 TLS를 사용 하지 않거나 작동 하기 위해 이전 버전의 TLS (<1.2)를 사용 하지 않을 수 있습니다. 이 경우 서버에서 데이터베이스에 연결할 수 있습니다. 그러나 특히 중요 한 데이터를 저장 하는 경우 이러한 드라이버와 응용 프로그램이 SQL Database에 연결 하도록 허용 하는 보안 위험을 평가 하는 것이 좋습니다.
>
> TLS 및 연결에 대 한 자세한 내용은 [tls 고려 사항](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity) 을 참조 하세요.

### <a name="transparent-data-encryption-encryption-at-rest"></a>투명한 데이터 암호화(저장소 데이터 암호화)

[SQL Database, SQL Managed Instance 및 Azure Synapse Analytics 용 TDE (투명 한 데이터 암호화)](transparent-data-encryption-tde-overview.md) 는 미사용 데이터를 원시 파일이 나 백업에 대 한 무단 또는 오프 라인 액세스 로부터 보호 하는 데 사용할 수 있는 보안 계층을 추가 합니다. 일반적인 시나리오에는 데이터 센터 도난 또는 하드웨어 또는 미디어 (예: 디스크 드라이브 및 백업 테이프)의 안전 하지 않은 삭제가 포함 됩니다.TDE는 응용 프로그램 개발자가 기존 응용 프로그램을 변경할 필요가 없는 AES 암호화 알고리즘을 사용 하 여 전체 데이터베이스를 암호화 합니다.

Azure에서 새로 만든 모든 데이터베이스는 기본적으로 암호화 되며 데이터베이스 암호화 키는 기본 제공 서버 인증서로 보호 됩니다.  인증서 유지 관리 및 회전이 서비스에 의해 관리 되며 사용자의 입력이 필요 하지 않습니다. 암호화 키를 직접 제어하려는 고객은 [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md)에서 키를 관리할 수 있습니다.

### <a name="key-management-with-azure-key-vault"></a>Azure Key Vault으로 키 관리

[Bring Your Own Key](transparent-data-encryption-byok-overview.md) (byok [투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption) )를 사용 하 여 고객은 Azure의 클라우드 기반 외부 키 관리 시스템 [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md)를 사용 하 여 키 관리 및 회전의 소유권을 가져올 수 있습니다. 데이터베이스의 키 자격 증명 모음 액세스 권한이 철회되면 데이터베이스를 암호 해독하여 메모리로 읽어들일 수 없습니다. 중앙 키 관리 플랫폼을 제공하며 철저하게 모니터링되는 HSM(하드웨어 보안 모듈)을 활용하는 Azure Key Vault를 사용하면 키와 데이터 관리 작업을 분리하여 보안 규정 준수 요구 사항을 충족할 수 있습니다.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted(사용 중인 데이터 암호화)

![Always Encrypted 기능에 대 한 기본 사항을 보여 주는 다이어그램 잠금을 사용 하는 SQL 데이터베이스는 키가 포함 된 앱 에서만 액세스할 수 있습니다.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)는 신용 카드 번호, 주민 등록 번호 또는 _확인이 필요_ 한 데이터와 같이 특정 데이터베이스 열에 저장된 중요한 데이터를 액세스할 수 없도록 보호하는 기능입니다. 예를 들어 이 기능을 통해 데이터베이스에 액세스하여 관리 작업을 수행할 권한은 부여되었지만 업무상 암호화된 열의 특정 데이터에는 액세스할 필요가 없는 데이터베이스 관리자 또는 기타 권한 있는 사용자로부터 데이터를 보호할 수 있습니다. 데이터는 항상 암호화되므로 암호화 키 액세스 권한이 있는 클라이언트 애플리케이션에서 처리해야 하는 경우에만 암호화된 데이터의 암호가 해독됩니다. 암호화 키는 SQL Database 또는 SQL Managed Instance에 노출 되지 않으며 [Windows 인증서 저장소](always-encrypted-certificate-store-configure.md) 또는 [Azure Key Vault](always-encrypted-azure-key-vault-configure.md)에 저장 될 수 있습니다.

### <a name="dynamic-data-masking"></a>동적 데이터 마스킹

![동적 데이터 마스킹을 보여 주는 다이어그램 비즈니스 앱은 데이터를 비즈니스 앱으로 다시 보내기 전에 데이터를 마스킹하는 SQL 데이터베이스에 데이터를 보냅니다.](./media/security-overview/azure-database-ddm.png)

동적 데이터 마스킹에서는 권한이 없는 사용자로 마스킹하여 중요한 데이터 노출을 제한합니다. 동적 데이터 마스킹은 Azure SQL Database 및 SQL Managed Instance에서 잠재적으로 중요 한 데이터를 자동으로 검색 하 고 응용 프로그램 계층에 미치는 영향을 최소화 하면서 이러한 필드를 마스킹할 조치 가능한 권장 사항을 제공 합니다. 이 기능은 지정된 데이터베이스 필드를 통해 쿼리의 결과 집합에 있는 중요한 데이터를 혼란스럽게 만들면서 작동하지만 데이터베이스의 데이터를 변경하지는 않습니다. 자세한 내용은 [SQL Database 및 SQL Managed Instance 동적 데이터 마스킹 시작](dynamic-data-masking-overview.md)을 참조 하세요.

## <a name="security-management"></a>보안 관리

### <a name="vulnerability-assessment"></a>취약점 평가

[취약성 평가](sql-vulnerability-assessment.md)는 전반적인 데이터베이스 보안을 사전에 개선하기 위해 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 손쉽게 구성 가능한 서비스입니다. VA (취약성 평가)는 고급 SQL 보안 기능을 위한 통합 패키지인 SQL 용 Azure Defender 제공의 일부입니다. 취약성 평가는 SQL 포털의 중앙 Azure Defender를 통해 액세스 하 고 관리할 수 있습니다.

### <a name="data-discovery-and-classification"></a>데이터 검색 및 분류

데이터 검색 및 분류 (현재 미리 보기 상태)는 데이터베이스의 중요 한 데이터를 검색, 분류, 레이블 지정 및 보호 하기 위해 Azure SQL Database 및 SQL Managed Instance에 기본 제공 되는 고급 기능을 제공 합니다. 업무/금융, 의료, 개인 데이터 등의 매우 중요한 데이터를 검색하고 분류하는 작업은 조직 정보 보호 상태를 유지하는 데 중추적인 역할을 할 수 있습니다. 다음에 대한 인프라를 제공할 수 있습니다.

- 중요한 데이터에 대한 비정상적인 엑세스 모니터링(감사) 및 경고하는 것과 같은 다양한 보안 시나리오.
- 매우 중요한 데이터가 들어 있는 데이터베이스에 대한 액세스 제어 및 보안 강화.
- 데이터 프라이버시 표준 및 규정 준수 요구 사항을 충족하도록 지원

자세한 내용은 [데이터 검색 및 분류 시작](data-discovery-and-classification-overview.md)을 참조 하세요.

### <a name="compliance"></a>규정 준수

Azure SQL Database는 위의 기능 및 애플리케이션이 다양한 보안 요구 사항을 충족하는 데 도움이 될 수 있는 기능을 포함할 뿐 아니라, 정기 감사도 받고 있으며 다수의 규정 준수 표준 충족 인증도 취득했습니다. 자세한 내용은 SQL Database 준수 인증의 최신 목록을 찾을 수 있는 [Microsoft Azure 보안 센터](https://www.microsoft.com/trust-center/compliance/compliance-overview) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- SQL Database 및 SQL Managed Instance의 로그인, 사용자 계정, 데이터베이스 역할 및 사용 권한에 대 한 설명은 [로그인 및 사용자 계정 관리](logins-create-manage.md)를 참조 하세요.
- 데이터베이스 감사에 대 한 설명은 [감사](../../azure-sql/database/auditing-overview.md)를 참조 하세요.
- 위협 검색에 대 한 자세한 내용은 [위협 감지](threat-detection-configure.md)를 참조 하세요.