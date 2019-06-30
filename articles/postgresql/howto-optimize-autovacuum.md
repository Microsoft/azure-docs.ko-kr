---
title: Azure Database for PostgreSQL-단일 서버에서 Autovacuum 최적화
description: 이 문서에서는 Azure Database for PostgreSQL-단일 서버에서 autovacuum를 최적화하는 방법을 설명합니다.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: fb1ab9525974601a8b8c22ccc44e2cf37baf21a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65069109"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL-단일 서버에서 Autovacuum 최적화
이 문서에서는 Azure Database for PostgreSQL 서버에서 Autovacuum을 효과적으로 최적화하는 방법을 설명합니다.

## <a name="overview-of-autovacuum"></a>Autovacuum 개요
PostgreSQL은 MVCC(다중 버전 동시성 제어)를 사용하여 데이터베이스 동시성을 향상합니다. 업데이트할 때마다 삽입 및 삭제가 발생하고, 삭제할 때마다 행이 삭제되도록 소프트 표시됩니다. 소프트 표시는 나중에 제거할 데드 튜플을 식별합니다. 이러한 작업을 수행하기 위해 PostgreSQL은 vacuum 작업을 실행합니다.

vacuum 작업은 수동 또는 자동으로 트리거할 수 있습니다. 데이터베이스에서 많은 업데이트 또는 삭제 작업이 발생하는 경우에는 데드 튜플이 많습니다. 데이터베이스가 유휴 상태일 때는 데드 튜플이 적습니다. 데이터베이스 부하가 많은 경우 더 자주 vacuum해야 하므로 이 경우 ‘수동으로’ vacuum 작업을 실행하는 것이 불편합니다. 

Autovacuum은 구성할 수 있으며 튜닝의 이점을 활용할 수 있습니다. PostgreSQL이 제공하는 기본값은 모든 종류의 디바이스에서 제품이 작동하도록 합니다. 이러한 디바이스에는 Raspberry Pi가 포함됩니다. 이상적인 구성 값은 다음에 따라 다릅니다.
- 사용 가능한 총 리소스 크기(예: SKU 및 스토리지 크기)
- 리소스 사용량
- 개별 개체 특성

## <a name="autovacuum-benefits"></a>Autovacuum의 이점
때때로 vacuum하지 않으면 누적된 데드 튜플로 인해 다음과 같은 결과가 발생할 수 있습니다.
- 데이터 블로트(예: 큰 데이터베이스 및 테이블)
- 부적절한 인덱스 증가
- I/O 증가

## <a name="monitor-bloat-with-autovacuum-queries"></a>Autovacuum 쿼리를 사용하여 블로트 모니터링
XYZ라는 테이블에 있는 데드 및 라이브 튜플 수를 식별하도록 설계된 샘플 쿼리는 다음과 같습니다.
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Autovacuum 구성
Autovacuum을 제어하는 구성 매개 변수는 다음 두 가지 주요 질문에 대한 답변을 기반으로 합니다.
- 언제 시작해야 하나요?
- 시작한 후에 얼마나 정리해야 하나요?

이전 질문에 따라 업데이트할 수 있는 일부 Autovacuum 구성 매개 변수와 몇 가지 지침은 다음과 같습니다.

