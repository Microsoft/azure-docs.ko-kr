---
title: Azure SQL Data Warehouse 관리 효율성 및 모니터링 - 쿼리 작업, 리소스 사용률 | Microsoft Docs
description: Azure SQL Data Warehouse를 관리하고 모니터링하는 데 사용할 수 있는 기능에 대해 알아봅니다. Azure Portal과 DMV(Dynamic Management Views)를 사용하여 데이터 웨어하우스의 쿼리 작업 및 리소스 사용률을 이해합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/12/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f80c1817d5c0ce79f2dc53f40a2cc4e00dd5c72b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420969"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에서 리소스 사용률 및 쿼리 작업 모니터링
Azure SQL Data Warehouse는 Azure Portal 내에 다양한 모니터링 환경을 제공하여 데이터 웨어하우스 워크로드에 대한 인사이트를 제공합니다. 데이터 웨어하우스를 모니터링할 때는 구성 가능한 보존 기간, 경고, 권장 사항, 메트릭과 로그용 사용자 지정 가능한 차트 및 대시보드를 제공하는 도구인 Azure Portal을 사용하는 것이 좋습니다. 포털 또한 데이터 웨어하우스가 뿐만 아니라 전체 Azure 분석에 대 한 포괄적인 모니터링 환경을 제공 하려면 Operations Management Suite (OMS) 및 Azure Monitor (로그)와 같은 다른 Azure 모니터링 서비스와 통합할 수 있습니다. 통합된 된 모니터링 환경 위한 플랫폼입니다. 이 문서에서는 SQL Data Warehouse를 사용하여 분석 플랫폼을 최적화하고 관리하는 데 사용할 수 있는 모니터링 기능에 대해 설명합니다. 

## <a name="resource-utilization"></a>리소스 사용률 
SQL Data Warehouse용 Azure Portal에서 다음 메트릭을 사용할 수 있습니다. 이러한 메트릭은 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics)를 통해 나타납니다.

> [!NOTE]
> 현재 노드 수준 CPU 및 IO 메트릭을 올바르게 반영 하지 않습니다 데이터 웨어하우스 사용 합니다. 이러한 메트릭은 모니터링 및 문제 해결 환경을 SQL Data Warehouse에 대 한 팀 향상 가까운 장래에 제거 됩니다. 

| 메트릭 이름                           | 설명     | 집계 형식 |
| --------------------------------------- | ---------------- | --------------------------------------- |
| CPU 비율                          | 데이터 웨어하우스에 대한 모든 노드에서의 CPU 사용률 | 최대      |
| 데이터 IO 비율                      | 데이터 웨어하우스에 대한 모든 노드에서의 IO 사용률 | 최대   |
| 성공적인 연결                  | 데이터에 대한 연결 성공 횟수 | 합계            |
| 실패한 연결                      | 데이터 웨어하우스에 대한 연결 실패 횟수 | 합계            |
| 방화벽에 의해 차단                     | 차단된 데이터 웨어하우스에 대한 로그인 횟수 | 합계            |
| DWU 제한                              | 데이터 웨어하우스의 서비스 수준 목표 | 최대   |
| DWU 백분율                          | CPU 비율과 데이터 IO 비율 사이의 최댓값 | 최대   |
| DWU 사용됨                                | DWU 한도 * DWU 비율 | 최대   |
| 캐시 적중 비율 | (캐시 적중/캐시 누락) * 100, 여기서 캐시 적중은 로컬 SSD 캐시에서 적중된 모든 columnstore 세그먼트에 대한 합계이며, 캐시 누락은 모든 노드에 걸쳐 로컬 SSD 캐시에서 누락된 columnstore 세그먼트에 대한 합계입니다. | 최대 |
| 캐시 사용 비율 | (사용된 캐시/캐시 용량) * 100, 여기서 사용된 캐시는 모든 노드에 걸친 로컬 SSD 캐시의 모든 바이트에 대한 합계이며, 캐시 용량은 모든 노드에 걸친 로컬 SSD 캐시의 저장소 용량에 대한 합계입니다 | 최대 |
| 로컬 tempdb 백분율 | 모든 컴퓨팅 노드의 로컬 tempdb 사용률 - 5분마다 값을 내보냅니다. | 최대 |

## <a name="query-activity"></a>쿼리 작업
T-SQL을 통해 SQL Data Warehouse를 모니터링할 때의 프로그래밍 방식 환경을 위해 서비스에서 일단의 DMV(Dynamic Management Views)를 제공합니다. 이러한 보기는 워크로드로 인한 성능 병목 상태를 적극적으로 해결하고 식별할 때 유용합니다.

SQL Data Warehouse에서 제공하는 DMV 목록을 보려면 [이 설명서](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)를 참조하세요. 

## <a name="metrics-and-diagnostics-logging"></a>메트릭 및 진단 로깅
메트릭 및 로그를 모두 내보낼 수 있습니다 Azure Monitor에 특히 합니다 [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 구성 요소를 통해 프로그래밍 방식으로 액세스할 수 있습니다 [로그 쿼리](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)합니다. SQL Data Warehouse의 로그 대기 시간은 약 10 ~ 15 분입니다. 대기 시간에 영향을 주는 요인에 대 한 자세한 내용은 다음 문서를 참조 하세요.


## <a name="next-steps"></a>다음 단계
다음 방법 가이드에서는 데이터 웨어하우스를 모니터링하고 관리할 때의 일반적인 시나리오와 사용 사례에 대해 설명합니다.

- [DMV를 사용하여 데이터 웨어하우스 워크로드 모니터링](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

