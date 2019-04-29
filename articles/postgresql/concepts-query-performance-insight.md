---
title: Azure Database for PostgreSQL의 Query Performance Insight
description: 이 문서에서는 Azure Database for PostgreSQL의 Query Performance Insight 기능을 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/28/2019
ms.openlocfilehash: 56abdd819e78312e64209078c3966826385df7bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564410"
---
# <a name="query-performance-insight"></a>쿼리 

**적용 대상:** Azure Database for PostgreSQL 9.6 및 10

Query Performance Insight를 사용하면 가장 오랫동안 실행되는 쿼리, 쿼리가 시간의 경과에 따라 변경되는 방식 및 쿼리에 영향을 주는 대기 등을 빠르게 파악할 수 있습니다.

## <a name="permissions"></a>권한
Query Performance Insight에서 쿼리 텍스트를 보는 데 필요한 **소유자** 또는 **참가자** 권한입니다. **읽기 권한자**는 차트 및 표를 볼 수 있지만 쿼리 텍스트는 볼 수 없습니다.

## <a name="prerequisites"></a>필수 조건
Query Performance Insight가 작동하려면 [쿼리 저장소](concepts-query-store.md)에 데이터가 있어야 합니다.

## <a name="viewing-performance-insights"></a>Performance Insight 보기
Azure Portal의 [Query Performance Insight](concepts-query-performance-insight.md) 보기에는 쿼리 저장소의 핵심 정보가 시각화되어 표시됩니다. 

PostgreSQL 서버용 Azure Database의 포털 페이지에서 선택 **쿼리 성능 Insight** 아래의 합니다 **지능형 성능** 메뉴의 섹션입니다.

![Query Performance Insight 장기 실행 쿼리](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

합니다 **장기 실행 쿼리** 15 분 간격으로 집계 탭 실행 당 평균 기간별 상위 5 개 쿼리를 보여 줍니다. **쿼리 수** 드롭다운에서 선택하여 더 많은 쿼리를 볼 수 있습니다. 이 작업을 수행하면 특정 쿼리 ID에 대해 차트 색이 변경될 수 있습니다.

차트를 클릭하고 끌어 특정 기간으로 범위를 좁힐 수 있습니다. 확대/축소 아이콘을 사용하여 더 긴 기간이나 더 짧은 기간을 표시할 수도 있습니다.

차트 아래의 표에 해당 기간의 장기 실행 쿼리에 대한 자세한 내용이 나와 있습니다.

서버의 대기 쿼리를 시각화하려면 **대기 통계** 탭을 선택합니다.

![Query Performance Insight 대기 통계](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>다음 단계
- Azure Database for PostgreSQL의 [모니터링 및 튜닝](concepts-monitoring.md)에 대해 자세히 알아보세요.


