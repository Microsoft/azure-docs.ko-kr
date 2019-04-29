---
title: Azure Database for PostgreSQL의 읽기 복제본
description: 이 문서에서는 Azure Database for PostgreSQL의 읽기 복제본 기능을 설명합니다.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 04/01/2019
ms.date: 04/22/2019
ms.openlocfilehash: f340f1e42b6993a1f834ab05570c669d4241222b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564360"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL의 읽기 복제본

읽기 복제본 기능을 사용하면 Azure Database for PostgreSQL 서버에서 읽기 전용 서버로 데이터를 복제할 수 있습니다. 마스터 서버에서 동일한 Azure 지역 내에 있는 최대 5개의 복제본으로 복제할 수 있습니다. 복제본은 PostgreSQL 엔진 기본 복제 기술을 사용하여 비동기식으로 업데이트 됩니다.

복제본은 일반 Azure Database for PostgreSQL 서버와 비슷한 방식으로 관리하는 새로운 서버입니다. 읽기 복제본의 경우, vCore 및 스토리지에 프로비저닝된 컴퓨팅에 대한 비용이 GB/월 단위로 청구됩니다.

[복제본을 만들고 관리](howto-read-replicas-portal.md)하는 방법을 알아봅니다.

## <a name="when-to-use-a-read-replica"></a>읽기 복제본을 사용하는 경우
읽기 복제본 기능은 읽기 작업이 많은 워크로드의 성능과 규모를 향상시키는 데 도움이 됩니다. 읽기 워크로드를 복제본과 격리할 수 있고 쓰기 워크로드를 마스터로 보낼 수 있습니다.

일반적인 시나리오는 BI와 분석 워크로드가 보고를 위한 데이터 원본으로 읽기 복제본을 사용하도록 합니다.

복제본은 읽기 전용이기 때문에 마스터에 대한 쓰기 용량 부담을 직접적으로 줄이지는 못합니다. 이 기능은 쓰기 작업이 많은 워크로드를 대상으로 하지 않습니다.

읽기 복제본 기능은 PostgreSQL 동기식 복제를 사용합니다. 이 기능은 동기식 복제 시나리오를 위한 것이 아닙니다. 마스터와 복제본 간에는 측정 가능한 지연이 발생합니다. 복제본의 데이터는 결과적으로 마스터의 데이터와 일치하게 됩니다. 이러한 지연 시간을 수용할 수 있는 워크로드에 이 기능을 사용합니다.

## <a name="create-a-replica"></a>복제본 만들기
마스터 서버는 `azure.replication_support` 매개 변수를 **REPLICA**로 설정해야 합니다. 이 매개 변수가 변경되면, 서버를 다시 시작해야 변경 사항이 적용됩니다. (`azure.replication_support` 매개 변수는 범용 및 메모리 최적화 계층에만 적용됩니다).

복제본 만들기 워크플로를 시작하면 빈 Azure Database for PostgreSQL 서버가 만들어집니다. 새 서버는 마스터 서버에 있는 데이터로 채워집니다. 생성 시간은 마스터의 데이터 양과 지난 주 전체 백업 이후의 시간에 따라 달라집니다. 시간은 몇 분에서 몇 시간까지 걸릴 수 있습니다.

읽기 복제본 기능은 PostgreSQL의 물리적 복제(논리 복제 아님)를 사용합니다. 복제 슬롯을 사용하는 스트리밍 복제가 기본 작동 모드입니다. 필요한 경우 따라잡기 위해 로그 전달이 사용됩니다.

> [!NOTE]
> 서버에 스토리지 경고가 설정되어 있지 않은 경우 스토리지 경고를 설정하는 것이 좋습니다. 이 경고는 서버가 스토리지 용량 한도에 도달하면 이를 알려주며 복제에 영향을 미칩니다.

[Azure Portal에서 읽기 복제본을 만드는 방법](howto-read-replicas-portal.md)을 알아봅니다.

## <a name="connect-to-a-replica"></a>복제본에 연결
복제본을 만들면 마스터 서버의 방화벽 규칙 또는 VNet 서비스 엔드포인트를 상속하지 않습니다. 이러한 규칙은 복제본에 대해 별도로 설정해야 합니다.

복제본은 마스터 서버에서 해당 관리자 계정을 상속합니다. 마스터 서버의 모든 사용자 계정은 읽기 복제본으로 복제됩니다. 마스터 서버에서 사용 가능한 사용자 계정을 사용해야만 읽기 복제본에 연결할 수 있습니다.

일반 Azure Database for PostgreSQL 서버에서처럼 같이 해당 호스트 이름 및 유효한 사용자 계정을 사용하여 복제본에 연결할 수 있습니다. 관리 사용자 이름이 **myadmin**인 **myreplica**라는 서버의 경우 psql:을 사용하여 복제본에 연결할 수 있습니다.

```
psql -h myreplica.postgres.database.chinacloudapi.cn -U myadmin@myreplica -d postgres
```

프롬프트가 표시되면 사용자 계정의 암호를 입력합니다.

## <a name="monitor-replication"></a>복제 모니터링
Azure Database for PostgreSQL은 Azure Monitor에 **복제본 간 최대 지연 시간** 메트릭을 제공합니다. 이 메트릭은 마스터 서버에서만 사용할 수 있습니다. 이 메트릭은 마스터와 가장 오래 지연된 복제본 간의 지연 시간을 바이트 단위로 보여 줍니다. 

