---
title: Azure Monitor는 Azure Backup에 대 한 데이터 모델 로그
description: Azure Backup 데이터에 대 한 데이터 모델 세부 정보를 기록 하는 Azure Monitor에이 문서에서는 설명 합니다.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: 801516ae2cfad891098c16f8cd6e9a4c7f157a93
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342007"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Azure Backup 데이터용 Log Analytics 데이터 모델

Log Analytics에서 사용자 지정 경고를 만들려면 Log Analytics 데이터 모델을 사용 합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Azure Backup 데이터 모델 사용

데이터 모델의 일부로 제공되는 다음 필드를 사용하여 요구 사항에 따라 시각적 개체, 사용자 지정 쿼리 및 대시보드를 만들 수 있습니다.

### <a name="alert"></a>경고

이 표에서는 경고 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| AlertUniqueId_s |텍스트 |생성된 경고의 고유 식별자 |
| AlertType_s |텍스트 |경고의 형식(예: Backup) |
| AlertStatus_s |텍스트 |경고의 상태(예: Active) |
| AlertOccurrenceDateTime_s |날짜/시간 |경고를 만든 날짜 및 시간 |
| AlertSeverity_s |텍스트 |경고의 심각도(예: Critical) |
|AlertTimeToResolveInMinutes_s    | Number        |경고를 해결 하는 데 걸린 시간입니다. 활성 경고에 대해 비어 있습니다.         |
|AlertConsolidationStatus_s   |텍스트         |경고 통합 된 경고를 인지 하는 경우를 식별 합니다.         |
|CountOfAlertsConsolidated_s     |Number         |경고를 통합된 하는 경우를 통합 하는 경고 수          |
|AlertRaisedOn_s     |텍스트         |경고에서 발생 하는 엔터티 형식         |
|AlertCode_s     |텍스트         |경고 유형을 고유 하 게 식별 하는 코드         |
|RecommendedAction_s   |텍스트         |경고를 해결 하려면 권장 작업         |
| EventName_s |텍스트 |이벤트의 이름입니다. 항상 AzureBackupCentralReport임 |
| BackupItemUniqueId_s |텍스트 |경고와 관련된 백업 항목의 고유 식별자 |
| SchemaVersion_s |텍스트 |예를 들어 스키마의 현재 버전 **V2** |
| State_s |텍스트 |경고 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 경고가 속한 FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(예: Alert) |
| Category |텍스트 |Azure Monitor 로그에 푸시된 진단 데이터의 범주입니다. 항상 AzureBackupReport임 |
| Resource |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedContainerUniqueId_s |텍스트 |경고 (V1의 ProtectedServerUniqueId_s 되었습니다)와 관련 된 보호 된 서버의 고유 식별자|
| VaultUniqueId_s |텍스트 |경고와 관련된 보호된 자격 증명 모음의 고유 식별자 |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스의 고유 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="backupitem"></a>BackupItem

