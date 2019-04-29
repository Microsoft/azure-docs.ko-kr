---
title: 성능 권장 사항 적용 - Azure SQL Database | Microsoft Docs
description: Azure Portal을 사용하여 Azure SQL Database의 성능을 최적화할 수 있는 성능 권장 사항을 찾을 수 있습니다.
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
ms.date: 12/19/2018
ms.openlocfilehash: d80581aae56fc9d65d6f24d21f2c582cb74b3f2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420382"
---
# <a name="find-and-apply-performance-recommendations"></a>성능 권장 사항 찾기 및 적용

Azure Portal을 사용하여 Azure SQL Database의 성능을 최적화할 수 있는 성능 권장 사항을 찾거나 워크로드에서 식별된 일부 문제를 수정할 수 있습니다. Azure Portal에서 **성능 권장 사항** 페이지를 사용하면 잠재적인 영향에 따라 가지 상위 권장 사항을 확인할 수 있습니다. 

## <a name="viewing-recommendations"></a>권장 사항 보기

성능 권장 사항을 보고 적용하려면 Azure에서 올바른 [역할 기반 액세스 제어](../role-based-access-control/overview.md) 권한이 필요합니다. 권장 사항을 보려면 **읽기 권한자**, **SQL DB 참가자** 권한이 필요하고, 모든 동작(인덱스 만들기 또는 삭제, 인덱스 만들기 취소)을 실행하려면 **소유자**, **SQL DB 참가자** 권한이 필요합니다.

다음 단계를 사용하여 Azure Portal에서 성능 권장 사항을 찾을 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > **SQL 데이터베이스**로 이동한 다음, 데이터베이스를 선택합니다.
3. **성능 권장 사항**으로 이동하여 선택된 데이터베이스의 사용 가능한 권장 사항을 봅니다.

성능 권장 사항은 다음 그림에서 보여주는 표와 비슷하게 표시됩니다.

![권장 사항](./media/sql-database-advisor-portal/recommendations.png)

권장 사항은 성능의 잠재적 영향 순으로 다음과 같은 카테고리에 정렬됩니다.

| 영향 | 설명 |
|:--- |:--- |
| 높음 |높은 영향 권장사항은 가장 중요한 성능 영향을 제공합니다. |
| 중간 |중간 영향 권장 사항은 성능을 향상시키지만, 크게 향상시키지는 않습니다. |
| 낮음 |낮은 영향 권장 사항은 없는 것보다 나은 성능을 제공하지만, 향상된 기능이 눈에 띄지는 않습니다. |


> [!NOTE]
> Azure SQL Database는 일부 권장 사항을 파악하기 위해 적어도 하루 동안 작업을 모니터링해야 합니다. Azure SQL Database는 임의적이며 폭발적으로 발생하는 작업보다 일관성 있는 쿼리 패턴을 더욱 쉽게 최적화할 수 있습니다. 현재 권장 사항을 사용할 수 없는 경우 **성능 권장** 페이지에서 원인에 대해 설명하는 메시지를 제공합니다.
> 

또한 과거 작업의 상태도 볼 수 있습니다. 세부 정보를 보려면 권장 사항 또는 상태를 선택합니다.

Azure Portal에서 "인덱스 만들기" 권장 사항의 예는 다음과 같습니다.

