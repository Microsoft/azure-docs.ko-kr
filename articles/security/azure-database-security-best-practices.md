---
title: Azure 데이터베이스 보안 모범 사례 | Microsoft Docs
description: 이 문서에서는 Azure 데이터베이스 보안을 위한 일단의 모범 사례를 제공합니다.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: tomsh
ms.openlocfilehash: 3e244f89904ce9aca161ed1ea435f4137e42bc5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587536"
---
# <a name="azure-database-security-best-practices"></a>Azure 데이터베이스 보안 모범 사례
보안은 데이터베이스 관리에서 가장 중요하며 항상 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/)의 최우선 순위였습니다. HIPAA, ISO 27001/27002, PCI DSS 수준 1을 포함한 대부분의 규정 또는 보안 요구 사항을 충족할 수 있도록 데이터베이스를 철저히 보호할 수 있습니다. 보안 규정 준수 인증의 최신 목록은 [Microsoft Azure 보안 센터 사이트](https://azure.microsoft.com/support/trust-center/services/)에서 제공합니다. 또한 규정 요구 사항에 따라 특정 Azure 데이터 센터에 데이터베이스를 배치하도록 선택할 수도 있습니다.

이 문서에서는 Azure 데이터베이스 보안 모범 사례 모음에 대해 설명합니다. 이러한 모범 사례는 Azure 데이터베이스 보안 경험과 여러분과 같은 고객의 경험에서 얻은 것입니다.

각 모범 사례에 대해 다음과 같이 설명합니다.

-   각 모범 사례
-   해당 모범 사례를 사용해야 하는 이유
-   해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
-   해당 모범 사례를 사용하는 방법을 알아보는 방법

이 Azure 데이터베이스 보안 모범 사례 문서는 이 문서가 작성된 당시에 있었던 합의된 의견과 Azure 플랫폼 기능 및 특징 집합을 기반으로 합니다. 이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하도록 주기적으로 업데이트 됩니다.

## <a name="use-firewall-rules-to-restrict-database-access"></a>방화벽 규칙을 사용하여 데이터베이스 액세스 제한
Microsoft Azure SQL Database는 Azure 및 기타 인터넷 기반 애플리케이션의 관계형 데이터베이스 서비스를 제공합니다. 액세스 보안을 제공하기 위해 SQL Database는 다음을 사용하여 액세스를 제어합니다.

- IP 주소를 통해 연결을 제한하는 방화벽 규칙
- 사용자에게 자신의 ID를 증명하도록 요구하는 인증 메커니즘
- 특정 작업 및 데이터로 사용자를 제한하는 권한 부여 메커니즘

방화벽은 권한이 있는 컴퓨터를 지정할 때까지 데이터베이스 서버에 대한 모든 액세스를 금지합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다.

다음 그림에는 SQL Database에 설정된 서버 방화벽이 나와 있습니다.

![방화벽 규칙](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Azure SQL Database 서비스는 TCP 포트 1433을 통해서만 사용할 수 있습니다. 사용자의 컴퓨터에서 SQL Database에 액세스하려면 클라이언트 컴퓨터 방화벽이 TCP 포트 1433을 통해 나가는 TCP 통신을 허용해야 합니다. 다른 애플리케이션에서는 이러한 연결이 필요하지 않은 경우 방화벽 규칙을 사용하여 TCP 포트 1433에서 인바운드 연결을 차단합니다.

연결 프로세스의 일환으로, Azure 가상 머신의 연결은 각 작업자 역할에 대해 고유한 IP 주소 및 포트로 리디렉션됩니다. 포트 번호의 범위는 11000~11999입니다. TCP 포트에 대한 자세한 내용은 [1433을 제외한 ADO.NET 4.5용 포트](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.

SQL Database의 방화벽 규칙에 대한 자세한 내용은 [SQL Database 방화벽 규칙](../sql-database/sql-database-firewall-configure.md)을 참조하세요.

> [!Note]
> IP 규칙 이외에 방화벽도 가상 네트워크 규칙을 관리합니다. 가상 네트워크 규칙은 가상 네트워크 서비스 엔드포인트를 기반으로 합니다. 경우에 따라 가상 네트워크 규칙이 IP 규칙보다 더 좋을 수 있습니다. 자세한 내용은 [Azure SQL Database에 대한 가상 네트워크 서비스 엔드포인트 및 규칙](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)을 참조하세요.

## <a name="enable-database-authentication"></a>데이터베이스 인증 사용
SQL Database는 두 가지 인증 유형, 즉 SQL Server 인증 및 Azure AD 인증을 지원합니다.

### <a name="sql-server-authentication"></a>*SQL Server 인증*

이점은 다음과 같습니다.

- SQL Database에서 모든 사용자가 Windows 도메인을 통해 인증되지 않는 혼합 운영 체제 환경을 지원할 수 있습니다.
- SQL Database에서 SQL Server 인증이 필요한 이전 애플리케이션 및 파트너 제공 애플리케이션을 지원할 수 있습니다.
- 사용자가 알 수 없거나 신뢰할 수 없는 도메인에서 연결합니다. 기존 고객이 할당된 SQL Server 로그인을 사용해 연결하여 주문 상태를 수신하는 애플리케이션을 예로 들 수 있습니다.
- SQL Database에서 사용자가 자신의 ID를 만드는 웹 기반 애플리케이션을 지원할 수 있습니다.
- 소프트웨어 개발자가 미리 설정된 SQL Server 로그인을 기반으로 복잡한 권한 계층 구조를 사용하여 애플리케이션을 배포합니다.

> [!NOTE]
> SQL Server 인증에서는 Kerberos 보안 프로토콜을 사용할 수 없습니다.

SQL Server 인증을 사용하는 경우 다음을 수행해야 합니다.

- 강력한 자격 증명을 직접 관리합니다.
- 연결 문자열에서 자격 증명을 보호합니다.
- 네트워크를 통해 웹 서버에서 데이터베이스로 전달되는 자격 증명을 보호해야 할 수 있습니다. 자세한 내용은 [방법: ASP.NET 2.0에서 SQL 인증을 사용하여 SQL Server에 연결](/previous-versions/msp-n-p/ff648340(v=pandp.10))을 참조하세요.

### <a name="azure-active-directory-ad-authentication"></a>*Azure AD(Active Directory) 인증*
Azure AD 인증은 Azure AD의 ID를 사용하여 Azure SQL Database 및 [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에 연결하는 메커니즘입니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 ID를 중앙 위치 한 곳에서 관리할 수 있습니다. 중앙 ID 관리는 데이터베이스 사용자 관리를 위한 단일 위치를 제공하며 권한 관리를 간소화합니다.

> [!NOTE]
> SQL Server 인증을 사용하는 대신 Azure AD 인증을 사용하는 것이 좋습니다.

이점은 다음과 같습니다.

- SQL Server 인증에 대한 대안을 제공합니다.
- 데이터베이스 서버 전체에서 사용자 ID의 확산을 중지할 수 있습니다.
- 한 곳에서 암호를 회전할 수 있습니다.
- 고객이 외부(Azure AD) 그룹을 사용하여 데이터베이스 권한을 관리할 수 있습니다.
- Windows 통합 인증 또는 Azure Active Directory에서 지원하는 기타 인증을 사용하여 암호 저장을 제거할 수 있습니다.
- 포함된 데이터베이스 사용자를 통해 데이터베이스 수준에서 ID를 인증합니다.
- SQL Database에 연결되는 애플리케이션에 대한 토큰 기반 인증을 지원합니다.
- 도메인 동기화 없이 로컬 Azure Active Directory 인스턴스에 대한 AD FS(도메인 페더레이션) 또는 기본 사용자/암호 인증을 지원합니다.
- Azure AD는 Multi-Factor Authentication을 비롯한 Active Directory 유니버설 인증을 사용하는 SQL Server Management Studio를 통해 연결하도록 지원합니다. Multi-Factor Authentication은 전화 통화, 문자 메시지, PIN을 사용하는 스마트 카드, 모바일 앱 알림 등의 폭넓은 확인 옵션이 포함된 강력한 인증 기능을 제공합니다. 자세한 내용은 [SQL Database 및 SQL Data Warehouse를 사용한 Azure AD Multi-Factor Authentication에 대한 SSMS 지원](../sql-database/sql-database-ssms-mfa-authentication.md)을 참조하세요.

구성 단계에는 Azure AD 인증의 구성 및 사용을 위한 다음 절차가 포함됩니다.

- Azure AD를 만들고 채웁니다.
- 선택 사항: 현재 Azure 구독과 연결된 Active Directory 인스턴스를 연결하거나 변경합니다.
- Azure SQL Database 또는 [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)에 대한 Azure Active Directory 관리자를 만듭니다.
- 클라이언트 컴퓨터를 구성합니다.
- Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다.
- Azure AD ID를 사용하여 데이터베이스에 연결합니다.

자세한 내용은 [SQL Database, Managed Instance 및 SQL Data Warehouse에서 인증을 위해 Azure Active Directory 인증 사용](../sql-database/sql-database-aad-authentication.md)에서 확인할 수 있습니다.

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>암호화 및 행 수준 보안을 사용하여 데이터 보호
[Azure SQL Database 투명한 데이터 암호화](../sql-database/transparent-data-encryption-azure-sql.md)를 사용하면 디스크의 데이터를 보호하고 하드웨어 무단 액세스를 방지할 수 있습니다. 애플리케이션에 대한 변경 없이 미사용 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행합니다. 투명한 데이터 암호화는 데이터베이스 암호화 키라는 대칭 키를 사용하여 전체 데이터베이스의 저장소를 암호화합니다.

저장소 전체를 암호화하는 경우에도 데이터베이스 자체까지 암호화해야 합니다. 이렇게 하면 데이터를 철저하게 보호할 수 있습니다. Azure SQL Database를 사용하고 있고 신용 카드 또는 주민 등록 번호와 같은 중요한 데이터를 보호하려는 경우 FIPS 140-2 검증 256비트 AES 암호화를 사용하여 데이터베이스를 암호화할 수 있습니다. 이 암호화는 HIPAA, PCI 등 여러 업계 표준의 요구 사항을 충족합니다.

투명한 데이터 암호화를 사용하여 데이터베이스를 암호화할 때 [BPE(버퍼 풀 확장)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) 관련 파일은 암호화되지 않습니다. BPE 관련 파일에는 [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) 또는 [EFS(파일 시스템 암호화)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10))와 같은 파일 시스템 수준 암호화 도구를 사용해야 합니다.

보안 관리자나 데이터베이스 관리자처럼 권한이 부여된 사용자는 투명한 데이터 암호화를 사용하여 데이터베이스를 암호화했더라도 데이터에 액세스할 수 있으므로 아래 권장 사항도 준수해야 합니다.

- 데이터베이스 수준에서 SQL Server 인증을 사용해야 합니다.
- [RBAC 역할](../role-based-access-control/overview.md)을 통해 Azure AD 인증을 사용해야 합니다.
- 사용자와 애플리케이션이 인증에 별도의 계정을 사용하도록 해야 합니다. 이렇게 하면 사용자와 애플리케이션에 부여되는 사용 권한을 제한하고 악의적인 활동의 위험을 줄일 수 있습니다.
- db_datareader 또는 db_datawriter와 같은 고정된 데이터베이스 역할을 사용하여 데이터베이스 수준 보안을 구현해야 합니다. 애플리케이션용 사용자 지정 역할을 만들어 선택한 데이터베이스 개체에 대한 명시적 권한을 부여할 수도 있습니다.

데이터를 보호하는 다른 방법으로 다음을 사용해보세요.

- [셀 수준 암호화](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) 는 특정 열 또는 서로 다른 암호화 키를 가진 데이터의 셀도 암호화합니다.
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 사용합니다. 그러면 클라이언트에서 클라이언트 애플리케이션 내의 중요한 데이터를 암호화하므로 데이터베이스 엔진(SQL Database 또는 SQL Server)에 암호화 키가 공개되지 않습니다. 따라서 Always Encrypted는 데이터를 소유하고 볼 수 있는 사용자와 데이터를 관리하지만 액세스할 수 없는 사용자를 구별합니다.
- [행 수준 보안](/sql/relational-databases/security/row-level-security)을 사용합니다. 그러면 고객이 쿼리를 실행하는 사용자의 특성을 기반으로 하여 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. 특성의 예로는 그룹 멤버 자격 및 실행 컨텍스트가 있습니다.

데이터베이스 수준 암호화를 사용하지 않는 조직은 SQL 데이터베이스에 있는 데이터를 손상시키는 공격에 더 취약할 수 있습니다.

[Azure SQL Database를 사용하여 투명한 데이터 암호화](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) 문서에서 SQL Database 투명한 데이터 암호화에 대해 자세히 알아볼 수 있습니다.

## <a name="enable-database-auditing"></a>데이터베이스 감사 사용
SQL Server Database Engine 또는 개별 데이터베이스의 인스턴스를 감사하는 작업에는 이벤트를 추적하고 기록하는 작업이 포함됩니다. SQL Server의 경우 서버 수준 이벤트의 사양과 데이터베이스 수준 이벤트의 사양을 포함하는 감사를 만들 수 있습니다. 감사 이벤트는 이벤트 로그 또는 감사 파일에 기록될 수 있습니다.

설치에 대한 정부 또는 표준 요구 사항에 따라 SQL Server에 대한 여러 수준의 감사가 있습니다. SQL Server 감사는 다양한 서버 및 데이터베이스 개체에 대한 감사를 사용, 저장 및 확인하는 데 필요한 도구와 프로세스를 제공합니다.

[Azure SQL 데이터베이스 감사](../sql-database/sql-database-auditing.md)는 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 이벤트를 기록합니다.

감사를 수행하면 규정 준수 상태를 유지하고, 데이터베이스 활동을 파악하고, 업무상의 문제나 보안 위반을 나타낼 수 있는 불일치 및 비정상적 현상을 찾아낼 수 있습니다. 감사를 통해 규정 준수 표준을 더 원활하게 따를 수는 있지만, 규정 준수가 보장되지는 않습니다.

데이터베이스 감사 및 감사를 사용하도록 설정하는 방법에 대한 자세한 내용은 [SQL Database 감사 시작](../sql-database/sql-database-auditing.md)을 참조하세요.

## <a name="enable-database-threat-detection"></a>데이터베이스 위협 검색 사용
위협 검색에서는 단순히 위협을 검색만 하는 것이 아니라 다음과 같은 작업을 수행합니다.

- 데이터를 보호할 수 있도록 가장 중요한 데이터를 검색 및 분류합니다.
- 데이터베이스를 보호할 수 있도록 데이터베이스에서 보안 구성을 구현합니다.
- 잠재적 위협 발생 시 빠르게 대응하고 수정할 수 있도록 검색 및 대응을 진행합니다.

**모범 사례**: 데이터베이스의 중요한 데이터를 검색 및 분류하고 레이블을 지정합니다.   
**세부 정보**: Azure SQL Database에서 [데이터 검색 및 분류](../sql-database/sql-database-data-discovery-and-classification.md)를 사용하도록 설정하여 SQL 데이터베이스의 데이터를 분류합니다. Azure 대시보드에서 중요한 데이터 액세스를 모니터링하거나 보고서를 다운로드할 수 있습니다.

**모범 사례**: 데이터베이스 보안을 사전에 개선할 수 있도록 데이터베이스 취약성을 추적합니다.   
**세부 정보**: 잠재적 데이터베이스 취약성을 검사하는 Azure SQL Database [취약성 평가](../sql-database/sql-vulnerability-assessment.md) 서비스를 사용합니다. 이 서비스는 보안 취약성에 플래그를 지정하고 잘못된 구성, 과도한 권한, 보호되지 않는 중요 데이터와 같은 모범 사례의 문제를 표시하는 규칙 기술 자료를 사용합니다.

이러한 규칙은 Microsoft의 모범 사례를 기반으로 하며, 데이터베이스 및 중요한 데이터에 가장 심각한 위험 요소인 보안 문제를 중점적으로 표시합니다. 이러한 규칙은 데이터베이스 수준 문제 및 서버 수준 보안 문제(예: 서버 방화벽 설정 및 서버 수준 권한)에 모두 적용됩니다. 이러한 규칙은 규정 준수 표준을 충족하기 위한 규제 기관의 대다수 요구 사항을 나타내기도 합니다.

**모범 사례**: 위협 탐지를 사용합니다.  
**세부 정보**:  위협을 조사하고 완화하는 방법과 관련한 권장 사항과 보안 경고가 수신되도록 Azure SQL Database [위협 탐지](../sql-database/sql-database-threat-detection.md)를 사용하도록 설정합니다. 그러면 의심스러운 데이터베이스 활동, 잠재적 취약성 및 SQL 삽입 공격뿐만 아니라 비정상적인 데이터베이스 액세스 및 쿼리 패턴에 대한 경고도 표시됩니다.

고급 SQL 보안 기능용 통합 패키지인 [Advanced Threat Protection](../sql-database/sql-advanced-threat-protection.md)에는 앞에서 설명한 데이터 검색 및 분류, 취약성 평가, 위협 탐지 등의 서비스가 포함되어 있습니다. 이 패키지를 통해 이러한 기능을 한 곳에서 사용하도록 설정하고 관리할 수 있습니다.

이러한 기능을 사용하도록 설정하는 경우의 이점은 다음과 같습니다.

- 데이터 프라이버시 표준 및 규정 준수 요구 사항을 충족할 수 있습니다.
- 데이터베이스 액세스를 제어하고 보안을 강화할 수 있습니다.
- 변경 사항을 추적하기 어려운 동적 데이터베이스 환경을 모니터링할 수 있습니다.
- 잠재적 위협을 검색하고 적절한 대응을 할 수 있습니다.

또한 위협 검색에는 Azure Security Center를 통한 경고 기능도 통합되어 있으므로 모든 Azure 리소스의 보안 상태를 중앙에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure 보안 모범 사례 및 패턴](security-best-practices-and-patterns.md)에서 Azure를 사용하여 클라우드 솔루션을 디자인하고, 배포하고, 관리할 때 사용할 수 있는 더 많은 보안 모범 사례를 참조하세요.

Azure 보안 및 관련 Microsoft 서비스에 대한 보다 일반적인 정보를 제공하는 다음 리소스도 확인할 수 있습니다.
* [Azure 보안 팀 블로그](https://blogs.msdn.microsoft.com/azuresecurity/) – Azure Security 관련 최신 정보를 확인할 수 있습니다.
* [Microsoft 보안 응답 센터](https://technet.microsoft.com/library/dn440717.aspx) - Azure와 관련된 문제를 비롯한 Microsoft 보안 취약점을 보고하거나 secure@microsoft.com으로 이메일을 보낼 수 있습니다.
