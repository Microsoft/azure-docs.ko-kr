---
title: Azure Stack에 SQL Server 워크로드 백업
description: 이 문서에서는 Azure Stack에서 SQL Server 데이터베이스를 보호 하도록 MABS (Microsoft Azure Backup Server)를 구성 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: e56b29f886224617a9ae13d58c8b3dd8dda0dcf8
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017961"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Azure Stack에 SQL Server 백업

이 문서를 사용하여 Azure Stack에서 SQL Server 데이터베이스를 보호하도록 MABS(Microsoft Azure Backup Server)를 구성할 수 있습니다.

Azure에 SQL Server 데이터베이스를 백업하고 Azure에서 데이터베이스를 복구하는 것을 관리하는 작업에는 세 가지 단계가 포함됩니다.

1. SQL server 데이터베이스를 보호하기 위한 백업 정책 만들기
2. 주문형 백업 복사본을 만들기
3. Azure 및 디스크에서 데이터베이스 복구

## <a name="prerequisites-and-limitations"></a>필수 구성 요소 및 제한 사항

* 데이터베이스에 원격 파일 공유의 파일이 포함되어 있으면 오류 ID 104를 나타내며 보호가 실패합니다. MABS는 원격 파일 공유의 SQL Server 데이터에 대 한 보호를 지원 하지 않습니다.
* MABS는 원격 SMB 공유에 저장 된 데이터베이스를 보호할 수 없습니다.
* [가용성 그룹 복제본이 읽기 전용으로 구성](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server?view=sql-server-ver15)되었는지 확인합니다.
* SQL Server의 Sysadmin 그룹에 **Ntauthority\system** 시스템 계정을 명시적으로 추가 해야 합니다.
* 부분적으로 포함된 데이터베이스를 대체 위치에 복구하는 경우 대상 SQL 인스턴스에서 [포함된 데이터베이스](/sql/relational-databases/databases/migrate-to-a-partially-contained-database?view=sql-server-ver15#enable) 기능을 사용하도록 설정했는지 확인해야 합니다.
* 파일 스트림 데이터베이스를 대체 위치에 복구하는 경우 대상 SQL 인스턴스에서 [파일 스트림 데이터베이스](/sql/relational-databases/blob/enable-and-configure-filestream?view=sql-server-ver15) 기능을 사용하도록 설정했는지 확인해야 합니다.
* SQL Server AlwaysOn 보호:
  * MABS는 보호 그룹을 만들 때 조회를 실행할 때 가용성 그룹을 검색 합니다.
  * MABS는 장애 조치 (failover)를 감지 하 고 데이터베이스 보호를 계속 합니다.
  * MABS는 SQL Server 인스턴스에 대 한 다중 사이트 클러스터 구성을 지원 합니다.
* AlwaysOn 기능을 사용 하는 데이터베이스를 보호 하는 경우 MABS에는 다음과 같은 제한 사항이 있습니다.
  * MABS는 다음과 같이 백업 기본 설정에 따라 SQL Server에 설정 된 가용성 그룹에 대 한 백업 정책을 적용 합니다.
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
  * SQL Server 2014에는 [Microsoft Azure Blob Storage에서 온-프레미스 SQL Server에 대한 데이터베이스](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-ver15)를 만드는 기능이 새로 추가되었습니다. 이 구성을 보호 하는 데 MABS를 사용할 수 없습니다.
  * SQL AlwaysOn 옵션에 대 한 "보조" 백업 기본 설정에 대 한 몇 가지 알려진 문제가 있습니다. MABS는 항상 보조 복제본에서 백업을 수행 합니다. 보조 데이터베이스를 찾을 수 없는 경우 백업이 실패 합니다.

## <a name="before-you-start"></a>시작하기 전에

[Azure Backup Server를 설치하고 준비](backup-mabs-install-azure-stack.md)합니다.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Azure에 대해 SQL server 데이터베이스를 보호하기 위한 백업 정책을 만듭니다.

1. Azure Backup Server UI에서 **보호** 작업 영역을 클릭합니다.

2. 도구 리본에서 **새로 만들기**를 클릭하여 새 보호 그룹을 만듭니다.

    ![보호 그룹 만들기](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server가 **보호 그룹**을 만드는 방법을 안내하는 보호 그룹 마법사를 시작합니다. **다음**을 클릭합니다.

3. **보호 그룹 형식 선택** 화면에서 **서버**를 선택합니다.

    ![선택하는 보호 그룹 종류 - '서버'](./media/backup-azure-backup-sql/pg-servers.png)

4. **그룹 멤버 선택** 화면에서, 사용 가능한 멤버 목록에 다양한 데이터 원본이 표시됩니다. **+** 폴더를 확장 하 고 하위 폴더를 표시 하려면 클릭 합니다. 확인란을 클릭하여 항목을 선택합니다.

    ![SQL DB를 선택합니다.](./media/backup-azure-backup-sql/pg-databases.png)

    선택한 모든 항목은 선택한 멤버 목록에 표시됩니다. 보호할 서버 또는 데이터베이스를 선택한 후 **다음**을 클릭합니다.

5. **데이터 보호 방법 선택** 화면에서 보호 그룹의 이름을 입력하고 **온라인 보호를 사용하려고 합니다** 확인란을 선택합니다.

    ![데이터 보호 방법 - 단기 디스크 및 온라인 Azure](./media/backup-azure-backup-sql/pg-name.png)

6. **단기 목표 지정** 화면에서, 디스크에 백업 지점을 만드는 데 필요한 입력을 포함하고 **다음**을 클릭합니다.

    이 예제에서 **보존 범위**는 **5일**, **동기화 빈도**는 백업 빈도인 **15분**입니다. **빠른 전체 Backup** 을 **오후 8시**로 설정합니다.

    ![단기 목표](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 표시된 예제는 매일 오후 8시에 전날의 오후 8시 백업 지점에서 수정된 데이터를 전송하여 백업 지점을 만듭니다. 이 프로세스를 **빠른 전체 Backup**이라고 합니다. 트랜잭션 로그는 15분마다 동기화됩니다. 오후 9시에 데이터베이스를 복구해야 하는 경우 마지막 고속 전체 백업 지점(이 예에서는 8PM)의 로그에서 지점이 생성됩니다.
   >
   >

7. **디스크 할당 검토** 화면에서 사용 가능한 전체 스토리지 공간 및 잠재적인 디스크 공간을 확인합니다. **다음**을 클릭합니다.

8. **복제본 만들기 방법 선택**에서, 첫 번째 복구 지점을 만들 방법을 선택합니다. 대역폭 정체를 방지하기 위해 초기 백업을 수동으로(오프 네트워크) 전송해도 되고 아니면 네트워크를 통해 전송해도 됩니다. 첫 번째 백업이 전송되기를 기다리기로 선택하는 경우 초기 전송의 시간을 지정할 수 있습니다. **다음**을 클릭합니다.

    ![초기 복제 방법](./media/backup-azure-backup-sql/pg-manual.png)

    초기 백업 복사본을 사용 하려면 프로덕션 서버 (SQL Server 컴퓨터)에서 Azure Backup Server로 전체 데이터 원본 (SQL Server 데이터베이스)을 전송 해야 합니다. 이 데이터는 규모가 클 수 있으며 네트워크를 통한 데이터 전송이 대역폭을 초과할 수 있습니다. 이 때문에 대역폭 혼잡을 방지하기 위해 최초 백업을 **수동**(이동식 미디어 사용)으로 전송해도 되고, **네트워크를 통해 자동으로 전송**(지정된 시간에)해도 됩니다.

    초기 백업을 완료하면 백업의 나머지 부분은 초기 백업 복사본의 증분 백업입니다. 증분 백업은 크기가 작으며 네트워크를 통해 간편하게 전송될 수 있습니다.

9. 일관성 확인을 실행하려는 경우 선택하고 **다음**을 클릭합니다.

    ![일관성 확인](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server는 백업 시점의 무결성을 지속적으로 확인합니다. Azure Backup Server 프로덕션 서버 (이 시나리오에서는 SQL Server 컴퓨터)에서 백업 파일의 체크섬을 계산 하 고 해당 파일의 백업 된 데이터를 계산 합니다. 충돌이 발생 하면 Azure Backup Server의 백업 파일이 손상 된 것으로 간주 됩니다. Azure Backup Server는 체크섬 불일치에 해당하는 블록을 전송하여 백업된 데이터를 균형 있게 조정합니다. 일관성 검사는 많은 성능이 필요한 작업이므로 일관성 검사를 예약하거나 자동으로 실행할 수 있습니다.

10. 데이터 원본에 온라인 보호를 지정하려면 데이터베이스를 Azure에 보호되도록 선택하고 **다음**을 클릭합니다.

    ![데이터 원본 선택](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. 조직 정책에 맞게 백업 일정 및 보존 정책을 선택합니다.

    ![일정 및 보존](./media/backup-azure-backup-sql/pg-schedule.png)

    이 예에서 백업은 매일 한 번 오후 12시 및 오후 8시에 수행됩니다.(화면의 아래쪽 부분)

    > [!NOTE]
    > 신속한 복구를 위해 디스크에 몇 가지 단기 복구 지점이 있는 것이 좋습니다. 이러한 복구 지점은 운영 복구에 사용됩니다. Azure는 높은 SLA 및 보장된 가용성이 있는 좋은 오프사이트 위치를 제공합니다.
    >
    >

    **모범 사례**: 로컬 디스크 백업이 완료되면 Azure로 백업을 시작하도록 예약하면 항상 마지막 디스크 백업이 Azure로 복사됩니다.

12. 보존 정책 일정을 선택합니다. 보존 정책이 작동하는 방법에 대한 자세한 내용은 [Azure Backup을 사용하여 테이프 인프라 대체 문서](backup-azure-backup-cloud-as-tape.md)에서 제공됩니다.

    ![보존 정책](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    이 예제에 대한 설명:

    * Backup은 매일 한 번 오후 12시 및 오후 8시에 수행되며(화면의 아래쪽 부분) 180일 동안 유지됩니다.
    * 토요일 오후 12시에 수행되는 백업은 104주 동안 유지됩니다.
    * 마지막 주 토요일 오후 12시에 수행되는 백업은 60개월 동안 유지됩니다.
    * 3월 마지막 주 토요일 오후 12시에 수행되는 백업은 10년 동안 유지됩니다.
13. **다음** 을 클릭하고 초기 백업 복사본을 Azure에 전송하기 위한 적절한 옵션을 선택합니다. **네트워크를 통해 자동으로**를 선택할 수 있습니다.

14. **요약** 화면에서 정책 세부 정보를 검토한 후에는 **그룹 만들기**를 클릭하여 워크플로를 완료합니다. **닫기**를 클릭하고 작업 영역 모니터링에서 작업 진행 상태를 모니터링할 수 있습니다.

    ![진행 중인 보호 그룹 만들기](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>SQL Server 데이터베이스의 주문형 백업

이전 단계가 백업 정책을 만드는 동안 첫 번째 백업이 발생하면 "복구 지점"을 만듭니다. 스케줄러가 시작되기를 기다리는 대신 다음 단계는 수동으로 복구 지점의 생성을 트리거합니다.

1. 복구 지점을 만들기 전에 보호 그룹 상태가 데이터베이스를 **확인**으로 표시될 때까지 대기합니다.

    ![보호 그룹 멤버](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **복구 지점 만들기**를 선택합니다.

    ![온라인 복구 지점 만들기](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. 드롭다운 메뉴에서 **온라인 보호**를 선택하고 **확인**을 클릭하여 Azure에서 복구 지점 만들기를 시작합니다.

    ![복구 지점 만들기](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. **모니터링** 작업 영역에서 작업 진행률을 봅니다.

    ![콘솔 모니터링](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Azure에서 SQL Server 데이터베이스 복구

Azure에서 보호되는 엔터티(SQL Server 데이터베이스)를 복구하려면 다음 단계가 필요합니다.

1. Azure Backup Server 관리 콘솔을 엽니다. 보호되는 서버를 볼 수 있는 **복구** 작업 영역으로 이동합니다. 필요한 데이터베이스로 이동합니다.(이 경우 ReportServer$ MSDPM2012) **온라인** 지점으로 지정 된 **복구** 시간을 선택 합니다.

    ![복구 지점 선택](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. 데이터베이스 이름을 마우스 오른쪽 단추로 클릭하고 **복구**를 클릭합니다.

    ![Azure에서 복구](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS에서는 복구 지점에 대한 세부 정보를 보여줍니다. **다음**을 클릭합니다. 데이터베이스를 덮어쓰려면 복구 형식 **SQL Server의 원본 인스턴스에 복구**를 선택합니다. **다음**을 클릭합니다.

    ![원래 위치로 복구](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    이 예제에서 MABS는 다른 SQL Server 인스턴스 또는 독립 실행형 네트워크 폴더로 데이터베이스를 복구합니다.

4. **복구 옵션 지정** 화면에서 네트워크 대역폭 사용량 제한을 같은 복구 옵션을 선택하여 복구에서 사용되는 대역폭을 제한할 수 있습니다. **다음**을 클릭합니다.

5. **요약** 화면에서는 지금까지 제공하는 모든 복구 구성을 표시합니다. **복구**를 클릭합니다.

    복구 상태는 복구 중인 데이터베이스를 표시합니다. **닫기**를 클릭하여 마법사를 닫고 **모니터링** 작업 영역에서 진행률을 봅니다.

    ![복구 프로세스 시작](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    복구가 완료되면 복원된 데이터베이스는 애플리케이션과 일치합니다.

## <a name="next-steps"></a>다음 단계

[파일 및 애플리케이션 백업](backup-mabs-files-applications-azure-stack.md) 문서를 참조하세요.
[Azure Stack에 SharePoint 백업](backup-mabs-sharepoint-azure-stack.md) 문서를 참조하세요.
