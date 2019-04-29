---
title: 롤링 애플리케이션 업그레이드 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database 지역에서 복제를 사용하여 클라우드 애플리케이션의 온라인 업그레이드를 지원하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 47fd6c1e2bb342bc1a31fb16a45a5ebc749dca69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702676"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>SQL Database 활성 지역 복제를 사용하여 클라우드 애플리케이션의 롤링 업그레이드 관리

Azure SQL Database의 [활성 지역 복제](sql-database-auto-failover-group.md)를 사용하여 클라우드 애플리케이션의 롤링 업그레이드를 사용하도록 설정하는 방법을 알아봅니다. 업그레이드는 중단 작업이기 때문에 비즈니스 연속성 계획 및 설계의 일부여야 합니다. 이 문서에서는 업그레이드 프로세스를 오케스트레이션하는 두 가지 다른 방법을 살펴보고 각 옵션의 이점 및 장단점을 설명합니다. 이 문서에서는 해당 데이터 계층으로 단일 데이터베이스에 연결된 웹 사이트로 구성되는 애플리케이션을 참조합니다. 우리의 목표는 사용자 환경에 많은 영향을 주지 않고 애플리케이션의 버전 1(V1)을 버전 2(V2)로 업그레이드하는 것입니다.

업그레이드 옵션을 평가할 때는 다음과 같은 요소를 고려합니다.

* 업그레이드 중에 애플리케이션 기능이 얼마나 제한되거나 성능 저하되는지와 같은 애플리케이션 가용성에 대한 영향
* 업그레이드에 실패하는 경우 다시 롤백하는 기능
* 업그레이드 중에 관련 없는 치명적인 오류가 발생하는 경우 애플리케이션의 취약성
* 총 달러 비용. 이 요소에는 업그레이드 프로세스에서 사용되는 임시 구성 요소의 추가 데이터베이스 중복 및 증분 비용이 포함됩니다.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>재해 복구에 대해 데이터베이스 백업을 사용하는 애플리케이션 업그레이드

애플리케이션이 자동 데이터베이스 백업을 사용하고 재해 복구에 지역 복원을 사용하는 경우 단일 Azure 지역에 배포됩니다. 사용자의 작업 중단을 최소화하려면 업그레이드가 적용되는 모든 애플리케이션 구성 요소가 포함된 스테이징 환경을 해당 지역에 만듭니다. 첫 번째 다이어그램에서는 업그레이드 프로세스 전의 운영 환경을 보여 줍니다. `contoso.azurewebsites.net` 엔드포인트는 웹앱의 프로덕션 환경을 나타냅니다. 업그레이드를 롤백할 수 있으려면, 데이터베이스의 완전히 동기화된 복사본으로 스테이징 환경을 만들어야 합니다. 업그레이드에 대한 스테이징 환경을 만들려면 다음 단계를 수행합니다.

1. 같은 Azure 지역에 보조 데이터베이스를 만듭니다. 보조 데이터베이스를 모니터링하여 시드 프로세스가 완료되었는지 확인합니다(1).
2. 웹앱에 대한 새 환경을 만들고 '스테이징'이라고 합니다. 이것은 URL `contoso-staging.azurewebsites.net`을 사용하여 Azure DNS에 등록됩니다(2).

> [!NOTE]
> 이러한 준비 단계는 전체 액세스 모드에서 작동할 수 있는 프로덕션 환경에 영향을 주지 않습니다.

