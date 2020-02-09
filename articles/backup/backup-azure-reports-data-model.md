---
title: Power BI 데이터 모델
description: 이 문서는 Azure Backup 보고서에 대한 Power BI 데이터 모델 정보에 대해 설명합니다.
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: a2f06da16280070448d7b42dc5e1dcfc46354cfa
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172797"
---
# <a name="data-model-for-azure-backup-reports"></a>Azure Backup 보고서용 데이터 모델

이 문서에서는 Azure Backup 보고서를 만드는 데 사용되는 Power BI 데이터 모델을 설명합니다. 이 데이터 모델을 사용하여 관련 필드를 기반으로 하는 기존 보고서를 필터링할 수 있으며 무엇보다도 모델의 테이블 및 필드를 사용하여 사용자 고유의 보고서를 만들 수 있습니다.

## <a name="creating-new-reports-in-power-bi"></a>Power BI에서 새 보고서 만들기

Power BI는 [데이터 모델을 사용하여 보고서를 만들](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/) 수 있는 사용자 지정 기능을 제공합니다.

## <a name="using-azure-backup-data-model"></a>Azure Backup 데이터 모델 사용

데이터 모델의 일부로 제공된 다음 필드를 사용하여 보고서를 만들고 기존 보고서를 사용자 지정할 수 있습니다.

### <a name="alert"></a>경고

이 표에서는 다양 한 경고 관련 필드에 대한 기본 필드 및 집계를 제공 합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| #AlertsCreatedInPeriod |정수 |선택한 기간에서 만든 알림 수 |
| %ActiveAlertsCreatedInPeriod |백분율 |선택한 기간의 활성 경고 비율 |
| %CriticalAlertsCreatedInPeriod |백분율 |선택한 기간의 중요한 경고 비율 |
| AlertOccurrenceDate |Date |경고를 만든 날짜 |
| AlertSeverity |텍스트 |경고의 심각도입니다. 예: 위험 |
| AlertStatus |텍스트 |경고의 상태입니다. 예: 활성 |
| AlertType |텍스트 |생성 된 경고의 유형입니다. 예: Backup |
| AlertUniqueId |텍스트 |생성 된 경고의 고유 ID |
| AsOnDateTime |Date/Time |선택한 행에 대한 최신 새로 고침 시간 |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |10진수 |선택한 기간 동안 경고를 해결하는 평균 시간(분) |
| EntityState |텍스트 |경고 개체의 현재 상태입니다. 예: 활성, 삭제 됨 |

### <a name="backup-item"></a>Backup 항목

이 표에서는 다양한 백업 항목 관련 필드를 통해 기본 필드 및 집계를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| #BackupItems |정수 |백업 항목의 수 |
| #UnprotectedBackupItems |정수 |보호가 중지되었거나 백업이 구성되었지만 백업이 시작하지 않은 백업 항목의 수|
| AsOnDateTime |Date/Time |선택한 행에 대한 최신 새로 고침 시간 |
| BackupItemFriendlyName |텍스트 |백업 항목의 친숙한 이름 |
| BackupItemId |텍스트 |백업 항목의 ID |
| BackupItemName |텍스트 |백업 항목의 이름 |
| BackupItemType |텍스트 |백업 항목의 유형입니다. 예: VM, FileFolder |
| EntityState |텍스트 |백업 항목 개체의 현재 상태입니다. 예: 활성, 삭제 됨 |
| LastBackupDateTime |Date/Time |선택한 백업 항목에 대한 마지막 백업 시간 |
| LastBackupState |텍스트 |선택한 백업 항목에 대한 마지막 백업의 상태입니다. 예: 성공, 실패 |
| LastSuccessfulBackupDateTime |Date/Time |선택한 백업 항목에 대한 마지막으로 성공한 백업 시간 |
| ProtectionState |텍스트 |백업 항목의 현재 보호 상태입니다. 예: Protected, Protectionstopped) |

### <a name="calendar"></a>달력

이 표에서는 일정 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| Date |Date |데이터 필터링에 대해 선택한 날짜 |
| DateKey |텍스트 |각 날짜 항목에 대한 고유 키 |
| DayDiff |10진수 |데이터 필터링의 일별 차이입니다. 예를 들어 0은 현재 날짜의 데이터를 나타내고,-1은 이전 1 일의 데이터를 나타내고, 0은 현재 날짜와 이전 날짜에 대한 데이터를 나타냅니다.  |
| Month |텍스트 |데이터 필터링에 선택한 연도의 월, 첫째 날짜에 시작하고 31일에 끝나는 월 |
| MonthDate | Date |데이터 필터링에 대해 선택한 월이 끝날 때 월의 날짜 |
| MonthDiff |10진수 |데이터 필터링에 대한 월간 차이입니다. 예를 들어 0은 현재 달의 데이터를 나타내고,-1은 이전 월의 데이터를 나타내고, 0 및-1은 현재와 이전 달의 데이터를 나타냅니다. |
| 주 |텍스트 |데이터 필터링에 대해 선택한 주, 일요일에 시작하고 토요일에 끝나는 주 |
| WeekDate |Date |데이터 필터링에 대해 선택한 주가 끝날 때 주의 날짜 |
| WeekDiff |10진수 |데이터 필터링의 주 차이입니다. 예를 들어 0은 현재 주 데이터를 나타내고,-1은 이전 주 데이터를 나타내고, 0 및-1은 현재 및 이전 주에 대한 데이터를 나타냅니다. |
| Year |텍스트 |데이터 필터링에 대해 선택한 연도 |
| YearDate |Date |데이터 필터링에 대해 선택한 연도가 끝날 때 연도의 날짜 |

