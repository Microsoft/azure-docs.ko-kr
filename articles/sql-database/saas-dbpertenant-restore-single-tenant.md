---
title: 다중 테넌트 SaaS 앱에서 데이터베이스 복원
description: 실수로 데이터를 삭제한 후 단일 테넌트 SQL 데이터베이스를 복원하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 12/04/2018
ms.openlocfilehash: a54e8e5629f6f8ad688b6fe11bbf50fc038095bf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818523"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>테넌트별 데이터베이스 SaaS 애플리케이션에서 단일 테넌트 복원

테넌트별 데이터베이스 모델을 사용하면 다른 테넌트에 영향을 주지 않으면서 단일 테넌트를 이전의 특정 시점으로 간편하게 복원할 수 있습니다.

이 자습서에서는 다음 두 가지 데이터 복구 패턴에 대해 알아봅니다.

> [!div class="checklist"]
> * 병렬 데이터베이스에 데이터베이스 복원(병렬)
> * 원래 위치에 데이터베이스를 복원하여 기존 데이터베이스 대체

|||
|:--|:--|
| 병렬 데이터베이스로 복원 | 이 패턴은 테넌트가 이전 시점에서 데이터를 검사할 수 있도록 검토, 감사, 준수 등의 작업에 사용할 수 있습니다. 테넌트의 현재 데이터베이스는 온라인 상태가 유지되고 변경되지 않습니다. |
| 원래 위치에 복원 | 이 패턴은 테넌트가 실수로 삭제되었거나 데이터가 손상된 경우에 테넌트를 이전 시점으로 복구할 때 사용합니다. 원래 데이터베이스는 오프라인 상태가 되고 복원된 데이터베이스로 대체됩니다. |
|||

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Wingtip SaaS 앱이 배포되었습니다. 5분 이내에 배포하려면 [Wingtip SaaS 애플리케이션 배포 및 탐색](saas-dbpertenant-get-started-deploy.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>SaaS 테넌트 복원 패턴 소개

개별 테넌트의 데이터를 복구하는 패턴에는 두 가지가 있습니다. 테넌트 데이터베이스가 서로 격리되어 있기 때문에 하나의 테넌트를 복원해도 다른 테넌트의 데이터에는 영향을 주지 않습니다. 두 가지 패턴에서 모두 Azure SQL Database PITR(특정 시점 복원) 기능이 사용됩니다. PITR은 항상 새 데이터베이스를 만듭니다.

* **병렬 데이터베이스로 복원**: 첫 번째 패턴에서는 테넌트의 현재 데이터베이스 옆에 새 병렬 데이터베이스가 생성됩니다. 테넌트는 복원된 데이터베이스에 대해 읽기 전용 액세스 권한을 갖습니다. 복원된 데이터를 사용하여 현재 데이터 값을 검토하고 덮어쓸 수 있습니다. 테넌트가 복원된 데이터베이스에 액세스하는 방식과 복구에 사용할 옵션은 앱 디자이너가 결정합니다. 경우에 따라 테넌트에 이전 시점의 데이터를 검토하는 권한만 허용해도 충분할 수 있습니다.

* **원래 위치에 복원**: 두 번째 패턴은 데이터가 유실되었거나 손상되어 테넌트를 이전 시점으로 되돌리려는 경우에 유용합니다. 데이터베이스가 복원되는 동안 테넌트는 오프라인 상태가 됩니다. 원래 데이터베이스가 삭제되고 복원된 데이터베이스의 이름이 바뀝니다. 원래 데이터베이스가 삭제된 후에도 해당 백업 체인에는 계속 액세스할 수 있으므로 필요한 경우, 데이터베이스를 이전의 특정 시점으로 복원할 수 있습니다.

데이터베이스가 [활성 지역 복제](sql-database-active-geo-replication.md)와 병렬 데이터베이스로 복원을 사용하는 경우 필요한 데이터를 복원된 사본에서 원래 데이터베이스로 복사하는 것이 좋습니다. 원본 데이터베이스를 복원된 데이터베이스로 바꾸는 경우 활성 지역 복제를 다시 구성하고 다시 동기화해야 합니다.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS Database-per-Tenant 애플리케이션 스크립트 가져오기

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 애플리케이션 소스 코드는 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 리포지토리에서 사용할 수 있습니다. Wingtip Tickets SaaS 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 참조하세요.

## <a name="before-you-start"></a>시작하기 전에

데이터베이스가 생성되면 복원할 수 있는 첫 번째 전체 백업이 되기까지 10~15분 정도 걸립니다. 애플리케이션을 설치한 지 얼마 지나지 않았다면 이 시나리오를 진행하기 전에 조금 기다려야 할 수도 있습니다.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>실수로 데이터를 삭제하는 테넌트 시뮬레이션

복구 시나리오를 진행하려면 먼저 테넌트 데이터베이스 중 하나에서 이벤트를 “실수로” 삭제합니다. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Events 앱을 열어 현재 이벤트 검토

1. 이벤트 허브(http://events.wtp.&lt;user&gt;.trafficmanager.net)를 열고 **Contoso Concert Hall**을 선택합니다.

   ![이벤트 허브](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. 이벤트 목록을 스크롤하고 목록의 마지막 이벤트를 적어 둡니다.

   ![마지막 이벤트가 표시됨](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>마지막 이벤트를 “실수로” 삭제하기

1. PowerShell ISE에서 ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\*Demo-RestoreTenant.ps1*을 열고 다음 값을 설정합니다.

   * **$DemoScenario** = **1**, *Delete last event (with no ticket sales)*
2. F5 키를 눌러 스크립트를 실행하고 마지막 이벤트를 삭제합니다. 다음과 같은 확인 메시지가 표시됩니다.

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Contoso 이벤트 페이지가 열립니다. 아래로 스크롤하여 해당 이벤트가 삭제되었는지 확인합니다. 해당 이벤트가 여전히 목록에 있으면 **새로 고침**을 선택하고 삭제되었는지 확인합니다.
   ![마지막 이벤트 제거됨](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>프로덕션 데이터베이스와 병렬로 테넌트 데이터베이스 복원

이 연습에서는 이벤트를 삭제하기 전 특정 시점의 Contoso Concert Hall 데이터베이스를 복원합니다. 이 시나리오에서는 병렬 데이터베이스에서 삭제된 데이터를 검토하려는 경우를 가정합니다.

 *Restore-TenantInParallel.ps1* 스크립트가 *ContosoConcertHall\_old*라는 이름의 병렬 테넌트 데이터베이스와 병렬 카탈로그 항목을 생성합니다. 이 복원 패턴은 사소한 데이터 손실로부터 복구하는 데 가장 적합합니다. 준수 또는 감사 목적으로 데이터를 검토해야 하는 경우에도 이 패턴을 사용할 수 있습니다. [활성 지역 복제](sql-database-active-geo-replication.md)를 사용할 때 권장되는 방법입니다.

1. [실수로 데이터를 삭제하는 테넌트 시뮬레이션](#simulate-a-tenant-accidentally-deleting-data) 섹션의 단계를 수행합니다.
2. PowerShell ISE에서 ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_을 엽니다.
3. **$DemoScenario** = **2**, *Restore tenant in parallel*을 설정합니다.
4. 스크립트를 실행하려면 F5 키를 누릅니다.

스크립트가 테넌트 데이터베이스를 사용자가 이벤트를 삭제하기 전의 시점으로 복원합니다. 데이터베이스가 _ContosoConcertHall\_old_라는 이름의 새 데이터베이스로 복원됩니다. 복원된 데이터베이스의 카탈로그 메타데이터가 삭제된 다음 데이터베이스가 *ContosoConcertHall\_old* 이름으로 만든 키를 사용하여 카탈로그에 추가됩니다.

데모 스크립트가 브라우저에서 새 테넌트 데이터베이스의 이벤트 페이지를 엽니다. URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old```를 보면 이 페이지가 이름에 *_old*가 추가된 복원된 데이터베이스의 데이터를 보여주고 있다는 사실을 알 수 있습니다.

브라우저에 나열된 이벤트를 스크롤하여 이전 섹션에서 삭제한 이벤트가 복원되었는지 확인합니다.

복원된 테넌트를 자체 이벤트 앱을 갖는 추가 테넌트로 노출하는 것은 일반적으로 테넌트에 복원된 데이터에 대한 액세스 권한을 제공하는 방법은 아닙니다. 여기서는 복원 패턴을 설명하기 위한 용도로 이 방법을 사용했습니다. 일반적으로 기존 데이터에 대한 읽기 전용 권한을 부여하고 복원된 데이터베이스를 지정된 기간 동안 보존하는 경우가 많습니다. 작업을 마친 후 샘플에서 _Remove restored tenant_ 시나리오를 실행하여 복원된 테넌트 항목을 삭제할 수 있습니다.

1. **$DemoScenario** = **4**, *Remove restored tenant*를 설정합니다.
2. 스크립트를 실행하려면 F5 키를 누릅니다.
3. 이제 카탈로그에서 *ContosoConcertHall\_old* 항목이 삭제됩니다. 브라우저에서 이 테넌트의 이벤트 페이지를 닫습니다.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>원래 위치에 테넌트 복원 및 기존 테넌트 데이터베이스 대체

이 예제에서는 Contoso Concert Hall 테넌트를 이벤트를 삭제하기 전 시점으로 복원합니다. *Restore-TenantInPlace* 스크립트가 테넌트 데이터베이스를 새 데이터베이스로 복원하고 기존 데이터베이스를 삭제합니다. 이 복원 패턴은 심각한 데이터 손상을 복구하는 데 적합하며, 테넌트에서 상당한 데이터 손실이 있을 수 있습니다.

1. PowerShell ISE에서 **Demo-RestoreTenant.ps1** 파일을 엽니다.
2. **$DemoScenario** = **5**, *Restore tenant in place*를 설정합니다.
3. 스크립트를 실행하려면 F5 키를 누릅니다.

스크립트가 테넌트 데이터베이스를 이벤트가 삭제되기 전 시점으로 복원합니다. 먼저 더 이상의 업데이트를 방지하기 위해 Contoso Concert Hall 테넌트가 오프라인 상태가 됩니다. 그런 다음, 복원 지점으로부터 복원하여 병렬 데이터베이스가 생성됩니다. 복원된 데이터베이스는 이름이 기존 테넌트 데이터베이스 이름과 충돌하지 않도록 이름에 타임스탬프가 추가되어 지정됩니다. 다음으로, 이전 테넌트 데이터베이스를 삭제하고, 복원된 데이터베이스 이름을 원본 데이터베이스 이름으로 바꿉니다. 마지막으로, Contoso Concert Hall이 온라인 상태가 되어 응용 프로그램에서 복원된 데이터베이스에 액세스할 수 있게 합니다.

이벤트를 삭제하기 전 특정 시점의 데이터베이스를 성공적으로 복원했습니다. **Events** 페이지가 열리면 마지막 이벤트가 복원되었는지 확인합니다.

데이터베이스를 복원하면 다시 복원할 수 있는 첫 번째 전체 백업이 수행될 때까지 10~15분 정도가 추가로 걸립니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 병렬 데이터베이스에 데이터베이스 복원(병렬)
> * 원래 위치에 데이터베이스 복원

[테넌트 데이터베이스 스키마 관리](saas-tenancy-schema-management.md) 자습서를 사용합니다.

## <a name="additional-resources"></a>추가 리소스

* [Wingtip SaaS 애플리케이션을 기반으로 작성된 추가 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)
* [SQL Database 백업에 대한 자세한 정보](sql-database-automated-backups.md)
