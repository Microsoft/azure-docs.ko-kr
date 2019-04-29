---
title: 'Azure Backup: Azure Monitor를 사용 하 여 Azure Backup 모니터링'
description: Azure Backup 워크 로드를 모니터링 하 고 Azure Monitor를 사용 하 여 사용자 지정 경고 만들기
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics; Azure Backup; 경고; 진단 설정 작업 그룹
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 15bb64917fa58ba2d13c6f372640957508ab29c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60700246"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Azure Monitor를 사용 하 여 모니터링

합니다 [기본 제공 모니터링 및 경고 문서](backup-azure-monitoring-built-in-monitor.md) 모니터링 및 경고를 단일 Recovery services 자격 증명 모음 및 추가 관리 인프라 없이 제공 되는 기능을 나열 합니다. 그러나 기본 제공 서비스는 다음과 같은 시나리오에서 제한 됩니다.

- 구독에서 여러 RS 자격 증명 모음에서 모니터링 데이터
- 전자 메일 기본 알림 채널이 없는 경우
- 더 많은 시나리오에 대 한 알림을 받는 사용자가 원하는 경우
- 에 표시 되지 않는 Azure에서 System Center DPM (SC DPM)와 같은 온-프레미스 구성 요소에서 정보를 확인 하려는 경우 [Backup 작업](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) 하거나 [Backup 경고](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) 포털에서 합니다.

## <a name="using-log-analytics-workspace"></a>Log Analytics 작업 영역을 사용 하 여

> [!NOTE]
> 진단 설정 통한 Log Analytics 작업 영역에 Azure VM 백업, MAB 에이전트를 System Center DPM (SC-DPM)에서 Azure Vm에서 SQL 백업에서에서 데이터를 주입 되는 합니다. Azure 파일 공유 백업에 Microsoft Azure Backup Server (MABS)에 대 한 지원이 곧 제공 됩니다.

두 개의 Azure 서비스의 기능을 활용 하는 것 **진단 설정** (보낼 데이터 여러 Azure Resource Manager 리소스에서 다른 리소스) 및 **Log Analytics** (LA-생성 하려면 작업 그룹을 사용 하 여 다른 알림 채널로 정의할 수 있는 사용자 지정 경고) 규모의 모니터링에 대 한 합니다. LA를 사용 하 여 대규모 Azure Backup을 모니터링 하는 방법은 다음 섹션에서는 세부 정보입니다.

### <a name="configuring-diagnostic-settings"></a>진단 설정 구성

Azure Recovery services 자격 증명 모음 등 Azure Resource Manager 리소스를 진단 데이터와 예약 된 작업 및 트리거는 사용자 작업에 대 한 가능한 모든 정보를 기록 합니다. 모니터링 섹션에서 '진단 설정'을 클릭 하 고 RS 자격 증명 모음의 진단 데이터에 대 한 대상을 지정 합니다.

