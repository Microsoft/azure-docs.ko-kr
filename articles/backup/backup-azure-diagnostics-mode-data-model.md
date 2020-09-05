---
title: Azure Monitor 로그 데이터 모델
description: 이 문서에서는 Azure Backup 데이터에 대한 Azure Monitor Log Analytics 데이터 모델 세부 정보에 대해 설명합니다.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 1fcb4eb0c584f792132f19c8c4d66289342aa36e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020952"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Azure Backup 데이터용 Log Analytics 데이터 모델

Log Analytics 데이터 모델을 사용하여 Log Analytics에서 사용자 지정 경고를 만들 수 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> 이 데이터 모델은 LA(Log Analytics)로 진단 이벤트를 보내는 Azure Diagnostics 모드를 참조합니다. 새 리소스 특정 모드의 데이터 모델에 대해 알아보려면 다음 문서를 참조할 수 있습니다. [Azure Backup 진단 이벤트에 대한 데이터 모델](./backup-azure-reports-data-model.md)

## <a name="using-azure-backup-data-model"></a>Azure Backup 데이터 모델 사용

데이터 모델의 일부로 제공 되는 다음 필드를 사용 하 여 요구 사항에 따라 시각적 개체, 사용자 지정 쿼리 및 대시보드를 만들 수 있습니다.

### <a name="alert"></a>경고

