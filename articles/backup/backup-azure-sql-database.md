---
title: Azure에 SQL Server 데이터베이스 백업
description: 이 문서에서는 Azure에 SQL Server를 백업하는 방법을 설명합니다. SQL Server 복구에 대해서도 설명합니다.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 39f2348a95be95a03dada45d48952dce99ec4ec7
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462581"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Azure VM의 SQL Server 백업 정보

SQL Server 데이터베이스는 낮은 RPO(복구 지점 목표)와 장기 보존이 필요한 중요한 워크로드입니다. [Azure Backup](backup-overview.md)을 사용하여 Azure VM에서 실행되는 SQL Server 데이터베이스를 백업할 수 있습니다.

## <a name="backup-process"></a>백업 프로세스

이 솔루션은 SQL 네이티브 API를 활용하여 SQL 데이터베이스를 백업합니다.

* 보호하려는 SQL Server VM을 지정하고 그 안에 있는 데이터베이스를 쿼리하면 Azure Backup 서비스가 `AzureBackupWindowsWorkload` 확장이라는 이름으로 VM에 워크로드 백업 확장을 설치합니다.
* 이 확장은 코디네이터 및 SQL 플러그 인으로 구성됩니다. 코디네이터는 백업 구성, 백업 및 복원 같은 다양한 워크플로를 트리거하는 역할을 담당하고, 플러그 인은 실제 데이터 흐름을 담당합니다.
* 이 VM에서 데이터베이스를 검색할 수 있도록 Azure Backup은 `NT SERVICE\AzureWLBackupPluginSvc` 계정을 만듭니다. 이 계정은 백업 및 복원에 사용되며 SQL sysadmin 권한이 필요합니다. `NT SERVICE\AzureWLBackupPluginSvc` 계정은 [Virtual Service Account](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)이므로 암호 관리가 필요하지 않습니다. Azure Backup은 데이터베이스 검색/조회에 `NT AUTHORITY\SYSTEM` 계정을 활용하므로, 이 계정은 SQL에서 공용 로그인이어야 합니다. Azure Marketplace에서 SQL Server VM을 만들지 않은 경우 **UserErrorSQLNoSysadminMembership** 오류가 발생할 수 있습니다. 이 경우 [다음 지침을 따르세요](#set-vm-permissions).
* 선택한 데이터베이스에서 구성 보호를 트리거하면 백업 서비스가 백업 일정과 기타 정책 세부 정보를 사용하여 코디네이터를 설정하며, 이 경우 확장이 VM에 로컬로 캐시합니다.
* 예약된 시간에 코디네이터가 플러그 인과 통신하고 VDI를 사용하여 SQL 서버의 백업 데이터를 스트리밍하기 시작합니다.  
* 플러그 인은 복구 서비스 자격 증명 모음에 직접 데이터를 보내기 때문에 준비 위치가 필요 없습니다. 데이터는 Azure Backup 서비스를 통해 암호화되어 스토리지 계정에 저장됩니다.
* 데이터 전송이 완료되면 코디네이터가 백업 서비스를 사용하여 커밋을 확인합니다.

  ![SQL 백업 아키텍처](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>시작하기 전 주의 사항

시작하기 전에 다음을 확인합니다.

1. Azure에서 실행되는 SQL Server 인스턴스가 있는지 확인합니다. [SQL Server 인스턴스를 마켓플레이스에 빠르게 만들](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) 수 있습니다.
2. [기능 고려 사항](#feature-consideration-and-limitations) 및 [시나리오 지원](#scenario-support)을 검토합니다.
3. 이 시나리오에 대한 [일반적인 질문을 검토](faq-backup-sql-server.md)합니다.

## <a name="scenario-support"></a>시나리오 지원

**지원** | **세부 정보**
--- | ---
**지원되는 배포** | SQL Marketplace Azure VM 및 비 Marketplace VM(SQL Server가 수동 설치됨)이 지원됩니다.
**지원되는 지역** | 오스트레일리아 남동부(ASE), 오스트레일리아 동부(AE), 오스트레일리아 중부(AC), 오스트레일리아 중부 2(AC) <br> 브라질 남부(BRS)<br> 캐나다 중부(CNC), 캐나다 동부(CE)<br> 동남 아시아(SEA), 동아시아(EA) <br> 미국 동부(EUS), 미국 동부 2(EUS2), 미국 중서부(WCUS), 미국 서부(WUS), 미국 서부 2(WUS 2), 미국 중북부(NCUS), 미국 중부(CUS), 미국 중남부(SCUS) <br> 인도 중부(INC), 인도 남부(INS), 인도 서부 <br> 일본 동부(JPE), 일본 서부(JPW) <br> 한국 중부(KRC), 한국 남부(KRS) <br> 북유럽(NE), 서유럽 <br> 영국 남부(UKS), 영국 서부(UKW) <br> US Gov 애리조나, US Gov 버지니아, US Gov 텍사스, US DoD 중부, US DoD 동부 <br> 독일 북부, 독일 중서부 <br> 스위스 북부, 스위스 서부 <br> 프랑스 중부 <br> 중국 동부, 중국 동부 2, 중국 북부, 중국 북부 2
**지원되는 운영 체제** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux는 현재 지원되지 않습니다.
**지원되는 SQL Server 버전** | [제품 수명 주기 페이지 검색](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)에 설명된 SQL Server 2019, SQL Server 2017, [제품 수명 주기 페이지 검색](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)에 설명된 SQL Server 2016 및 SP, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express
**지원되는 .NET 버전** | VM에 설치된 .NET Framework 4.5.2 이상

## <a name="feature-consideration-and-limitations"></a>기능 고려 사항 및 제한 사항

* SQL Server 백업은 Azure Portal 또는 **PowerShell**에서 구성할 수 있습니다. CLI는 지원되지 않습니다.
* 솔루션은 Azure Resource Manager VM과 클래식 VM의 두 종류 [배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)에서 모두 지원됩니다.
* SQL Server를 실행하는 VM에서 Azure 공용 IP 주소에 액세스하려면 인터넷 연결이 필요합니다.
* SQL Server **장애 조치 (Failover) 클러스터 인스턴스 (FCI)** 는 지원 되지 않습니다.
* 미러 데이터베이스와 데이터베이스 스냅샷에 대한 백업 및 복원 작업은 지원되지 않습니다.
* 백업 솔루션을 2개 이상 사용하여 독립 실행형 SQL Server 인스턴스 또는 SQL Always On 가용성 그룹을 백업하면 오류가 발생할 수 있으므로 그렇게 하지 말아야 합니다.
* 같은 솔루션 또는 다른 솔루션을 사용하여 한 가용성 그룹의 두 노드를 개별적으로 백업해도 오류가 발생할 수 있습니다.
* Azure Backup은 **읽기 전용** 데이터베이스에 전체 백업 및 복사 전용 전체 백업만 지원합니다.
* 많은 수의 파일이 있는 데이터베이스는 보호할 수 없습니다. 지원되는 최대 파일 수는 **1000**개입니다.  
* 하나의 자격 증명 모음에 최대 **2,000**개의 SQL Server 데이터베이스를 백업할 수 있습니다. 데이터베이스 수가 이보다 더 많은 경우 자격 증명 모음을 여러 개 만들면 됩니다.
* 한 번에 데이터베이스 **50**개까지 백업을 구성할 수 있습니다. 이 제한은 백업 부하 최적화에 도움이 됩니다.
* 최대 **2tb** 크기의 데이터베이스를 지원 합니다. 크기 보다 큰 경우 성능 문제가 발생할 수 있습니다.
* 서버당 보호할 수 있는 데이터베이스 수에 대한 의미를 이해 하려면 대역폭, VM 크기, 백업 빈도, 데이터베이스 크기 등의 요소를 고려해 야 합니다. VM 리소스 및 백업 정책에 따라 서버당 사용할 수 있는 데이터베이스의 대략적인 수를 제공 하는 resource planner를 [다운로드](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) 합니다.
* 가용성 그룹의 경우 백업은 몇 가지 요소에 따라 다른 노드에서 수행됩니다. 아래는 가용성 그룹에 대한 백업 동작을 요약한 것입니다.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Always On 가용성 그룹의 백업 동작

백업이 AG의 한 노드에서 구성되어 있는 것이 좋습니다. 백업은 항상 주 노드와 동일한 지역에서 구성되어야 합니다. 즉, 백업을 구성하는 지역에 항상 주 노드가 있어야 합니다. AG의 모든 노드가 백업이 구성되어 있는 곳과 동일한 지역에 있는 경우 문제가 없습니다.

#### <a name="for-cross-region-ag"></a>지역 간 AG

* 백업 기본 설정에 관계 없이 백업이 구성되어 있는 곳과 동일한 지역에 있지 않은 노드에서는 백업이 일어나지 않습니다. 따라서 지역 간 백업은 지원되지 않습니다. 노드가 두 개뿐이고 보조 노드가 다른 지역에 있는 경우, 백업은 계속 주 노드에서 발생합니다(백업 기본 설정이 ‘보조만’이 아닌 경우에 한 해).
* 백업이 구성되어 있는 곳과 다른 지역으로 장애 조치(failover)가 발생하는 경우 장애 조치 지역의 노드에서 백업이 실패합니다.

백업 기본 설정 및 백업 유형(전체/차등/로그/복사 전용 전체)에 따라 특정 노드(주/보조)에서 백업이 수행됩니다.

* **백업 기본 설정: 기본**

**백업 유형** | **노드**
    --- | ---
    전체 | 보조
    차등 | 보조
    로그 |  보조
    복사 전용 전체 |  보조

* **백업 기본 설정: 보조만**

**백업 유형** | **노드**
--- | ---
전체 | 보조
차등 | 보조
로그 |  주
복사 전용 전체 |  주

* **백업 기본 설정: 보조**

**백업 유형** | **노드**
--- | ---
전체 | 보조
차등 | 보조
로그 |  주
복사 전용 전체 |  주

* **백업 기본 설정 없음**

**백업 유형** | **노드**
--- | ---
전체 | 보조
차등 | 보조
로그 |  주
복사 전용 전체 |  주

## <a name="set-vm-permissions"></a>VM 권한 설정

  SQL Server에서 검색을 실행하면 Azure Backup은 다음을 수행합니다.

* AzureBackupWindowsWorkload 확장을 추가합니다.
* 가상 머신에서 에서 데이터베이스를 검색하기 위해 NT SERVICE\AzureWLBackupPluginSvc 계정을 만듭니다. 이 계정은 백업 및 복원에 사용되며 SQL sysadmin 권한이 필요합니다.
* VM에서 실행 중인 데이터베이스를 검색하고 Azure Backup은 NT AUTHORITY\SYSTEM 계정을 사용합니다. 이 계정은 SQL에서 공용 로그인이어야 합니다.

Azure Marketplace에서 SQL Server VM을 만들지 않았거나 SQL 2008 및 2008 R2를 사용 중인 경우, **UserErrorSQLNoSysadminMembership** 오류가 발생할 수 있습니다.

Windows 2008 R2에서 실행되는 **SQL 2008** 및 **2008 R2**의 경우 권한을 부여하려면 [여기](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)를 참조하세요.

다른 모든 버전의 경우 다음 단계에 따라 권한을 수정합니다.

  1. SQL Server sysadmin 권한이 있는 계정을 사용하여 SSMS(SQL Server Management Studio)에 로그인합니다. 특별한 사용 권한이 필요하지 않으면 Windows 인증이 작동해야 합니다.
  2. SQL Server에서 **Security/Logins** 폴더를 엽니다.

      ![Security/Logins 폴더를 열어서 계정 보기](./media/backup-azure-sql-database/security-login-list.png)

  3. **Logins** 폴더를 마우스 오른쪽 단추로 클릭하고 **새 로그인**을 선택합니다. **로그인 - 새로 만들기**에서 **검색**을 선택합니다.

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
9. 사용 권한을 부여 하 고 나면 포털에서 **db** 를 다시 검색: 자격 증명 모음 **->** 백업 인프라 **->** Azure VM에서 작업을 수행 합니다.

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
