---
title: Azure Database for MySQL에서 쿼리 성능 문제를 해결하는 방법
description: 이 문서에서는 EXPLAIN을 사용하여 Azure Database for MySQL에서 쿼리 성능 문제를 해결하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 819e2393619766d46385cdd6fe550fff1e1a7631
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720215"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>EXPLAIN을 사용하여 Azure Database for MySQL에서 쿼리 성능을 프로파일링하는 방법
**EXPLAIN**은 쿼리를 최적화하는 편리한 도구입니다. EXPLAIN 문은 SQL 문이 어떻게 실행되는지에 대한 정보를 얻는 데 사용할 수 있습니다. 다음 출력은 EXPLAIN 문의 실행 예제입니다.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

이 예제에서 볼 수 있듯이 *key*의 값은 NULL입니다. 이 출력은 MySQL이 쿼리에 최적화된 인덱스를 찾을 수 없어서 전체 테이블 검색을 수행한다는 의미입니다. **ID** 열에 인덱스를 추가하여 쿼리를 최적화해 보겠습니다.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

새 EXPLAIN을 보면 MySQL에서 이제 색인을 사용하여 행의 수를 1로 제한했기 때문에 검색 시간이 상당히 단축되었습니다.
 
## <a name="covering-index"></a>커버링 인덱스
커버링 인덱스는 데이터 테이블에서 값 검색을 줄이기 위해 인덱스의 모든 쿼리 열로 구성됩니다. 다음은 **GROUP BY** 문의 일러스트레이션입니다.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

출력에서 볼 수 있듯이 적절한 인덱스가 없기 때문에 MySQL에 인덱스가 사용되지 않았습니다. 또한 *Using temporary; Using file sort*를 볼 수 있습니다. 이것은 MySQL에서 **GROUP BY** 절을 충족시키기 위해 임시 테이블을 생성한다는 의미입니다.
 
**c2** 열에만 인덱스를 만들면 아무런 차이가 없어서 MySQL에서 임시 테이블을 여전히 만들어야 합니다.

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

이런 경우 **c1**과 **c2** 모두에 **covered 인덱스**를 만들고 나중에 데이터 조회가 필요 없도록 **c2**"의 값을 인덱스에 바로 추가할 수 있습니다.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

위의 EXPLAIN에서 보면 MySQL은 이제 covered 인덱스를 사용하기 때문에 임시 테이블을 만들지 않아도 됩니다. 

## <a name="combined-index"></a>결합된 인덱스
결합된 인덱스는 여러 열의 값으로 구성되며 인덱싱된 열의 값을 연결하여 정렬되는 행의 배열로 간주할 수 있습니다. 이 메서드는 **GROUP BY** 문에 유용할 수 있습니다.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MySQL은 상당히 느린 *파일 정렬* 작업을 수행합니다. 많은 행을 정렬해야 하는 경우에는 특히 느립니다. 이 쿼리를 최적화하려면, 정렬 중인 두 열 모두에 결합된 인덱스를 만듭니다.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

이제 EXPLAIN을 보면 인덱스가 이미 정렬되어 있기 때문에 MySQL에서 결합된 인덱스를 사용하여 추가 정렬을 피할 수 있습니다.
 
## <a name="conclusion"></a>결론
 
EXPLAIN과 다른 유형의 인덱스를 사용하면 성능이 크게 향상될 수 있습니다. 테이블에 인덱스가 있다고 해서 MySQL이 해당 인덱스를 쿼리에 반드시 사용할 수 있는 것은 아닙니다. 항상 EXPLAIN을 사용하여 가정을 검증하고 인덱스를 사용하여 쿼리를 최적화하십시오.


## <a name="next-steps"></a>다음 단계
- 가장 궁금한 질문에 대한 동료의 답변을 찾아보거나 새로운 질문/답변을 게시하려면 [MSDN 포럼](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) 또는 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)를 참조하세요.
