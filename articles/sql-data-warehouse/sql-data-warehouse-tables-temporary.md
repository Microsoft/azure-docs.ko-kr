---
title: SQL Data Warehouse의 임시 테이블 | Microsoft Docs
description: 임시 테이블을 사용하기 위한 필수 지침을 제공하고 세션 수준 임시 테이블의 원리를 강조해서 설명합니다.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 04/17/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 23a62e28700ad5fd733040c43ea0eec225fd286f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474898"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>SQL Data Warehouse의 임시 테이블
이 문서에서는 임시 테이블을 사용하기 위한 필수 지침을 제공하고 세션 수준 임시 테이블의 원리를 강조해서 설명합니다. 이 문서의 정보를 사용하여 코드를 모듈화할 수 있으므로 코드의 재사용 가능성 및 유지 관리 용이성이 개선됩니다.

## <a name="what-are-temporary-tables"></a>임시 테이블이란?
특히 중간 결과가 일시적인 변환 중 데이터를 처리할 때 임시 테이블은 유용합니다. 임시 테이블은 Microsoft Azure SQL Data Warehouse의 세션 수준에서 존재합니다.  이러한 임시 테이블은 생성된 세션에서만 보이고, 해당 세션이 로그오프되면 자동으로 삭제됩니다.  임시 테이블은 결과가 원격 저장소 대신 로컬로 기록되기 때문에 성능상의 이점을 제공합니다.

## <a name="create-a-temporary-table"></a>임시 테이블 만들기
임시 테이블은 테이블 이름 앞에 `#`을 붙여 만듭니다.  예를 들면 다음과 같습니다.

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

정확히 동일한 접근 방식을 사용하여 `CTAS` 를 통해 임시 테이블을 만들 수도 있습니다.

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

> [!NOTE]
> `CTAS`는(은) 강력한 명령이며 트랜잭션 로그 공간을 사용한다는 점에서 효율적이라는 추가적인 이점이 있습니다. 
> 
> 

## <a name="dropping-temporary-tables"></a>임시 테이블 삭제
새 세션이 만들어지면 임시 테이블이 존재하지 않습니다.  그러나 동일한 이름의 임시 테이블을 만드는 동일한 저장 프로시저를 호출하는 경우 `CREATE TABLE` 문이 정상적으로 수행되도록 하려면 다음 예제와 같이 `DROP`을 사용해서 단순한 사전 존재 여부 확인을 수행할 수 있습니다.

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

코딩 일관성을 유지하려면 테이블 및 임시 테이블 모두에 대해 이 패턴을 사용하는 것이 좋습니다.  또한 코드에서 사용을 완료한 경우 `DROP TABLE` 을 사용하여 임시 테이블을 제거하는 것이 좋습니다.  저장 프로시저 개발에서는 프로시저 끝에 삭제 명령이 번들로 포함되어 있는지 확인하여 이러한 개체가 정리되는지 알 수 있습니다.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>코드 모듈화
임시 테이블을 사용자 세션의 어디에서나 볼 수 있으므로 애플리케이션 코드를 모듈화하는 데 이용될 수 있습니다.  예를 들어 다음 저장된 프로시저는 통계 이름으로 데이터베이스의 모든 통계를 업데이트하는 DDL을 생성합니다.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

이 단계에서 발생 하는 유일한 작업은 #stats_ddl DDL 문으로 임시 테이블을 생성 하는 저장된 프로시저를 만드는 것입니다.  이 저장 프로시저는 세션 내에서 두 번 이상 실행하는 경우 실패하지 않도록 #stats_ddl(이미 있는 경우)을 삭제합니다.  그러나 저장 프로시저 끝에는 `DROP TABLE` 이 없으므로 저장 프로시저가 완료되면 저장 프로시저 외부에서 읽을 수 있도록 만든 테이블이 그대로 남아 있습니다.  다른 SQL Server 데이터베이스와 달리, SQL Data Warehouse에서 임시 테이블을 만든 프로시저의 외부에서 임시 테이블을 사용할 수 있습니다.  세션 내의 **어디에서나** SQL Data Warehouse 임시 테이블을 사용할 수 있습니다. 다음 예제와 같이 모듈식 및 관리 가능 코드가 더 많아질 수 있습니다.

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>임시 테이블 제한 사항
임시 테이블을 구현하는 경우 SQL Data Warehouse는 두 가지 제한 사항을 적용합니다.  현재 세션 범위의 임시 테이블만 지원됩니다.  전역 임시 테이블은 지원되지 않습니다.  또한 임시 테이블에서 뷰를 만들 수 없습니다.  임시 테이블은 해시 또는 라운드 로빈 배포를 사용 하 여만 만들 수 있습니다.  복제 된 임시 테이블 배포 지원 되지 않습니다. 

## <a name="next-steps"></a>다음 단계
테이블 개발에 대해 자세히 알아보려면 [테이블 개요](sql-data-warehouse-tables-overview.md)를 참조하세요.

