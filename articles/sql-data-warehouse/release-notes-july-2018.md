---
title: Azure SQL Data Warehouse 릴리스 정보 2018년 7월 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 123198b21122a23d81794db0a5ca2051b15ee2e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476122"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Azure SQL Data Warehouse의 새로운 기능 2018년 7월
Azure SQL Data Warehouse는 지속적으로 개선 사항을 수신합니다. 이 문서에서는 2018년 7월에 도입된 새로운 기능과 변경 사항에 대해 설명합니다.

## <a name="lightning-fast-query-performance"></a>매우 빠른 쿼리 성능
[Azure SQL Data Warehouse](https://aka.ms/sqldw)는 셔플 작업을 개선하는 Instant Data Access의 도입으로 새로운 성능 벤치 마크를 설정합니다. Instant Data Access는 SQL Server 원시 데이터 작업에 직접 SQL Server를 사용하여 데이터 이동 작업의 오버헤드를 줄입니다. 데이터 이동을 위해 SQL Server 엔진이 직접 통합됨에 따라 SQL Data Warehouse는 이제 널리 인정받고 있는 업계 표준인 [TPC Benchmark™ H(TPC-H)](http://www.tpc.org/tpch/)에서 파생된 워크로드를 사용하면서 **Amazon Redshift보다 67% 더 빨라졌습니다**.

![Azure SQL Data Warehouse는 Amazon Redshift보다 빠르고 저렴합니다.](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>출처: [Gigaom Research Analyst Report: Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

[Gigaom Research](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) 보고서에는 런타임 성능 외에 특정 워크로드의 USD 비용을 수치화한 가격 대 성능비 측정값도 나와 있습니다. SQL Data Warehouse는 30TB 워크로드에서 Redshift보다 **23% 이상의 비용**이 들었습니다. 컴퓨팅 성능을 유연하게 확장하고 워크로드를 일시 중지했다가 다시 시작하는 SQL Data Warehouse의 기능 덕분에 고객은 서비스를 사용할 때만 요금을 지불하여 비용을 훨씬 더 절감할 수 있습니다.
![Azure SQL Data Warehouse는 Amazon Redshift보다 빠르고 저렴합니다.](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>출처: [Gigaom Research Analyst Report: Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

### <a name="query-concurrency"></a>쿼리 동시성
또한 SQL Data Warehouse는 조직 전체에서 데이터에 액세스할 수 있게 보장합니다. Microsoft는 더 많은 사용자가 동일한 데이터베이스를 쿼리하고 다른 요청으로 인해 차단되지 않도록 128개의 동시 쿼리를 지원할 수 있게 서비스를 개선했습니다. 이에 비해 Amazon Redshift는 최대 동시 쿼리를 50개로 제한하여, 조직 내 데이터 액세스를 제한합니다.

SQL Data Warehouse는 스토리지와 계산이 분리된 고유한 아키텍처를 기반으로 가격 상승 없이 이러한 쿼리 성능 및 쿼리 동시성 향상을 제공합니다.

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>지역 및 서버 간 복원을 위한 보다 자세한 세분성
이제 24시간마다 진행되는 지역 중복 백업을 선택하는 대신, 임의 복원 지점을 사용하여 지역 및 서버 간에 복원을 수행할 수 있습니다. 지역 및 서버 간 복원은 사용자 정의 또는 자동 복원 지점 둘 다에서 지원되며, 추가 데이터 보호를 위한 보다 자세한 세분성을 지원합니다. 사용 가능한 복원 지점이 더 많이 있는 경우 지역 간에 복원을 수행할 때 데이터 웨어하우스의 논리적 일관성이 보장될 수 있습니다.

![복원 명령 - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![복원 옵션 - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

자세한 내용은 [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)(가속화되고 유연한 복원 지점) 블로그 게시물을 참조하세요.

## <a name="20-minute-restorations"></a>20분 복원
이제 SQL Data Warehouse는 데이터베이스 크기에 관계 없이, 모든 데이터 웨어하우스가 같은 지역 내에서 **20분 이내**에 복원될 것으로 제안합니다. 같은 논리적 서버로 복원되든, 같은 지역의 다른 논리적 서버로 복원되든, 이 복원 시간이 적용됩니다. 또한 복원 지점을 만드는 데 걸리는 시간을 줄이기 위해 스냅숏 프로세스가 개선되었습니다. 더 낮은 성능 수준(더 낮은 DWU 설정)에서는 스냅숏 생성 시간이 *2배 감소*됩니다.

자세한 내용은 [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)(가속화되고 유연한 복원 지점) 블로그 게시물을 참조하세요.

## <a name="custom-restoration-configurations"></a>사용자 지정 복원 구성
이제 Azure Portal에서 복원하는 경우 성능 수준(DWU)을 변경할 수 있습니다. 업그레이드된 Gen2 데이터 웨어하우스로도 복원할 수 있습니다. 이제 Gen 2 인스턴스로 복원하여 [Gen1 데이터 웨어하우스로 업그레이드](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation)하기 전에 Gen2의 영향을 평가할 수 있습니다.

![사용자 지정 복원 구성 - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
[sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) 저장 프로시저는 Transact-SQL 일괄 처리의 매개 변수에 대한 메타데이터를 얻기 위해 도구에서 사용됩니다. 이 프로시저는 해당 매개 변수에 대한 추론된 형식 정보를 사용하여 일괄 처리의 각 매개 변수에 대해 하나의 행을 반환합니다. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

결과 집합에는 `@id` 매개 변수(부분 결과 표시)에 대한 메타데이터가 포함됩니다.
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
The [sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) 저장 프로시저는 기본 개체 변경으로 인해 기본 메타데이터가 만료된 경우 데이터베이스 개체의 메타데이터를 업데이트합니다. 뷰의 기본 테이블이 변경된 후 뷰가 다시 생성되지 않은 경우에 이러한 작업이 수행됩니다. 따라서 사용자는 종속 개체를 삭제하고 다시 만드는 단계를 생략할 수 있습니다.

아래 예제에서는 기본 테이블이 변경되어 만료된 뷰를 보여 줍니다. 첫 번째 열 변경(1을 Mollie로)의 경우 데이터는 올바르지만 열 이름이 유효하지 않고, 두 번째 열은 없다는 것을 알 수 있습니다. 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

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
