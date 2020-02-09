---
title: Azure Monitor를 사용 하 여 Azure Backup 모니터링
description: Azure Backup 작업을 모니터링 하 고 Azure Monitor를 사용 하 여 사용자 지정 경고를 만듭니다.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 4ff51080d675c53e53397a070c1f6f1766aa9e85
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989589"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Azure Monitor를 사용 하 여 규모에 맞게 모니터링

Azure Backup은 Recovery Services 자격 증명 모음에 [기본 제공 모니터링 및 경고 기능](backup-azure-monitoring-built-in-monitor.md) 을 제공 합니다. 이러한 기능은 추가 관리 인프라 없이 사용할 수 있습니다. 그러나이 기본 제공 서비스는 다음과 같은 시나리오에서 제한 됩니다.

- 여러 구독에서 여러 Recovery Services 자격 증명 모음의 데이터를 모니터링 하는 경우
- 기본 알림 채널이 전자 메일이 *아닌* 경우
- 사용자가 더 많은 시나리오에 대한 경고를 원하는 경우
- Azure의 System Center Data Protection Manager와 같은 온-프레미스 구성 요소의 정보를 보려는 경우 포털이 [**백업 작업**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) 또는 [**백업 경고**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) 에 표시 되지 않는 경우

## <a name="using-log-analytics-workspace"></a>Log Analytics 작업 영역 사용

### <a name="create-alerts-by-using-log-analytics"></a>Log Analytics를 사용 하 여 경고 만들기

Azure Monitor에서 Log Analytics 작업 영역에 경고를 직접 만들 수 있습니다. 작업 영역에서 *Azure 작업 그룹* 을 사용 하 여 기본 설정 된 알림 메커니즘을 선택 합니다.

> [!IMPORTANT]
> 이 쿼리를 만드는 데 드는 비용에 대한 자세한 내용은 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조 하세요.

그래프 중 하나를 선택 하 여 Log Analytics 작업 영역의 **로그** 섹션을 엽니다. **로그** 섹션에서 쿼리를 편집 하 고이에 대한 경고를 만듭니다.