이 표에서는 경고 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | Description |
| --- | --- | --- |
| AlertUniqueId_s |텍스트 |생성된 경고의 고유 식별자 |
| AlertType_s |텍스트 |경고의 형식(예: Backup) |
| AlertStatus_s |텍스트 |경고의 상태(예: Active) |
| AlertOccurrenceDateTime_s |날짜/시간 |경고를 만든 날짜 및 시간 |
| AlertSeverity_s |텍스트 |경고의 심각도(예: Critical) |
|AlertTimeToResolveInMinutes_s    | Number        |경고를 해결하는 데 걸린 시간입니다. 활성 경고의 경우 비어 있습니다.         |
|AlertConsolidationStatus_s   |텍스트         |경고가 통합 경고인지 여부 식별         |
|CountOfAlertsConsolidated_s     |Number         |통합 된 경고 인 경우 통합 된 경고 수          |
|AlertRaisedOn_s     |텍스트         |경고가 발생하는 엔터티의 유형         |
|AlertCode_s     |텍스트         |경고 유형을 고유하게 식별하는 코드         |
|RecommendedAction_s   |텍스트         |경고를 해결하기 위한 권장 작업         |
| EventName_s |텍스트 |이벤트의 이름, 항상 AzureBackupCentralReport임 |
| BackupItemUniqueId_s |텍스트 |경고와 관련된 백업 항목의 고유 식별자 |
| SchemaVersion_s |텍스트 |스키마의 현재 버전(예: **V2**) |
| State_s |텍스트 |경고 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업을 수행 하기 위한 공급자 유형 (예:이 경고가 속한 예 iaasvm, FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(예: Alert) |
| Category |텍스트 |Azure Monitor 로그에 푸시된 진단 데이터의 범주 항상 AzureBackupReport임 |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedContainerUniqueId_s |텍스트 |경고와 관련된 보호된 서버의 고유 식별자(V1에서 ProtectedServerUniqueId_s였음)|
| VaultUniqueId_s |텍스트 |경고와 관련된 보호된 자격 증명 모음의 고유 식별자 |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스의 고유 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="backupitem"></a>BackupItem

이 표에서는 백업 항목 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | Description |
| --- | --- | --- |
| EventName_s |텍스트 |이벤트의 이름, 항상 AzureBackupCentralReport임 |  
| BackupItemUniqueId_s |텍스트 |백업 항목의 고유 식별자 |
| BackupItemId_s |텍스트 |백업 항목의 식별자(이 필드는 v1 스키마에만 사용할 수 있음) |
| BackupItemName_s |텍스트 |백업 항목의 이름 |
| BackupItemFriendlyName_s |텍스트 |백업 항목의 친숙한 이름 |
| BackupItemType_s |텍스트 |백업 항목의 형식(예: VM, FileFolder) |
| BackupItemProtectionState_s |텍스트 |Backup 항목의 보호 상태 |
| BackupItemAppVersion_s |텍스트 |백업 항목의 애플리케이션 버전 |
| ProtectionState_s |텍스트 |백업 항목의 현재 보호 상태(예: Protected, ProtectionStopped) |
| ProtectionGroupName_s |텍스트 | SC DPM 및 MABS의 경우 백업 항목이 보호되는 보호 그룹의 이름(해당하는 경우)|
| SecondaryBackupProtectionState_s |텍스트 |백업 항목에 대해 보조 보호를 사용하도록 설정할지 여부|
| SchemaVersion_s |텍스트 |스키마의 버전(예: **V2**) |
| State_s |텍스트 |백업 항목 개체의 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업을 수행 하기 위한 공급자 유형 (예:이 백업 항목이 속한 예 iaasvm, FileFolder) |
| OperationName |텍스트 |작업의 이름(예: BackupItem) |
| Category |텍스트 |Azure Monitor 로그에 푸시된 진단 데이터의 범주 항상 AzureBackupReport임 |
| 리소스 |텍스트 |데이터가 수집되는 리소스(예: Recovery Services 자격 증명 모음 이름) |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 ID(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |수집되는 데이터에 대한 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |수집되는 데이터에 대한 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |수집되는 데이터에 대한 리소스 종류(예: Vaults) |

### <a name="backupitemassociation"></a>BackupItemAssociation

이 표에서는 다양한 엔터티와의 백업 항목 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | Description |
| --- | --- | --- |
| EventName_s |텍스트 |이 필드는이 이벤트의 이름을 나타냅니다. 항상 AzureBackupCentralReport |  
| BackupItemUniqueId_s |텍스트 |백업 항목의 고유 ID |
| SchemaVersion_s |텍스트 |이 필드는 스키마의 현재 버전을 나타냅니다. **V2** |
| State_s |텍스트 |백업 항목 연결 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| BackupItemSourceSize_s |텍스트 | 백업 항목의 프런트 엔드 크기 |
| BackupManagementServerUniqueId_s |텍스트 | 해당하는 경우 백업 항목을 보호하는 백업 관리 서버를 고유하게 식별하는 필드 |
| Category |텍스트 |이 필드는 Log Analytics으로 푸시되는 진단 데이터의 범주를 나타냅니다. AzureBackupReport입니다. |
| OperationName |텍스트 |현재 작업의 이름(BackupItemAssociation)을 나타냅니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedContainerUniqueId_s |텍스트 |백업 항목과 관련된 보호된 서버의 고유 식별자(V1에서 ProtectedServerUniqueId_s였음) |
| VaultUniqueId_s |텍스트 |백업 항목이 포함된 자격 증명 모음의 고유 식별자 |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |수집되는 데이터에 대한 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |수집되는 데이터에 대한 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |수집되는 데이터에 대한 리소스 종류(예: Vaults) |

### <a name="backupmanagementserver"></a>BackupManagementServer

이 표에서는 다양한 엔터티와의 백업 항목 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | Description |
| --- | --- | --- |
|BackupManagementServerName_s     |텍스트         |백업 관리 서버의 이름        |
|AzureBackupAgentVersion_s     |텍스트         |백업 관리 서버의 Azure Backup 에이전트 버전          |
|BackupManagementServerVersion_s     |텍스트         |백업 관리 서버의 버전|
|BackupManagementServerOSVersion_s    |텍스트            |백업 관리 서버의 OS 버전|
|BackupManagementServerType_s     |텍스트         |백업 관리 서버의 유형(MABS, SC DPM)|
|BackupManagementServerUniqueId_s     |텍스트         |백업 관리 서버를 고유하게 식별하는 필드       |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |수집되는 데이터에 대한 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |수집되는 데이터에 대한 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |수집되는 데이터에 대한 리소스 종류(예: Vaults) |

### <a name="job"></a>작업

이 표에서는 작업 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | Description |
| --- | --- | --- |
| EventName_s |텍스트 |이벤트의 이름, 항상 AzureBackupCentralReport임 |
| BackupItemUniqueId_s |텍스트 |백업 항목의 고유 식별자 |
| SchemaVersion_s |텍스트 |스키마의 버전(예: **V2**) |
| State_s |텍스트 |작업 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(Job)을 나타냅니다. |
| Category |텍스트 |이 필드는 Azure Monitor 로그에 푸시되는 진단 데이터의 범주를 나타냅니다. AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedServerUniqueId_s |텍스트 |작업과 관련된 보호된 서버의 고유 식별자 |
| ProtectedContainerUniqueId_s |텍스트 | 작업이 실행되는 보호 컨테이너를 식별하기 위한 고유 ID |
| VaultUniqueId_s |텍스트 |보호된 자격 증명 모음의 고유 식별자 |
| JobOperation_s |텍스트 |실행되는 작업에 대한 동작(예: Backup, 복원, Backup 구성) |
| JobStatus_s |텍스트 |완료된 작업의 상태(예: Completed, Failed) |
| JobFailureCode_s |텍스트 |발생한 작업 실패로 인한 오류 코드 문자열 |
| JobStartDateTime_s |날짜/시간 |작업 실행이 시작된 날짜 및 시간 |
| BackupStorageDestination_s |텍스트 |백업 스토리지의 대상(예: Cloud, Disk)  |
| AdHocOrScheduledJob_s |텍스트 | 작업이 임시 작업인지 또는 예약된 작업인지 지정하는 필드 |
| JobDurationInSecs_s | Number |총 작업 기간(초) |
| DataTransferredInMB_s | Number |이 작업에 대해 전송되는 데이터 크기(MB 단위)|
| JobUniqueId_g |텍스트 |작업을 식별하는 고유 ID |
| RecoveryJobDestination_s |텍스트 | 데이터를 복구하는 복구 작업의 대상 |
| RecoveryJobRPDateTime_s |DateTime | 복구 지점이 생성 된 날짜, 시간 |
| RecoveryJobRPLocation_s |텍스트 | 복구 중인 복구 지점이 저장 된 위치입니다.|
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID)|
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="policy"></a>정책

이 표에서는 정책 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 적용할 수 있는 버전 | Description |
| --- | --- | --- | --- |
| EventName_s |텍스트 ||이 필드는이 이벤트의 이름을 나타냅니다. 항상 AzureBackupCentralReport |
| SchemaVersion_s |텍스트 ||이 필드는 스키마의 현재 버전을 나타냅니다. **V2** |
| State_s |텍스트 ||정책 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 ||백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 ||현재 작업의 이름(Policy)을 나타냅니다. |
| Category |텍스트 ||이 필드는 Azure Monitor 로그에 푸시되는 진단 데이터의 범주를 나타냅니다. AzureBackupReport입니다. |
| 리소스 |텍스트 ||데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| PolicyUniqueId_g |텍스트 ||정책을 식별하는 고유 ID |
| PolicyName_s |텍스트 ||지정된 정책의 이름 |
| BackupFrequency_s |텍스트 ||백업이 실행되는 빈도(예: 매일, 매주) |
| BackupTimes_s |텍스트 ||백업이 예약된 날짜 및 시간 |
| BackupDaysOfTheWeek_s |텍스트 ||백업이 예약된 주의 요일 |
| RetentionDuration_s |정수 ||구성된 백업에 대한 보존 기간 |
| DailyRetentionDuration_s |정수 ||구성된 백업에 대한 총 보존 기간(일) |
| DailyRetentionTimes_s |텍스트 ||매일 보존이 구성된 날짜 및 시간 |
| WeeklyRetentionDuration_s |10진수 ||구성된 백업에 대한 총 매주 보존 기간 |
| WeeklyRetentionTimes_s |텍스트 ||매주 보존이 구성된 날짜 및 시간 |
| WeeklyRetentionDaysOfTheWeek_s |텍스트 ||매주 보존에 대해 선택한 주의 요일 |
| MonthlyRetentionDuration_s |10진수 ||구성된 백업에 대한 총 보존 기간(월) |
| MonthlyRetentionTimes_s |텍스트 ||매월 보존이 구성된 날짜 및 시간 |
| MonthlyRetentionFormat_s |텍스트 ||월별 보존에 대한 구성 형식(예: 일 기준 매일, 주 기준 매주) |
| MonthlyRetentionDaysOfTheWeek_s |텍스트 ||매월 보존에 대해 선택한 주의 요일 |
| MonthlyRetentionWeeksOfTheMonth_s |텍스트 ||매월 보존이 구성 된 월의 주 (예: 첫 번째, 마지막) |
| YearlyRetentionDuration_s |10진수 ||구성된 백업에 대한 총 보존 기간(연) |
| YearlyRetentionTimes_s |텍스트 ||매년 보존이 구성된 날짜 및 시간 |
| YearlyRetentionMonthsOfTheYear_s |텍스트 ||매년 보존에 대해 선택한 연도의 달 |
| YearlyRetentionFormat_s |텍스트 ||연별 보존에 대한 구성 형식(예: 일 기준 매일, 주 기준 매주) | |
| YearlyRetentionDaysOfTheMonth_s |텍스트 ||매년 보존에 대해 선택한 월의 날짜 |
| SynchronisationFrequencyPerDay_s |정수 |v2|SC DPM 및 MABS에 대해 파일 백업이 동기화된 하루 중 시간 수 |
| DiffBackupFormat_s |텍스트 |v2|Azure VM 백업에서 SQL에 대한 차등 백업의 형식 |
| DiffBackupTime_s |Time |v2|Azure VM 백업에서 SQL에 대한 차등 백업 시간|
| DiffBackupRetentionDuration_s |10진수 |v2|Azure VM 백업에서 SQL에 대한 차등 백업의 보존 기간|
| LogBackupFrequency_s |10진수 |v2|SQL에 대한 로그 백업의 빈도|
| LogBackupRetentionDuration_s |10진수 |v2|Azure VM 백업에서 SQL에 대한 로그 백업의 보존 기간|
| DiffBackupDaysofTheWeek_s |텍스트 |v2|Azure VM 백업에서 SQL에 대한 차등 백업의 요일|
| SourceSystem |텍스트 ||현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 ||수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 ||데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 ||데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 ||데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 ||데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="policyassociation"></a>PolicyAssociation

이 표에서는 다양한 엔터티와의 정책 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 적용할 수 있는 버전 | Description |
| --- | --- | --- | --- |
| EventName_s |텍스트 ||이 필드는이 이벤트의 이름을 나타냅니다. 항상 AzureBackupCentralReport |
| SchemaVersion_s |텍스트 ||이 필드는 스키마의 현재 버전을 나타냅니다. **V2** |
| State_s |텍스트 ||정책 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 ||백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 ||현재 작업의 이름(PolicyAssociation)을 나타냅니다. |
| Category |텍스트 ||이 필드는 Azure Monitor 로그에 푸시되는 진단 데이터의 범주를 나타냅니다. AzureBackupReport입니다. |
| 리소스 |텍스트 ||데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| PolicyUniqueId_g |텍스트 ||정책을 식별하는 고유 ID |
| VaultUniqueId_s |텍스트 ||이 정책이 속한 자격 증명 모음의 고유 ID |
| BackupManagementServerUniqueId_s |텍스트 |v2 |해당하는 경우 백업 항목을 보호하는 백업 관리 서버를 고유하게 식별하는 필드        |
| SourceSystem |텍스트 ||현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 ||수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 ||데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 ||데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 ||데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 ||데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="protected-container"></a>보호된 컨테이너

다음 표에서는 보호된 컨테이너에 대한 기본 필드를 제공합니다. (v1에서 ProtectedServer였음)

| 필드 | 데이터 형식 | Description |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |텍스트 | 보호된 컨테이너를 고유하게 식별하는 필드 |
| ProtectedContainerOSType_s |텍스트 |보호된 컨테이너의 OS 유형 |
| ProtectedContainerOSVersion_s |텍스트 |보호된 컨테이너의 OS 버전 |
| AgentVersion_s |텍스트 |에이전트 백업 또는 보호 에이전트의 버전 번호 (SC DPM 및 MABS의 경우) |
| BackupManagementType_s |텍스트 |백업을 수행하기 위한 공급자 유형입니다. 예: IaaSVM, FileFolder |
| EntityState_s |텍스트 |보호된 서버 개체의 현재 상태 예: 활성, 삭제됨 |
| ProtectedContainerFriendlyName_s |텍스트 |보호된 서버의 친숙한 이름 |
| ProtectedContainerName_s |텍스트 |보호된 컨테이너의 이름 |
| ProtectedContainerWorkloadType_s |텍스트 |백업된 보호된 컨테이너의 유형 예: IaaSVMContainer |
| ProtectedContainerLocation_s |텍스트 |보호된 컨테이너가 온-프레미스 또는 Azure에 있는지 여부 |
| ProtectedContainerType_s |텍스트 |보호된 컨테이너가 서버인지 또는 컨테이너인지 여부 |
| ProtectedContainerProtectionState_s’  |텍스트 |보호된 컨테이너의 보호 상태 |

### <a name="storage"></a>스토리지

이 표에서는 스토리지 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | Description |
| --- | --- | --- |
| CloudStorageInBytes_s |10진수 |최신 값에 따라 계산된, 백업에 사용된 클라우드 백업 스토리지(이 필드는 v1 스키마에만 해당됨)|
| ProtectedInstances_s |10진수 |청구에서 프런트 엔드 스토리지 계산에 사용된 보호된 인스턴스 수이며, 최신 값을 기준으로 하여 계산됩니다. |
| EventName_s |텍스트 |이 필드는이 이벤트의 이름을 나타냅니다. 항상 AzureBackupCentralReport |
| SchemaVersion_s |텍스트 |이 필드는 스키마의 현재 버전을 나타냅니다. **V2** |
| State_s |텍스트 |스토리지 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(Storage)을 나타냅니다. |
| Category |텍스트 |이 필드는 Azure Monitor 로그에 푸시되는 진단 데이터의 범주를 나타냅니다. AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedServerUniqueId_s |텍스트 |스토리지가 계산되는 보호된 서버의 고유 ID |
| VaultUniqueId_s |텍스트 |스토리지가 계산되는 자격 증명 모음의 고유 ID |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |
| StorageUniqueId_s |텍스트 |스토리지 엔터티를 식별하는 데 사용되는 고유 ID |
| StorageType_s |텍스트 |스토리지 유형(예: 클라우드, 볼륨, 디스크) |
| StorageName_s |텍스트 |스토리지 엔터티 이름(예: E:\) |
| StorageTotalSizeInGBs_s |텍스트 |스토리지 엔터티에서 사용한 총 스토리지 크기(GB)|

### <a name="storageassociation"></a>StorageAssociation

이 표에서는 스토리지를 다른 엔터티에 연결하는 기본적인 스토리지 관련 필드를 제공합니다.

| 필드 | 데이터 형식 | Description |
| --- | --- |  --- |
| StorageUniqueId_s |텍스트 |스토리지 엔터티를 식별하는 데 사용되는 고유 ID |
| SchemaVersion_s |텍스트 |이 필드는 스키마의 현재 버전을 나타냅니다. **V2** |
| BackupItemUniqueId_s |텍스트 |스토리지 엔터티와 관련된 백업 항목을 식별하는 데 사용되는 고유 ID |
| BackupManagementServerUniqueId_s |텍스트 |스토리지 엔터티와 관련된 백업 관리 서버를 식별하는 데 사용되는 고유 ID|
| VaultUniqueId_s |텍스트 |스토리지 엔터티와 관련된 자격 증명 모음을 식별하는 데 사용되는 고유 ID|
| StorageConsumedInMBs_s |Number|해당하는 스토리지의 해당 백업 항목에서 사용하는 스토리지 크기 |
| StorageAllocatedInMBs_s |Number |디스크 형식의 해당 스토리지에 있는 해당 백업 항목에 의해 할당된 스토리지 크기|

### <a name="vault"></a>Vault

이 표에서는 자격 증명 모음 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | Description |
| --- | --- | --- |
| EventName_s |텍스트 |이 필드는이 이벤트의 이름을 나타냅니다. 항상 AzureBackupCentralReport |
| SchemaVersion_s |텍스트 |이 필드는 스키마의 현재 버전을 나타냅니다. **V2** |
| State_s |텍스트 |자격 증명 모음 개체의 현재 상태(예: Active, Deleted) |
| OperationName |텍스트 |현재 작업의 이름(Vault)을 나타냅니다. |
| Category |텍스트 |이 필드는 Azure Monitor 로그에 푸시되는 진단 데이터의 범주를 나타냅니다. AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| VaultUniqueId_s |텍스트 |자격 증명 모음의 고유 ID |
| VaultName_s |텍스트 |자격 증명 모음의 이름 |
| AzureDataCenter_s |텍스트 |자격 증명 모음이 위치한 데이터 센터 |
| StorageReplicationType_s |텍스트 |자격 증명 모음에 대한 스토리지 복제 유형(예: GeoRedundant) |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="backup-management-server"></a>백업 관리 서버

다음 표에서는 백업 관리 서버에 대한 기본 필드를 제공합니다.

|필드  |데이터 형식  | Description  |
|---------|---------|----------|
|BackupManagementServerName_s     |텍스트         |백업 관리 서버의 이름        |
|AzureBackupAgentVersion_s     |텍스트         |백업 관리 서버의 Azure Backup 에이전트 버전          |
|BackupManagementServerVersion_s     |텍스트         |백업 관리 서버의 버전|
|BackupManagementServerOSVersion_s     |텍스트            |백업 관리 서버의 OS 버전|
|BackupManagementServerType_s     |텍스트         |백업 관리 서버의 유형(MABS, SC DPM)|
|BackupManagementServerUniqueId_s     |텍스트         |백업 관리 서버를 고유하게 식별하는 필드       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

이 표에서는 볼륨이 연결된 작업을 지정합니다.

| 필드 | 데이터 형식 | Description |
| --- | --- | --- |
| StorageUniqueId_s |텍스트 |스토리지 엔터티를 식별하는 데 사용되는 고유 ID |
| BackupItemType_s |텍스트 |이 볼륨이 기본 설정 스토리지인 작업|

### <a name="protectedinstance"></a>ProtectedInstance

이 표에서는 보호된 기본 인스턴스 관련 필드를 제공합니다.

| 필드 | 데이터 형식 |적용할 수 있는 버전 | Description |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |텍스트 |v2|DPM, MABS를 사용하여 백업된 VM의 백업 항목을 식별하는 데 사용되는 고유 ID|
| ProtectedContainerUniqueId_s |텍스트 |v2|DPM, MABS를 사용하여 백업된 VM을 제외한 모든 항목에 대해 보호된 컨테이너를 식별하는 데 사용되는 고유 ID|
| ProtectedInstanceCount_s |텍스트 |v2|해당 날짜/시간에 연결된 백업 항목 또는 보호된 컨테이너에 대한 보호된 인스턴스 수|

### <a name="recoverypoint"></a>복구 지점

이 표에서는 기본적인 복구 지점 관련 필드를 제공합니다.

| 필드 | 데이터 형식 | Description |
| --- | --- | --- |
| BackupItemUniqueId_s |텍스트 |DPM, MABS를 사용하여 백업된 VM의 백업 항목을 식별하는 데 사용되는 고유 ID|
| OldestRecoveryPointTime_s |텍스트 |백업 항목에 대한 가장 오래된 복구 지점의 날짜 시간|
| OldestRecoveryPointLocation_s |텍스트 |백업 항목에 대한 가장 오래된 복구 지점의 위치|
| LatestRecoveryPointTime_s |텍스트 |백업 항목에 대한 최신 복구 지점의 날짜 시간|
| LatestRecoveryPointLocation_s |텍스트 |백업 항목에 대한 최신 복구 지점의 위치|

## <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

다음은 Azure Diagnostics 테이블에 있는 Azure Backup 데이터에 대한 쿼리를 작성하는 데 도움이 되는 몇 가지 샘플입니다.

- 모든 성공한 백업 작업

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- 모든 실패한 백업 작업

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- 모든 성공한 Azure VM 백업 작업

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

- 모든 성공한 SQL 로그 백업 작업

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

- 모든 성공한 Azure Backup 에이전트 작업

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

## <a name="v1-schema-vs-v2-schema"></a>V1 스키마 및 V2 스키마

이전에는 Azure Backup 에이전트 및 Azure VM 백업용 진단 데이터가 ***V1 스키마***라는 스키마의 Azure Diagnostics 테이블로 전송되었습니다. 이후에는 새 열이 추가되어 다른 시나리오 및 워크로드를 지원하고, 진단 데이터를 ***V2 스키마***라는 새 스키마에 푸시했습니다.  

이전 버전과의 호환성을 위해 Azure Backup 에이전트 및 Azure VM 백업에 대한 진단 데이터는 현재 V1 및 V2 스키마 모두의 Azure Diagnostics 테이블로 전송됩니다(현재는 사용 중단 경로의 V1 스키마 사용). 로그 쿼리에서 SchemaVersion_s=="V1"에 대한 레코드를 필터링하여 Log Analytics에서 V1 스키마의 레코드를 식별할 수 있습니다.

위에 설명된 [데이터 모델](#using-azure-backup-data-model)의 세 번째 열 '설명'을 참조하여 V1 스키마에만 속하는 열을 식별합니다.

### <a name="modifying-your-queries-to-use-the-v2-schema"></a>V2 스키마를 사용 하도록 쿼리 수정

V1 스키마는 사용 중단 경로에 있으므로 Azure Backup 진단 데이터의 모든 사용자 지정 쿼리에서 V2 스키마만 사용 하는 것이 좋습니다. 다음은 V1 스키마에 대 한 종속성을 제거 하도록 쿼리를 업데이트 하는 방법의 예입니다.

1. 쿼리가 V1 스키마에만 적용 되는 필드를 사용 하 고 있는지 확인 합니다. 다음과 같이 모든 백업 항목과 연결 된 보호 된 서버를 나열 하는 쿼리가 있다고 가정 합니다.

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | distinct BackupItemUniqueId_s, ProtectedServerUniqueId_s
    ````

    위의 쿼리는 V1 스키마에만 적용 되는 필드 ProtectedServerUniqueId_s를 사용 합니다. 이 필드에 해당 하는 V2 스키마가 ProtectedContainerUniqueId_s 됩니다 (위의 표 참조). BackupItemUniqueId_s 필드는 V2 스키마에도 적용 될 수 있으며이 쿼리에서는 동일한 필드를 사용할 수 있습니다.

2. V2 스키마 필드 이름을 사용 하도록 쿼리를 업데이트 합니다. 모든 쿼리에서 **SchemaVersion_s = = "v2"** 와 같이 필터를 사용 하 여 V2 스키마에 해당 하는 레코드만 쿼리로 구문 분석 되도록 하는 것이 좋습니다.

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | where SchemaVersion_s=="V2"
    | distinct BackupItemUniqueId_s, ProtectedContainerUniqueId_s
    ````

## <a name="next-steps"></a>다음 단계

데이터 모델을 검토한 후에는 Azure Monitor 로그에서 [사용자 지정 쿼리 만들기](../azure-monitor/learn/tutorial-logs-dashboards.md)를 시작하여 사용자 고유의 대시보드를 작성할 수 있습니다.