![인덱스 만들기](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>권장 사항 적용
Azure SQL Database는 다음 세 가지 옵션을 사용하여 권장 사항을 사용하도록 설정하는 방법을 완전히 제어할 수 있게 합니다. 

* 개별 권장 구성을 한 번에 하나씩 적용합니다.
* 자동 튜닝을 사용하도록 설정하여 권장 사항을 자동으로 적용합니다.
* 권장 사항을 수동으로 구현하려면 데이터베이스에 대해 권장 T-SQL 스크립트를 실행합니다.

권장 사항을 선택하여 세부 정보를 본 다음 **스크립트 보기** 를 클릭하여 권장 사항이 어떻게 만들어지는지에 대해 정확한 세부 사항을 검토합니다.

데이터베이스는 성능 권장 사항을 사용하여 권장 사항이 적용되는 동안 온라인 상태로 유지됩니다. 자동 튜닝은 오프라인인 상태인 데이터베이스를 사용하지 않습니다.

### <a name="apply-an-individual-recommendation"></a>개별 권장 구성 적용
권장 구성을 한 번에 하나씩 검토하고 수락할 수 있습니다.

1. **권장 사항** 페이지에서 권장 사항을 선택합니다.
2. **세부 정보** 페이지에서 **적용** 단추를 클릭합니다.
   
    ![권장 구성 적용](./media/sql-database-advisor-portal/apply.png)

선택한 권장 사항은 데이터베이스에 적용됩니다.

### <a name="removing-recommendations-from-the-list"></a>목록에서 권장 사항 제거

권장 사항 목록에 목록에서 제거할 항목이 포함된 경우 권장 사항을 삭제할 수 있습니다.

1. **권장 사항** 목록에서 권장 사항을 선택하여 세부 정보를 엽니다.
2. **세부 정보** 페이지에서 **삭제**를 클릭합니다.

원하는 경우 삭제된 항목을 **권장 사항** 목록에 다시 추가할 수 있습니다.

1. **권장 사항** 페이지에서 **삭제된 항목 보기**를 클릭합니다.
2. 자세히 보기 목록에서 삭제된 항목을 선택합니다.
3. 필요에 따라 **권장 사항**의 기본 목록에 인덱스를 다시 추가하려면 **삭제 취소**를 클릭하세요.

> [!NOTE]
> SQL Database [자동 조정](sql-database-automatic-tuning.md)을 사용하며 목록에서 권장 사항을 수동으로 삭제한 경우 해당 권장 사항은 자동으로 적용되지 않습니다. 권장 사항 삭제는 특정 권장 사항이 적용되지 않도록 해야 하는 경우 사용자가 자동 조정을 사용할 수 있는 편리한 방법입니다.
> 삭제 취소 옵션을 선택하여 삭제된 권장 사항을 권장 사항 목록에 다시 추가하면 이 동작을 되돌릴 수 있습니다.
> 

### <a name="enable-automatic-tuning"></a>자동 조정 사용
Azure SQL Database가 권장 사항을 자동으로 구현하도록 설정할 수 있습니다. 권장 구성은 사용할 수 있을 때 자동으로 적용됩니다. 서비스에서 관리되는 권장 사항처럼 권장 사항이 성능에 좋지 않은 영향을 주는 경우 되돌려집니다.

1. **권장 사항** 페이지에서 **자동화**를 클릭합니다.
   
    ![관리자 설정](./media/sql-database-advisor-portal/settings.png)
2. 자동화할 작업을 선택합니다.
   
    ![권장된 인덱스](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> **DROP_INDEX** 옵션은 현재 파티션 전환 및 인덱스 힌트를 사용하는 애플리케이션과 호환되지 않습니다. 
>

원하는 구성을 선택한 후 적용을 클릭합니다.

### <a name="manually-apply-recommendations-through-t-sql"></a>수동으로 T-SQL을 통해 권장 사항을 적용합니다

권장 사항을 선택한 다음 **스크립트 보기**를 클릭합니다. 권장 구성을 수동으로 적용하도록 데이터베이스에 대해 이 스크립트를 실행합니다.

*수동으로 실행된 인덱스는 성능에 미치는 서비스 영향에 대해 모니터링하고 유효성 검사를 실시하지 않으므로* 필요한 경우 인덱스 생성 후 인덱스를 성능을 향상시키거나 조절 또는 삭제하기 위해 모니터링하는 것이 좋습니다. 인덱스 만들기에 대한 세부 정보는 [CREATE INDEX (TRANSACT-SQL)](https://msdn.microsoft.com/library/ms188783.aspx)를 참조하세요. 또한 수동으로 적용 된 권장 사항을 24 ~ 48 시간에 대 한 활성 및 권장 사항 목록에 표시 된 유지 됩니다. 전에 자동으로 시스템을 삭제 합니다. 권장 사항을 더 빨리 제거 하려는 경우 수동으로 취소할 수 있습니다.

### <a name="canceling-recommendations"></a>권장 사항 취소

**보류 중**, **유효성 검사 중** 또는 **성공** 상태에 있는 권장 사항은 취소할 수 있습니다. **실행 중** 상태의 권장 사항은 취소할 수 없습니다.

1. **튜닝 기록** 영역에서 권장 사항을 선택하면 **권장 사항 세부 정보** 페이지가 열립니다.
2. **취소** 를 클릭하여 권장 사항을 적용하는 과정을 중단합니다.

## <a name="monitoring-operations"></a>모니터링 작업

권장 구성을 적용해도 즉각적으로 일어나지 않을 수 있습니다. 포털에서는 권장 사항의 상태에 대한 세부 정보를 제공합니다. 다음은 인덱스 안에 나타날 수 있는 상태입니다.

| 상태 | 설명 |
|:--- |:--- |
| Pending |권장 사항 적용 명령을 수신했고 실행이 예약됩니다. |
| 실행 중 |권장 사항을 적용 중입니다. |
| 유효성 검사 중 |권장 사항이 성공적으로 적용되면 서비스가 성능을 측정합니다. |
| 성공 |권장 사항이 성공적으로 적용되면 성능을 측정합니다. |
| 오류 |권장 사항을 적용하는 과정 중에 오류가 발생했습니다. 일시적인 문제일 수도 있고, 테이블의 스키마변경 문제일 수도 있고, 스크립트가 더 이상 유효하지 않을 수도 있습니다. |
| 되돌리기 |권장 사항이 적용되었지만 효율적이지 않은 것으로 간주되어 자동으로 되돌리고 있습니다. |
| 되돌림 |권장 사항을 되돌렸습니다. |

세부 정보를 보려면 목록에서 In Process 권장 구성을 클릭합니다.

![권장된 인덱스](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>권장 사항 되돌리기
성능 권장 사항을 사용하여 권장 사항을 적용하는 경우(즉, 수동으로 T-SQL 스크립트를 실행하지 않음) 해당 권장 사항이 성능에 좋지 않은 영향을 준다는 점을 확인하면 자동으로 변경 내용을 되돌립니다. 어떤 이유로든 단순히 권장 사항을 되돌리려면 다음을 수행할 수 있습니다.

1. **튜닝 기록** 영역에서 성공적으로 적용된 권장 사항을 선택합니다.
2. **권장 사항 세부 정보** 페이지에서 **되돌리기**를 클릭합니다.

![권장된 인덱스](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>인덱스 권장 구성의 성능 영향 모니터링
권장 사항이 성공적으로 구현된 후(현재는 인덱스 작업 및 쿼리 매개 변수화 권장 사항만) 권장 사항 세부 정보 페이지에서 **쿼리 인사이트**를 클릭하여 [Query Performance Insights](sql-database-query-performance.md)를 열고 상위 쿼리의 성능 영향을 확인할 수 있습니다.

![성능에 미치는 영향을 모니터링합니다.](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>요약
Azure SQL Database는 SQL 데이터베이스 성능을 향상하기 위한 권장 사항을 제공합니다. T-SQL 스크립트를 제공하여 데이터베이스를 최적화하고 궁극적으로 쿼리 성능을 향상시키도록 지원합니다.

## <a name="next-steps"></a>다음 단계
권장 사항을 모니터링하고 개선된 성능을 계속 적용합니다. 데이터베이스 워크로드는 동적 이며 지속적으로 변경합니다. Azure SQL Database는 데이터베이스 성능을 잠재적으로 향상시킬 권장 사항을 계속 제공하고 모니터링할 것입니다. 

* [자동 튜닝](sql-database-automatic-tuning.md)을 참조하여 Azure SQL Database에서 자동 튜닝에 대한 자세한 내용을 알아봅니다.
* Azure SQL Database 성능 권장 사항에 대한 개요는 [성능 권장 사항](sql-database-advisor.md)을 참조하세요.
* 상위 쿼리의 성능에 미치는 영향을 알아보려면 [Query Performance Insights](sql-database-query-performance.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스
* [쿼리 저장소](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [역할 기반 액세스 제어](../role-based-access-control/overview.md)