이 표에서는 백업 항목 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이벤트의 이름입니다. 항상 AzureBackupCentralReport임 |  
| BackupItemUniqueId_s |텍스트 |백업 항목의 고유 식별자 |
| BackupItemId_s |텍스트 |(V1 스키마에 대해서만이 필드는) 백업 항목의 식별자 |
| BackupItemName_s |텍스트 |백업 항목의 이름 |
| BackupItemFriendlyName_s |텍스트 |백업 항목의 친숙한 이름 |
| BackupItemType_s |텍스트 |백업 항목의 형식(예: VM, FileFolder) |
| BackupItemProtectionState_s |텍스트 |백업 항목의 보호 상태 |
| BackupItemAppVersion_s |텍스트 |백업 항목의 응용 프로그램 버전 |
| ProtectionState_s |텍스트 |백업 항목의 현재 보호 상태(예: Protected, ProtectionStopped) |
| ProtectionGroupName_s |텍스트 | 보호 그룹 백업 항목의 이름은 보호에서는 SC DPM 및 MABS에 해당 하는 경우|
| SecondaryBackupProtectionState_s |텍스트 |백업 항목에 대 한 보조 보호 사용 여부|
| SchemaVersion_s |텍스트 |예를 들어 스키마의 버전 **V2** |
| State_s |텍스트 |백업 항목 개체의 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 백업 항목이 속한 FileFolder) |
| OperationName |텍스트 |작업의 이름(예: BackupItem) |
| Category |텍스트 |Azure Monitor 로그에 푸시된 진단 데이터의 범주입니다. 항상 AzureBackupReport임 |
| Resource |텍스트 |데이터가 수집되는 리소스(예: Recovery Services 자격 증명 모음 이름) |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 ID(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |수집되는 데이터에 대한 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |수집되는 데이터에 대한 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |수집되는 데이터에 대한 리소스 종류(예: Vaults) |

### <a name="backupitemassociation"></a>BackupItemAssociation

이 표에서는 다양한 엔터티와의 백업 항목 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |  
| BackupItemUniqueId_s |텍스트 |백업 항목의 고유 ID |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V2** |
| State_s |텍스트 |백업 항목 연결 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| BackupItemSourceSize_s |텍스트 | 백업 항목의 프런트 엔드 크기 |
| BackupManagementServerUniqueId_s |텍스트 | 해당 하는 경우 백업 항목을 통해 보호 되는 백업 관리 서버를 고유 하 게 식별 하는 필드 |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| OperationName |텍스트 |현재 작업의 이름(BackupItemAssociation)을 나타냅니다. |
| Resource |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedContainerUniqueId_s |텍스트 |백업 항목 (V1의 ProtectedServerUniqueId_s 되었습니다)와 연결 된 보호 된 서버의 고유 식별자 |
| VaultUniqueId_s |텍스트 |백업 항목이 포함된 자격 증명 모음의 고유 식별자 |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |수집되는 데이터에 대한 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |수집되는 데이터에 대한 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |수집되는 데이터에 대한 리소스 종류(예: Vaults) |

### <a name="backupmanagementserver"></a>BackupManagementServer

이 표에서는 다양한 엔터티와의 백업 항목 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
|BackupManagementServerName_s     |텍스트         |백업 관리 서버 이름        |
|AzureBackupAgentVersion_s     |텍스트         |백업 관리 서버에 Azure Backup 에이전트의 버전          |
|BackupManagementServerVersion_s     |텍스트         |백업 관리 서버 버전|
|BackupManagementServerOSVersion_s    |텍스트            |백업 관리 서버의 운영 체제 버전|
|BackupManagementServerType_s     |텍스트         |백업 관리 서버에서 MABS, SC DPM의 형식|
|BackupManagementServerUniqueId_s     |텍스트         |Backup 관리 서버를 고유 하 게 식별 하는 필드       |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |수집되는 데이터에 대한 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |수집되는 데이터에 대한 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |수집되는 데이터에 대한 리소스 종류(예: Vaults) |

### <a name="job"></a>작업

이 표에서는 작업 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이벤트의 이름입니다. 항상 AzureBackupCentralReport임 |
| BackupItemUniqueId_s |텍스트 |백업 항목의 고유 식별자 |
| SchemaVersion_s |텍스트 |예를 들어 스키마의 버전 **V2** |
| State_s |텍스트 |작업 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(Job)을 나타냅니다. |
| Category |텍스트 |Azure Monitor 로그에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| Resource |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedServerUniqueId_s |텍스트 |작업과 관련된 보호된 서버의 고유 식별자 |
| ProtectedContainerUniqueId_s |텍스트 | 작업에서 실행 되는 보호 되는 컨테이너를 식별 하는 고유 Id |
| VaultUniqueId_s |텍스트 |보호된 자격 증명 모음의 고유 식별자 |
| JobOperation_s |텍스트 |실행되는 작업에 대한 동작(예: Backup, 복원, Backup 구성) |
| JobStatus_s |텍스트 |완료된 작업의 상태(예: Completed, Failed) |
| JobFailureCode_s |텍스트 |발생한 작업 실패로 인한 오류 코드 문자열 |
| JobStartDateTime_s |날짜/시간 |작업 실행이 시작된 날짜 및 시간 |
| BackupStorageDestination_s |텍스트 |백업 저장소의 대상(예: Cloud, Disk)  |
| AdHocOrScheduledJob_s |텍스트 | 임시 또는 예약 된 작업 인지를 지정 하는 필드 |
| JobDurationInSecs_s | Number |총 작업 기간(초) |
| DataTransferredInMB_s | Number |이 작업에 대해 전송되는 데이터 크기(MB 단위)|
| JobUniqueId_g |텍스트 |작업을 식별하는 고유 ID |
| RecoveryJobDestination_s |텍스트 | 데이터를 복구 하는 복구 작업의 대상 |
| RecoveryJobRPDateTime_s |DateTime | 날짜를 복구 하려는 복구 지점을 만든 시간 |
| RecoveryJobRPLocation_s |텍스트 | 복구 중인 복구 지점이 저장 된 위치|
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID)|
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="policy"></a>정책