Azure Database for PostgreSQL은 Azure Monitor에 **복제본 지연 시간** 메트릭도 제공합니다. 이 메트릭은 복제본에만 사용할 수 있습니다. 

메트릭은 `pg_stat_wal_receiver` 보기에서 계산됩니다.

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```

지연 시간 메트릭은 마지막으로 재생된 트랜잭션 이후의 시간을 보여 줍니다. 마스터 서버에서 트랜잭션이 발생하지 않으면 메트릭은 이 지연 시간을 반영합니다.

복제본 지연 시간이 워크로드에 적합하지 않은 값에 도달하면 알리도록 경고를 설정하십시오. 

추가 인사이트를 얻으려면 마스터 서버를 직접 쿼리하여 모든 복제본의 복제 지연 시간(바이트)을 가져옵니다.

PostgreSQL 버전 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

PostgreSQL 버전 9.6 이전:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> 마스터 서버 또는 읽기 복제본이 다시 시작되면 다시 시작하여 따라잡는 데 걸리는 시간이 복제본 지연 시간 메트릭에 반영됩니다.

## <a name="stop-replication"></a>복제 중지
마스터 및 복제본 간의 복제를 중지할 수 있습니다. 중지 작업을 수행하면 복제본이 다시 시작되고 복제 설정이 제거됩니다. 마스터 서버와 읽기 복제본 사이에서 복제가 중지된 후에 복제본은 독립 실행형 서버가 됩니다. 독립 실행형 서버의 데이터는 복제 중지 명령이 시작될 때 복제본에서 사용할 수 있던 데이터입니다. 독립 실행형 서버는 마스터 서버를 따라잡지 않습니다.

> [!IMPORTANT]
> 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.
> 읽기 복제본에서 복제를 중지하기 전에 복제본에 필요한 모든 데이터가 있는지 확인하십시오.

[복제본에 대한 복제를 중지](howto-read-replicas-portal.md)하는 방법을 알아봅니다.


## <a name="considerations"></a>고려 사항

이 섹션에서는 읽기 복제본 기능의 고려 사항에 대한 요약이 제공됩니다.

### <a name="prerequisites"></a>필수 조건
읽기 복제본을 만들기 전에, 마스터 서버에서 `azure.replication_support` 매개 변수를 **REPLICA**로 설정해야 합니다. 이 매개 변수가 변경되면, 서버를 다시 시작해야 변경 사항이 적용됩니다. `azure.replication_support` 매개 변수는 범용 및 메모리 최적화 계층에만 적용됩니다.

### <a name="new-replicas"></a>새 복제본
읽기 복제본은 새로운 Azure Database for PostgreSQL 서버로 생성됩니다. 기존 서버를 복제본으로 만들 수 없습니다. 읽기 복제본은 마스터와 동일한 Azure 지역에서만 만들 수 있습니다. 다른 읽기 복제본의 복제본은 만들 수 없습니다.

### <a name="replica-configuration"></a>복제본 구성
복제본은 마스터와 같은 서버 구성을 사용하여 생성됩니다. 복제본을 만든 후에는 마스터 서버와는 별도로 컴퓨팅 생성, vCore, 스토리지 및 백업 보존 기간 등의 일부 설정을 변경할 수 있습니다. 가격 책정도 기본 계층에서 다른 계층으로 또는 다른 계층에서 기본 계층으로 변경하는 경우를 제외하고 독립적으로 변경할 수 있습니다.

> [!IMPORTANT]
> 마스터 서버 구성을 새 값으로 업데이트하기 전에 복제본의 구성을 같거나 더 큰 값으로 업데이트합니다. 이렇게 하면 복제본이 마스터에 대한 변경 내용을 유지할 수 있습니다.

PostgreSQL에서는 읽기 복제본의 `max_connections` 매개 변수 값이 마스터 값보다 크거나 같아야 합니다. 그렇지 않으면 해당 복제본이 시작되지 않습니다. Azure Database for PostgreSQL에서 `max_connections` 매개 변수 값은 SKU에 기반합니다. 자세한 내용은 [Azure Database for PostgreSQL의 제한](concepts-limits.md)을 참조하세요. 

서버 값을 업데이트하려고 해도 한도를 지키지 않으면 오류가 발생합니다.

### <a name="stopped-replicas"></a>중지된 복제본
마스터 서버와 읽기 복제본 간의 복제를 중지하면 복제본이 다시 시작되어 변경 내용이 적용됩니다. 중지된 복제본은 읽기와 쓰기를 모두 허용하는 독립 실행형 서버가 됩니다. 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.

### <a name="deleted-master-and-standalone-servers"></a>삭제된 마스터 및 독립 실행형 서버
마스터 서버를 삭제하면 이 서버의 모든 읽기 복제본이 독립 실행형 서버가 됩니다. 이 변경 내용을 반영하기 위해 복제본이 다시 시작됩니다.

## <a name="next-steps"></a>다음 단계
[Azure Portal에서 읽기 복제본을 만들고 관리하는 방법](howto-read-replicas-portal.md)을 알아봅니다.
