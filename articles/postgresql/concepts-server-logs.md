---
title: 로그-Azure Database for PostgreSQL-단일 서버
description: Azure Database for PostgreSQL 단일 서버에서 로깅 구성, 저장 및 분석에 대해 설명 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844941"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL의 로그-단일 서버
Azure Database for PostgreSQL를 사용 하 여 Postgres의 표준 로그를 구성 하 고 액세스할 수 있습니다. 로그를 사용 하 여 구성 오류를 식별 하 고, 문제를 해결 하 고, 문제를 해결할 수 있습니다. 구성 및 액세스할 수 있는 로깅 정보에는 오류, 쿼리 정보, autovacuum 레코드, 연결 및 검사점이 포함 됩니다. (트랜잭션 로그에 대 한 액세스를 사용할 수 없습니다.)

감사 로깅은 Postgres 확장 (pgaudit)을 통해 사용할 수 있습니다. 자세히 알아보려면 [감사 개념](concepts-audit.md) 문서를 참조 하세요.


## <a name="configure-logging"></a>로깅 구성 
로깅 서버 매개 변수를 사용 하 여 서버에서 Postgres 표준 로깅을 구성할 수 있습니다. 각 Azure Database for PostgreSQL 서버에서 `log_checkpoints` 및 `log_connections`은 기본적으로 설정 되어 있습니다. 로깅 요구에 맞게 매개 변수를 추가로 조정할 수도 있습니다. 

![Azure Database for PostgreSQL - 로깅 매개 변수](./media/concepts-server-logs/log-parameters.png)

Postgres 로그 매개 변수에 대 한 자세한 내용을 보려면 Postgres 설명서의 [시기를 기록](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) 하는 시기 및 [로깅할 항목](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) 섹션을 참조 하세요. Azure Database for PostgreSQL에서 대부분의 Postgres 로깅 매개 변수를 구성할 수 있습니다.

Azure Database for PostgreSQL에서 매개 변수를 구성 하는 방법에 대 한 자세한 내용은 [포털 설명서](howto-configure-server-parameters-using-portal.md) 또는 [CLI 설명서](howto-configure-server-parameters-using-cli.md)를 참조 하세요. 

> [!NOTE]
> 문 로깅과 같이 많은 양의 로그를 구성 하면 상당한 성능 오버 헤드를 추가할 수 있습니다. 

## <a name="access-log-files"></a>로그 파일 액세스
Azure Database for PostgreSQL의 기본 로그 형식은 .log입니다. 이 로그의 샘플 줄은 다음과 같습니다.

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL는 .log 파일에 대 한 단기 저장 위치를 제공 합니다. 새 파일은 1 시간 마다 또는 100 MB 중 먼저 도달 하는 것으로 시작 됩니다. 로그는 Postgres에서 내보낼 때 현재 파일에 추가 됩니다.  

`log_retention_period` 매개 변수를 사용 하 여이 단기 로그 저장소에 대 한 보존 기간을 설정할 수 있습니다. 기본값은 3일이며 최대값은 7일입니다. 단기 저장소 위치는 최대 1gb의 로그 파일을 보유할 수 있습니다. 1gb 후에는 새 로그를 위한 공간을 확보 하기 위해 보존 기간에 관계 없이 가장 오래 된 파일이 삭제 됩니다. 

로그 및 로그 분석에 대 한 장기 보존을 위해 .log 파일을 다운로드 하 고 타사 서비스로 이동할 수 있습니다. [Azure CLI](howto-configure-server-logs-using-cli.md) [Azure Portal](howto-configure-server-logs-in-portal.md)를 사용 하 여 파일을 다운로드할 수 있습니다. 또는 로그 (JSON 형식)를 장기 위치로 자동으로 내보내는 Azure Monitor 진단 설정을 구성할 수 있습니다. 이 옵션에 대 한 자세한 내용은 아래 섹션을 참조 하세요. 

매개 변수 `logging_collector`을 OFF로 설정 하 여 .log 파일 생성을 중지할 수 있습니다. Azure Monitor 진단 설정을 사용 하는 경우 로그 파일 생성을 해제 하는 것이 좋습니다. 이 구성을 통해 추가 로깅의 성능 영향을 줄일 수 있습니다.

## <a name="diagnostic-logs"></a>진단 로그
Azure Database for PostgreSQL은 Azure Monitor 진단 설정과 통합 됩니다. 진단 설정을 사용 하면 분석 및 경고를 위해 Postgres 로그를 JSON 형식으로 Azure Monitor 하 고, 스트리밍 Event Hubs 하 고, 보관에 Azure Storage 보낼 수 있습니다. 

