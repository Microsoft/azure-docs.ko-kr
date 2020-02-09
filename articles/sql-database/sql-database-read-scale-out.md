---
title: 복제본에 대 한 쿼리 읽기
description: Azure SQL Database는 읽기 확장 이라는 읽기 전용 복제본의 용량을 사용 하 여 읽기 전용 작업의 부하를 분산 하는 기능을 제공 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: f111b19eb07c218a9f3250ef3ffdb8a97cf07542
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420720"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>읽기 전용 복제본을 사용 하 여 읽기 전용 쿼리 작업 부하 분산

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

고가용성 [아키텍처](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)의 일부로 프리미엄 및 중요 비즈니스용 서비스 계층의 각 데이터베이스는 주 복제본과 여러 보조 복제본으로 자동으로 프로 비전 됩니다. 보조 복제본은 주 복제본과 동일한 계산 크기로 프로 비전 됩니다. 읽기 **확장** 기능을 사용 하면 읽기-쓰기 복제본을 공유 하는 대신 읽기 전용 복제본 중 하나의 용량을 사용 하 여 읽기 전용 작업 SQL Database 부하를 분산할 수 있습니다. 이러한 방식으로 읽기 전용 워크로드는 주 읽기-쓰기 작업에서 격리되고 해당 성능에 영향을 주지 않습니다. 이 기능은 분석과 같이 논리적으로 구분 된 읽기 전용 작업을 포함 하는 응용 프로그램을 위한 것입니다. 프리미엄 및 중요 비즈니스용 서비스 계층에서 응용 프로그램은 추가 비용 없이이 추가 용량을 사용 하 여 성능상의 이점을 얻을 수 있습니다.

**읽기 확장** 기능은 하나 이상의 보조 복제본이 생성 될 때 hyperscale 서비스 계층 에서도 사용할 수 있습니다. 읽기 전용 작업에 보조 복제본 하나에서 사용할 수 있는 것 보다 많은 리소스가 필요한 경우 보조 복제본을 여러 개 사용할 수 있습니다. Basic, Standard 및 범용 서비스 계층의 고가용성 아키텍처에는 복제본이 포함 되지 않습니다. 이러한 서비스 계층에서는 **읽기 확장** 기능을 사용할 수 없습니다.

다음 다이어그램에서는 중요 비즈니스용 데이터베이스를 사용 하는 방법을 보여 줍니다.

