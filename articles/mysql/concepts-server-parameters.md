---
title: 서버 매개 변수-Azure Database for MySQL
description: 이 항목에서는 Azure Database for MySQL에서 서버 매개 변수를 구성 하기 위한 지침을 제공 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: e7ca86d0146f05d5171d5eae18aac81d75122bcc
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258558"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Azure Database for MySQL의 서버 매개 변수

이 문서에서는 Azure Database for MySQL에서 서버 매개 변수를 구성 하기 위한 고려 사항 및 지침을 제공 합니다.

## <a name="what-are-server-parameters"></a>서버 매개 변수는 무엇 인가요? 

MySQL 엔진은 엔진 동작을 구성 하 고 조정 하는 데 사용할 수 있는 다양 한 서버 변수/매개 변수를 제공 합니다. 일부 매개 변수는 런타임 중에 동적으로 설정할 수 있으며, 다른 매개 변수는 "정적" 이므로 적용 하기 위해 서버를 다시 시작 해야 합니다.

Azure Database for MySQL는 [Azure Portal](./howto-server-parameters.md), [Azure CLI](./howto-configure-server-parameters-using-cli.md)및 [PowerShell](./howto-configure-server-parameters-using-powershell.md) 을 사용 하 여 다양 한 MySQL 서버 매개 변수의 값을 변경 하 여 워크 로드 요구 사항에 맞게 변경 하는 기능을 제공 합니다.

## <a name="configurable-server-parameters"></a>구성 가능한 서버 매개 변수

지원되는 서버 매개 변수 목록은 계속 확장됩니다. Azure Portal의 서버 매개 변수 탭을 사용 하 여 전체 목록을 보고 서버 매개 변수 값을 구성할 수 있습니다.

다음 섹션을 참조 하 여 일반적으로 업데이트 되는 여러 서버 매개 변수의 제한에 대해 자세히 알아보세요. 제한은 서버의 가격 책정 계층 및 vCores에 의해 결정 됩니다.

### <a name="thread-pools"></a>스레드 풀

MySQL은 일반적으로 모든 클라이언트 연결에 대 한 스레드를 할당 합니다. 동시 사용자 수가 증가 함에 따라 성능이 저하 됩니다. 많은 활성 스레드는 컨텍스트 전환 증가, 스레드 경합 및 CPU 캐시의 잘못 된 집약성으로 인해 성능에 크게 영향을 줄 수 있습니다.

서버 쪽 기능이 며 연결 풀링과는 다른 스레드 풀은 서버에서 실행 되는 활성 스레드 수를 제한 하 고 스레드 변동 (code churn)을 최소화 하는 데 사용할 수 있는 작업자 스레드의 동적 풀을 도입 하 여 성능을 최대화 합니다. 이렇게 하면 연결 버스트로 인해 서버에서 리소스가 부족 하거나 메모리 부족 오류가 발생 하 여 작동이 중단 되지 않도록 할 수 있습니다. 스레드 풀은 OLTP 워크 로드와 같이 짧은 쿼리와 CPU 집약적 워크 로드에 가장 효율적입니다.

스레드 풀에 대 한 자세한 내용은 [Azure Database for MySQL 스레드 풀 소개](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173) 를 참조 하세요.

> [!NOTE]
> MySQL 5.6 버전에 대해서는 스레드 풀 기능이 지원 되지 않습니다. 

### <a name="configuring-the-thread-pool"></a>스레드 풀 구성
스레드 풀을 사용 하도록 설정 하려면 `thread_handling` 서버 매개 변수를 "스레드 풀"로 업데이트 합니다. 기본적으로이 매개 변수는로 설정 됩니다 `one-thread-per-connection` . 즉, MySQL은 각 새 연결에 대해 새 스레드를 만듭니다. 이 매개 변수는 정적 매개 변수 이며 적용 하려면 서버를 다시 시작 해야 합니다.

다음 서버 매개 변수를 설정 하 여 풀의 최대 및 최소 스레드 수를 구성할 수도 있습니다. 
- `thread_pool_max_threads`:이 값을 사용 하면 풀에 있는 스레드 수를 초과할 수 없습니다.
- `thread_pool_min_threads`:이 값은 연결을 닫은 후에도 예약 되는 스레드 수를 설정 합니다.

