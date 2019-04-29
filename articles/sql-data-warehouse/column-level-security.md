---
title: Azure SQL Data Warehouse 열 수준 보안 | Microsoft Docs
description: 고객은 CLS(열 수준 보안)을 통해 사용자의 실행 컨텍스트 또는 해당 그룹 구성원에 따라 데이터베이스 테이블 열에 대한 액세스를 제어할 수 있습니다. CLS는 애플리케이션에서 보안의 설계 및 코딩을 간소화합니다. CLS를 사용하면 열 액세스에 대한 제한을 구현할 수 있습니다.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: aa91bd586e064239d0e05c754427947963c9ee3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082811"
---
# <a name="column-level-security"></a>열 수준 보안
고객은 CLS(열 수준 보안)을 통해 사용자의 실행 컨텍스트 또는 해당 그룹 구성원에 따라 데이터베이스 테이블 열에 대한 액세스를 제어할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS는 애플리케이션에서 보안의 설계 및 코딩을 간소화합니다. CLS를 사용하면 열 액세스에 대한 제한을 구현하여 중요한 데이터를 보호할 수 있습니다. 예를 들어 특정 사용자가 해당 부서와 관련된 테이블의 특정 열에만 액세스할 수 있도록 합니다. 액세스 제한 논리는 다른 애플리케이션 계층의 데이터가 아닌 데이터베이스 계층에 있습니다. 데이터베이스는 모든 계층에서 데이터 액세스를 시도할 때마다 액세스 제한을 적용합니다. 이렇게 제한하면 전체 보안 시스템의 노출 영역이 줄어들어 보안 시스템을 보다 안정적이고 강력하게 만들 수 있습니다. 또한 CLS를 사용하면 사용자에게 액세스 제한을 적용하기 위해 열을 필터링하는 뷰를 도입할 필요가 없습니다.

[GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL 문을 사용하여 CLS를 구현할 수 있습니다. 이 메커니즘을 사용하면 SQL 및 AAD(Azure Active Directory) 인증이 모두 지원됩니다.

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>구문

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>예
다음 예제는 ‘Membership’ 테이블의 ‘SSN’ 열에 액세스하지 못하도록 ‘TestUser’를 제한하는 방법을 보여 줍니다.

사회 보장 번호를 저장하는 데 사용되는 SSN 열이 포함된 ‘Membership’ 테이블을 만듭니다.

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

‘TestUser’는 중요한 데이터가 포함된 SSN 열을 제외한 모든 열에 액세스할 수 있습니다.

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

‘TestUser’로 실행된 쿼리는 SSN 열을 포함하는 경우 실패합니다.

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>사용 사례
현재 CLS가 사용되는 방법에 대한 몇 가지 예:
- 금융 서비스 회사는 계정 관리자만 고객 SSN(사회 보장 번호), 전화 번호 및 기타 PII(개인 식별 정보)에 액세스할 수 있도록 허용합니다.
- 의료 공급업체는 청구 부서의 구성원은 이 데이터를 볼 수 없는 반면, 의사와 간호사만 중요한 의료 기록에 액세스할 수 있도록 허용합니다.