이 표에서는 정책 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 적용 가능한 버전 | 설명 |
| --- | --- | --- | --- |
| EventName_s |텍스트 ||이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 ||스키마의 현재 버전을 나타내며, **V2** |
| State_s |텍스트 ||정책 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 ||백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 ||현재 작업의 이름(Policy)을 나타냅니다. |
| Category |텍스트 ||Azure Monitor 로그에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| Resource |텍스트 ||데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
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
| MonthlyRetentionWeeksOfTheMonth_s |텍스트 ||월별 보존을 구성한 경우 월의 주(예: First, Last 등) |
| YearlyRetentionDuration_s |10진수 ||구성된 백업에 대한 총 보존 기간(연) |
| YearlyRetentionTimes_s |텍스트 ||매년 보존이 구성된 날짜 및 시간 |
| YearlyRetentionMonthsOfTheYear_s |텍스트 ||매년 보존에 대해 선택한 연도의 달 |
| YearlyRetentionFormat_s |텍스트 ||연별 보존에 대한 구성 형식(예: 일 기준 매일, 주 기준 매주) | |
| YearlyRetentionDaysOfTheMonth_s |텍스트 ||매년 보존에 대해 선택한 월의 날짜 |
| SynchronisationFrequencyPerDay_s |정수 |v2|파일 백업 SC DPM 및 MABS에 대 한 동기화는 하루에 한 번의 수 |
| DiffBackupFormat_s |텍스트 |v2|Azure VM 백업에서 SQL에 대 한 차등 백업에 대 한 형식 |
| DiffBackupTime_s |Time |v2|SQL Azure VM 백업에 대 한 차등 백업에 대 한 시간|
| DiffBackupRetentionDuration_s |10진수 |v2|SQL Azure VM 백업에 대 한 차등 백업에 대 한 보존 기간|
| LogBackupFrequency_s |10진수 |v2|Sql 로그 백업 위한 빈도|
| LogBackupRetentionDuration_s |10진수 |v2|SQL Azure VM 백업에 대 한 로그 백업에 대 한 보존 기간|
| DiffBackupDaysofTheWeek_s |텍스트 |v2|SQL Azure VM 백업에 대 한 차등 백업에 대 한 요일|
| SourceSystem |텍스트 ||현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 ||수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 ||데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 ||데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 ||데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 ||데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="policyassociation"></a>PolicyAssociation

이 표에서는 다양한 엔터티와의 정책 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 적용 가능한 버전 | 설명 |
| --- | --- | --- | --- |
| EventName_s |텍스트 ||이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 ||스키마의 현재 버전을 나타내며, **V2** |
| State_s |텍스트 ||정책 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 ||백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 ||현재 작업의 이름(PolicyAssociation)을 나타냅니다. |
| Category |텍스트 ||Azure Monitor 로그에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| Resource |텍스트 ||데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| PolicyUniqueId_g |텍스트 ||정책을 식별하는 고유 ID |
| VaultUniqueId_s |텍스트 ||이 정책이 속한 자격 증명 모음의 고유 ID |
| BackupManagementServerUniqueId_s |텍스트 |v2 |해당 하는 경우 백업 항목을 통해 보호 되는 백업 관리 서버를 고유 하 게 식별 하는 필드        |
| SourceSystem |텍스트 ||현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 ||수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 ||데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 ||데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 ||데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 ||데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="protected-container"></a>보호 컨테이너