![Log Analytics 작업 영역에서 경고 만들기](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

**새 경고 규칙**을 선택 하면 다음 그림에 표시 된 것 처럼 Azure Monitor 경고 생성 페이지가 열립니다. 여기서 리소스는 이미 Log Analytics 작업 영역으로 표시 되었으며 작업 그룹 통합이 제공 됩니다.

![Log Analytics 경고 만들기 페이지](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>경고 조건

경고의 정의 특성은 트리거 조건입니다. 다음 이미지와 같이 **로그** 페이지에서 kusto 쿼리를 자동으로 로드 하는 **조건을** 선택 합니다. 여기서 요구 사항에 맞게 조건을 편집할 수 있습니다. 자세한 내용은 [샘플 Kusto 쿼리](#sample-kusto-queries)를 참조 하세요.

![경고 조건 설정](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

필요한 경우 Kusto 쿼리를 편집할 수 있습니다. 임계값, 기간 및 빈도를 선택 합니다. 임계값은 경고가 발생 하는 시간을 결정 합니다. 기간은 쿼리가 실행 되는 기간입니다. 예를 들어 임계값이 0 보다 크면 기간이 5 분이 고 빈도가 5 분인 경우 규칙은 5 분 마다 쿼리를 실행 하 여 이전 5 분을 검토 합니다. 결과 수가 0 보다 큰 경우 선택한 작업 그룹을 통해 알림이 표시 됩니다.

#### <a name="alert-action-groups"></a>경고 작업 그룹

알림 채널을 지정 하려면 작업 그룹을 사용 합니다. 사용 가능한 알림 메커니즘을 보려면 **작업 그룹**에서 **새로 만들기**를 선택 합니다.

!["작업 그룹 추가" 창에서 사용할 수 있는 알림 메커니즘](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Log Analytics의 모든 경고 및 모니터링 요구 사항을 충족 하거나 Log Analytics를 사용 하 여 기본 제공 알림을 보완할 수 있습니다.

자세한 내용은 [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) 및 [Azure Portal에서 작업 그룹 만들기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)를 참조 하세요.

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

기본 그래프는 경고를 작성할 수 있는 기본 시나리오에 대한 Kusto 쿼리를 제공 합니다. 또한 경고를 발생 시킬 데이터를 가져오도록 쿼리를 수정할 수 있습니다. **로그** 페이지에서 다음 샘플 Kusto 쿼리를 붙여넣은 다음 쿼리에 대한 경고를 만듭니다.

- 성공한 모든 백업 작업

    ````Kusto
    AddonAzureBackupJobs
| where JobOperation=="Backup"
| where JobStatus=="Completed"
    ````

- 실패 한 모든 백업 작업

    ````Kusto
    AddonAzureBackupJobs
| where JobOperation=="Backup"
| where JobStatus=="Failed"
    ````

- 모든 성공한 Azure VM 백업 작업

    ````Kusto
    AddonAzureBackupJobs
| where JobOperation=="Backup"
| where JobStatus=="Completed"
| join kind=inner
(
    CoreAzureBackup
    | where OperationName == "BackupItem"
    | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
    | distinct BackupItemUniqueId, BackupItemFriendlyName
)
on BackupItemUniqueId
    ````

- 모든 성공한 SQL 로그 백업 작업

    ````Kusto
    AddonAzureBackupJobs
| where JobOperation=="Backup" and JobOperationSubType=="Log"
| where JobStatus=="Completed"
| join kind=inner
(
    CoreAzureBackup
    | where OperationName == "BackupItem"
    | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
    | distinct BackupItemUniqueId, BackupItemFriendlyName
)
on BackupItemUniqueId
    ````

- 성공한 모든 Azure Backup 에이전트 작업

    ````Kusto
    AddonAzureBackupJobs
| where JobOperation=="Backup"
| where JobStatus=="Completed"
| join kind=inner
(
    CoreAzureBackup
    | where OperationName == "BackupItem"
    | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
    | distinct BackupItemUniqueId, BackupItemFriendlyName
)
on BackupItemUniqueId
    ````

### <a name="diagnostic-data-update-frequency"></a>진단 데이터 업데이트 빈도

자격 증명 모음의 진단 데이터는 일정 시간 동안 Log Analytics 작업 영역에 펌프 됩니다. 모든 이벤트는 Recovery Services 자격 증명 모음에서 푸시된 후 *20 분에서 30 분* Log Analytics 작업 영역에 도착 합니다. 지연에 대한 자세한 내용은 다음과 같습니다.

- 모든 솔루션에서 백업 서비스의 기본 제공 경고는 생성 되는 즉시 푸시됩니다. 따라서 일반적으로 20 분에서 30 분 후에 Log Analytics 작업 영역에 나타납니다.
- 모든 솔루션에서 주문형 백업 작업 및 복원 작업은 *완료*되는 즉시 푸시됩니다.
- SQL 백업을 제외한 모든 솔루션에 대해 예약 된 백업 작업은 *완료*되는 즉시 푸시됩니다.
- SQL 백업의 경우 로그 백업이 15 분 마다 발생할 수 있으므로 로그를 포함 하 여 완료 된 모든 예약 된 백업 작업에 대한 정보는 6 시간 마다 일괄 처리 되 고 푸시됩니다.
- 모든 솔루션에서 백업 항목, 정책, 복구 지점, 저장소 등의 기타 정보는 *하루에 한 번* 이상 푸시됩니다.
- 백업 구성 변경 (예: 정책 변경 또는 정책 편집)은 관련 된 모든 백업 정보의 푸시를 트리거합니다.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Recovery Services 자격 증명 모음의 활동 로그 사용

> [!CAUTION]
> 다음 단계는 *AZURE VM 백업* 에만 적용 됩니다. Azure Backup 에이전트, Azure 내의 SQL 백업 또는 Azure Files와 같은 솔루션에는 이러한 단계를 사용할 수 없습니다.

활동 로그를 사용 하 여 백업 성공 같은 이벤트에 대한 알림을 받을 수도 있습니다. 시작 하려면 다음 단계를 수행 합니다.

1. Azure Portal에 로그인합니다.
1. 관련 Recovery Services 자격 증명 모음을 엽니다.
1. 자격 증명 모음의 속성에서 **활동 로그** 섹션을 엽니다.

적절 한 로그를 확인 하 고 경고를 만들려면 다음을 수행 합니다.

1. 다음 이미지에 표시 된 필터를 적용 하 여 성공적인 백업에 대한 활동 로그를 수신 하 고 있는지 확인 합니다. 레코드를 보려면 필요에 따라 **Timespan** 값을 변경 합니다.

   ![Azure VM 백업에 대한 활동 로그를 검색 하는 필터링](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. 작업 이름을 선택 하 여 관련 세부 정보를 확인 합니다.
1. **새 경고 규칙** 을 선택 하 여 **규칙 만들기** 페이지를 엽니다.
1. [Azure Monitor를 사용 하 여 활동 로그 경고 만들기, 보기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)의 단계를 수행 하 여 경고를 만듭니다.

   ![새 경고 규칙](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

여기서 리소스는 Recovery Services 자격 증명 모음 자체입니다. 활동 로그를 통해 알림이 받으려는 모든 자격 증명 모음에 대해 동일한 단계를 반복 합니다. 이 경고는 이벤트를 기반으로 하기 때문에 조건에는 임계값, 기간 또는 빈도가 없습니다. 관련 활동 로그가 생성 되는 즉시 경고가 발생 합니다.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Log Analytics를 사용 하 여 대규모로 모니터링

활동 로그 및 Log Analytics 작업 영역에서 생성 한 모든 경고를 Azure Monitor에서 볼 수 있습니다. 왼쪽에서 **경고** 창을 열어야 합니다.

활동 로그를 통해 알림을 받을 수는 있지만, 대규모로 모니터링 하기 위해 활동 로그가 아닌 Log Analytics를 사용 하는 것이 좋습니다. 이유는 다음과 같습니다.

- **제한 된 시나리오**: 활동 로그를 통한 알림은 Azure VM 백업에만 적용 됩니다. 모든 Recovery Services 자격 증명 모음에 대해 알림을 설정 해야 합니다.
- **정의 맞춤**: 예약 된 백업 작업이 활동 로그의 최신 정의와 일치 하지 않습니다. 대신 [리소스 로그](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace)에 맞춰집니다. 이 맞춤은 활동 로그 채널을 통해 흐르는 데이터가 변경 될 때 예기치 않은 효과를 발생 시킵니다.
- **활동 로그 채널 문제**: Recovery Services 자격 증명 모음에서 Azure Backup 펌프 활동 로그는 새 모델을 따릅니다. 아쉽게도이 변경 내용은 Azure Government, Azure 독일 및 Azure 중국 21Vianet에서 활동 로그를 생성 하는 것에 영향을 줍니다. 이러한 클라우드 서비스 사용자가 Azure Monitor의 활동 로그에서 경고를 만들거나 구성 하는 경우 경고가 트리거되지 않습니다. 또한 모든 Azure 공용 지역에서 사용자가 [Recovery Services 활동 로그를 Log Analytics 작업 영역으로 수집](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)하면 이러한 로그가 표시 되지 않습니다.

Log Analytics 작업 영역을 사용 하 여 Azure Backup으로 보호 되는 모든 작업에 대해 대규모로 모니터링 하 고 경고 합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 쿼리를 만들려면 [Log Analytics 데이터 모델](backup-azure-log-analytics-data-model.md)을 참조 하세요.
