---
title: PostgreSQL용 Azure 데이터베이스의 서버 로그
description: 이 문서에서는 Azure Database for PostgreSQL에서 쿼리 및 오류 로그를 생성하는 방법과 로그 보존을 구성하는 방법을 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: 99deef907818ffdb1ce858c8e988e26cbd53a1a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871538"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL의 서버 로그 
Azure Database for PostgreSQL에서는 쿼리 및 오류 로그를 생성합니다. 쿼리 및 오류 로그는 구성 오류 및 최적 상태가 아닌 성능 문제를 식별하고, 문제를 해결하고, 복구하는 데 사용될 수 있습니다. 단, 트랜잭션 로그 액세스 권한은 포함되지 않습니다. 

## <a name="configure-logging"></a>로깅 구성 
로깅 서버 매개 변수를 사용하면 서버에서 로깅을 구성할 수 있습니다. 각 새 서버의 **log_checkpoints** 및 **log_connections**는 기본적으로 설정되어 있습니다. 로깅 요구에 맞게 매개 변수를 추가로 조정할 수도 있습니다. 

![Azure Database for PostgreSQL - 로깅 매개 변수](./media/concepts-server-logs/log-parameters.png)

이러한 매개 변수에 대한 자세한 내용은 PostgreSQL의 [오류 보고 및 로깅](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) 설명서를 참조하세요. Azure Database for PostgreSQL 매개 변수를 구성하는 방법을 알아보려면 [포털 설명서](howto-configure-server-parameters-using-portal.md) 또는 [CLI 설명서](howto-configure-server-parameters-using-cli.md)를 참조하세요.

## <a name="access-server-logs-through-portal-or-cli"></a>포털 또는 CLI를 통해 서버 로그 액세스
로그를 사용하도록 설정한 경우 [Azure Portal](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md) 및 Azure REST API를 사용하여 Azure Database for PostgreSQL 로그 저장소에서 로그에 액세스할 수 있습니다. 로그 파일은 1시간마다 또는 100MB 크기가 될 때마다(둘 중 먼저 도달할 때) 순환됩니다. 서버에 연결된  **log\_retention\_period** 매개 변수를 사용하여 이 로그 스토리지의 보존 기간을 설정할 수 있습니다. 기본값은 3일이며 최대값은 7일입니다. 로그 파일을 저장하기에 충분한 저장소를 서버에 할당해야 합니다. Azure 진단 로그는 이 보존 매개 변수를 통해 제어되지 않습니다.


## <a name="diagnostic-logs"></a>진단 로그
Azure Database for PostgreSQL은 Azure Monitor 진단 로그와 통합됩니다. PostgreSQL 서버에 로그를 설정한 후에 내보낸 것 하도록 선택할 수 있습니다 [Azure Monitor 로그](../azure-monitor/log-query/log-query-overview.md), Event Hubs 또는 Azure Storage. 진단 로그를 사용하도록 설정하는 방법에 대한 자세한 내용은 [진단 로그 설명서](../azure-monitor/platform/diagnostic-logs-overview.md)의 방법 섹션을 참조하세요. 

> [!IMPORTANT]
> 이 진단 서버 로그에 대 한 기능은 사용할 수 있습니다 범용 및 메모리 최적화 [가격 책정 계층](concepts-pricing-tiers.md)합니다.

아래 표에는 각 로그의 내용에 대한 설명이 나와 있습니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 선택한 출력 엔드포인트에 따라 달라질 수 있습니다. 

|**필드** | **설명** |
|---|---|
| TenantId | 테넌트 ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
| Type | 로그의 형식 항상 `AzureDiagnostics` |
| SubscriptionId | 서버가 속한 구독의 GUID |
| ResourceGroup | 서버가 속한 리소스 그룹의 이름 |
| ResourceProvider | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | 리소스 URI |
| 리소스 | 서버의 이름 |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | 로깅 수준(예: LOG, ERROR, NOTICE) |
| Message | 기본 로그 메시지 | 
| 도메인 | 서버 버전(예: postgres-10) |
| 세부 정보 | 보조 로그 메시지(해당하는 경우) |
| ColumnName | 열 이름(해당하는 경우) |
| SchemaName | 스키마 이름(해당하는 경우) |
| DatatypeName | 데이터 형식 이름(해당하는 경우) |
| LogicalServerName | 서버의 이름 | 
| _ResourceId | 리소스 URI |

## <a name="next-steps"></a>다음 단계
- [Azure Portal](howto-configure-server-logs-in-portal.md) 또는 [Azure CLI](howto-configure-server-logs-using-cli.md)에서 로그에 액세스하는 방법에 대해 자세히 알아봅니다.
- [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/)에 대해 자세히 알아봅니다.
