---
title: Azure 데이터베이스 보안 개요 | Microsoft Docs
description: 이 문서에서는 Azure 데이터베이스 보안 기능에 대한 개요를 제공합니다.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: 7e0e93c82279ec1a4fbecbbf27c7a1866286b2f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587655"
---
# <a name="azure-database-security-overview"></a>Azure 데이터베이스 보안 개요

보안은 데이터베이스 관리에서 가장 중요하며 항상 Azure SQL Database의 최우선 순위였습니다. Azure SQL Database는 방화벽 규칙과 연결 암호화를 통해 연결 보안을 지원합니다. 사용자 이름과 암호를 사용한 인증과, Azure Active Directory에서 관리하는 Azure AD(Azure Active Directory) Authentication을 지원합니다. 권한 부여는 역할 기반 액세스 제어를 사용합니다.

Azure SQL Database는 애플리케이션에 대한 변경 없이 미사용 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행하여 암호화를 지원합니다.

Microsoft는 엔터프라이즈 데이터를 암호화하는 추가적인 방법을 제공합니다.

-   셀 수준 암호화는 특정 열 또는 서로 다른 암호화 키를 가진 데이터의 셀을 암호화하는 데 사용할 수 있습니다.
-   하드웨어 보안 모듈 또는 암호화 키 계층의 중앙 관리가 필요한 경우 Azure VM(Virtual Machine)에서 SQL Server와 함께 Azure Key Vault를 사용하는 것을 고려해 보세요.
-   Always Encrypted(현재 미리 보기)는 애플리케이션에 대해 암호화를 투명하게 합니다. 이 기능을 사용하면 암호화 키를 SQL 데이터베이스와 공유하지 않은 상태로 클라이언트에서 클라이언트 애플리케이션 내의 민감한 데이터를 암호화할 수 있습니다.

Azure SQL Database 감사를 사용하면 기업에서 로그인 Azure Storage에 이벤트를 기록할 수 있습니다. 또한 드릴다운 보고서 및 분석을 용이하게 하려면 SQL Database 감사 기능을 Microsoft Power BI와 통합합니다.