매개 변수|설명|기본값
---|---|---
autovacuum_vacuum_threshold|한 테이블에서 vacuum 작업을 트리거하는 데 필요한 업데이트 또는 삭제된 튜플의 최소 개수를 지정합니다. 기본값은 50개 튜플입니다. 이 매개 변수는 postgresql.conf 파일 또는 서버 명령줄에서만 설정합니다. 개별 테이블에 대한 설정을 재정의하려면 테이블 스토리지 매개 변수를 변경합니다.|50
autovacuum_vacuum_scale_factor|Vacuum 작업을 트리거할지 여부를 결정할 때 autovacuum_vacuum_threshold에 추가할 테이블 크기의 비율을 지정합니다. 기본값은 0.2로, 테이블 크기의 20%입니다. 이 매개 변수는 postgresql.conf 파일 또는 서버 명령줄에서만 설정합니다. 개별 테이블에 대한 설정을 재정의하려면 테이블 스토리지 매개 변수를 변경합니다.|5%
autovacuum_vacuum_cost_limit|자동 vacuum 작업에 사용되는 비용 제한 값을 지정합니다. -1(기본값)을 지정하면 기본 vacuum_cost_limit 값이 사용됩니다. 둘 이상의 작업자가 있는 경우 이 값은 실행 중인 autovacuum 작업자 간에 비례적으로 배분됩니다. 각 작업자에 대한 제한의 합계는 이 변수의 값을 초과하지 않습니다. 이 매개 변수는 postgresql.conf 파일 또는 서버 명령줄에서만 설정합니다. 개별 테이블에 대한 설정을 재정의하려면 테이블 스토리지 매개 변수를 변경합니다.|-1
autovacuum_vacuum_cost_delay|자동 vacuum 작업에 사용되는 비용 지연 값을 지정합니다. -1을 지정하면 기본 vacuum_cost_delay 값이 사용됩니다. 기본값은 20밀리초입니다. 이 매개 변수는 postgresql.conf 파일 또는 서버 명령줄에서만 설정합니다. 개별 테이블에 대한 설정을 재정의하려면 테이블 스토리지 매개 변수를 변경합니다.|20밀리초
autovacuum_nap_time|지정된 데이터베이스에서 자동 진공 실행 사이의 최소 지연 시간을 지정합니다. 각 라운드에서 디먼은 데이터베이스를 검사하고, 해당 데이터베이스의 테이블에 필요한 대로 VACUUM 및 ANALYZE 명령을 실행합니다. 지연 시간은 초 단위로 측정되며, 기본값은 1분(1min)입니다. 이 매개 변수는 postgresql.conf 파일 또는 서버 명령줄에서만 설정합니다.|15초
autovacuum_max_workers|한 번에 실행할 수 있는 autovacuum 프로세스(autovacuum 시작 관리자 제외)의 최대 개수를 지정합니다. 기본값은 3입니다. 이 매개 변수는 서버 시작 시에만 설정합니다.|3

개별 테이블에 대한 설정을 재정의하려면 테이블 스토리지 매개 변수를 변경합니다. 

## <a name="autovacuum-cost"></a>Autovacuum 비용
vacuum 작업을 실행하는 “비용”은 다음과 같습니다.

- vacuum이 실행되는 데이터 페이지가 잠깁니다.
- vacuum 작업을 실행할 때 컴퓨팅 및 메모리가 사용됩니다.

따라서 vacuum 작업을 너무 자주 실행하거나 너무 드물게 실행하지 마세요. 워크로드에 맞게 vacuum 작업을 조정해야 합니다. Autovacuum 매개 변수 간 절충되므로 Autovacuum 매개 변수의 모든 변경 내용을 테스트하도록 합니다.

## <a name="autovacuum-start-trigger"></a>Autovacuum 시작 트리거
데드 튜플 수가 autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples를 초과할 경우 autovacuum이 트리거됩니다. 여기서 reltuples는 상수입니다.

데이터베이스 부하에 따라 autovacuum을 통해 정리되어야 합니다. 그렇지 않으면 스토리지가 부족하여 쿼리 성능이 일반적으로 저하될 수 있습니다. 시간에 따라 분할 시, vacuum 작업이 데드 튜플을 정리하는 속도는 데드 튜플이 생성되는 속도와 같아야 합니다.

많은 업데이트 및 삭제가 있는 데이터베이스에는 데드 튜플이 더 많으며, 더 많은 공간이 필요합니다. 일반적으로 많은 업데이트 및 삭제가 있는 데이터베이스의 경우 autovacuum_vacuum_scale_factor 및 autovacuum_vacuum_threshold에 낮은 값을 사용하는 것이 좋습니다. 낮은 값을 사용하면 데드 튜플이 장기간 누적되는 것을 방지할 수 있습니다. 작은 데이터베이스의 경우 vacuum 요구가 비교적 긴급하지 않으므로 두 매개 변수에 더 높은 값을 사용할 수 있습니다. 자주 vacuum하면 컴퓨팅 및 메모리가 소모됩니다.

