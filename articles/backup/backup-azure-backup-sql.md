---
title: DPM을 사용한 SQL Server 워크로드에 대한 Azure Backup
description: Azure Backup 서비스를 사용하여 SQL Server 데이터베이스를 백업하는 방법 소개
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: d7d94c7b238f8d413d8837c3c34468c6cd653fe3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60644137"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>SQL Server를 DPM 작업으로 Azure에 백업
이 문서는 Azure Backup을 사용한 SQL Server 데이터베이스 백업의 구성 단계를 안내합니다.

SQL Server 데이터베이스를 Azure에 백업하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

Azure에 SQL Server 데이터베이스를 백업하고 Azure에서 데이터베이스를 복구하는 것을 관리하는 작업에는 세 가지 단계가 포함됩니다.

1. Azure에 대해 SQL server 데이터베이스를 보호하기 위한 백업 정책을 만듭니다.
2. Azure에 주문형 백업 복사본을 만듭니다.
3. Azure에서 데이터베이스를 복구합니다.

## <a name="before-you-start"></a>시작하기 전에
시작하기 전에, 워크로드를 보호하기 위하여 Microsoft Azure Backup 사용을 위한 [필수 구성 요소](backup-azure-dpm-introduction.md#prerequisites-and-limitations)를 모두 충족하는지 확인합니다. 필수 구성 요소는 백업 저장소 만들기, 보관 자격 증명 다운로드, Azure Backup 에이전트 설치 및 저장소에 서버 등록 등의 작업들을 다룹니다.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Azure에 대해 SQL server 데이터베이스를 보호하기 위한 백업 정책을 만듭니다.
1. DPM 서버에서 **보호** 작업 영역을 클릭합니다.
2. 도구 리본에서 **새로 만들기**를 클릭하여 새 보호 그룹을 만듭니다.

    ![보호 그룹 만들기](./media/backup-azure-backup-sql/protection-group.png)
3. DPM은 **보호 그룹**을 만드는 지침으로 시작 화면을 표시합니다. **다음**을 클릭합니다.
4. **서버**를 선택합니다.

    ![선택하는 보호 그룹 종류 - '서버'](./media/backup-azure-backup-sql/pg-servers.png)
5. 백업할 데이터베이스가 있는 SQL Server 컴퓨터를 확장합니다. DPM은 해당 서버에서 백업할 수 있는 다양한 데이터 원본을 표시합니다. **모든 SQL 공유**를 확장하고 백업할 데이터베이스를 선택합니다(이 경우 ReportServer$ MSDPM2012 및 ReportServer$ MSDPM2012TempDB를 선택함). **다음**을 클릭합니다.

    ![SQL DB를 선택합니다.](./media/backup-azure-backup-sql/pg-databases.png)
6. 보호 그룹의 이름을 입력하고 **온라인 보호** 확인란을 선택합니다.

    ![데이터 보호 방법 - 단기 디스크 및 온라인 Azure](./media/backup-azure-backup-sql/pg-name.png)
7. **단기 목표 지정** 화면에서 필요한 입력을 포함하여 디스크에 백업 지점을 만듭니다.

    여기서 **보존 범위**가 *5일*, **동기화 빈도**가 백업이 수행되는 빈도인 *15분*마다 한 번으로 설정된 것을 확인합니다. **빠른 전체 Backup** 을 *오후 8시*로 설정합니다.

    ![단기 목표](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 백업 시점은 전날의 오후 8시 백업 시점에서 수정된 데이터를 전송하여 오후 8시에(화면 입력에 따라) 매일 만들어집니다. 이 프로세스를 **빠른 전체 Backup**이라고 합니다. 트랜잭션 로그를 15분마다 동기화하는 반면 오후 9시에 데이터베이스를 복구해야 할 경우 마지막 빠른 전체 백업 지점에서 로그를 재생하여 지점을 만듭니다(이 경우에 오후 8시).
   >
   >

8. **다음**을 누릅니다

    DPM에서는 사용 가능한 전체 저장소 공간 및 잠재적인 디스크 공간 사용률을 보여줍니다.

    ![디스크 할당](./media/backup-azure-backup-sql/pg-storage.png)

    기본적으로 DPM에서는 초기 백업 복사본에 사용되는 데이터 원본(SQL Server 데이터베이스)당 하나의 볼륨을 만듭니다. 이 방법을 사용하여 논리 디스크 관리자(LDM)는 DPM 보호를 300개 데이터 원본(SQL Server 데이터베이스)으로 제한합니다. 이 제한을 해결하려면 **DPM 저장소 풀에 데이터 배치**옵션을 선택합니다. 이 옵션을 사용하면 DPM에서 여러 데이터 원본에 단일 볼륨을 사용하므로 DPM이 최대 2000개의 SQL 데이터베이스를 보호할 수 있습니다.

    **볼륨 자동 증가** 옵션을 선택할 경우 프로덕션 데이터가 증가함에 따라 DPM은 백업 볼륨 증가를 처리할 수 있습니다. **볼륨 자동 증가** 옵션을 선택하지 않으면 DPM은 백업 저장소 사용을 보호 그룹의 데이터 원본으로 제한합니다.
9. 관리자는 해당 초기 백업을 수동으로 전송하도록 선택하여(오프 네트워크) 네트워크를 통한 대역폭 정체를 방지합니다. 또한 초기 전송이 발생할 수 있는 시간을 구성할 수 있습니다. **다음**을 클릭합니다.

    ![초기 복제 방법](./media/backup-azure-backup-sql/pg-manual.png)

    초기 백업 복사본은 프로덕션 서버(SQL Server 컴퓨터)에서 DPM 서버로 전체 데이터 원본(SQL Server 데이터베이스)을 전송해야 합니다. 이 데이터는 규모가 클 수 있으며 네트워크를 통한 데이터 전송이 대역폭을 초과할 수 있습니다. 이러한 이유로 관리자는 초기 백업을 대역폭 정체 방지를 위해 이동식 미디어를 사용하여 **수동**으로 전송할 수도 있고, 지정된 시간에 **네트워크를 통해 자동**으로 전송할 수도 있습니다.

    초기 백업을 완료하면 백업의 나머지 부분은 초기 백업 복사본의 증분 백업입니다. 증분 백업은 크기가 작으며 네트워크를 통해 간편하게 전송될 수 있습니다.
10. 일관성 확인을 실행하려는 경우 선택하고 **다음**을 클릭합니다.

    ![일관성 확인](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM은 일관성 확인을 수행하여 백업 시점의 무결성을 확인합니다. 프로덕션 서버(이 시나리오에서 SQL Server 컴퓨터)에서 백업 파일의 체크섬 및 DPM에서 해당 파일에 대한 백업 데이터를 계산합니다. 충돌이 있을 경우 DPM에서 백업된 파일이 손상되었음을 가정합니다. DPM은 체크섬 불일치에 해당하는 블록을 전송하여 백업된 데이터를 균형있게 조정합니다. 일관성 확인은 성능 집약적인 작업이므로 관리자는 일관성 확인을 예약하거나 자동으로 실행하도록 선택할 수 있습니다.
11. 데이터 원본에 온라인 보호를 지정하려면 데이터베이스를 Azure에 보호되도록 선택하고 **다음**을 클릭합니다.

    ![데이터 원본 선택](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. 관리자는 자신의 조직 정책에 맞게 백업 일정 및 보존 정책을 선택할 수 있습니다.

    ![일정 및 보존](./media/backup-azure-backup-sql/pg-schedule.png)

    이 예에서 백업은 매일 한 번 오후 12시 및 오후 8시에 수행됩니다.(화면의 아래쪽 부분)

    > [!NOTE]
    > 신속한 복구를 위해 디스크에 몇 가지 단기 복구 지점이 있는 것이 좋습니다. 이러한 복구 지점은 “운영 복구"에 사용됩니다. Azure는 높은 SLA 및 보장된 가용성이 있는 좋은 오프사이트 위치를 제공합니다.
    >
    >

    **모범 사례**: DPM을 사용하여 로컬 디스크 백업이 완료된 후에 Azure Backup을 예약합니다. 이를 통해 최신 디스크 백업이 Azure에 복사될 수 있습니다.

13. 보존 정책 일정을 선택합니다. 보존 정책이 작동하는 방법에 대한 자세한 내용은 [Azure Backup을 사용하여 테이프 인프라 대체 문서](backup-azure-backup-cloud-as-tape.md)에서 제공됩니다.

    ![보존 정책](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    이 예제에서:

    * Backup은 매일 한 번 오후 12시 및 오후 8시에 수행되며(화면의 아래쪽 부분) 180일 동안 유지됩니다.
    * 토요일 오후 12시에 수행되는 백업은 104주 동안 유지됩니다.
    * 마지막 주 토요일 오후 12시에 수행되는 백업은 60개월 동안 유지됩니다.
    * 3월 마지막 주 토요일 오후 12시에 수행되는 백업은 10년 동안 유지됩니다.
14. **다음** 을 클릭하고 초기 백업 복사본을 Azure에 전송하기 위한 적절한 옵션을 선택합니다. **네트워크를 통해 자동으로** 또는 **오프라인 Backup**을 선택할 수 있습니다.

    * **네트워크를 통해 자동으로** 는 백업에 선택한 일정에 따라 Azure에 백업 데이터를 전송합니다.
    * **오프 라인 Backup** 이 작동하는 방법을 [Azure Backup에서 오프라인 Backup 워크플로](backup-azure-backup-import-export.md)에서 설명합니다.

    관련 전송 메커니즘을 선택하여 초기 백업 복사본을 Azure로 보내고 **다음**을 클릭합니다.
15. **요약** 화면에서 정책 세부 정보를 검토하면 **그룹 만들기** 단추를 클릭하여 워크플로를 완료합니다. **닫기** 단추를 클릭하고 작업 영역 모니터링에서 작업 진행 상태를 모니터링합니다.

    ![진행 중인 보호 그룹 만들기](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>SQL Server 데이터베이스의 주문형 백업
이전 단계가 백업 정책을 만드는 동안 첫 번째 백업이 발생하면 "복구 지점"을 만듭니다. 스케줄러가 시작되기를 기다리는 대신 다음 단계는 수동으로 복구 지점의 생성을 트리거합니다.

1. 복구 지점을 만들기 전에 보호 그룹 상태가 데이터베이스를 **확인**으로 표시될 때까지 대기합니다.

    ![보호 그룹 멤버](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **복구 지점 만들기**를 선택합니다.

    ![온라인 복구 지점 만들기](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. 드롭다운 메뉴에서 **온라인 보호**를 선택하고 **확인**을 클릭합니다. 그러면 Azure에서 복구 지점 만들기가 시작됩니다.

    ![복구 지점 만들기](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. 다음 그림에서처럼 **모니터링** 작업 영역에서 진행 중인 작업의 진행 상황을 확인할 수 있습니다.

    ![콘솔 모니터링](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Azure에서 SQL Server 데이터베이스 복구
Azure에서 보호되는 엔터티(SQL Server 데이터베이스)를 복구하려면 다음 단계가 필요합니다.

1. DPM 서버 관리 콘솔을 엽니다. DPM에서 백업된 서버를 확인할 수 있는 **복구** 작업 영역으로 이동합니다. 필요한 데이터베이스로 이동합니다.(이 경우 ReportServer$ MSDPM2012) **온라인**으로 끝나는 시간**에서 복구**를 선택합니다.

    ![복구 지점 선택](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. 데이터베이스 이름을 마우스 오른쪽 단추로 클릭하고 **복구**를 클릭합니다.

    ![Azure에서 복구](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM에서는 복구 지점에 대한 세부 정보를 보여줍니다. **다음**을 클릭합니다. 데이터베이스를 덮어쓰려면 복구 형식 **SQL Server의 원본 인스턴스에 복구**를 선택합니다. **다음**을 클릭합니다.

    ![원래 위치로 복구](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    이 예제에서 DPM를 사용하면 다른 SQL Server 인스턴스 또는 독립 실행형 네트워크 폴더에 데이터베이스를 복구합니다.
4. **복구 옵션 지정** 화면에서 네트워크 대역폭 사용 제한을 같은 복구 옵션을 선택하여 복구에서 사용되는 대역폭을 제한할 수 있습니다. **다음**을 클릭합니다.
5. **요약** 화면에서는 지금까지 제공하는 모든 복구 구성을 표시합니다. **복구**를 클릭합니다.

    복구 상태는 복구 중인 데이터베이스를 표시합니다. **닫기**를 클릭하여 마법사를 닫고 **모니터링** 작업 영역에서 진행률을 봅니다.

    ![복구 프로세스 시작](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    복구가 완료되면 복원된 데이터베이스는 애플리케이션과 일치합니다.

### <a name="next-steps"></a>다음 단계:
•   [Azure Backup - FAQ](backup-azure-backup-faq.md)
