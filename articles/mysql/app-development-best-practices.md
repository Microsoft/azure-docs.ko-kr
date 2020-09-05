---
title: 앱 개발 모범 사례-Azure Database for MySQL
description: Azure Database for MySQL를 사용 하 여 앱을 빌드하기 위한 모범 사례에 대해 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 93bd6972a89065832a20fbd66949cde5b7510534
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794207"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>Azure Database for MySQL를 사용 하 여 응용 프로그램을 빌드하기 위한 모범 사례 

Azure Database for MySQL를 사용 하 여 클라우드 지원 응용 프로그램을 빌드하는 데 도움이 되는 몇 가지 모범 사례는 다음과 같습니다. 이러한 모범 사례는 앱에 대 한 개발 시간을 줄일 수 있습니다. 

## <a name="configuration-of-application-and-database-resources"></a>응용 프로그램 및 데이터베이스 리소스 구성

### <a name="keep-the-application-and-database-in-the-same-region"></a>응용 프로그램과 데이터베이스를 동일한 지역에 유지
Azure에서 응용 프로그램을 배포할 때 모든 종속성이 동일한 지역에 있는지 확인 합니다. 영역 또는 가용성 영역에 걸쳐 인스턴스를 분산 하는 경우 네트워크 대기 시간이 만들어지므로 응용 프로그램의 전반적인 성능에 영향을 줄 수 있습니다. 