스레드 풀에 대 한 짧은 쿼리의 성능 문제를 향상 시키기 위해 Azure Database for MySQL를 사용 하면 쿼리를 실행 한 직후 스레드 풀로 다시 반환 하는 대신 일괄 처리 실행을 사용할 수 있습니다. 스레드는이 연결을 통해 다음 쿼리를 대기 하는 데 짧은 시간 동안 활성 상태를 유지 합니다. 그런 다음 스레드는 쿼리를 신속 하 게 실행 하 고 완료 되 면이 프로세스의 전체 시간 사용량이 임계값을 초과할 때까지 다음 시간 동안 기다립니다. 일괄 처리 실행 동작은 다음 서버 매개 변수를 사용 하 여 결정 됩니다.  

-  `thread_pool_batch_wait_timeout`:이 값은 스레드가 다른 쿼리가 처리할 때까지 대기 하는 시간을 지정 합니다.
- `thread_pool_batch_max_time`:이 값은 스레드가 쿼리 실행 주기를 반복 하 고 다음 쿼리를 대기 하는 최대 시간을 결정 합니다.

> [!IMPORTANT]
> 프로덕션 환경에서 설정 하기 전에 스레드 풀을 테스트 하세요. 

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)를 참조하세요.

#### <a name="servers-supporting-up-to-4-tb-storage"></a>최대 2TB 저장소를 지 원하는 서버

|**가격 책정 계층**|**vCore**|**기본값 (바이트)**|**Min value (바이트)**|**Max 값 (바이트)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|범용|2|3758096384|134217728|3758096384|
|범용|4|8053063680|134217728|8053063680|
|범용|8|16106127360|134217728|16106127360|
|범용|16|32749125632|134217728|32749125632|
|범용|32|66035122176|134217728|66035122176|
|범용|64|132070244352|134217728|132070244352|
|메모리 최적화|2|7516192768|134217728|7516192768|
|메모리 최적화|4|16106127360|134217728|16106127360|
|메모리 최적화|8|32212254720|134217728|32212254720|
|메모리 최적화|16|65498251264|134217728|65498251264|
|메모리 최적화|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>서버는 최대 16TB의 저장소를 지원 합니다.

