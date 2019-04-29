---
title: 워크 로드 중요도 | Microsoft Docs
description: Azure SQL Data Warehouse의 쿼리에 대 한 중요도 설정 하기 위한 지침입니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 12e7d9bc22eff14bbf302aed50080412d04a40d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474705"
---
# <a name="sql-data-warehouse-workload-importance-preview"></a>SQL Data Warehouse 워크 로드 중요도 (미리 보기)

이 문서에서는 워크 로드 중요도 SQL Data Warehouse 요청에 대 한 실행 순서 영향을 줄 수는 방법을 설명 합니다.

> [!Note]
> 워크로드 분류는 SQL Data Warehouse Gen2에서 미리 보기로 제공됩니다. 워크로드 관리 분류 및 중요도 미리 보기는 2019년 4월 9일 이후 릴리스를 사용한 빌드를 위한 것입니다.  사용자는 워크로드 관리 테스트를 위해 이 날짜 이전에는 빌드를 사용하지 않는 것이 좋습니다.  자신의 빌드에서 워크로드 관리가 가능한지 알아보려면 SQL Data Warehouse 인스턴스에 연결된 경우 @@version을 선택하여 실행합니다.

## <a name="importance"></a>중요도

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

비즈니스 요구 사항에는 데이터 웨어하우징 워크 로드를 다른 메트릭보다 더 중요 필요할 수 있습니다.  회계 기간 닫습니다 하기 전에 중요 한 판매 데이터를 로드 하는 있는 경우를 고려해 보십시오.  날씨 데이터에는 엄격한 Sla 없는 같은 다른 원본에 대 한 데이터를 로드 합니다.   리소스에 대 한 첫 번째 액세스를 가져오고 더 빠르게 완료 요청 부하 데이터 판매 데이터 로드를 통해 여부와 상관에 판매 데이터를 로드 하는 요청에 대 한 중요도 높음 및 낮음 중요도 설정 합니다.

## <a name="importance-levels"></a>중요도 수준

다섯 가지 수준의 중요도: 낮음, 높으나, 보통, 높으나, 및 높은 합니다.  중요도 설정 하지 않은 요청에는 보통의 기본 수준을 할당 됩니다.  요청에 동일한 중요도 수준을 동일 하 게 예약 동작 현재 존재 하는 합니다.

## <a name="importance-scenarios"></a>중요도 시나리오

판매와 날씨 데이터를 사용 하 여 위에서 설명한 기본 중요도 경우 초과 작업 중요도 데이터 처리 및 쿼리 요구에 맞게 사용 하면 다른 경우도 있습니다.

### <a name="locking"></a>잠금

읽기에 대 한 잠금에 액세스 하 고 작업 중 하나입니다 자연 스러운 경합을 작성 합니다.  활동과 같은 [파티션 전환을](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) 또는 [RENAME OBJECT](/sql/t-sql/statements/rename-transact-sql) 상승 된 잠금이 필요 합니다.  SQL Data Warehouse 워크 로드 중요 하지 않고 처리량은 최적화합니다.  큐에 대기 중인된 요청 처리량 즉, 실행 하는 경우 큐에 대기 중인된 요청 동일한 잠금 요구 및 리소스를 사용할 수에 대 한 최적화를 요구의 높은 잠금이 설정 된 이전 요청 큐에 도착 한 요청을 무시할 수 있습니다.  워크 로드 중요도 높은 잠금을 사용 하 여 요청에 적용 되 면 해야 합니다. 낮은 중요도 사용 하 여 더 높은 중요도 사용 하 여 요청 요청 하기 전에 실행 됩니다.

다음 예제를 살펴보세요.

Q1은 적극적으로 실행 중 이며 SalesFact에서 데이터를 선택 합니다.
Q2 Q1 완료 될 때까지 대기 큐에 대기 됩니다.  오전 9 시에 제출한 하 고 SalesFact에 파티션 전환에 대 한 새 데이터를 하려고 합니다.
Q3 01 오전 9 시 전송 되 고 SalesFact에서 데이터를 선택 하려고 합니다.

Q2 및 Q3에 똑같이 중요 Q1 계속 실행 중인 경우 Q3 실행을 시작 합니다. SalesFact에서 배타적 잠금을 기다려야 Q2 계속 됩니다.  Q2 Q3 보다 중요도 더 높고 있으면 Q3 Q2 실행을 시작 하기 전에 완료 될 때까지 기다릴 수 있습니다.

### <a name="non-uniform-requests"></a>비균일 요청

다른 시나리오는 중요도 쿼리 요구를 충족 시킬 수 있습니다 다른 리소스 클래스를 사용 하 여 요청 제출 되는 경우입니다.  똑같이 중요에서 이전에 언급 된 대로 SQL Data Warehouse는 처리량 최적화 합니다.  혼합 된 크기 요청 (예: smallrc 또는 mediumrc) 큐에 대기 하는 경우 SQL Data Warehouse는 사용 가능한 리소스에 포함 되는 가장 빠른 도착 요청을 선택 합니다.  중요 워크 로드에 적용 되는 경우 가장 높은 중요도 요청 다음 예약 됩니다.
  
DW500c에 다음 예제를 살펴보세요.

Q1, Q2, Q3, 및 Q4 smallrc 쿼리 실행 됩니다.
Q5 오전 9 시에 mediumrc 리소스 클래스를 사용 하 여 제출 됩니다.
Q6 오전 9 시 01 smallrc 리소스 클래스를 사용 하 여 제출 됩니다.

Q5 mediumrc 이기 때문에 두 개의 동시성 슬롯이 필요 합니다.  Q5 두 실행 중인 쿼리를 완료할 때까지 대기 해야 합니다.  그러나 실행 중인 쿼리 (Q1-Q4) 중 하나에 다음이 완료 되 면 Q6 예약 됩니다 즉시 쿼리를 실행 하는 리소스 존재 하기 때문에.  Q5 Q6 보다 중요도 더 높고 있으면 Q6 Q5 실행을 시작 하기 전에 실행 될 때까지 대기 합니다.

## <a name="next-steps"></a>다음 단계

SQL Data Warehouse 워크 로드 분류에 대 한 자세한 내용은 참조 하세요. [SQL 데이터 웨어하우스 워크 로드 분류](sql-data-warehouse-workload-classification.md) 하 고 [워크 로드 분류자 만들기](quickstart-create-a-workload-classifier-tsql.md)합니다. 쿼리 및 할당된 중요도를 보려면 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)를 참조하세요.
