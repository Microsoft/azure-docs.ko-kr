---
title: FAQ-Azure Vm에서 SQL Server 데이터베이스 백업
description: Azure Backup를 사용 하 여 Azure Vm에 SQL Server 데이터베이스를 백업 하는 방법에 대 한 일반적인 질문과 대답을 찾습니다.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: c1efbc44ae1e4bea96c063e4dc734f1cc53a42ac
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180102"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Azure VM 백업에서 실행 되는 SQL Server 데이터베이스에 대 한 FAQ

이 문서에서는 Azure Vm (가상 머신)에서 실행 되 고 [Azure Backup](backup-overview.md) 서비스를 사용 하는 SQL Server 데이터베이스 백업에 대 한 일반적인 질문에 답변 합니다.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>동일한 컴퓨터에 SQL Server 뿐만 아니라 IaaS VM에 대 한 Azure Backup를 사용할 수 있나요?

예, 동일한 VM에서 VM 백업과 SQL 백업을 모두 사용할 수 있습니다. 이 경우 내부적으로 VM에서 복사 전용 전체 백업을 트리거하여 로그를 자르지 않습니다.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>솔루션을 다시 시도 하거나 백업을 자동으로 치료 합니까?

경우에 따라 Azure Backup 서비스는 수정 백업을 트리거합니다. 자동 치료는 아래에 설명 된 6 가지 조건에 대해 발생할 수 있습니다.

- LSN 유효성 검사 오류로 인해 로그 또는 차등 백업이 실패 하는 경우 다음 로그 또는 차등 백업이 대신 전체 백업으로 변환 됩니다.
- 로그 또는 차등 백업 전에 전체 백업이 발생 하지 않은 경우에는 해당 로그 또는 차등 백업이 전체 백업으로 변환 됩니다.
- 최신 전체 백업의 지정 시간이 15 일 보다 오래 된 경우 다음 로그 또는 차등 백업이 대신 전체 백업으로 변환 됩니다.
- 확장 업그레이드로 인해 취소 되는 모든 백업 작업은 업그레이드가 완료 되 고 확장이 시작 된 후에 다시 트리거됩니다.
- 복원 중에 데이터베이스를 덮어쓰도록 선택한 경우 다음 로그/차등 백업이 실패 하 고 대신 전체 백업이 트리거됩니다.
- 데이터베이스 복구 모델의 변경으로 인해 로그 체인을 다시 설정 하기 위해 전체 백업을 수행 해야 하는 경우에는 다음 일정에 따라 전체가 자동으로 트리거됩니다.

기본적으로 모든 사용자에 게 기능이 설정 되어 있으므로 자동으로 치료 됩니다. 그러나 옵트아웃 (opt out) 하도록 선택 하는 경우 다음 단계를 수행 합니다.

- SQL Server 인스턴스의 *C:\Program Files\Azure 워크 로드 Backup\bin* 폴더에서 파일 ** 의ExtensionSettingsOverrides.js** 를 만들거나 편집 합니다.
- **ExtensionSettingsOverrides.js**에서 *{"EnableAutoHealer": false}* 를 설정 합니다.
- 변경 내용을 저장하고 파일을 닫습니다.
- SQL Server 인스턴스에서 **작업 관리** 를 열고 **AzureWLBackupCoordinatorSvc** 서비스를 다시 시작 합니다.

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>SQL server에서 실행 되는 동시 백업 수를 제어할 수 있나요?

예. SQL Server 인스턴스에 대한 영향을 최소화하기 위해 백업 정책이 실행되는 속도를 제한할 수 있습니다. 설정을 변경하려면:

1. SQL Server 인스턴스의 *C:\Program Files\Azure 워크 로드 Backup\bin* 폴더에서 파일 * 에ExtensionSettingsOverrides.js* 를 만듭니다.
2. 파일 * 의ExtensionSettingsOverrides.js* 에서 **Defaultbackuptasksthreshold** 설정을 더 낮은 값 (예: 5)으로 변경 합니다. <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
Defaultbackup업무 임계값의 기본값은 **20**입니다.

3. 변경 내용을 저장하고 파일을 닫습니다.
4. SQL Server 인스턴스에서 **작업 관리자**를 엽니다. **AzureWLBackupCoordinatorSvc** 서비스를 다시 시작합니다.<br/> <br/>
 이 방법을 사용 하면 백업 응용 프로그램이 많은 양의 리소스를 사용 하는 경우에는 SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 는 들어오는 응용 프로그램 요청이 사용할 수 있는 CPU, 물리적 IO 및 메모리 양에 대 한 제한을 지정 하는 보다 일반적인 방법입니다.

> [!NOTE]
> UX에서 계속 진행 하 여 언제 든 지 많은 백업을 예약할 수 있습니다. 그러나 위의 예제에 따라 5 라는 슬라이딩 윈도우에서 처리 됩니다.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>보조 복제본에서 전체 백업을 실행할 수 있나요?

SQL 제한 사항에 따라 보조 복제본에서 전체 백업만 복사를 실행할 수 있습니다. 그러나 전체 백업은 허용 되지 않습니다.

## <a name="can-i-protect-availability-groups-on-premises"></a>온-프레미스 가용성 그룹을 보호할 수 있나요?

아니요. Azure Backup는 Azure에서 실행 되는 SQL Server 데이터베이스를 보호 합니다. Azure와 온-프레미스 컴퓨터 간에 AG (가용성 그룹)를 분산 하는 경우 주 복제본이 Azure에서 실행 되는 경우에만 AG를 보호할 수 있습니다. 또한 Azure Backup는 Recovery Services 자격 증명 모음과 동일한 Azure 지역에서 실행 되는 노드만 보호 합니다.

