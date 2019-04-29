---
title: Azure Resource Health를 사용하여 SQL Database 상태 모니터링 | Microsoft Docs
description: Azure Resource Health를 사용하여 SQL Database 상태를 모니터링하면 Azure 문제가 SQL 리소스에 영향을 줄 때 문제를 진단하고 지원을 받는 데 도움이 됩니다.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: c3b9fecd3ad404385732e55a9cf3aa65a6e388b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483065"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Resource Health를 사용하여 Azure SQL Database 연결 문제 해결

## <a name="overview"></a>개요

SQL Database용 [Resource Health](../service-health/resource-health-overview.md#getting-started)는 Azure 문제가 SQL 리소스에 영향을 줄 때 문제를 진단하고 지원을 받는 데 도움이 됩니다. 리소스의 현재 및 이전 상태에 대해 알려주고 문제를 완화하는 데 도움이 됩니다. Resource Health는 Azure 서비스 문제와 관련된 도움이 필요할 때 기술 지원을 제공합니다.

![개요](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>상태 검사

Resource Health는 리소스에 대한 로그인의 성공 및 실패를 검사하여 SQL 리소스의 상태를 결정합니다. 현재 SQL DB 리소스에 대한 Resource Health는 시스템 오류로 인한 로그인 실패만 검사하며 사용자 오류는 검사하지 않습니다. Resource Health 상태는 1-2분마다 업데이트됩니다.

## <a name="health-states"></a>성능 상태

### <a name="available"></a>사용 가능

**사용 가능** 상태는 Resource Health가 SQL 리소스의 시스템 오류로 인한 로그인 실패를 검색하지 못했음을 의미합니다.

![사용 가능](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>성능 저하됨

**성능 저하됨** 상태는 Resource Health가 대부분의 성공적인 로그인을 감지했지만 일부 오류도 감지했음을 의미합니다. 이러한 오류는 일시적인 로그인 오류일 가능성이 높습니다. 일시적인 로그인 오류로 인한 연결 문제의 영향을 줄이려면 코드에서 [재시도 논리](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)를 구현하세요.

![성능 저하됨](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>사용할 수 없음

**사용할 수 없음** 상태는 Resource Health가 SQL 리소스에 대한 일관된 로그인 실패를 감지했음을 의미 합니다. 리소스가 이 상태로 장기간 유지되면 고객 지원팀에 문의하세요.

![사용할 수 없음](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>알 수 없음

**알 수 없음** 상태는 Resource Health에서 이 리소스에 대한 정보를 10분 넘게 수신하지 못했음을 나타냅니다. 이 상태는 리소스 상태에 대한 결정적인 표시는 아니지만 문제 해결 프로세스의 중요한 데이터 요소입니다. 리소스가 예상한 대로 실행 중이면 몇 분 후 리소스 상태가 사용 가능으로 변경됩니다. 리소스에 문제가 발생하는 경우 상태가 알 수 없음이면, 플랫폼의 이벤트가 리소스에 영향을 미치고 있다는 것을 의미할 수 있습니다.

![알 수 없음](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>기록 정보

Resource Health의 상태 기록 섹션에서 최대 14일 동안의 상태 기록에 액세스할 수 있습니다. 섹션에는 Resource Health에서 보고하는 가동 중지 시간에 대한 가동 중지 시간 이유(가능한 경우)도 포함됩니다. 현재 Azure는 SQL 데이터베이스 리소스의 가동 중지 시간을 2분 단위로 표시합니다. 실제 가동 중지 시간은 1분 미만일 가능성이 높습니다. 평균 값은 8초입니다.

### <a name="downtime-reasons"></a>가동 중지 시간 이유

SQL Database에 가동 중지 시간이 발생하면 이유를 확인하기 위해 분석이 수행됩니다. 사용 가능한 경우 가동 중지 시간 이유가 Resource Health의 상태 기록 섹션에 보고됩니다. 가동 중지 시간 이유는 일반적으로 이벤트 발생 후 30분에 게시됩니다.

#### <a name="planned-maintenance"></a>계획된 유지 보수

Azure 인프라는 정기적으로 계획된 유지 관리(데이터 센터의 하드웨어 또는 소프트웨어 구성 요소 업그레이드)를 수행합니다. 데이터베이스에서 유지 관리를 수행하는 동안 SQL은 일부 기존 연결을 종료하고 새 연결을 거부할 수 있습니다. 계획된 유지 관리를 수행하는 동안 발생하는 로그인 실패는 일반적으로 일시적이며 [다시 시도 논리](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)로 그 영향을 줄일 수 있습니다. 로그인 오류가 계속 발생하면 고객 지원팀에 문의하세요.

#### <a name="reconfiguration"></a>Reconfiguration

재구성은 일시적인 조건으로 간주되며 때때로 필요합니다. 이러한 이벤트는 부하 분산 또는 소프트웨어/하드웨어 실패에 의해 트리거될 수 있습니다. 클라우드 데이터베이스에 연결하는 모든 클라이언트 프로덕션 애플리케이션은 이러한 상황을 완화하므로 강력한 연결 [재시도 논리](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)를 구현해야 하며 일반적으로 오류를 최종 사용자에게 투명하게 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [일시적인 오류에 대한 재시도 논리](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)에 대한 자세한 정보
- [문제 해결, 진단 및 SQL 연결 오류 방지](./sql-database-connectivity-issues.md)
- [Resource Health 경고 구성](../service-health/resource-health-alert-arm-template-guide.md)에 대한 자세한 정보
- [Resource Health](../service-health/resource-health-overview.md)의 개요 보기
- [Resource Health FAQ](../service-health/resource-health-faq.md)