![클라우드 재해 복구를 위한 SQL Database 지역 복제 구성](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

준비 단계가 완료되면 애플리케이션이 실제 업그레이드할 준비가 된 것입니다. 다음 다이어그램에서는 업그레이드 프로세스와 관련된 단계를 보여 줍니다.

1. 주 데이터베이스를 읽기 전용 모드로 설정합니다(3). 이 모드에서는 웹앱(V1)의 프로덕션 환경이 업그레이드 중에 읽기 전용으로 유지되므로 V1 및 V2 데이터베이스 인스턴스 간에 데이터베이스가 확산되지 않습니다.
2. 계획된 종료 모드를 사용하여 보조 데이터베이스의 연결을 끊습니다(4). 이 작업으로 주 데이터베이스의 완전히 동기화된 독립 복사본이 만들어집니다. 이 데이터베이스가 업그레이드됩니다.
3. 보조 데이터베이스를 읽기-쓰기 모드로 설정하고 업그레이드 스크립트를 실행합니다(5).

![클라우드 재해 복구를 위한 SQL Database 지역 복제 구성](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

업그레이드가 성공적으로 완료되면 이제 프로덕션 환경이 되는 업그레이드된 복사본 애플리케이션으로 전환할 수 있습니다. 전환에는 다음 다이어그램에 설명된 대로 몇 가지 단계가 더 포함됩니다.

1. 웹앱의 프로덕션 및 스테이징 환경 간 전환 작업을 활성화합니다(6). 이 작업의 두 가지 환경의 URL을 전환합니다. 이제 `contoso.azurewebsites.net`은 웹 사이트 및 데이터베이스의 V2 버전(프로덕션 환경)을 가리킵니다. 
2. 전환 후에 스테이징 복사본이 된 V1 버전이 더 이상 필요하지 않으면 스테이징 환경을 서비스 해제할 수 있습니다(7).

![클라우드 재해 복구를 위한 SQL Database 지역 복제 구성](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

업그레이드 스크립트의 오류 등으로 인해 업그레이드 프로세스가 실패하면 스테이징 환경은 손상된 것으로 간주됩니다. 애플리케이션을 업그레이드 전 상태로 롤백하려면 프로덕션 환경의 애플리케이션을 모든 권한으로 되돌리면 됩니다. 다음 다이어그램은 되돌리는 단계를 보여 줍니다.

1. 데이터베이스 복사본을 읽기-쓰기 모드로 설정합니다(8). 이 작업에는 프로덕션 복사본의 전체 V1 기능을 복원합니다.
2. 근본 원인 분석을 수행하고 스테이징 환경을 서비스 해제합니다(9).

이 시점에서 애플리케이션은 완벽하게 작동하며, 업그레이드 단계를 반복할 수 있습니다.

> [!NOTE]
> 전환 작업을 아직 수행하지 않았으므로 롤백에서는 DNS를 변경할 필요가 없습니다.

![클라우드 재해 복구를 위한 SQL Database 지역 복제 구성](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

이 옵션의 핵심 이점은 일련의 간단한 단계에 따라 단일 지역의 애플리케이션을 업그레이드할 수 있다는 것입니다. 업그레이드의 달러 비용은 상대적으로 낮습니다. 

하지만 업그레이드 중에 치명적인 오류가 발생하는 경우 업그레이드 전 상태로 복구할 때 다른 지역에 애플리케이션이 다시 배포되며 지역 복원을 사용하여 백업에서 데이터베이스가 복원된다는 주요 단점이 있습니다. 이 프로세스를 통해 상당한 가동 중지 시간이 발생합니다.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>재해 복구에 대해 데이터베이스 지역에서 복제를 사용하는 애플리케이션 업그레이드

애플리케이션에서 비즈니스 연속성을 위해 활성 지역 복제를 사용하는 경우 두 개 이상의 서로 다른 지역에 배포됩니다. 활성, 주 데이터베이스는 주 지역에 있고 읽기 전용의 보조 데이터베이스는 백업 지역에 있습니다. 이 문서의 시작 부분에서 언급한 요소와 함께 업그레이드 프로세스에서는 다음 사항도 보장해야 합니다.

* 애플리케이션이 업그레이드 프로세스 중에 치명적인 오류로부터 항상 보호된 상태로 유지되어야 합니다.
* 애플리케이션의 지역 중복 구성 요소가 활성화된 구성 요소와 함께 병렬로 업그레이드되어야 합니다.

이러한 목표를 달성하기 위해 Web Apps 환경을 사용하는 동시에 활성 엔드포인트와 백업 엔드포인트가 하나씩 포함된 장애 조치(failover) 프로필을 사용하는 ATM(Azure Traffic Manager)을 활용하게 됩니다. 다음 다이어그램에서는 업그레이드 프로세스 전의 운영 환경을 보여 줍니다. `contoso-1.azurewebsites.net` 및 `contoso-dr.azurewebsites.net` 웹 사이트는 지리적으로 완전하게 중복되는 애플리케이션의 프로덕션 환경을 나타냅니다. 프로덕션 환경에는 다음 구성 요소가 포함되어 있습니다.

* 주 지역에 있는 웹앱 `contoso-1.azurewebsites.net`의 프로덕션 환경(1)
* 주 지역의 주 데이터베이스(2)
* 백업 지역에 있는 웹앱의 대기 인스턴스(3)
* 백업 지역의 지역에서 복제된 보조 데이터베이스(4)
* 온라인 엔드포인트 `contoso-1.azurewebsites.net` 및 오프라인 엔드포인트 `contoso-dr.azurewebsites.net`이 포함된 Azure Traffic Manager 성능 프로필

업그레이드의 롤백이 가능하도록 하려면, 애플리케이션의 완전히 동기화된 복사본으로 스테이징 환경을 만들어야 합니다. 업그레이드 프로세스 중에 치명적인 오류가 발생하는 경우 애플리케이션이 빠르게 복구되도록 해야 하므로 스테이징 환경도 지역 중복 환경으로 만들어야 합니다. 업그레이드용 스테이징 환경을 만들려면 다음 단계를 수행해야 합니다.

1. 주 지역에 웹앱의 스테이징 환경을 배포합니다(6).
2. 주 Azure 지역에서 보조 데이터베이스를 만들고(7) 여기에 연결하도록 웹앱의 스테이징 환경을 구성합니다. 
3. 주 지역의 보조 데이터베이스를 복제하여 백업 지역에 다른 지역 중복 보조 데이터베이스를 만듭니다. 이 메서드를 *연결된 지역 복제*라고 합니다(8).
4. 백업 지역에서 웹앱 인스턴스의 스테이징 환경을 배포하고(9), (8)에서 만든 지역 중복 보조 데이터베이스에 연결하도록 구성합니다.

> [!NOTE]
> 이러한 준비 단계는 프로덕션 환경의 애플리케이션에 영향을 주지 않습니다. 읽기-쓰기 모드에서 정상적으로 작동합니다.

![클라우드 재해 복구를 위한 SQL Database 지역 복제 구성](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

준비 단계가 완료되면 스테이징 환경을 업그레이드할 준비가 된 것입니다. 다음 다이어그램에서는 업그레이드 단계를 보여 줍니다.

1. 프로덕션 환경의 주 데이터베이스를 읽기 전용 모드로 설정합니다(10). 이 모드에서는 업그레이드 중에 프로덕션 데이터베이스(V1)가 변경되지 않으므로 V1 및 V2 데이터베이스 인스턴스 간에 데이터베이스가 확산되지 않습니다.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. 보조 (11) 연결을 끊어 지역에서 복제를 종료 합니다. 이 작업으로 프로덕션 데이터베이스의 완전히 동기화된 독립 복사본이 생성됩니다. 이 데이터베이스가 업그레이드됩니다. 다음 예제에서는 TRANSACT-SQL을 사용 하지만 [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) 도 제공 됩니다. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net` 및 스테이징 주 데이터베이스에 대해 업그레이드 스크립트를 실행합니다(12). 데이터베이스 변경 내용이 스테이징 보조 데이터베이스에 자동으로 복제됩니다.

![클라우드 재해 복구를 위한 SQL Database 지역 복제 구성](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

업그레이드가 정상적으로 완료되면 이제 사용자를 애플리케이션의 V2 버전으로 전환할 준비가 된 것입니다. 다음 다이어그램은 관련 단계를 보여 줍니다.

1. 주 지역(13)과 백업 지역(14)에서 웹앱의 프로덕션 및 스테이징 환경 간 전환 작업을 활성화합니다. 이제 애플리케이션 V2 버전이 프로덕션 환경이 되고, 백업 지역에는 중복 복사본이 생성됩니다.
2. V1 애플리케이션이 더 이상 필요하지 않으면(15 및 16) 스테이징 환경을 서비스 해제할 수 있습니다.

![클라우드 재해 복구를 위한 SQL Database 지역 복제 구성](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

업그레이드 스크립트의 오류 등으로 인해 업그레이드 프로세스가 실패하면 스테이징 환경은 불일치 상태로 간주합니다. 애플리케이션을 업그레이드 전 상태로 롤백하려면 프로덕션 환경에서 애플리케이션의 V1 버전을 사용하여 애플리케이션 상태를 되돌립니다. 이때 수행해야 하는 단계는 다음 다이어그램에 나와 있습니다.

1. 프로덕션 환경의 주 데이터베이스 복사본을 읽기-쓰기 모드로 설정합니다(17). 이 작업에서는 프로덕션 환경에서 전체 V1 기능을 복원합니다.
2. 근본 원인 분석을 수행하고 스테이징 환경을 복구하거나 제거합니다(18 및 19).

이 시점에서 애플리케이션은 완벽하게 작동하며, 업그레이드 단계를 반복할 수 있습니다.

> [!NOTE]
> 전환 작업을 수행하지 않았으므로 롤백에서는 DNS를 변경할 필요가 없습니다.

![클라우드 재해 복구를 위한 SQL Database 지역 복제 구성](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

이 옵션의 핵심 이점 은 업그레이드하는 동안 비즈니스 연속성을 손상시키지 않고 애플리케이션 및 해당 지역 중복 복사본 모두를 병렬로 업그레이드할 수 있다는 것입니다.

주 단점은 각 애플리케이션 구성 요소의 이중 중복이 필요하므로 더 높은 달러 비용이 발생한다는 것입니다. 또한 더 복잡한 워크플로가 사용됩니다.

## <a name="summary"></a>요약

문서에서 설명된 두 개의 업그레이드 방법은 복잡성과 달러 비용에서 차이가 있지만 둘 다 사용자가 읽기 전용 작업으로 제한되는 기간을 최소화하는 데 집중합니다. 해당 시간은 업그레이드 스크립트의 기간으로 직접 정의됩니다. 데이터베이스 크기, 선택한 서비스 계층, 웹 사이트 구성 또는 쉽게 제어할 수 없는 기타 요인에 따라 달라지지 않습니다. 모든 준비 단계는 업그레이드 단계에서 분리되며 프로덕션 애플리케이션에 영향을 주지 않습니다. 업그레이드 중에 사용자 환경을 결정하는 핵심 요소 중 하나는 업그레이드 스크립트의 효율성입니다. 따라서 환경을 개선하는 가장 좋은 방법은 업그레이드 스크립트를 가능한 한 효율적으로 만드는 데 집중하는 것입니다.

## <a name="next-steps"></a>다음 단계

* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
* Azure SQL Database 활성 지역 복제에 대해 알아보려면 [활성 지역 복제를 사용하여 읽기 가능한 보조 데이터베이스 만들기](sql-database-active-geo-replication.md)를 참조하세요.
* Azure SQL Database 자동 장애 조치(failover) 그룹에 대해 알아보려면 [자동 장애 조치(failover) 그룹을 통해 여러 데이터베이스에서 조정된 투명 장애 조치(failover) 수행](sql-database-auto-failover-group.md)을 참조하세요.
* Azure App Service의 스테이징 환경에 대해 알아보려면 [Azure App Service에서 스테이징 환경 설정](../app-service/deploy-staging-slots.md)을 참조하세요.
* Azure Traffic Manager 프로필에 대해 알아보려면 [Azure Traffic Manager 프로필 관리](../traffic-manager/traffic-manager-manage-profiles.md)를 참조하세요.
