---
title: Azure Database for MySQL에서 성능 튜닝 및 데이터베이스 유지 관리를 위해 sys_schema를 사용하는 방법
description: 이 문서에서는 Azure Database for MySQL에서 sys_schema를 사용하여 성능 문제를 찾고 데이터베이스를 유지 관리하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/01/2018
ms.openlocfilehash: 993c77056c09c1dc21d5317ddbfe8e937341718d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422374"
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 성능 튜닝 및 데이터베이스 유지 관리를 위해 sys_schema를 사용하는 방법

MySQL 5.5에 처음 제공된 MySQL performance_schema에는 메모리 할당, 저장된 프로그램, 메타데이터 잠금 등과 같은 다수의 중요한 서버 리소스에 대한 계측이 제공됩니다. 하지만 performance_schema에는 80개를 초과하는 테이블이 포함되어 있어서 필요한 정보를 얻으려면 performance_schema 내의 테이블은 물론 information_schema의 테이블을 조인해야 합니다. performance_schema 및 information_schema 모두를 기반으로 하는 sys_schema에는 읽기 전용 데이터베이스의 강력하고 [사용자에게 친숙한 보기](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) 컬렉션이 제공되며 Azure Database for MySQL 버전 5.7에서 완벽한 사용이 가능합니다.

![sys_schema의 버전](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

sys_schema에는 52개의 보기가 있고 각 보기에는 다음 접두사 중 하나가 있습니다.

- Host_summary 또는 IO: I/O 관련 대기 시간.
- InnoDB: InnoDB 버퍼 상태 및 잠금.
- 메모리: 호스트 및 사용자별 메모리 사용량.
- 스키마: 스키마 관련 정보(예: 자동 증분, 인덱스 등).
- Statement: SQL 문에 대한 정보. 전체 테이블 검색 또는 긴 쿼리 시간을 유발하는 명령문이 될 수 있습니다.
- 사용자: 사용자별로 소비되고 그룹화된 리소스. 예: 파일 I/O, 연결 및 메모리.
- Wait: 호스트 또는 사용자별로 그룹화된 대기 이벤트.

이제 sys_schema의 몇 가지 일반적인 사용량 패턴을 살펴보겠습니다. 우선, 사용량 패턴을 다음 두 가지 범주로 그룹화하겠습니다. **성능 튜닝** 및 **데이터베이스 유지 관리**.

## <a name="performance-tuning"></a>성능 튜닝

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

IO는 데이터베이스에서 비용이 가장 높은 작업입니다. *sys.user_summary_by_file_io* 보기를 쿼리하면 평균 IO 대기 시간을 찾을 수 있습니다. 기본적으로 프로비전된 저장소가 125GB인 상태에서 내 IO 대기 시간은 약 15초입니다.

![IO 대기 시간: 125GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Azure Database for MySQL에서 저장소와 관련된 IO 크기를 조정하기 때문에 내 프로비전된 저장소를 1TB로 늘린 후, 내 IO 대기 시간이 571ms로 단축됩니다.

![IO 대기 시간: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

신중한 계획에도 불구하고 전체 테이블 검색을 유발하는 쿼리가 여전히 많습니다. 인덱스 유형을 최적화하는 방법과 인덱스 유형에 대한 자세한 정보는 다음 문서를 참조하세요. [쿼리 성능 문제를 해결하는 방법](./howto-troubleshoot-query-performance.md). 전체 테이블 검색에는 리소스가 많이 사용되기 때문에 데이터베이스 성능이 저하됩니다. 전체 테이블 검색으로 테이블을 찾는 가장 빠른 방법은 *sys.schema_tables_with_full_table_scans*보기를 쿼리하는 것입니다.

![전체 테이블 검색](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

데이터베이스 성능 문제를 해결하려면 데이터베이스 내부에서 발생하는 이벤트를 식별하는 것이 좋으며, *sys.user_summary_by_statement_type* 보기를 사용하는 것이 유용할 수 있습니다.

![명령문 요약](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

이 예제에서 Azure Database for MySQL이 slog 쿼리 로그를 44579번 플러시하는 데 53분이 소요되었습니다. 오랜 시간과 많은 IO가 소비되었습니다. Azure Portal에서 느린 쿼리 로그를 사용하지 않도록 설정하거나 느린 쿼리 로그의 빈도를 줄여서 이 작업을 줄일 수 있습니다.

## <a name="database-maintenance"></a>데이터베이스 유지 관리

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

InnoDB 버퍼 풀은 메모리에 상주하며 DBMS와 저장소 사이의 주요 캐시 매커니즘입니다. InnoDB 버퍼 풀의 크기는 성능 계층에 연결되어 있고 다른 제품 SKU를 선택하지 않으면 변경할 수 없습니다. 운영 체제의 메모리와 마찬가지로 새로운 데이터를 위한 공간을 확보하기 위해 오래된 페이지가 스왑 아웃(swap out)됩니다. 어떤 테이블이 대부분의 InnoDB 버퍼 풀 메모리를 소비하는지 찾아내려면 *sys.innodb_buffer_stats_by_table* 보기를 쿼리합니다.

![InnoDB 버퍼 상태](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

위의 그래프에서 보면, 시스템 테이블과 보기 외에 WordPress 사이트 중 하나를 호스트하는 mysqldatabase033 데이터베이스의 각 테이블이 메모리의 데이터를 16KB 또는 1페이지 차지하고 있습니다.

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

인덱스는 읽기 성능을 향상시키는 뛰어난 도구이지만 삽입과 저장소에 추가 비용이 발생합니다. *Sys.schema_unused_indexes* 및 *sys.schema_redundant_indexes*는 사용하지 않은 인덱스나 중복 인덱스에 대한 정보를 제공합니다.

![사용하지 않은 인덱스](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![중복 인덱스](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>결론

요약하자면, sys_schema는 성능 튜닝 및 데이터베이스 유지 관리 모두에 유용한 도구입니다. Azure Database for MySQL에서 이 기능을 반드시 활용하세요. 

## <a name="next-steps"></a>다음 단계
- 가장 궁금한 질문에 대한 동료의 답변을 찾아보거나 새로운 질문/답변을 게시하려면 [MSDN 포럼](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) 또는 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)를 참조하세요.