### <a name="keep-your-mysql-server-secure"></a>MySQL 서버를 안전 하 게 유지
MySQL 서버를 안전 하 게 [보호](https://docs.microsoft.com/azure/mysql/concepts-security) 하 고 공개적으로 액세스할 수 없도록 구성 합니다. 다음 옵션 중 하나를 사용 하 여 서버를 보호 합니다. 
- [방화벽 규칙](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)
- [가상 네트워크](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) 
- [Azure Private Link](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

보안을 위해 항상 SSL을 통해 MySQL 서버에 연결 하 고 MySQL 서버 및 응용 프로그램에서 TLS 1.2를 사용 하도록 구성 해야 합니다. [SSL/TLS를 구성 하는 방법을](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security)참조 하세요. 

### <a name="tune-your-server-parameters"></a>서버 매개 변수 조정
읽기 작업이 많은 워크 로드의 경우 서버 매개 변수를 조정 `tmp_table_size` 하는 `max_heap_table_size` 데 도움이 되며 성능을 향상 시킬 수 있습니다. 이러한 변수에 필요한 값을 계산 하려면 총 연결당 메모리 값 및 기본 메모리를 확인 합니다. `tmp_table_size`서버의 총 메모리에 대 한 기본 메모리 계정과 결합 된 연결별 메모리 별 매개 변수의 합입니다.

및의 가능한 최대 크기를 계산 `tmp_table_size` 하려면 `max_heap_table_size` 다음 수식을 사용 합니다.

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> 총 메모리는 서버가 프로 비전 된 vCores를 통해 보유 하 고 있는 총 메모리 양을 나타냅니다.  예를 들어 범용 2 vCore Azure Database for MySQL 서버에서 총 메모리는 5gb * 2가 됩니다. [가격 책정 계층](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) 설명서에서 각 계층에 대 한 메모리에 대 한 자세한 정보를 찾을 수 있습니다.
>
> 기본 메모리는 `query_cache_size` `innodb_buffer_pool_size` MySQL이 서버 시작 시 초기화 하 고 할당 하는 및와 같은 메모리 변수를 나타냅니다. 및와 같은 연결 별 메모리 `sort_buffer_size` 는 `join_buffer_size` 쿼리에 필요한 경우에만 할당 되는 메모리입니다.

### <a name="create-non-admin-users"></a>비관리자 사용자 만들기 
각 데이터베이스에 대해 [관리자가 아닌 사용자를 만듭니다](https://docs.microsoft.com/azure/mysql/howto-create-users) . 일반적으로 사용자 이름은 데이터베이스 이름으로 식별 됩니다.

### <a name="reset-your-password"></a>암호 재설정
Azure Portal를 사용 하 여 MySQL server에 대 한 [암호를 다시 설정할](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) 수 있습니다. 

프로덕션 데이터베이스에 대 한 서버 암호를 다시 설정 하면 응용 프로그램을 종료할 수 있습니다. 응용 프로그램의 사용자에 대 한 영향을 최소화 하기 위해 사용량이 적은 시간에 프로덕션 워크 로드에 대 한 암호를 다시 설정 하는 것이 좋습니다.

## <a name="performance-and-resiliency"></a>성능 및 복원 력 
응용 프로그램의 성능 문제를 디버그 하는 데 사용할 수 있는 몇 가지 도구와 사례는 다음과 같습니다.

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>성능 문제를 식별 하기 위해 느리게 쿼리 로그 사용
서버에서 [느리게 쿼리 로그](https://docs.microsoft.com/azure/mysql/concepts-server-logs) 및 [감사 로그](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) 를 사용 하도록 설정할 수 있습니다. 느리게 쿼리 로그를 분석 하면 문제 해결에 대 한 성능 병목 상태를 식별할 수 있습니다. 

감사 로그는 Azure Monitor 로그, Azure Event Hubs 및 저장소 계정의 Azure 진단 로그를 통해 사용할 수도 있습니다. [쿼리 성능 문제를 해결 하는 방법을](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)참조 하세요.

### <a name="use-connection-pooling"></a>연결 풀링 사용
데이터베이스 연결을 관리 하면 전체적으로 응용 프로그램의 성능에 상당한 영향을 줄 수 있습니다. 성능을 최적화 하려면 연결이 설정 되는 횟수와 키 코드 경로에서 연결을 설정 하는 시간을 줄여야 합니다. [연결 풀링을](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) 사용 하 여 Azure Database for MySQL에 연결 하면 복원 력 및 성능을 향상 시킬 수 있습니다. 

[Proxysql](https://proxysql.com/) 연결 풀을 사용 하 여 연결을 효율적으로 관리할 수 있습니다. 연결 풀을 사용 하면 유휴 연결을 줄이고 기존 연결을 다시 사용 하 여 문제를 방지할 수 있습니다. 자세히 알아보려면 [ProxySQL을 설정 하는 방법](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) 을 참조 하세요. 

### <a name="retry-logic-to-handle-transient-errors"></a>일시적인 오류를 처리 하는 재시도 논리
응용 프로그램에서 데이터베이스에 대 한 연결이 일시적으로 삭제 되거나 손실 되는 [일시적인 오류가](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) 발생할 수 있습니다. 이러한 경우 서버는 5 ~ 10 초 내에 1 ~ 2 회 다시 시도 된 후에 실행 됩니다. 

첫 번째 재시도 전에 5 초간 대기 하는 것이 좋습니다. 그런 다음 각 다시 시도를 수행 하 고, 최대 60 초까지 대기를 늘립니다. 응용 프로그램에서 작업에 실패 한 것으로 간주 하는 최대 다시 시도 횟수를 제한 하 여 추가 조사를 수행할 수 있습니다. 자세히 알아보려면 [연결 오류 문제를 해결 하는 방법](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) 을 참조 하세요. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>장애 조치 (failover)를 완화 하기 위해 읽기 복제 사용
장애 조치 (failover) 시나리오에 [입력 데이터 복제](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) 를 사용할 수 있습니다. 읽기 복제본을 사용 하는 경우 마스터 서버와 복제 서버 간에 자동 장애 조치 (failover)가 수행 되지 않습니다. 

복제는 비동기 이기 때문에 마스터와 복제본 사이에 지연이 발생 합니다. 네트워크 지연은 마스터 서버에서 실행 되는 워크 로드의 크기 및 데이터 센터 간의 대기 시간 등 여러 가지 요인에 의해 영향을 받을 수 있습니다. 대부분의 경우 복제본 지연 범위는 몇 초에서 몇 분까지입니다.

## <a name="database-deployment"></a>데이터베이스 배포 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>CI/CD 배포 파이프라인에서 MySQL 용 Azure database 작업 구성
경우에 따라 데이터베이스에 변경 내용을 배포 해야 합니다. 이러한 경우 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) 를 통해 CI (지속적인 통합) 및 CD (지속적인 업데이트)를 사용 하 고 [MySQL server](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) 에 대 한 작업을 사용 하 여 사용자 지정 스크립트를 실행 하 여 데이터베이스를 업데이트할 수 있습니다.

### <a name="use-an-effective-process-for-manual-database-deployment"></a>수동 데이터베이스 배포에 효과적인 프로세스 사용 
수동 데이터베이스 배포 중에 다음 단계를 수행 하 여 가동 중지 시간을 최소화 하거나 배포 실패의 위험을 줄입니다. 

1. [Mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) 또는 [MySQL 워크 벤치](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html)를 사용 하 여 새 데이터베이스에 프로덕션 데이터베이스의 복사본을 만듭니다. 
2. 새 데이터베이스를 새 스키마 변경 내용으로 업데이트 하거나 데이터베이스에 필요한 업데이트를 업데이트 합니다. 
3. 프로덕션 데이터베이스를 읽기 전용 상태로 전환 합니다. 배포가 완료 될 때까지 프로덕션 데이터베이스에 대 한 쓰기 작업이 없어야 합니다. 
4. 1 단계의 새로 업데이트 된 데이터베이스를 사용 하 여 응용 프로그램을 테스트 합니다.
5. 응용 프로그램 변경 내용을 배포 하 고 응용 프로그램이 현재 최신 업데이트가 있는 새 데이터베이스를 사용 하 고 있는지 확인 합니다. 
6. 변경 사항을 롤백할 수 있도록 이전 프로덕션 데이터베이스를 유지 합니다. 그런 다음 이전 프로덕션 데이터베이스를 삭제 하거나 필요한 경우 Azure Storage에서 내보낼 수 있습니다. 

>[!NOTE]
>응용 프로그램이 전자 상거래 앱과 비슷하며 읽기 전용 상태로 전환할 수 없는 경우 백업을 수행한 후 프로덕션 데이터베이스에 직접 변경 내용을 배포 합니다. 일부 사용자는 실패 한 요청을 경험할 수 있으므로 앱에 대 한 트래픽이 낮은 사용량이 적은 시간에 이러한 변경이 발생 하 여 영향을 최소화 해야 합니다. 
>
>응용 프로그램 코드가 실패 한 모든 요청을 처리 하는지 확인 합니다.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>MySQL 기본 메트릭을 사용 하 여 워크 로드가 메모리 내 임시 테이블 크기를 초과 하는지 확인 합니다.
읽기 작업이 많은 작업을 수행 하는 경우 MySQL 서버에 대해 실행 되는 쿼리는 메모리 내 임시 테이블 크기를 초과할 수 있습니다. 읽기 작업이 많은 작업을 수행 하면 서버에서 디스크에 임시 테이블을 기록 하는 것으로 전환 하 여 응용 프로그램의 성능에 영향을 줄 수 있습니다. 임시 테이블 크기를 초과 하 여 서버가 디스크에 쓰고 있는지 확인 하려면 다음 메트릭을 확인 합니다.

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
`created_tmp_disk_tables`메트릭은 디스크에 생성 된 테이블 수를 나타냅니다. `created_tmp_table`메트릭은 작업을 통해 메모리에 형성 되어야 하는 임시 테이블의 수를 알려 줍니다. 특정 쿼리 실행이 임시 테이블을 사용 하는지 확인 하려면 쿼리에서 [설명](https://dev.mysql.com/doc/refman/8.0/en/explain.html) 문을 실행 합니다. 열의 세부 정보는 `extra` `Using temporary` 임시 테이블을 사용 하 여 쿼리를 실행할지 여부를 나타냅니다.

디스크로 분산 쿼리를 사용 하 여 워크 로드의 비율을 계산 하려면 다음 수식에서 메트릭 값을 사용 합니다.

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

이상적으로이 백분율은 25% 미만 이어야 합니다. 백분율이 25% 이상으로 표시 되는 경우 두 개의 서버 매개 변수 tmp_table_size 및 max_heap_table_size를 수정 하는 것이 좋습니다.

## <a name="database-schema-and-queries"></a>데이터베이스 스키마 및 쿼리

데이터베이스 스키마와 쿼리를 빌드할 때 유의 해야 할 몇 가지 팁은 다음과 같습니다.

### <a name="use-the-right-datatype-for-your-table-columns"></a>테이블 열에 적합 한 데이터 형식 사용
저장 하려는 데이터 형식에 따라 올바른 데이터 형식을 사용 하면 저장소를 최적화 하 고 잘못 된 데이터 형식으로 인해 발생할 수 있는 오류를 줄일 수 있습니다.

### <a name="use-indexes"></a>인덱스 사용
쿼리 속도가 느려지는 것을 방지 하기 위해 인덱스를 사용할 수 있습니다. 인덱스를 사용 하면 특정 열이 있는 행을 신속 하 게 찾을 수 있습니다. [MySQL에서 인덱스를 사용 하는 방법을](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html)참조 하세요.

### <a name="use-explain-for-your-select-queries"></a>SELECT 쿼리에 대 한 설명 사용
문을 사용 `EXPLAIN` 하 여 MySQL에서 쿼리를 실행 하는 방법에 대 한 정보를 얻습니다. 이를 통해 쿼리 병목 현상 또는 문제를 검색할 수 있습니다. [설명을 사용 하 여 쿼리 성능을 프로 파일링 하는 방법](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)을 참조 하세요.


