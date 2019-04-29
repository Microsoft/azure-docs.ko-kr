---
title: Azure의 데이터 웨어하우스 개발을 위한 리소스 | Microsoft Docs
description: SQL Data Warehouse에 대한 개발 개념, 디자인 결정, 권장 사항 및 코딩 기술입니다.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
origin.date: 08/29/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 8c04e0409faa3b63a8a2957284ac7aa96740ae03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60747832"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>SQL Data Warehouse에 대한 디자인 결정 및 코딩 기술
SQL Data Warehouse에 대한 주요 디자인 결정, 권장 사항 및 코딩 기술을 더 잘 이해하려면 다음 개발 문서를 살펴보세요.

## <a name="key-design-decisions"></a>주요 디자인 결정
다음 문서에서는 SQL Data Warehouse를 사용하여 분산된 데이터 웨어하우스를 개발하는 개념 및 디자인 결정 사항을 중점적으로 설명합니다.

* [연결][connections]
* [동시성][concurrency]
* [트랜잭션][transactions]
* [사용자 정의 스키마][user-defined schemas]
* [테이블 배포][table distribution]
* [테이블 인덱스][table indexes]
* [테이블 파티션][table partitions]
* [CTAS][CTAS]
* [통계][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>개발 권장 사항 및 코딩 기술
이러한 문서에는 SQL Data Warehouse 개발을 위한 구체적인 코딩 기술, 팁 및 권장 사항이 요약되어 있습니다.

* [저장 프로시저][stored procedures]
* [레이블][labels]
* [뷰][views]
* [임시 테이블][temporary tables]
* [동적 SQL][dynamic SQL]
* [반복][looping]
* [옵션으로 그룹화][group by options]
* [변수 할당][variable assignment]

## <a name="next-steps"></a>다음 단계
자세한 참조 정보는 [SQL Data Warehouse T-SQL 명령문](sql-data-warehouse-reference-tsql-statements.md)을 참조하세요.

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->