이 표에서 보호 컨테이너에 대 한 기본 필드를 제공 합니다. (V1 ProtectedServer 시점의)

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |텍스트 | 보호 컨테이너를 고유 하 게 식별 하는 필드 |
| ProtectedContainerOSType_s |텍스트 |보호 컨테이너의 OS 유형 |
| ProtectedContainerOSVersion_s |텍스트 |보호 되는 컨테이너의 OS 버전 |
| AgentVersion_s |텍스트 |백업 에이전트 또는 SC DPM 및 MABS) (의 보호 에이전트의 버전 번호 |
| BackupManagementType_s |텍스트 |백업 수행에 대 한 공급자 유형입니다. 예: IaaSVM, FileFolder |
| EntityState_s |텍스트 |보호 된 서버 개체의 현재 상태입니다. 예를 들어, 활성, 삭제 됨 |
| ProtectedContainerFriendlyName_s |텍스트 |보호된 서버의 친숙한 이름 |
| ProtectedContainerName_s |텍스트 |보호 컨테이너의 이름 |
| ProtectedContainerWorkloadType_s |텍스트 |백업 보호 컨테이너의 형식입니다. 예를 들어 IaaSVMContainer |
| ProtectedContainerLocation_s |텍스트 |보호 컨테이너를 온-프레미스 인지 또는 Azure에서 |
| ProtectedContainerType_s |텍스트 |보호 컨테이너를 컨테이너 또는 서버 인지 |
| ProtectedContainerProtectionState_s’  |텍스트 |보호 되는 컨테이너의 보호 상태 |

### <a name="storage"></a>Storage

이 표에서는 저장소 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| CloudStorageInBytes_s |10진수 |(V1 스키마에 대해서만이 필드는) 최신 값을 기반으로 하는 계산 된, 백업에서 사용 하는 클라우드 백업 저장소|
| ProtectedInstances_s |10진수 |청구에서 프런트 엔드 저장소 계산에 사용된 보호된 인스턴스 수이며, 최신 값을 기준으로 하여 계산됩니다. |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V2** |
| State_s |텍스트 |저장소 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(Storage)을 나타냅니다. |
| Category |텍스트 |Azure Monitor 로그에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| Resource |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedServerUniqueId_s |텍스트 |저장소가 계산되는 보호된 서버의 고유 ID |
| VaultUniqueId_s |텍스트 |저장소가 계산되는 자격 증명 모음의 고유 ID |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |
| StorageUniqueId_s |텍스트 |저장소는 엔터티를 식별 하는 데 사용 되는 고유 Id |
| StorageType_s |텍스트 |예를 들어 클라우드, 볼륨, 디스크 저장소 유형으로 |
| StorageName_s |텍스트 |예를 들어 E:\ 저장소 엔터티의 이름 |
| StorageTotalSizeInGBs_s |텍스트 |저장소 엔터티에 의해 사용 된 gb에서 저장소의 총 크기|

### <a name="storageassociation"></a>StorageAssociation

이 테이블 저장소를 다른 엔터티에 연결할 기본 저장소 관련 필드를 제공 합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- |  --- |
| StorageUniqueId_s |텍스트 |저장소는 엔터티를 식별 하는 데 사용 되는 고유 Id |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V2** |
| BackupItemUniqueId_s |텍스트 |저장소 엔터티와 관련 된 백업 항목을 식별 하는 데 사용 되는 고유 Id |
| BackupManagementServerUniqueId_s |텍스트 |저장소 엔터티와 관련 된 백업 관리 서버를 식별 하는 데 사용 되는 고유 Id|
| VaultUniqueId_s |텍스트 |엔터티와 관련 된 저장소 자격 증명 모음을 식별 하는 데 사용 되는 고유 Id|
| StorageConsumedInMBs_s |Number|해당 저장소에서 해당 백업 항목에서 사용 된 저장소의 크기 |
| StorageAllocatedInMBs_s |Number |디스크 형식의 해당 저장소에서 해당 백업 항목에 의해 할당 된 저장소의 크기|

### <a name="vault"></a>Vault

이 표에서는 자격 증명 모음 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V2** |
| State_s |텍스트 |자격 증명 모음 개체의 현재 상태(예: Active, Deleted) |
| OperationName |텍스트 |현재 작업의 이름(Vault)을 나타냅니다. |
| Category |텍스트 |Azure Monitor 로그에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| Resource |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| VaultUniqueId_s |텍스트 |자격 증명 모음의 고유 ID |
| VaultName_s |텍스트 |자격 증명 모음의 이름 |
| AzureDataCenter_s |텍스트 |자격 증명 모음이 위치한 데이터 센터 |
| StorageReplicationType_s |텍스트 |자격 증명 모음에 대한 저장소 복제 유형(예: GeoRedundant) |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="backup-management-server"></a>백업 관리 서버

이 표에서 백업 관리 서버에 대 한 기본 필드를 제공 합니다.

|필드  |데이터 형식  | 설명  |
|---------|---------|----------|
|BackupManagmentServerName_s     |텍스트         |백업 관리 서버 이름        |
|AzureBackupAgentVersion_s     |텍스트         |백업 관리 서버에 Azure Backup 에이전트의 버전          |
|BackupManagmentServerVersion_s     |텍스트         |백업 관리 서버 버전|
|BackupManagmentServerOSVersion_s     |텍스트            |백업 관리 서버의 운영 체제 버전|
|BackupManagementServerType_s     |텍스트         |백업 관리 서버에서 MABS, SC DPM의 형식|
|BackupManagmentServerUniqueId_s     |텍스트         |Backup 관리 서버를 고유 하 게 식별 하는 필드       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

이 표에서 볼륨와 연결 된 작업을 지정 합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| StorageUniqueId_s |텍스트 |저장소는 엔터티를 식별 하는 데 사용 되는 고유 Id |
| BackupItemType_s |텍스트 |이 볼륨은 기본 저장소를 워크 로드|

### <a name="protectedinstance"></a>ProtectedInstance

이 표에서 기본 보호 된 인스턴스가 관련 필드를 제공 합니다.

| 필드 | 데이터 형식 |적용 가능한 버전 | 설명 |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |텍스트 |v2|DPM에서 MABS를 사용 하 여 Vm에 대 한 백업 항목을 식별 하는 데 사용 되는 고유 Id를 백업|
| ProtectedContainerUniqueId_s |텍스트 |v2|DPM에서 MABS를 사용 하 여 Vm을 제외한 모든 항목에 대 한 보호 되는 컨테이너를 식별 하는 데 사용 되는 고유 Id를 백업|
| ProtectedInstanceCount_s |텍스트 |v2|Count의 보호 된 인스턴스는 연결 된 백업 항목 또는 해당 날짜 및 시간에 보호 되는 컨테이너에 대 한|

### <a name="recoverypoint"></a>RecoveryPoint

다음이 표에 기본 복구 관련된 필드를 가리킵니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| BackupItemUniqueId_s |텍스트 |DPM에서 MABS를 사용 하 여 Vm에 대 한 백업 항목을 식별 하는 데 사용 되는 고유 Id를 백업|
| OldestRecoveryPointTime_s |텍스트 |백업 항목에 대 한 가장 오래 된 복구 지점의 날짜 시간|
| OldestRecoveryPointLocation_s |텍스트 |백업 항목에 대 한 가장 오래 된 복구 지점의 위치|
| LatestRecoveryPointTime_s |텍스트 |백업 항목에 대 한 최신 복구 지점의 날짜 시간|
| LatestRecoveryPointLocation_s |텍스트 |백업 항목에 대 한 최신 복구 지점의 위치|

## <a name="next-steps"></a>다음 단계

데이터 모델을 검토 한 후 시작할 수 있습니다 [사용자 지정 쿼리를 만드는](../azure-monitor/learn/tutorial-logs-dashboards.md) 자신의 대시보드를 빌드하는 Azure Monitor 로그에 있습니다.
