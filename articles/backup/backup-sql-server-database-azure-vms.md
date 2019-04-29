---
title: Azure에 SQL Server 데이터베이스 백업 | Microsoft Docs
description: 이 자습서에서는 Azure에 SQL Server를 백업하는 방법을 설명합니다.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 08eff24dc42f594424d109b82933b01b5c1be454
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62733903"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Azure VM의 SQL Server 데이터베이스 백업

SQL Server 데이터베이스는 낮은 RPO(복구 지점 목표)와 장기 보존이 필요한 중요한 워크로드입니다. [Azure Backup](backup-overview.md)을 사용하여 Azure VM에서 실행되는 SQL Server 데이터베이스를 백업할 수 있습니다.

이 문서에서는 Azure VM에서 실행되는 SQL Server 데이터베이스를 Azure Backup Recovery Services 자격 증명 모음에 백업하는 방법을 보여 줍니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 자격 증명 모음을 만들고 구성합니다.
> * 데이터베이스를 검색하고 백업을 설정합니다.
> * 데이터베이스에 대한 자동 보호를 설정합니다.


## <a name="prerequisites"></a>필수 조건

SQL Server 데이터베이스를 백업하기 전에 다음 조건을 확인하십시오.

1. 식별 하거나 [만들](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) 동일한 지역 또는 SQL Server 인스턴스를 호스팅하는 VM으로 로캘 Recovery Services 자격 증명 모음입니다.
2. SQL 데이터베이스를 백업하는 데 필요한 [VM 권한을 확인](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)합니다.
3. VM에 [네트워크 연결](backup-sql-server-database-azure-vms.md#establish-network-connectivity)이 있는지 확인합니다.
4. SQL Server 데이터베이스의 이름이 Azure Backup [명명 지침](#verify-database-naming-guidelines-for-azure-backup)에 따라 지정되었는지 확인합니다.
5. 데이터베이스에 사용할 수 있는 다른 백업 솔루션이 없는지 확인합니다. 이 시나리오를 설정하기 전에 다른 모든 SQL Server 백업을 사용하지 않도록 설정합니다. 충돌 없이 VM에서 실행되는 SQL Server 데이터베이스에 대한 Azure Backup과 함께 Azure VM에 대한 Azure Backup을 사용하도록 설정할 수 있습니다.


### <a name="establish-network-connectivity"></a>네트워크 연결 설정

모든 작업에서 SQL Server VM 가상 머신에는 Azure 공용 IP 주소에 대한 연결이 필요합니다. 공용 IP 주소에 연결할 수 없는 VM 작업 (데이터베이스 검색 백업을 구성, 백업 일정, 복구 지점 복원 및 등)이 실패 합니다. 다음 옵션 중 하나를 사용하여 연결을 설정합니다.

- **Azure 데이터 센터 IP 범위 허용**: 다운로드에서 [IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 허용합니다. 네트워크 보안 그룹 (NSG)에 액세스 하려면 사용 합니다 **Set-azurenetworksecurityrule** cmdlet.
- **트래픽을 라우팅하는 HTTP 프록시 서버 배포**: Azure VM에서 SQL Server 데이터베이스를 백업하는 경우 VM의 백업 확장에서 HTTPS API를 사용하여 관리 명령을 Azure Backup에 보내고 데이터를 Azure Storage에 보냅니다. 백업 확장은 인증에 Azure AD(Azure Active Directory)도 사용합니다. HTTP 프록시를 통해 이 세 가지 서비스에 대한 백업 확장 트래픽을 라우팅합니다. 확장의 경우 공용 인터넷에 액세스 하도록 구성 된 유일한 구성 요소

각 옵션에 장점 및 단점

**옵션** | **장점** | **단점**
--- | --- | ---
IP 범위 허용 | 추가 비용 없음 | 시간이 지남에 따라 IP 주소 범위가 변경되므로 관리가 복잡합니다. <br/><br/> Azure Storage 뿐만 아니라 Azure 전체에 대한 액세스를 제공합니다.
HTTP 프록시 사용   | 저장소 URL에 대한 프록시의 세부적인 제어가 허용됩니다. <br/><br/> VM에 대한 인터넷 액세스의 단일 지점 <br/><br/> Azure IP 주소 변경이 적용되지 않음 | 프록시 소프트웨어로 VM을 실행하기 위한 추가 비용입니다.

### <a name="set-vm-permissions"></a>VM 권한 설정

Azure Backup은 SQL Server 데이터베이스에 대한 백업을 구성할 때 다음과 같은 몇 가지 작업을 수행합니다.

- **AzureBackupWindowsWorkload** 확장을 추가합니다.
- 가상 머신에서 데이터베이스를 검색하기 위해 Azure Backup은 **NT SERVICE\AzureWLBackupPluginSvc** 계정을 만듭니다. 이 계정은 백업 및 복원에 사용되며 SQL sysadmin 권한이 필요합니다.
- Azure Backup은 데이터베이스 검색/조회에서 **NT AUTHORITY\SYSTEM** 계정을 활용하므로 이 계정은 SQL에서 공개적으로 로그인해야 합니다.

Azure Marketplace에서 SQL Server VM을 만들지 않은 경우 **UserErrorSQLNoSysadminMembership** 오류가 발생할 수 있습니다. 이 문제가 발생 하면 [이 지침에 따라](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)합니다.

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Azure Backup의 데이터베이스 명명 지침 확인

방지는 아래 데이터베이스 이름에 대 한 합니다.

  * 선행/후행 공백
  * 후행 ‘!’
  * 닫는 대괄호 ‘]’
  * 'F:\'로 시작 하는 데이터베이스 이름

Azure 테이블에 지원되지 않는 문자에 대해 앨리어싱을 사용하지만 이러한 문자는 사용하지 않는 것이 좋습니다. [자세히 알아보기](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server 데이터베이스 검색

VM에서 실행되는 데이터베이스를 검색합니다.

1. [Azure Portal](https://portal.azure.com)에서 데이터베이스를 백업하는 데 사용하는 Recovery Services 자격 증명 모음을 엽니다.

2. **Recovery Services 자격 증명 모음** 대시보드에서 **Backup**을 선택합니다.

   ![Backup을 선택하여 백업 목표 메뉴 열기](./media/backup-azure-sql-database/open-backup-menu.png)

3. **백업 목표**에서 **작업이 실행되는 위치**를 **Azure**(기본값)로 설정합니다.

4. **백업할 항목**에서 **Azure VM의 SQL Server**를 선택합니다.

    ![백업을 위해 Azure VM에서 SQL Server 선택](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. **백업 목표** > **VM에서 DB 검색** 에서 **검색 시작**을 선택하여 구독에서 보호되지 않은 가상 머신을 검색합니다. 구독에서 보호되지 않은 가상 머신의 수에 따라 약간의 시간이 걸릴 수 있습니다.

   - 검색 후에는 보호되지 않은 VM이 이름 및 리소스 그룹별로 나열된 목록에 표시됩니다.
   - VM이 예상대로 나열되지 않으면 자격 증명 모음에 이미 백업되어 있는지 확인합니다.
   - 여러 VM은 동일한 이름을 사용할 수 있지만 서로 다른 리소스 그룹에 속합니다.

     ![VM의 DB 검색 중에는 백업이 보류됩니다.](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. VM 목록에서 SQL Server 데이터베이스가 실행되는 VM, **DB 검색**을 차례로 선택합니다.

7. **알림** 영역에서 데이터베이스 검색을 추적합니다. VM의 데이터베이스 수에 따라 작업이 완료되는 데 약간의 시간이 걸릴 수 있습니다. 선택한 데이터베이스가 검색되면 성공 메시지가 표시됩니다.

    ![배포 성공 메시지](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup은 VM의 모든 SQL Server 데이터베이스를 검색합니다. 검색 하는 동안는 아래 백그라운드에서 발생 합니다.

    - Azure Backup에서 워크로드 백업용 자격 증명 모음에 VM을 등록합니다. 등록된 VM의 모든 데이터베이스는 이 자격 증명 모음에만 백업할 수 있습니다.
    - Azure Backup에서 **AzureBackupWindowsWorkload** 확장을 VM에 설치합니다. 에이전트는 SQL 데이터베이스에 설치되지 않습니다.
    - Azure Backup에서 **NT Service\AzureWLBackupPluginSvc** 서비스 계정을 VM에 만듭니다.
      - 모든 백업 및 복원 작업에는 서비스 계정이 사용됩니다.
      - **NT Service\AzureWLBackupPluginSvc**에는 SQL sysadmin 권한이 필요합니다. Azure Marketplace에서 만든 모든 SQL Server VM에는 **SqlIaaSExtension**이 설치되어 있습니다. **AzureBackupWindowsWorkload** 확장은 **SQLIaaSExtension**을 사용하여 필요한 권한을 자동으로 확보합니다.
    - 마켓플레이스에서 VM을 만들지 않은 경우 VM에 **SqlIaaSExtension**이 설치되어 있지 않고, **UserErrorSQLNoSysAdminMembership** 오류 메시지와 함께 검색 작업이 실패합니다. 수행 합니다 [지침](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) 이 문제를 해결 하려면.

        ![VM 및 데이터베이스 선택](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>백업 구성  

다음과 같이 백업을 구성합니다.

1. **백업 목표**에서 **백업 구성**을 선택합니다.

   ![백업 구성 선택](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. 클릭 **백업 구성**서 **백업할 항목 선택** 블레이드가 표시 됩니다. 모든 등록 된 가용성 그룹 및 독립 실행형 SQL Server를 나열합니다. 해당 인스턴스 또는 Always on AG의 모든 보호 되지 않는 데이터베이스를 보려면 행의 왼쪽에 있는 펼침 단추를 확장 합니다.  

    ![독립 실행형 데이터베이스를 사용하는 모든 SQL Server 인스턴스 표시](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 보호하려는 모든 데이터베이스, **확인**을 차례로 선택합니다.

   ![데이터베이스 보호](./media/backup-azure-sql-database/select-database-to-protect.png)

   백업 로드를 최적화하기 위해 Azure Backup은 한 백업 작업의 최대 데이터베이스 수를 50개로 설정합니다.

     * 50개가 넘는 데이터베이스를 보호하려면 여러 백업을 구성합니다.
     * 또는 **AUTOPROTECT** 열의 해당 드롭다운에서 **ON** 옵션을 선택하여 전체 인스턴스 또는 Always On 가용성 그룹에 대한 [자동 보호](#enable-auto-protection)를 설정할 수 있습니다. [자동 보호](#enable-auto-protection) 기능은 모든 기존 데이터베이스에 보호를 사용하도록 설정할 뿐 아니라 나중에 해당 인스턴스 또는 가용성 그룹에 추가되는 새 데이터베이스도 자동으로 보호합니다.  

4. 클릭 **확인** 열려는 합니다 **Backup 정책** 블레이드입니다.

    ![Always On 가용성 그룹에 자동 보호를 사용하도록 설정](./media/backup-azure-sql-database/enable-auto-protection.png)

5.  **백업 정책 선택**정책을 선택한 클릭 **확인**합니다.

   - 기본 정책 HourlyLogBackup.
   - 이전에 SQL용으로 만든 기존 백업 정책을 선택합니다.
   - RPO 및 보존 범위에 따라 새 정책을 정의 합니다.

     ![백업 정책 선택](./media/backup-azure-sql-database/select-backup-policy.png)

6. 온 **백업** 메뉴에서 **백업 사용**합니다.

    ![선택한 백업 정책 사용](./media/backup-azure-sql-database/enable-backup-button.png)

7. 구성 진행률을 추적 합니다 **알림을** 포털의 영역입니다.

    ![알림 영역](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 백업이 수행되는 시기와 유지되는 기간을 정의합니다.

- 정책은 자격 증명 모음 수준에서 만들어집니다.
- 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다.
- 백업 정책을 만드는 경우 매일 전체 백업이 기본값입니다.
- 차등 백업을 추가할 수 있지만 전체 백업이 매주 발생하도록 구성하는 경우에만 가능합니다.
- 다양한 유형의 백업 정책에 대해 [알아봅니다](backup-architecture.md#sql-server-backup-types).

백업 정책을 만들려면:

1. 자격 증명 모음에서 **백업 정책** > **추가**를 차례로 클릭합니다.
2. **추가** 메뉴에서 **Azure VM의 SQL Server**를 클릭합니다. 하려면 정책 유형을 정의합니다.

   ![새 백업 정책에 대한 정책 유형 선택](./media/backup-azure-sql-database/policy-type-details.png)

3. **정책 이름**에 새 정책의 이름을 입력합니다.
4. **전체 백업 정책**에서 **백업 빈도**를 선택하고, **매일** 또는 **매주**를 선택합니다.

   - **매일**의 경우 백업 작업이 시작될 때 시간과 표준 시간대를 선택합니다.
   - 해제할 수 없습니다으로 전체 백업을 실행 해야 합니다는 **전체 백업** 옵션입니다.
   - **전체 백업**을 클릭하여 정책을 확인합니다.
   - 매일 전체 백업에 대해서는 차등 백업을 만들 수 없습니다.
   - **매주**의 경우 백업 작업이 시작되는 요일, 시간 및 표준 시간대를 선택합니다.

     ![새 백업 정책 필드](./media/backup-azure-sql-database/full-backup-policy.png)  

5. **보존 범위**에는 모든 옵션이 기본적으로 선택되어 있습니다. 사용하지 않으려는 보존 범위 제한을 모두 선택 취소하고 사용 간격을 설정합니다.

    - 백업 (전체/차등/로그)의 모든 형식에 대 한 최소 보존 기간은 7 일입니다.
    - 복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다.
    - 주별 보존 범위 및 매주 보존 설정을 기반으로 특정 날짜에 대한 백업에 태그가 지정되어 유지됩니다.
    - 매월 및 매년 보존 범위도 유사한 방식으로 작동합니다.

   ![보존 범위 간격 설정](./media/backup-azure-sql-database/retention-range-interval.png)

6. **전체 백업 정책** 메뉴에서 **확인**을 클릭하여 설정을 적용합니다.
7. 차등 백업 정책을 추가하려면 **차등 백업**을 선택합니다.

   ![보존 범위 간격 설정](./media/backup-azure-sql-database/retention-range-interval.png)
   ![차등 백업 정책 메뉴 열기](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. **차등 백업 정책**에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다.

    - 많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다.
    - 차등 백업은 최대 180일 동안 보존될 수 있습니다. 더 오래 보존해야 하는 경우에는 전체 백업을 사용해야 합니다.

9. **확인**을 선택하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.

10. 트랜잭션 로그 백업 정책을 추가하려면 **로그 백업**을 선택합니다.
11. **로그 백업**에서 **사용**을 선택한 다음, 빈도 및 보존 컨트롤을 설정합니다. 로그 백업은 최소 15분 간격으로 수행할 수 있으며 최대 35일 동안 보존할 수 있습니다.
12. **확인**을 선택하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.

    ![로그 백업 정책 편집](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. **백업 정책** 메뉴에서 **SQL 백업 압축**을 사용할지 여부를 선택합니다.
    - 압축은 기본적으로 사용하지 않도록 설정되어 있습니다.
    - 백 엔드에서, Azure Backup은 SQL 네이티브 백업 압축을 사용합니다.

14. 백업 정책 편집을 완료 한 후, **확인**을 선택합니다.


### <a name="modify-policy"></a>정책 수정
백업 빈도 또는 보존 범위를 변경 하려면 정책을 수정 합니다.

> [!NOTE]
> 보존 기간에 변경 내용을 새로 외에도 모든 이전 복구 지점에 소급 적용 됩니다.

로 자격 증명 모음 대시보드에서 **관리** > **Backup 정책** 편집 하려는 정책을 선택 합니다.

  ![백업 정책 관리](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>자동 보호 사용  

모든 기존 데이터베이스 및 독립 실행형 SQL Server 인스턴스에서 호스팅되는지를 SQL Server Always on 가용성 그룹을 나중에 추가할 데이터베이스를 자동으로 백업할 자동 보호를 사용 하도록 설정 합니다.

- 데이터베이스를 한꺼번에 자동 보호를 위해 선택할 수 있습니다 수에 제한은 없습니다.
- 선택적으로 보호 하거나 데이터베이스 인스턴스 자동 보호를 사용 하도록 설정 시 보호에서 제외할 수 없습니다.
- 인스턴스는 이미 보호 된 데이터베이스 일부에 포함 된 경우 자동 보호를 설정한 후에 해당 해당 정책에서 보호할 계속 됩니다. 그러나 보호 되지 않은 모든 데이터베이스 및 데이터베이스를 나중에 추가 됩니다 갖습니다 시간에서 자동으로 보호를 사용 하도록 설정에서 정의 하는 하나의 정책만 **백업 구성**합니다. 그러나 나중에 자동으로 보호 되는 데이터베이스와 연결 된 정책을 변경할 수 있습니다.  

자동 보호를 사용 하도록 설정 하는 단계는 다음과 같습니다.

  1. **백업할 항목**에서 자동 보호를 사용하도록 설정하려는 인스턴스를 선택합니다.
  2. **자동 보호** 아래의 드롭다운을 선택하고 **On**으로 설정합니다. 그런 후 **OK**를 클릭합니다.

      ![Always On 가용성 그룹에 자동 보호를 사용하도록 설정](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. 백업은 모든 데이터베이스에 대해 함께 구성되며 **백업 작업**에서 추적할 수 있습니다.

자동 보호를 사용 하지 않도록 설정 해야 할 경우 아래에서 인스턴스 이름을 클릭 **Backup 구성**, 선택한 **자동 보호를 사용 하지 않도록 설정** 인스턴스에 대 한 합니다. 모든 데이터베이스 백업에 계속 있지만 이후의 데이터베이스를 자동으로 보호 되지 않습니다.

![해당 인스턴스에서 자동 보호를 사용하지 않도록 설정](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>다음 단계

- 백업된 SQL Server 데이터베이스를 복원하는 방법을 [알아봅니다](restore-sql-database-azure-vm.md).
- 백업된 SQL Server 데이터베이스를 관리하는 방법을 [알아봅니다](manage-monitor-sql-database-backup.md).