기본 배율 20%는 데드 튜플 백분율이 낮은 테이블에 적합합니다. 데드 튜플 백분율이 높은 테이블에는 효과적이지 않습니다. 예를 들어 20GB 테이블에서 이 배율은 4GB 데드 튜플을 나타냅니다. 1TB 테이블에서는 200GB 데드 튜플이 됩니다.

PostgreSQL을 사용하면 이러한 매개 변수를 테이블 수준 또는 인스턴스 수준에서 설정할 수 있습니다. 현재 이러한 매개 변수는 Azure Database for PostgreSQL의 테이블 수준에서만 설정할 수 있습니다.

## <a name="estimate-the-cost-of-autovacuum"></a>Autovacuum 비용 예측
Autovacuum을 실행하는 경우 “비용이 많이 들기” 때문에 vacuum 작업의 런타임을 제어하는 매개 변수가 있습니다. Vacuum 실행 비용을 예측하는 데 도움이 되는 매개 변수는 다음과 같습니다.
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Vacuum 프로세스는 물리적 페이지를 읽고 데드 튜플을 확인합니다. shared_buffers의 모든 페이지는 비용이 1(vacuum_cost_page_hit)인 것으로 간주됩니다. 다른 모든 페이지는 데드 튜플이 있는 경우 비용이 20(vacuum_cost_page_dirty), 데드 튜플이 없는 경우 10(vacuum_cost_page_miss)인 것으로 간주됩니다. 프로세스가 autovacuum_vacuum_cost_limit를 초과하면 vacuum 작업이 중지됩니다. 

제한에 도달하면 프로세스가 autovacuum_vacuum_cost_delay 매개 변수에 지정된 기간에 일시 중지되었다가 다시 시작됩니다. 제한에 도달하지 않으면 autovacuum이 autovacuum_nap_time 매개 변수에 지정된 값 이후에 시작됩니다.

요약하면, autovacuum_vacuum_cost_delay 및 autovacinum_vacuum_cost_limit 매개 변수는 시간 단위당 허용되는 데이터 정리 양을 제어합니다. 기본값은 대부분의 가격 책정 계층에서 너무 낮습니다. 이러한 매개 변수에 대한 최적 값은 가격 책정 계층에 따라 달라지며 이에 따라 적절히 구성해야 합니다.

autovacuum_max_worker 매개 변수는 동시에 실행할 수 있는 autovacuum 프로세스의 최대 개수를 결정합니다.

PostgreSQL을 사용하면 이러한 매개 변수를 테이블 수준 또는 인스턴스 수준에서 설정할 수 있습니다. 현재 이러한 매개 변수는 Azure Database for PostgreSQL의 테이블 수준에서만 설정할 수 있습니다.

## <a name="optimize-autovacuum-per-table"></a>테이블별 autovacuum 최적화
테이블당 이전 구성 매개 변수를 모두 구성할 수 있습니다. 예를 들면 다음과 같습니다.
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum은 테이블별 동기 프로세스입니다. 한 테이블에 있는 데드 튜플의 백분율이 높을수록 autovacuum “비용”도 높아집니다. 업데이트 및 삭제 비율이 높은 테이블을 여러 테이블로 분할할 수 있습니다. 테이블을 분할하면 autovacuum을 병렬 처리하여 한 테이블에서 autovacuum을 완료하는 “비용”을 줄일 수 있습니다. 작업자 예약이 편리하도록 병렬 autovacuum 작업자 수를 늘릴 수도 있습니다.

## <a name="next-steps"></a>다음 단계
autovacuum을 사용하고 튜닝하는 방법에 대한 자세한 내용은 다음 PostgreSQL 문서를 참조하세요.

 - [18장, 서버 구성](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [24장, 일상적인 데이터베이스 유지 관리 작업](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
