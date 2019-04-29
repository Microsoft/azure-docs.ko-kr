---
title: Azure SQL Data Warehouse 릴리스 정보 2018년 4월 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: a9fae37acd653b05502737756baf463188ef631d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475017"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Azure SQL Data Warehouse의 새로운 기능 2018년 4월
Azure SQL Data Warehouse는 지속적으로 개선 사항을 수신합니다. 이 문서에서는 2018년 4월에 도입된 새로운 기능과 변경 사항에 대해 설명합니다.

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>전환 전에 파티션을 자르는 기능
고객은 종종 패턴으로 파티션 전환을 사용하여 `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` 구문을 통해 테이블의 메타데이터를 변경하여 하나의 테이블에서 다른 테이블로 데이터를 로드합니다. SQL Data Warehouse는 대상 파티션이 데이터를 포함하는 경우 파티션 전환을 지원하지 않습니다. 대상 파티션에 이미 데이터가 있는 경우 고객은 대상 파티션을 자른 다음, 전환을 수행해야 했습니다.

SQL Data Warehouse는 이제 단일 T-SQL 문에서 이 작업을 지원합니다.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
자세한 내용은 [ALTER 테이블](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)을 참조하세요.

## <a name="improved-query-compilation-performance"></a>향상된 쿼리 컴파일 성능
SQL Data Warehouse는 분산 쿼리의 쿼리 컴파일 단계를 개선하기 위해 변경 집합을 도입했습니다. 이러한 변경 내용은 전체 실행 런타임을 줄여 쿼리 컴파일 시간을 최대 **10x**로 개선합니다. 이러한 변경 내용은 다수의 개체가 있는 데이터 웨어하우스에서 더욱 분명합니다(테이블, 함수, 뷰, 프로시저).

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>DBCC 명령은 동시성 슬롯을 사용하지 않음(동작 변경 내용)
SQL Data Warehouse는 [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql)와 같은 T-SQL [DBCC 명령](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql)의 하위 집합을 지원합니다. 이러한 명령은 이전에 실행될 수 있는 사용자 로드/쿼리 수를 줄이는 [동시성 슬롯](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots)을 사용했습니다. `DBCC` 명령은 이제 전체 쿼리 실행 성능을 향상시키는 리소스 슬롯을 사용하지 않는 로컬 큐에서 실행됩니다.

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>과도한 리터럴에 대한 업데이트된 오류 메시지(동작 변경 내용)
SQL Data Warehouse는 이전에 쿼리에 너무 많은 리터럴이 포함되었던 경우 *대략적인* 수를 포함했습니다.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

오류 메시지는 리터럴 제한에 도달했음만을 나타내도록 업데이트되었습니다.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

자세한 내용은 최대 제한의 추가 세부 정보에 대한 [용량 제한](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits) 문서의 [쿼리](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) 섹션을 참조하세요.

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>제거된 SYS.PDW_DATABASE_MAPPINGS 보기(동작 변경 내용)
이 `sys.pdw_database_mappings` 보기는 SQL Data Warehouse에서 사용되지 않습니다. 이전에 이 보기의 SELECT는 결과를 반환하지 않았습니다. 보기가 제거되었습니다. 

## <a name="next-steps"></a>다음 단계
SQL Data Warehouse에 대한 내용을 파악했으므로 [SQL Data Warehouse 만들기][create a SQL Data Warehouse]에 대해 신속히 알아봅니다. Azure를 처음 사용하는 경우 새 용어를 발견하면 [Azure 용어집][Azure glossary]을 유용하게 사용할 수 있습니다. 또는 그 밖의 SQL Data Warehouse 리소스를 살펴봅니다.  

* [고객 성공 사례]
* [블로그]
* [기능 요청]
* [비디오]
* [고객 자문 팀 블로그]
* [Stack Overflow 포럼]
* [Twitter]


[블로그]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[고객 자문 팀 블로그]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[고객 성공 사례]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow 포럼]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[비디오]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
