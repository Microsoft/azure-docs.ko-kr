---
title: Azure SQL Data Warehouse에 대한 인증 | Microsoft Docs
description: AAD(Azure Active Directory) 또는 SQL Server 인증을 사용하여 Azure SQL Data Warehouse에서 인증을 받는 방법을 알아봅니다.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a3bed9df5b62ce7f2f3df7046357dc4f2458575c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475034"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 대한 인증
AAD(Azure Active Directory) 또는 SQL Server 인증을 사용하여 Azure SQL Data Warehouse에서 인증을 받는 방법을 알아봅니다.

SQL Data Warehouse에 연결하려면 인증 목적으로 보안 자격 증명을 전달해야 합니다. 연결을 설정할 때 특정 연결 설정이 쿼리 세션을 설정하는 일부로 구성됩니다.  

보안에 대한 자세한 내용과 데이터 웨어하우스에 대한 연결을 설정하는 방법은 [SQL Data Warehouse에서 데이터베이스 보호][Secure a database in SQL Data Warehouse]를 참조하세요.

## <a name="sql-authentication"></a>SQL 인증
SQL Data Warehouse에 연결하려면 다음 정보를 제공해야 합니다.

* 정규화된 서버 이름
* SQL 인증 지정
* 사용자 이름
* 암호
* 기본 데이터베이스(옵션)

기본적으로 사용자의 연결은 사용자의 사용자 데이터베이스가 아닌 *master* 데이터베이스에 연결합니다. 사용자의 사용자 데이터베이스에 연결하려면 다음과 같은 두 가지 작업 중 하나를 수행하도록 선택할 수 있습니다.

* SSDT, SSMS 또는 애플리케이션 연결 문자열에서 SQL Server 개체 탐색기에 서버를 등록할 때 기본 데이터베이스를 지정합니다. 예를 들어 ODBC 연결에 대해 InitialCatalog 매개 변수를 포함합니다.
* SSDT에서 세션을 만들기 전에 사용자 데이터베이스를 강조 표시합니다.

> [!NOTE]
> 연결을 위한 데이터베이스 변경의 경우 Transact-SQL 문 **USE MyDatabase;** 는 지원되지 않습니다. SSDT를 사용하여 SQL Data Warehouse에 연결하는 지침은 [Visual Studio로 쿼리][Query with Visual Studio] 문서를 참조하세요.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>AAD(Azure Active Directory) 인증
[Azure Active Directory][What is Azure Active Directory] 인증은 Azure AD(Azure Active Directory)의 ID를 사용하여 Microsoft Azure SQL Data Warehouse에 연결하는 메커니즘입니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 하나의 중앙 위치에서 관리할 수 있습니다. 중앙 ID 관리는 SQL Data Warehouse 사용자 관리를 위한 단일 위치를 제공하며 권한 관리를 간소화합니다. 

### <a name="benefits"></a>이점
Azure Active Directory의 이점은 다음과 같습니다.

* SQL Server 인증에 대한 대안을 제공합니다.
* 데이터베이스 서버 전체에서 사용자 ID의 확산을 중지합니다.
* 한 위치에서의 암호 회전이 가능합니다.
* 외부(AAD) 그룹을 사용하여 데이터베이스 사용 권한을 관리할 수 있습니다.
* Windows 통합 인증 또는 Azure Active Directory에서 지원하는 기타 인증을 사용하여 암호 저장을 제거할 수 있습니다.
* 포함된 데이터베이스 사용자를 통해 데이터베이스 수준에서 ID를 인증합니다.
* SQL Data Warehouse에 연결되는 애플리케이션에 대한 토큰 기반 인증을 지원합니다.
* 비롯 한 다양 한 도구에 대 한 Active Directory 유니버설 인증을 통해 다단계 인증 지원 [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) 하 고 [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json)합니다.

> [!NOTE]
> Azure Active Directory는 비교적 새로운 기능으로, 몇 가지 제한 사항이 있습니다. Azure Active Directory가 사용자 환경에 적합한지 확인하려면 [Azure AD 기능 및 제한 사항][Azure AD features and limitations], 특히 추가 고려 사항을 참조하세요.
> 
> 

### <a name="configuration-steps"></a>구성 단계
다음 단계에 따라 Azure Active Directory 인증을 구성합니다.

1. Azure Active Directory 만들기 및 채우기
2. 선택 사항: 현재 Azure 구독과 연결된 Active Directory 연결 또는 변경
3. Azure SQL Data Warehouse에 대한 Azure Active Directory 관리자 만들기
4. 클라이언트 컴퓨터 구성
5. Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자 만들기
6. Azure AD ID를 사용하여 데이터 웨어하우스에 연결합니다.

현재 Azure Active Directory 사용자는 SSDT 개체 탐색기에 표시되지 않습니다. 해결 방법으로 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)에서 사용자를 봅니다.

### <a name="find-the-details"></a>세부 정보 찾기
* Azure Active Directory 인증을 구성하고 사용하는 단계는 Azure SQL Database 및 Azure SQL Data Warehouse의 경우와 거의 동일합니다. [Azure Active Directory 인증을 사용하여 SQL Database 또는 SQL Data Warehouse 연결](../sql-database/sql-database-aad-authentication.md)항목의 자세한 단계를 따릅니다.
* 사용자 지정 데이터베이스 역할을 만들고 역할에 사용자를 추가 합니다. 그런 다음 역할에 세부적인 권한을 부여합니다. 자세한 내용은 [데이터베이스 엔진 권한 시작](https://msdn.microsoft.com/library/mt667986.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Visual Studio 및 다른 애플리케이션으로 데이터 웨어하우스 쿼리를 시작하려면 [Visual Studio를 사용하여 쿼리][Query with Visual Studio]를 참조하세요.

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
