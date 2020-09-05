---
title: SQL Server를 DPM 작업으로 Azure에 백업
description: Azure Backup 서비스를 사용 하 여 SQL Server 데이터베이스를 백업 하는 방법 소개
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e7877d9104fe1263368083eaabd99eae3bdc657b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017314"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>SQL Server를 DPM 작업으로 Azure에 백업

이 문서에서는 Azure Backup를 사용 하 여 SQL Server 데이터베이스를 백업 하는 구성 단계를 안내 합니다.

SQL Server 데이터베이스를 Azure에 백업하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분만에 무료 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 계정 만들기](https://azure.microsoft.com/pricing/free-trial/)를 참조 하세요.

Azure에 SQL Server 데이터베이스를 백업 하 고 Azure에서 복구 하려면 다음을 수행 합니다.

1. Azure에서 SQL Server 데이터베이스를 보호 하기 위한 백업 정책을 만듭니다.
1. Azure에서 주문형 백업 복사본을 만듭니다.
1. Azure에서 데이터베이스를 복구합니다.

>[!NOTE]
>DPM 2019 U R 2은 CSV (클러스터 공유 볼륨)를 사용 하 여 FCI (장애 조치 (Failover) 클러스터 인스턴스)를 SQL Server 지원 합니다.

## <a name="prerequisites-and-limitations"></a>필수 구성 요소 및 제한 사항

* 데이터베이스에 원격 파일 공유의 파일이 포함되어 있으면 오류 ID 104를 나타내며 보호가 실패합니다. DPM은 원격 파일 공유의 SQL Server 데이터에 대 한 보호를 지원 하지 않습니다.
* DPM은 원격 SMB 공유에 저장 된 데이터베이스를 보호할 수 없습니다.
* [가용성 그룹 복제본이 읽기 전용으로 구성](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server?view=sql-server-ver15)되었는지 확인합니다.
* SQL Server의 Sysadmin 그룹에 **Ntauthority\system** 시스템 계정을 명시적으로 추가 해야 합니다.
* 부분적으로 포함된 데이터베이스를 대체 위치에 복구하는 경우 대상 SQL 인스턴스에서 [포함된 데이터베이스](/sql/relational-databases/databases/migrate-to-a-partially-contained-database?view=sql-server-ver15#enable) 기능을 사용하도록 설정했는지 확인해야 합니다.
* 파일 스트림 데이터베이스를 대체 위치에 복구하는 경우 대상 SQL 인스턴스에서 [파일 스트림 데이터베이스](/sql/relational-databases/blob/enable-and-configure-filestream?view=sql-server-ver15) 기능을 사용하도록 설정했는지 확인해야 합니다.
* SQL Server AlwaysOn 보호:
  * 보호 그룹 만들기에서 쿼리를 실행할 때 DPM에서 사용 가능 그룹을 검색합니다.
  * DPM에서 장애 조치(failover)를 검색하고 데이터베이스를 계속 보호합니다.
  * DPM은 SQL Server의 인스턴스에 대한 멀티 사이트 클러스터 구성을 지원합니다.
* AlwaysOn 기능을 사용하는 데이터베이스를 보호하는 경우 DPM은 다음과 같은 제한 사항을 규정합니다.
  * DPM은 다음과 같은 백업 기본 설정에 따라 SQL Server에 설정 된 가용성 그룹에 대 한 백업 정책을 적용 합니다.
    * 보조 사용 - 주 복제본이 유일한 온라인 복제본인 경우를 제외하고는 보조 복제본에 대한 백업이 수행됩니다. 보조 복제본을 여러 개 사용할 수 있는 경우 백업 우선 순위가 가장 높은 노드가 백업 되도록 선택 됩니다. 주 복제본만 사용할 수 있는 경우에는 백업이 주 복제본에서 수행 되어야 합니다.
    * 보조만 - 주 복제본에 대해서는 백업이 수행되지 않습니다. 주 복제본이 유일한 온라인 복제본인 경우 백업이 수행되지 않습니다.
    * 주 - 항상 주 복제본 백업이 수행됩니다.
    * 임의의 복제본 - 가용성 그룹의 모든 가용성 복제본에 대해 백업을 수행할 수 있습니다. 백업할 노드는 각 노드의 백업 우선 순위에 따라 결정됩니다.
  * 다음 사항에 유의하세요.
    * 백업은 모든 읽기 가능한 복제본 (즉, 주, 동기 보조 복제본, 비동기 보조 복제본)에서 발생할 수 있습니다.
    * 백업에서 복제본이 제외 된 경우 (예: **복제본 제외** 가 사용 되도록 설정 되었거나 읽을 수 없음으로 표시 된 경우) 옵션을 사용 하 여 해당 복제본이 백업 되도록 선택 되지 않습니다.
    * 여러 복제본을 사용할 수 있고 읽을 수 있는 경우 백업 우선 순위가 가장 높은 노드가 백업 되도록 선택 됩니다.
    * 선택한 노드에서 백업이 실패 하면 백업 작업이 실패 합니다.
    * 원래 위치로의 복구는 지원 되지 않습니다.
* SQL Server 2014 이상 백업 문제:
  * SQL Server 2014에는 [Microsoft Azure Blob Storage에서 온-프레미스 SQL Server에 대한 데이터베이스](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-ver15)를 만드는 기능이 새로 추가되었습니다. DPM을 사용 하 여이 구성을 보호할 수 없습니다.
  * SQL AlwaysOn 옵션에 대 한 "보조" 백업 기본 설정에 대 한 몇 가지 알려진 문제가 있습니다. DPM은 항상 보조 데이터베이스에서 백업을 수행 합니다. 보조 데이터베이스를 찾을 수 없는 경우 백업이 실패 합니다.

## <a name="before-you-start"></a>시작하기 전에

시작 하기 전에 Azure Backup를 사용 하 여 워크 로드를 보호 하기 위한 [필수 구성 요소](backup-azure-dpm-introduction.md#prerequisites-and-limitations) 를 충족 하는지 확인 합니다. 몇 가지 필수 구성 요소 작업은 다음과 같습니다.

* 백업 자격 증명 모음을 만듭니다.
* 자격 증명 모음을 다운로드 합니다.
* Azure Backup 에이전트를 설치 합니다.
* 자격 증명 모음에 서버를 등록 합니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

Azure에서 SQL Server 데이터베이스를 보호 하려면 먼저 백업 정책을 만듭니다.

1. Data Protection Manager (DPM) 서버에서 **보호** 작업 영역을 선택 합니다.
1. 보호 그룹을 만들려면 **새로** 만들기를 선택 합니다.

    ![보호 그룹 만들기](./media/backup-azure-backup-sql/protection-group.png)
1. 시작 페이지에서 보호 그룹 만들기에 대 한 지침을 검토 합니다. **다음**을 선택합니다.
1. **서버**를 선택 합니다.

    ![서버 보호 그룹 유형 선택](./media/backup-azure-backup-sql/pg-servers.png)
1. 백업 하려는 데이터베이스가 있는 SQL Server 가상 컴퓨터를 확장 합니다. 해당 서버에서 백업할 수 있는 데이터 원본이 표시 됩니다. **모든 SQL 공유** 를 확장 하 고 백업 하려는 데이터베이스를 선택 합니다. 이 예에서는 ReportServer $ MSDPM2012 및 ReportServer $ MSDPM2012TempDB를 선택 합니다. **다음**을 선택합니다.

    ![SQL Server 데이터베이스 선택](./media/backup-azure-backup-sql/pg-databases.png)
1. 보호 그룹의 이름을 지정한 다음 **온라인 보호**를 선택 합니다.

    ![데이터 보호 방법 선택-단기 디스크 보호 또는 온라인 Azure 보호](./media/backup-azure-backup-sql/pg-name.png)
1. **단기 목표 지정** 페이지에서 디스크에 대 한 백업 위치를 만드는 데 필요한 입력을 포함 합니다.

    이 예에서는 **보존 범위** 를 *5 일로*설정 합니다. 백업 **동기화 빈도** 는 *15 분*마다 한 번으로 설정 됩니다. **빠른 전체 백업** 은 *오후 8:00*시로 설정 됩니다.

    ![백업 보호의 단기 목표 설정](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 이 예에서 백업 지점은 매일 8:00 PM에 생성 됩니다. 이전 날의 8:00 PM 백업 지점 이후 수정 된 데이터는 전송 됩니다. 이 프로세스를 **빠른 전체 Backup**이라고 합니다. 트랜잭션 로그는 15 분 마다 동기화 되지만 오후 9:00 시에 데이터베이스를 복구 해야 하는 경우 마지막 빠른 전체 백업 지점에서 로그를 재생 하 여 (이 예제에서는 8:00 PM) 지점을 만듭니다.
   >
   >

1. **다음**을 선택합니다. DPM은 사용 가능한 전체 저장소 공간을 보여 줍니다. 또한 잠재적인 디스크 공간 사용률을 보여 줍니다.

    ![디스크 할당 설정](./media/backup-azure-backup-sql/pg-storage.png)

    기본적으로 DPM은 데이터 원본 (SQL Server 데이터베이스) 당 하나의 볼륨을 만듭니다. 볼륨은 초기 백업 복사본에 사용 됩니다. 이 구성에서 LDM (논리 디스크 관리자)은 DPM 보호를 300 데이터 원본 (SQL Server 데이터베이스)으로 제한 합니다. 이 제한을 해결하려면 **DPM 스토리지 풀에 데이터 공동 배치**를 선택합니다. 이 옵션을 사용 하는 경우 DPM은 여러 데이터 원본에 단일 볼륨을 사용 합니다. 이렇게 설정 하면 DPM에서 최대 2000 SQL Server 데이터베이스를 보호할 수 있습니다.

    **볼륨 자동 증가**를 선택 하는 경우 프로덕션 데이터가 증가 함에 따라 DPM이 증가 하는 백업 볼륨을 고려해 야 할 수 있습니다. **볼륨 자동 증가**를 선택 하지 않으면 DPM이 보호 그룹의 데이터 원본으로 백업 저장소를 제한 합니다.

1. 관리자는이 초기 백업을 **네트워크를 통해 자동으로** 전송 하 고 전송 시간을 선택 하도록 선택할 수 있습니다. 또는 백업을 **수동으로** 전송 하도록 선택 합니다. **다음**을 선택합니다.

    ![복제본 만들기 방법 선택](./media/backup-azure-backup-sql/pg-manual.png)

    초기 백업 복사본을 사용 하려면 전체 데이터 원본 (SQL Server 데이터베이스)을 전송 해야 합니다. 백업 데이터는 프로덕션 서버 (SQL Server 컴퓨터)에서 DPM 서버로 이동 합니다. 이 백업이 크면 네트워크를 통해 데이터를 전송 하면 대역폭이 정체 될 수 있습니다. 이러한 이유로 관리자는 이동식 미디어를 사용 하 여 초기 백업을 **수동으로**전송 하도록 선택할 수 있습니다. 또는 지정 된 시간에 **네트워크를 통해 데이터를 자동으로** 전송할 수 있습니다.

    초기 백업이 완료 되 면 백업은 초기 백업 복사본에 대해 증분 방식으로 계속 됩니다. 증분 백업은 크기가 작으며 네트워크를 통해 간편하게 전송될 수 있습니다.

1. 일관성 확인을 실행할 시간을 선택 합니다. **다음**을 선택합니다.

    ![일관성 확인을 실행할 시기를 선택 하십시오.](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM은 백업 지점의 무결성에 대 한 일관성 확인을 실행할 수 있습니다. 프로덕션 서버 (이 예에서는 SQL Server 컴퓨터)에서 백업 파일의 체크섬과 DPM에서 해당 파일에 대 한 백업 된 데이터를 계산 합니다. 검사가 충돌을 발견 하면 DPM의 백업 된 파일이 손상 된 것으로 간주 됩니다. DPM은 체크섬 불일치에 해당 하는 블록을 전송 하 여 백업 된 데이터를 수정 합니다. 일관성 확인은 성능 집약적인 작업 이므로 관리자는 일관성 확인을 예약 하거나 자동으로 실행 하도록 선택할 수 있습니다.

1. Azure에서 보호할 데이터 원본을 선택 합니다. **다음**을 선택합니다.

    ![Azure에서 보호할 데이터 원본 선택](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. 관리자 인 경우 조직의 정책에 맞는 백업 일정 및 보존 정책을 선택할 수 있습니다.

    ![일정 및 보존 정책 선택](./media/backup-azure-backup-sql/pg-schedule.png)

    이 예제에서 백업은 매일 오후 12:00 시와 오후 8:00에 수행 됩니다.

    > [!TIP]
    > 빠른 복구를 위해 디스크에 몇 가지 단기 복구 지점이 유지 됩니다. 이러한 복구 지점은 운영 복구에 사용됩니다. Azure는 좋은 오프 사이트 위치 역할을 하며 높은 Sla 및 보장 된 가용성을 제공 합니다.
    >
    > DPM을 사용 하 여 로컬 디스크 백업이 완료 된 후 Azure 백업을 예약 합니다. 이 연습을 수행 하면 최신 디스크 백업이 Azure에 복사 됩니다.
    >

1. 보존 정책 일정을 선택합니다. 보존 정책의 작동 방법에 대 한 자세한 내용은 [Azure Backup를 사용 하 여 테이프 인프라 교체](backup-azure-backup-cloud-as-tape.md)를 참조 하세요.

    ![보존 정책 선택](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    이 예제에 대한 설명:

    * 백업은 매일 오후 12:00 시와 오후 8:00에 수행 됩니다. 180 일 동안 유지 됩니다.
    * 토요일 12:00 PM의 백업은 104 주 동안 보관 됩니다.
    * 매월 마지막 토요일 12:00 PM의 백업은 60 개월 동안 유지 됩니다.
    * 3 월의 마지막 토요일 12:00 PM에서의 백업은 10 년 동안 유지 됩니다.

    보존 정책을 선택한 후 **다음**을 선택 합니다.

1. 초기 백업 복사본을 Azure에 전송 하는 방법을 선택 합니다.

    * **네트워크를 통해 자동으로** 옵션은 백업 일정에 따라 데이터를 Azure로 전송 합니다.
    * **오프 라인 백업**에 대 한 자세한 내용은 [오프 라인 백업 개요](offline-backup-overview.md)를 참조 하세요.

    전송 메커니즘을 선택한 후 **다음**을 선택 합니다.

1. **요약** 페이지에서 정책 세부 정보를 검토 합니다. 그런 다음 **그룹 만들기**를 선택 합니다. **닫기** 를 선택 하 고 **모니터링** 작업 영역에서 작업 진행률을 볼 수 있습니다.

    ![보호 그룹 만들기 진행률](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>SQL Server 데이터베이스의 주문형 백업 복사본 만들기

첫 번째 백업이 발생 하면 복구 지점이 생성 됩니다. 예약 실행을 대기 하는 대신 복구 지점 만들기를 수동으로 트리거할 수 있습니다.

1. 보호 그룹에서 데이터베이스 상태가 **양호**인지 확인 합니다.

    ![데이터베이스 상태를 표시 하는 보호 그룹](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. 데이터베이스를 마우스 오른쪽 단추로 클릭 한 다음 **복구 지점 만들기**를 선택 합니다.

    ![온라인 복구 지점 만들기를 선택 합니다.](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. 드롭다운 메뉴에서 **온라인 보호**를 선택 합니다. 그런 다음 **확인** 을 선택 하 여 Azure에서 복구 지점 만들기를 시작 합니다.

    ![Azure에서 복구 지점 만들기 시작](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. **모니터링** 작업 영역에서 작업 진행률을 볼 수 있습니다.

    ![모니터링 콘솔에서 작업 진행률 보기](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Azure에서 SQL Server 데이터베이스 복구

Azure에서 SQL Server 데이터베이스와 같은 보호 된 엔터티를 복구 하려면 다음을 수행 합니다.

1. DPM 서버 관리 콘솔을 엽니다. **복구** 작업 영역으로 이동 하 여 DPM에서 백업 하는 서버를 확인 합니다. 데이터베이스 (이 예에서는 ReportServer $ MSDPM2012)를 선택 합니다. **온라인**으로 끝나는 **복구 시간** 을 선택 합니다.

    ![복구 지점 선택](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. 데이터베이스 이름을 마우스 오른쪽 단추로 클릭 하 고 **복구**를 선택 합니다.

    ![Azure에서 데이터베이스 복구](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM에서는 복구 지점에 대한 세부 정보를 보여줍니다. **다음**을 선택합니다. 데이터베이스를 덮어쓰려면 복구 형식 **SQL Server의 원본 인스턴스에 복구**를 선택합니다. **다음**을 선택합니다.

    ![데이터베이스를 원래 위치로 복구](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    이 예에서는 DPM을 사용 하 여 다른 SQL Server 인스턴스 또는 독립 실행형 네트워크 폴더에 데이터베이스를 복구할 수 있습니다.
1. **복구 옵션 지정** 페이지에서 복구 옵션을 선택할 수 있습니다. 예를 들어, **네트워크 대역폭 사용 제한을** 선택 하 여 복구에 사용 되는 대역폭을 제한할 수 있습니다. **다음**을 선택합니다.
1. **요약** 페이지에 현재 복구 구성이 표시 됩니다. **복구**를 선택 합니다.

    복구 상태에는 복구 중인 데이터베이스가 표시 됩니다. **닫기** 를 선택 하 여 마법사를 닫고 **모니터링** 작업 영역에서 진행률을 확인할 수 있습니다.

    ![복구 프로세스를 시작 합니다.](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    복구가 완료 되 면 복원 된 데이터베이스는 응용 프로그램과 일치 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [AZURE BACKUP FAQ](backup-azure-backup-faq.md)를 참조 하세요.
