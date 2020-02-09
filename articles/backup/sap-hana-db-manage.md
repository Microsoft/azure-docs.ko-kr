---
title: Azure Vm에서 백업 SAP HANA 데이터베이스 관리
description: 이 문서에서는 Azure virtual machines에서 실행 되는 SAP HANA 데이터베이스를 관리 및 모니터링 하는 일반적인 작업에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: a9462f8608fc5ae35255ac321a0742b3f1834fde
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390586"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>백업한 SAP HANA 데이터베이스 관리 및 모니터링

이 문서에서는 Azure VM (가상 컴퓨터)에서 실행 되 고 [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) 서비스에서 Azure Backup Recovery Services 자격 증명 모음에 백업 되는 SAP HANA 데이터베이스를 관리 하 고 모니터링 하는 일반적인 작업에 대해 설명 합니다. 작업 및 경고를 모니터링 하 고, 요청 시 백업을 트리거하고, 정책을 편집 하 고, 데이터베이스 보호를 중지 한 후 다시 시작 하 고, 백업에서 VM의 등록을 취소 하는 방법을 알아봅니다.

SAP HANA 데이터베이스에 대해 아직 백업을 구성 하지 않은 경우 [Azure vm에서 SAP HANA 데이터베이스 백업](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)을 참조 하세요.

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>포털에서 수동 백업 작업 모니터링

Azure Backup Azure Portal의 **Backup 작업** 섹션에서 수동으로 트리거된 모든 작업을 표시 합니다.

![Backup 작업 섹션](./media/sap-hana-db-manage/backup-jobs.png)

이 포털에 표시 되는 작업에는 데이터베이스 검색 및 등록, 백업 및 복원 작업이 포함 됩니다. 로그 백업을 포함 하 여 예약 된 작업은이 섹션에 표시 되지 않습니다. SAP HANA native client에서 수동으로 트리거된 백업 (스튜디오/환경/DBA 환경)도 여기에 표시 되지 않습니다.

![백업 작업 목록](./media/sap-hana-db-manage/backup-jobs-list.png)

모니터링에 대해 자세히 알아보려면 [Azure Portal의 모니터링](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) 및 [Azure Monitor를 사용 하 여 모니터링](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)을 참조 하세요.

## <a name="view-backup-alerts"></a>백업 경고 보기

경고는 SAP HANA 데이터베이스의 백업을 모니터링 하는 쉬운 방법입니다. 경고는 백업이 생성 하는 다 수의 이벤트를 손실 하지 않고 가장 중요 한 이벤트에 집중 하는 데 도움이 됩니다. Azure Backup를 사용 하 여 경고를 설정할 수 있으며, 다음과 같이 모니터링할 수 있습니다.