### <a name="job"></a>작업

이 표에서는 다양한 작업 관련 필드를 통해 기본 필드 및 집계를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| #JobsCreatedInPeriod |정수 |선택한 기간에서 만든 작업 수 |
| %FailuresForJobsCreatedInPeriod |백분율 |선택한 기간의 전체 작업 오류 백분율 |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |10진수 |선택한 기간에서 만든 **백업** 작업에 대해 MB로 전송된 데이터의 80번째 백분위수 값 |
| AsOnDateTime |Date/Time |선택한 행에 대한 최신 새로 고침 시간 |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |10진수 |선택한 기간에서 만든 **완료된 백업** 작업에 대한 평균 시간(분) |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |10진수 |선택한 기간에서 만든 **완료된 복원** 작업에 대한 평균 시간(분) |
| BackupStorageDestination |텍스트 |백업 저장소의 대상입니다. 예: 클라우드, 디스크  |
| EntityState |텍스트 |작업 개체의 현재 상태입니다. 예: 활성, 삭제 됨 |
| JobFailureCode |텍스트 |발생한 작업 실패로 인한 오류 코드 문자열 |
| JobOperation |텍스트 |작업이 실행 되는 작업입니다. 예: 백업, 복원, 백업 구성 |
| JobStartDate |Date |작업 실행이 시작된 날짜 |
| JobStartTime |Time |작업 실행이 시작된 시간 |
| JobStatus |텍스트 |완료 된 작업의 상태입니다. 예: Completed, Failed |
| JobUniqueId |텍스트 |작업을 식별 하기 위한 고유 ID |

### <a name="policy"></a>정책

이 표에서는 다양한 정책 관련 필드를 통해 기본 필드 및 집계를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| #Policies |정수 |시스템에 있는 백업 정책 수 |
| #PoliciesInUse |정수 |백업 구성에 현재 사용되고 있는 정책 수 |
| AsOnDateTime |Date/Time |선택한 행에 대한 최신 새로 고침 시간 |
| BackupDaysOfTheWeek |텍스트 |백업이 예약된 주의 요일 |
| BackupFrequency |텍스트 |백업이 실행 되는 빈도입니다. 예: 매일, 매주 |
| BackupTimes |텍스트 |백업이 예약된 날짜 및 시간 |
| DailyRetentionDuration |정수 |구성된 백업에 대한 총 보존 기간(일) |
| DailyRetentionTimes |텍스트 |매일 보존이 구성된 날짜 및 시간 |
| EntityState |텍스트 |정책 개체의 현재 상태입니다. 예: 활성, 삭제 됨 |
| MonthlyRetentionDaysOfTheMonth |텍스트 |매월 보존에 대해 선택한 월의 날짜 |
| MonthlyRetentionDaysOfTheWeek |텍스트 |매월 보존에 대해 선택한 주의 요일 |
| MonthlyRetentionDuration |10진수 |구성된 백업에 대한 총 보존 기간(월) |
| MonthlyRetentionFormat |텍스트 |월별 보존에 대한 구성 유형입니다. 예를 들어 매일, 매주 기준으로 매주 |
| MonthlyRetentionTimes |텍스트 |매월 보존이 구성된 날짜 및 시간 |
| MonthlyRetentionWeeksOfTheMonth |텍스트 |매월 보존이 구성 된 월의 주입니다. 예를 들어, First, Last 등입니다. |
| PolicyName |텍스트 |지정된 정책의 이름 |
| PolicyUniqueId |텍스트 |정책을 식별 하는 고유 ID |
| RetentionType |텍스트 |보존 정책의 유형입니다. 예: 매일, 매주, 매월, 매년 |
| WeeklyRetentionDaysOfTheWeek |텍스트 |매주 보존에 대해 선택한 주의 요일 |
| WeeklyRetentionDuration |10진수 |구성된 백업에 대한 총 매주 보존 기간 |
| WeeklyRetentionTimes |텍스트 |매주 보존이 구성된 날짜 및 시간 |
| YearlyRetentionDaysOfTheMonth |텍스트 |매년 보존에 대해 선택한 월의 날짜 |
| YearlyRetentionDaysOfTheWeek |텍스트 |매년 보존에 대해 선택한 주의 요일 |
| YearlyRetentionDuration |10진수 |구성된 백업에 대한 총 보존 기간(연) |
| YearlyRetentionFormat |텍스트 |매년 보존에 대한 구성 유형입니다. 예를 들어 매일, 매주 기준으로 매주 |
| YearlyRetentionMonthsOfTheYear |텍스트 |매년 보존에 대해 선택한 연도의 달 |
| YearlyRetentionTimes |텍스트 |매년 보존이 구성된 날짜 및 시간 |
| YearlyRetentionWeeksOfTheMonth |텍스트 |매년 보존이 구성 된 월의 주입니다. 예를 들어, First, Last 등입니다. |

