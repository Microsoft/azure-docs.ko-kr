---
title: 감사 로깅-Azure Database for PostgreSQL 단일 서버
description: Azure Database for PostgreSQL의 pgAudit 감사 로깅에 대 한 개념-단일 서버.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842472"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL의 감사 로깅-단일 서버

Azure Database for PostgreSQL에서 데이터베이스 활동에 대 한 감사 로깅은 PostgreSQL Audit 확장: [Pgaudit](https://www.pgaudit.org/)를 통해 사용할 수 있습니다. pgAudit는 자세한 세션 및/또는 개체 감사 로깅을 제공 합니다.

> [!NOTE]
> pgAudit는 Azure Database for PostgreSQL 미리 보기 상태입니다.
> 확장은 범용 및 메모리 액세스에 최적화 된 서버 에서만 사용 하도록 설정할 수 있습니다.

계산 및 저장소 크기 조정과 같은 작업에 대 한 Azure 리소스 수준 로그를 원하는 경우 [Azure 활동 로그](../azure-monitor/platform/platform-logs-overview.md)를 참조 하세요.

## <a name="usage-considerations"></a>사용 고려 사항
기본적으로 pgAudit 로그 문은 Postgres의 표준 로깅 기능을 사용하여 일반 로그 문과 함께 내보내집니다. Azure Database for PostgreSQL에서 이러한 .log 파일은 Azure Portal 또는 CLI를 통해 다운로드할 수 있습니다. 파일 컬렉션에 대 한 최대 저장소는 1gb이 고 각 파일은 최대 7 일 동안 사용할 수 있습니다 (기본값은 3 일). 이 서비스는 단기 저장소 옵션입니다.

또는 Azure Monitor의 진단 로그 서비스에 내보내도록 모든 로그를 구성할 수 있습니다. Azure Monitor 진단 로깅을 사용 하는 경우 사용자의 선택에 따라 로그가 자동으로 (JSON 형식으로) Azure Storage, Event Hubs 및/또는 Azure Monitor 로그에 전송 됩니다.

pgAudit를 사용하도록 설정하면 서버에서 많은 양의 로깅이 생성되어 성능 및 로그 스토리지에 영향을 줍니다. Azure 진단 로그 서비스를 사용하는 것이 좋습니다. 이 서비스는 분석 및 경고 기능뿐만 아니라 장기적인 스토리지 옵션을 제공합니다. 추가 로깅의 성능 영향을 줄이려면 표준 로깅을 해제하는 것이 좋습니다.

   1. 매개 변수 `logging_collector`을 OFF로 설정 합니다. 
   2. 서버를 다시 시작 하 여이 변경 내용을 적용 합니다.

Azure Storage, Event Hubs 또는 Azure Monitor 로그에 대 한 로깅을 설정 하는 방법에 대 한 자세한 내용은 [서버 로그 문서의](concepts-server-logs.md)진단 로그 섹션을 참조 하세요.

## <a name="installing-pgaudit"></a>PgAudit 설치

PgAudit를 설치 하려면 서버 공유 미리 로드 라이브러리에이를 포함 해야 합니다. Postgres의 `shared_preload_libraries` 매개 변수를 변경 하려면 서버를 다시 시작 해야 적용 됩니다. [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)또는 [REST API](/rest/api/postgresql/configurations/createorupdate)를 사용 하 여 매개 변수를 변경할 수 있습니다.

[Azure portal](https://portal.azure.com) 사용:

   1. Azure Database for PostgreSQL 서버를 선택합니다.
   2. 사이드바에서 **서버 매개 변수**를 선택합니다.
   3. `shared_preload_libraries` 매개 변수를 검색합니다.
   4. **Pgaudit**를 선택 합니다.
   5. 서버를 다시 시작 하 여 변경 내용을 적용 합니다.

   6. 클라이언트 (예: psql)를 사용 하 여 서버에 연결 하 고 pgAudit 확장을 사용 하도록 설정 합니다.
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> 오류가 표시 되 면 `shared_preload_libraries`저장 한 후 서버를 다시 시작 했는지 확인 합니다.

## <a name="pgaudit-settings"></a>pgAudit 설정

pgAudit를 사용 하면 세션 또는 개체 감사 로깅을 구성할 수 있습니다. [세션 감사 로깅은](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) 실행 된 문의 자세한 로그를 내보냅니다. [개체 감사 로깅은](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) 특정 관계로 범위가 지정 된 감사입니다. 하나 또는 두 가지 로깅 유형을 설정 하도록 선택할 수 있습니다. 

> [!NOTE]
> pgAudit 설정은 gloabally로 지정 되며 데이터베이스 또는 역할 수준에서 지정할 수 없습니다.

[PgAudit를 설치한](#installing-pgaudit)후에는 해당 매개 변수를 구성 하 여 로깅을 시작할 수 있습니다. [Pgaudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) 는 각 매개 변수의 정의를 제공 합니다. 먼저 매개 변수를 테스트 하 고 예상 되는 동작을 확인 하 고 있는지 확인 합니다.

> [!NOTE]
> `pgaudit.log_client`을 ON으로 설정 하면 로그를 파일에 기록 하는 대신 클라이언트 프로세스 (예: psql)로 리디렉션합니다. 이 설정은 일반적으로 사용하지 않도록 설정해야 합니다. <br> <br>
> `pgaudit.log_level`은 `pgaudit.log_client` on 인 경우에만 사용할 수 있습니다.

> [!NOTE]
> Azure Database for PostgreSQL에서는 pgAudit 설명서에 설명 된 대로 `-` (빼기) 기호 바로 가기를 사용 하 여 `pgaudit.log`를 설정할 수 없습니다. 모든 필수 문 클래스(READ, WRITE 등)는 개별적으로 지정해야 합니다.

### <a name="audit-log-format"></a>감사 로그 형식
각 감사 항목은 로그 줄의 시작 부분 근처 `AUDIT:` 표시 됩니다. 항목의 나머지 형식은 [Pgaudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)에 자세히 설명 되어 있습니다.

감사 요구 사항을 충족 하는 다른 필드가 필요한 경우 Postgres 매개 변수 `log_line_prefix`를 사용 합니다. `log_line_prefix`은 모든 Postgres 로그 줄의 시작 부분에 출력 되는 문자열입니다. 예를 들어 다음 `log_line_prefix` 설정은 타임 스탬프, 사용자 이름, 데이터베이스 이름 및 프로세스 ID를 제공 합니다.

```
t=%m u=%u db=%d pid=[%p]:
```

`log_line_prefix`에 대해 자세히 알아보려면 [PostgreSQL 설명서](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)를 참조 하세요.

### <a name="getting-started"></a>시작
신속 하 게 시작 하려면 `pgaudit.log`를 `WRITE`로 설정 하 고 로그를 열어 출력을 검토 합니다. 

## <a name="viewing-audit-logs"></a>감사 로그 보기
.Log 파일을 사용 하는 경우 PostgreSQL 오류 로그와 동일한 파일에 감사 로그가 포함 됩니다. 로그 파일은 Azure [portal](howto-configure-server-logs-in-portal.md) 또는 [CLI](howto-configure-server-logs-using-cli.md)에서 다운로드할 수 있습니다. 

Azure 진단 로깅을 사용 하는 경우 로그에 액세스 하는 방법은 선택한 엔드포인트에 따라 다릅니다. Azure Storage는 [로그 저장소 계정](../azure-monitor/platform/resource-logs-collect-storage.md) 문서를 참조 하세요. Event Hubs에 대해서는 [Stream Azure logs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 문서를 참조 하세요.

Azure Monitor 로그의 경우 로그는 선택한 작업 영역으로 전송 됩니다. Postgres 로그 **는 azurediagnostics 수집 모드** 를 사용 하므로 azurediagnostics 테이블에서 쿼리할 수 있습니다. 테이블의 필드는 아래에 설명 되어 있습니다. [Azure Monitor 로그 쿼리](../azure-monitor/log-query/log-query-overview.md) 개요의 쿼리 및 경고에 대해 자세히 알아보세요.

이 쿼리를 사용 하 여 시작할 수 있습니다. 쿼리를 기반으로 경고를 구성할 수 있습니다.

마지막 날에 특정 서버에 대 한 모든 Postgres 로그를 검색 합니다.
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>다음 단계
- [Azure Database for PostgreSQL 로그인에 대해 알아보기](concepts-server-logs.md)
- [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)또는 [REST API](/rest/api/postgresql/configurations/createorupdate)를 사용 하 여 매개 변수를 설정 하는 방법을 알아봅니다.
