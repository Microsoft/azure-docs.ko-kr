---
title: Azure Monitor로 Azure Backup 모니터
description: Azure Backup 워크로드를 모니터하고 Azure Monitor를 사용하여 사용자 지정 경고를 만듭니다.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 3f5f663a2f0ed0f91cc414d352e975a2ff3b9649
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827157"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Azure Monitor를 사용하여 대규모 모니터링

Azure Backup은 [기본 제공 모니터링 및 경고 기능](backup-azure-monitoring-built-in-monitor.md)을 Recovery Services 자격 증명 모음에 제공합니다. 이러한 기능은 추가 관리 인프라 없이 사용할 수 있습니다. 그러나 다음과 같은 경우에는 이러한 기본 제공 서비스가 제한됩니다.

- 여러 구독 간의 여러 복구 서비스 저장소로부터 데이터를 모니터링하는 경우
- 기본 알림 채널이 이메일이 *아닌* 경우
- 사용자가 더 많은 시나리오에 대해 경고를 원하는 경우
- 포털의 [**백업 작업**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) 또는 [**백업 경고**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)에 표시되지 않는 Azure의 System Center Data Protection Manager와 같은 온-프레미스 구성 요소의 정보를 보려는 경우

## <a name="using-log-analytics-workspace"></a>Log Analytics 작업 영역 사용

### <a name="create-alerts-by-using-log-analytics"></a>Log Analytics를 사용하여 경고 만들기

Azure Monitor에서는 Log Analytics 작업 영역에 고유 경고를 만들 수 있습니다. 작업 영역에서 *Azure 작업 그룹*을 사용하여 원하는 알림 메커니즘을 선택합니다.

> [!IMPORTANT]
> 이 쿼리를 만드는 비용에 대한 자세한 내용은 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.

Log Analytics 작업 영역의 **로그** 섹션을 열고 고유 로그에 대한 쿼리를 만듭니다. **새 경고 규칙**을 선택하면 다음 이미지에 표시된 것처럼 Azure Monitor 경고 만들기 페이지가 열립니다.

![Log Analytics 작업 영역에서 경고 만들기](media/backup-azure-monitoring-laworkspace/custom-alert.png)

여기에서 리소스는 이미 Log Analytics 작업 영역으로 표시되어 있으며, 작업 그룹 통합이 제공됩니다.

