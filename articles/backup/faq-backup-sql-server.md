---
title: Azure Backup을 사용하여 Azure VM의 SQL Server 데이터베이스를 백업하는 방법에 대한 자주 묻는 질문
description: Azure Backup을 사용 하 여 Azure Vm에서 SQL Server 데이터베이스를 백업 하는 방법에 대 한 일반적인 질문과 대답을을 찾습니다.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 649e50634d901ab48f1cb36c39d7331401c0cc51
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62733551"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Azure VM 백업에서 실행 되는 SQL Server 데이터베이스에 대 한 FAQ

이 문서에서는 Azure virtual machines (Vm)에서 실행 되는 및 사용 하는 SQL Server 데이터베이스 백업에 대 한 일반적인 질문에 답변 합니다 [Azure Backup](backup-overview.md) 서비스입니다.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>사용할 수 있습니까 Azure backup IaaS VM 뿐만 아니라 SQL Server에 대 한 동일한 컴퓨터에서?
예, 할 수 있습니다 VM 백업 및 SQL 백업 모두 동일한 VM에서. 이 예에서는 내부적으로 트리거할 복사 전용 전체 백업을 vm 로그를 자르지 않습니다.


## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>솔루션을 다시 시도 또는 자동 치료 백업을?

상황에 따라 Azure Backup 서비스에는 수정 백업을 트리거합니다. 자동 복구를 아래 언급 된 6 가지 조건 중 하나가 발생할 수 있습니다.

  - 로그 백업이 나 차등 백업의 LSN 유효성 검사 오류로 인해 실패 하는 경우 다음 로그 백업이 나 차등 백업의 전체 백업으로 대신 변환 됩니다.
  - 로그 또는 차등 백업 하기 전에 전체 백업이 없는 문제가 발생 한 경우 해당 로그 또는 차등 백업이 전체 백업으로 대신 변환 됩니다.
  - 최신 전체 백업 시간에 15 일 보다 오래 된 경우 다음 로그 또는 차등 백업이 전체 백업으로 대신 변환 됩니다.
  - 업그레이드가 완료 된 후 확장 시작 되는 확장 업그레이드로 인해 취소 하는 모든 백업 작업이 다시 트리거됩니다.
  - 데이터베이스 복원 중 덮어쓰기 하려는 경우 다음 로그/차등 백업이 실패 하 고 전체 백업을 대신 트리거됩니다.
  - 전체 백업 데이터베이스 복구 모델에서 변경으로 인해 로그 체인을 다시 설정 하는 데 필요한 경우에는 다음 일정에 따라 자동으로 전체 가져옵니다 트리거됩니다.

자동 복구 기능을를 사용 하도록 설정한 모든 사용자에 대 한 기본값은 그러나 옵트아웃을 선택할 경우에 다음을 수행 합니다 아래:

  * SQL Server 인스턴스에서는 *C:\Program Files\Azure 워크 로드 Backup\bin* 폴더를 만들거나 편집 합니다 **ExtensionSettingsOverrides.json** 파일.
  * 에 **ExtensionSettingsOverrides.json**설정 *{"EnableAutoHealer": false}* 합니다.
  * 변경 내용을 저장하고 파일을 닫습니다.
  * SQL Server 인스턴스를 엽니다 **작업 관리** 한 다음 다시 시작 합니다 **AzureWLBackupCoordinatorSvc** 서비스.  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>얼마나 많은 동시 백업이 SQL server에서 실행 하는 것에 대 한 제어할 수 있나요?

예. SQL Server 인스턴스에 대한 영향을 최소화하기 위해 백업 정책이 실행되는 속도를 제한할 수 있습니다. 설정을 변경하려면:
1. SQL Server 인스턴스에서는 *C:\Program Files\Azure 워크 로드 Backup\bin* 폴더를 만들기는 *ExtensionSettingsOverrides.json* 파일입니다.
2. 에 *ExtensionSettingsOverrides.json* 파일에서 변경 합니다 **DefaultBackupTasksThreshold** 낮은 값 (예를 들어, 5)으로 설정 합니다. <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. 변경 내용을 저장하고 파일을 닫습니다.
4. SQL Server 인스턴스에서 **작업 관리자**를 엽니다. **AzureWLBackupCoordinatorSvc** 서비스를 다시 시작합니다.<br/> <br/>
 백업 응용 프로그램 리소스, SQL Server를 많이 사용 하는 경우이 방법을 사용 하는 동안 [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) CPU, 물리적 IO 및 들어오는 응용 프로그램 요청 수 있는 메모리의 양에 제한을 지정 하는 보다 일반적인 방법 이 옵션을 사용 합니다.

> [!NOTE]
> UX 여전히 진행를 특정 시점 백업을 예약에 위의 예제에 따라 5, 슬라이딩 윈도우에서 처리 됩니다 있지만.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>보조 복제본에서 전체 백업을 실행할 수 있나요?
SQL 제한 사항에 따라 복사 전체 백업을 보조 복제본에서 실행할 수 있습니다. 그러나 전체 백업을 허용 되지 않습니다.

## <a name="can-i-protect-availability-groups-on-premises"></a>가용성 그룹 온-프레미스를 보호할 수 있습니까?
아니요. Azure Backup은 Azure에서 실행 되는 SQL Server 데이터베이스를 보호 합니다. Azure 및 온-프레미스 컴퓨터 간에 분산 되는 가용성 그룹 (AG), 주 복제본은 Azure에서 실행 중인 경우에 AG은 보호할 수 있습니다. 또한 Azure Backup Recovery Services 자격 증명 모음과 동일한 Azure 지역에서 실행 되는 노드만 보호 합니다.

