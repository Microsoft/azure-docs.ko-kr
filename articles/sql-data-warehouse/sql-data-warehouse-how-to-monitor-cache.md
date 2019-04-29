---
title: Gen2 캐시 최적화 | Microsoft Docs
description: Azure Portal을 사용하여 Gen2 캐시를 모니터링하는 방법에 대해 알아봅니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 26791aecb2ca57b31358d3385d07230c73c84904
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474422"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Gen2 캐시 모니터링 방법
Gen2 저장소 아키텍처는 Gen2 데이터 웨어하우스용으로 설계된 NVMe 기반의 SSD에 있는 캐시에 가장 자주 쿼리되는 columnstore 세그먼트를 자동으로 계층화합니다. 쿼리가 캐시에 있는 세그먼트를 검색하면 성능이 향상됩니다. 이 문서에서는 워크로드가 Gen2 캐시를 최적으로 활용하는지 판단하여 쿼리 성능을 모니터링하고 문제를 해결하는 방법에 대해 설명합니다.  
## <a name="troubleshoot-using-the-azure-portal"></a>Azure Portal을 사용하여 문제 해결
Azure Monitor를 사용하여 Gen2 캐시 메트릭을 보고 쿼리 성능 문제를 해결할 수 있습니다. 먼저 Azure Portal로 이동하여 Azure Monitor를 클릭합니다.

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

메트릭 단추를 선택하고 **구독**, **리소스** **그룹**, **리소스 종류** 및 데이터 웨어하우스의 **리소스 이름**을 입력합니다.

Gen2 캐시 문제 해결에 대한 키 메트릭은 **캐시 적중 비율** 및 **캐시 사용 비율**입니다. 이러한 두 개의 메트릭을 표시하도록 Azure 메트릭 차트를 구성합니다.

![캐시 메트릭](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>캐시 적중 및 사용 비율
아래 매트릭스에서는 캐시 메트릭의 값에 따라 시나리오를 설명합니다.

|                                | **높은 캐시 적중 비율** | **낮은 캐시 적중 비율** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **높은 캐시 사용 비율** |          시나리오 1           |          시나리오 2          |
| **낮은 캐시 사용 비율**  |          시나리오 3           |          시나리오 4          |

**시나리오 1:** 현재 캐시를 최적으로 사용하고 있습니다. 쿼리 속도를 저하시킬 수 있는 다른 영역의 [문제를 해결](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)하세요.

**시나리오 2:** 현재 작업 중인 데이터 세트가 물리적 읽기로 인해 낮은 캐시 적중 비율을 초래할 수 있어 캐시에 적합하지 않습니다. 성능 수준의 규모를 확장하고 워크로드를 다시 실행하여 캐시를 채우는 것을 고려하세요.

**시나리오 3:** 캐시와 관련이 없는 이유로 인해 쿼리가 느리게 실행되는 것 같습니다. 쿼리 속도를 저하시킬 수 있는 다른 영역의 [문제를 해결](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)하세요. 또한 캐시 크기를 줄여 비용을 절약하도록 [인스턴스 규모를 축소](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)하는 것을 고려해 보세요. 

**시나리오 4:** 쿼리가 느려진 원인일 수 있는 콜드 캐시가 있습니다. 작업 데이터 세트가 캐시될 때 쿼리를 다시 실행해보세요. 

**중요: 워크로드를 다시 실행한 후 캐시 적중 비율 또는 캐시 사용 비율이 업데이트되지 않으면 작업 세트가 이미 메모리에 있는 것일 수 있습니다. 클러스터된 columnstore 테이블만 캐시됩니다.**

## <a name="next-steps"></a>다음 단계
일반 쿼리 성능 튜닝에 대한 자세한 내용은 [쿼리 실행 모니터링](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution)을 참조하세요.


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
