---
title: Azure에 SQL Server 데이터베이스 백업
description: 이 문서에서는 Azure에 SQL Server를 백업하는 방법을 설명합니다. SQL Server 복구에 대해서도 설명합니다.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: d8cdafe215d9271151d8dacee114d40108e907bd
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892442"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Azure VM의 SQL Server 백업 정보

[Azure Backup](backup-overview.md) 은 Azure vm에서 실행 되는 SQL Server를 백업 하는 스트림 기반의 특수 솔루션을 제공 합니다. 이 솔루션은 제로 인프라 백업, 장기 보존 및 중앙 관리에 대 한 Azure Backup의 이점에 부합 합니다. 또한 SQL Server에 대해 특별히 다음과 같은 이점을 제공 합니다.

1. 모든 백업 유형을 지 원하는 작업 인식 백업-전체, 차등 및 로그
2. 로그 백업이 빈번한 15 분 RPO (복구 지점 목표)
3. 지정 시간 복구 (초)
4. 개별 데이터베이스 수준 백업 및 복원

현재 지원 되는 백업 및 복원 시나리오를 보려면 [지원 매트릭스](sql-support-matrix.md#scenario-support)를 참조 하세요.

## <a name="backup-process"></a>백업 프로세스

이 솔루션은 SQL 네이티브 API를 활용하여 SQL 데이터베이스를 백업합니다.

* 보호하려는 SQL Server VM을 지정하고 그 안에 있는 데이터베이스를 쿼리하면 Azure Backup 서비스가 `AzureBackupWindowsWorkload` 확장이라는 이름으로 VM에 워크로드 백업 확장을 설치합니다.
* 이 확장은 코디네이터 및 SQL 플러그 인으로 구성됩니다. 코디네이터는 백업 구성, 백업 및 복원 같은 다양한 워크플로를 트리거하는 역할을 담당하고, 플러그 인은 실제 데이터 흐름을 담당합니다.
* 이 VM에서 데이터베이스를 검색할 수 있도록 Azure Backup은 `NT SERVICE\AzureWLBackupPluginSvc` 계정을 만듭니다. 이 계정은 백업 및 복원에 사용되며 SQL sysadmin 권한이 필요합니다. `NT SERVICE\AzureWLBackupPluginSvc`계정은 [가상 서비스 계정](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)이므로 암호 관리가 필요 하지 않습니다. Azure Backup는 `NT AUTHORITY\SYSTEM` 데이터베이스 검색/조회를 위해 계정을 사용 하므로이 계정은 SQL의 공용 로그인 이어야 합니다. Azure Marketplace에서 SQL Server VM을 만들지 않은 경우 **UserErrorSQLNoSysadminMembership** 오류가 발생할 수 있습니다. 이 경우 [다음 지침을 따르세요](#set-vm-permissions).
* 선택한 데이터베이스에서 구성 보호를 트리거하면 백업 서비스가 백업 일정과 기타 정책 세부 정보를 사용하여 코디네이터를 설정하며, 이 경우 확장이 VM에 로컬로 캐시합니다.
* 예약된 시간에 코디네이터가 플러그 인과 통신하고 VDI를 사용하여 SQL 서버의 백업 데이터를 스트리밍하기 시작합니다.  
* 플러그 인은 데이터를 Recovery Services 자격 증명 모음으로 직접 전송 하므로 준비 위치가 필요 하지 않습니다. 데이터는 Azure Backup 서비스를 통해 암호화되어 스토리지 계정에 저장됩니다.
* 데이터 전송이 완료되면 코디네이터가 백업 서비스를 사용하여 커밋을 확인합니다.

  ![SQL 백업 아키텍처](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>시작하기 전에

시작 하기 전에 다음 요구 사항을 확인 합니다.

1. Azure에서 실행되는 SQL Server 인스턴스가 있는지 확인합니다. [SQL Server 인스턴스를 마켓플레이스에 빠르게 만들](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) 수 있습니다.
2. [기능 고려 사항](sql-support-matrix.md#feature-considerations-and-limitations) 및 [시나리오 지원](sql-support-matrix.md#scenario-support)을 검토 합니다.
3. 이 시나리오에 대한 [일반적인 질문을 검토](faq-backup-sql-server.md)합니다.

## <a name="set-vm-permissions"></a>VM 권한 설정

  SQL Server에서 검색을 실행하면 Azure Backup은 다음을 수행합니다.

* AzureBackupWindowsWorkload 확장을 추가합니다.
* 가상 머신에서 에서 데이터베이스를 검색하기 위해 NT SERVICE\AzureWLBackupPluginSvc 계정을 만듭니다. 이 계정은 백업 및 복원에 사용되며 SQL sysadmin 권한이 필요합니다.
* VM에서 실행 중인 데이터베이스를 검색하고 Azure Backup은 NT AUTHORITY\SYSTEM 계정을 사용합니다. 이 계정은 SQL에서 공용 로그인이어야 합니다.

Azure Marketplace에서 SQL Server VM를 만들지 않았거나 SQL 2008 또는 2008 r 2에 있는 경우 **Usererrorsqlnosysadminmembership** error를 받을 수 있습니다.

Windows 2008 r 2에서 실행 되는 **SQL 2008** 및 **2008** r 2의 경우 사용 권한을 부여 하려면 [여기](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)를 참조 하세요.

다른 모든 버전의 경우 다음 단계에 따라 권한을 수정합니다.

  1. SQL Server sysadmin 권한이 있는 계정을 사용하여 SSMS(SQL Server Management Studio)에 로그인합니다. 특별한 사용 권한이 필요하지 않으면 Windows 인증이 작동해야 합니다.
  2. SQL Server에서 **Security/로그인** 폴더를 엽니다.

      ![Security/Logins 폴더를 열어서 계정 보기](./media/backup-azure-sql-database/security-login-list.png)

  3. **로그인** 폴더를 마우스 오른쪽 단추로 클릭 하 고 **새 로그인**을 선택 합니다. **로그인 - 새로 만들기**에서 **검색**을 선택합니다.

      ![로그인 - 신규 대화 상자에서 검색 선택](./media/backup-azure-sql-database/new-login-search.png)

  4. Windows 가상 서비스 계정 **NT SERVICE\AzureWLBackupPluginSvc**는 가상 머신 등록 및 SQL 검색 단계 중에 만들어졌습니다. **선택할 개체 이름 입력**에 표시된 대로 계정 이름을 입력합니다. **이름 확인**을 선택하여 이름을 확인합니다. **확인**을 클릭합니다.

      ![이름 확인을 클릭하여 알 수 없는 서비스 이름 확인](./media/backup-azure-sql-database/check-name.png)

  5. **서버 역할**에서 **sysadmin** 역할이 선택되었는지 확인합니다. **확인**을 클릭합니다. 이제 필요한 권한이 있어야 합니다.

      ![sysadmin 서버 역할이 선택되어 있는지 확인](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. 이제 데이터베이스를 Recovery Services 자격 증명 모음에 연결합니다. Azure Portal의 **보호된 서버** 목록에서 오류 상태인 서버를 마우스 오른쪽 단추로 클릭하고 **DB 다시 검색**을 클릭합니다.

      ![서버서 적절한 권한이 있는지 확인](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. **알림** 영역에서 진행률을 확인합니다. 선택한 데이터베이스가 발견되면 성공 메시지가 표시됩니다.

      ![배포 성공 메시지](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> SQL Server에 여러 개의 SQL Server 인스턴스가 설치되어 있는 경우 **NT Service\AzureWLBackupPluginSvc** 계정에 대한 sysadmin 권한을 모든 SQL 인스턴스에 추가해야 합니다.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL 2008 및 SQL 2008 R2에 대한 SQL sysadmin 권한 부여

SQL Server 인스턴스에 **NT AUTHORITY\SYSTEM** 및 **NT Service\AzureWLBackupPluginSvc** 로그인을 추가합니다.

1. 개체 탐색기에서 SQL Server 인스턴스로 이동합니다.
2. 보안 -> 로그인으로 이동합니다.
3. 로그인을 마우스 오른쪽 단추로 클릭하고 *새 로그인…* 을 클릭합니다.

    ![SSMS를 사용한 새 로그인](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. 일반 탭으로 이동하여 로그인 이름에 **NT AUTHORITY\SYSTEM**을 입력합니다.

    ![SSMS의 로그인 이름](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. *서버 역할*로 이동하여 *public* 및 *sysadmin* 역할을 선택합니다.

    ![SSMS에서 역할 선택](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. *상태*로 이동합니다. 데이터베이스 엔진 연결 권한을 허용(*Grant*)하고 로그인을 사용(*Enabled*)으로 설정합니다.

    ![SSMS에서 권한 부여](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. 확인을 클릭합니다.
8. 동일한 일련의 단계(위의 1~7)를 반복하여 SQL Server 인스턴스에 NT Service\AzureWLBackupPluginSvc 로그인을 추가합니다. 로그인이 이미 있으면 sysadmin 서버 역할이 있는지 확인하고 상태에서 데이터베이스 엔진 연결 권한이 허용되고 로그인이 사용되는지 확인합니다.
9. 사용 권한을 부여 하 고 나면 포털에서 **db** 를 다시 검색 합니다. **->** Azure VM의 자격 증명 모음 백업 인프라 **->** 워크 로드:

    ![Azure Portal에서 DB 다시 검색](media/backup-azure-sql-database/sql-rediscover-dbs.png)

또는 관리 모드에서 다음 PowerShell 명령을 실행하여 권한 부여를 자동화할 수 있습니다. 인스턴스 이름은 MSSQLSERVER로 기본 설정됩니다. 필요한 경우 스크립트에서 인스턴스 이름 인수를 변경합니다.

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>다음 단계

* SQL Server 데이터베이스 백업에 대해 [자세히 알아봅니다](backup-sql-server-database-azure-vms.md).
* 백업된 SQL Server 데이터베이스를 복원하는 방법을 [알아봅니다](restore-sql-database-azure-vm.md).
* 백업된 SQL Server 데이터베이스를 관리하는 방법을 [알아봅니다](manage-monitor-sql-database-backup.md).