![읽기 전용 복제본](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

읽기 확장 기능은 새 Premium, 중요 비즈니스용 및 Hyperscale 데이터베이스에서 기본적으로 사용 하도록 설정 됩니다. Hyperscale의 경우 기본적으로 새 데이터베이스에 대해 하나의 보조 복제본이 생성 됩니다. SQL 연결 문자열이 `ApplicationIntent=ReadOnly`구성 된 경우 해당 데이터베이스의 읽기 전용 복제본에 대 한 응용 프로그램이 해당 데이터베이스의 읽기 전용 복제본으로 리디렉션됩니다. `ApplicationIntent` 속성을 사용 하는 방법에 대 한 자세한 내용은 [응용 프로그램 의도 지정](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)을 참조 하세요.

SQL 연결 문자열의 `ApplicationIntent` 설정에 관계 없이 응용 프로그램이 주 복제본에 연결 되도록 하려면 데이터베이스를 만들 때 또는 구성을 변경할 때 읽기 확장을 명시적으로 해제 해야 합니다. 예를 들어 데이터베이스를 Standard 또는 범용 계층에서 Premium, 중요 비즈니스용 또는 Hyperscale 계층으로 업그레이드 하 고 모든 연결이 주 복제본으로 계속 진행 되도록 하려면 읽기 확장을 사용 하지 않도록 설정 합니다. 사용 하지 않도록 설정 하는 방법에 대 한 자세한 내용은 [읽기 확장 사용 및 사용 안 함](#enable-and-disable-read-scale-out)을 참조 하세요.

> [!NOTE]
> 쿼리 데이터 저장소, 확장 이벤트, SQL Profiler 및 감사 기능은 읽기 전용 복제본에서 지원 되지 않습니다.

## <a name="data-consistency"></a>데이터 일관성

복제본의 이점 중 하나는 복제본이 트랜잭션 측면에서 항상 일관된 상태로 있지만 다른 시점에 서로 다른 복제본 간에 약간의 대기 시간이 있을 수 있다는 것입니다. 읽기 확장은 세션 수준 일관성을 지원합니다. 즉, 복제본을 사용할 수 없는 경우 발생 하는 연결 오류가 발생 한 후 읽기 전용 세션이 다시 연결 되 면 읽기-쓰기 복제본으로 최신 100%가 아닌 복제본으로 리디렉션될 수 있습니다. 마찬가지로 응용 프로그램에서 읽기/쓰기 세션을 사용 하 여 데이터를 쓰고 읽기 전용 세션을 사용 하 여 즉시 읽는 경우 최신 업데이트가 복제본에 즉시 표시 되지 않을 수 있습니다. 대기 시간은 비동기 트랜잭션 로그 다시 실행 작업으로 인해 발생 합니다.

> [!NOTE]
> 지역 내의 복제 대기 시간은 낮으며 이 상황은 드물게 발생합니다.

## <a name="connect-to-a-read-only-replica"></a>읽기 전용 복제본에 연결

데이터베이스에 대한 읽기 확장을 사용하도록 설정하면 클라이언트에서 제공하는 연결 문자열의 `ApplicationIntent` 옵션은 연결이 쓰기 복제본 또는 읽기 전용 복제본으로 라우팅되는지 여부를 나타냅니다. 특히 `ApplicationIntent` 값이 `ReadWrite`(기본값)이면 연결이 데이터베이스의 읽기/쓰기 복제본으로 전달됩니다. 이는 기존 동작과 동일합니다. `ApplicationIntent` 값이 `ReadOnly`이면 연결이 읽기 전용 복제본으로 라우팅됩니다.

예를 들어 다음 연결 문자열은 클라이언트를 읽기 전용 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

다음 연결 문자열 중 하나는 클라이언트를 읽기/쓰기 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>읽기 전용 복제본에 대한 연결인지 확인

다음 쿼리를 실행하여 읽기 전용 복제본에 연결되어 있는지 여부를 확인할 수 있습니다. 읽기 전용 복제본에 연결된 경우 READ_ONLY가 반환됩니다.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> ReadOnly 세션에서는 항상 AlwaysON 복제본 중 하나만 액세스할 수 있습니다.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>읽기 전용 복제본 모니터링 및 문제 해결

읽기 전용 복제본에 연결 된 경우 `sys.dm_db_resource_stats` DMV를 사용 하 여 성능 메트릭에 액세스할 수 있습니다. 쿼리 계획 통계에 액세스 하려면 `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` 및 `sys.dm_exec_sql_text` Dmv를 사용 합니다.

> [!NOTE]
> 논리 master 데이터베이스의 DMV `sys.resource_stats`는 주 복제본의 CPU 사용량 및 저장소 데이터를 반환 합니다.

## <a name="enable-and-disable-read-scale-out"></a>읽기 확장 사용 및 사용 안 함

읽기 확장은 프리미엄, 중요 비즈니스용 및 Hyperscale 서비스 계층에서 기본적으로 사용 하도록 설정 되어 있습니다. 기본, 표준 또는 범용 서비스 계층에서는 읽기 확장을 사용 하도록 설정할 수 없습니다. 읽기 확장은 0 개 복제본으로 구성 된 Hyperscale 데이터베이스에서 자동으로 비활성화 됩니다.

다음 방법을 사용 하 여 프리미엄 또는 중요 비즈니스용 서비스 계층의 단일 데이터베이스 및 탄력적 풀 데이터베이스에서 읽기 확장을 사용 하지 않도록 설정 하 고 다시 사용 하도록 설정할 수 있습니다.

> [!NOTE]
> 읽기 확장을 사용 하지 않도록 설정 하는 기능은 이전 버전과의 호환성을 위해 제공 됩니다.

### <a name="azure-portal"></a>Azure 포털

데이터베이스 **구성** 블레이드에서 읽기 확장 설정을 관리할 수 있습니다.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> Azure SQL Database에서 RM (PowerShell Azure Resource Manager) 모듈을 계속 사용할 수 있지만 향후의 모든 개발은 Az. Sql 모듈에 대 한 것입니다. AzureRM 모듈은 12 월 2020 일까 때까지 버그 수정을 계속 받습니다.  Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다. 호환성에 대 한 자세한 내용은 [새 Azure PowerShell Az Module 소개](/powershell/azure/new-azureps-module-az)를 참조 하세요.

Azure PowerShell에서 읽기 확장을 관리하려면 2016년 12월 Azure PowerShell 릴리스 이상이 필요합니다. 최신 PowerShell 버전은 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)을 참조하세요.

Azure PowerShell에서 읽기 확장을 사용 하지 않도록 설정 하거나 다시 사용 하도록 설정할 수 있습니다. `-ReadScale` 매개 변수에 대해 원하는 값 – `Enabled` 또는 `Disabled`를 전달 하 여 [AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet을 호출 합니다.

기존 데이터베이스에서 읽기 확장을 사용 하지 않도록 설정 하려면 다음을 수행 합니다 (꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제 함).

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

새 데이터베이스에서 읽기 확장을 사용 하지 않도록 설정 하려면 (꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호를 삭제 합니다.)

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

기존 데이터베이스에서 읽기 확장을 다시 사용 하도록 설정 하려면 다음을 수행 합니다 (꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제 함).

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

읽기 확장을 사용 하지 않도록 설정 된 데이터베이스를 만들거나 기존 데이터베이스의 설정을 변경 하려면 아래 샘플 요청에서와 같이 `readScale` 속성이 `Enabled` 또는 `Disabled`으로 설정 된 다음 메서드를 사용 합니다.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

자세한 내용은 [데이터베이스 - 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)를 참조하세요.

## <a name="using-tempdb-on-read-only-replica"></a>읽기 전용 복제본에서 TempDB 사용

TempDB 데이터베이스가 읽기 전용 복제본으로 복제 되지 않습니다. 각 복제본에는 복제본을 만들 때 생성 되는 고유 버전의 TempDB 데이터베이스가 있습니다. 이를 통해 TempDB를 업데이트할 수 있고 쿼리 실행 중에 수정할 수 있습니다. 읽기 전용 작업에 TempDB 개체를 사용 하는 경우에는 이러한 개체를 쿼리 스크립트의 일부로 만들어야 합니다.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>지역 복제 데이터베이스에서 읽기 확장 사용

장애 조치 (failover) 그룹의 구성원 인 경우와 같이 지역에서 복제 된 데이터베이스에서 읽기 확장을 사용 하 여 읽기 전용 작업 부하를 분산 하는 경우 주 데이터베이스와 지역에서 복제 된 보조 데이터베이스 모두에서 읽기 확장을 사용 하도록 설정 했는지 확인 합니다. 이 구성은 응용 프로그램이 장애 조치 (failover) 후 새 주 복제본에 연결 될 때 동일한 부하 분산 환경을 계속 유지 합니다. 읽기 확장을 사용할 수 있는 지역 복제된 보조 데이터베이스에 연결하는 경우, `ApplicationIntent=ReadOnly`가 있는 세션은 주 데이터베이스에서 연결을 라우팅하는 것과 동일한 방식으로 복제본 중 하나로 라우팅됩니다.  `ApplicationIntent=ReadOnly`가 없는 세션은 역시 읽기 전용인 지역 복제된 보조 데이터베이스의 주 복제본으로 라우팅됩니다. 지리적으로 복제 된 보조 데이터베이스는 주 데이터베이스와 다른 엔드포인트을 사용 하기 때문에 보조 데이터베이스에 액세스 하려면 `ApplicationIntent=ReadOnly`를 설정 하지 않아도 됩니다. 이전 버전과의 호환성을 보장하기 위해 `sys.geo_replication_links` DMV에 `secondary_allow_connections=2`(모든 클라이언트 연결이 허용됨)가 표시됩니다.

> [!NOTE]
> 보조 데이터베이스의 로컬 복제본 간에 라운드 로빈 또는 다른 부하 분산 라우팅이 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- SQL Database 하이퍼 확장 제공에 대 한 자세한 내용은 [hyperscale service 계층](./sql-database-service-tier-hyperscale.md)을 참조 하세요.