## <a name="can-i-protect-availability-groups-across-regions"></a>Azure 지역 간에 가용성 그룹을 보호할 수 있나요?

Azure Backup Recovery Services 자격 증명 모음은 자격 증명 모음과 동일한 지역에 있는 모든 노드를 검색 하 고 보호할 수 있습니다. SQL Server Always On 가용성 그룹이 여러 Azure 지역에 걸쳐 있는 경우 주 노드가 있는 지역에서 백업을 설정 합니다. 그러면 Azure Backup이 백업 기본 설정에 따라 가용성 그룹의 모든 데이터베이스를 검색하고 보호할 수 있습니다. 백업 기본 설정이 충족 되지 않으면 백업이 실패 하 고 오류 경고가 발생 합니다.

## <a name="do-successful-backup-jobs-create-alerts"></a>성공한 백업 작업에 경고가 만들어지나요?

아니요. 성공한 백업 작업에서는 경고가 생성되지 않습니다. 경고는 실패한 백업 작업에 대해서만 전송됩니다. 포털 경고의 상세한 동작은 [여기](backup-azure-monitoring-built-in-monitor.md)에서 확인하세요. 그러나 성공한 작업에 대 한 경고를 포함 하는 경우에는 [Azure Monitor를 사용 하 여 모니터링](backup-azure-monitoring-use-azuremonitor.md)을 사용할 수 있습니다.

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>백업 작업 메뉴에서 예약된 백업 작업을 볼 수 있나요?

**백업 작업** 메뉴에는 주문형 백업 작업만 표시 됩니다. 예약 된 작업의 경우 [Azure Monitor를 사용 하 여 모니터링](backup-azure-monitoring-use-azuremonitor.md)을 사용 합니다.

## <a name="are-future-databases-automatically-added-for-backup"></a>이후 데이터베이스가 백업을 위해 자동으로 추가되나요?

예, [자동 보호](backup-sql-server-database-azure-vms.md#enable-auto-protection)를 사용 하 여이 기능을 달성할 수 있습니다.  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Autoprotected 인스턴스에서 데이터베이스를 삭제 하는 경우 백업에 어떤 일이 발생 하나요?

Autoprotected 인스턴스에서 데이터베이스를 삭제 하면 데이터베이스 백업이 계속 시도 됩니다. 즉, 삭제된 데이터베이스가 **백업 항목**에서 비정상 상태로 표시되기 시작하지만 여전히 보호되는 것으로 처리됩니다.

이 데이터베이스의 보호를 중지 하는 올바른 방법은이 데이터베이스에서 **데이터를 삭제** 하는 **백업 중지** 를 수행 하는 것입니다.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Autoprotected 데이터베이스의 백업 작업을 중지 하는 경우 해당 동작이 어떻게 되나요?

**데이터 보관을 사용 하 여 백업을 중지**하면 이후 백업이 수행 되지 않으며 기존 복구 지점은 그대로 유지 됩니다. 데이터베이스는 여전히 보호 된 것으로 간주 되며 **백업 항목**아래에 표시 됩니다.

**데이터 삭제를 사용 하 여 백업을 중지**하는 경우 이후 백업이 수행 되지 않으며 기존 복구 지점도 삭제 됩니다. 데이터베이스는 보호 되지 않는 것으로 간주 되 고 구성 백업에서 인스턴스에 표시 됩니다. 그러나 수동으로 선택 하거나 autoprotected를 받을 수 있는 다른 보호 된 데이터베이스와 달리이 데이터베이스는 회색으로 표시 되며 선택할 수 없습니다. 이 데이터베이스를 다시 보호 하는 유일한 방법은 인스턴스에서 자동 보호를 사용 하지 않도록 설정 하는 것입니다. 이제이 데이터베이스를 선택 하 고 보호를 구성 하거나 인스턴스에서 자동 보호를 다시 사용 하도록 설정할 수 있습니다.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>보호 된 데이터베이스의 이름을 변경 하는 경우 동작이 어떻게 되나요?

이름이 변경된 데이터베이스는 새 데이터베이스로 처리됩니다. 따라서 서비스는 데이터베이스를 찾을 수 없고가 백업에 실패 한 것 처럼이 상황을 처리 합니다.

이제 데이터베이스를 선택 하 여 해당 데이터베이스에 대 한 보호를 구성할 수 있습니다. 인스턴스에서 자동 보호를 사용 하도록 설정 하면 이름이 바뀐 데이터베이스가 자동으로 검색 되 고 보호 됩니다.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Autoprotected 인스턴스에 대해 추가 된 데이터베이스가 표시 되지 않는 이유는 무엇 인가요?

[Autoprotected 인스턴스에 추가](backup-sql-server-database-azure-vms.md#enable-auto-protection) 하는 데이터베이스는 보호 된 항목 아래에 즉시 표시 되지 않을 수 있습니다. 일반적으로 검색이 8시간마다 실행되기 때문입니다. 그러나 다음 그림에 표시 된 것 처럼 **db**다시 검색을 선택 하 여 수동으로 검색을 실행 하는 경우 새 데이터베이스를 즉시 검색 하 고 보호할 수 있습니다.

  ![새로 추가 된 데이터베이스를 수동으로 검색](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>다음 단계

Azure VM에서 실행 되는 [SQL Server 데이터베이스를 백업](backup-azure-sql-database.md) 하는 방법에 대해 알아봅니다.
