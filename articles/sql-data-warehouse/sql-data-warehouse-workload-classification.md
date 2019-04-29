---
title: SQL Data Warehouse 분류 | Microsoft Docs
description: 분류를 사용 하 여 동시성, 중요성, 관리 및 Azure SQL Data Warehouse의 쿼리에 대 한 리소스를 계산 하는 지침입니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 888a64de29178834fc47199a033eb6bc62858e57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474830"
---
# <a name="sql-data-warehouse-workload-classification-preview"></a>SQL Data Warehouse 작업 분류 (미리 보기)

이 문서는 들어오는 요청에 리소스 클래스와 중요도 할당 하는 SQL Data Warehouse 워크 로드 분류 프로세스를 설명 합니다.

> [!Note]
> 워크로드 분류는 SQL Data Warehouse Gen2에서 미리 보기로 제공됩니다. 워크로드 관리 분류 및 중요도 미리 보기는 2019년 4월 9일 이후 릴리스를 사용한 빌드를 위한 것입니다.  사용자는 워크로드 관리 테스트를 위해 이 날짜 이전에는 빌드를 사용하지 않는 것이 좋습니다.  자신의 빌드에서 워크로드 관리가 가능한지 알아보려면 SQL Data Warehouse 인스턴스에 연결된 경우 @@version을 선택하여 실행합니다.

## <a name="classification"></a>분류

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

