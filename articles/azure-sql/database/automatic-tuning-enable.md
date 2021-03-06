---
title: 자동 튜닝 사용
description: Azure Portal를 사용 하 여 데이터베이스에 대 한 자동 조정을 쉽게 설정할 수 있습니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/03/2021
ms.openlocfilehash: 1362d8c1f15b64b9d76b28fd354cdae8919504b0
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558281"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>Azure Portal에서 자동 조정 기능을 사용 하 여 쿼리를 모니터링 하 고 워크 로드 성능을 향상 시킵니다.
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database는 쿼리를 지속적으로 모니터링 하 고 워크 로드의 성능을 향상 시키기 위해 수행할 수 있는 작업을 식별 하는 데이터 서비스를 자동으로 관리 합니다. 권장 사항을 검토하고 수동으로 적용하거나 Azure SQL Database에서 정정 작업을 자동으로 적용하도록 할 수 있습니다. 이는 **자동 조정 모드** 로 알려져 있습니다.

다음을 통해 서버 또는 데이터베이스 수준에서 자동 조정을 사용 하도록 설정할 수 있습니다.

- [Azure Portal](automatic-tuning-enable.md#azure-portal)
- [REST API](automatic-tuning-enable.md#rest-api) 호출
- [T-sql](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) 명령

> [!NOTE]
> Azure SQL Managed Instance의 경우 지원 되는 옵션 FORCE_LAST_GOOD_PLAN [t-sql](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) 을 통해서만 구성할 수 있습니다. 이 문서에서 설명 하는 Azure Portal 기반 구성 및 자동 인덱스 튜닝 옵션은 Azure SQL Managed Instance에 적용 되지 않습니다.

> [!NOTE]
> ARM (Azure Resource Manager) 템플릿을 통해 자동 조정 옵션을 구성 하는 것은 현재 지원 되지 않습니다.

## <a name="enable-automatic-tuning-on-server"></a>서버에서 자동 조정 사용

서버 수준에서 "Azure 기본값"에서 자동 조정 구성을 상속하거나 구성을 상속하지 않도록 선택할 수 있습니다. Azure 기본값은 FORCE_LAST_GOOD_PLAN 사용 하도록 설정 되 고 CREATE_INDEX 비활성화 되며 DROP_INDEX 사용 하지 않도록 설정 됩니다.

> [!IMPORTANT]
> 2020 년 3 월을 기준으로 자동 조정에 대 한 새 Azure 기본값은 다음과 같습니다.
>
> - FORCE_LAST_GOOD_PLAN = enabled, CREATE_INDEX = disabled 및 DROP_INDEX = disabled입니다.
> - 자동 조정 기본 설정이 없는 기존 서버는 Azure 기본값을 상속 하도록 자동으로 구성 됩니다. 이는 정의 되지 않은 상태에서 자동 조정을 위한 서버 설정이 현재 있는 모든 고객에 게 적용 됩니다.
> - 새로 만든 서버는 Azure 기본값을 상속 하도록 자동으로 구성 됩니다 (이전에는 새 서버를 만들 때 자동 튜닝 구성이 정의 되지 않은 상태에 있는 경우와 다름).

### <a name="azure-portal"></a>Azure portal

Azure SQL Database [서버](logical-servers.md) 에서 자동 조정을 사용 하도록 설정 하려면 Azure Portal에서 서버로 이동한 다음 메뉴에서 **자동 조정** 을 선택 합니다.

![서버에 대 한 옵션을 적용할 수 있는 Azure Portal의 자동 튜닝을 보여 주는 스크린샷](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> 이 경우 **DROP_INDEX** 옵션은 파티션 전환 및 인덱스 힌트를 사용 하는 응용 프로그램과 호환 되지 않으므로 이러한 경우 사용 하도록 설정 하면 안 됩니다. Premium 및 중요 비즈니스용 서비스 계층에서는 사용 하지 않는 인덱스를 삭제할 수 없습니다.

사용 하도록 설정할 자동 조정 옵션을 선택 하 고 **적용** 을 선택 합니다.

서버의 자동 조정 옵션은 서버의 모든 데이터베이스에 적용됩니다. 기본적으로 모든 데이터베이스는 부모 서버에서 구성을 상속하지만 이는 각 데이터베이스에 대해 개별적으로 재정의되고 지정될 수 있습니다.

### <a name="rest-api"></a>REST API

REST API를 사용 하 여 **서버** 에서 자동 조정 기능을 사용 하는 방법에 대 한 자세한 내용은 [서버 자동 조정 업데이트 및 GET HTTP 메서드](/rest/api/sql/serverautomatictuning)를 참조 하세요.

## <a name="enable-automatic-tuning-on-an-individual-database"></a>개별 데이터베이스에서 자동 조정 활성화

Azure SQL Database를 사용 하 여 각 데이터베이스에 대 한 자동 조정 구성을 개별적으로 지정할 수 있습니다. 데이터베이스 수준에서 부모 서버, "Azure 기본값"에서 자동 조정 구성을 상속하거나 구성을 상속하지 않도록 선택할 수 있습니다. Azure 기본값은 FORCE_LAST_GOOD_PLAN 설정 되 고 CREATE_INDEX 사용 하지 않도록 설정 되며 DROP_INDEX 사용 하지 않도록 설정 됩니다.

> [!TIP]
> 일반적으로 **서버 수준** 에서 자동 조정 구성을 관리 하 여 모든 데이터베이스에 동일한 구성 설정을 자동으로 적용할 수 있도록 하는 것이 좋습니다. 데이터베이스가 동일한 서버에서 설정을 상속하는 다른 데이터베이스와 다른 설정을 가지도록 해야 하는 경우에만 개별 데이터베이스에서 자동 조정을 구성합니다.

### <a name="azure-portal"></a>Azure portal

**단일 데이터베이스** 에서 자동 조정을 사용 하도록 설정 하려면 Azure Portal의 데이터베이스로 이동 하 고 **자동 조정** 을 선택 합니다.

개별 자동 조정 설정을 각 데이터베이스에 대해 별도로 구성할 수 있습니다. 개별 자동 조정 옵션을 수동으로 구성하거나 서버에서 해당 설정을 상속하는 옵션을 지정할 수 있습니다.

![단일 데이터베이스에 대 한 옵션을 적용할 수 있는 Azure Portal의 자동 튜닝을 보여 주는 스크린샷](./media/automatic-tuning-enable/database.png)

현재 DROP_INDEX 옵션은 파티션 전환 및 인덱스 힌트를 사용하는 애플리케이션과 호환되지 않으므로 이러한 경우에는 사용하도록 설정해서는 안 됩니다.

원하는 구성을 선택한 후 **적용** 을 클릭합니다.

### <a name="rest-api"></a>Rest API

REST API를 사용 하 여 단일 데이터베이스에서 자동 조정 기능을 사용 하는 방법에 대 한 자세한 내용은 [Azure SQL Database 자동 조정 업데이트 및 GET HTTP 메서드](/rest/api/sql/databaseautomatictuning)를 참조 하세요.

### <a name="t-sql"></a>T-SQL

T-SQL을 통해 단일 데이터베이스에서 자동 조정을 활성화하려면 데이터베이스에 연결하고 다음 쿼리를 실행합니다.

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

자동 조정을 자동으로 설정하면 Azure 기본값을 적용합니다. 상속으로 설정하면 자동 조정 구성은 부모 서버에서 상속됩니다. 사용자 정의를 선택하면 자동 조정을 수동으로 구성해야 합니다.

T-SQL을 통해 개별 자동 조정 옵션을 구성하려면 데이터베이스에 연결하고 다음과 같은 쿼리를 실행합니다.

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

개별 튜닝 옵션을 ON으로 설정 하면 데이터베이스가 상속 된 모든 설정이 재정의 되 고 튜닝 옵션을 사용 하도록 설정 됩니다. OFF로 설정 하면 데이터베이스가 상속 된 모든 설정이 재정의 되 고 튜닝 옵션이 사용 되지 않도록 설정 됩니다. 기본값을 지정 하는 자동 조정 옵션은 서버 수준 설정에서 자동 조정 구성을 상속 합니다.  

> [!IMPORTANT]
> [활성 지역 복제](auto-failover-group-overview.md)의 경우 주 데이터베이스에만 자동 튜닝을 구성 해야 합니다. 예를 들어 index create 또는 delete와 같은 자동으로 적용 되는 튜닝 작업은 읽기 전용 보조 데이터베이스에 자동으로 복제 됩니다. 읽기 전용 보조 데이터베이스에 다른 튜닝 구성을 적용하는 것은 지원되지 않으므로, 읽기 전용 보조 데이터베이스에서 T-SQL을 통한 자동 튜닝을 사용하도록 설정하려고 하면 실패합니다.
>

자동 튜닝을 구성 하는 섹션인 T-sql 옵션에 대 한 자세한 내용은 [ALTER DATABASE SET 옵션 (transact-sql)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true)을 참조 하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="automated-recommendation-management-is-disabled"></a>자동화 된 권장 사항 관리 사용 안 함

자동화 된 권장 사항 관리를 사용 하지 않도록 설정 했거나 시스템에서 간단히 사용 하지 않도록 설정 하는 오류 메시지의 경우 가장 일반적인 원인은 다음과 같습니다.
- 쿼리 저장소를 사용할 수 없거나
- 지정 된 데이터베이스에 대 한 읽기 전용 모드 쿼리 저장소 또는
- 할당 된 저장소 공간을 사용 하 여 쿼리 저장소 실행이 중지 되었습니다.

다음 단계를 고려 하 여이 문제를 해결할 수 있습니다.
- T-sql을 사용 하 여 쿼리 저장소를 정리 하거나 데이터 보존 기간을 "auto"로 수정 하십시오. [쿼리 저장소에 대 한 권장 보존 및 캡처 정책을 구성](./query-performance-insight-use.md#recommended-retention-and-capture-policy)하는 방법을 참조 하세요.
- SSMS (SQL Server Management Studio)를 사용 하 고 다음 단계를 수행 합니다.
  - Azure SQL Database에 연결
  - 데이터베이스를 마우스 오른쪽 단추로 클릭 합니다.
  - 속성으로 이동 하 여 쿼리 저장소를 클릭 합니다.
  - 작업 모드를 Read-Write로 변경 합니다.
  - 저장소 캡처 모드를 자동으로 변경
  - 크기 기반 정리 모드를 자동으로 변경

### <a name="permissions"></a>권한

자동 튜닝은 Azure 기능 이므로,이 기능을 사용 하려면 Azure의 기본 제공 역할을 사용 해야 합니다. SQL 인증을 사용 하는 경우에만 Azure Portal 기능을 사용할 수 있는 것은 아닙니다.

자동 조정을 사용 하려면 사용자에 게 권한을 부여 하는 데 필요한 최소 권한이 Azure의 기본 제공 [SQL Database 기여자](../../role-based-access-control/built-in-roles.md#sql-db-contributor) 역할입니다. SQL Server 참여자, SQL Managed Instance 기여자, 참가자, 소유자 등의 더 높은 권한 역할을 사용 하는 것을 고려할 수도 있습니다.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>이메일 알림 자동 조정 구성

자동 조정으로 만들어진 권장 사항에 대 한 자동화 된 전자 메일 알림을 받으려면 [전자 메일 알림 자동 조정](automatic-tuning-email-notifications-configure.md) 가이드를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 자동 조정 및 성능을 개선할 수 있는 방법에 대한 자세한 내용은 [자동 조정 문서](automatic-tuning-overview.md)를 참조하세요.
- Azure SQL Database 성능 권장 사항에 대한 개요는 [성능 권장 사항](database-advisor-implement-performance-recommendations.md)을 참조하세요.
- 상위 쿼리의 성능에 미치는 영향을 알아보려면 [Query Performance Insights](query-performance-insight-use.md)를 참조하세요.