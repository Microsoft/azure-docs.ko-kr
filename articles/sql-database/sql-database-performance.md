---
title: 성능 모니터링 및 향상 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database는 현재 쿼리 성능을 향상시킬 수 있는 영역을 식별하는 데 도움이 되는 성능 도구를 제공합니다.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: jrasnik, carlrab
manager: digimobile
origin.date: 01/25/2019
ms.date: 02/25/2019
ms.openlocfilehash: adbccd5f9cfd5ddd1912e304f800f3ebe04912c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584831"
---
# <a name="monitor-and-improve-performance"></a>성능 모니터링 및 향상

Azure SQL Database는 데이터베이스의 잠재적 문제를 파악하고 지능형 튜닝 작업 및 권장 사항을 제공하여 워크로드 성능을 높일 수 있는 작업을 권장합니다.

데이터베이스 성능을 검토하려면 개요 페이지의 **성능** 타일을 사용하거나 "지원 + 문제 해결" 섹션에서 아래로 이동합니다.

   ![성능 보기](./media/sql-database-performance/entries.png)

"지원 + 문제 해결" 섹션에서 사용할 수 있는 페이지는 다음과 같습니다.


1. [성능 개요](#performance-overview) - 데이터베이스 성능을 모니터링합니다. 
2. [성능 권장 사항](#performance-recommendations) - 워크로드 성능을 향상시킬 수 있는 성능 권장 사항을 찾습니다.
3. [Query Performance Insight](#query-performance-insight) - 쿼리를 소비하는 상위 리소스를 찾습니다.
4. [자동 튜닝](#automatic-tuning) - Azure SQL Database에서 데이터베이스를 자동으로 최적화하도록 허용합니다.

## <a name="performance-overview"></a>성능 개요

이 보기에서는 데이터베이스 성능에 대한 요약을 제공하여 성능 튜닝 및 문제 해결에 도움이 됩니다. 

![성능](./media/sql-database-performance/performance.png)

* **권장 사항** 타일은 데이터베이스에 대한 튜닝 권장 사항을 분석하여 제공합니다(표시되는 내용이 많은 경우 상위 3가지 권장 사항이 표시됨). 이 타일을 클릭하면  **[성능 권장 사항](#performance-recommendations)** 으로 이동합니다. 
* **튜닝 활동** 타일은 데이터베이스에 대해 진행 중이고 완료된 튜닝 작업을 제공하여 튜닝 활동의 기록을 간략히 볼 수 있습니다. 이 타일을 클릭하면 데이터베이스에 대한 전체 튜닝 기록 보기로 이동합니다.
* **자동 튜닝** 타일은 데이터베이스에 대한 [자동 튜닝 구성](sql-database-automatic-tuning-enable.md)을 보여 줍니다(데이터베이스에 자동으로 적용되는 튜닝 옵션). 이 타일을 클릭하면 자동화 구성 대화 상자가 열립니다.
* **데이터베이스 쿼리** 타일은 데이터베이스에 대한 쿼리 성능 요약을 보여 줍니다(전체 DTU 사용량 및 최상위 리소스 사용 쿼리). 이 타일을 클릭하면 **[Query Performance Insight](#query-performance-insight)** 로 이동합니다.

## <a name="performance-recommendations"></a>성능 권장 사항

이 페이지는 데이터베이스의 성능을 향상시킬 수 있는 지능형 [튜닝 권장 사항](sql-database-advisor.md)을 제공합니다. 다음과 같은 유형의 권장 사항이 이 페이지에 표시됩니다.

* 만들거나 삭제할 인덱스에 대한 권장 사항.
* 데이터베이스에서 스키마 문제가 확인될 때 권장 사항.
* 쿼리가 매개 변수화된 쿼리에서 이점을 얻을 수 있는 경우 권장 사항.

![성능](./media/sql-database-performance/recommendations.png)

또한 이전에 적용되었던 튜닝 작업의 전체 기록을 찾을 수도 있습니다.

[성능 권장 사항 찾기 및 적용](sql-database-advisor-portal.md) 문서에서 성능 권장 사항을 찾아 적용하는 방법을 알아보세요.

## <a name="automatic-tuning"></a>자동 조정

Azure SQL 데이터베이스는 자동으로 [성능 권장 사항](sql-database-advisor.md)을 적용하여 데이터베이스 성능을 튜닝할 수 있습니다. 자세한 내용은 [자동 튜닝 문서](sql-database-automatic-tuning.md)를 읽어보세요. 이 기능을 사용하려면 [자동 튜닝 사용 방법](sql-database-automatic-tuning-enable.md)을 읽어보세요.

## <a name="query-performance-insight"></a>쿼리

[Query Performance Insight](sql-database-query-performance.md) 를 통해 다음을 제공하여 데이터베이스 성능 문제 해결 시간을 줄일 수 있습니다.

* 데이터베이스 리소스(DTU) 사용에 대한 보다 자세한 정보를 확인합니다. 
* 최상위 CPU 사용 쿼리는 향상된 성능을 위해 잠재적으로 조정될 수 있습니다. 
* 쿼리에 대한 세부 정보로 드릴다운할 수 있습니다. 

  ![성능 대시보드](./media/sql-database-query-performance/performance.png)

**[Query Performance Insight 사용 방법](sql-database-query-performance.md)** 문서에서 이 페이지에 대한 자세한 내용을 알아보세요.

## <a name="additional-resources"></a>추가 리소스

* [단일 데이터베이스의 Azure SQL Database 성능 지침](sql-database-performance-guidance.md)
* [탄력적 풀을 사용해야 하는 경우](sql-database-elastic-pool-guidance.md)