워크 로드 관리 분류 작업 정책 할당을 통해 요청을 적용할 수 있습니다 [리소스 클래스](resource-classes-for-workload-management.md#what-are-resource-classes) 하 고 [중요도](sql-data-warehouse-workload-importance.md)합니다.

데이터 웨어하우징 워크 로드를 분류 하는 방법은 여러 가지를 하는 동안 로드 및 쿼리를 간단 하 고 가장 일반적인 분류는입니다. Insert, update 및 delete 문을 사용 하 여 데이터를 로드 합니다.  Select를 사용 하 여 데이터를 쿼리할 수 있습니다. 데이터 웨어하우징 솔루션은 더 높은 리소스 클래스에 더 많은 리소스를 할당 하는 등의 로드 작업에 대 한 워크 로드 정책을 경우가 많습니다. 작업 부하에 비해 낮은 중요도 같은 쿼리는 다양 한 워크 로드 정책을 적용할 수 없습니다.

로드 및 쿼리 워크 로드를 subclassify 수도 있습니다. Subclassification 워크 로드에 대 한 제어를 제공합니다. 예를 들어 큐브의 새로 고침, 대시보드 쿼리나 임시 쿼리 워크 로드 구성할 수 있습니다. 각각 다른 리소스 클래스 또는 중요도 설정으로 이러한 쿼리 워크 로드를 분류할 수 있습니다. Subclassification에서 부하를 활용할 수도 있습니다. 큰 변환 더 큰 리소스 클래스를 할당할 수 있습니다. 중요도 더 높고 키 판매 데이터를 날씨 데이터 전에 로더 또는 소셜 데이터 피드를 사용할 수 있습니다.

모든 문은 리소스가 필요 하지 않거나 실행에 영향을 중요 한 역할을 할 것으로 분류 됩니다.  BEGIN, COMMIT 및 ROLLBACK TRANSACTION 문을 분류 되지 않는 DBCC 명령이 있습니다.

## <a name="classification-process"></a>분류 프로세스

SQL Data Warehouse의 분류에 사용 하 여 할당 된 리소스 클래스가 해당 하는 역할에 사용자를 할당 하 여 현재 이루어집니다 [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)합니다. 리소스 클래스에는 로그인 이외의 요청 특징을 결정 하는 기능이이 기능을 사용 하 여 제한 됩니다. 분류에 대 한 다양 한 메서드를 사용 하 여 출시 되었습니다 합니다 [워크 로드 분류자 만들기](/sql/t-sql/statements/create-workload-classifier-transact-sql) 구문입니다.  이 구문을 사용 하 여 SQL Data Warehouse 사용자가 중요도 및 리소스 클래스를 요청에 할당할 수 있습니다.  

> [!NOTE]
> 분류 요청 별로에서 평가 됩니다. 단일 세션에서 여러 요청을 다른 방식으로 분류할 수 있습니다.

## <a name="classification-precedence"></a>분류 우선 순위

분류 프로세스의 일환으로, 우선 순위는 리소스 클래스 할당을 확인 하는 위치입니다. 데이터베이스 사용자를 기반으로 하는 분류 역할 멤버 자격에 우선 합니다. 분류자를 만든 경우 매핑되는 UserA 데이터베이스 사용자를 mediumrc 리소스 클래스입니다. 그런 다음 largerc 리소스 클래스에는 UserA 이어서 멤버인 RoleA 데이터베이스 역할을 매핑하십시오. 데이터베이스 사용자를 mediumrc 리소스 클래스에 매핑되는 분류자는 largerc 리소스 클래스에 RoleA 데이터베이스 역할에 매핑하는 분류자를 통해 우선을 적용 됩니다.

사용자 할당 또는 여러 분류자에서 일치 하는 다양 한 리소스 클래스를 사용 하 여 여러 역할의 멤버인 경우 사용자는 가장 높은 리소스 클래스 할당을 지정 됩니다.  이 동작은 기존 리소스 클래스 할당 동작과 일치 합니다.

## <a name="system-classifiers"></a>시스템 분류자

작업 분류 시스템 워크 로드 분류자에 있습니다. 시스템 분류자 중요도 사용 하 여 리소스 클래스 리소스 할당을 기존 리소스 클래스 역할 멤버 자격을 매핑합니다. 시스템 분류자를 삭제할 수 없습니다. 시스템 분류자를 보려면 실행 수는 쿼리 아래:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>리소스 클래스 할당 분류자를 사용 하 여 혼합

시스템 분류자를 만든 사용자를 대신해 작업 분류로 마이그레이션하도록 편리한 경로 제공 합니다. 리소스 클래스 역할 매핑을 사용 하 여 분류 우선 순위를 사용 하 여, 중요도 사용 하 여 새 분류자를 만들려면 먼저 오 분류 될 수 있습니다.

다음과 같은 시나리오를 고려해 보세요.

기존 데이터 웨어하우스 •An DBAUser largerc 리소스 클래스 역할에 할당 된 데이터베이스 사용자를 있습니다. 리소스 클래스 할당 sp_addrolemember를 사용 하 여 수행 되었습니다.
• 매핑된 데이터 웨어하우스 워크 로드 관리를 사용 하 여 이제 업데이트 됩니다.
• 하려면 새 분류 구문을 (임 DBAUser 소속) DBARole를 mediumrc 및 높은 중요도로 매핑하면에 만든 분류기가 데이터베이스 역할을 테스트 합니다.
•When DBAUser 로그인 하 고 쿼리를 실행, 쿼리 largerc에 할당 됩니다. 사용자 역할 멤버 자격을 보다 우선 순위가 높습니다 때문에.

문제 해결 오 분류를 간소화 하려면 작업 분류자를 만들면 리소스 클래스 역할 매핑을 제거 하는 것이 좋습니다.  아래 코드는 기존 리소스 클래스의 역할 멤버 자격을 반환합니다.  실행할 [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) 각 멤버 이름은 해당 리소스 클래스에서 반환 합니다.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>다음 단계

SQL Data Warehouse 작업 분류 및 중요도 대 한 자세한 내용은 참조 하세요. [워크 로드 분류자를 만듭니다](quickstart-create-a-workload-classifier-tsql.md) 하 고 [SQL Data Warehouse 중요도](sql-data-warehouse-workload-importance.md)합니다. 쿼리 및 할당된 중요도를 보려면 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)를 참조하세요.