> [!IMPORTANT]
> 이 서버 로그에 대한 진단 기능은 범용 및 메모리 최적화 [가격 책정 계층](concepts-pricing-tiers.md)에서만 사용할 수 있습니다.


### <a name="configure-diagnostic-settings"></a>진단 설정 구성
Azure Portal, CLI, REST API 및 Powershell을 사용 하 여 Postgres server에 대 한 진단 설정을 사용 하도록 설정할 수 있습니다. 선택할 로그 범주는 **PostgreSQLLogs**입니다. [쿼리 저장소](concepts-query-store.md)를 사용 하는 경우 구성할 수 있는 다른 로그가 있습니다.

Azure Portal를 사용 하 여 진단 로그를 사용 하도록 설정 하려면

   1. 포털에서 Postgres server의 탐색 메뉴에 있는 *진단 설정* 으로 이동 합니다.
   2. *진단 설정 추가*를 선택 합니다.
   3. 이 설정의 이름을로 설정 합니다. 
   4. 기본 설정 엔드포인트 (저장소 계정, 이벤트 허브, log analytics)을 선택 합니다. 
   5. **PostgreSQLLogs**로그 유형을 선택 합니다.
   7. 설정을 저장합니다.

Powershell, CLI 또는 REST API를 사용 하 여 진단 로그를 사용 하도록 설정 하려면 [진단 설정](../azure-monitor/platform/diagnostic-settings.md) 문서를 참조 하세요.

### <a name="access-diagnostic-logs"></a>진단 로그 액세스

로그에 액세스 하는 방법은 선택한 엔드포인트에 따라 다릅니다. Azure Storage는 [로그 저장소 계정](../azure-monitor/platform/resource-logs-collect-storage.md) 문서를 참조 하세요. Event Hubs에 대해서는 [Stream Azure logs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 문서를 참조 하세요.

Azure Monitor 로그의 경우 로그는 선택한 작업 영역으로 전송 됩니다. Postgres 로그 **는 azurediagnostics 수집 모드** 를 사용 하므로 azurediagnostics 테이블에서 쿼리할 수 있습니다. 테이블의 필드는 아래에 설명 되어 있습니다. [Azure Monitor 로그 쿼리](../azure-monitor/log-query/log-query-overview.md) 개요의 쿼리 및 경고에 대해 자세히 알아보세요.

다음은 시작 하기 위해 시도할 수 있는 쿼리입니다. 쿼리를 기반으로 경고를 구성할 수 있습니다.

마지막 날에 특정 서버에 대 한 모든 Postgres 로그를 검색 합니다.
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

모든 localhost가 아닌 연결 시도를 검색 합니다.
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
위의 쿼리는이 작업 영역에 있는 Postgres 서버 로깅에 대해 최근 6 시간 동안의 결과를 표시 합니다.

### <a name="log-format"></a>로그 형식

다음 표에서는 **PostgreSQLLogs** 형식에 대 한 필드를 설명 합니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 선택한 출력 엔드포인트에 따라 달라질 수 있습니다. 

|**필드** | **설명** |
|---|---|
| TenantId | 테넌트 ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
| 유형 | 로그의 형식 항상 `AzureDiagnostics` |
| SubscriptionId | 서버가 속한 구독의 GUID |
| ResourceGroup | 서버가 속한 리소스 그룹의 이름 |
| ResourceProvider | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | 리소스 URI |
| 리소스 | 서버의 이름 |
| 범주 | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | 로깅 수준(예: LOG, ERROR, NOTICE) |
| 메시지 | 기본 로그 메시지 | 
| 도메인 | 서버 버전(예: postgres-10) |
| 세부 정보 | 보조 로그 메시지(해당하는 경우) |
| ColumnName | 열 이름(해당하는 경우) |
| SchemaName | 스키마 이름(해당하는 경우) |
| DatatypeName | 데이터 형식 이름(해당하는 경우) |
| LogicalServerName | 서버의 이름 | 
| _ResourceId | 리소스 URI |
| 접두사 | 로그 줄의 접두사 |


## <a name="next-steps"></a>다음 단계
- [Azure Portal](howto-configure-server-logs-in-portal.md) 또는 [Azure CLI](howto-configure-server-logs-using-cli.md)에서 로그에 액세스하는 방법에 대해 자세히 알아봅니다.
- [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/)에 대해 자세히 알아봅니다.
- [감사 로그](concepts-audit.md) 에 대해 자세히 알아보기
