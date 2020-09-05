---
title: SQL Server 데이터베이스 백업 문제 해결
description: Azure Backup을 사용하여 Azure VM에서 실행되는 SQL Server 데이터베이스를 백업하는 경우의 문제 해결 정보입니다.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: b462ebd25a7dac4f215d599aa0dfa8665965fb2f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180952"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Azure Backup를 사용 하 여 SQL Server 데이터베이스 백업 문제 해결

이 문서에서는 Azure virtual machines에서 실행 되는 SQL Server 데이터베이스에 대 한 문제 해결 정보를 제공 합니다.

백업 프로세스 및 제한 사항에 대 한 자세한 내용은 [Azure vm의 SQL Server 백업 정보](sql-support-matrix.md#feature-considerations-and-limitations)를 참조 하세요.

## <a name="sql-server-permissions"></a>SQL Server 사용 권한

가상 머신에서 SQL Server 데이터베이스에 대 한 보호를 구성 하려면 해당 가상 머신에 **Azurebackupwindowsworkload 로드** 확장을 설치 해야 합니다. **Usererrorsqlnosysadminmembership**오류가 발생 하면 SQL Server 인스턴스에 필요한 백업 권한이 없는 것입니다. 이 오류를 해결 하려면 [VM 권한 설정](backup-azure-sql-database.md#set-vm-permissions)의 단계를 따르세요.

## <a name="troubleshoot-discover-and-configure-issues"></a>검색 및 구성 문제 해결

Recovery Services 자격 증명 모음을 만들고 구성한 후 데이터베이스를 검색 하 고 백업을 구성 하는 과정은 두 단계로 구성 됩니다.<br>

![백업 목표-Azure VM의 SQL Server](./media/backup-azure-sql-database/sql.png)

백업 구성 중에 SQL VM 및 해당 인스턴스가 **vm의 검색 db** 에 표시 되지 않고 백업 (위의 이미지 참조)을 **구성** 하는 경우 다음을 확인 합니다.

### <a name="step-1-discovery-dbs-in-vms"></a>1 단계: Vm에서 Db 검색

- VM이 검색 된 VM 목록에 나열 되어 있지 않고 다른 자격 증명 모음에 SQL backup에 등록 되어 있지 않은 경우에는 [검색 SQL Server 백업](./backup-sql-server-database-azure-vms.md#discover-sql-server-databases) 단계를 따릅니다.

### <a name="step-2-configure-backup"></a>2 단계: 백업 구성

- SQL VM이 데이터베이스를 보호 하는 데 사용 되는 것과 동일한 자격 증명 모음에 등록 된 경우 [백업 구성](./backup-sql-server-database-azure-vms.md#configure-backup) 단계를 따릅니다.

새 자격 증명 모음에 SQL VM을 등록 해야 하는 경우 이전 자격 증명 모음에서 등록 취소 해야 합니다.  자격 증명 모음에서 SQL VM의 등록을 취소 하려면 보호 된 모든 데이터 원본의 보호를 중지 하 고 백업 된 데이터를 삭제할 수 있습니다. 백업 된 데이터를 삭제 하는 작업은 안전 하지 않습니다.  SQL VM의 등록을 취소 하 고 모든 예방 조치를 수행한 후에는이 동일한 VM을 새 자격 증명 모음에 등록 하 고 백업 작업을 다시 시도 합니다.

## <a name="troubleshoot-backup-and-recovery-issues"></a>백업 및 복구 문제 해결  

때때로 백업 및 복원 작업에서 임의 오류가 발생할 수 있습니다. 그렇지 않으면 해당 작업이 중단 될 수 있습니다. 이는 VM의 바이러스 백신 프로그램이 원인일 수 있습니다. 가장 좋은 방법은 다음 단계를 수행 하는 것입니다.

1. 바이러스 백신 검사에서 다음 폴더를 제외 합니다.

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.Edp.AzureBackupWindowsWorkload`

    `C:\`가 나의 문자로 대체 합니다 *SystemDrive*.

1. VM 내에서 실행 되는 다음 세 프로세스를 바이러스 백신 검사에서 제외 합니다.

    - IaasWLPluginSvc.exe
    - IaasWorkloadCoordinaorService.exe
    - TriggerExtensionJob.exe

1. 또한 SQL은 바이러스 백신 프로그램 작업에 대 한 몇 가지 지침을 제공 합니다. 자세한 내용은 [이 문서](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) 를 참조 하세요.

## <a name="error-messages"></a>오류 메시지

### <a name="backup-type-unsupported"></a>지원 되지 않는 백업 유형

| 심각도 | 설명 | 가능한 원인 | 권장 조치 |
|---|---|---|---|
| 경고 | 이 데이터베이스의 현재 설정은 연결 된 정책에 있는 특정 백업 유형을 지원 하지 않습니다. | <li>Master 데이터베이스에서는 전체 데이터베이스 백업 작업만 수행할 수 있습니다. 차등 백업 및 트랜잭션 로그 백업은 가능 하지 않습니다. </li> <li>단순 복구 모델의 모든 데이터베이스는 트랜잭션 로그의 백업을 허용 하지 않습니다.</li> | 데이터베이스 설정 sp 정책에 있는 모든 백업 유형이 지원 됩니다. 또는 지원 되는 백업 유형만 포함 하도록 현재 정책을 변경 합니다. 그렇지 않으면 예약 된 백업 중에 지원 되지 않는 백업 유형을 건너뛰지 않으며 요청 시 백업에 대 한 백업 작업이 실패 합니다.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 이 SQL 데이터베이스는 요청된 백업 유형을 지원하지 않습니다. | 요청된 백업 유형을 데이터베이스 복구 모델에서 허용하지 않을 때 발생합니다. 이 오류는 다음과 같은 상황에서 발생할 수 있습니다. <br/><ul><li>단순 복구 모델을 사용 하는 데이터베이스에서는 로그 백업을 허용 하지 않습니다.</li><li>Master 데이터베이스에 대해서는 차등 및 로그 백업이 허용 되지 않습니다.</li></ul>자세한 내용은 [SQL Server 복구 모델](/sql/relational-databases/backup-restore/recovery-models-sql-server) 설명서를 참조 하세요. | 단순 복구 모델에서 데이터베이스에 대 한 로그 백업이 실패 하는 경우 다음 옵션 중 하나를 시도 합니다.<ul><li>데이터베이스가 단순 복구 모드인 경우 로그 백업을 사용하지 않도록 설정합니다.</li><li>[SQL Server 설명서](/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) 를 사용 하 여 데이터베이스 복구 모델을 전체 또는 대량 로그로 변경 합니다. </li><li> 여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있고 복구 모델을 변경하지 않으려는 경우에는 오류를 무시합니다. 전체 및 차등 백업은 일정에 따라 작동합니다. 로그 백업은 예상대로 건너뜁니다.</li></ul>Master 데이터베이스이 고 차등 또는 로그 백업을 구성한 경우 다음 단계 중 하나를 사용 합니다.<ul><li>포털을 사용 하 여 master 데이터베이스의 백업 정책 일정을 전체로 변경 합니다.</li><li>여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있는 경우에는 오류를 무시합니다. 전체 백업은 일정에 따라 작동합니다. 차등 또는 로그 백업은 예상대로 발생하지 않습니다.</li></ul> |
| 충돌하는 작업이 동일한 데이터베이스에서 이미 실행 중이기 때문에 작업이 취소되었습니다. | 동시에 실행 되는 [백업 및 복원 제한 사항에 대 한 블로그 항목](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) 을 참조 하세요.| [SSMS (SQL Server Management Studio)를 사용 하 여 백업 작업을 모니터링할 수](manage-monitor-sql-database-backup.md)있습니다. 충돌 하는 작업이 실패 한 후 작업을 다시 시작 합니다.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| SQL 데이터베이스가 없습니다. | 데이터베이스 삭제되었거나 이름이 변경되었습니다. | 데이터베이스가 실수로 삭제되었거나 이름이 변경되었는지 확인합니다.<br/><br/> 데이터베이스가 실수로 삭제된 경우 백업을 계속하려면 데이터베이스를 원래 위치로 복원합니다.<br/><br/> 데이터베이스를 삭제 하 고 향후 백업이 필요 하지 않은 경우 Recovery Services 자격 증명 모음에서 백업 **중지** 를 선택 하 여 Backup **데이터 보관** 또는 **백업 데이터 삭제**를 선택 합니다. 자세한 내용은 [백업 SQL Server 데이터베이스 관리 및 모니터링](manage-monitor-sql-database-backup.md)을 참조 하세요.

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 로그 체인이 손상되었습니다. | 데이터베이스 또는 VM은 다른 백업 솔루션을 통해 백업 되며이를 통해 로그 체인이 잘립니다.|<ul><li>다른 백업 솔루션이나 스크립트를 사용 중인지 확인합니다. 이런 경우 백업 솔루션을 중지합니다. </li><li>백업이 주문형 로그 백업 인 경우 전체 백업을 트리거하여 새 로그 체인을 시작 합니다. 예약 된 로그 백업의 경우 Azure Backup 서비스가 자동으로 전체 백업을 트리거하여이 문제를 해결 하기 때문에 작업이 필요 하지 않습니다.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| Azure Backup에서 SQL 인스턴스에 연결할 수 없습니다. | Azure Backup SQL Server 인스턴스에 연결할 수 없습니다. | Azure Portal 오류 메뉴에서 추가 세부 정보를 사용 하 여 근본 원인을 좁힐 수 있습니다. 오류를 해결하려면 [SQL 백업 문제 해결](/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)을 참조하세요.<br/><ul><li>기본 SQL 설정이 원격 연결을 허용 하지 않는 경우 설정을 변경 합니다. 설정 변경에 대 한 자세한 내용은 다음 문서를 참조 하세요.<ul><li>[MSSQLSERVER_-1](/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>로그인 문제가 있는 경우 다음 링크를 사용 하 여 문제를 해결 하십시오.<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 이 데이터 원본에 대한 첫 번째 전체 백업이 없습니다. | 데이터베이스에 대한 전체 백업이 누락되었습니다. 로그 및 차등 백업은 전체 백업에 대 한 부모 이므로 차등 또는 로그 백업을 트리거하기 전에 전체 백업을 수행 해야 합니다. | 주문형 전체 백업을 트리거합니다.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 데이터 원본의 트랜잭션 로그가 꽉 차서 백업을 수행할 수 없습니다. | 데이터베이스 트랜잭션 로그 공간이 꽉 찼습니다. | 이 문제를 해결 하려면 [SQL Server 설명서](/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)를 참조 하세요. |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 대상 위치에 이름이 같은 데이터베이스가 이미 있습니다. | 대상 복원 대상에 이미 같은 이름의 데이터베이스가 있습니다.  | <ul><li>대상 데이터베이스 이름을 변경 합니다.</li><li>또는 복원 페이지에서 강제 덮어쓰기 옵션을 사용 합니다.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 데이터베이스를 오프라인 상태로 만들지 못해서 복원에 실패했습니다. | 복원을 수행 하는 동안 대상 데이터베이스를 오프 라인 상태로 전환 해야 합니다. Azure Backup이 데이터를 오프 라인으로 전환할 수 없습니다. | Azure Portal 오류 메뉴에서 추가 세부 정보를 사용 하 여 근본 원인을 좁힐 수 있습니다. 자세한 내용은 [SQL Server 설명서](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)를 참조하세요. |

### <a name="wlextgenericiofaultusererror"></a>WlExtGenericIOFaultUserError

|오류 메시지 |가능한 원인  |권장 작업  |
|---------|---------|---------|
|작업을 수행 하는 동안 입/출력 오류가 발생 했습니다. 가상 머신에서 일반적인 IO 오류를 확인 하세요.   |   대상에 대 한 액세스 권한 또는 공간 제약 조건입니다.       |  가상 머신에서 일반적인 IO 오류를 확인 합니다. 컴퓨터의 대상 드라이브/네트워크 공유가 있는지 확인 합니다. <li> 컴퓨터에서 NT 권한 \ 계정에 대 한 읽기/쓰기 권한이 있습니다. <li> 작업을 성공적으로 완료 하는 데 충분 한 공간이 있습니다.<br> 자세한 내용은 [파일로 복원](restore-sql-database-azure-vm.md#restore-as-files)을 참조 하세요.
       |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 대상에서 지문이 포함된 서버 인증서를 찾을 수 없습니다. | 대상 인스턴스의 master 데이터베이스에 유효한 암호화 지문이 없습니다. | 원본 인스턴스에서 사용 되는 유효한 인증서 지문을 대상 인스턴스로 가져옵니다. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 복구에 사용되는 로그 백업에 대량 로그된 변경 내용이 포함되어 있습니다. SQL 지침에 따라 임의의 시점에서 중지 하는 데 사용할 수 없습니다. | 데이터베이스가 대량 로그 복구 모드인 경우 대량 로그 트랜잭션과 다음 로그 트랜잭션 간의 데이터를 복구할 수 없습니다. | 복구를 위해 다른 지정 시간을 선택 합니다. [자세한 정보를 알아보세요](/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 가용성 그룹의 일부 노드가 등록되지 않아서 SQL Always On 가용성 그룹에 대한 백업 기본 설정을 충족할 수 없습니다. | 백업을 수행 하는 데 필요한 노드가 등록 되지 않았거나 연결할 수 없습니다. | <ul><li>이 데이터베이스의 백업을 수행 하는 데 필요한 모든 노드가 등록 되어 있고 정상 상태 인지 확인 한 후 작업을 다시 시도 하십시오.</li><li>SQL Server Always On 가용성 그룹에 대 한 백업 기본 설정을 변경 합니다.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| SQL 서버 VM이 종료되어 Azure Backup 서비스에 액세스할 수 없습니다. | VM이 종료 됩니다. | SQL Server 인스턴스가 실행 중인지 확인 합니다. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| Azure Backup 서비스가 Azure VM 게스트 에이전트를 사용하여 백업을 수행하는 데 대상 서버에서 게스트 에이전트를 사용할 수 없습니다. | 게스트 에이전트가 사용 하도록 설정 되어 있지 않거나 비정상입니다. | [VM 게스트 에이전트를 수동으로 설치](../virtual-machines/extensions/agent-windows.md)합니다. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 자동 보호 의도가 제거되었거나 더 이상 유효하지 않습니다. | SQL Server 인스턴스에서 자동 보호를 사용 하도록 설정 하는 경우 해당 인스턴스의 모든 데이터베이스에 대해 백업 작업을 실행 하도록 **구성** 합니다. 작업을 실행하는 동안 자동 보호를 사용하지 않도록 설정하는 경우 **In-Progress** 작업이 이 오류 코드로 취소됩니다. | 나머지 데이터베이스를 모두 보호 하려면 자동 보호를 다시 사용 하도록 설정 합니다. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
24 시간 동안 허용 되는 작업 수에 대 한 제한에 도달 하 여 작업이 차단 되었습니다. | 24 시간 범위의 작업에 허용 되는 최대 제한에 도달 하면이 오류가 나타납니다. <br> 예를 들어 매일 트리거할 수 있는 백업 구성 작업 수에 대 한 제한에 도달 하 고 새 항목에 백업을 구성 하려고 하면이 오류가 표시 됩니다. | 일반적으로 24 시간 후에 작업을 다시 시도 하면이 문제가 해결 됩니다. 그러나 문제가 계속 되 면 Microsoft 지원에 문의 하 여 도움을 받을 수 있습니다.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
자격 증명 모음이 24 시간 범위에서 허용 되는 작업의 최대 제한에 도달 하 여 작업이 차단 되었습니다. | 24 시간 범위의 작업에 허용 되는 최대 제한에 도달 하면이 오류가 나타납니다. 이 오류는 일반적으로 정책 수정 또는 자동 보호와 같은 규모에 따라 작업을 수행 하는 경우에 나타납니다. CloudDosAbsoluteLimitReached의 경우와 달리이 상태를 해결 하기 위해 수행할 수 있는 작업은 많지 않습니다. 실제로 Azure Backup 서비스는 문제의 모든 항목에 대해 내부적으로 작업을 다시 시도 합니다.<br> 예를 들어 정책을 사용 하 여 보호 되는 데이터 원본 수가 많은 경우 해당 정책을 수정 하려고 하면 보호 된 각 항목에 대 한 보호 작업 구성이 트리거되고 때로는 매일 이러한 작업에 허용 되는 최대 제한에 도달할 수 있습니다.| Azure Backup 서비스는 24 시간 후에이 작업을 자동으로 다시 시도 합니다.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
인터넷 연결 문제로 인해 VM이 Azure Backup 서비스에 연결할 수 없습니다. | VM Azure Backup 서비스, Azure Storage 또는 Azure Active Directory 서비스에 대 한 아웃 바운드 연결이 필요 합니다.| -NSG를 사용 하 여 연결을 제한 하는 경우 AzureBackup 서비스 태그를 사용 하 여 Azure Backup 서비스, Azure Storage 또는 Azure Active Directory 서비스에 대 한 아웃 바운드 액세스를 허용 해야 합니다. 액세스 권한을 부여 하려면 다음 [단계](./backup-sql-server-database-azure-vms.md#nsg-tags) 를 수행 합니다.<br>-DNS가 Azure 끝점을 확인 하 고 있는지 확인 합니다.<br>-VM이 인터넷 액세스를 차단 하는 부하 분산 장치 뒤에 있는지 확인 합니다. Vm에 공용 IP를 할당 하면 검색은 작동 합니다.<br>-위의 세 대상 서비스에 대 한 호출을 차단 하는 방화벽/바이러스 백신/프록시가 없는지 확인 합니다.

## <a name="re-registration-failures"></a>다시 등록 오류

다시 등록 작업을 트리거하기 전에 하나 이상의 다음 증상을 확인하세요.

- **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**오류 코드 중 하나를 사용 하 여 VM에서 모든 작업 (예: 백업, 복원 및 구성 백업)이 실패 합니다.
- 백업 항목에 대한 **백업 상태** 영역에 **연결할 수 없음**이 표시되면 동일한 상태를 유발하는 다른 모든 원인을 제외합니다.

  - VM에서 백업 관련 작업을 수행할 수 있는 권한이 없습니다.
  - 백업을 수행할 수 없으므로 VM을 종료 합니다.
  - 네트워크 문제

   ![VM 다시 등록](./media/backup-azure-sql-database/re-register-vm.png)

- Always On 가용성 그룹의 경우 백업 기본 설정을 변경 하거나 장애 조치 (failover) 후에 백업이 실패 하기 시작 합니다.

다음 이유 중 하나 이상으로 인해 이러한 현상이 발생할 수 있습니다.

- 확장이 삭제되었거나 포털에서 제거되었습니다.
- **프로그램 제거 또는 변경**아래의 VM에 있는 **제어판** 에서 확장이 제거 되었습니다.
- VM이 내부 디스크 복원을 통해 다시 복원되었습니다.
- 오랜 기간 동안 VM이 종료되었으므로 확장 구성이 만료되었습니다.
- VM이 삭제되고 삭제된 VM과 이름이 같고 동일한 리소스 그룹에 있는 다른 VM이 생성되었습니다.
- 가용성 그룹 노드 중 하나가 전체 백업 구성을 받지 못했습니다. 이는 가용성 그룹이 자격 증명 모음에 등록 되거나 새 노드가 추가 될 때 발생할 수 있습니다.

위의 시나리오에서 VM에 대한 다시 등록 작업을 트리거하는 것이 좋습니다. PowerShell에서이 작업을 수행 하는 방법에 대 한 지침은 [여기](./backup-azure-sql-automation.md#enable-backup) 를 참조 하세요.

## <a name="size-limit-for-files"></a>파일의 크기 제한

파일의 전체 문자열 크기는 파일 수 뿐만 아니라 해당 이름 및 경로에 따라 달라 집니다. 각 데이터베이스 파일에 대해 논리적 파일 이름 및 실제 경로를 가져옵니다. 다음 SQL 쿼리를 사용할 수 있습니다.

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

이제 다음 형식으로 정렬 합니다.

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

예를 들면 다음과 같습니다.

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

콘텐츠의 문자열 크기가 2만 바이트를 초과 하는 경우 데이터베이스 파일은 다르게 저장 됩니다. 복구 중에는 복원에 대 한 대상 파일 경로를 설정할 수 없습니다. 파일은 SQL Server에서 제공 하는 기본 SQL 경로에 복원 됩니다.

### <a name="override-the-default-target-restore-file-path"></a>기본 대상 복원 파일 경로를 재정의 합니다.

복원 작업 중에 데이터베이스 파일의 매핑이 포함 된 JSON 파일을 대상 복원 경로에 배치 하 여 대상 복원 파일 경로를 재정의할 수 있습니다. 파일을 만들고 `database_name.json` 위치에 배치 `C:\Program Files\Azure Workload Backup\bin\plugins\SQL*` 합니다.

파일의 내용은 다음과 같은 형식 이어야 합니다.

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

예를 들면 다음과 같습니다.

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

위의 콘텐츠에서 다음 SQL 쿼리를 사용 하 여 데이터베이스 파일의 논리적 이름을 가져올 수 있습니다.

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
```

복원 작업을 트리거하기 전에이 파일을 배치 해야 합니다.

## <a name="next-steps"></a>다음 단계

SQL Server Vm (공개 미리 보기)에 대 한 Azure Backup에 대 한 자세한 내용은 [SQL vm에 대 한 Azure Backup](../azure-sql/virtual-machines/windows/backup-restore.md#azbackup)를 참조 하세요.
