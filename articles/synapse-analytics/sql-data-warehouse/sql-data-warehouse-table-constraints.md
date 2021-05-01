---
title: 기본, 외래 및 고유 키
description: Azure Synapse Analytics에서 전용 SQL 풀을 사용하는 테이블 수준 조건 지원
services: synapse-analytics
author: mstehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: emtehran
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 88b63ce30000340a70811e9f623e4273ccbb272a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117285"
---
# <a name="primary-key-foreign-key-and-unique-key-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀을 사용하는 기본 키, 외래 키 및 고유 키

기본 키, 외래 키 및 고유 키를 포함하여 전용 SQL 풀의 테이블 제약 조건에 대해 알아봅니다.

## <a name="table-constraints"></a>테이블 제약 조건

전용 SQL 풀은 다음 테이블 제약 조건을 지원합니다. 
- PRIMARY KEY는 NONCLUSTERED와 NOT ENFORCED를 모두 사용하는 경우에만 지원됩니다.    
- UNIQUE 제약 조건은 NOT ENFORCED를 사용하는 경우에만 지원됩니다.

구문의 경우 [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) 및 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)을 확인합니다. 

FOREIGN KEY 제약 조건은 전용 SQL 풀에서 지원되지 않습니다.  


## <a name="remarks"></a>설명

기본 키 및/또는 고유 키를 사용하면 전용 SQL 풀 엔진에서 쿼리에 대해 최적의 실행 계획을 생성할 수 있습니다.  기본 키 열 또는 고유 제약 조건 열의 모든 값은 고유해야 합니다.

전용 SQL 풀에서 비공개 키 또는 고유 제약 조건이 있는 테이블을 만든 후에는 사용자가 해당 열의 모든 값이 고유한지 확인해야 합니다.  위반으로 인해 쿼리에서 부정확한 결과가 반환될 수 있습니다.  이 예에서는 기본 키 또는 고유 제약 조건 열에 중복 값이 포함된 경우 쿼리에서 부정확한 결과가 반환될 수 있는 방법을 보여 줍니다.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>예

기본 키를 사용하여 전용 SQL 풀 테이블을 만듭니다. 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```

고유 제한 조건을 사용하여 전용 SQL 풀 테이블을 만듭니다.

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>다음 단계

전용 SQL 풀에 대한 테이블을 만든 후 다음 단계는 테이블에 데이터를 로드하는 것입니다. 로드에 대한 자습서는 [전용 SQL 풀에 데이터 로드](load-data-wideworldimportersdw.md)를 참조하세요.