### <a name="protected-server"></a>보호된 서버

이 표에서는 다양한 보호된 서버 관련 필드를 통해 기본 필드 및 집계를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| #ProtectedServers |정수 |보호된 서버의 수 |
| AsOnDateTime |Date/Time |선택한 행에 대한 최신 새로 고침 시간 |
| AzureBackupAgentOSType |텍스트 |Azure Backup 에이전트의 OS 유형 |
| AzureBackupAgentOSVersion |텍스트 |Azure Backup 에이전트의 OS 버전 |
| AzureBackupAgentUpdateDate |텍스트 |Azure Backup 에이전트가 업데이트된 날짜 |
| AzureBackupAgentVersion |텍스트 |Azure Backup 에이전트의 버전 번호 |
| BackupManagementType |텍스트 |백업을 수행 하기 위한 공급자 유형입니다. 예: 예 iaasvm, FileFolder |
| EntityState |텍스트 |보호 된 서버 개체의 현재 상태입니다. 예: 활성, 삭제 됨 |
| ProtectedServerFriendlyName |텍스트 |보호된 서버의 친숙한 이름 |
| ProtectedServerName |텍스트 |보호된 서버의 이름 |
| ProtectedServerType |텍스트 |백업 된 보호 된 서버 유형입니다. 예: IaaSVMContainer |
| ProtectedServerName |텍스트 |백업 항목이 속한 보호된 서버의 이름 |
| RegisteredContainerId |텍스트 |백업용으로 등록 된 컨테이너의 ID입니다. |

### <a name="storage"></a>스토리지

이 표에서는 다양한 스토리지 관련 필드를 통해 기본 필드 및 집계를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| #ProtectedInstances |10진수 |선택된 시간에 최신 값에 따라 계산된, 청구에서 프런트 엔드 스토리지 계산에 사용된 보호된 인스턴스 수 |
| AsOnDateTime |Date/Time |선택한 행에 대한 최신 새로 고침 시간 |
| CloudStorageInMB |10진수 |선택된 시간에 최신 값에 따라 계산된, 백업에 사용된 클라우드 백업 스토리지 |
| EntityState |텍스트 |개체의 현재 상태입니다. 예: 활성, 삭제 됨 |
| LastUpdatedDate |Date |선택된 행이 마지막으로 업데이트된 날짜 |

### <a name="time"></a>Time

이 표에서는 시간 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| Hour |Time |하루 중 시간입니다. 예: 오후 1:00:00 |
| HourNumber |10진수 |날짜의 시간입니다. 예: 13.00 |
| Minute |10진수 |시간의 분 |
| PeriodOfTheDay |텍스트 |날짜의 기간 슬롯입니다. 예: 오전 12-3 |
| Time |Time |시간입니다. 예: 오전 12:00:01 |
| TimeKey |텍스트 |시간을 나타내는 키 값 |

### <a name="vault"></a>Vault

이 표에서는 다양한 자격 증명 모음 관련 필드를 통해 기본 필드 및 집계를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| #Vaults |정수 |자격 증명 모음의 수 |
| AsOnDateTime |Date/Time |선택한 행에 대한 최신 새로 고침 시간 |
| AzureDataCenter |텍스트 |자격 증명 모음이 위치한 데이터 센터 |
| EntityState |텍스트 |자격 증명 모음 개체의 현재 상태입니다. 예: 활성, 삭제 됨 |
| StorageReplicationType |텍스트 |자격 증명 모음에 대한 저장소 복제 유형입니다. 예: GeoRedundant |
| SubscriptionId |텍스트 |보고서를 생성 하기 위해 선택한 고객의 구독 ID |
| VaultName |텍스트 |자격 증명 모음의 이름 |
| VaultTags |텍스트 |자격 증명 모음에 연결된 태그 |

## <a name="next-steps"></a>다음 단계

Azure Backup 보고서를 만들기 위해 데이터 모델을 검토한 후 Power BI에서 보고서 만들기 및 보기에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Power BI에서 보고서 만들기](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Power BI에서 보고서 필터링](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
