---
title: Azure SQL Database에 대 한 보안 모범 사례 플레이 북 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database의 보안 모범 사례에 대 한 일반적인 지침을 제공 합니다.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 01/22/2020
ms.reviewer: ''
ms.openlocfilehash: 095d435b9a595c420821da0813fdfc0893d70d89
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845875"
---
# <a name="azure-sql-database-security-best-practices-playbook"></a>Azure SQL Database 보안 모범 사례 플레이 북

## <a name="overview"></a>개요

이 문서에서는 Azure SQL Database를 사용 하 여 신규 또는 기존 응용 프로그램에 대 한 일반적인 보안 요구 사항을 해결 하는 방법을 안내 합니다. 이는 높은 수준의 보안 영역을 기준으로 구성 됩니다. 특정 위협에 대 한 해결 방법에 대 한 자세한 내용은 [일반적인 보안 위협 및 잠재적 완화](#common-security-threats-and-potential-mitigations) 섹션을 참조 하세요. 응용 프로그램을 온-프레미스에서 Azure로 마이그레이션할 때 제시 된 권장 사항 중 일부를 사용할 수 있지만 마이그레이션 시나리오는이 문서에 중점을 두지 않습니다.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>이 가이드에서 설명 하는 Azure SQL Database 배포 제품

- [SQL 데이터베이스](https://docs.microsoft.com/azure/sql-database/sql-database-single-index): [Azure SQL Database 서버의](sql-database-servers.md) [단일 데이터베이스](sql-database-single-database.md) 및 [탄력적 풀](sql-database-elastic-pool.md)
- [관리 되는 인스턴스](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>이 가이드에서 다루지 않는 SQL 배포 제품

- Azure SQL Data Warehouse
- Azure SQL Vm (IaaS)
- SQL Server 온-프레미스

### <a name="audience"></a>대상

이 가이드의 대상 사용자는 Azure SQL Database를 보호 하는 방법에 대 한 질문을 하는 고객입니다. 이 모범 사례 문서에 관심이 있는 역할에는 다음이 포함 되지만이에 국한 되지 않습니다.

- 보안 설계자
- 보안 관리자
- 규정 준수 책임자
- 개인 정보 취급 방침
- 보안 엔지니어

### <a id="using"></a>이 가이드 사용

이 문서는 기존 [Azure SQL Database 보안](sql-database-security-overview.md) 설명서와 함께 제공 됩니다.

별도로 언급 하지 않는 한 각 섹션에 나열 된 모든 모범 사례를 따라 각 목표 또는 요구 사항을 충족 하는 것이 좋습니다. 특정 보안 규정 준수 표준 또는 모범 사례를 충족 하기 위해 중요 한 규정 준수 제어는 요구 사항 또는 목표 섹션 (해당 하는 경우)에 나열 됩니다. 다음은이 문서에서 참조 하는 보안 표준 및 규정입니다.

- [FedRAMP](https://www.fedramp.gov/documents/): ac-04, ac-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): CM-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Access Control, 암호화
- [MICROSOFT OSA (운영 보안 보증) 관행](https://www.microsoft.com/en-us/securityengineering/osa/practices): 실습 #1-6 및 #9
- [NIST 특별 게시 800-53 보안 제어](https://nvd.nist.gov/800-53): ac-5, ac-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>피드백

여기에 나열 된 권장 사항 및 모범 사례를 계속 업데이트할 예정입니다. 이 문서 맨 아래에 있는 **피드백** 링크를 사용 하 여이 문서에 대 한 입력 또는 수정 사항을 제공 합니다.

## <a name="authentication"></a>인증

인증은 사용자의 신원을 증명하는 과정입니다. Azure SQL Database는 두 가지 인증 유형을 지원합니다.

- SQL 인증
- Azure Active Directory 인증

### <a name="central-management-for-identities"></a>Id에 대 한 중앙 관리

중앙 id 관리는 다음과 같은 이점을 제공 합니다.

- Azure SQL Database 서버와 데이터베이스 간에 로그인을 복제 하지 않고 그룹 계정을 관리 하 고 사용자 권한을 제어 합니다.
- 간단 하 고 유연한 권한 관리.
- 대규모로 응용 프로그램 관리.

**구현 방법**:

- 중앙 집중식 id 관리를 위해 Azure AD (Azure Active Directory) 인증을 사용 합니다.

**모범 사례**:

- Azure AD 테넌트를 만들고 [사용자를 만들어 사용자](../active-directory/fundamentals/add-users-azure-active-directory.md) 에 게 앱, 서비스 및 자동화 도구를 나타내는 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md) 를 만듭니다. 서비스 주체는 Windows 및 Linux의 서비스 계정에 해당 합니다. 

- 그룹 할당을 통해 Azure AD 사용자에 게 리소스에 대 한 액세스 권한 할당: Azure AD 그룹을 만들고, 그룹에 액세스 권한을 부여 하 고, 그룹에 개별 멤버를 추가 합니다. 데이터베이스에서 Azure AD 그룹을 매핑하는 포함 된 데이터베이스 사용자를 만듭니다. 
  - Sql을 사용한 인증에는 SQL을 사용 하 [여 Azure Active Directory 인증 구성 및 관리](sql-database-aad-authentication-configure.md) 및 [sql 인증을 위해 Azure AD 사용](sql-database-aad-authentication.md)문서를 참조 하세요.
  > [!NOTE]
  > 관리 되는 인스턴스에서 master 데이터베이스의 Azure AD 보안 주체에 매핑되는 로그인을 만들 수도 있습니다. [CREATE LOGIN (transact-sql)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)을 참조 하세요.

- Azure AD 그룹을 사용 하 여 권한 관리 및 그룹 소유자를 간소화 하 고 리소스 소유자가 그룹에서 구성원을 추가/제거할 수 있습니다. 

- SQL DB 서버에 대 한 Azure AD 관리자에 대 한 별도의 그룹을 만듭니다.

  - [Azure SQL Database 서버에 대 한 Azure Active Directory 관리자 프로 비전](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)문서를 참조 하세요.

- Azure AD 감사 활동 보고서를 사용 하 여 Azure AD 그룹 멤버 자격 변경 내용을 모니터링 합니다. 

- 관리 되는 인스턴스의 경우 Azure AD 관리자를 만들려면 별도의 단계가 필요 합니다. 
  - [관리 되는 인스턴스에 대 한 Azure Active Directory 관리자 프로 비전](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)문서를 참조 하세요. 

> [!NOTE]
> - Azure AD 인증은 azure SQL 감사 로그에 기록 되지만 Azure AD 로그인 로그에는 기록 되지 않습니다.
> - Azure에 부여 된 RBAC 권한은 Azure SQL DB 권한에 적용 되지 않습니다. 이러한 권한은 기존 SQL 사용 권한을 사용 하 여 SQL DB에서 수동으로 만들거나 매핑해야 합니다.
> - 클라이언트 쪽 Azure AD 인증에는 VNet에 대 한 인터넷 또는 UDR (사용자 정의 경로)을 통해 액세스 해야 합니다.
> - Azure AD 액세스 토큰은 클라이언트 쪽에서 캐시 되 고 수명은 토큰 구성에 따라 달라 집니다. [Azure Active Directory에서 구성 가능한 토큰 수명](../active-directory/develop/active-directory-configurable-token-lifetimes.md) 문서를 참조 하세요.
> - Azure AD 인증 문제를 해결 하는 방법에 대 한 지침은 다음 블로그를 참조 하세요 <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>MFA(Multi-Factor Authentication)

> 설명: OSA 사례 #2, ISO Access Control (AC)

MFA (Azure Multi-Factor Authentication)는 둘 이상의 인증 형태를 요구 하 여 추가 보안을 제공 합니다.

**구현 방법**:

- 조건부 액세스를 사용 하 고 대화형 인증을 사용 하 여 Azure AD에서 [MFA를 사용 하도록 설정](../active-directory/authentication/concept-mfa-howitworks.md) 합니다. 

- 대안은 전체 Azure AD 또는 AD 도메인에 대해 MFA를 사용 하도록 설정 하는 것입니다.

**모범 사례**:

- Azure AD에서 조건부 액세스를 활성화 합니다 (프리미엄 구독 필요). 
  - [AZURE AD의 조건부 액세스](../active-directory/conditional-access/overview.md)문서를 참조 하세요.  

- Azure ad 그룹을 만들고 Azure AD 조건부 액세스를 사용 하 여 선택한 그룹에 대해 MFA 정책을 사용 하도록 설정 합니다. 
  - [조건부 액세스 배포 계획](../active-directory/conditional-access/plan-conditional-access.md)문서를 참조 하세요. 

- MFA는 전체 Azure AD 또는 Azure AD와 페더레이션된 전체 Active Directory에 대해 사용 하도록 설정할 수 있습니다. 

- 암호가 대화형으로 요청 된 후 MFA 인증을 사용 하는 SQL DB에 대해 Azure AD 대화형 인증 모드를 사용 합니다.      
  - SSMS에서 유니버설 인증을 사용 합니다. [Azure SQL Database 및 Azure SQL Data Warehouse에서 MULTI-FACTOR AAD 인증 사용 (MFA에 대 한 SSMS 지원)](sql-database-ssms-mfa-authentication.md)문서를 참조 하세요.
  - SQL Server Data Tools에서 지원 되는 대화형 인증 (SSDT)을 사용 합니다. [SQL Server Data Tools에서 Azure Active Directory 지원 (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current)문서를 참조 하세요.
  - MFA를 지 원하는 다른 SQL 도구를 사용 합니다. 
    - 데이터베이스 내보내기/추출/배포에 대 한 SSMS 마법사 지원  
    - [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage): 옵션 '/ua ' 
    - [Sqlcmd 유틸리티](https://docs.microsoft.com/sql/tools/sqlcmd-utility): 옵션-G (대화형)
    - [Bcp 유틸리티](https://docs.microsoft.com/sql/tools/bcp-utility): 옵션-G (대화형) 

- MFA를 지 원하는 대화형 인증을 사용 하 여 Azure SQL Database에 연결 하는 응용 프로그램을 구현 합니다. 
  - [Azure Multi-Factor Authentication를 사용 하 여 Azure SQL Database에 연결](active-directory-interactive-connect-azure-sql-db.md)문서를 참조 하세요. 
  > [!NOTE]
  > 이 인증 모드에는 사용자 기반 id가 필요 합니다. 개별 Azure AD 사용자 인증을 우회 하는 신뢰할 수 있는 id 모델을 사용 하는 경우 (예: Azure 리소스에 관리 되는 id 사용) MFA는 적용 되지 않습니다.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>사용자에 대 한 암호 기반 인증 사용 최소화

> 설명: OSA 사례 #4, ISO Access Control (AC)

암호 기반 인증 방법은 더 약한 형태의 인증입니다. 자격 증명이 손상 되거나 실수로 제공 될 수 있습니다.

**구현 방법**:

- 암호 사용을 제거 하는 Azure AD 통합 인증을 사용 합니다.

**모범 사례**:

- Windows 자격 증명을 사용 하 여 Single Sign-On 인증을 사용 합니다. 온-프레미스 AD 도메인을 Azure AD와 페더레이션 하 고 Windows 통합 인증 (Azure AD에서 도메인에 가입 된 컴퓨터의 경우)을 사용 합니다.
  - [AZURE AD 통합 인증에 대 한 SSMS 지원](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication)문서를 참조 하세요.

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>응용 프로그램에 대 한 암호 기반 인증 사용 최소화 

> 설명: OSA 사례 #4, ISO Access Control (AC)

**구현 방법**:

- Azure 관리 Id를 사용 하도록 설정 합니다. 또한 통합 또는 인증서 기반 인증을 사용할 수 있습니다. 

**모범 사례**:

- [Azure 리소스에 관리 되는 id를](../active-directory/managed-identities-azure-resources/overview.md)사용 합니다.
  - [시스템 할당 관리 id](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [사용자 할당 관리 id](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [관리 id를 사용 하 여 app service에서 Azure SQL Database 사용 (코드 변경 없음)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- 응용 프로그램에 대 한 인증서 기반 인증을 사용 합니다. 
  - 이 [코드 샘플](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token)을 참조 하세요. 

- 통합 페더레이션 도메인 및 도메인에 가입 된 컴퓨터에 Azure AD 인증을 사용 합니다 (위 섹션 참조).
  - [통합 인증에 대 한 샘플 응용 프로그램](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)을 참조 하세요.

### <a name="protect-passwords-and-secrets"></a>암호 및 비밀 보호

암호가 갖추고 않는 경우 보안이 유지 되는지 확인 합니다.

**구현 방법**:

- Azure Key Vault를 사용 하 여 암호 및 암호를 저장 합니다. 해당 하는 경우, Azure AD 사용자와 Azure SQL Database MFA를 사용 합니다.

**모범 사례**:

- 암호나 암호를 사용할 수 없는 경우 Azure Key Vault에 사용자 암호 및 응용 프로그램 암호를 저장 하 고 Key Vault 액세스 정책을 통해 액세스를 관리 합니다. 

- 다양 한 앱 개발 프레임 워크는 앱에서 비밀을 보호 하기 위한 프레임 워크 특정 메커니즘을 제공할 수도 있습니다. 예: [ASP.NET core 앱](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>레거시 응용 프로그램에 SQL 인증 사용 

SQL 인증은 사용자 이름과 암호를 사용 하 여 Azure SQL Database에 연결할 때 사용자의 인증을 나타냅니다. 각 SQL Database 서버 또는 관리 되는 인스턴스 및 각 데이터베이스에서 만든 사용자에 게 로그인을 만들어야 합니다.

**구현 방법**:

- SQL 인증을 사용 합니다.

**모범 사례**:

- 서버 관리자로 서 로그인 및 사용자를 만듭니다. 포함 된 데이터베이스 사용자에 게 암호를 사용 하지 않는 한 모든 암호는 master 데이터베이스에 저장 됩니다.
  - [SQL Database 및 SQL Data Warehouse에 대 한 데이터베이스 액세스 제어 및 권한 부여](sql-database-manage-logins.md)문서를 참조 하세요.

- 암호 관리 모범 사례를 따릅니다.
  - 라틴어 상한 및 소문자, 숫자 (0-9) 및 영숫자가 아닌 문자 (예: $,!, # 또는%)로 구성 된 복잡 한 암호를 제공 합니다.
  - 임의로 선택 된 문자 대신 긴 암호를 사용 합니다.
  - 최소 90 일 마다 수동 암호 변경을 적용 합니다.

## <a name="access-management"></a>액세스 관리

액세스 관리는 Azure SQL Database에 대 한 권한 있는 사용자의 액세스 및 권한을 제어 하 고 관리 하는 프로세스입니다.

### <a name="implement-principle-of-least-privilege"></a>최소 권한의 원칙 구현

> 설명: FedRamp controls AC-06, NIST: AC-6, OSA 연습 #3

최소 권한의 원칙에 따라 사용자에 게 작업을 완료 하는 데 필요한 것 보다 많은 권한이 부여 되는 것은 아닙니다. 자세한 내용은 [충분 한 관리](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview)문서를 참조 하세요.

**구현 방법**:

필요한 작업을 완료 하는 데 필요한 [권한만](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) 할당 합니다.

- SQL 데이터 평면: 
    - 세분화 된 권한 및 사용자 정의 데이터베이스 역할 (또는 MI의 서버 역할)을 사용 합니다. 
        1. 필요한 역할 만들기
            - [CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [서버 역할 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. 필수 사용자 만들기
            - [CREATE USER](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. 사용자를 역할에 멤버로 추가 
            - [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. 그런 다음 역할에 사용 권한을 할당 합니다. 
            - [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - 불필요 한 역할에 사용자를 할당 하지 않아야 합니다.

- Azure Resource Manager:
  - 기본 제공 역할 (사용 가능한 경우) 또는 사용자 지정 RBAC 역할을 사용 하 고 필요한 사용 권한을 할당 합니다.
    - [Azure의 기본 제공 역할](../role-based-access-control/built-in-roles.md) 
    - [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md)

**모범 사례**:

다음 모범 사례는 선택 사항 이지만 보안 전략의 관리 효율성 및 지원 가능성을 향상 시킬 수 있습니다. 

- 가능 하면 사용 가능한 최소 권한 집합부터 시작 하 여 사용 권한을 추가 하는 것이 좋습니다 .이는 반대 방식과 반대 되는 방법입니다. 

- 개별 사용자에 게 사용 권한을 할당 하지 마십시오. 대신 역할 (데이터베이스 또는 서버 역할)을 일관 되 게 사용 합니다. 역할은 보고 및 문제 해결 권한에 매우 유용 합니다. Azure RBAC는 역할을 통한 권한 할당만 지원 합니다. 

- 역할의 권한이 사용자에 대해 필요한 권한과 정확히 일치 하는 경우 기본 제공 역할을 사용 합니다. 여러 역할에 사용자를 할당할 수 있습니다. 

- 기본 제공 역할이 너무 많거나 사용 권한을 부여 하지 않는 경우 사용자 지정 역할을 만들고 사용 합니다. 실제로 사용 되는 일반적인 역할은 다음과 같습니다. 
  - 보안 배포 
  - 관리자 
  - 개발자 
  - 지원 담당자 
  - 감사자 
  - 자동화 된 프로세스 
  - 최종 사용자 

- SQL Server 데이터베이스 엔진의 권한은 다음 범위에 적용 될 수 있습니다. 범위가 작을수록 부여 된 권한의 영향이 작아집니다. 
  - Azure SQL Database server (master 데이터베이스의 특수 역할) 
  - 데이터베이스 
  - 스키마 (참고 항목: [스키마 디자인 for SQL Server: 보안을 고려 하 여 스키마 디자인에 대 한 권장 사항](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - 개체 (테이블, 뷰, 프로시저 등) 
  > [!NOTE]
  > 이 수준에서는 전체 구현에 불필요 한 복잡성이 추가 되기 때문에 개체 수준에 대 한 사용 권한을 적용 하지 않는 것이 좋습니다. 개체 수준 사용 권한을 사용 하기로 결정 한 경우이를 명확 하 게 문서화 해야 합니다. 열 수준 사용 권한에도 동일 하 게 적용 됩니다. 즉, 동일한 이유로 인해 것이 좋습니다 훨씬 줄어듭니다. [거부](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) 에 대 한 표준 규칙은 열에 적용 되지 않습니다.

- [VA (취약성 평가)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 를 사용 하 여 정기적으로 검사를 수행 하 여 너무 많은 사용 권한을 테스트 합니다.

### <a name="implement-separation-of-duties"></a>의무 분리 구현

> 설명: FedRamp: AC-04, NIST: AC-5, ISO: 6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

의무의 분리 (분리 라고도 함)는 중요 한 작업을 여러 사용자에 게 할당 된 여러 작업으로 분할 하기 위한 요구 사항을 설명 합니다. 의무를 분리 하면 데이터 위반을 방지할 수 있습니다.

**구현 방법**:

- 필요한 의무의 분리 수준을 식별 합니다. 예시: 
  - 개발/테스트 및 프로덕션 환경 간 
  - 보안에 중요 한 작업은 DBA (데이터베이스 관리자) 관리 수준 작업과 개발자 작업을 비교 합니다. 
    - 예: 감사자, RLS (역할 수준 보안)에 대 한 보안 정책 만들기, DDL 권한으로 개체 SQL Database 구현

- 시스템에 액세스 하는 사용자 (및 자동화 된 프로세스)의 포괄적인 계층 구조를 식별 합니다.

- 필요한 사용자 그룹에 따라 역할을 만들고 역할에 사용 권한을 할당 합니다. 
  - Azure Portal 또는 PowerShell-automation을 통해 관리 수준 태스크의 경우 RBAC 역할을 사용 합니다. 요구 사항과 일치 하는 기본 제공 역할을 찾거나 사용 가능한 권한을 사용 하 여 사용자 지정 RBAC 역할을 만듭니다. 
  - 관리 되는 인스턴스에서 서버 차원의 태스크 (새 로그인, 데이터베이스 만들기)에 대 한 서버 역할을 만듭니다. 
  - 데이터베이스 수준 태스크에 대 한 데이터베이스 역할을 만듭니다.

- 특정 한 중요 한 태스크의 경우 인증서로 서명 된 특수 저장 프로시저를 만들어 사용자를 대신 하 여 작업을 실행 하는 것이 좋습니다. 
  - 예: [자습서: 인증서로 저장 프로시저 서명](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Azure Key Vault에서 고객 관리 키를 사용 하 여 TDE (투명한 데이터 암호화)를 구현 하 여 데이터 소유자와 보안 소유자 간에 업무를 분리할 수 있습니다. 
  - [Azure Portal에서 Azure Storage 암호화를 위해 고객이 관리 하는 키 구성](../storage/common/storage-encryption-keys-portal.md)문서를 참조 하세요. 

- DBA가 매우 중요 한 것으로 간주 되 고 DBA 작업을 수행할 수 있는 데이터를 볼 수 없도록 하려면 역할 구분과 함께 Always Encrypted를 사용할 수 있습니다. 
  - 역할 구분을 사용 하 여 [Always Encrypted에 대 한 키 관리 개요](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), [역할 분리를 사용한 키 프로 비전](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)및 [열 마스터 키 회전](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation)에 대 한 개요 문서를 참조 하세요. 

- 시스템을 거의 사용할 수 없게 만들 수 있는 최소한의 비용과 노력 없이는 불가능 한 경우 다음과 같은 보정 제어를 사용 하 여 손상 될 수 있습니다. 
  - 사용자가 프로세스에서 작업 합니다. 
  - 감사 내역 – 감사에 대 한 자세한 내용은 [중요 보안 이벤트 감사](#audit-critical-security-events)를 참조 하세요.

**모범 사례**:

- 개발/테스트 및 프로덕션 환경에 다른 계정이 사용 되는지 확인 합니다. 여러 계정을 통해 테스트 & 프로덕션 시스템의 분리를 준수할 수 있습니다.

- 개별 사용자에 게 사용 권한을 할당 하지 마십시오. 대신 역할 (데이터베이스 또는 서버 역할)을 일관 되 게 사용 합니다. 역할을 사용 하면 보고 및 문제 해결 권한이 크게 향상 됩니다.

- 사용 권한이 필요한 권한과 정확히 일치 하는 경우 기본 제공 역할을 사용 합니다. 여러 기본 제공 역할의 모든 사용 권한 통합이 100% 일치를 야기 하는 경우 여러 역할을 동시에 할당할 수 있습니다. 

- 기본 제공 역할이 너무 많은 권한을 부여 하거나 권한이 부족 한 경우 사용자 지정 역할을 만들고 사용 합니다. 

- 또한 역할 할당을 일시적으로 수행할 수 있습니다 .이는 T-sql의 SQL 에이전트 작업 단계 내에서 또는 RBAC 역할에 대해 Azure PIM을 사용 하는 TD (동적 의무 분리) 라고도 합니다. 

- Dba에 게 암호화 키 또는 키 저장소에 대 한 액세스 권한이 없고 키에 대 한 액세스 권한이 있는 보안 관리자에 게는 데이터베이스에 대 한 액세스 권한이 없습니다. 

- 보안 관련 작업에 대 한 감사 내역이 항상 있는지 확인 합니다. 

- 기본 제공 RBAC 역할의 정의를 검색 하 여 사용 되는 사용 권한을 확인 하 고, Powershell을 통해 이러한 항목의 발췌 및 cumulations을 기반으로 사용자 지정 역할을 만들 수 있습니다. 

- Db_owner 데이터베이스 역할의 멤버는 TDE (투명한 데이터 암호화)와 같은 보안 설정을 변경 하거나 SLO를 변경할 수 있으므로이 멤버 자격은 신중 하 게 부여 되어야 합니다. 그러나 db_owner 권한이 필요한 많은 태스크가 있습니다. DB 옵션 변경 등의 데이터베이스 설정을 변경 하는 것과 같은 작업입니다. 감사는 모든 솔루션에서 주요 역할을 수행 합니다.

- Db_owner 권한을 제한 하 여 관리 계정이 사용자 데이터를 볼 수 없도록 할 수는 없습니다. 데이터베이스에 매우 중요 한 데이터가 있는 경우 Always Encrypted를 사용 하 여 db_owners 또는 다른 DBA가 해당 데이터를 볼 수 없도록 방지할 수 있습니다.

> [!NOTE]
> 의무를 분리 하는 것은 보안 관련 작업 또는 문제 해결 작업에 어려움이 있습니다. 개발 및 최종 사용자 역할과 같은 다른 영역은 더 쉽게 구분할 수 있습니다. 대부분의 호환성 관련 컨트롤을 사용 하면 다른 솔루션이 실용적이 지 않을 때 감사와 같은 대체 제어 기능을 사용할 수 있습니다.

이에 대 한 자세한 내용은 다음 리소스를 선택 하는 것이 좋습니다. 

- Azure SQL 데이터베이스의 경우.  
  - [SQL Database 및 SQL Data Warehouse에 대 한 데이터베이스 액세스 제어 및 권한 부여](sql-database-manage-logins.md)
  - [응용 프로그램 개발자에 대 한 의무의 엔진 분리](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [SQL Server 2014에서 의무 분리](https://www.microsoft.com/download/details.aspx?id=39269)
  - [SQL Server에서 저장 프로시저에 서명](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Azure 리소스 관리:
  - [Azure의 기본 제공 역할](../role-based-access-control/built-in-roles.md) 
  - [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md)
  - [관리자 권한으로 Azure AD Privileged Identity Management 사용](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>일반 코드 검토 수행

> 설명: PCI: 6.3.2, SOC: SDL-3 

의무의 분리는 데이터베이스의 데이터로 제한 되지 않지만 응용 프로그램 코드를 포함 합니다. 악의적인 코드는 잠재적으로 보안 제어를 우회할 수 있습니다. 사용자 지정 코드를 프로덕션에 배포 하기 전에 배포 되는 항목을 검토 하는 것이 중요 합니다.

**구현 방법**:

- 원본 제어를 지 원하는 Azure Data Studio와 같은 데이터베이스 도구를 사용 합니다. 

- 분리 된 코드 배포 프로세스를 구현 합니다.

- Main 분기에 커밋하기 전에 코드 자체의 작성자가 아닌 사람이 코드를 검사 하 여 악의적인 데이터 수정 뿐만 아니라 사기 행위 및 rogue 액세스 로부터 보호 하는 악의적인 데이터를 수정 해야 합니다. 원본 제어 메커니즘을 사용 하 여이 작업을 수행할 수 있습니다.

**모범 사례**:

- 표준화: 코드 업데이트에 대해 따라야 하는 표준 프로시저를 구현 하는 데 도움이 됩니다. 

- 취약성 평가에는 과도 한 사용 권한을 확인 하 고, 이전 암호화 알고리즘을 사용 하 고, 데이터베이스 스키마 내에서 기타 보안 문제를 확인 하는 규칙이 포함 됩니다. 

- 추가 검사는 SQL 삽입에 취약 한 코드를 검색 하는 고급 위협 방지를 사용 하 여 QA 또는 테스트 환경에서 수행할 수 있습니다.

- 확인할 항목의 예: 
  - 자동화 된 SQL 코드 업데이트 배포 내에서 사용자 만들기 또는 보안 설정 변경 
  - 제공 된 매개 변수에 따라 일치 하지 않는 방식으로 셀의 통화 값을 업데이트 하는 저장 프로시저입니다. 

- 검토를 수행 하는 사용자가 코드 검토 및 보안 코딩의 원래 코드 작성자 및 지식 이외의 개인 인지 확인 합니다.

- 코드 변경 내용에 대 한 모든 소스를 알고 있어야 합니다. 코드는 T-sql 스크립트에 있을 수 있습니다. 실행 하거나 보기, 함수, 트리거 및 저장 프로시저의 형태로 배포할 임시 명령 일 수 있습니다. SQL 에이전트 작업 정의에 포함 될 수 있습니다 (단계). SSIS 패키지, Azure Data Factory 및 기타 서비스 내에서 실행할 수도 있습니다.

## <a name="data-protection"></a>데이터 보호

데이터 보호는 암호화 또는 난독 처리의 손상 으로부터 중요 한 정보를 보호 하는 기능 집합입니다.

> [!NOTE]
> Microsoft 입증는 FIPS 140-2 수준 1을 준수 하는 것으로 Azure SQL Database 합니다. 이 작업은 필요한 키 길이, 키 관리, 키 생성 및 키 저장소와의 일관성을 비롯 하 여 FIPS 140-2 수준 1 허용 가능한 알고리즘과 FIPS 140-2 수준 1의 유효성을 검사 하 여 해당 알고리즘의 엄격한 사용을 확인 한 후에 수행 됩니다. 이 증명은 고객이 데이터를 처리 하거나 시스템이 나 응용 프로그램을 전달 하는 데 FIPS 140-2 수준 1의 유효성을 검사 하는 데 필요한 요구 또는 요구 사항에 대응할 수 있도록 하기 위한 것입니다. 위의 문에 사용 되는 "fips 140-2 Level 1 규격" 및 "FIPS 140-2 Level 1 준수" 라는 용어를 정의 하 여 "FIPS 140-2 Level 1 유효성 검사"와 같은 용어를 미국 및 캐나다 정부 기관에서 사용 하는 것에 대 한 적용 가능성을 보여 줍니다. 


### <a name="encrypt-data-in-transit"></a>전송 중인 데이터 암호화

> 설명: OSA 사례 #6, ISO 컨트롤 제품군: 암호화

클라이언트와 서버 간에 데이터가 이동 하는 동안 데이터를 보호 합니다. [네트워크 보안](#network-security)을 참조 하세요.

### <a name="encrypt-data-at-rest"></a>미사용 암호화 데이터

> 설명: OSA 사례 #6, ISO 컨트롤 제품군: 암호화

미사용 데이터 암호화는 데이터베이스, 로그 및 백업 파일에 유지 되는 데이터의 암호화 보호입니다.

**구현 방법**:

- 서비스 관리 키를 사용 하는 [TDE (투명 데이터베이스 암호화](transparent-data-encryption-azure-sql.md) )는 Azure SQL Database에서 2017 이후에 생성 된 모든 데이터베이스에 대해 기본적으로 사용 하도록 설정 됩니다.
- 관리 되는 인스턴스에서 데이터베이스가 온-프레미스 서버를 사용 하 여 복원 작업에서 만들어진 경우 원본 데이터베이스의 TDE 설정이 적용 됩니다. 원본 데이터베이스에서 TDE를 사용 하도록 설정 하지 않은 경우 관리 되는 인스턴스에 대해 TDE를 수동으로 설정 하는 것이 좋습니다.

**모범 사례**:

- Master 데이터베이스에서 미사용 암호화가 필요한 데이터는 저장 하지 않습니다. Master 데이터베이스는 TDE를 사용 하 여 암호화할 수 없습니다.

- Azure Key Vault에서 사용자가 관리 하는 키를 사용 합니다. Azure Key Vault를 사용 하면 언제 든 지 사용 권한을 취소 하 여 데이터베이스에 액세스할 수 없게 할 수 있습니다. 다른 키와 함께 TDE 보호기를 중앙에서 관리 하거나 Azure Key Vault를 사용 하 여 사용자의 일정에 따라 TDE 보호기를 회전할 수 있습니다.

- Azure Key Vault에서 고객 관리 키를 사용 하는 경우 [Azure Key Vault를 사용 하 여 TDE를 구성 하기 위한 지침](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) 과 [Azure Key Vault를 사용 하 여 지역에서 DR을 구성 하는 방법](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde)문서를 따르세요.

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>권한이 없는 사용자가 사용 하는 중요 한 데이터 보호

사용 중인 데이터는 SQL 쿼리를 실행 하는 동안 데이터베이스 시스템의 메모리에 저장 되는 데이터입니다. 데이터베이스에서 중요 한 데이터를 저장 하는 경우 조직에서는 권한이 높은 사용자가 데이터베이스에서 중요 한 데이터를 볼 수 없도록 해야 할 수 있습니다. 조직의 Microsoft operators 또는 Dba와 같은 높은 권한 사용자는 데이터베이스를 관리할 수 있지만 SQL Server 프로세스의 메모리 또는 데이터베이스를 쿼리하여 중요 한 데이터를 보고 잠재적으로 빼내기 위해 공공 수 없습니다.

**구현 방법**:

- [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) 를 사용 하 여 메모리/사용 시에도 중요 한 데이터가 Azure SQL Database 일반 텍스트로 노출 되지 않도록 합니다. Always Encrypted는 Dba (데이터베이스 관리자) 및 클라우드 관리자 (또는 권한이 없지만 권한이 없는 사용자를 가장할 수 있는 잘못 된 행위자)의 데이터를 보호 하 고 데이터에 액세스할 수 있는 사용자를 보다 강력 하 게 제어할 수 있도록 합니다.

**모범 사례**:

- Always Encrypted은 미사용 데이터 (TDE) 또는 전송 중 (SSL/TLS)을 대체 하지 않습니다. 성능 및 기능 영향을 최소화 하기 위해 중요 하지 않은 데이터에는 Always Encrypted를 사용할 수 없습니다. TDE 및 TLS (전송 계층 보안)와 함께 Always Encrypted를 사용 하는 것은 미사용 데이터, 전송 중 및 사용 중인 데이터의 포괄적인 보호에 사용 하는 것이 좋습니다. 

- Always Encrypted를 사용 하 여 악의적인 Dba 로부터 데이터를 보호 하는 경우 역할 구분을 사용 하 여 Always Encrypted 키를 관리 합니다. 역할 분리를 사용 하면 보안 관리자가 물리적 키를 만듭니다. DBA는 데이터베이스의 물리적 키를 설명 하는 열 마스터 키 및 열 암호화 키 메타 데이터 개체를 만듭니다. 이 과정에서 보안 관리자는 데이터베이스에 대 한 액세스가 필요 하지 않으며 DBA는 일반 텍스트의 물리적 키에 액세스할 필요가 없습니다. 
  - 자세한 내용은 [역할 구분을 사용 하 여 키 관리](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) 문서를 참조 하세요. 

- 관리를 용이 하 게 하기 위해 Azure Key Vault에 열 마스터 키를 저장 합니다. 키 관리를 지 원하는 Windows 인증서 저장소 (일반적으로 중앙 키 관리 솔루션과 달리 분산 키 저장소 솔루션)를 사용 하지 마십시오. 

- 여러 키 (열 마스터 키 또는 열 암호화 키) 사용의 장단점을 신중 하 게 고려해 야 합니다. 키 관리 비용을 줄이기 위해 키 수를 작게 유지 합니다. 단일 열 마스터 키와 데이터베이스당 하나의 열 암호화 키가 일반적으로 안정적인 상태 환경에서 충분 합니다 (키 회전이 아닌). 각각 서로 다른 키를 사용 하 고 다른 데이터에 액세스 하는 다른 사용자 그룹이 있는 경우 추가 키가 필요할 수 있습니다.  

- 규정 준수 요구 사항에 따라 열 마스터 키를 회전 합니다. 열 암호화 키를 회전 해야 하는 경우 온라인 암호화를 사용 하 여 응용 프로그램 가동 중지 시간을 최소화 하는 것이 좋습니다. 
  - [성능 및 가용성 고려 사항](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations)문서를 참조 하세요. 

- 데이터의 계산 (같음)을 지원 해야 하는 경우 결정적 암호화를 사용 합니다. 그렇지 않으면 임의 암호화를 사용 합니다. 낮은 엔트로피 데이터 집합 또는 공개적으로 알려진 배포가 포함 된 데이터 집합에는 결정적 암호화를 사용 하지 마십시오. 

- 동의 없이 데이터에 액세스 하는 제 3 자에 대해 염려 하는 경우 일반 텍스트의 키 및 데이터에 대 한 액세스 권한이 있는 모든 응용 프로그램 및 도구가 Microsoft Azure 클라우드 외부에서 실행 되도록 합니다. 키에 대 한 액세스 권한이 없으면 제 3 자가 암호화를 우회 하지 않는 한 데이터를 암호 해독할 수 없습니다.

- Always Encrypted는 키 및 보호 된 데이터에 대 한 임시 액세스 권한을 부여 하는 것을 쉽게 지원 하지 않습니다. 예를 들어 dba와 키를 공유 하 여 DBA가 중요 하 고 암호화 된 데이터에 대해 일부 정리 작업을 수행할 수 있도록 해야 하는 경우. DBA에서 데이터에 대 한 액세스를 취소 하는 유일한 방법은 데이터를 보호 하는 열 암호화 키와 열 마스터 키를 모두 순환 하는 것입니다 .이는 비용이 많이 드는 작업입니다. 

- 암호화 된 열에서 일반 텍스트 값에 액세스 하려면 사용자가 CMK를 보유 하는 키 저장소에 구성 된 열을 보호 하는 CMK에 대 한 액세스 권한이 있어야 합니다. 또한 사용자에 게 **VIEW ANY COLUMN MASTER KEY definition** 및 **VIEW ANY COLUMN ENCRYPTION key definition** database 권한이 있어야 합니다.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>암호화를 통해 중요 한 데이터에 대 한 응용 프로그램 사용자의 액세스 제어

암호화는 암호화 키에 대 한 액세스 권한이 있는 특정 응용 프로그램 사용자만 데이터를 보거나 업데이트할 수 있도록 하는 방법으로 사용할 수 있습니다.

**구현 방법**:

- CLE (셀 수준 암호화)를 사용 합니다. 자세한 내용은 [데이터 열 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) 문서를 참조 하세요. 
- Always Encrypted를 사용 하지만 제한 사항을 알고 있어야 합니다. 제한 사항은 다음과 같습니다.

**모범 사례**

CLE를 사용 하는 경우:

- SQL 사용 권한 및 역할을 통해 키에 대 한 액세스를 제어 합니다. 

- 데이터 암호화에 AES (AES 256 권장)를 사용 합니다. RC4, DES, TripleDES 등의 알고리즘은 더 이상 사용 되지 않으며 알려진 취약점으로 인해 사용 하면 안 됩니다. 

- 3DES를 사용 하지 않도록 비대칭 키/인증서 (암호 아님)를 사용 하 여 대칭 키를 보호 합니다. 

- 내보내기/가져오기 (bacpac 파일)를 통해 셀 수준 암호화를 사용 하 여 데이터베이스를 마이그레이션할 때는 주의 해야 합니다. 
  - 데이터를 마이그레이션할 때 키 손실을 방지 하는 방법에 대 한 [Azure SQL Database에서 셀 수준 암호화 사용에 대 한 권장 사항](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) 및 기타 모범 사례 지침을 참조 하세요.

Always Encrypted는 주로 Azure SQL Database (클라우드 연산자, Dba)의 높은 권한 사용자가 사용 하는 중요 한 데이터를 보호 하기 위해 설계 되었습니다. 권한이 [높은 권한이 없는 사용자의 중요 한 데이터 보호](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)를 참조 하세요. Always Encrypted를 사용 하 여 응용 프로그램 사용자 로부터 데이터를 보호 하는 경우 다음과 같은 문제를 고려해 야 합니다.

- 기본적으로 Always Encrypted를 지 원하는 모든 Microsoft 클라이언트 드라이버는 열 암호화 키의 전역 (응용 프로그램당) 캐시를 유지 관리 합니다. 클라이언트 드라이버가 열 마스터 키를 포함 하는 키 저장소에 연결 하 여 일반 텍스트 열 암호화 키를 얻은 후에는 일반 텍스트 열 암호화 키가 캐시 됩니다. 이렇게 하면 다중 사용자 응용 프로그램 사용자의 데이터를 격리 하기가 어려워집니다. 응용 프로그램에서 키 저장소 (예: Azure Key Vault)와 상호 작용할 때 최종 사용자를 가장 하는 경우, 사용자의 쿼리가 열 암호화 키를 사용 하 여 캐시를 채운 후 동일한 키를 필요로 하지만 다른 사용자가 트리거한 후속 쿼리는 캐시 된 키입니다. 드라이버는 키 저장소를 호출 하지 않으며 두 번째 사용자에 게 열 암호화 키에 대 한 액세스 권한이 있는지 확인 하지 않습니다. 따라서 사용자가 키에 액세스할 수 없는 경우에도 사용자가 암호화 된 데이터를 볼 수 있습니다. 다중 사용자 응용 프로그램 내에서 사용자를 격리 하기 위해 열 암호화 키 캐싱을 사용 하지 않도록 설정할 수 있습니다. 캐싱을 사용 하지 않도록 설정 하면 드라이버에서 각 데이터 암호화 또는 암호 해독 작업에 대해 키 저장소에 연결 해야 하므로 추가 성능 오버 헤드가 발생 합니다.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>데이터 형식을 유지 하면서 응용 프로그램 사용자의 무단 보기 로부터 데이터 보호
권한이 없는 사용자가 데이터를 볼 수 없도록 방지 하는 또 다른 방법은 데이터 형식 및 형식을 유지 하면서 데이터를 난독 처리 하거나 마스킹하 여 사용자 응용 프로그램에서 데이터를 계속 처리 하 고 표시할 수 있도록 하는 것입니다.

**구현 방법**:

- [동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) 를 사용 하 여 테이블 열을 난독 처리 합니다.

> [!NOTE]
> Always Encrypted은 동적 데이터 마스킹에서 작동 하지 않습니다. 동일한 열을 암호화 하 고 마스킹할 수는 없습니다 .이는 사용 중인 데이터 보호의 우선 순위를 지정 하 고 동적 데이터 마스킹를 통해 앱 사용자에 대 한 데이터를 마스킹 해야 함을 의미 합니다.

**모범 사례**:

> [!NOTE]
> 동적 데이터 마스킹는 높은 권한 사용자의 데이터를 보호 하는 데 사용할 수 없습니다. 마스킹 정책은 db_owner와 같은 관리자 액세스 권한이 있는 사용자에 게는 적용 되지 않습니다.

- 앱 사용자가 임시 쿼리를 실행 하는 것을 허용 하지 않습니다 (동적 데이터 마스킹를 해결할 수 있음).  
  - 자세한 내용은 유추를 [사용 하 여 마스킹 또는 무차별 대입 기법 무시](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) 문서를 참조 하세요.  

- 적절 한 액세스 제어 정책 (SQL 사용 권한, 역할, RLS를 통해)을 사용 하 여 마스킹된 열에서 업데이트를 수행할 수 있는 사용자 권한을 제한 합니다. 열에 대 한 마스크를 만들면 해당 열에 대 한 업데이트가 차단 되지 않습니다. 마스킹된 열을 쿼리할 때 마스킹된 데이터를 수신 하는 사용자는 쓰기 권한이 있는 경우 데이터를 업데이트할 수 있습니다.    

-  동적 데이터 마스킹는 마스킹된 값의 통계 속성을 유지 하지 않습니다. 이는 쿼리 결과에 영향을 미칠 수 있습니다 (예: 마스킹된 데이터에 대 한 필터링 조건자 또는 조인이 포함 된 쿼리).

## <a name="network-security"></a>네트워크 보안
네트워크 보안은 Azure SQL Database 전송 중인 데이터를 보호 하기 위한 액세스 제어 및 모범 사례를 나타냅니다.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Azure SQL Database에 안전 하 게 연결 하도록 클라이언트 구성 
잘 알려진 취약점 (예: 이전 TLS 프로토콜 및 암호 그룹 사용)을 사용 하 여 클라이언트 컴퓨터 및 응용 프로그램을 Azure SQL Database에 연결 하지 못하게 하는 방법에 대 한 모범 사례입니다.

**구현 방법**:

- Azure SQL Database에 연결 하는 클라이언트 컴퓨터에서 [TLS (Transport Layer Security)](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)를 사용 하 고 있는지 확인 합니다.

**모범 사례**:

- 암호화를 사용 하 여 SQL DB에 연결 하도록 모든 앱 및 도구 구성 
  - Encrypt = On, TrustServerCertificate = Off (또는 타사 드라이버와 동일). 

- 앱이 TLS를 지원 하지 않거나 이전 버전의 TLS를 지 원하는 드라이버를 사용 하는 경우 가능 하면 드라이버를 바꿉니다. 가능 하지 않은 경우 보안 위험을 신중 하 게 평가 합니다. 

- [Tls (Transport Layer Security) 레지스트리 설정](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10)에 Azure SQL Database에 연결 하는 클라이언트 컴퓨터에서 ssl 2.0, ssl 3.0, tls 1.0 및 tls 1.1의 취약성을 통해 공격 벡터를 줄입니다. 

- 클라이언트에서 사용할 수 있는 암호 그룹 확인: [TLS/SSL (SCHANNEL SSP)의 암호 그룹](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel)입니다. 특히, [TLS 암호 그룹 순서 구성](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)에 따라 3des를 사용 하지 않도록 설정 합니다. 

- Azure SQL Database의 경우 프록시 및 리디렉션 연결 형식 모두에 대해 암호화가 적용 됩니다. 관리 되는 인스턴스를 사용 하는 경우 서버 쪽에서 암호화를 적용 하는 **프록시** 연결 형식 (기본값)을 사용 합니다. **리디렉션** 연결 유형은 현재 암호화 적용을 지원 하지 않으며 개인 IP 연결에 대해서만 사용할 수 있습니다. 

- 자세한 내용은 [AZURE SQL 연결 아키텍처-연결 정책](sql-database-connectivity-architecture.md#connection-policy)을 참조 하세요.


### <a name="minimize-attack-surface"></a>공격 노출 영역 최소화
악의적인 사용자의 공격을 받을 수 있는 기능 수를 최소화 합니다. Azure SQL Database에 대 한 네트워크 액세스 제어를 구현 합니다.

> 설명: OSA 사례 #5

**구현 방법**:

단일 데이터베이스 또는 탄력적 풀을 포함 하는 Azure SQL Database 서버에서 다음을 수행 합니다.
- Azure 서비스에 대 한 액세스 허용을 OFF로 설정 합니다.

- VNet 서비스 엔드포인트 및 VNet 방화벽 규칙을 사용 합니다.

- 개인 링크 (미리 보기)를 사용 합니다.

관리 되는 인스턴스:
- [네트워크 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)의 지침을 따릅니다. 

**모범 사례**:

- 개인 엔드포인트 (예: 전용 데이터 경로 사용)에 연결 하 여 Azure SQL Database에 대 한 액세스를 제한 합니다. 
  - 외부 액세스를 방지 하기 위해 관리 되는 인스턴스를 VNet 내부에 격리할 수 있습니다. 동일한 지역에 있는 동일한 또는 피어 링 VNet에 있는 응용 프로그램 및 도구는 직접 액세스할 수 있습니다. 다른 지역의 응용 프로그램 및 도구는 VNet 간 연결 또는 Express 경로 회로 피어 링을 사용 하 여 연결을 설정할 수 있습니다. 고객은 NSG (네트워크 보안 그룹)를 사용 하 여 관리 되는 인스턴스에 액세스 해야 하는 리소스로만 포트 1433을 통해 액세스를 제한 해야 합니다. 
  - 단일 데이터베이스 또는 탄력적 풀을 포함 하는 SQL Database 서버의 경우 VNet 내에서 SQL Database 서버에 대 한 전용 개인 IP를 제공 하는 [개인 링크](sql-database-private-endpoint-overview.md) 기능을 사용 합니다. Vnet 방화벽 규칙을 사용 하 여 [Vnet 서비스 엔드포인트](sql-database-vnet-service-endpoint-rule-overview.md) 을 사용 하 여 SQL Database 서버에 대 한 액세스를 제한할 수도 있습니다.
  - 모바일 사용자는 지점 및 사이트 간 VPN 연결을 사용 하 여 데이터 경로를 통해 연결 해야 합니다.
  - 온-프레미스 네트워크에 연결 된 사용자는 사이트 간 VPN 연결 또는 Express 경로를 사용 하 여 데이터 경로를 통해 연결 해야 합니다.

- 공용 엔드포인트 (예: 공용 데이터 경로 사용)에 연결 하 여 Azure SQL Database에 액세스할 수 있습니다. 다음 모범 사례를 고려해 야 합니다. 
  - SQL Database 서버의 경우 [ip 방화벽 규칙](sql-database-firewall-configure.md) 을 사용 하 여 권한이 부여 된 ip 주소에만 액세스를 제한 합니다.
  - 관리 되는 인스턴스의 경우 NSG (네트워크 보안 그룹)를 사용 하 여 포트 3342을 통한 액세스만 필요한 리소스로 제한 합니다. 자세한 내용은 [공용 엔드포인트과 안전 하 게 Azure SQL Database 관리 되는 인스턴스 사용](sql-database-managed-instance-public-endpoint-securely.md)을 참조 하세요. 

> [!NOTE]
> 관리 되는 인스턴스 공용 엔드포인트은 기본적으로 사용 하도록 설정 되어 있지 않으며 명시적으로 사용 하도록 설정 해야 합니다. 회사 정책에서 공용 엔드포인트 사용을 허용 하지 않는 경우 [Azure Policy](../governance/policy/overview.md) 를 사용 하 여 첫 번째 위치의 공용 엔드포인트 사용을 방지 합니다.

- Azure 네트워킹 구성 요소를 설정 합니다. 
  - [네트워크 보안에 대 한 Azure 모범 사례를](../security/fundamentals/network-best-practices.md)따릅니다.
  - 모범 사례에 따라 [Azure VIRTUAL NETWORK faq (질문과 대답)](../virtual-network/virtual-networks-faq.md) 및 요금제에 설명 된 모범 사례에 대 한 계획 Virtual Network (VNet) 구성입니다. 
  - VNet을 여러 서브넷으로 분할 하 고 유사한 역할에 대 한 리소스를 동일한 서브넷에 할당 합니다 (예: 프런트 엔드 및 백 엔드 리소스).
  - [NSGs (네트워크 보안 그룹)](../virtual-network/security-overview.md) 를 사용 하 여 Azure VNet 경계 내의 서브넷 간 트래픽을 제어 합니다.
  - 인바운드 및 아웃 바운드 네트워크 트래픽을 모니터링 하려면 구독에 대해 [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) 를 사용 하도록 설정 합니다.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Azure SQL Database에 대 한 보안 연결 Power BI 구성

**모범 사례**:

- Power BI Desktop의 경우 가능 하면 전용 데이터 경로를 사용 합니다. 

- Tls ( [전송 계층 보안)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) 레지스트리 설정에 따라 클라이언트 컴퓨터에서 레지스트리 키를 설정 하 여 POWER BI DESKTOP가 tls 1.2를 사용 하 여 연결 되는지 확인 합니다. 

- [Power BI를 사용 하 여 RLS (행 수준 보안)](https://docs.microsoft.com/power-bi/service-admin-rls)를 통해 특정 사용자에 대 한 데이터 액세스를 제한 합니다. 

- Power BI 서비스의 경우, [온-프레미스 데이터 게이트웨이](https://docs.microsoft.com/power-bi/service-gateway-onprem)를 사용 하 여 [제한 사항 및 고려 사항을](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)염두에 두어야 합니다.

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Azure SQL Database에 대 한 보안 연결 App Service 구성

**모범 사례**:

- 간단한 웹 앱의 경우 공용 엔드포인트을 통해 연결 하려면 **Azure 서비스 허용** 을 설정 해야 합니다. 

- [Azure Virtual Network에 앱을 통합](../app-service/web-sites-integrate-with-vnet.md) 하 여 전용 데이터 경로를 관리 되는 인스턴스에 연결 합니다. 필요에 따라 [ASE (App Service 환경)](../app-service/environment/intro.md)를 사용 하 여 웹 앱을 배포할 수도 있습니다. 

- ASE 또는 VNet 통합 웹 앱이 있는 웹 앱의 경우 SQL Database 서버의 데이터베이스에 연결 하는 경우 [Vnet 서비스 엔드포인트 및 Vnet 방화벽 규칙](sql-database-vnet-service-endpoint-rule-overview.md) 을 사용 하 여 특정 vnet 및 서브넷에서 액세스를 제한할 수 있습니다. 그런 다음 **Azure 서비스 허용** 을 OFF로 설정 합니다. 또한 ASE를 개인 데이터 경로를 통해 관리 되는 인스턴스에 연결할 수 있습니다.  

- [Azure App Service를 사용 하 여 PaaS 웹 및 모바일 응용 프로그램을 보호 하는 방법에 대 한](../security/security-paas-applications-using-app-services.md)문서에 따라 웹 앱이 구성 되어 있는지 확인 합니다. 

- [WAF (웹 응용 프로그램 방화벽)](../application-gateway/waf-overview.md) 를 설치 하 여 일반적인 악용 및 취약성 으로부터 웹 앱을 보호 합니다.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Azure SQL Database에 대 한 보안 연결을 위한 Azure VM 호스팅 구성

**모범 사례**:

- Azure Vm의 NSGs에 대 한 허용 및 거부 규칙의 조합을 사용 하 여 VM에서 액세스할 수 있는 지역을 제어 합니다.

- [Azure의 IaaS 작업에 대 한 보안 모범 사례](../security/azure-security-iaas.md)문서에 따라 VM이 구성 되어 있는지 확인 합니다.

- 모든 Vm이 특정 VNet 및 서브넷에 연결 되어 있는지 확인 합니다. 

- [강제 터널링 정보](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)에서 지침에 따라 기본 경로 0.0.0.0/인터넷이 필요한 경우 평가 합니다. 
  - 예 (예: 프런트 엔드 서브넷)는 기본 경로를 유지 합니다.
  - No (예: 중간 계층 또는 백 엔드 서브넷)를 사용 하는 경우 트래픽이 인터넷을 통해 이동 하 여 온-프레미스 (예: 크로스-프레미스)에 도달 하지 않도록 강제 터널링을 사용 하도록 설정 합니다. 

- 피어 링을 사용 하거나 온-프레미스에 연결 하는 경우 [선택적 기본 경로](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) 를 구현 합니다. 

- 패킷 검사를 위해 VNet의 모든 트래픽을 네트워크 가상 어플라이언스로 전송 해야 하는 경우 [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md#user-defined) 를 구현 합니다. 

- [VNet 서비스 엔드포인트](sql-database-vnet-service-endpoint-rule-overview.md) 을 사용 하 여 Azure 백본 네트워크를 통해 Azure Storage와 같은 PaaS 서비스에 안전 하 게 액세스할 수 있습니다. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>DDoS (분산 서비스 거부) 공격 으로부터 보호
DDoS (배포 된 서비스 거부) 공격은 악의적인 사용자가 Azure 인프라를 과도 하 게 사용 하 여 유효한 로그인 및 워크 로드를 거부 하는 데 많은 양의 네트워크 Azure SQL Database 트래픽을 전송 하는 데 사용 됩니다.

> 설명: OSA 사례 #9

**구현 방법**:

DDoS 보호는 Azure 플랫폼의 일부로 자동으로 사용 하도록 설정 됩니다. 여기에는 공용 엔드포인트에 대 한 네트워크 수준 공격의 실시간 완화 및 상시 트래픽 모니터링이 포함 됩니다. 

- [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) 를 사용 하 여 vnet에 배포 된 리소스에 연결 된 공용 IP 주소를 모니터링할 수 있습니다.

- [Azure SQL Database에 대 한 Advanced Threat Protection](sql-database-threat-detection-overview.md) 을 사용 하 여 데이터베이스에 대 한 Dos (서비스 거부) 공격을 감지 합니다.

**모범 사례**:

- [공격 노출 영역 최소화](#minimize-attack-surface) 에 설명 된 지침에 따라 DDoS 공격 위협을 최소화 합니다. 

- Advanced Threat Protection **무차별 암호 대입 SQL 자격 증명** 경고는 무차별 암호 대입 공격을 검색 하는 데 도움이 됩니다. 경우에 따라 경고는 침투 테스트 작업을 구분할 수 있습니다. 

- SQL Database에 연결 하는 Azure VM 호스팅 응용 프로그램의 경우: 
  - 권장 사항에 따라 Azure Security Center에서 인터넷 연결 엔드포인트을 통해 액세스를 제한 합니다. 
  - Virtual machine scale sets를 사용 하 여 Azure Vm에서 응용 프로그램의 여러 인스턴스를 실행 합니다. 
  - 무차별 암호 대입 공격을 방지 하려면 인터넷에서 RDP 및 SSH를 사용 하지 않도록 설정 합니다. 

## <a name="monitoring-logging-and-auditing"></a>모니터링, 로깅 및 감사  
이 섹션에서는 비정상적인 활동을 검색 하는 데 도움이 되는 비정상적인 활동을 검색 하는 데 도움이 되는 기능을 참조 합니다. 또한 데이터베이스 이벤트를 추적 하 고 캡처하기 위해 데이터베이스 감사를 구성 하는 모범 사례에 대해 설명 합니다.

### <a name="protect-databases-against-attacks"></a>공격 으로부터 데이터베이스 보호 
고급 위협 방지를 사용 하면 비정상적인 활동에 대 한 보안 경고를 제공 하 여 발생 하는 잠재적인 위협을 감지 하 고 대응할 수 있습니다.

**구현 방법**:

- [SQL에 대 한 Advanced Threat Protection](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) 을 사용 하 여 다음을 비롯 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 잠재적으로 유해한 시도를 검색 합니다.
  - SQL 삽입 공격입니다.
  - 자격 증명 도난/유출.
  - 권한 남용.
  - 데이터 exfiltration.

**모범 사례**:

- 특정 SQL Database 서버 또는 관리 되는 인스턴스에 대 한 Azure SQL Database에 대 한 [광고 (Advanced Data Security)](sql-database-advanced-data-security.md#getting-started-with-ads) 구성 합니다. [Azure Security Center 표준 계층](../security-center/security-center-pricing.md)으로 전환 하 여 구독에 있는 모든 SQL Database 서버 및 관리 되는 인스턴스에 대해 광고를 구성할 수도 있습니다. 

- 전체 조사 환경을 위해서는 [SQL Database 감사](sql-database-auditing.md)를 사용 하도록 설정 하는 것이 좋습니다. 감사를 사용 하면 데이터베이스 이벤트를 추적 하 고 Azure Storage 계정 또는 Azure Log Analytics 작업 영역에서 감사 로그에 쓸 수 있습니다. 

### <a name="audit-critical-security-events"></a>중요 보안 이벤트 감사
데이터베이스 이벤트를 추적 하면 데이터베이스 작업을 이해 하는 데 도움이 됩니다. 비즈니스 문제나 의심 스러운 보안 위반을 나타낼 수 있는 불일치 및 비정상을 파악할 수 있습니다. 또한 규정 준수 표준을 준수 하도록 설정 하 고 지원 합니다. 

**구현 방법**:

-  [SQL Database 감사](sql-database-auditing.md) 를 사용 하 여 데이터베이스 이벤트를 추적 하 고 Azure Storage 계정, Log Analytics 작업 영역 (미리 보기) 또는 Event Hubs (미리 보기)의 감사 로그에 기록 합니다. 

- 감사 로그는 Azure Storage 계정, Azure Monitor 로그에서 사용 하기 위한 Log Analytics 작업 영역 또는 이벤트 허브를 사용 하 여 사용 하기 위해 이벤트 허브에 기록 될 수 있습니다. 이러한 옵션을 조합하여 구성할 수 있으며, 감사 로그는 각각에 대해 작성됩니다. 

**모범 사례**:

- 데이터베이스 서버에서 감사 이벤트에 대 한 [SQL Database 감사](sql-database-auditing.md) 를 구성 하면 해당 서버에 있는 모든 기존 및 새로 만든 데이터베이스가 감사 됩니다.
- 기본적으로 감사 정책에는 데이터베이스에 대 한 모든 작업 (쿼리, 저장 프로시저 및 성공 및 실패 한 로그인)이 포함 되어 많은 수의 감사 로그가 생성 될 수 있습니다. 사용자가 [PowerShell을 사용 하 여 다양 한 유형의 작업 및 작업 그룹에 대 한 감사를 구성](sql-database-auditing.md#subheading-7)하는 것이 좋습니다. 이렇게 구성 하면 감사 된 작업의 수를 제어 하 고 이벤트 손실의 위험을 최소화할 수 있습니다. 사용자 지정 감사 구성을 통해 고객은 필요한 감사 데이터만 캡처할 수 있습니다.
- 감사 로그는 [Azure Portal](https://portal.azure.com/)에서 직접 사용 하거나 구성 된 저장소 위치에서 사용할 수 있습니다. 


> [!NOTE]
> Log Analytics에 감사를 사용하면 수집 속도에 따른 요금이 부과됩니다. 이 [옵션](https://azure.microsoft.com/pricing/details/monitor/), 사용 시 관련된 비용에 주의하세요.또는 Azure Storage 계정에 로그인 감사를 저장하는 것을 고려해 보세요. 

**추가 리소스**:

- [SQL 데이터베이스 감사](sql-database-auditing.md)
- [SQL Server 감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>보안 감사 로그
저장소 계정에 대 한 액세스를 제한 하 여 업무 분리를 지원 하 고 감사자와 DBA를 분리 합니다. 

**구현 방법**:

- Azure Storage에 감사 로그를 저장 하는 경우 저장소 계정에 대 한 액세스가 최소한의 보안 원칙으로 제한 되는지 확인 합니다. 저장소 계정에 대 한 액세스 권한이 있는 사용자를 제어 합니다.
    - 자세한 내용은 [Azure Storage에 대 한 액세스 권한 부여](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조 하세요.

**모범 사례**:

- 감사 대상에 대 한 액세스를 제어 하는 것은 감사자에서 DBA를 분리 하는 주요 개념입니다. 

- 중요 한 데이터에 대 한 액세스를 감사할 때 감사에 대 한 정보 누출을 방지 하기 위해 데이터 암호화로 데이터를 보호 하는 것이 좋습니다. 자세한 내용은 권한이 [없는 권한 있는 사용자가 사용 하는 중요 한 데이터 보호](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)섹션을 참조 하세요.

## <a name="security-management"></a>보안 관리

이 섹션에서는 데이터베이스 보안 상태를 관리 하기 위한 다양 한 측면과 모범 사례에 대해 설명 합니다. 데이터베이스에서 잠재적으로 중요 한 데이터에 대 한 액세스를 검색 하 고 분류 및 추적 하기 위해 데이터베이스를 보안 표준을 충족 하도록 구성 하는 모범 사례를 포함 합니다. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>데이터베이스가 보안 모범 사례에 맞게 구성 되어 있는지 확인 합니다. 

잠재적 데이터베이스 취약성을 검색 하 고 수정 하 여 데이터베이스 보안을 사전에 향상 합니다.

**구현 방법**:

- [SQL 취약성 평가](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA)를 사용 하 여 데이터베이스에서 보안 문제를 검색 하 고 데이터베이스에서 주기적으로 자동으로 실행 되도록 합니다.

**모범 사례**:

- 처음에는 데이터베이스에서 VA를 실행 하 고 보안 모범 사례를 oppose 수정 실패 검사를 반복 합니다. 검사가 _깨끗_하거나 모든 검사가 통과 될 때까지 허용 되는 구성에 대 한 기준을 설정 합니다.  

- 정기적 되풀이 검색을 구성 하 여 매주 한 번 실행 하 고 요약 전자 메일을 받을 관련 사용자를 구성 합니다. 

- 각 주간 검색 후 VA 요약을 검토 합니다. 발견 된 취약성에 대해 이전 검사 결과에서 드리프트를 평가 하 고 확인을 해결 해야 하는지 확인 합니다. 구성 변경에 대 한 정당한 이유가 있는지 검토 합니다.   

- 확인을 해결 하 고 관련 된 기준을 업데이트 합니다. 작업을 확인 하 고 해결 될 때까지 추적 하는 데 사용할 티켓 항목을 만듭니다. 

**추가 리소스**:

- [SQL 취약성 평가](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [SQL 취약성 평가 서비스를 사용 하 여 데이터베이스 취약성 파악](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>중요 한 데이터 식별 및 태그 지정 

잠재적으로 중요 한 데이터가 포함 된 열을 검색 합니다. 고급 민감도 기반 감사 및 보호 시나리오를 사용 하도록 열을 분류 합니다. 

**구현 방법**:

- [SQL 데이터 검색 및 분류](sql-database-data-discovery-and-classification.md) 를 사용 하 여 데이터베이스에서 중요 한 데이터를 검색, 분류, 레이블 및 보호 합니다. 
  - SQL 데이터 검색 및 분류 대시보드에서 자동 검색을 통해 생성 되는 분류 권장 사항을 봅니다. 중요 한 데이터에 분류 레이블로 영구적으로 태그를 지정 하는 관련 분류를 적용 합니다. 
  - 자동화 된 메커니즘에 의해 검색 되지 않은 추가 중요 데이터 필드에 대 한 분류를 수동으로 추가 합니다. 
- 자세한 내용은 [SQL 데이터 검색 & 분류](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification)를 참조 하세요.

**모범 사례**:

- 정기적으로 분류 대시보드를 모니터링 하 여 데이터베이스의 분류 상태를 정확 하 게 평가 합니다. 데이터베이스 분류 상태에 대 한 보고서는 준수 및 감사를 위해 공유 하도록 내보내거나 인쇄할 수 있습니다.

- SQL 취약성 평가에서 권장 되는 중요 한 데이터의 상태를 지속적으로 모니터링 합니다. 중요 한 데이터 검색 규칙을 추적 하 고 분류를 위해 권장 되는 열에서 드리프트를 식별 합니다.  

- 조직의 특정 요구에 맞게 조정 된 방식으로 분류를 사용 합니다. Azure Security Center의 [SQL Information Protection](../security-center/security-center-info-protection-policy.md) 정책에서 Information Protection 정책 (민감도 레이블, 정보 유형, 검색 논리)을 사용자 지정 합니다. 

### <a name="track-access-to-sensitive-data"></a>중요 한 데이터에 대 한 액세스 추적 
중요 한 데이터에 액세스 하는 사용자를 모니터링 하 고 감사 로그의 중요 한 데이터에 대해 쿼리를 캡처합니다.

**구현 방법**:

- SQL 감사 및 데이터 분류를 조합 하 여 사용 합니다. 
  - [SQL Database 감사](sql-database-auditing.md) 로그에서 특히 중요 한 데이터에 대 한 액세스를 추적할 수 있습니다. 또한 액세스 한 데이터와 같은 정보 및 해당 민감도 레이블을 볼 수 있습니다. 자세한 내용은 [중요 한 데이터에 대 한 액세스 감사](sql-database-data-discovery-and-classification.md#subheading-3)를 참조 하세요. 

**모범 사례**:

- 감사 및 데이터 분류 섹션에 대 한 모범 사례를 참조 하세요. 
  - [중요 보안 이벤트 감사](#audit-critical-security-events) 
  - [중요 한 데이터 식별 및 태그 지정](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>보안 및 규정 준수 상태 시각화 

SQL 데이터베이스를 포함 하 여 데이터 센터의 보안 상태를 강화 하는 통합 인프라 보안 관리 시스템을 사용 합니다. 데이터베이스의 보안 및 준수 상태와 관련 된 권장 사항 목록을 봅니다.

**구현 방법**:

- [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/)에서 SQL 관련 보안 권장 사항 및 활성 위협을 모니터링 합니다.

## <a name="common-security-threats-and-potential-mitigations"></a>일반적인 보안 위협 및 잠재적 완화

이 섹션은 특정 공격 벡터 로부터 보호 하기 위한 보안 조치를 찾는 데 도움이 됩니다. 위의 보안 지침 중 하나 이상을 수행 하 여 대부분의 완화가 가능 합니다.

### <a name="security-threat-data-exfiltration"></a>보안 위협: 데이터 반출

데이터 반출은 컴퓨터 또는 서버에서 데이터를 복사, 전송 또는 검색 하지 못하도록 합니다. 위키백과의 [데이터 반출](https://en.wikipedia.org/wiki/Data_exfiltration) 에 대 한 정의를 참조 하세요.

공용 엔드포인트을 통해 Azure SQL Database 서버에 연결 하면 고객이 공용 ip에 대 한 방화벽을 열어야 하므로 데이터 반출 위험이 있습니다.  

**시나리오 1**: Azure VM의 응용 프로그램은 Azure SQL Database 서버의 데이터베이스에 연결 합니다. Rogue 행위자는 VM에 대 한 액세스 권한을 가져오고이를 손상 시킵니다. 이 시나리오에서 데이터 반출은 rogue VM을 사용 하는 외부 엔터티가 데이터베이스에 연결 하 고, 개인 데이터를 복사 하 여 다른 구독에 있는 다른 SQL Database blob 저장소에 저장 하는 것을 의미 합니다.

**시나리오 2**: 불량 DBA 이 시나리오는 규정 된 업계의 보안 민감한 고객에 의해 발생 하는 경우가 많습니다. 이 시나리오에서 높은 권한 사용자는 데이터를 Azure SQL Database에서 데이터 소유자가 제어 하지 않는 다른 구독으로 복사할 수 있습니다.

**잠재적 완화**:

현재 Azure SQL Database에서는 데이터 반출 위협을 완화 하기 위해 다음과 같은 기술을 제공 합니다. 

- Azure Vm의 NSGs에 대 한 허용 및 거부 규칙의 조합을 사용 하 여 VM에서 액세스할 수 있는 지역을 제어 합니다. 
- 단일 데이터베이스 또는 탄력적 풀을 포함 하는 Azure SQL Database 서버를 사용 하는 경우 다음 옵션을 설정 합니다.
  - Azure 서비스를 해제할 수 있습니다.
  - VNet 방화벽 규칙을 설정 하 여 Azure VM을 포함 하는 서브넷의 트래픽만 허용 합니다.
  - [개인 링크](sql-database-private-endpoint-overview.md) 사용
- 관리 되는 인스턴스의 경우에는 기본적으로 개인 IP 액세스를 사용 하 여 rogue VM의 첫 번째 데이터 반출 문제를 해결 합니다. 서브넷에서 서브넷 위임 기능을 설정 하 여 관리 되는 인스턴스 서브넷에 대해 가장 제한적인 정책을 자동으로 설정 합니다.
- 악의적인 DBA의 관심사는 더 큰 노출 영역이 있고 네트워킹 요구 사항이 고객에 게 표시 되기 때문에 관리 되는 인스턴스에 더 노출 됩니다. 이에 대 한 가장 좋은 완화 방법은이 보안 가이드의 모든 사례를 적용 하 여 첫 번째 장소에서 Rogue DBA 시나리오를 방지 하는 것입니다 (데이터 exfiltration에 대해서는 아님). Always Encrypted은 중요 한 데이터를 암호화 하 고 DBA에 게 키를 액세스할 수 없도록 유지 하는 방법 중 하나입니다.

## <a name="security-aspects-of-business-continuity-and-availability"></a>비즈니스 연속성 및 가용성의 보안 측면

대부분의 보안 표준은 단일 실패 지점이 발생 하지 않도록 중복성 및 장애 조치 (failover) 기능을 구현 하 여 달성할 수 있는 운영 연속성 측면에서 데이터 가용성을 해결 합니다. 재해 시나리오의 경우 데이터 및 로그 파일의 백업을 유지 하는 것이 일반적인 방법입니다. 다음 섹션에서는 Azure에 기본 제공 되는 기능에 대 한 개략적인 개요를 제공 합니다. 또한 특정 요구 사항을 충족 하도록 구성할 수 있는 추가 옵션을 제공 합니다. 

- Azure는 기본적으로 고가용성 [및 Azure SQL Database](sql-database-high-availability.md) 을 제공 합니다. 

- 중요 비즈니스용 계층에는 장애 조치 (failover) 그룹, 다중 가용성 영역, 전체 및 차등 로그 백업, 기본적으로 사용 하도록 설정 된 지정 시간 복원 백업이 포함 됩니다.  
  - [고가용성 및 Azure SQL Database 영역 중복 구성](sql-database-high-availability.md#zone-redundant-configuration)
  - [자동화된 백업](sql-database-automated-backups.md)
  - [자동화 된 데이터베이스 백업을 사용 하 여 Azure SQL Database 복구-지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)

- 다양 한 Azure 지역에서 자동 장애 조치 (failover) 그룹과 같은 추가 비즈니스 연속성 기능을 구성할 수 있습니다 .이 [에 대 한 개요는 Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 보안 기능 개요를](sql-database-security-overview.md) 참조 하세요.