* [Azure Portal](https://portal.azure.com/)에 로그인합니다.
* 자격 증명 모음 대시보드에서 **Backup 경고**를 선택 합니다.

  ![자격 증명 모음 대시보드에 대한 백업 경고](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* 경고를 볼 수 있습니다.

  ![백업 경고 목록](./media/sap-hana-db-manage/backup-alerts-list.png)

* 자세한 내용을 보려면 경고를 클릭 합니다.

  ![경고 세부 정보](./media/sap-hana-db-manage/alert-details.png)

현재 Azure Backup에서는 전자 메일을 통해 경고를 보낼 수 있습니다. 이러한 경고는 다음과 같습니다.

* 모든 백업 실패에 대해 트리거됩니다.
* 오류 코드를 기준으로 데이터베이스 수준에서 통합 됩니다.
* 데이터베이스의 첫 번째 백업 실패에 대해서만 전송 됩니다.

모니터링에 대해 자세히 알아보려면 [Azure Portal의 모니터링](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) 및 [Azure Monitor를 사용 하 여 모니터링](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)을 참조 하세요.

## <a name="management-operations"></a>관리 작업

Azure Backup은 지원 되는 다양 한 관리 작업으로 백업 된 SAP HANA 데이터베이스를 쉽게 관리할 수 있도록 합니다. 이러한 작업에 대해서는 다음 섹션에서 자세히 설명 합니다.

### <a name="run-an-ad-hoc-backup"></a>임시 백업 실행

백업은 정책 일정에 따라 실행 됩니다. 다음과 같이 요청 시 백업을 실행할 수 있습니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **백업 항목**에서 SAP HANA 데이터베이스를 실행 하는 VM을 선택 하 고 **지금 Backup**을 클릭 합니다.
3. **지금 백업**에서 달력 컨트롤을 사용 하 여 복구 지점을 유지할 마지막 날을 선택 합니다. 그런 후 **OK**를 클릭합니다.
4. 포털 알림을 모니터링합니다. 자격 증명 모음 대시보드 > **백업 작업** > **진행 중**에서 작업 진행률을 모니터링할 수 있습니다. 데이터베이스의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Azure backup을 사용 하 여 데이터베이스에서 SAP HANA native client 백업 실행

Azure Backup를 사용 하 여 백업 되는 데이터베이스의 로컬 백업 (HANA Studio/환경 사용)을 수행 하려면 다음을 수행 합니다.

1. 데이터베이스에 대한 전체 또는 로그 백업이 완료 될 때까지 기다립니다. SAP HANA Studio/환경에서 상태를 확인 합니다.
2. 로그 백업을 사용 하지 않도록 설정 하 고, 관련 데이터베이스에 대한 백업 카탈로그를 파일 시스템으로 설정 합니다.
3. 이렇게 하려면 **systemdb** > **구성** >  > **데이터베이스 선택** **필터 (로그)** 를 차례로 선택 합니다.
4. **Enable_auto_log_backup** 를 **아니요**로 설정 합니다.
5. **Log_backup_using_backint** 을 **False**로 설정 합니다.
6. 데이터베이스의 주문형 전체 백업을 수행 합니다.
7. 전체 백업 및 카탈로그 백업이 완료 될 때까지 기다립니다.
8. 이전 설정을 Azure에 대한 설정을 다시 되돌립니다.
   * **Enable_auto_log_backup** 를 **예**로 설정 합니다.
   * **Log_backup_using_backint** 를 **True**로 설정 합니다.

### <a name="change-policy"></a>정책 변경

SAP HANA 백업 항목에 대한 기본 정책을 변경할 수 있습니다.

* 자격 증명 모음 대시보드에서 **백업 항목**으로 이동 합니다.

  ![백업 항목 선택](./media/sap-hana-db-manage/backup-items.png)

* **AZURE VM에서 SAP HANA** 선택

  ![Azure VM에서 SAP HANA 선택](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* 변경 하려는 기본 정책을 포함 하는 백업 항목을 선택 합니다.
* 기존 백업 정책을 클릭 합니다.

  ![기존 백업 정책 선택](./media/sap-hana-db-manage/existing-backup-policy.png)

* 목록에서 선택 하 여 정책을 변경 합니다. 필요한 경우 [새 백업 정책을 만듭니다](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy) .

  ![드롭다운 목록에서 정책을 선택 합니다.](./media/sap-hana-db-manage/choose-backup-policy.png)

* 변경 내용 저장

  ![변경 내용 저장](./media/sap-hana-db-manage/save-changes.png)

* 정책 수정은 관련 된 모든 백업 항목 및 트리거 **구성 보호** 작업에 영향을 줍니다.

>[!NOTE]
> 보존 기간을 변경 하면 새 복구 지점이 아닌 모든 이전 복구 소급 적용 됩니다.
>
> SAP HANA 데이터베이스에는 증분 백업 정책을 사용할 수 없습니다. 증분 백업은 현재 이러한 데이터베이스에 대해 지원 되지 않습니다.

### <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA 데이터베이스에 대한 보호 중지

몇 가지 방법으로 SAP HANA 데이터베이스 보호를 중지할 수 있습니다.

* 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제
* 이후의 모든 백업 작업을 중지 하 고 복구 지점은 그대로 둡니다.

복구 지점의 보존을 선택 하는 경우 다음 세부 정보를 염두에 두어야 합니다.

* 모든 복구 지점은 영구적으로 유지 되 고, 모든 정리는 데이터 보존을 사용 하 여 보호 중지에서 중지 됩니다.
* 보호 된 인스턴스와 사용 된 저장소에 대한 요금이 청구 됩니다. 자세한 내용은 [Azure Backup 가격 책정](https://azure.microsoft.com/pricing/details/backup/)을 참조 하세요.
* 백업을 중지 하지 않고 데이터 원본을 삭제 하는 경우 새 백업이 실패 합니다.

데이터베이스에 대한 보호를 중지하려면:

* 자격 증명 모음 대시보드에서 **백업 항목**을 선택 합니다.
* **Backup 관리 유형**에서 **Azure VM의 SAP HANA** 를 선택 합니다.

  ![Azure VM에서 SAP HANA 선택](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 보호를 중지할 데이터베이스를 선택 합니다.

  ![보호를 중지할 데이터베이스 선택](./media/sap-hana-db-manage/select-database.png)

* 데이터베이스 메뉴에서 **백업 중지**를 선택합니다.

  ![백업 중지를 선택 합니다.](./media/sap-hana-db-manage/stop-backup.png)

* **백업 중지** 메뉴에서 데이터를 보존할지 삭제할지를 선택 합니다. 원하는 경우 이유 및 설명을 제공 합니다.

  ![데이터 유지 또는 삭제를 선택 합니다.](./media/sap-hana-db-manage/retain-backup-data.png)

* **백업 중지**를 선택 합니다.

### <a name="resume-protection-for-an-sap-hana-database"></a>SAP HANA 데이터베이스에 대한 보호 다시 시작

SAP HANA 데이터베이스에 대한 보호를 중지 하는 경우 **백업 데이터 보존** 옵션을 선택 하면 나중에 보호를 다시 시작할 수 있습니다. 백업 된 데이터를 유지 하지 않는 경우에는 보호를 다시 시작할 수 없습니다.

SAP HANA 데이터베이스에 대한 보호를 다시 시작 하려면:

* 백업 항목을 열고 **백업 다시 시작**을 선택 합니다.

   ![백업 다시 시작을 선택 합니다.](./media/sap-hana-db-manage/resume-backup.png)

* **백업 정책** 메뉴에서 정책을 선택한 다음, **저장**을 클릭합니다.

### <a name="upgrading-from-sap-hana-10-to-20"></a>SAP HANA 1.0에서 2.0로 업그레이드

[SAP HANA 1.0에서 2.0로 업그레이드 한 후](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20)SAP HANA 데이터베이스에 대한 백업을 계속 하는 방법에 대해 알아봅니다.

### <a name="upgrading-without-a-sid-change"></a>SID 변경 없이 업그레이드

[업그레이드 후 SID가 변경 되지](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)않은 SAP HANA 데이터베이스의 백업을 계속 하는 방법에 대해 알아봅니다.

### <a name="unregister-an-sap-hana-database"></a>SAP HANA 데이터베이스 등록 취소

보호를 사용 하지 않도록 설정 하 고 자격 증명 모음을 삭제 하기 전에 SAP HANA 인스턴스를 등록 취소 합니다.

* 자격 증명 모음 대시보드의 **관리**에서 **백업 인프라**를 선택 합니다.

   ![백업 인프라 선택](./media/sap-hana-db-manage/backup-infrastructure.png)

* **AZURE VM에서** **백업 관리 유형을** 작업으로 선택

   ![Azure VM에서 백업 관리 유형을 작업으로 선택](./media/sap-hana-db-manage/backup-management-type.png)

* **보호 된 서버**에서 등록을 취소할 인스턴스를 선택 합니다. 자격 증명 모음을 삭제 하려면 모든 서버/인스턴스를 등록 취소 해야 합니다.

* 보호 된 인스턴스를 마우스 오른쪽 단추로 클릭 하 고 **등록 취소**를 선택 합니다.

   ![등록 취소 선택](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>다음 단계

* [SAP HANA 데이터베이스를 백업할 때 일반적인 문제를 해결](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot) 하는 방법에 대해 알아봅니다.
