---
title: Azure VM에서 백업된 SAP HANA 데이터베이스 관리
description: 이 문서에서는 Azure 가상 머신에서 실행되는 SAP HANA 데이터베이스를 관리하고 모니터링하기 위한 일반적인 작업에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 40761415042cc619893ab3a712a763d4fb046e38
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267482"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>백업한 SAP HANA 데이터베이스 관리 및 모니터링

이 문서에서는 [Microsoft Azure Backup](./backup-overview.md) 서비스를 통해 Microsoft Azure Backup Recovery Services 자격 증명 모음으로 백업되는 SAP HANA 데이터베이스와 Azure VM(가상 머신)에서 실행되는 SAP HANA 데이터베이스를 관리하고 모니터링하기 위한 일반적인 작업을 설명합니다. 작업과 경고를 모니터링하고, 주문형 백업을 트리거하고, 정책을 편집하고, 데이터베이스 보호를 중지했다가 다시 시작하고, 백업에서 VM을 등록 취소하는 방법을 알아봅니다.

SAP HANA 데이터베이스에 대해 백업을 아직 구성하지 않은 경우 [Azure VM에서 SAP HANA 데이터베이스 백업](./backup-azure-sap-hana-database.md)을 참조하세요.

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>포털에서 수동 백업 작업 모니터링

Microsoft Azure Backup은 Microsoft Azure Portal의 **백업 작업** 섹션에 수동으로 트리거된 모든 작업을 표시합니다.

![백업 작업 섹션](./media/sap-hana-db-manage/backup-jobs.png)

이 포털에는 데이터베이스 검색 및 등록 작업과 백업 및 복원 작업이 표시됩니다. 로그 백업을 포함한 예약된 작업은 이 섹션에 표시되지 않습니다. SAP HANA 네이티브 클라이언트(Studio/Cockpit/DBA Cockpit)에서 수동으로 트리거된 백업도 여기에 표시되지 않습니다.

![백업 작업 목록](./media/sap-hana-db-manage/backup-jobs-list.png)

모니터링에 대해 자세히 알아보려면 [Microsoft Azure Portal에서 모니터링](./backup-azure-monitoring-built-in-monitor.md)과 [Azure Monitor를 사용한 모니터링](./backup-azure-monitoring-use-azuremonitor.md)으로 이동하세요.

## <a name="view-backup-alerts"></a>백업 경고 보기

경고는 SAP HANA 데이터베이스의 백업을 모니터링하는 쉬운 방법입니다. 경고를 통해 백업이 생성하는 수많은 이벤트 속에서 헤매지 않고 가장 중요한 이벤트에 집중할 수 있습니다. Microsoft Azure Backup를 사용하여 경고를 설정하고 다음과 같이 모니터링할 수 있습니다.