## <a name="can-i-protect-availability-groups-across-regions"></a>지역에서 가용성 그룹을 보호할 수 있습니까?
Azure Backup Recovery Services 자격 증명 모음 검색 하 고 자격 증명 모음과 동일한 지역에 있는 모든 노드를 보호할 수 있습니다. SQL Server Always On 가용성 그룹에 여러 Azure 지역에 걸쳐 있는 경우 주 노드에 있는 지역에서 백업 될 때까지 설정 합니다. 그러면 Azure Backup이 백업 기본 설정에 따라 가용성 그룹의 모든 데이터베이스를 검색하고 보호할 수 있습니다. 백업 기본 설정을 충족 되지 않으면 백업이 실패 하 고 실패 경고를 받을 수 있습니다.

## <a name="do-successful-backup-jobs-create-alerts"></a>성공한 백업 작업에 경고가 만들어지나요?
아니요. 성공한 백업 작업에서는 경고가 생성되지 않습니다. 경고는 실패한 백업 작업에 대해서만 전송됩니다. 포털 경고에 대 한 자세한 동작 된다 [여기](backup-azure-monitoring-built-in-monitor.md)합니다. 그러나 원하는 경우 권한이 경고 성공한 작업에 대해서도 사용할 수 있습니다 [Azure Monitor를 사용 하 여 모니터링](backup-azure-monitoring-use-azuremonitor.md)합니다.

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>백업 작업 메뉴에서 예약 된 백업 작업을 볼 수 있나요?
합니다 **백업 작업** 메뉴 애드혹 백업 작업 표시만 됩니다. 예약 된 작업에 대 한 사용 [Azure Monitor를 사용 하 여 모니터링](backup-azure-monitoring-use-azuremonitor.md)합니다.

## <a name="are-future-databases-automatically-added-for-backup"></a>이후 데이터베이스가 백업을 위해 자동으로 추가되나요?
예,이 기능을 얻을 수 있습니다 [자동 보호](backup-sql-server-database-azure-vms.md#enable-auto-protection)합니다.  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Autoprotected 인스턴스에서 데이터베이스를 삭제할 경우 백업은 어떻게 되나요?
데이터베이스 autoprotected 인스턴스에서 삭제 되 면 데이터베이스 백업은 계속 시도 됩니다. 이 삭제 된 데이터베이스에서 비정상으로 표시를 시작 하는 것을 의미 **Backup 항목** 되 고 계속 보호 됩니다.

작업을 수행 하는이 데이터베이스의 보호를 중지 하는 올바른 방법을 **Backup 중지** 사용 하 여 **데이터를 삭제할** 이 데이터베이스에서.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>해당 동작은 어떤 것 autoprotected 데이터베이스의 백업 작업 중지 수행 하나요?
만약 **로 백업 중지 데이터 보존**이후 백업이 수행 되 고 기존 복구 지점을 그대로 유지 됩니다. 데이터베이스는 여전히 간주 보호 되며 아래에 표시 된 **Backup 항목**합니다.

만약 **데이터 삭제로 백업 중지**이후 백업이 수행 되 고 기존 복구 지점이 삭제 됩니다. 데이터베이스 것으로 간주 되지 않은 보호 된 백업 구성에서 인스턴스 아래에 표시 하 고 합니다. 그러나 데이터베이스와 달리 다른 최대 보호 수동으로 선택할 수 있는 또는 autoprotected를 가져올 수 있는,이 데이터베이스 회색 표시 및 선택할 수 없습니다. 이 데이터베이스를 다시 보호 하는 유일한 방법은 인스턴스의 자동 보호를 사용 하지 않도록 설정 됩니다. 이제이 데이터베이스를 선택 및 구성 보호를 하거나 다시 인스턴스의 자동 보호를 다시 설정 합니다.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>보호 된 후 데이터베이스의 이름을 변경 하려면 동작 됩니다 무엇입니까?
다시 명명된 된 데이터베이스를 새 데이터베이스로 처리 됩니다. 따라서 서비스는 데이터베이스를 찾을 수 없습니다 및 사용 하 여 백업 실패 처럼이 이런을 처리 합니다.

이제 이름이 바뀌고에 보호를 구성 하는 데이터베이스를 선택할 수 있습니다. 인스턴스의 자동 보호는 사용 하도록 설정 하는 경우 이름이 바뀐된 데이터베이스 자동으로 검색 되며 보호 합니다.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Autoprotected 인스턴스에 대 한 추가 된 데이터베이스는 표시 되지 않는 이유
데이터베이스 [autoprotected 인스턴스에 추가](backup-sql-server-database-azure-vms.md#enable-auto-protection) 보호 된 항목 아래에서 즉시 나타나지 않을 수 있습니다. 일반적으로 검색이 8시간마다 실행되기 때문입니다. 검색 하 고 선택 하 여 검색을 수동으로 실행 하는 경우 새 데이터베이스를 즉시 보호할 수 있지만 **Db 복구**다음 그림에 나와 있는 것 처럼 합니다.

  ![새로 추가 된 데이터베이스를 수동으로 검색합니다](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>다음 단계

에 대해 알아봅니다 하는 방법 [SQL Server 데이터베이스 백업](backup-azure-sql-database.md) Azure VM에서 실행 중인 합니다.
