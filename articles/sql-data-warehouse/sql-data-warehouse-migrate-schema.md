---
title: SQL Data Warehouse로 스키마 마이그레이션| Microsoft Docs
description: 솔루션 개발을 위한 Azure SQL Data Warehouse로 스키마를 마이그레이션하기 위한 팁
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
origin.date: 04/17/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 4139ea776f6947eeacf4620c3676606d6535dd2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748155"
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>SQL Data Warehouse로 스키마 마이그레이션
SQL Data Warehouse에 SQL 스키마를 마이그레이션하기 위한 지침입니다. 

## <a name="plan-your-schema-migration"></a>스키마 마이그레이션 계획

마이그레이션을 계획할 경우 [테이블 개요][table overview]를 참조하여 통계, 배포, 분할 및 인덱싱과 같은 테이블 설계 고려 사항에 대해 알아봅니다.  또한 몇 가지 [지원되지 않는 테이블 기능][unsupported table features] 및 해당 해결 방법을 나열합니다.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>사용자 정의 스키마를 사용하여 데이터베이스 통합

기존 워크로드에는 둘 이상의 데이터베이스가 있습니다. 예를 들어 SQL Server 데이터 웨어하우스는 스테이징 데이터베이스, 데이터 웨어하우스 데이터베이스 및 일부 데이터 마트 데이터베이스를 포함할 수 있습니다. 이 토폴로지에서 각 데이터베이스는 별도 보안 정책을 사용하여 별도 워크로드로 실행됩니다.

반면, SQL Data Warehouse는 하나의 데이터베이스 내에서 전체 데이터 웨어하우스 워크로드를 실행합니다. 데이터베이스 간 조인은 허용되지 않습니다. 따라서 SQL Data Warehouse는 데이터 웨어하우스에서 사용된 모든 테이블을 하나의 데이터베이스 내에 저장할 것으로 예상합니다.

사용자 정의 스키마를 사용하여 하나의 데이터베이스에 기존 워크로드를 통합하는 것이 좋습니다. 예를 들어 [사용자 정의 스키마](sql-data-warehouse-develop-user-defined-schemas.md)를 참조하세요.

## <a name="use-compatible-data-types"></a>호환되는 데이터 형식 사용
SQL Data Warehouse와 호환되도록 데이터 형식을 수정합니다. 지원되는 데이터 형식 및 지원되지 않는 데이터 형식의 목록은 [데이터 형식][data types]을 참조하세요. 해당 항목은 지원되지 않는 형식에 대한 해결 방법을 제공합니다. 또한SQL Data Warehouse에서 지원되지 않는 기존 형식을 식별하는 쿼리를 제공합니다.

## <a name="minimize-row-size"></a>행 크기 최소화
최상의 성능을 위해 테이블의 행 길이를 최소화합니다. 행 길이가 짧으면 성능을 향상시키기 때문에 데이터에 작동하는 가장 작은 데이터 형식을 사용합니다. 

PolyBase는 테이블 행 너비를 1MB로 제한합니다.  PolyBase를 사용하여 SQL Data Warehouse로 데이터를 로드하려는 경우 최대 행 너비가 1MB 미만이 되도록 테이블을 업데이트합니다. 

## <a name="specify-the-distribution-option"></a>배포 옵션 지정
SQL Data Warehouse는 배포된 데이터베이스 시스템입니다. 각 테이블은 Compute 노드에 배포되거나 복제됩니다. 데이터를 배포하는 방법을 지정할 수 있는 테이블 옵션이 있습니다. 라운드 로빈, 복제 또는 해시 배포를 선택할 수 있습니다. 각각에 장점 및 단점이 있습니다. 배포 옵션을 지정하지 않은 경우 SQL Data Warehouse는 기본적으로 라운드 로빈을 사용합니다.

- 라운드 로빈이 기본값입니다. 사용하기 간단하고 데이터를 빠르게 로드하지만 조인에는 쿼리 성능을 저하시키는 데이터 이동이 필요합니다.
- 복제는 각 Compute 노드에 테이블의 복사본을 저장합니다. 복제된 테이블은 조인 및 집계에 데이터 이동을 필요로 하지 않기 때문에 적합합니다. 추가 저장소가 필요하므로 작은 테이블에 가장 적합합니다.
- 배포된 해시는 해시 함수를 통해 모든 노드에 행을 배포합니다. 배포된 해시 테이블은 큰 테이블에서 높은 쿼리 성능을 제공하도록 설계되었기 때문에 SQL Data Warehouse의 핵심입니다. 이 옵션을 사용하려면 데이터를 배포하는 경우 가장 적합한 열을 선택해야 합니다. 그러나 처음으로 가장 적합한 열을 선택하지 않더라도 다른 열에 데이터를 다시 쉽게 배포할 수 있습니다. 

각 테이블에 가장 적합한 배포 옵션을 선택하려면 [배포된 테이블](sql-data-warehouse-tables-distribute.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
SQL Data Warehouse로 데이터베이스 스키마를 성공적으로 마이그레이션한 후에 다음 문서 중 하나를 진행할 수 있습니다.

* [데이터 마이그레이션][Migrate your data]
* [코드 마이그레이션][Migrate your code]

SQL Data Warehouse 모범 사례에 대한 자세한 내용은 [모범 사례][best practices] 문서를 참조하세요.

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->

<!--Other Web references-->

<!--Update_Description: update meta properties, add new content about Migrate schemas to SQL Data Warehouse -->