* [Azure Portal](https://portal.azure.com/)에 로그인합니다.
* 자격 증명 모음 대시보드에서 **백업 경고**를 선택 합니다.

  ![자격 증명 모음 대시보드의 백업 경고](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* 경고를 볼 수 있습니다.

  ![백업 경고 목록](./media/sap-hana-db-manage/backup-alerts-list.png)

* 경고를 클릭하여 세부 정보를 봅니다.

  ![경고 세부 정보](./media/sap-hana-db-manage/alert-details.png)

현재 Microsoft Azure Backup에서는 이메일을 통해 경고를 보낼 수 있습니다. 경고는 다음과 같이 처리됩니다.

* 모든 백업 실패에 대해 트리거됩니다.
* 오류 코드별로 데이터베이스 수준에서 통합됩니다.
* 데이터베이스의 첫 번째 백업 실패에 대해서만 전송 됩니다.

모니터링에 대해 자세히 알아보려면 [Microsoft Azure Portal에서 모니터링](./backup-azure-monitoring-built-in-monitor.md)과 [Azure Monitor를 사용한 모니터링](./backup-azure-monitoring-use-azuremonitor.md)으로 이동하세요.

## <a name="management-operations"></a>관리 작업

Microsoft Azure Backup에서 지원하는 다양한 관리 작업으로 백업된 SAP HANA 데이터베이스를 쉽게 관리할 수 ​​있습니다. 이러한 작업은 다음 섹션에서 자세히 다룹니다.

### <a name="run-an-on-demand-backup"></a>주문형 백업 실행

백업은 정책 일정에 따라 실행됩니다. 다음과 같이 주문형 백업을 실행할 수 있습니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **백업 항목**에서 SAP HANA 데이터베이스를 실행하는 VM을 선택하고 **지금 백업**을 클릭합니다.
3. **지금 백업**에서 수행할 백업 유형을 선택 합니다. 그런 후 **OK**를 클릭합니다. 이 백업은이 백업 항목과 연결 된 정책에 따라 보존 됩니다.
4. 포털 알림을 모니터링합니다. 자격 증명 모음 대시보드 > **백업 작업** > **진행 중**에서 작업 진행률을 모니터링할 수 있습니다. 데이터베이스의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

기본적으로 요청 시 백업의 보존은 45 일입니다.

### <a name="hana-native-client-integration"></a>HANA 네이티브 클라이언트 통합

#### <a name="backup"></a>Backup

HANA 네이티브 클라이언트에서 **Backint**로 트리거되는 주문형 백업은 **백업 항목** 페이지의 백업 목록에 표시됩니다.

![마지막 백업 실행](./media/sap-hana-db-manage/last-backups.png)

**백업 작업** 페이지에서 [이러한 백업을 모니터링](#monitor-manual-backup-jobs-in-the-portal)할 수도 있습니다.

이러한 주문형 백업은 복원에 대한 복원 지점 목록에도 표시됩니다.

![복원 지점 목록](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>복원

동일한 머신으로 복원을 위해 **Backint**를 사용하여 HANA 네이티브 클라이언트에서 트리거된 복원을 **백업 작업** 페이지에서[모니터링](#monitor-manual-backup-jobs-in-the-portal)할 수 있습니다.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Azure Backup 사용 하도록 설정 된 데이터베이스에서 SAP HANA native client 백업 실행

Microsoft Azure Backup으로 백업 중인 데이터베이스의 로컬 백업(HANA Studio/Cockpit 사용)을 수행하려면 다음 단계를 따르세요.

1. 데이터베이스에 대한 전체 또는 로그 백업이 완료될 때까지 기다립니다. SAP HANA Studio/Cockpit에서 상태를 확인합니다.
2. 로그 백업을 사용하지 않도록 설정하고 관련 데이터베이스에 대한 백업 카탈로그를 파일 시스템에 설정합니다.
3. 이렇게 하려면 **systemdb** > **구성** > **데이터베이스 선택** > **필터(로그)** 를 두 번 클릭합니다.
4. **enable_auto_log_backup**을 **아니요**로 설정합니다.
5. **log_backup_using_backint**를 **False**로 설정합니다.
6. 데이터베이스의 주문형 전체 백업을 수행합니다.
7. 전체 백업 및 카탈로그 백업이 완료될 때까지 기다립니다.
8. 이전 설정을 Azure에 대한 설정으로 되돌립니다.
   * **enable_auto_log_backup**을 **예**로 설정합니다.
   * **log_backup_using_backint**를 **True**로 설정합니다.

### <a name="change-policy"></a>정책 변경

SAP HANA 백업 항목에 대한 기본 정책을 변경할 수 있습니다.

* 자격 증명 모음 대시보드에서 **백업 항목**으로 이동합니다.

  ![백업 항목 선택](./media/sap-hana-db-manage/backup-items.png)

* **Azure VM의 SAP HANA**를 선택합니다.

  ![Azure VM의 SAP HANA 선택](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* 기본 정책을 변경하려는 백업 항목을 선택합니다.
* 기존 백업 정책을 클릭합니다.

  ![기존 백업 정책 선택](./media/sap-hana-db-manage/existing-backup-policy.png)

* 목록에서 선택하여 정책을 변경합니다. 필요한 경우 [새 백업 정책을 만듭니다](./backup-azure-sap-hana-database.md#create-a-backup-policy).

  ![드롭다운 목록에서 정책](./media/sap-hana-db-manage/choose-backup-policy.png)

* 변경 내용을 저장합니다.

  ![변경 내용 저장](./media/sap-hana-db-manage/save-changes.png)

* 정책 수정은 관련된 모든 백업 항목에 영향을 주며 해당하는 **보호 구성** 작업을 트리거합니다.

>[!NOTE]
> 보존 기간의 변경 내용은 새 복구 지점이 아닌 모든 이전 복구 지점에 소급 적용됩니다.
>
> SAP HANA 데이터베이스에는 증분 백업 정책을 사용할 수 없습니다. 증분 백업은 현재 이러한 데이터베이스에 대해 지원 되지 않습니다.

### <a name="modify-policy"></a>정책 수정

정책을 수정 하 여 백업 유형, 빈도 및 보존 범위를 변경 합니다.

>[!NOTE]
>보존 기간의 변경 내용은 새 복구 지점과 모든 이전 복구 지점에 소급 적용됩니다.

1. 자격 증명 모음 대시보드에서 **관리 > 백업 정책**으로 이동하고 편집하려는 정책을 선택합니다.

   ![편집할 정책 선택](./media/sap-hana-db-manage/manage-backup-policies.png)

1. **수정**을 선택합니다.

   ![수정 선택](./media/sap-hana-db-manage/modify-policy.png)

1. 백업 유형의 빈도를 선택합니다.

   ![백업 빈도 선택](./media/sap-hana-db-manage/choose-frequency.png)

정책 수정은 관련된 모든 백업 항목에 영향을 주며 해당하는 **보호 구성** 작업을 트리거합니다.

### <a name="inconsistent-policy"></a>일관성 없는 정책

경우에 따라 정책 수정 작업으로 인해 일부 백업 항목에 대한 정책 버전에 **일관성이 없을** 수 있습니다. 정책 수정 작업이 트리거된 후 백업 항목에 대한 **보호 구성** 작업이 실패하는 경우 이러한 상황이 발생합니다. 백업 항목 보기에 다음과 같이 나타납니다.

![일관성 없는 정책](./media/sap-hana-db-manage/inconsistent-policy.png)

한 번의 클릭으로 영향을 받는 모든 항목에 대한 정책 버전을 수정할 수 있습니다.

![정책 버전 수정](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA 데이터베이스 보호 중지

다음과 같은 몇 가지 방법으로 SAP HANA 데이터베이스 보호를 중지할 수 있습니다.

* 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제
* 미래의 모든 백업 작업을 중지하고 복구 지점을 원래 상태로 유지

복구 지점을 그대로 두기로 선택하는 경우 다음 세부 정보를 염두에 두어야 합니다.

* 모든 복구 지점은 영구적으로 그대로 유지 되 고 모든 정리는 데이터 보존을 사용 하 여 보호 중지에서 중지 됩니다.
* 보호된 인스턴스와 사용한 스토리지 요금이 청구됩니다. 자세한 내용은 [Microsoft Azure Backup 가격 책정](https://azure.microsoft.com/pricing/details/backup/)을 참조하세요.
* 백업을 중지하지 않고 데이터 원본을 삭제하면 새 백업이 실패합니다.

데이터베이스에 대한 보호를 중지하려면:

* 자격 증명 모음 대시보드에서 **백업 항목**을 선택합니다.
* **백업 관리 유형**에서 **Azure VM의 SAP HANA**를 선택합니다.

  ![Azure VM의 SAP HANA 선택](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 보호를 중지하려는 데이터베이스를 선택합니다.

  ![보호를 중지할 데이터베이스 선택](./media/sap-hana-db-manage/select-database.png)

* 데이터베이스 메뉴에서 **백업 중지**를 선택합니다.

  ![백업 중지 선택](./media/sap-hana-db-manage/stop-backup.png)

* **백업 중지** 메뉴에서 데이터를 보존할지 아니면 삭제할지 선택합니다. 원하는 경우 이유나 설명을 입력합니다.

  ![데이터 유지 또는 삭제 선택](./media/sap-hana-db-manage/retain-backup-data.png)

* **백업 중지**를 선택합니다.

### <a name="resume-protection-for-an-sap-hana-database"></a>SAP HANA 데이터베이스에 대한 보호 다시 시작

**백업 데이터 보존** 옵션을 선택하고 SAP HANA 데이터베이스에 대한 보호를 중지하는 경우 나중에 보호를 다시 시작할 수 있습니다. 백업 된 데이터를 유지 하지 않는 경우 보호를 다시 시작할 수 없습니다.

SAP HANA 데이터베이스에 대한 보호를 다시 시작하려면

* 백업 항목을 열고 **백업 다시 시작**을 선택합니다.

   ![백업 다시 시작 선택](./media/sap-hana-db-manage/resume-backup.png)

* **백업 정책** 메뉴에서 정책을 선택한 다음, **저장**을 클릭합니다.

### <a name="upgrading-from-sdc-to-mdc"></a>SDC에서 MDC로 업그레이드

[SDC에서 MDC로 업그레이드 후](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) SAP HANA 데이터베이스 백업을 계속하는 방법에 대해 알아봅니다.

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>SID 변경 없이 SDC에서 MDC로 업그레이드

[SDC에서 MDC로 업그레이드 한 후 SID가 변경](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid)되지 않은 SAP HANA 데이터베이스의 백업을 계속 하는 방법에 대해 알아봅니다.

### <a name="unregister-an-sap-hana-instance"></a>SAP HANA 인스턴스 등록 취소

다음과 같이 보호를 사용하지 않도록 설정한 후 자격 증명 모음을 삭제하기 전에 SAP HANA 인스턴스 등록을 취소합니다.

* 자격 증명 모음 대시보드의 **관리** 아래에서 **백업 인프라**를 선택합니다.

   ![백업 인프라 선택](./media/sap-hana-db-manage/backup-infrastructure.png)

* **Azure VM의 워크로드**로 **백업 관리 유형**을 선택합니다.

   ![Azure VM의 워크로드로 백업 관리 유형 선택](./media/sap-hana-db-manage/backup-management-type.png)

* **보호된 서버**에서 등록을 취소할 인스턴스를 선택합니다. 자격 증명 모음을 삭제하려면 모든 서버/인스턴스의 등록을 취소해야 합니다.

* 보호된 인스턴스를 마우스 오른쪽 단추로 클릭하고 **등록 취소**를 선택합니다.

   ![등록 취소 선택](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>SAP HANA 서버 VM에 확장 다시 등록

VM의 워크로드 확장이 어떤 이유로 영향을 받는 경우가 있습니다. 이러한 경우 VM에서 트리거되는 모든 작업이 실패하기 시작합니다. 그런 다음, VM에서 확장을 다시 등록해야 할 수 있습니다. 다시 등록하면 작업을 계속할 수 있도록 VM에 워크 로드 백업 확장이 다시 설치됩니다.

이 옵션은 주의해서 사용해야 합니다. 이미 정상 상태의 확장이 있는 VM에서 이 작업이 트리거되면 확장이 다시 시작됩니다. 이로 인해 진행 중인 모든 작업이 실패할 수 있습니다. 다시 등록 작업을 트리거하기 전에 하나 이상의 [증상](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures)을 확인하세요.

## <a name="next-steps"></a>다음 단계

* [SAP HANA 데이터베이스를 백업할 때 발생하는 일반적인 문제를 해결하는 방법](./backup-azure-sap-hana-database-troubleshoot.md)을 알아봅니다.
