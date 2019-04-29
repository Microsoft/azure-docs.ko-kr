---
title: Azure SQL Database 및 SQL Data Warehouse에 대한 액세스 권한 부여 | Microsoft Doc
description: Azure SQL Database 및 SQL Data Warehouse에 대한 액세스 권한을 부여하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 8cb044397cf439e97f3630b5c1c3f53fbf3f356d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61468400"
---
# <a name="azure-sql-database-and-sql-data-warehouse-access-control"></a>Azure SQL Database 및 SQL Data Warehouse 액세스 제어

보안을 제공하기 위해, Azure [SQL Database](sql-database-technical-overview.md) 및 [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)는 IP 주소로 연결을 제한하는 방화벽 규칙, 사용자가 해당 ID를 증명하도록 하는 인증 메커니즘 및 특정 작업 및 데이터에 대한 사용자를 제한하는 권한 부여 메커니즘을 사용하여 액세스를 제어합니다. 

> [!IMPORTANT]
> SQL Database 보안 기능에 대한 개요는 [SQL 보안 개요](sql-database-security-overview.md)를 참조하세요. 자습서는 [Azure SQL Database 보안](sql-database-security-tutorial.md)을 참조하세요. SQL Data Warehouse 보안 기능에 대한 개요는 [SQL Data Warehouse 보안 개요](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)를 참조하세요.

## <a name="firewall-and-firewall-rules"></a>방화벽 및 방화벽 규칙

Microsoft Azure SQL Database는 Azure 및 기타 인터넷 기반 애플리케이션의 관계형 데이터베이스 서비스를 제공합니다. 데이터를 보호하기 위해 방화벽은 권한이 있는 컴퓨터를 지정할 때까지 데이터베이스 서버에 대한 모든 액세스를 금지합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다. 자세한 내용은 [Azure SQL Database 방화벽 규칙 개요](sql-database-firewall-configure.md)를 참조하세요.

Azure SQL Database 서비스는 TCP 포트 1433을 통해서만 사용할 수 있습니다. 사용자의 컴퓨터에서 SQL Database에 액세스하려면 클라이언트 컴퓨터 방화벽이 TCP 포트 1433을 통해 나가는 TCP 통신을 허용해야 합니다. 다른 애플리케이션에 필요하지 않은 경우 TCP 포트 1433의 인바운드 연결을 차단합니다. 

