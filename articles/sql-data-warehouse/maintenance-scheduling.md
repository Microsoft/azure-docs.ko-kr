---
title: Azure 유지 관리 일정(미리 보기) | Microsoft Docs
description: 고객은 유지 관리 예약 기능을 사용하여 Azure SQL Data Warehouse 서비스에서 새 기능, 업그레이드 및 패치를 출시하는 데 사용하는 필요한 예약된 유지 관리 이벤트를 계획할 수 있습니다.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 03/13/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: b97e27b86ecad1f7f87a6de4d43b09d69c167c6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075331"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>유지 관리 일정을 사용하여 서비스 유지 관리 및 업데이트 관리

유지 관리 일정 모든 Azure SQL Data Warehouse 지역에서 사용할 수 있습니다. 이 기능은 서비스 상태 계획된 유지 관리 알림, 리소스 상태 검사 모니터 및 Azure SQL Data Warehouse 유지 관리 예약 서비스와 통합됩니다.

유지 관리 예약 기능을 사용하면 새 기능, 업그레이드 및 패치를 받기에 편리한 시간 범위를 선택할 수 있습니다. 7일 기간 내에서 기본 및 보조 유지 관리 기간을 선택합니다. 예를 들어 기본 기간을 토요일 22:00 ~ 일요일 01:00로, 보조 기간을 수요일 19:00 ~ 22:00로 할 수 있습니다. SQL Data Warehouse가 기본 유지 관리 기간 중에 유지 관리를 수행할 수 없는 경우 보조 유지 관리 기간 중에 유지 관리를 다시 시도합니다. 서비스 유지 관리의 기본 및 보조 windows 하는 동안 발생할 수 있습니다. 신속 하 게 모든 유지 관리 작업이 완료 되도록 DW400(c) 및 낮은 데이터 웨어하우스 계층 지정 된 유지 관리 기간 외의 유지 관리를 완료할 수 있습니다.

새로 작성되는 모든 Azure SQL Data Warehouse 인스턴스의 경우 배포 중에 시스템 정의 유지 관리 일정이 적용됩니다. 배포가 완료되는 즉시 일정을 편집할 수 있습니다.

각 유지 관리 기간은 3 ~ 8시간이 될 수 있습니다. 유지 관리는 기간 내에 언제든 발생할 수 있습니다. 서비스가 데이터 웨어하우스에 새 코드를 배포할 때는 연결이 잠깐 동안 끊깁니다.

이 기능을 사용하려면 별도의 날짜 범위 내에서 기본 및 보조 기간을 식별해야 합니다. 모든 유지 관리 작업은 예약된 유지 관리 기간 내에 완료되어야 합니다. 사전 알림 없이는 지정된 유지 관리 기간이 아닐 때 유지 관리가 수행되지 않습니다. 데이터 웨어하우스는 예약된 유지 관리 중에 일시 중지되는 경우 다시 시작 작업 중에 업데이트됩니다.  

## <a name="alerts-and-monitoring"></a>경고 및 모니터링

서비스 상태 알림 및 리소스 상태 검사 모니터와의 통합으로 고객은 예정된 유지 관리 활동의 정보를 지속적으로 파악할 수 있습니다. 새 자동화는 Azure Monitor를 이용합니다. 예정된 유지 관리 이벤트의 알림을 어떻게 받을 것인지 결정할 수 있습니다. 가동 중지 시간을 관리하고 운영에 미치는 영향을 최소화하는 데 도움이 되는 자동화된 흐름을 결정할 수도 있습니다.

24 시간제 사전 알림을 DW400c 및 낮은 계층의 현재 예외를 사용 하 여 모든 유지 관리 이벤트는 앞에 옵니다. 인스턴스 가동 중지 시간을 최소화하려면 선택한 유지 관리 기간 전에 데이터 웨어하우스에 장기 실행 트랜잭션이 없어야 합니다. 유지 관리가 시작되면 모든 활성 세션은 취소됩니다. 되지 않은 커밋된 트랜잭션은 롤백됩니다, 그리고 및 데이터 웨어하우스에 대 한 연결이 짧은 손실 됩니다. 데이터 웨어하우스에서 유지 관리가 완료된 직후에 알림이 전송됩니다.

유지 관리가 수행된다는 사전 알림을 받았는데 해당 시간에 SQL Data Warehouse가 유지 관리를 수행할 수 없는 경우에는 취소 알림이 전송됩니다. 그러면 유지 관리는 예약된 다음 유지 관리 기간에 다시 시작됩니다.

모든 활성 유지 관리 이벤트는 **서비스 상태 - 계획된 유지 관리** 섹션에 표시됩니다. 서비스 상태 기록에는 지난 이벤트의 전체 레코드가 포함됩니다. 활성 이벤트가 진행되는 중에 Azure Service Health 검사 포털 대시보드를 통해 유지 관리를 모니터링할 수 있습니다.

### <a name="maintenance-schedule-availability"></a>유지 관리 일정 사용 가능성

선택한 지역에서 유지 관리 예약 기능이 아직 제공되지 않더라도 언제든지 유지 관리 일정을 확인하고 편집할 수 있습니다. 유지 관리 예약 기능을 해당 지역에서 사용할 수 있게 되면 확인된 일정이 데이터 웨어하우스에서 즉시 활성화됩니다.

## <a name="next-steps"></a>다음 단계

- 유지 관리 일정을 확인하는 방법을 [자세히 알아봅니다](viewing-maintenance-schedule.md).
- 유지 관리 일정을 변경하는 방법을 [자세히 알아봅니다](changing-maintenance-schedule.md).
- Azure Monitor를 사용하여 경고를 작성, 확인 및 관리하는 방법에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage).
- 로그 경고 규칙용 웹후크 작업에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).
- 작업 그룹을 만들고 관리하는 방법을 [자세히 알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).
- Azure Service Health에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/service-health/service-health-overview).
