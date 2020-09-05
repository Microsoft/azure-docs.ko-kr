---
title: Azure Cosmos DB 쿼리 언어의 날짜 및 시간 함수
description: Azure Cosmos DB에서 날짜 및 시간 SQL 시스템 함수를 학습 하 여 DateTime 및 timestamp 작업을 수행 합니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1ea838224f9d91b000100d5fa9308289619fd963
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605193"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>날짜 및 시간 함수 (Azure Cosmos DB)

날짜 및 시간 함수를 사용 하 여 Azure Cosmos DB에서 DateTime 및 timestamp 작업을 수행할 수 있습니다.

## <a name="functions-to-obtain-the-date-and-time"></a>날짜 및 시간을 가져오는 함수

다음 스칼라 함수를 사용 하면 ISO 8601 형식을 준수 하는 문자열, Unix epoch 이후 경과 된 시간 (밀리초)을 나타내는 숫자 타임 스탬프 또는 Unix epoch 이후 경과한 100 나노초 틱 수를 값으로 갖는 숫자 틱의 세 가지 형식으로 현재 UTC 날짜 및 시간을 가져올 수 있습니다. :

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>DateTime 값으로 작업 하는 함수

다음 함수를 사용 하 여 날짜/시간, 타임 스탬프 및 틱 값을 쉽게 조작할 수 있습니다.

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [DateTimeToTimestamp](sql-query-datetimetotimestamp.md)
* [TicksToDateTime](sql-query-tickstodatetime.md)
* [TimestampToDateTime](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>다음 단계

- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
- [사용자 정의 함수](sql-query-udfs.md)
- [집계](sql-query-aggregates.md)
