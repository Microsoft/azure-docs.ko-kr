---
title: Azure SQL Data Warehouse의 저장 프로시저 사용 | Microsoft Docs
description: 솔루션 개발을 위한 Azure SQL Data Warehouse의 저장 프로시저 구현을 위한 팁
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/02/2019
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8a53a63b7425935e117d7af951717999bc9340b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439714"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>SQL Data Warehouse의 저장 프로시저 사용
솔루션 개발을 위한 Azure SQL Data Warehouse의 저장 프로시저 구현을 위한 팁

## <a name="what-to-expect"></a>예상 프로그램

SQL Data Warehouse는 SQL Server에서 사용되는 여러 T-SQL 기능을 지원합니다. 무엇보다도 솔루션의 성능을 최대화하기 위해 사용할 수 있는 특정 기능 확장 사항이 있습니다.

그러나 SQL Data Warehouse의 크기와 성능을 유지하기 위해, 동작의 차이가 있는 일부 기능 및 지원되지 않는 일부 다른 기능이 있습니다.


## <a name="introducing-stored-procedures"></a>저장된 프로시저 소개
저장된 프로시저는 SQL 코드를 캡슐화하여 데이터 웨어하우스의 데이터에 가까이 저장하는 좋은 방법입니다. 관리 가능한 단위로 코드를 캡슐화하여 저장 프로시저를 사용하면 개발자가 솔루션을 모듈화하여 코드의 더 큰 재유용성을 용이하게 합니다. 각 저장된 프로시저로 매개 변수를 더 유연하게 할 수도 있습니다.

SQL Data Warehouse는 단순하고 효율적인 저장된 프로시저 구현을 제공합니다. SQL Server와 비교하여 가장 큰 차이점은 저장된 프로시저가 미리 컴파일된 코드가 아닙니다. 데이터 웨어하우스에서 컴파일 시간은 대용량 데이터에 대해 쿼리를 실행하는 데 걸리는 시간에 비해 작습니다. 저장 프로시저 코드가 대형 쿼리에 적절하게 최적화되었는지 확인하는 것은 더욱 중요합니다. 시간, 분 및 초(밀리초가 아닌)를 저장하는 것이 목표입니다. 그러므로 저장된 프로시저를 SQL 논리에 대한 컨테이너로 생각하는 것이 더욱 도움이 됩니다.     

SQL Data Warehouse는 저장 프로시저를 실행할 때 SQL 문이 구문 분석되고 변환되고 런타임 시 최적화됩니다. 이 프로세스 중 각 명령문이 분산 쿼리로 변환됩니다. 데이터에 대해 실행되는 SQL 코드는 전송된 쿼리와 다릅니다.

## <a name="nesting-stored-procedures"></a>중첩 저장 프로시저
저장 프로시저가 다른 저장 프로시저를 호출하거나 동적 SQL을 실행하면 내부 저장 프로시저 또는 코드 호출을 중첩된 것이라고 합니다.

SQL Data Warehouse는 최대 8개의 중첩 수준을 지원합니다. 이는 SQL Server와 약간 다릅니다. SQL Server의 중첩 수준은 32입니다.

상위 수준 저장 프로시저 호출은 중첩 수준 1과 같습니다.

```sql
EXEC prc_nesting
```
또한 저장 프로시저가 다른 EXEC 호출을 만드는 경우 중첩 수준이 2로 증가합니다.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
그 다음 두 번째 프로시저가 일부 동적 SQL을 실행하면 중첩 수준이 3으로 증가합니다.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

SQL Data Warehouse는 현재 [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql)을 지원하지 않습니다. 중첩 수준을 추적해야 합니다. 8개의 중첩 수준을 초과할 가능성은 없지만 초과하는 경우 이 한도 내에서 중첩 수준에 맞도록 코드를 다시 작업해야 합니다.

## <a name="insertexecute"></a>INSERT..EXECUTE
SQL Data Warehouse는 INSERT 문을 사용하여 저장된 프로시저의 결과 집합을 사용하도록 허용하지 않습니다. 그러나 대체 방법을 사용할 수 있습니다. 예를 들어 [임시 테이블](sql-data-warehouse-tables-temporary.md)의 문서를 참조하세요. 

## <a name="limitations"></a>제한 사항
SQL Data Warehouse에서 구현되지 않은 TRANSACT-SQL 저장된 프로시저의 일부 측면이 있습니다.

아래에 이 계정과 키의 예제가 나와 있습니다.

* 임시 저장 프로시저
* 숫자가 매겨진 저장 프로시저
* 확장된 저장 프로시저
* CLR 저장 프로시저
* 암호화 옵션
* 복제 옵션
* 테이블 반환 매개 변수
* 읽기 전용 매개 변수
* 기본 매개 변수
* 실행 컨텍스트
* return 문

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.

