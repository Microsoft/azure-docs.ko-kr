---
title: Azure SQL Database - 자동 조정 | Microsoft Docs
description: Azure SQL Database는 SQL 쿼리를 분석하고 사용자 워크로드에 자동으로 적용됩니다.
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
ms.date: 03/06/2019
ms.openlocfilehash: 6e818da29b7ee0d17ebe4f8e523648146973fa63
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61415777"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database에서 자동 조정

Azure SQL Database 자동 조정은 AI 및 기계 학습에 기반한 지속적인 성능 조정을 통해 최고의 성능과 안정적인 워크로드를 제공합니다.

자동 조정은 기본 제공 인텔리전스를 사용하여 데이터베이스에서 실행된 쿼리를 지속적으로 모니터링하고 해당 성능을 자동으로 개선하는 완전히 관리되는 지능형 성능 서비스입니다. 이것은 변화하는 워크로드에 동적으로 데이터베이스를 적용하고 튜닝 권장 사항을 적용함으로써 달성됩니다. 자동 조정은 AI를 통해 Azure의 모든 데이터베이스에서 수평적으로 학습하고 동적으로 조정 작업을 개선합니다. Azure SQL Database가 자동 조정으로 실행되는 시간이 길어질수록 성능이 향상됩니다.

Azure SQL Database 자동 조정은 안정적이고 최고 성능의 데이터베이스 워크로드를 제공하는 데 사용할 수 있는 가장 중요한 기능 중 하나일 수 있습니다.

## <a name="what-can-automatic-tuning-do-for-you"></a>자동 조정으로 무엇을 할 수 있나요?

- Azure SQL 데이터베이스의 자동화된 성능 조정
- 성능 향상의 자동화된 확인
- 자동화된 롤백 및 자체 수정
- 조정 기록
- 수동 배포를 위한 조정 작업 T-SQL 스크립트
- 자동 관리 워크로드 성능 모니터링
- 수십만 개의 데이터베이스에서 기능 확장
- DevOps 리소스와 총 소유 비용에 미치는 긍정적인 영향

## <a name="safe-reliable-and-proven"></a>안전하고 신뢰할 수 있으며 검증됨

Azure SQL 데이터베이스에 적용된 조정 작업은 가장 강력한 워크로드의 성능에 대해 완벽하게 안전합니다. 시스템은 사용자 워크로드를 방해하지 않도록 주의하여 설계되었습니다. 자동 조정 권장 사항은 사용률이 낮은 경우에만 적용됩니다. 시스템 또한 워크로드 성능을 보호하기 위해 일시적으로 자동 조정 작업을 사용하지 않도록 설정할 수도 있습니다. 이런 경우, Azure Portal에서 “시스템에서 사용할 수 없다”는 메시지가 표시됩니다. 자동 조정 작업은 리소스 우선 순위가 가장 높은 워크로드를 인식합니다.

자동 조정 메커니즘은 더욱 진보하여 Azure에서 실행되는 수백만 개의 데이터베이스에서 완성도가 높아졌습니다. 적용된 자동 조정 작업이 자동으로 검증되어 워크로드 성능이 긍정적으로 향상됩니다. 회귀된 성능 권장 사항은 동적으로 감지되어 즉시 되돌려집니다. 기록된 조정 기록을 통해 각 Azure SQL Database의 성능 조정을 명확히 추적할 수 있습니다. 

