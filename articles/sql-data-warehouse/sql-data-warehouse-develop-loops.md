---
title: Azure SQL Data Warehouse의 T-SQL 루프 사용 | Microsoft Docs
description: 솔루션을 개발하기 위해 Azure SQL Data Warehouse에서 Transact-SQL 루프를 사용하여 커서를 대체하는 방법에 대한 팁입니다.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 5aa26aeb27d962e6e6289a754ef57b49158b68db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439199"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>SQL Data Warehouse의 T-SQL 루프 사용
솔루션을 개발하기 위해 Azure SQL Data Warehouse에서 Transact-SQL 루프를 사용하여 커서를 대체하는 방법에 대한 팁입니다.

## <a name="purpose-of-while-loops"></a>WHILE 루프의 목적

SQL Data Warehouse는 명령문 블록을 반복 실행하기 위한 [WHILE](/sql/t-sql/language-elements/while-transact-sql) 루프를 지원합니다. 이 WHILE 루프는 지정한 조건이 true이거나 코드가 BREAK 키워드를 사용하여 루프를 명시적으로 종료할 때까지 계속됩니다. 루프는 SQL 코드에 정의된 커서를 대체하는 데 유용합니다. 다행히 SQL 코드로 작성된 거의 모든 커서는 빠른 정방향 읽기 전용 변형만 존재합니다. 따라서 [WHILE] 루프는 커서를 대체하는 좋은 대안입니다.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>SQL Data Warehouse에서 커서 대체
커서를 대체하기 전에 먼저 세트 기반 작업을 사용하도록 이 커서를 재작성할 수 있는지를 확인해야 합니다. 대부분의 경우 다시 작성할 수 있으며 종종 가장 좋은 방법이기도 합니다. 집합 기반 작업은 흔히 행별 반복 접근 방식보다 더 빠르게 수행됩니다.

빠른 정방향 읽기 전용 커서는 루핑 구성으로 쉽게 대체할 수 있습니다. 다음은 간단한 예제입니다. 이 코드 예제는 데이터베이스의 모든 테이블에 대한 통계를 업데이트합니다. 루프의 테이블을 반복하여 각 명령이 순차적으로 실행됩니다.

첫째, 개별 문을 식별하는 데 사용되는 고유한 행 번호를 포함하는 임시 테이블을 만듭니다.

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

둘째, 루프를 수행하는 데 필요한 변수를 초기화합니다.

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

이제 문을 반복하여 한 번에 하나씩 실행합니다.

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

마지막으로 첫 번째 단계에서 만든 임시 테이블을 삭제합니다.

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.

