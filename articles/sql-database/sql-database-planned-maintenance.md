---
title: Azure 유지 관리 이벤트 계획 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database에 대한 계획된 유지 관리 이벤트를 준비하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
manager: craigg
ms.date: 01/30/2019
ms.openlocfilehash: 928338a911efae051df7164239dbd19f9317338a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584610"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Azure SQL Database의 Azure 유지 관리 이벤트 계획

Azure SQL 데이터베이스에서 계획된 유지 관리 이벤트를 준비하는 방법을 알아봅니다.

## <a name="what-is-a-planned-maintenance-event"></a>계획된 유지 관리 이벤트란?

각 데이터베이스에 대해, Azure SQL DB는 데이터베이스 복제본의 쿼럼을 유지 관리하며 여기에서는 하나의 복제본이 주 복제본입니다. 주 복제본은 온라인 서비스를 항상 제공해야 하며, 하나 이상의 보조 복제본이 정상 상태여야 합니다. 계획된 유지 관리 기간 동안 데이터베이스 쿼럼 멤버는 한 번에 하나씩 오프라인 상태가 되는데, 그 이유는 응답하는 주 복제본이 하나 있고 보조 복제본이 하나 이상 있어서 클라이언트 가동 중단 시간이 없도록 보장하기 위해서 입니다. 주 복제본을 오프라인으로 전환해야 하는 경우, 재구성/장애 조치(failover) 프로세스가 발생하고 하나의 보조 복제본이 새로운 주 복제본이 됩니다.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>계획된 유지 관리 이벤트 기간 동안 예상되는 상황

재구성/장애 조치(failover)는 일반적으로 30초 이내에 완료되며 평균은 8초가 걸립니다. 이미 연결되어있는 경우, 애플리케이션은 데이터베이스의 새로운 주 복제본을 정상적으로 복제해야 합니다. 새로운 주 복제본이 온라인 상태가 되기 전에, 데이터베이스가 재구성되는 동안 새 연결을 시도하면 40613(데이터베이스 사용 불가): "서버 '{servername}'의 데이터베이스 '{databasename}'을(를) 현재 사용할 수 없습니다. 나중에 연결을 다시 시도해보세요." 오류가 발생합니다. 데이터베이스에 장기 실행 쿼리가 있으면, 이 쿼리가 재구성 중에 중단되어 다시 시작해야 합니다.

## <a name="retry-logic"></a>재시도 논리

클라우드 데이터베이스 서비스에 연결하는 모든 클라이언트 프로덕션 애플리케이션은 강력한 연결 [재시도 논리](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)를 구현해야 합니다. 그러면 이러한 상황을 완화하는 데 도움이 되며 일반적으로 최종 사용자에게 오류를 투명하게 만들 수 있습니다.

## <a name="frequency"></a>Frequency(빈도)

평균적으로 계획된 유지 관리 이벤트는 매달 1.7개가 발생합니다.

## <a name="resource-health"></a>리소스 상태

SQL 데이터베이스에 로그인 장애가 있으면, [Azure Portal](https://portal.azure.com)의 [Resource Health](../service-health/resource-health-overview.md#getting-started) 창에서 현재 상태를 확인하세요. 상태 기록 섹션에는 각 이벤트에 대한 가동 중지 시간 이유가 포함됩니다(가능한 경우).


## <a name="next-steps"></a>다음 단계

- SQL Database의 [Resource Health](sql-database-resource-health.md)에 대해 자세히 알아봅니다.
- 재시도 논리에 대한 자세한 내용은 [일시적인 오류에 대한 재시도 논리](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)를 참조하세요.