![자동 조정 작동 방식](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database 자동 조정은 핵심 논리를 SQL Server 자동 조정 엔진과 공유합니다. 기본 제공 인텔리전스 메커니즘에 대한 추가 기술 정보는 [SQL Server 자동 조정](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)을 참조하세요.

## <a name="use-automatic-tuning"></a>자동 조정 사용

자동 조정을 구독에서 사용 하도록 설정 해야 합니다. Azure Portal을 사용하여 자동으로 조정을 사용하도록 하려면 [자동 조정 사용](sql-database-automatic-tuning-enable.md)을 참조하세요.

성능 향상의 자동화된 확인을 포함하여 자동으로 적용되는 조정 권장 사항을 통해 자동 조정을 자율적으로 작동할 수 있습니다. 

더 많은 제어를 위해 조정 권장 사항의 자동 애플리케이션을 해제하고 Azure Portal을 통해 조정 권장 사항을 수동으로 적용할 수 있습니다. 또한 솔루션을 사용하여 자동 조정 권장 사항만 보고 원하는 스크립트 및 도구를 통해 수동으로 적용할 수도 있습니다. 

자동 조정이 작동하는 방법과 일반적인 사용 시나리오에 대한 개요는 다음에 포함된 비디오를 참조하세요.


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>자동 조정 옵션

Azure SQL Database에서 사용 가능한 자동 조정 옵션은 다음과 같습니다.

| 자동 튜닝 옵션 | 단일 데이터베이스 및 풀링된 데이터베이스 지원 | 인스턴스 데이터베이스 지원 |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** -워크 로드의 성능을 향상 시킬 수 인덱스를 만들고 자동으로 쿼리 성능이 향상 되었습니다를 확인 하는 인덱스를 식별 합니다. | 예 | 아닙니다. | 
| **DROP INDEX** -오랜 시간 동안 사용 되지 않은 인덱스 및 고유 인덱스를 제외 하 고 매일 중복 및 복제 인덱스를 식별 (> 90 일). 현재 이 옵션은 파티션 전환 및 인덱스 힌트를 사용하는 애플리케이션과 호환되지 않습니다. | 예 | 아닙니다. |
| **FORCE LAST GOOD PLAN** (자동 계획 수정)-적합 한 계획을 이전 보다 느린 하 고 회귀 된 계획 대신 마지막으로 알려진된 좋은 계획을 사용 하 여 쿼리 실행 계획을 사용 하 여 식별 SQL 쿼리. | 예 | 예 |

자동 조정은 데이터베이스 성능을 최적화하여 [Azure Portal](sql-database-advisor-portal.md)에 표시할 수 있는 **CREATE INDEX**, **DROP INDEX** 및 **FORCE LAST GOOD PLAN** 권장 사항을 식별하고 [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) 및 [REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)를 통해 노출합니다. T-SQL을 통해 자동 조정 옵션 구성을 참조 하 고 FORCE LAST GOOD PLAN 하는 방법에 대 한 자세한 [자동 계획 수정에서는 자동 튜닝](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)합니다.

포털을 사용하여 조정 권장 사항을 수동으로 적용하거나 자동 조정을 통해 조정 권장 사항이 자율적으로 적용되도록 할 수 있습니다. 시스템에서 조정 권장 사항이 자율적으로 적용되도록 하는 경우 좋은 점은 워크로드 성능에 긍정적인 이득이 있는지 자동으로 확인하고, 뛰어난 성능 향상이 감지되지 않는 경우에는 조정 권장 사항을 자동으로 되돌립니다. 자주 실행되지 않는 조정 권장 사항의 영향을 받는 쿼리에 대한 유효성 검사 단계는 최대 72시간이 소요될 수 있습니다.

조정 권장 사항을 수동으로 적용하는 경우에는 자동 성능 검증 및 반전 메커니즘을 사용할 수 없습니다. 또한 수동으로 적용 된 권장 사항을 24 ~ 48 시간에 대 한 활성 및 권장 사항 목록에 표시 된 유지 됩니다. 전에 자동으로 시스템을 삭제 합니다. 권장 사항을 더 빨리 제거 하려는 경우 수동으로 취소할 수 있습니다.

자동 조정 옵션은 데이터베이스당 별도로 설정 또는 해제하거나 SQL Database 서버에서 구성하고 서버에서 설정을 상속하는 모든 데이터베이스에서 적용할 수 있습니다. SQL Database 서버는 자동 조정 설정에 대한 Azure 기본값을 상속할 수 있습니다. 현재 Azure에서는 기본적으로 FORCE_LAST_GOOD_PLAN 및 CREATE_INDEX는 사용하고 DROP_INDEX는 사용하지 않도록 설정됩니다.

서버에서 자동 조정 옵션을 구성하고 부모 서버에 속한 데이터베이스에서 설정을 상속하면 많은 수의 데이터베이스에서 자동 조정 옵션의 관리를 단순화할 수 있으므로 자동 조정 구성에 권장되는 방법입니다.

## <a name="next-steps"></a>다음 단계

- Azure SQL Database에서 자동 조정을 사용하도록 설정하고 워크로드를 관리하려면 [자동 조정 사용](sql-database-automatic-tuning-enable.md)을 참조하세요.
- 자동 조정 권장 사항을 수동으로 검토 및 적용하려면 [성능 권장 사항 찾기 및 적용](sql-database-advisor-portal.md)을 참조하세요.
- T-SQL을 사용하여 자동 조정 권장 사항을 적용하고 보는 방법은 [T-SQL을 통해 자동 조정 관리](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)를 참조하세요.
- 자동 조정 권장 구성에 대한 이메일 알림을 작성하는 방법에 대한 자세한 내용은 [자동 조정에 대한 이메일 알림](sql-database-automatic-tuning-email-notifications.md)을 참조하세요.
- 자동 조정에 사용된 기본 제공 인텔리전스에 대한 내용은 [인공 지능으로 Azure SQL 데이터베이스 조정](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)을 참조하세요.
- Azure SQL Database 및 SQL server 2017에서 자동 조정이 작동하는 방식에 대한 내용은 [SQL Server 자동 조정](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)을 참조하세요.
