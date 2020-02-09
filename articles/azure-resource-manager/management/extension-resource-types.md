---
title: 확장 리소스 종류
description: 다른 리소스 유형의 기능을 확장 하는 데 사용 되는 Azure 리소스 유형을 나열 합니다.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c14cf6fec2da11534a7358599c79961a3258470b
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515368"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>다른 리소스의 기능을 확장 하는 리소스 종류

확장 리소스는 다른 리소스의 기능에 추가 되는 리소스입니다. 예를 들어 리소스 잠금은 확장 리소스입니다. 리소스 잠금을 삭제 하거나 수정 하지 못하도록 다른 리소스에 적용 합니다. 리소스 잠금을 직접 만드는 것은 적절 하지 않습니다. 확장 리소스는 항상 다른 리소스에 적용 됩니다.

## <a name="extension-resource-types"></a>확장 리소스 종류

- Microsoft Advisor/구성
- Microsoft Advisor/권장 사항
- Microsoft Advisor/비 표시 오류
- AlertsManagement/경고
- AlertsManagement/alertsSummary
- Microsoft. Authorization/checkAccess
- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/findOrphanRoleAssignments
- Microsoft. 권한 부여/잠금
- Microsoft. 권한 부여/사용 권한
- Microsoft. 권한 부여/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. 권한 부여/역할 할당
- Microsoft. Authorization/roleAssignmentsUsageMetrics
- Microsoft. Authorization/roleDefinitions
- Microsoft. 청구/billingPeriods
- Microsoft. 청구/billingPermissions
- Microsoft. 청구/billingRoleAssignments
- Microsoft. 청구/billingRoleDefinitions
- Microsoft. 청구/createBillingRoleAssignment
- Microsoft. 청사진/blueprintAssignments
- Microsoft. 청사진/청사진
- Microsoft 소비량/AggregatedCost
- Microsoft 소비량/잔액
- Microsoft 소비량/예산
- Microsoft 소비량/요금
- Microsoft 소비량/CostTags
- Microsoft의 소비/예측
- Microsoft 소비량/마켓플레이스
- Microsoft의 소비율/OperationResults
- Microsoft 사용/OperationStatus
- Microsoft 소비량/Pricesheets
- Microsoft 소비량/ReservationDetails
- Microsoft 소비량/ReservationRecommendations
- Microsoft 소비량/ReservationSummaries
- Microsoft 소비량/ReservationTransactions
- Microsoft. 소비/태그
- Microsoft 사용량과 사용 약관
- Microsoft 소비량/사용량 세부 정보
- Microsoft 소비량/크레딧
- Microsoft 사용량과 이벤트
- Microsoft 사용량과 많은
- Microsoft. 소비/제품
- Microsoft 소비/테넌트
- ContainerInstance/serviceAssociationLinks
- CostManagement/경고
- CostManagement/예산
- CostManagement/차원
- CostManagement/내보내기
- CostManagement/ExternalSubscriptions
- CostManagement/예측
- CostManagement/쿼리
- CostManagement/Reportconfigs
- CostManagement/보고서
- CostManagement/뷰
- CostManagement/showbackRules
- Microsoft.CustomProviders/associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- GuestConfiguration/configurationprofil
- GuestConfiguration/guestConfigurationAssignments
- GuestConfiguration/소프트웨어
- GuestConfiguration/Microsoft 프로필
- GuestConfiguration/\\D 업데이트
- microsoft insights/automatedExportSettings
- microsoft. a s e/기준
- microsoft insights/calculatebaseline
- microsoft insights/dataCollectionRuleAssociations
- microsoft insights/diagnosticSettings
- microsoft insights/diagnosticSettingsCategories
- microsoft insights/eventtypes
- microsoft insights/extendedDiagnosticSettings
- microsoft insights/찾은 guestdiagnosticsettingsassociation
- microsoft insights/logDefinitions
- microsoft 인 사이트/로그
- microsoft insights/metricDefinitions
- microsoft insights/metricNamespaces
- microsoft insights/metricbaselines
- microsoft. 통찰력/메트릭
- microsoft insights/myWorkbooks 문서
- microsoft. a s e/토폴로지
- microsoft. a s e/트랜잭션
- microsoft insights/vmInsightsOnboardingStatuses
- KubernetesConfiguration/sourceControlConfigurations
- Microsoft. Maintenance/applyUpdates
- Microsoft. 유지 관리/configurationAssignments
- Microsoft. 유지 관리/업데이트
- Microsoft.managedidentity/Id
- Microsoft ManagedServices/registrationAssignments
- Microsoft ManagedServices/registrationDefinitions
- OperationalInsights/storageInsightConfigs
- Microsoft.operationsmanagement/managementassociations
- Microsoft PolicyInsights/Policyinsights
- Microsoft PolicyInsights/Policyinsights
- Microsoft PolicyInsights/policyTrackedResources
- Microsoft PolicyInsights/재구성
- Microsoft RecoveryServices/backupProtectedItems
- Microsoft RecoveryServices/replicationEligibilityResults  
- Microsoft ResourceHealth/availabilityStatuses
- Microsoft ResourceHealth/childAvailabilityStatuses
- Microsoft ResourceHealth/childResources
- Microsoft ResourceHealth/이벤트
- Microsoft ResourceHealth/impactedResources
- Microsoft ResourceHealth/알림
- Microsoft .Resources/링크
- Microsoft .Resources/태그
- Microsoft. Security/규격
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. 보안/평가
- Microsoft. Security/complianceResults
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/dataCollectionResults
- Microsoft. Security/deviceSecurityGroups
- Microsoft. 보안/네트워크 데이터
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft SecurityInsights/집계
- Microsoft SecurityInsights/alertRuleTemplates
- Microsoft SecurityInsights/alertRules
- Microsoft SecurityInsights/책갈피
- Microsoft SecurityInsights/사례
- Microsoft SecurityInsights/dataConnectors
- Microsoft SecurityInsights/엔터티
- Microsoft SecurityInsights/entityQueries
- Microsoft SecurityInsights/officeConsents
- Microsoft SecurityInsights/설정
- SoftwarePlan/hybridUseBenefits
- Microsoft. Subscription/CreateSubscription
- microsoft. support/createsupportticket
- microsoft. 지원/지원 티켓
- WorkloadMonitor/구성 요소
- WorkloadMonitor/monitorInstances
- WorkloadMonitor/모니터
- WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager 템플릿에서 확장 리소스의 리소스 ID를 가져오려면 [Extensionresourceid](../templates/template-functions-resource.md#extensionresourceid)를 사용 합니다.
- 템플릿에 확장 리소스를 만드는 방법에 대 한 예는 [Event Grid 이벤트 구독](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)을 참조 하십시오.