![대상으로 버전과 RS 자격 증명 모음 진단 설정](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

LA 작업 영역을 대상으로 하는 다른 구독에서 선택할 수 있습니다. *여러 RS 자격 증명 모음에 대 한 동일한 LA 작업 영역을 선택 하면 한 곳에서 구독의 자격 증명 모음을 모니터링할 수 있습니다.* 모든 채널에 로그 'AzureBackupReport'를 선택 합니다. Azure Backup 관련 LA 작업 영역에 대 한 정보입니다.

> [!IMPORTANT]
> 구성을 마치면 초기 데이터 푸시가 완료에 대 한 24 시간 동안 기다려야 합니다. 이후 모든 이벤트에 설명 된 대로 푸시됩니다 합니다 [빈도 섹션](#diagnostic-data-update-frequency)합니다.

### <a name="deploying-solution-to-log-analytics-workspace"></a>Log Analytics 작업 영역에 솔루션 배포

LA 작업 영역 내에서 데이터가 있으면 [github 템플릿 배포](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) 데이터를 시각화 하려면 LA에 있습니다. 제대로 작업 영역을 식별 하 고 다음에서이 서식 파일을 설치 하려면 동일한 리소스 그룹, 작업 영역 이름 및 작업 영역 위치를 제공 해야 합니다.

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Log Analytics (LA)를 사용 하 여 Azure Backup 데이터 보기

템플릿에 배포 되 면 Azure Backup 모니터링 솔루션 작업 영역 요약 영역에 표시 됩니다. 통해 이동할 수 있습니다.

- Azure Monitor '정보' 섹션 아래에서-> "자세히" 및 관련 작업 영역을 선택 또는
- Log Analytics 작업 영역 선택-> 관련 작업 영역에 일반 섹션에서 '작업 영역 요약'-> 합니다.

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

타일을 클릭 하 여 일련의 그래프와 같은 Azure Backup에서 기본 모니터링 데이터에 대 한 디자이너 템플릿이 열립니다.

#### <a name="all-backup-jobs"></a>모든 백업 작업

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>복원 작업

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Azure 리소스에 대 한 기본 제공된 Azure 백업 경고

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>온-프레미스 리소스에 대 한 기본 제공된 Azure 백업 경고

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>현재 데이터 원본

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>RS 자격 증명 모음 클라우드 저장소

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

위의 그래프는 템플릿과 함께 제공 되 고 고객 자세한 그래프 편집/추가 무료입니다.

> [!IMPORTANT]
> 템플릿을 배포 하는 경우 기본적으로 읽기 전용 잠금을 만듭니다 및 서식 파일을 편집 하 고 저장 되도록 제거 해야 합니다. 잠금을 제거 'Locks' 창에서 Log Analytics 작업 영역의 [설정] 섹션에서 확인 합니다.

### <a name="create-alerts-using-log-analytics"></a>Log Analytics를 사용 하 여 경고 만들기

Azure Monitor에서는 수행할 수 있는 최신 작업 영역에서 자신의 경고를 만들 수 있습니다 *기본 알림 메커니즘을 선택 하 여 Azure 작업 그룹을 활용*합니다. 그래프 위의 LA 영역의 '로그' 섹션에서 클릭 ***쿼리를 편집 하 고 위쪽에 경고를 만들 수 있는***합니다.

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

"새 경고 규칙에서" 위에 표시 된 대로 클릭 하면 Azure Monitor 경고 만들기 화면을 열립니다.

아래 알 수 있듯이, 리소스 LA 작업 영역으로 이미 표시 되어 및 작업 그룹 통합이 제공 됩니다.

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> 이 쿼리를 만드는 관련 가격 책정 영향 제공 되는 점에 유의 하십시오 [여기](https://azure.microsoft.com/pricing/details/monitor/)합니다.

#### <a name="alert-condition"></a>경고 조건

중요 한 측면에는 경고 트리거 조건입니다. 아래와 같이 'Logs' 화면 Kusto 쿼리에서 자동으로 로드 'Condition'를 클릭 하 고 시나리오에 맞게 편집할 수 있습니다. 몇 가지 샘플 Kusto 쿼리에서 제공 되는 [섹션 아래](#sample-kusto-queries)합니다.

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

필요한 경우 Kusto 쿼리를 편집, 오른쪽 임계값 (경고 발생 수를 결정 합니다)를 선택, 올바른 기간 (시간 쿼리를 실행할 창)와 빈도입니다. 에 대 한 예를 들어: 임계값은 0 보다 큰, 기간은 5 분이 고 빈도가 5 분, 다음 규칙은 번역으로 "지난 5 분 동안 5 분 마다 쿼리를 실행 및 결과 수가 0 보다 큰 경우 선택한 작업 그룹을 통해 알림"

#### <a name="action-group-integration"></a>작업 그룹 통합

작업 그룹 사용자에 게 제공 되는 알림 채널을 지정합니다. 섹션에서는 "새로 만들기"에서 "작업 그룹" 클릭 알림 메커니즘의 사용 가능한 목록을 보여줍니다.

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

에 대 한 자세한 정보는 [LA 작업 영역에서 경고](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) 및 약 [작업 그룹](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) Azure Monitor 설명서에서.

따라서 모든 경고 및 모니터링만 LA에서 요구 사항을 충족할 수도 있고 보조 기술은 사용 하는 데 기본 제공 알림 메커니즘으로 사용할 수 있습니다.

### <a name="sample-kusto-queries"></a>Kusto 쿼리 예제

기본 그래프 제공 Kusto 쿼리 기본 시나리오의 경고를 작성할 수 있습니다. 알림을 받을 하려는 데이터를 가져오기 위해 수정할 수 있습니다. 여기 있는 몇 가지 샘플 Kusto 쿼리 'Logs' 창에 붙여 넣고을 만든 다음 쿼리에서 경고 제공 합니다.

#### <a name="all-successful-backup-jobs"></a>모든 성공한 백업 작업

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>모든 실패 한 백업 작업

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>성공한 모든 Azure VM 백업 작업

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-sql-log-backup-jobs"></a>성공한 모든 SQL 로그 백업 작업

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-mab-agent-backup-jobs"></a>성공한 모든 MAB 에이전트 백업 작업

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

### <a name="diagnostic-data-update-frequency"></a>진단 데이터 새로 고침 빈도

자격 증명 모음에서 진단 데이터를 일부 lag 사용 하 여 최신 작업 영역에 주입 됩니다. LA 작업 영역에 모든 이벤트 도착 ***RS 자격 증명 모음에서 푸시됩니다 후 20 ~ 30 분의 간격으로 하 여 합니다.***

- (모든 솔루션)에 걸쳐 기본 제공 백업 서비스 경고를 만들 때 푸시됩니다. 즉, 일반적으로 나타나는 LA 작업 영역에서 20 ~ 30 분 지연 후 합니다.
- 임시 백업 작업 및 모든 솔루션) (전체 복원 작업을 수행 하 되 자 마자 푸시되 ***완료 된***합니다.
- 예약된 된 백업 작업 (SQL 백업)를 제외한 모든 솔루션에서 이러한 되 자 마자 푸시됩니다 ***완료 된***합니다.
- SQL 백업에 대 한 모든 완료 된 예약된 된 백업 작업, 로그를 포함 하 여에 대 일 분 마다 로그 백업을 사용할 수 있으므로 정보 일괄 처리 이며 6 시간 마다 푸시됩니다.
- 백업 항목, 정책, 복구 지점, 저장소 등 모든 솔루션에서와 같은 다른 모든 정보가 푸시됩니다 **하루에 한 번 이상.**
- 백업 구성 정책 편집 정책 등을 변경 하는 등 변경 모든 관련 백업 정보를 푸시를 트리거합니다.

## <a name="using-rs-vaults-activity-logs"></a>RS Vault를 사용 하 여 활동 로그

또한 백업 성공 여부와 같은 이벤트에 대 한 알림을 가져오기 위한 활동 로그를 사용할 수 있습니다.

> [!CAUTION]
> **이 Azure VM 백업에 적용할 수만 note 하십시오.** Azure Backup 에이전트를 같은 Azure 파일 등 Azure 내에서 SQL 백업 다른 솔루션에 대 한이 사용할 수 없습니다.

### <a name="sign-in-into-azure-portal"></a>Azure portal에 로그인

Azure portal에 로그인 하 고 관련 Azure Recovery Services 자격 증명 모음으로 이동 하 고 속성에서 "작업 로그" 섹션을 클릭 합니다.

### <a name="identify-appropriate-log-and-create-alert"></a>경고를 만들고 적절 한 로그 확인

다음 그림에 나와 있는 필터를 적용하여 성공적인 백업에 대한 활동 로그를 받도록 할 수 있습니다. 시간 범위를 변경하면서 레코드를 봅니다.

![Azure VM 백업에 대 한 활동 로그](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

"JSON" 세그먼트를 클릭하여 자세한 내용을 확인하고 복사한 후 텍스트 편집기에서 붙여넣고 볼 수 있습니다. 자격 증명 모음 세부 정보를 표시 해야 하 고 작업을 트리거한 항목 로그 백업 항목 즉, 합니다.

그런 후 "활동 로그 경고 추가"를 클릭하여 이러한 모든 로그에 대한 경고를 생성합니다.

"추가"활동 로그 경고 위에 표시 된 클릭할 수 있습니다 및 경고 만들기 화면 유사한 경고를 만드는 화면 열립니다 [위에서 설명한 대로](#create-alerts-using-log-analytics)입니다.

여기 리소스는 자체 RS 자격 증명 모음 및 하므로 알림 활동 로그를 통해 원하는 모든 자격 증명 모음에 대 한 동일한 작업을 반복 해야 합니다. 조건이는 이벤트 기반 경고 이므로 모든 임계값, 기간, 빈도 제공 되지 않습니다. 관련 활동 로그가 생성 되는 즉시 경고가 발생 합니다.

## <a name="recommendation"></a>권장 사항

***활동 로그에서 모든 경고를 생성 하 고 왼쪽 '경고' 창에서 Azure Monitor에서 LA 작업 영역을 볼 수 있습니다.***

활동 로그를 통해 알림을 사용할 수 있지만 ***LA 하 여 규모 및 없습니다 활동 로그에서 다음과 같은 이유로 모니터링에 Azure Backup 서비스 좋습니다***합니다.

- **제한 된 시나리오의 경우:** Azure VM 백업에만 적용 및 모든 RS 자격 증명 모음에 대해 반복 해야 합니다.
- **에 맞게 정의:** 예약된 된 백업 작업 활동 로그의 최신 정의 사용 하 여 맞지 및 부합 [진단 로그](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-are-azure-monitor-diagnostic-logs)합니다. 아래 지정 하는 대로 활동 로그 채널을 통해 펌프 데이터가 변경 될 때 예기치 않은 영향을이 잠재 고객입니다.
- **활동 로그 채널을 사용 하 여 문제:** Azure Backup에서 Recovery Services 자격 증명 모음에 활동 로그를 펌핑하는 새 모델로 전환했습니다. 그러나 이동이 차세대 Azure Sovereign Clouds에서 활동 로그에 영향을 합니다. Azure 소 버린 클라우드 사용자 생성/구성에서 Azure Monitor를 통해 활동 로그 경고가 트리거되지 않습니다 됩니다. 또한 [여기](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)에 설명된 것처럼 모든 Azure 공용 지역에서 사용자가 Recovery Services 활동 로그를 Log Analytic 작업 영역으로 수집하는 경우 이러한 로그도 나타나지 않습니다.

따라서 모니터링에 대 한 로그 분석 작업 영역을 사용 하는 것은 권장 항상 및 워크 로드를 보호 하 여 모든 Azure Backup에 대 한 규모의 경고.

## <a name="next-steps"></a>다음 단계

- 가리킵니다 [Log analytics 데이터 모델](backup-azure-log-analytics-data-model.md) 사용자 지정 쿼리를 만들 수 있습니다.
