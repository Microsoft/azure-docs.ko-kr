---
title: Azure SQL Database에 대한 자동 조정 사용 | Microsoft Docs
description: Azure SQL Database에서 쉽게 자동 조정을 사용할 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: f94d731fd7b9a9fa85ae42d22949c7ca4024aabe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416512"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>자동 조정을 사용하여 쿼리 모니터링 및 워크로드 성능 향상

Azure SQL Database는 지속적으로 쿼리를 모니터링하고 워크로드의 성능 향상을 위해 수행할 수 있는 작업을 식별하는 자동으로 관리되는 데이터 서비스입니다. 권장 사항을 검토하고 수동으로 적용하거나 Azure SQL Database에서 정정 작업을 자동으로 적용하도록 할 수 있습니다. 이는 **자동 조정 모드**로 알려져 있습니다.

자동 조정은 [Azure Portal](sql-database-automatic-tuning-enable.md#azure-portal), [REST API](sql-database-automatic-tuning-enable.md#rest-api) 호출 및 [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) 명령을 통해 서버 또는 데이터베이스 수준에서 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 관리 되는 인스턴스 FORCE_LAST_GOOD_PLAN 지원 되는 옵션을 통해 구성할 수 있습니다 [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) 만 합니다. 포털 기반 구성 및 관리 되는 인스턴스를이 문서에서 설명 하는 자동 인덱스 튜닝 옵션이 적용 되지 않습니다.

> [!NOTE]
> ARM (Azure Resource Manager) 템플릿을 통해 자동 튜닝 옵션을 구성 하는 지금은 지원 되지 않습니다.

## <a name="enable-automatic-tuning-on-server"></a>서버에서 자동 조정 사용

서버 수준에서 "Azure 기본값"에서 자동 조정 구성을 상속하거나 구성을 상속하지 않도록 선택할 수 있습니다. Azure에서는 기본적으로 FORCE_LAST_GOOD_PLAN 및 CREATE_INDEX는 사용하고 DROP_INDEX는 사용하지 않도록 설정됩니다.

### <a name="azure-portal"></a>Azure portal

Azure SQL Database 논리 **서버**에서 자동 조정을 사용하려면 Azure Portal에서 서버로 이동한 다음, 메뉴에서 **자동 조정**을 선택합니다.

![서버](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> 현재 **DROP_INDEX** 옵션은 파티션 전환 및 인덱스 힌트를 사용하는 애플리케이션과 호환되지 않으므로 이러한 경우에는 사용하도록 설정해서는 안 됩니다.
>

사용하려는 자동 조정 옵션을 선택하고 **적용**을 선택합니다.

서버의 자동 조정 옵션은 서버의 모든 데이터베이스에 적용됩니다. 기본적으로 모든 데이터베이스는 부모 서버에서 구성을 상속하지만 이는 각 데이터베이스에 대해 개별적으로 재정의되고 지정될 수 있습니다.

### <a name="rest-api"></a>REST API

서버에서 자동 조정을 활성화하기 위한 REST API 사용에 대해 자세히 알아보고, [SQL Server 자동 조정 업데이트 및 HTTP 메서드 가져오기](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)를 참조하세요.

## <a name="enable-automatic-tuning-on-an-individual-database"></a>개별 데이터베이스에서 자동 조정 활성화

Azure SQL Database를 통해 각 데이터베이스에서 자동 조정 구성을 개별적으로 지정할 수 있습니다. 데이터베이스 수준에서 부모 서버, "Azure 기본값"에서 자동 조정 구성을 상속하거나 구성을 상속하지 않도록 선택할 수 있습니다. Azure에서는 기본적으로 FORCE_LAST_GOOD_PLAN 및 CREATE_INDEX는 사용하고 DROP_INDEX는 사용하지 않도록 설정됩니다.

> [!TIP]
> 동일한 구성 설정을 모든 데이터베이스에 대해 자동으로 적용할 수 있도록 **서버 수준**에서 자동 조정 구성을 관리하는 것이 좋습니다. 데이터베이스가 동일한 서버에서 설정을 상속하는 다른 데이터베이스와 다른 설정을 가지도록 해야 하는 경우에만 개별 데이터베이스에서 자동 조정을 구성합니다.
>

### <a name="azure-portal"></a>Azure portal

**단일 데이터베이스**에서 자동 조정을 사용하려면 Azure Portal에서 데이터베이스로 이동하여 **자동 조정**을 선택합니다.

개별 자동 조정 설정을 각 데이터베이스에 대해 별도로 구성할 수 있습니다. 개별 자동 조정 옵션을 수동으로 구성하거나 서버에서 해당 설정을 상속하는 옵션을 지정할 수 있습니다.

![데이터베이스](./media/sql-database-automatic-tuning-enable/database.png)

현재 DROP_INDEX 옵션은 파티션 전환 및 인덱스 힌트를 사용하는 애플리케이션과 호환되지 않으므로 이러한 경우에는 사용하도록 설정해서는 안 됩니다.

원하는 구성을 선택한 후 **적용**을 클릭합니다.

### <a name="rest-api"></a>Rest API

단일 데이터베이스에서 자동 조정을 활성화하기 위한 REST API 사용에 대해 자세히 알아보고, [SQL Database 자동 조정 업데이트 및 HTTP 메서드 가져오기](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)를 참조하세요.

### <a name="t-sql"></a>T-SQL

T-SQL을 통해 단일 데이터베이스에서 자동 조정을 활성화하려면 데이터베이스에 연결하고 다음 쿼리를 실행합니다.

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

자동 조정을 자동으로 설정하면 Azure 기본값을 적용합니다. 상속으로 설정하면 자동 조정 구성은 부모 서버에서 상속됩니다. 사용자 정의를 선택하면 자동 조정을 수동으로 구성해야 합니다.

T-SQL을 통해 개별 자동 조정 옵션을 구성하려면 데이터베이스에 연결하고 다음과 같은 쿼리를 실행합니다.

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

개별 조정 옵션을 ON으로 설정하면 데이터베이스가 상속한 설정을 무시하고 조정 옵션을 활성화합니다. OFF로 설정하면 마찬가지로 데이터베이스가 상속한 설정을 무시하고 조정 옵션을 비활성화합니다. 기본값이 지정된 자동 조정 옵션은 데이터베이스 수준 자동 조정 설정에서 구성을 상속합니다.  

> [!IMPORTANT]
> [활성 지역 복제](sql-database-auto-failover-group.md)의 경우 주 데이터베이스에서만 자동 튜닝을 구성하면 됩니다. 인덱스 만들기 또는 삭제와 같은 자동으로 적용되는 튜닝 작업은 읽기 전용 보조 데이터베이스에 자동으로 복제됩니다. 읽기 전용 보조 데이터베이스에 다른 튜닝 구성을 적용하는 것은 지원되지 않으므로, 읽기 전용 보조 데이터베이스에서 T-SQL을 통한 자동 튜닝을 사용하도록 설정하려고 하면 실패합니다.
>

자동 조정을 구성하기 위한 T-SQL 옵션에 대해 자세히 알아보고, [SQL Database 서버에 대한 ALTER DATABASE SET 옵션(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)을 참조하세요.

## <a name="disabled-by-the-system"></a>시스템에서 비활성화됨

자동 조정은 데이터베이스에서 수행하는 모든 작업을 모니터링하고 일부 경우에서 자동 조정이 데이터베이스에서 제대로 작동할 수 없는지 결정할 수 있습니다. 이 상황에서 조정 옵션은 시스템에서 비활성화됩니다. 쿼리 저장소는 활성화되지 않았거나 특정 데이터베이스에서 읽기 전용 상태에 있기 때문에 대부분의 경우에서 발생합니다.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>이메일 알림 자동 조정 구성

[이메일 알림 자동 조정](sql-database-automatic-tuning-email-notifications.md) 가이드를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 자동 조정 및 성능을 개선할 수 있는 방법에 대한 자세한 내용은 [자동 조정 문서](sql-database-automatic-tuning.md)를 참조하세요.
* Azure SQL Database 성능 권장 사항에 대한 개요는 [성능 권장 사항](sql-database-advisor.md)을 참조하세요.
* 상위 쿼리의 성능에 미치는 영향을 알아보려면 [Query Performance Insights](sql-database-query-performance.md)를 참조하세요.
