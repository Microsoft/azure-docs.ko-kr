---
title: Recovery Services 자격 증명 모음에 대 한 진단 설정 사용
description: 이 문서에서는 Azure Backup에 대해 이전 및 새 진단 이벤트를 사용 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 3d10053bae5148f33dba6d1207a81bdb16c37577
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182601"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Recovery Services 자격 증명 모음에 대 한 진단 설정 사용

Azure Backup은 수집 하 여 분석, 경고 및 보고를 위해 사용할 수 있는 진단 이벤트를 보냅니다.

자격 증명 모음으로 이동 하 고 **진단 설정**을 선택 하 여 Azure Portal를 통해 Recovery Services 자격 증명 모음에 대 한 진단 설정을 구성할 수 있습니다. **+ 진단 추가 설정을** 선택 하면 하나 이상의 진단 이벤트를 저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역으로 보낼 수 있습니다.

![진단 설정 창](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure Backup 사용자에 게 제공 되는 진단 이벤트

Azure Backup는 다음과 같은 진단 이벤트를 제공 합니다. 각 이벤트는 백업 관련 아티팩트의 특정 집합에 대 한 자세한 데이터를 제공 합니다.

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

[레거시 이벤트](#legacy-event) azurebackupreport를 계속 사용 하는 경우 위의 이벤트를 사용 하도록 전환 하는 것이 좋습니다.

자세한 내용은 [Azure Backup 진단 이벤트에 대 한 데이터 모델](./backup-azure-reports-data-model.md)을 참조 하세요.

이러한 이벤트에 대 한 데이터는 저장소 계정, Log Analytics 작업 영역 또는 이벤트 허브로 보낼 수 있습니다. 이 데이터를 Log Analytics 작업 영역으로 전송 하는 경우 **진단 설정** 화면에서 **리소스 특정** 설정/해제를 선택 합니다. 자세한 내용은 다음 섹션을 참조하세요.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Log Analytics에서 진단 설정 사용

이제 Azure Backup를 사용 하 여 백업에 대 한 전용 Log Analytics 테이블로 자격 증명 모음 진단 데이터를 보낼 수 있습니다. 이러한 테이블을 [리소스 관련 테이블](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)이라고 합니다.

자격 증명 모음 진단 데이터를 Log Analytics 전송 하려면 다음을 수행 합니다.

1. 자격 증명 모음으로 이동 하 고 **진단 설정**을 선택 합니다. **+ 진단 설정 추가**를 선택 합니다.
1. 진단 설정에 이름을 지정 합니다.
1. **Log Analytics 보내기** 확인란을 선택 하 고 Log Analytics 작업 영역을 선택 합니다.
1. 설정/해제에서 **특정 리소스** 를 선택 하 고 **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**및 **AddonAzureBackupProtectedInstance**의 6 가지 이벤트를 선택 합니다.
1. **저장**을 선택합니다.

   ![리소스 특정 모드](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

데이터가 Log Analytics 작업 영역으로 흐르는 후에는 각 이벤트에 대 한 전용 테이블이 작업 영역에 생성 됩니다. 이러한 테이블을 직접 쿼리할 수 있습니다. 필요한 경우 이러한 테이블 간에 조인이 나 합집합을 수행할 수도 있습니다.

> [!IMPORTANT]
> 6 개 이벤트, 즉 CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage 및 AddonAzureBackupProtectedInstance은 [백업 보고서](./configure-reports.md)의 리소스 전용 모드 *에서만* 지원 됩니다. *Azure 진단 모드에서 이러한 6 개의 이벤트에 대 한 데이터를 보내려고 하면 백업 보고서에 데이터가 표시 되지 않습니다.*

## <a name="legacy-event"></a>레거시 이벤트

일반적으로 자격 증명 모음에 대 한 모든 백업 관련 진단 데이터는 AzureBackupReport 라는 단일 이벤트에 포함 되었습니다. 여기에 설명 된 여섯 가지 이벤트는 기본적으로 AzureBackupReport에 포함 된 모든 데이터를 분해 하는 것입니다.

현재 사용자가이 이벤트에 대 한 기존 사용자 지정 쿼리를가지고 있는 경우 이전 버전과의 호환성을 위해 AzureBackupReport 이벤트를 계속 지원 합니다. 예제는 사용자 지정 로그 경고 및 사용자 지정 시각화입니다. *가능한 한 빨리 [새 이벤트](#diagnostics-events-available-for-azure-backup-users) 로 이동 하는 것이 좋습니다*. 새 이벤트:

* 로그 쿼리에서 데이터를 훨씬 쉽게 사용할 수 있도록 합니다.
* 스키마 및 스키마의 구조를 보다 효율적으로 검색할를 제공 합니다.
* 수집 대기 시간과 쿼리 시간 모두에서 성능을 향상 시킵니다.

*Azure 진단 모드의 레거시 이벤트는 결국 사용 되지 않습니다. 새 이벤트를 선택 하면 나중에 복잡 한 마이그레이션을 피하는 데 도움이 될 수 있습니다*. Log Analytics를 사용 하는 [보고 솔루션](./configure-reports.md) 은 레거시 이벤트의 지원 데이터를 중지 하기도 합니다.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Log Analytics 작업 영역에 대 한 새 진단 설정으로 이동 하는 단계

1. 레거시 이벤트를 사용 하 여 Log Analytics 작업 영역에 데이터를 전송 하는 자격 증명 모음 및 해당 사용자가 속한 구독을 식별 합니다. 각 작업 영역에서 다음 쿼리를 실행 하 여 이러한 자격 증명 모음 및 구독을 식별 합니다.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

    다음은 작업 영역 중 하나에서 실행 되는 쿼리의 스크린샷입니다.

    ![작업 영역 쿼리](./media/backup-azure-diagnostics-events/workspace-query.png)

2. Azure Backup에서 [기본 제공 Azure Policy 정의](./azure-policy-configure-diagnostics.md) 를 사용 하 여 지정 된 범위의 모든 자격 증명 모음에 대 한 새 진단 설정을 추가 합니다. 이 정책은 진단 설정이 없거나 레거시 진단 설정만 있는 자격 증명 모음에 새 진단 설정을 추가 합니다. 이 정책은 한 번에 전체 구독 또는 리소스 그룹에 할당할 수 있습니다. 정책이 할당 된 각 구독에 대 한 소유자 액세스 권한이 있어야 합니다.

새 테이블의 데이터를 사용 하기 위해 사용자 지정 쿼리를 모두 마이그레이션할 때까지 AzureBackupReport 및 6 개의 새 이벤트에 대 한 별도 진단 설정을 선택할 수 있습니다. 다음 그림에서는 두 개의 진단 설정이 있는 자격 증명 모음의 예를 보여 줍니다. 첫 번째 설정인 **Setting1**는 AzureBackupReport 이벤트의 데이터를 Azure 진단 모드의 Log Analytics 작업 영역으로 보냅니다. **Setting2**라는 두 번째 설정은 리소스 특정 모드의 Log Analytics 작업 영역에 6 개의 새 Azure Backup 이벤트 데이터를 보냅니다.

![두 가지 설정](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> AzureBackupReport 이벤트는 Azure 진단 모드 *에서만* 지원 됩니다. *리소스 특정 모드에서이 이벤트에 대 한 데이터를 보내려고 하면 Log Analytics 작업 영역에 데이터가 전달 되지 않습니다.*

> [!NOTE]
> **Azure 진단** 또는 **리소스** 에 대 한 토글은 사용자가 **Log Analytics 보내기를 선택 하**는 경우에만 표시 됩니다. 저장소 계정 또는 이벤트 허브로 데이터를 전송 하기 위해 사용자는 필요한 대상을 선택 하 고 추가 입력 없이 원하는 이벤트의 확인란을 선택 합니다. 다시, 앞으로 이동 하는 레거시 이벤트 AzureBackupReport를 선택 하지 않는 것이 좋습니다.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Azure Site Recovery 이벤트를 Log Analytics으로 보내기

Azure Backup 및 Azure Site Recovery 이벤트는 동일한 Recovery Services 자격 증명 모음에서 전송 됩니다. Azure Site Recovery는 현재 리소스 관련 테이블에 사용할 수 없습니다. Log Analytics Azure Site Recovery 이벤트를 전송 하려는 사용자는 이미지에 표시 된 것 처럼 Azure 진단 *모드만 사용*하도록 지정 됩니다. *Azure Site Recovery 이벤트에 대 한 리소스 특정 모드를 선택 하면 필요한 데이터가 Log Analytics 작업 영역으로 전송 되지 않습니다*.

![Site Recovery 이벤트](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

요약:

* 이미 Azure 진단를 사용 하 Log Analytics 진단이 설정 되어 있고 그 위에 사용자 지정 쿼리를 작성 한 경우 새 이벤트의 데이터를 사용 하도록 쿼리를 마이그레이션할 때까지 해당 설정을 *그대로* 유지 합니다.
* 새 테이블에도 등록 하려면 **새** 진단 설정을 만들고, **리소스 관련**을 선택 하 고, 6 개의 새 이벤트를 선택 합니다.
* 현재 Azure Site Recovery 이벤트를 Log Analytics에 보내는 경우 이러한 이벤트에 대해 리소스 특정 모드를 선택 *하지 마십시오* . 그렇지 않으면 이러한 이벤트의 데이터가 Log Analytics 작업 영역으로 이동 하지 않습니다. 대신, 추가 진단 설정을 만들고, **Azure 진단**을 선택 하 고, 관련 Azure Site Recovery 이벤트를 선택 합니다.

다음 이미지는 자격 증명 모음에 대해 3 개의 진단 설정이 있는 사용자의 예를 보여 줍니다. **Setting1**라는 첫 번째 설정은 AzureBackupReport 이벤트의 데이터를 Azure 진단 모드의 Log Analytics 작업 영역으로 보냅니다. **Setting2**라는 두 번째 설정에서는 6 개의 새 Azure Backup 이벤트에서 리소스 관련 모드의 Log Analytics 작업 영역으로 데이터를 보냅니다. **Setting3**라는 세 번째 설정은 Azure 진단 모드의 Log Analytics 작업 영역에 Azure Site Recovery 이벤트의 데이터를 보냅니다.

![3 가지 설정](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>다음 단계

[진단 이벤트에 대 한 Log Analytics 데이터 모델 알아보기](./backup-azure-reports-data-model.md)
