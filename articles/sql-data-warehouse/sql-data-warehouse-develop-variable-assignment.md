---
title: Azure SQL Data Warehouse의 변수 할당 | Microsoft Docs
description: 솔루션 개발을 위한 Azure SQL Data Warehouse의 T-SQL 변수 할당을 위한 팁
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 04/17/2017
ms.date: 04/01/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: ee97f7e5cda8b954fb697f73746e416d88d38c2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61480836"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse의 변수 할당

솔루션 개발을 위한 Azure SQL Data Warehouse의 T-SQL 변수 할당을 위한 팁

## <a name="setting-variables-with-declare"></a>DECLARE를 사용하여 변수 설정

SQL Data Warehouse의 변수는 `DECLARE` 문 또는 `SET` 문을 사용하여 설정됩니다. DECLARE를 사용한 변수 초기화는 SQL Data Warehouse의 변수 값을 설정하는 가장 유연한 방법 중 하나입니다.

```sql
DECLARE @v  int = 0
;
```

한 번에 둘 이상의 변수를 설정하려면 DECLARE를 사용할 수도 있습니다. SELECT 또는 UPDATE를 사용하여 다음 작업을 수행할 수 없습니다.

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

초기화하여 동일한 DECLARE 문에서 변수를 사용할 수 없습니다. 지점을 설명하기 위해 다음 예에서는 @p1이 동일한 DECLARE 문에서 시작되고 사용되기 때문에 허용되지 **않습니다**. 다음 예제에서는 오류가 발생합니다.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>SET을 사용하여 값 설정

SET은 단일 변수를 설정하기 위한 일반적인 방법입니다.

다음 명령문은 SET을 사용하여 변수를 설정하는 모든 유효한 방법입니다.

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

SET을 사용하여 한 번에 하나의 변수만 설정할 수 있습니다. 그러나 복합 연산자는 허용됩니다.

## <a name="limitations"></a>제한 사항

변수 할당에 대 한 업데이트를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