![Log Analytics 경고 만들기 페이지](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>경고 조건

경고 특성을 정의하는 것이 트리거 조건입니다. 다음 이미지에 표시된 것처럼 **로그** 페이지에서 **조건**을 선택하여 Kusto 쿼리를 자동으로 로드합니다. 여기에서 필요에 맞게 조건을 편집할 수 있습니다. 자세한 내용은 [샘플 Kusto 쿼리](#sample-kusto-queries)를 참조하세요.

![경고 조건 설정](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

필요한 경우 Kusto 쿼리를 편집할 수 있습니다. 임계값, 기간 및 빈도를 선택합니다. 임계값에 따라 경고 발생 시간이 결정됩니다. 기간은 쿼리가 실행되는 기간입니다. 예를 들어 임계값이 0보다 크고 기간이 5분고, 간격이 5분이면, 규칙에 따라 쿼리가 5분마다 실행되고, 이전 5분을 검토합니다. 결과 수가 0보다 크면 선택된 작업 그룹을 통해 알림이 표시됩니다.

> [!NOTE]
> 하루에 한 번 경고 규칙을 실행 하려면 지정 된 날짜에 생성 된 모든 이벤트/로그에서 ' period ' 및 ' frequency '의 값을 모두 1440, 즉 24 시간으로 변경 합니다.

#### <a name="alert-action-groups"></a>경고 작업 그룹

작업 그룹을 사용하여 알림 채널을 지정합니다. 사용 가능한 알림 메커니즘을 보려면 **작업 그룹**에서 **새로 만들기**를 선택합니다.

!["작업 그룹 추가" 창에서 사용 가능한 알림 메커니즘](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Log Analytics 단독으로만 모든 경고 및 모니터링 요구 사항을 충족하거나 Log Analytics를 사용하여 기본 제공되는 알림을 보완할 수 있습니다.

자세한 내용은 [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../azure-monitor/platform/alerts-log.md) 및 [Azure Portal에서 작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md)를 참조하세요.

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

기본 그래프는 경고를 빌드할 수 있는 기본 시나리오에 대한 Kusto 쿼리를 제공합니다. 또한 경고 대상 데이터를 가져오도록 쿼리를 수정할 수 있습니다. 다음 샘플 Kusto 쿼리를 **로그** 페이지에 붙여넣고 쿼리에 대한 경고를 만듭니다.

- 모든 성공한 백업 작업

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- 모든 실패한 백업 작업

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- 모든 성공한 Azure VM 백업 작업

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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

- 모든 성공한 Azure Backup 에이전트 작업

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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

- 백업 항목별로 소비된 백업 스토리지

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs
    | sort by StorageConsumedInMBs desc
    ````

### <a name="diagnostic-data-update-frequency"></a>진단 데이터 업데이트 빈도

자격 증명 모음에서 Log Analytics 작업 영역으로 전달되는 진단 데이터가 일부 지연되었습니다. 모든 이벤트는 Recovery Services 자격 증명 모음에서 푸시되고 *20~30분* 후에 Log Analytics 작업 영역에 도착합니다. 지연에 대한 세부 정보는 다음과 같습니다.

- 모든 솔루션에서 백업 서비스의 기본 제공 경고는 생성되는 즉시 푸시됩니다. 따라서 일반적으로 20~30분 후에는 Log Analytics 작업 영역에 표시됩니다.
- 모든 솔루션에서 주문형 백업 작업 및 복원 작업은 *완료*되는 즉시 푸시됩니다.
- SQL 백업을 제외한 모든 솔루션의 경우, *완료*되는 즉시 예약된 백업 작업이 푸시됩니다.
- SQL 백업의 경우, 로그 백업이 15분 간격으로 발생할 수 있으므로, 로그를 포함하여 모든 완료된 예약 백업 작업에 대한 정보가 6시간 간격으로 일괄 처리되고 푸시됩니다.
- 모든 솔루션에서 백업 항목, 정책, 복구 지점, 스토리지 등과 같은 다른 정보는 최소한 *하루 한 번* 이상 푸시됩니다.
- 백업 구성에 변경(예: 정책 변경 또는 정책 편집)이 있으면 모든 관련 백업 정보의 푸시가 트리거됩니다.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Recovery Services 자격 증명 모음의 활동 로그 사용

> [!CAUTION]
> 다음 단계는 *Azure VM 백업*에만 적용됩니다. Azure Backup 에이전트, Azure 내부의 SQL 백업 또는 Azure Files와 같은 솔루션에서는 이러한 단계를 사용할 수 없습니다.

또한 활동 로그를 사용하여 백업 성공과 같은 이벤트에 대한 알림을 가져올 수 있습니다. 시작하려면 다음 단계를 따르세요.

1. Azure Portal에 로그인합니다.
2. 관련된 Recovery Services 자격 증명 모음을 엽니다.
3. 자격 증명 모음 속성에서 **활동 로그** 섹션을 엽니다.

적절한 로그를 확인하고 경고를 만듭니다.

1. 다음 이미지에 표시된 필터를 적용하여 성공한 백업에 대해 활동 로그가 수신되는지 확인합니다. 레코드를 보려면 필요에 따라 **시간 범위** 값을 변경합니다.

   ![Azure VM 백업에 대한 활동 로그를 찾기 위한 필터링](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. 작업 이름을 선택하여 관련 세부 정보를 확인합니다.
3. **새 경고 규칙**을 선택하여 **규칙 만들기** 페이지를 엽니다.
4. [Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리](../azure-monitor/platform/alerts-activity-log.md)의 단계에 따라 경고를 만듭니다.

   ![새 경고 규칙](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

여기에서 리소스는 Recovery Services 자격 증명 모음 자체입니다. 활동 로그를 통해 알림을 받으려는 모든 자격 증명 모음에 대해 동일한 단계를 반복합니다. 이 경고는 이벤트를 기반으로 하기 때문에 조건에 임계값, 기간 또는 빈도가 포함되지 않습니다. 관련 활동 로그가 생성되는 즉시 경고가 발생합니다.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Log Analytics를 사용하여 대규모로 모니터링

Azure Monitor의 활동 로그 및 Log Analytics 작업 영역에서 생성된 모든 경고를 볼 수 있습니다. 왼쪽에서 **경고** 창을 엽니다.

활동 로그를 통해 알림을 받을 수 있지만, 대규모 모니터링을 위해서는 활동 로그 대신 Log Analytics를 사용하는 것이 좋습니다. 이유는 다음과 같습니다.

- **제한된 시나리오**: 활동 로그를 통한 알림은 Azure VM 백업에만 적용됩니다. 모든 Recovery Services 자격 증명 모음에 대해 알림을 설정해야 합니다.
- **정의 적합성**: 예약된 백업 활동이 활동 로그의 최신 정의와 맞지 않습니다. 대신 [리소스 로그](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)에 맞게 정렬됩니다. 이러한 정렬로 인해 활동 로그 채널을 통과하는 데이터가 변경될 때 예상치 않은 효과가 발생합니다.
- **활동 로그 채널 문제**: Recovery Services 자격 증명 모음에서 Azure Backup에서 제공되는 활동 로그는 새 모델을 따릅니다. 안타깝게도 이 변경 사항은 Azure Government, Azure 독일 및 Azure 중국 21Vianet에서 활동 로그 생성에 영향을 줍니다. 이러한 클라우드 서비스 사용자가 Azure Monitor의 활동 로그에서 경고를 만들거나 구성할 때는 경고가 트리거되지 않습니다. 또한 모든 Azure 공용 지역에서 사용자가 [Recovery Services 활동 로그를 Log Analytics 작업 영역에 수집](../azure-monitor/platform/activity-log.md)하는 경우, 이러한 로그가 표시되지 않습니다.

Azure Backup으로 보호되는 모든 워크로드에 대한 대규모 모니터링 및 경고를 위해서는 Log Analytics 작업 영역을 사용하세요.

## <a name="next-steps"></a>다음 단계

사용자 지정 쿼리를 만들려면 [Log Analytics 데이터 모델](backup-azure-reports-data-model.md)을 참조하세요.
