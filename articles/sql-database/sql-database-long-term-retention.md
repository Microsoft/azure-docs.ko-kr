---
title: 최대 10 년 동안 백업 저장
description: Azure SQL Database에서 최대 10년 동안 전체 데이터베이스 백업을 저장하도록 지원하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 9c5534f2df4a375daf355d74f788b7f610f92919
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162160"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>최대 10년 동안 Azure SQL Database 백업 저장

여러 애플리케이션에서는 규정, 규정 준수 또는 기타 비즈니스를 목적으로 Azure SQL Database의 [자동 백업](sql-database-automated-backups.md)에서 제공하는 데이터베이스 백업을 7-35일 넘게 보존하도록 요구합니다. LTR (장기 보존) 기능을 사용 하면 최대 10 년 동안 읽기 액세스 지역 중복 저장소를 사용 하 여 Azure Blob storage에 지정 된 SQL database 전체 백업을 저장할 수 있습니다. 그런 다음, 새 데이터베이스로 모든 백업을 복원할 수 있습니다. Azure Storage 중복성에 대 한 자세한 내용은 [중복성 Azure Storage](../storage/common/storage-redundancy.md)를 참조 하세요.

> [!NOTE]
> 단일 데이터베이스와 풀링된 데이터베이스에 대해 LTR을 사용하도록 설정할 수 있습니다. Managed Instance의 인스턴스 데이터베이스에는 아직 LTR을 사용할 수 없습니다. SQL 에이전트 작업을 사용하여 35일이 초과된 LTR의 대안으로 [복사 전용 데이터베이스 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)을 예약할 수 있습니다.
> 

## <a name="how-sql-database-long-term-retention-works"></a>SQL Database의 장기 보존 작동 방법

LTR(장기 백업 보존)은 [자동 생성](sql-database-automated-backups.md)되는 전체 데이터베이스 백업을 활용하여 PITR(지정 시간 복원)을 지원합니다. LTR 정책이 구성 된 경우 이러한 백업은 장기 저장을 위해 다른 blob에 복사 됩니다. 복사 작업은 데이터베이스 워크 로드에 대 한 성능에 영향을 주지 않는 백그라운드 작업입니다. Ltr 백업은 LTR 정책에 설정 된 기간 동안 보존 됩니다. 각 SQL database에 대 한 LTR 정책은 LTR 백업 생성 빈도를 지정할 수도 있습니다. 이러한 유연성을 사용 하도록 설정 하려면 네 가지 매개 변수, 즉 주간 백업 보존 (W), 월별 백업 보존 (M), 연도별 백업 보존 (Y) 및 연간 주 (WeekOfYear)의 조합을 사용 하 여 정책을 정의할 수 있습니다. W를 지정하는 경우 매주 하나의 백업이 장기 스토리지에 복사됩니다. M을 지정하는 경우 각 월의 첫 주 동안 하나의 백업이 장기 스토리지에 복사됩니다. Y를 지정하는 경우 WeekOfYear로 지정된 주 동안 하나의 백업이 장기 스토리지에 복사됩니다. 각 백업은 이러한 매개 변수에 의해 지정된 기간 동안 장기 스토리지에 유지됩니다. LTR 정책의 변경 내용은 이후 백업에 적용 됩니다. 예를 들어, 지정 된 WeekOfYear 정책이 구성 될 때 과거에 있는 경우 첫 번째 LTR 백업은 다음 연도에 생성 됩니다. 

LTR 정책의 예:

-  W=0, M=0, Y=5, WeekOfYear=3

   각 연도의 세 번째 전체 백업은 5 년 동안 유지 됩니다.
   
- W=0, M=3, Y=0

   매월 첫 번째 전체 백업은 3 개월 동안 유지 됩니다.

- W=12, M=0, Y=0

   각각의 매주 전체 백업이 12주 동안 유지됩니다.

- W=6, M=12, Y=10, WeekOfYear=16

   매주 전체 백업이 6 주 동안 유지 됩니다. 매월 첫 번째 전체 백업을 제외하고 12개월 동안 유지됩니다. 연간 16번째 주에서 수행된 전체 백업을 제외하고 10년 동안 유지됩니다. 

다음 표는 다음 정책에 대한 장기 백업의 주기 및 만료를 나타냅니다.

W=12주(84일), M=12개월(365일), Y=10년(3650일), WeekOfYear=15(4월 15일 이후 주)

   ![ltr 예제](./media/sql-database-long-term-retention/ltr-example.png)



위의 정책을 수정 하 고 W = 0 (주간 백업 없음)을 설정 하는 경우 백업 복사본의 흐름은 위의 표에 표시 된 대로 강조 표시 된 날짜로 변경 됩니다. 이러한 백업을 유지하는 데 필요한 스토리지 용량은 적절하게 줄어듭니다. 

> [!IMPORTANT]
> 개별 LTR 백업의 타이밍은 Azure SQL Database에 의해 제어 됩니다. LTR 백업을 수동으로 만들거나 백업 생성 시간을 제어할 수 없습니다. LTR 정책을 구성한 후에는 첫 번째 LTR 백업이 사용 가능한 백업 목록에 표시 될 때까지 최대 7 일 걸릴 수 있습니다.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>지역 복제 및 장기 백업 보존

활성 지역 복제 또는 장애 조치 (failover) 그룹을 비즈니스 연속성 솔루션으로 사용 하는 경우 최종 장애 조치 (failover)를 준비 하 고 지역 보조 데이터베이스에서 동일한 LTR 정책을 구성 해야 합니다. 보조 복제본에서 백업이 생성 되지 않으므로 LTR 저장소 비용이 증가 하지 않습니다. 보조 데이터베이스가 주 데이터베이스가 되는 경우에만 백업이 생성됩니다. 장애 조치 (failover)가 트리거되고 주 복제본이 보조 지역으로 이동 될 때 중단 되지 않는 LTR 백업 생성을 보장 합니다. 

> [!NOTE]
> 원래 주 데이터베이스가 장애 조치 (failover)를 일으킨 중단에서 복구 되 면 새 보조 데이터베이스가 됩니다. 따라서 백업 생성이 다시 시작되지 않고 기존 LTR 정책은 다시 주 데이터베이스가 될 때까지 적용되지 않습니다. 

## <a name="configure-long-term-backup-retention"></a>장기 백업 보존 구성

Azure Portal 또는 PowerShell을 사용 하 여 장기 보존을 구성 하는 방법을 알아보려면 [장기 백업 보존 Azure SQL Database 관리](sql-database-long-term-backup-retention-configure.md)를 참조 하세요.

## <a name="restore-database-from-ltr-backup"></a>LTR 백업에서 데이터베이스 복원

LTR 스토리지에서 데이터베이스를 복원하기 위해 해당 타임스탬프에 따라 특정 백업을 선택할 수 있습니다. 데이터베이스는 원본 데이터베이스와 동일한 구독 아래의 기존 서버로 복원될 수 있습니다. Azure Portal 또는 PowerShell을 사용 하 여 LTR 백업에서 데이터베이스를 복원 하는 방법을 알아보려면 [장기 백업 보존 Azure SQL Database 관리](sql-database-long-term-backup-retention-configure.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략에서 필수입니다. 다른 SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