|**가격 책정 계층**|**vCore**|**기본값 (바이트)**|**Min value (바이트)**|**Max 값 (바이트)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|범용|2|7516192768|134217728|7516192768|
|범용|4|16106127360|134217728|16106127360|
|범용|8|32212254720|134217728|32212254720|
|범용|16|65498251264|134217728|65498251264|
|범용|32|132070244352|134217728|132070244352|
|범용|64|264140488704|134217728|264140488704|
|메모리 최적화|2|15032385536|134217728|15032385536|
|메모리 최적화|4|32212254720|134217728|32212254720|
|메모리 최적화|8|64424509440|134217728|64424509440|
|메모리 최적화|16|130996502528|134217728|130996502528|
|메모리 최적화|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` 은 범용 및 메모리 액세스에 최적화 된 가격 책정 계층 에서만 업데이트할 수 있습니다.

MySQL은 테이블을 만드는 동안 제공된 구성에 따라 InnoDB 테이블을 다른 테이블스페이스에 저장합니다. [시스템 테이블스페이스](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html)는 InnoDB 데이터 사전의 스토리지 영역입니다. [file-per-table 테이블스페이스](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html)에는 단일 InnoDB 테이블에 대한 데이터 및 인덱스를 포함하며 파일 시스템에 자체 데이터 파일로 저장됩니다. 이 동작은 `innodb_file_per_table` 서버 매개 변수에 의해 제어됩니다. `innodb_file_per_table`을 `OFF`로 설정하면 InnoDB가 시스템 테이블스페이스에 테이블을 만듭니다. 아니면 InnoDB가 file-per-table 테이블스페이스에 테이블을 만듭니다.

Azure Database for MySQL는 단일 데이터 파일에서 최대 **1TB**를 지원합니다. 데이터베이스 크기가 1TB보다 큰 경우 [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) 테이블스페이스에 테이블을 만들어야 합니다. 단일 테이블 크기가 1TB보다 큰 경우에는 파티션 테이블을 사용해야 합니다.

### <a name="join_buffer_size"></a>join_buffer_size

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size)를 참조하세요.

|**가격 책정 계층**|**vCore**|**기본값 (바이트)**|**Min value (바이트)**|**Max 값 (바이트)**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|262144|128|268435455|
|범용|4|262144|128|536870912|
|범용|8|262144|128|1073741824|
|범용|16|262144|128|2147483648|
|범용|32|262144|128|4294967295|
|범용|64|262144|128|4294967295|
|메모리 최적화|2|262144|128|536870912|
|메모리 최적화|4|262144|128|1073741824|
|메모리 최적화|8|262144|128|2147483648|
|메모리 최적화|16|262144|128|4294967295|
|메모리 최적화|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**가격 책정 계층**|**vCore**|**기본값**|**최솟값**|**최댓값**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|범용|2|300|10|600|
|범용|4|625|10|1250|
|범용|8|1250|10|2500|
|범용|16|2500|10|5,000|
|범용|32|5,000|10|10000|
|범용|64|10000|10|20000|
|메모리 최적화|2|625|10|1250|
|메모리 최적화|4|1250|10|2500|
|메모리 최적화|8|2500|10|5,000|
|메모리 최적화|16|5,000|10|10000|
|메모리 최적화|32|10000|10|20000|

연결 한도를 초과하면 다음과 같은 오류가 발생할 수 있습니다.
> 오류 1040(08004): 연결이 너무 많음

> [!IMPORTANT]
> 최상의 환경을 위해 ProxySQL과 같은 연결 풀을 사용하여 연결을 효율적으로 관리하는 것이 좋습니다.

MySQL에 대한 새 클라이언트 연결을 만들려면 시간이 필요하며, 일단 설정되면 이러한 연결은 유휴 상태일 때에도 데이터베이스 리소스를 차지합니다. 대부분의 애플리케이션은 많은 단기 연결을 요청합니다. 이는 이러한 상황을 복잡하게 만듭니다. 결과적으로 실제 워크로드에 사용할 수 있는 리소스가 줄어들어 성능이 저하됩니다. 유휴 연결을 줄이고 기존 연결을 다시 사용하는 연결 풀러는 이러한 문제를 방지하는 데 도움이 됩니다. ProxySQL 설정에 대해 알아보려면 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)을 방문하세요.

>[!Note]
>ProxySQL은 오픈 소스 커뮤니티 도구입니다. Microsoft에서 최상의 노력으로 지원 됩니다. 신뢰할 수 있는 지침을 사용 하 여 프로덕션 지원을 받으려면 [Proxysql 제품 지원 서비스](https://proxysql.com/services/support/)를 평가 하 고이에 도달할 수 있습니다.

### <a name="max_heap_table_size"></a>max_heap_table_size

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size)를 참조하세요.

|**가격 책정 계층**|**vCore**|**기본값 (바이트)**|**Min value (바이트)**|**Max 값 (바이트)**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|16777216|16384|268435455|
|범용|4|16777216|16384|536870912|
|범용|8|16777216|16384|1073741824|
|범용|16|16777216|16384|2147483648|
|범용|32|16777216|16384|4294967295|
|범용|64|16777216|16384|4294967295|
|메모리 최적화|2|16777216|16384|536870912|
|메모리 최적화|4|16777216|16384|1073741824|
|메모리 최적화|8|16777216|16384|2147483648|
|메모리 최적화|16|16777216|16384|4294967295|
|메모리 최적화|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

쿼리 캐시는 기본적으로 해제되어 있습니다. 쿼리 캐시를 사용하도록 설정하려면 `query_cache_type` 매개 변수를 구성합니다. 

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size)를 참조하세요.

> [!NOTE]
> 쿼리 캐시는 MySQL 5.7.20부터 더 이상 사용되지 않으며 MySQL 8.0에서 제거되었습니다.

|**가격 책정 계층**|**vCore**|**기본값 (바이트)**|**Min value (바이트)**|* * 최대 값 * *|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|0|0|16777216|
|범용|4|0|0|33554432|
|범용|8|0|0|67108864|
|범용|16|0|0|134217728|
|범용|32|0|0|134217728|
|범용|64|0|0|134217728|
|메모리 최적화|2|0|0|33554432|
|메모리 최적화|4|0|0|67108864|
|메모리 최적화|8|0|0|134217728|
|메모리 최적화|16|0|0|134217728|
|메모리 최적화|32|0|0|134217728|

### <a name="lower_case_table_names"></a>lower_case_table_names

Lower_case_table_name은 기본적으로 1로 설정 되며 MySQL 5.6 및 MySQL 5.7에서이 매개 변수를 업데이트할 수 있습니다.

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names)를 참조하세요.

> [!NOTE]
> MySQL 8.0에서 lower_case_table_name는 기본적으로 1로 설정 되며 변경할 수 없습니다.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

"행 크기 너무 큼 (> 8126)"과 유사한 오류가 표시 되는 경우 매개 변수 **innodb_strict_mode**를 해제할 수 있습니다. 행 데이터 크기가 8k 보다 크면 서버 매개 변수 **innodb_strict_mode** 를 서버 수준에서 전역적으로 수정할 수 없습니다. 데이터가 손실 될 수 있으므로 오류가 발생 하지 않고 데이터가 잘립니다. 페이지 크기 제한에 맞게 스키마를 수정 하는 것이 좋습니다. 

이 매개 변수는를 사용 하 여 세션 수준에서 설정할 수 있습니다 `init_connect` . 세션 수준에서 **innodb_strict_mode** 설정 하려면 [나열 되지 않은 매개 변수 설정](https://docs.microsoft.com/azure/mysql/howto-server-parameters#setting-parameters-not-listed)을 참조 하세요.

> [!NOTE]
> 복제 서버를 복제 하는 경우 마스터 서버의 세션 수준에서 **innodb_strict_mode** 을 OFF로 설정 하면 복제가 중단 됩니다. 복제본을 읽은 경우 매개 변수를 OFF로 설정 하는 것이 좋습니다.

### <a name="sort_buffer_size"></a>sort_buffer_size

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size)를 참조하세요.

|**가격 책정 계층**|**vCore**|**기본값 (바이트)**|**Min value (바이트)**|**Max 값 (바이트)**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|524288|32768|4194304|
|범용|4|524288|32768|8388608|
|범용|8|524288|32768|16777216|
|범용|16|524288|32768|33554432|
|범용|32|524288|32768|33554432|
|범용|64|524288|32768|33554432|
|메모리 최적화|2|524288|32768|8388608|
|메모리 최적화|4|524288|32768|16777216|
|메모리 최적화|8|524288|32768|33554432|
|메모리 최적화|16|524288|32768|33554432|
|메모리 최적화|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size)를 참조하세요.

|**가격 책정 계층**|**vCore**|**기본값 (바이트)**|**Min value (바이트)**|**Max 값 (바이트)**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|16777216|1024|67108864|
|범용|4|16777216|1024|134217728|
|범용|8|16777216|1024|268435456|
|범용|16|16777216|1024|536870912|
|범용|32|16777216|1024|1073741824|
|범용|64|16777216|1024|1073741824|
|메모리 최적화|2|16777216|1024|134217728|
|메모리 최적화|4|16777216|1024|268435456|
|메모리 최적화|8|16777216|1024|536870912|
|메모리 최적화|16|16777216|1024|1073741824|
|메모리 최적화|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

초기 배포 시 Azure for MySQL 서버는 표준 시간대 정보에 대 한 시스템 테이블을 포함 하지만 이러한 테이블은 채워지지 않습니다. MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `mysql.az_load_timezone` 저장 프로시저를 호출하여 표준 시간대 테이블을 채울 수 있습니다. 저장 프로시저를 호출하고 글로벌 또는 세션 수준 표준 시간대를 설정하는 방법은 [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) 문서를 참조하세요.

## <a name="non-configurable-server-parameters"></a>구성 불가능한 서버 매개 변수

아래 서버 매개 변수는 서비스에서 구성할 수 없습니다.

|**매개 변수**|**고정 값**|
| :------------------------ | :-------- |
|기본 계층의 innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

여기에 나열 되지 않은 다른 변수는 기본 MySQL 기본 값으로 설정 됩니다. 기본값은 버전 [8.0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html), [5.7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)및 [5.6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) 에 대 한 MySQL 문서를 참조 하세요. 

## <a name="next-steps"></a>다음 단계

- [Azure Portal를 사용 하 여 서버 매개 변수를 구성](./howto-server-parameters.md) 하는 방법을 알아봅니다.
- [Azure CLI를 사용 하 여 서버 매개 변수를 구성](./howto-configure-server-parameters-using-cli.md) 하는 방법을 알아봅니다.
- [PowerShell을 사용 하 여 서버 매개 변수를 구성](./howto-configure-server-parameters-using-powershell.md) 하는 방법 알아보기