연결 프로세스의 일부로 Azure 가상 머신에서 연결은 각 작업자 역할에 대한 고유한 다른 IP 주소 및 포트에 리디렉션됩니다. 포트 번호의 범위는 11000~11999입니다. TCP 포트에 대한 자세한 내용은 [ADO.NET 4.5 및 SQL Database2에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.

## <a name="authentication"></a>Authentication

SQL Database는 두 가지 인증 유형을 지원합니다.

- **SQL 인증**:

  이 인증 방법은 사용자 이름과 암호를 사용합니다. 데이터베이스의 SQL Database 서버를 만들 때 사용자 이름 및 암호를 사용하여 “서버 관리자” 로그인을 지정했습니다. 이러한 자격 증명을 사용하면 해당 서버의 모든 데이터베이스에 데이터베이스 소유자 또는 "dbo"로 인증할 수 있습니다. 
- **Azure Active Directory 인증**:

  이 인증 방법은 Azure Active Directory에서 관리하는 ID를 사용하며, 관리되는 도메인과 통합된 도메인에 대해 지원됩니다. [가능한 경우](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) Active Directory 인증(통합 보안)을 사용합니다. Azure Active Directory 인증을 사용하려는 경우 Azure AD 사용자 및 그룹을 허용하는 "Azure AD 관리자"라는 다른 서버 관리자를 만들어야 합니다. 이 관리자는 일반 서버 관리자가 할 수 있는 모든 작업을 수행할 수도 있습니다. Azure AD 관리자를 만들어 Azure Active Directory 인증을 활성화하는 방법에 대한 연습은 [Azure Active Directory 인증을 사용하여 SQL Database에 연결](sql-database-aad-authentication.md) 을 참조하세요.

데이터베이스 엔진은 30분 이상 유휴 상태로 있는 연결을 닫습니다. 연결을 사용하기 전에 다시 로그인해야 합니다. SQL Database에 대한 연결을 지속적으로 활성화하려면 적어도 10시간 마다 권한을 다시 부여받아야 합니다(데이터베이스 엔진에서 수행함). 데이터베이스 엔진은 전송된 원래 암호를 사용하여 권한을 다시 부여하려고 하며 사용자 입력이 필요하지 않습니다. 성능상의 이유로 SQL Database에 암호를 다시 설정할 경우 연결 풀링으로 인해 연결을 재설정하더라도 연결은 다시 인증되지 않습니다. 온-프레미스 SQL Server의 동작과 다릅니다. 연결이 처음에 권한을 부여받은 이후에 암호가 변경되었다면 연결을 종료해야 하고 새 암호를 사용하여 새로 연결합니다. `KILL DATABASE CONNECTION` 권한이 있는 사용자는 [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql) 명령을 사용하여 SQL Database에 연결을 명시적으로 종료할 수 있습니다.

사용자 계정을 마스터 데이터베이스에서 만들고 서버의 모든 데이터베이스에 대한 사용 권한을 부여할 수 있습니다. 또는 데이터베이스 자체(포함된 사용자라고 함)에서 만들 수 있습니다. 로그인 만들기 및 관리에 대한 자세한 내용은 [로그인 관리](sql-database-manage-logins.md)를 참조하세요. 이동성 및 확장성을 강화하려면 포함된 데이터베이스를 사용하세요. 포함된 사용자에 대한 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), [사용자 만들기(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 및 [포함된 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)를 참조하세요.

가장 좋은 방법은 애플리케이션이 전용 계정을 사용하여 인증하는 것입니다. 이 방법을 사용하면 애플리케이션에 부여되는 사용 권한을 제한하여 애플리케이션 코드가 SQL 삽입 공격에 취약한 경우 악의적인 활동의 위험을 줄일 수 있습니다. 응용 프로그램을 통해 데이터베이스에 직접 인증할 수 있는 [포함된 데이터베이스 사용자](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)를 만드는 것이 좋습니다. 

## <a name="authorization"></a>권한 부여

권한 부여는 사용자가 Azure SQL Database에서 수행할 수 있는 작업을 나타내며, 사용자 계정의 데이터베이스 [역할 멤버 자격](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)과 [개체 수준 사용 권한](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)으로 제어합니다. 사용자에게 필요한 최소한의 권한을 부여하는 것이 가장 좋습니다. 연결 중인 서버 관리자 계정은 데이터베이스 내에서 작업을 수행할 권한이 있는 db_owner의 구성원입니다. 스키마 업그레이드 및 기타 관리 작업을 배포하기 위해서는 이 계정을 저장합니다. 애플리케이션에서 해당 애플리케이션에 필요한 최소한의 권한이 있는 데이터베이스에 연결하려면 보다 제한된 사용 권한을 가진 "ApplicationUser" 계정을 사용합니다. 자세한 내용은 [로그인 관리](sql-database-manage-logins.md)를 참조하세요.

일반적으로 관리자만 `master` 데이터베이스에 대한 액세스가 필요합니다. 각 사용자 데이터베이스에 대한 일상적인 액세스는 각 데이터베이스에서 관리자가 아닌 포함된 데이터베이스 사용자를 통해서여야 합니다. 포함된 데이터베이스 사용자를 사용하는 경우 `master` 데이터베이스에서 로그인을 만들 필요가 없습니다. 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)를 참조하세요.

사용 권한을 제한 또는 확장시키는 데 사용할 수 있는 다음 기능을 잘 알고 있어야 합니다.

- [가장](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) 및 [모듈 서명](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)은 일시적으로 권한을 안전하게 상승시키는 데 사용할 수 있습니다.
- [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) 은 사용자가 액세스할 수 있는 행을 제한하는 데 사용할 수 있습니다.
- [데이터 마스킹](sql-database-dynamic-data-masking-get-started.md) 은 중요한 데이터의 노출을 제한하는 데 사용할 수 있습니다.
- [저장 프로시저](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 는 데이터베이스에서 수행할 수 있는 작업을 제한하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- SQL Database 보안 기능에 대한 개요는 [SQL 보안 개요](sql-database-security-overview.md)를 참조하세요.
- 방화벽 규칙에 대해 자세히 알아보려면 [방화벽 규칙](sql-database-firewall-configure.md)을 참조하세요.
- 사용자와 로그인에 대해 알아보려면 [로그인 관리](sql-database-manage-logins.md)를 참조하세요. 
- 사전 모니터링에 대한 설명은 [데이터베이스 감사](sql-database-auditing.md) 및 [SQL Database 위협 탐지](sql-database-threat-detection.md)를 참조하세요.
- 자습서는 [Azure SQL Database 보안](sql-database-security-tutorial.md)을 참조하세요.