Azure SQL 데이터베이스는 HIPAA, ISO 27001/27002, PCI DSS Level 1 등을 비롯한 대부분의 규제 또는 보안 요구 사항을 만족할 수 있게 강력히 보호될 수 있습니다. 보안 규정 준수 인증의 최신 목록은 [Microsoft Azure Trust Center 사이트](https://azure.microsoft.com/support/trust-center/services/)에서 제공합니다.

이 문서에서는 구조적, 테이블 형식 및 관계형 데이터에 대한 Microsoft Azure SQL 데이터베이스 보호의 기본 사항을 안내합니다. 특히 이 문서에서는 데이터 보호, 액세스 제어 및 사전 모니터링을 위한 리소스로 시작합니다.

## <a name="protection-of-data"></a>데이터 보호

SQL Database는 암호화를 제공하여 데이터를 보호하도록 도와줍니다.

- 이동 중인 데이터 - [TLS(전송 계층 보안)](https://support.microsoft.com/kb/3135244)를 통해
- 미사용 데이터 - [투명한 데이터 암호화](https://go.microsoft.com/fwlink/?LinkId=526242)를 통해
- 사용 중인 데이터 - [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)를 통해

데이터를 암호화하는 다른 방법으로 다음을 고려해 보세요.

-   [셀 수준 암호화](https://msdn.microsoft.com/library/ms179331.aspx) 는 특정 열 또는 서로 다른 암호화 키를 가진 데이터의 셀도 암호화합니다.
-   하드웨어 보안 모듈 또는 암호화 키 계층의 중앙 관리가 필요한 경우 [Azure VM에서 SQL Server와 함께 Azure Key Vault](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)를 사용하는 것을 고려해 보세요.

### <a name="encryption-in-motion"></a>진행 중인 암호화

모든 클라이언트/서버 애플리케이션의 공통적인 문제는 데이터가 공공 및 사설 네트워크를 통해 이동할 때 개인 정보 보호가 필요하다는 점입니다. 네트워크를 통해 이동하는 데이터가 암호화되지 않으면 무단 사용자에 의한 수집 및 도난 가능성이 있습니다. 데이터베이스 서비스를 처리할 때 데이터베이스 클라이언트와 서버 간에 데이터가 암호화되어 있는지 확인합니다. 또한 서로 또는 중간 계층 애플리케이션과 통신하는 데이터베이스 서버 간에 데이터가 암호화되어 있는지 확인합니다.

네트워크를 관리할 때의 한 가지 문제는 신뢰할 수 없는 네트워크를 통해 애플리케이션 간에 전송되는 데이터를 보호하는 것입니다. [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol)을 사용하여 서버 및 클라이언트를 인증한 다음 인증된 당사자 간의 메시지를 암호화하는 데 사용할 수 있습니다.

인증 프로세스에서 TLS/SSL 클라이언트는 TLS/SSL 서버로 메시지를 보냅니다. 서버는 자신을 인증하는 데 필요한 정보로 응답합니다. 클라이언트와 서버가 추가적인 세션 키 교환을 수행하고 인증 대화가 종료됩니다. 인증이 완료되면 인증 프로세스 중에 수립된 대칭 암호화 키를 통해 서버와 클라이언트 간에 SSL로 보호되는 통신을 시작할 수 있습니다.

Azure SQL Database에 대한 모든 연결은 데이터베이스로/로부터 데이터 "전송 중"에 항상 암호화(TLS/SSL)가 필요합니다. SQL Database는 TLS/SSL을 사용하여 서버 및 클라이언트를 인증한 다음 인증된 당사자 간의 메시지를 암호화하는 데 사용할 수 있습니다. 

애플리케이션의 연결 문자열에서 연결을 암호화하고 서버 인증서를 신뢰하지 않도록 매개 변수를 지정해야 합니다. Azure Portal 외부에서 연결 문자열을 복사할 때 이 작업을 수행합니다. 그렇지 않으면 연결에서 서버의 ID를 확인하지 않으므로 "메시지 가로채기(man-in-the-middle)" 공격에 노출됩니다. 예를 들어, ADO.NET 드라이버의 경우 이러한 연결 문자열 매개 변수는 `Encrypt=True` 및 `TrustServerCertificate=False`입니다.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화

데이터베이스를 보호하기 위해 여러 가지 예방 조치를 취할 수 있습니다. 예를 들어, 보안 시스템을 설계하고, 중요한 자산을 암호화하고, 데이터베이스 서버 방화벽을 작성합니다. 그러나 물리적 미디어(예: 드라이브 또는 백업 테이프)가 도난되는 시나리오에서는 악의적인 사용자가 데이터베이스를 단순히 복원하거나 연결하여 데이터를 찾아 볼 수 있습니다.

한 가지 해결 방법으로 데이터베이스의 중요한 데이터를 암호화하고 인증서로 데이터를 암호화하는 데 사용되는 키를 보호합니다. 이렇게 하면 키가 없는 사람이 데이터를 사용할 수 없게 되지만 이러한 종류의 보호는 계획해야 합니다.

이 문제를 해결하기 위해 SQL Server 및 SQL Database는 [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017)를 지원합니다. 투명한 데이터 암호화는 SQL Server 및 SQL Database 데이터 파일을 암호화합니다. 이를 미사용 데이터 암호화라고 합니다.

투명한 데이터 암호화는 악의적인 활동의 위협으로부터 보호합니다. 애플리케이션에 대한 변경 없이 미사용 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행합니다.  

투명한 데이터 암호화는 데이터베이스 암호화 키라는 대칭 키를 사용하여 전체 데이터베이스의 저장소를 암호화합니다. SQL Database에서 데이터베이스 암호화 키는 기본 제공 서버 인증서에 의해 보호됩니다. 기본 제공 서버 인증서는 각 SQL Database 서버에 대해 고유합니다.

데이터베이스가 Geo-DR 관계에 있는 경우 각 서버에서 서로 다른 키로 보호됩니다. 두 개의 데이터베이스가 동일한 서버에 연결되어 있으면 동일한 기본 제공 인증서를 공유합니다. Microsoft는 적어도 90일마다 이러한 인증서를 자동으로 회전합니다. 

자세한 내용은 [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde)를 참조하세요.

### <a name="encryption-in-use-client"></a>암호화 사용(클라이언트)

대부분의 데이터 침해는 신용 카드 번호나 신원 식별 가능 정보 등의 중요 데이터 도용이 연루되어 있습니다. 데이터베이스는 중요 정보를 담고 있을 수 있습니다. 여기에 고객의 개인 데이터(예: 주민 등록 번호), 기밀 경쟁 정보, 지적 재산 등이 포함될 수 있습니다. 특히 고객 데이터의 손실이나 도난은 브랜드 평판 손상, 경쟁력 상실, 소송까지 초래하는 심각한 벌금을 초래할 수 있습니다.

![자물쇠와 열쇠로 표현된 Always Encrypted 기능](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)는 Azure SQL Database 또는 SQL Server 데이터베이스에 저장된 중요한 데이터를 보호하기 위한 기능입니다. Always Encrypted를 사용하면 클라이언트에서 클라이언트 애플리케이션 내의 중요한 데이터를 암호화하고 데이터베이스 엔진(SQL Database 또는 SQL Server)에 암호화 키를 공개하지 않을 수 있습니다.

Always Encrypted는 데이터를 소유하고 볼 수 있는 사용자와 데이터를 관리하지만 액세스 권한이 없는 사용자를 구별합니다. 이는 온-프레미스 데이터베이스 관리자, 클라우드 데이터베이스 운영자 또는 기타 권한이 높지만 인증되지 않은 사용자가 암호화된 데이터에 액세스하지 못하게 합니다.

또한 Always Encrypted는 애플리케이션에 대해 암호화를 투명하게 합니다. 클라이언트 애플리케이션에서 중요 데이터를 자동으로 암호화 및 암호 해독할 수 있게, Always Encrypted를 구현하는 드라이버는 클라이언트 컴퓨터에 설치됩니다. 드라이버는 데이터베이스 엔진에 데이터를 전달하기 전에 중요한 열의 데이터를 암호화합니다. 드라이버는 애플리케이션에 대한 의미 체계가 유지되도록 쿼리를 자동으로 다시 작성합니다. 마찬가지로, 드라이버는 암호화된 데이터베이스 열에 저장되고 쿼리 결과에 포함된 데이터를 투명하게 암호 해독합니다.

## <a name="access-control"></a>액세스 제어

보안을 제공하기 위해 SQL Database는 다음을 사용하여 액세스를 제어합니다.

- IP 주소를 통해 연결을 제한하는 방화벽 규칙
- 사용자에게 자신의 ID를 증명하도록 요구하는 인증 메커니즘
- 특정 작업 및 데이터로 사용자를 제한하는 권한 부여 메커니즘

### <a name="database-access"></a>데이터베이스 액세스

데이터 보호는 데이터 액세스 제어에서 출발합니다. 데이터를 호스트하는 데이터 센터는 물리적 액세스를 관리합니다. 네트워크 계층에서 보안을 관리하도록 방화벽을 구성할 수 있습니다. 또한 인증에 대한 로그인을 구성하고 서버 및 데이터베이스 역할의 권한을 정의하여 액세스를 제어할 수도 있습니다.

#### <a name="firewall-and-firewall-rules"></a>방화벽 및 방화벽 규칙

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/)는 Azure 및 기타 인터넷 기반 애플리케이션의 관계형 데이터베이스 서비스를 제공합니다. 데이터를 보호하기 위해 방화벽은 권한이 있는 컴퓨터를 지정할 때까지 데이터베이스 서버에 대한 모든 액세스를 금지합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다. 자세한 내용은 [Azure SQL Database 방화벽 규칙 개요](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)를 참조하세요.

Azure SQL Database 서비스는 TCP 포트 1433을 통해서만 사용할 수 있습니다. 사용자의 컴퓨터에서 SQL Database에 액세스하려면 클라이언트 컴퓨터 방화벽이 TCP 포트 1433을 통해 나가는 TCP 통신을 허용해야 합니다. 다른 애플리케이션에 인바운드 연결이 필요하지 않은 경우 TCP 포트 1433에서 인바운드 연결을 차단합니다.

#### <a name="authentication"></a>Authentication

인증은 데이터베이스에 연결할 때 사용자의 ID를 증명하는 방법을 가리킵니다. SQL Database는 두 가지 인증 유형을 지원합니다.

-   **SQL Server 인증**: 논리적 SQL 인스턴스가 만들어질 때, SQL Database 구독자 계정이라는 단일 로그인 계정이 만들어집니다. 이 계정은 [SQL Server 인증](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)(사용자 이름 및 암호)을 사용하여 연결됩니다. 이 계정은 논리적 서버 인스턴스 및 해당 인스턴스에 연결된 모든 사용자 데이터베이스에서 관리자입니다. 구독자 계정의 사용 권한은 제한할 수 없습니다. 이러한 계정 중 하나만 존재할 수 있습니다.
-   **Azure Active Directory 인증**: [Azure AD 인증](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)은 Azure AD의 ID를 사용하여 Azure SQL Database 및 Azure SQL Data Warehouse에 연결하는 메커니즘입니다. 이를 사용하여 데이터베이스 사용자의 ID를 중앙에서 관리할 수 있습니다.

![SQL Database를 사용하는 Azure AD 인증](./media/azure-databse-security-overview/azure-database-fig2.png)

 Azure AD 인증에는 다음과 같은 장점이 있습니다.
  - SQL Server 인증에 대한 대안을 제공합니다.
  - 데이터베이스 서버를 통한 사용자 ID의 확산을 중지하는 데도 도움이 되며 한 위치에서 암호 회전을 허용합니다.
  - 외부(Azure AD) 그룹을 사용하여 데이터베이스 사용 권한을 관리할 수 있습니다.
  - Windows 통합 인증 또는 Azure AD에서 지원하는 기타 인증을 사용하여 암호 저장을 제거할 수 있습니다.

#### <a name="authorization"></a>권한 부여

[권한 부여](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)는 사용자가 Azure SQL Data 데이터베이스 내에서 수행할 수 있는 작업을 참조합니다. 사용자 계정의 데이터베이스 [역할 멤버 자격](https://msdn.microsoft.com/library/ms189121) 및 [개체 수준 사용 권한](https://msdn.microsoft.com/library/ms191291.aspx)에 따라 제어됩니다. 권한 부여는 보안 주체가 액세스할 수 있는 보호 가능한 리소스와, 해당 리소스에 대해 허용되는 작업을 결정하는 프로세스입니다.

### <a name="application-access"></a>애플리케이션 액세스

#### <a name="dynamic-data-masking"></a>동적 데이터 마스킹

콜 센터의 서비스 담당자는 주민 등록 번호 또는 신용 카드 번호의 여러 숫자로 호출자를 식별할 수 있습니다. 하지만 이러한 데이터 항목이 서비스 담당자에게 완전히 노출되어서는 안 됩니다.

모든 쿼리의 결과 집합에서 주민 등록 번호 또는 신용 카드 번호의 마지막 4자리를 제외한 모든 숫자를 마스킹하도록 마스킹 규칙을 정의할 수 있습니다.

![SQL Database와 비즈니스 응용 프로그램 간에 전송되는 숫자에 대한 마스킹](./media/azure-databse-security-overview/azure-database-fig3.png)

또 다른 예로, 개인 식별 정보를 보호하는 적절한 데이터 마스크를 정의할 수 있습니다. 그러면 개발자는 준수 규정을 위반하지 않고 문제 해결을 위해 프로덕션 환경을 쿼리할 수 있습니다.

[SQL Database 동적 데이터 마스킹](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)에서는 권한이 없는 사용자에 대해 중요한 데이터를 마스킹해 표시함으로써 데이터 노출을 제한합니다. Azure SQL Database의 V12 버전에서는 동적 데이터 마스킹을 지원합니다.

[동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)을 수행하면 애플리케이션 계층에 대한 영향을 최소화하면서 표시할 중요한 데이터의 양을 지정할 수 있도록 하여 중요한 데이터에 대한 무단 액세스를 방지할 수 있습니다. 동적 데이터 마스킹은 지정된 데이터베이스 필드에 대한 쿼리의 결과 집합에서 중요한 데이터를 숨기는 정책 기반 보안 기능입니다. 이때 데이터베이스의 데이터 자체는 변경되지 않습니다.

> [!Note]
> Azure 데이터베이스 관리자, 서버 관리자 또는 보안 관리자 역할이 동적 데이터 마스킹을 구성할 수 있습니다.

#### <a name="row-level-security"></a>행 수준 보안

다중 테넌트 데이터베이스에 대한 또 다른 공통 보안 요구 사항은 [행 수준 보안](https://msdn.microsoft.com/library/dn765131.aspx)입니다. 이 기능을 사용하면 쿼리를 실행하는 사용자의 특성을 기반으로 하여 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. 특성의 예로는 그룹 멤버 자격 및 실행 컨텍스트가 있습니다.

![클라이언트 응용 프로그램을 통해 사용자가 테이블의 행에 액세스하도록 허용하는 행 수준 보안](./media/azure-databse-security-overview/azure-database-fig4.png)

액세스 제한 논리는 다른 애플리케이션 계층의 데이터가 아닌 데이터베이스 계층에 있습니다. 데이터베이스 시스템은 모든 계층에서 데이터 액세스를 시도할 때마다 액세스 제한을 적용합니다. 이렇게 하면 보안 시스템의 노출 영역을 줄임으로써 보안 시스템을 보다 안정적이고 강력하게 만들 수 있습니다.

행 수준 보안에서는 조건자 기반 액세스 제어를 도입합니다. 이 제어는 메타데이터 또는 관리자가 적절하다고 판단한 다른 기준을 반영할 수 있는 유연하고 중앙 집중적인 평가가 특징입니다. 조건자는 사용자 특성에 따라 해당 사용자가 데이터에 대해 적합한 액세스 권한이 있는지 여부를 판단하기 위한 기준으로 사용됩니다. 조건자 기준 액세스 제어를 사용하여 레이블 기반 액세스 제어를 구현할 수 있습니다.

## <a name="proactive-monitoring"></a>사전 모니터링

SQL Database는 *감사* 및 *위협 검색* 기능을 제공하여 데이터를 보호합니다.

### <a name="auditing"></a>감사

[SQL Database 감사](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)는 데이터베이스 내에서 발생하는 변경 사항과 이벤트에 대한 정보를 파악하는 기능을 강화합니다. 업데이트 및 데이터에 대한 쿼리를 예로 들 수 있습니다.

SQL Database 감사는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 이벤트를 기록합니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다. 감사를 사용하면 규정 표준을 보다 쉽게 준수할 수 있지만 규정을 완전히 준수한다고 보장할 수는 없습니다.

SQL Database 감사를 사용하여 다음을 수행할 수 있습니다.

- **유지** 합니다. 감사할 데이터베이스 동작의 범주를 정의할 수 있습니다.
- **보고** 합니다. 미리 구성된 보고서 및 대시보드를 사용하여 활동 및 이벤트 보고를 빠르게 시작할 수 있습니다.
- **분석** 합니다. 의심스러운 이벤트, 특별한 활동 및 추세를 찾을 수 있습니다.

두 가지 감사 방법이 있습니다.

-   **Blob 감사**: 로그는 Azure Blob Storage에 쓰기됩니다. 이는 새로운 감사 방법으로, 뛰어난 성능을 제공하고, 보다 세밀한 개체 수준 감사를 지원하며, 보다 비용 효율적인 방법입니다.
-   **테이블 감사**: Azure Table Storage에 로그가 기록됩니다.

### <a name="threat-detection"></a>위협 감지

[Azure SQL Database에 대한 Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-advanced-threat-protection)은 보안 위협의 가능성이 있는 의심스러운 활동을 검색합니다. 위협 검색을 통해 SQL 삽입 등, 데이터베이스에 의심스러운 이벤트가 발생하면 그에 대처할 수 있습니다. 경고를 제공하며 Azure SQL Database Auditing이 의심스러운 이벤트를 탐색할 수 있게 지원합니다.

![외부 공격자와 악의적인 내부자가 있는 SQL Database 및 웹 앱에 대한 위협 검색](./media/azure-databse-security-overview/azure-database-fig5.jpg)

SQL ATP(Advanced Threat Protection)은 데이터 검색 및 분류, 취약성 평가 및 위협 검색을 포함하여 SQL 고급 보안 기능을 제공합니다. 

- [데이터 검색 및 분류](../sql-database/sql-database-data-discovery-and-classification.md)
- [취약성 평가](../sql-database/sql-vulnerability-assessment.md)  
- [위협 탐지](../sql-database/sql-database-threat-detection.md)

[Azure Database for PostgreSQL Advanced Threat Protection](../postgresql/concepts-data-access-and-security-threat-protection.md)은 비정상적인 활동에 대한 보안 경고를 제공하여 잠재적인 위협이 발생하면 사용자가 이를 검색하고 대응할 수 있도록 하는 새로운 차원의 보안을 제공합니다. 사용자는 의심스러운 데이터베이스 활동, 잠재적 취약성, 그리고 비정상적인 데이터베이스 액세스 및 쿼리 패턴에 대한 경고를 받습니다. Azure Database for PostgreSQL용 Advanced Threat Protection의 경고는 Azure Security Center에 통합되어 있습니다. 경고의 유형은 다음과 같습니다.

- 비정상적인 위치에서 액세스
- 비정상적인 Azure 데이터 센터에서 액세스 
- 알 수 없는 보안 주체에서 액세스 
- 잠재적으로 위험한 애플리케이션에서 액세스 
- Azure Database for PostgreSQL 자격 증명에 대한 무차별 암호 대입 공격 

[Azure Database for MySQL Advanced Threat Protection](../mysql/concepts-data-access-and-security-threat-protection.md)은 PostgreSQL Advanced Protection과 유사한 보호를 제공합니다.  

## <a name="centralized-security-management"></a>중앙 집중식 보안 관리 

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/)를 통해 위협을 예방하고 감지하며 대응할 수 있습니다. Azure 구독에서 통합된 보안 모니터링 및 정책 관리를 제공합니다. 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되고 보안 솔루션의 광범위한 환경에서 작동합니다.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database)를 사용하면 모든 서버 및 데이터베이스의 보안에 가시성을 제공하여 SQL Database에서 데이터를 보호할 수 있습니다. 보안 센터를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- SQL Database 암호화 및 감사를 위한 정책을 정의합니다.
- 모든 구독 간에 SQL Database 리소스의 보안을 모니터링합니다.
- 보안 문제를 신속하게 파악하고 해결합니다.
- [Azure SQL Database 위협 감지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)에 대한 경고를 통합합니다.

보안 센터는 역할 기반 액세스를 지원합니다.

## <a name="sql-information-protection"></a>SQL Information Protection

[SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md)은 잠재적으로 중요한 데이터를 자동으로 검색하고 분류하며, 분류 속성으로 중요한 데이터의 태그를 영구적으로 지정하는 레이블 지정 메커니즘을 제공하고, 데이터베이스 분류 상태를 보여주는 상세 대시보드를 제공합니다.  

또한 중요한 데이터를 추출하는 쿼리를 명시적으로 감사할 수 있도록 SQL 쿼리의 결과 집합 민감도를 계산하고 데이터를 보호할 수 있습니다. SQL Information Protection에 대한 자세한 내용은 Azure SQL Database 데이터 검색 및 분류를 참조하세요.

Azure Security Center에서 [SQL Information Protection 정책](../security-center/security-center-info-protection-policy.md)을 구성할 수 있습니다.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace는 신생 기업 및 ISV(독자적 소프트웨어 공급업체)가 전 세계 Azure 고객에게 자사의 솔루션을 제공할 수 있도록 하는 온라인 애플리케이션 및 서비스 마켓플레이스입니다.
Azure Marketplace는 고객과 파트너에게 더 좋은 서비스를 제공하기 위해 Microsoft Azure 파트너 생태계를 통합된 플랫폼으로 결합합니다. [검색을 실행](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1)하여 Azure Marketplace에서 제공되는 데이터베이스 보안 제품을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL 데이터베이스 보안](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center 및 Azure SQL Database 서비스](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [SQL Database 위협 검색](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [SQL Database 성능 향상](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
