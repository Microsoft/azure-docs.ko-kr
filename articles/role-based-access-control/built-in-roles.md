---
title: Azure 리소스에 대한 기본 제공 역할 | Microsoft Docs
description: Azure 리소스 및 RBAC(역할 기반 액세스 제어)에 대한 기본 제공 역할에 대해 설명합니다. Actions, NotActions, DataActions 및 NotDataActions를 나열합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 5a63053cc7fa1c1c86669ce2cea56b68f1a7b4b6
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341498"
---
# <a name="built-in-roles-for-azure-resources"></a>Azure 리소스에 대한 기본 제공 역할

[RBAC(역할 기반 액세스 제어)](overview.md)에는 사용자, 그룹, 서비스 주체 및 관리 ID에 할당할 수 있는 Azure 리소스에 대한 여러 기본 제공 역할이 있습니다. 역할 할당은 Azure 리소스에 대한 액세스를 제어하는 방법입니다. 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [Azure 리소스에 대한 사용자 지정 역할](custom-roles.md)을 만들면 됩니다.

이 문서에는 계속 업데이트되는 Azure 리소스에 대한 기본 제공 역할이 나와 있습니다. 최신 역할을 가져오려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 또는 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용합니다. Azure Active Directory의 관리자 역할을 찾고 있는 경우 [Azure Active Directory의 관리자 역할 권한](../active-directory/users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.

## <a name="built-in-role-descriptions"></a>기본 제공 역할 설명

다음 표에서는 각 기본 제공 역할에 대한 간략한 설명을 제공합니다. 각 역할의 `Actions`, `NotActions`, `DataActions` 및 `NotDataActions` 목록을 보려면 역할 이름을 클릭합니다. 이러한 동작의 의미와 동작이 관리 및 데이터 평면에 적용되는 방식에 대한 자세한 내용은 [Azure 리소스에 대한 역할 정의 이해](role-definitions.md)를 참조하세요.


| 기본 제공 역할 | 설명 |
| --- | --- |
| [소유자](#owner) | 리소스 액세스를 비롯한 모든 것을 관리할 수 있습니다. |
| [기여자](#contributor) | 리소스 액세스를 제외한 모든 것을 관리할 수 있습니다. |
| [판독기](#reader) | 모든 것을 볼 수 있지만 변경은 할 수 없습니다. |
| [AcrDelete](#acrdelete) | acr 삭제 |
| [AcrImageSigner](#acrimagesigner) | acr 이미지 서명자 |
| [AcrPull](#acrpull) | acr pull |
| [AcrPush](#acrpush) | acr push |
| [AcrQuarantineReader](#acrquarantinereader) | acr 격리 데이터 판독기 |
| [AcrQuarantineWriter](#acrquarantinewriter) | acr 격리 데이터 작성자 |
| [API Management 서비스 참가자](#api-management-service-contributor) | 서비스 및 API를 관리할 수 있습니다. |
| [API Management 서비스 운영자 역할](#api-management-service-operator-role) | 서비스를 관리할 수 있지만 API는 관리할 수 없습니다. |
| [Azure API Management 읽기 권한자 역할](#api-management-service-reader-role) | 서비스 및 API에 대한 읽기 전용 액세스 |
| [Application Insights 구성 요소 기여자](#application-insights-component-contributor) | Application Insights 구성 요소를 관리할 수 있음 |
| [Application Insights 스냅숏 디버거](#application-insights-snapshot-debugger) | Application Insights 스냅샷 디버거를 사용하여 수집한 디버그 스냅샷을 보고 다운로드할 수 있는 사용자 권한을 제공합니다. 이러한 사용 권한은 [소유자](#owner) 또는 [기여자](#contributor) 역할에 포함되지 않습니다. |
| [Automation 작업 연산자](#automation-job-operator) | Automation Runbook을 사용하여 작업을 만들고 관리합니다. |
| [Automation 운영자](#automation-operator) | 자동화 연산자는 작업을 시작, 중지, 일시 중단 및 다시 시작할 수 있습니다. |
| [Automation Runbook 연산자](#automation-runbook-operator) | Runbook 작업을 만들려면 Runbook 속성을 읽어보세요. |
| [Avere 참가자](#avere-contributor) | 수 만들고 Avere vFXT 클러스터를 관리 합니다. |
| [Avere 연산자](#avere-operator) | Avere vFXT 클러스터에서 클러스터 관리에 사용 |
| [Azure Kubernetes Service 클러스터 관리자 역할](#azure-kubernetes-service-cluster-admin-role) | 클러스터 관리자 자격 증명 작업을 나열합니다. |
| [Azure Kubernetes Service 클러스터 사용자 역할](#azure-kubernetes-service-cluster-user-role) | 클러스터 사용자 자격 증명 작업을 나열합니다. |
| [Azure Maps 데이터 판독기 (미리 보기)](#azure-maps-data-reader-preview) | Azure 지도 계정에서 지도 관련 데이터를 읽을 수 있는 액세스 권한을 부여합니다. |
| [Azure Stack 등록 소유자](#azure-stack-registration-owner) | Azure Stack 등록을 관리할 수 있습니다. |
| [Backup 기여자](#backup-contributor) | 백업 서비스를 관리할 수 있지만, 자격 증명 모음을 만들고 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. |
| [Backup 운영자](#backup-operator) | 백업 제거를 제외한 백업 서비스를 관리하고 자격 증명 모음 만들고 다른 사람에게 액세스 권한을 부여할 수 있습니다. |
| [Backup 읽기 권한자](#backup-reader) | 백업 서비스를 볼 수 있지만 변경할 수는 없습니다. |
| [청구 읽기 권한자](#billing-reader) | 결제 데이터에 대해 읽기 권한 허용 |
| [BizTalk 기여자](#biztalk-contributor) | BizTalk Services를 관리할 수 있지만 액세스할 수는 없습니다. |
| [블록 체인 멤버 노드 액세스 (미리 보기)](#blockchain-member-node-access-preview) | 블록체인 멤버 노드에 대한 권한 허용 |
| [CDN 엔드포인트 기여자](#cdn-endpoint-contributor) | CDN 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. |
| [CDN 엔드포인트 독자](#cdn-endpoint-reader) | CDN 엔드포인트를 볼 수 있지만 변경할 수는 없습니다. |
| [CDN 프로필 기여자](#cdn-profile-contributor) | CDN 프로필과 해당 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. |
| [CDN 프로필 독자](#cdn-profile-reader) | CDN 프로필과 해당 엔드포인트를 볼 수 있지만 변경할 수는 없습니다. |
| [클래식 네트워크 기여자](#classic-network-contributor) | 기본 네트워크를 관리할 수 있지만 액세스할 수는 없습니다. |
| [클래식 Storage 계정 기여자](#classic-storage-account-contributor) | 클래식 Storage 계정을 관리할 수 있지만 여기에 액세스할 수는 없습니다. |
| [클래식 저장소 계정 키 운영자 서비스 역할](#classic-storage-account-key-operator-service-role) | 클래식 저장소 계정 키 운영자가 클래식 저장소 계정에서 키를 나열하고 다시 생성할 수 있습니다. |
| [Classic Virtual Machine 참가자](#classic-virtual-machine-contributor) | 클래식 가상 머신을 관리할 수 있지만 가상 머신이나 연결된 가상 네트워크 또는 저장소 계정에 액세스할 수는 없습니다. |
| [Cognitive Services 기여자](#cognitive-services-contributor) | Cognitive Services의 키를 만들고, 읽고, 업데이트하고, 삭제 및 관리할 수 있습니다. |
| [Cognitive Services 데이터 판독기 (미리 보기)](#cognitive-services-data-reader-preview) | Cognitive Services 데이터를 읽을 수 있습니다. |
| [Cognitive Services 사용자](#cognitive-services-user) | Cognitive Services의 키를 읽고 나열할 수 있습니다. |
| [Cosmos DB 계정 독자 역할](#cosmos-db-account-reader-role) | Azure Cosmos DB 계정 데이터를 읽을 수 있음. Azure Cosmos DB 계정 관리는 [DocumentDB 계정 참가자](#documentdb-account-contributor)를 참조하세요. |
| [Cosmos DB 연산자](#cosmos-db-operator) | Azure Cosmos DB 계정은 하지만에 데이터를 액세스를 관리할 수 있습니다. 계정 키 및 연결 문자열에 대 한 액세스를 차단합니다. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Cosmos DB 데이터베이스 또는 계정의 컨테이너에 대한 복원 요청을 제출할 수 있습니다. |
| [Cost Management 기여자](#cost-management-contributor) | 비용을 확인하고 비용 구성(예: 예산, 내보내기)을 관리할 수 있음 |
| [Cost Management 읽기 권한자](#cost-management-reader) | 비용 데이터 및 구성(예: 예산, 내보내기)을 확인할 수 있음 |
| [Data Box 기여자](#data-box-contributor) | 다른 사람에게 액세스 권한을 부여하는 것을 제외한 모든 항목을 Data Box 서비스에서 관리할 수 있습니다. |
| [Data Box 읽기 권한자](#data-box-reader) | 주문하기나 주문 세부 정보 편집 및 다른 사용자에게 액세스 권한 부여 외에 Data Box 서비스를 관리할 수 있습니다. |
| [Data Factory 참가자](#data-factory-contributor) | 데이터 팩터리를 만들고 관리하며 해당 하위 리소스도 만들고 관리합니다. |
| [Data Lake Analytics 개발자](#data-lake-analytics-developer) | 사용자 자신의 작업을 제출, 모니터링 및 관리할 수 있지만 Data Lake Analytics 계정을 만들거나 삭제할 수는 없습니다. |
| [데이터 제거자](#data-purger) | 분석 데이터를 제거할 수 있습니다. |
| [DevTest Lab 사용자](#devtest-labs-user) | Azure DevTest Labs의 가상 머신을 연결, 시작, 다시 시작 및 종료할 수 있습니다. |
| [DNS 영역 기여자](#dns-zone-contributor) | Azure DNS의 DNS 영역과 레코드 집합을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다. |
| [DocumentDB 계정 기여자](#documentdb-account-contributor) | Azure Cosmos DB 계정을 관리할 수 있습니다. Azure Cosmos DB는 이전의 DocumentDB입니다. |
| [Event Hubs 데이터 소유자](#event-hubs-data-owner) | Azure Event Hubs 리소스에 대 한 전체 액세스를 허용합니다. | 
| [EventGrid EventSubscription 기여자](#eventgrid-eventsubscription-contributor) | EventGrid 이벤트 구독 작업을 관리할 수 있습니다. |
| [EventGrid EventSubscription 읽기 권한자](#eventgrid-eventsubscription-reader) | EventGrid 이벤트 구독을 읽을 수 있습니다. |
| [HDInsight 클러스터 연산자](#hdinsight-cluster-operator) | 읽기 및 HDInsight 클러스터 구성을 수정할 수 있습니다. |
| [HDInsight 도메인 서비스 기여자](#hdinsight-domain-services-contributor) | HDInsight Enterprise Security Package에 필요한 도메인 서비스 관련 작업을 읽고, 만들고, 수정하고, 삭제할 수 있음 |
| [지능형 시스템 계정 기여자](#intelligent-systems-account-contributor) | 인텔리전트 시스템 계정을 관리할 수 있지만 액세스할 수는 없습니다. |
| [키 자격 증명 모음 기여자](#key-vault-contributor) | 키 자격 증명 모음을 관리할 수 있지만 액세스할 수는 없습니다. |
| [랩 작성자](#lab-creator) | Azure Lab 계정 하에서 관리 랩을 만들고, 관리하고, 삭제할 수 있습니다. |
| [Log Analytics 기여자](#log-analytics-contributor) | Log Analytics 기여자는 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 스토리지 계정 키 읽기, Automation 계정 생성 및 구성, 솔루션 추가 및 모든 Azure 리소스에 대한 Azure 진단을 구성하는 기능도 포함되어 있습니다. |
| [Log Analytics 독자](#log-analytics-reader) | Log Analytics 독자는 모든 Azure 리소스에 대한 Azure 진단의 구성 보기를 비롯하여 모니터링 설정 보기 및 모든 모니터링 데이터를 보고 검색할 수 있습니다. |
| [논리 앱 기여자](#logic-app-contributor) | 있습니다 logic apps를 관리 하지만에 대 한 액세스를 변경 하지. |
| [논리 앱 운영자](#logic-app-operator) | 읽기, 사용 및 logic apps를 사용 하지 않도록 설정 하지만 하지 편집 또는 업데이트할 수 있습니다. |
| [관리되는 애플리케이션 운영자 역할](#managed-application-operator-role) | 관리되는 애플리케이션 리소스에서 작업을 읽고 수행할 수 있습니다. |
| [Managed Applications 읽기 권한자](#managed-applications-reader) | 관리 앱 및 요청 JIT 액세스에서 리소스를 읽을 수 있습니다. |
| [관리 ID 기여자](#managed-identity-contributor) | 사용자 할당 ID를 만들고, 읽고, 업데이트하고, 삭제합니다. |
| [관리 ID 운영자](#managed-identity-operator) | 사용자 할당 ID를 읽고 할당합니다. |
| [관리 그룹 참가자](#management-group-contributor) | 관리 그룹 참가자 역할 |
| [관리 그룹 읽기 권한자](#management-group-reader) | 관리 그룹 읽기 권한자 역할 |
| [Monitoring Contributor](#monitoring-contributor) | 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있음 [Azure Monitor에서의 역할, 권한 및 보안 시작](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요. |
| [모니터링 메트릭 게시자](#monitoring-metrics-publisher) | Azure 리소스에 대한 게시 메트릭 사용 |
| [Monitoring Reader](#monitoring-reader) | 모든 모니터링 데이터를 읽을 수 있음(메트릭, 로그 등) [Azure Monitor에서의 역할, 권한 및 보안 시작](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요. |
| [네트워크 기여자](#network-contributor) | 네트워크를 관리할 수 있지만 액세스할 수는 없습니다. |
| [NewRelic APM 계정 기여자](#new-relic-apm-account-contributor) | New Relic Application Performance Management 계정 및 애플리케이션을 관리할 수 있지만 액세스할 수는 없습니다. |
| [읽기 권한자 및 데이터 액세스](#reader-and-data-access) | 모든 것을 볼 수 있지만, 저장소 계정 또는 포함된 리소스를 삭제하거나 만들 수는 없습니다. 또한 저장소 계정 키에 액세스하여 저장소 계정에 포함된 모든 데이터를 읽고 쓸 수 있습니다. |
| [Redis Cache 참가자](#redis-cache-contributor) | Redis Cache를 관리할 수 있지만 액세스할 수는 없습니다. |
| [리소스 정책 참가자(미리 보기)](#resource-policy-contributor-preview) | (미리 보기) 리소스 정책을 생성/수정하고, 지원 티켓을 만들고, 리소스/계층 구조를 읽을 수 있는 권한을 가진 EA의 백필된 사용자입니다. |
| [Scheduler 작업 컬렉션 참가자](#scheduler-job-collections-contributor) | Scheduler 작업 컬렉션을 관리할 수 있지만 액세스할 수는 없습니다. |
| [Search 서비스 기여자](#search-service-contributor) | Search 서비스를 관리할 수 있지만 액세스할 수는 없습니다. |
| [보안 관리자](#security-admin) | Security Center에서만: 보안 정책 보기, 보안 상태 보기, 보안 정책 편집, 경고 및 권장 사항 보기, 경고 및 권장 사항 해제를 수행합니다. |
| [보안 관리자(레거시)](#security-manager-legacy) | 레거시 역할입니다. 보안 관리자를 대신 사용하세요. |
| [보안 판독기](#security-reader) | Security Center에서만: 권장 사항 및 경고를 보고, 보안 정책을 보고, 보안 상태를 볼 수 있지만 변경할 수는 없습니다. |
| [Service Bus 데이터 소유자](#service-bus-data-owner) | Azure Service Bus 리소스에 대 한 전체 액세스 허용 |
| [Site Recovery 기여자](#site-recovery-contributor) | 자격 증명 모음 만들기 및 역할 할당을 제외한 Site Recovery 서비스를 관리할 수 있습니다. |
| [Site Recovery 운영자](#site-recovery-operator) | 장애 조치(failover) 및 장애 복구(failback)를 수행할 수 있지만 다른 Site Recovery 관리 작업은 수행할 수 없습니다. |
| [Site Recovery 구독자](#site-recovery-reader) | Site Recovery 상태를 볼 수 있지만 다른 관리 작업은 수행할 수 없습니다. |
| [공간 앵커 계정 기여자](#spatial-anchors-account-contributor) | 계정에서 Spatial Anchors를 관리할 수 있지만 삭제할 수는 없습니다. |
| [공간 앵커 계정 소유자](#spatial-anchors-account-owner) | 계정에서 Spatial Anchors를 관리할 수 있고 삭제도 할 수 있습니다. |
| [공간 앵커 계정 판독기](#spatial-anchors-account-reader) | 계정에서 Spatial Anchors의 속성을 찾고 읽을 수 있습니다. |
| [SQL DB 기여자](#sql-db-contributor) | SQL 데이터베이스를 관리할 수 있지만 액세스할 수는 없습니다. 또한 보안 관련 정책이나 부모 SQL 서버를 관리할 수 없습니다. |
| [SQL 관리 인스턴스에 참가자](#sql-managed-instance-contributor) | SQL 관리 되는 인스턴스를 관리 하는 데 필요한 수 있습니다 네트워크 구성에 있지만 다른 사용자에 게 액세스할 수 없습니다. |
| [SQL 보안 관리자](#sql-security-manager) | SQL Server 및 데이터베이스의 보안과 관련된 정책을 관리할 수 있지만 여기에 액세스할 수는 없습니다. |
| [SQL Server 기여자](#sql-server-contributor) | SQL Server 및 데이터베이스를 관리할 수 있지만 여기에 액세스할 수는 없으며, 해당하는 보안과 관련된 정책에도 액세스할 수 없습니다. |
| [Storage 계정 기여자](#storage-account-contributor) | Storage 계정을 관리할 수 있지만 여기에 액세스할 수는 없습니다. |
| [저장소 계정 키 운영자 서비스 역할](#storage-account-key-operator-service-role) | 저장소 계정 키 운영자가 저장소 계정에서 키를 나열하고 다시 생성할 수 있습니다. |
| [Storage Blob 데이터 기여자](#storage-blob-data-contributor) | Azure Storage Blob 컨테이너 및 데이터에 대한 읽기, 쓰기 및 삭제 액세스를 허용합니다. |
| [Storage Blob 데이터 소유자](#storage-blob-data-owner) | POSIX 액세스 제어 할당을 포함하여 Azure Storage Blob 컨테이너 및 데이터에 대한 모든 액세스 권한을 허용합니다. |
| [Storage Blob 데이터 읽기 권한자](#storage-blob-data-reader) | Azure Storage Blob 컨테이너 및 데이터에 대한 읽기 액세스를 허용합니다. |
| [Storage 큐 데이터 기여자](#storage-queue-data-contributor) | Azure Storage 큐 및 큐 메시지에 대한 읽기, 쓰기 및 삭제 액세스를 허용합니다. |
| [Storage 큐 데이터 메시지 처리기](#storage-queue-data-message-processor) | Azure Storage 큐 메시지에 대한 미리 보기, 수신 및 삭제 권한 허용 |
| [Storage 큐 데이터 메시지 보낸 사람](#storage-queue-data-message-sender) | Azure Storage 큐 메시지 보내기 허용 |
| [Storage 큐 데이터 판독기](#storage-queue-data-reader) | Azure Storage 큐 및 큐 메시지에 대한 읽기 액세스를 허용합니다. |
| [지원 요청 참가자](#support-request-contributor) | 지원 요청을 만들고 관리할 수 있습니다. |
| [Traffic Manager 기여자](#traffic-manager-contributor) | Traffic Manager 프로필을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다. |
| [사용자 액세스 관리자](#user-access-administrator) | Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다. |
| [가상 머신 관리자 로그인](#virtual-machine-administrator-login) | 포털에서 Virtual Machines를 보고 관리자 권한으로 로그인합니다. |
| [Virtual Machine 참가자](#virtual-machine-contributor) | 가상 머신을 관리할 수 있지만 가상머신이나 연결된 가상 네트워크 또는 저장소 계정에 액세스할 수는 없습니다. |
| [가상 머신 사용자 로그인](#virtual-machine-user-login) | 포털에서 Virtual Machines를 보고 일반 사용자 권한으로 로그인합니다. |
| [웹 계획 참가자](#web-plan-contributor) | 웹 사이트의 웹 계획을 관리할 수 있지만 액세스할 수는 없습니다. |
| [웹 사이트 기여자](#website-contributor) | 웹 사이트(웹 계획은 제외)를 관리할 수 있지만 액세스할 수는 없습니다. |


## <a name="owner"></a>소유자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 리소스 액세스를 비롯한 모든 것을 관리할 수 있습니다. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **actions** |  |
> | * | 모든 유형의 리소스 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="contributor"></a>참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 리소스 액세스를 제외한 모든 것을 관리할 수 있습니다. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **actions** |  |
> | * | 모든 유형의 리소스 만들기 및 관리 |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | 역할 및 역할 할당 삭제 |
> | Microsoft.Authorization/*/Write | 역할 및 역할 할당 만들기 |
> | Microsoft.Authorization/elevateAccess/Action | 테넌트 범위에서 호출자에게 사용자 액세스 관리자 액세스 권한을 부여합니다. |
> | Microsoft.Blueprint/blueprintAssignments/write | 청사진 아티팩트 만들기 또는 업데이트 |
> | Microsoft.Blueprint/blueprintAssignments/delete | 청사진 아티팩트 삭제 |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="reader"></a>판독기
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 모든 것을 볼 수 있지만 변경은 할 수 없습니다. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **actions** |  |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | acr 삭제 |
> | **Id** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **actions** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | 컨테이너 레지스트리에 있는 아티팩트를 삭제 합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | acr 이미지 서명자 |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **actions** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | 컨테이너 레지스트리에 대한 콘텐츠 신뢰 메타데이터를 푸시/풀합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | acr pull |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **actions** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | 컨테이너 레지스트리에서 이미지를 끌어오거나 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | acr push |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **actions** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | 컨테이너 레지스트리에서 이미지를 끌어오거나 가져옵니다. |
> | Microsoft.ContainerRegistry/registries/push/write | 컨테이너 레지스트리에 이미지를 푸시하거나 작성합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | acr 격리 데이터 읽기 권한자 |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **actions** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | 컨테이너 레지스트리에서 격리된 이미지를 끌어오거나 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | acr 격리 데이터 작성자 |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **actions** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | 컨테이너 레지스트리에서 격리된 이미지를 끌어오거나 가져옵니다. |
> | Microsoft.ContainerRegistry/registries/quarantineWrite/write | 격리된 이미지의 격리 상태를 작성/수정합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="api-management-service-contributor"></a>API Management 서비스 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 서비스 및 API를 관리할 수 있습니다. |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **actions** |  |
> | Microsoft.ApiManagement/service/* | API Management 서비스 만들기 및 관리 |
> | Microsoft.Authorization/*/read | 읽기 권한 부여 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="api-management-service-operator-role"></a>API Management 서비스 운영자 역할
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 서비스를 관리할 수 있지만 API는 관리할 수 없습니다. |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **actions** |  |
> | Microsoft.ApiManagement/service/*/read | API Management 서비스 인스턴스 읽기 |
> | Microsoft.ApiManagement/service/backup/action | 사용자가 제공한 저장소 계정의 지정된 컨테이너로 API Management 서비스를 백업합니다. |
> | Microsoft.ApiManagement/service/delete | API Management 서비스 인스턴스를 삭제합니다. |
> | Microsoft.ApiManagement/service/managedeployments/action | SKU/단위를 변경하고 API Management 서비스의 지역별 배포를 추가 또는 제거합니다. |
> | Microsoft.ApiManagement/service/read | API Management 서비스 인스턴스에 대한 메타데이터 읽기 |
> | Microsoft.ApiManagement/service/restore/action | 사용자가 제공한 저장소 계정의 지정된 컨테이너에서 API Management 서비스 복원 |
> | Microsoft.ApiManagement/service/updatecertificate/action | API Management 서비스에 대한 SSL 인증서를 업로드합니다. |
> | Microsoft.ApiManagement/service/updatehostname/action | API Management 서비스에 대한 사용자 지정 도메인 이름 설정, 업데이트 또는 제거합니다. |
> | Microsoft.ApiManagement/service/write | API Management 서비스의 새 인스턴스 만들기 |
> | Microsoft.Authorization/*/read | 읽기 권한 부여 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | 사용자와 연결 된 키를 가져옵니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="api-management-service-reader-role"></a>Azure API Management 읽기 권한자 역할
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 서비스 및 API에 읽기 전용 액세스 |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **actions** |  |
> | Microsoft.ApiManagement/service/*/read | API Management 서비스 인스턴스 읽기 |
> | Microsoft.ApiManagement/service/read | API Management 서비스 인스턴스에 대한 메타데이터 읽기 |
> | Microsoft.Authorization/*/read | 읽기 권한 부여 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | 사용자와 연결 된 키를 가져옵니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="application-insights-component-contributor"></a>Application Insights 구성 요소 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Application Insights 구성 요소를 관리할 수 있습니다. |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.Insights/components/* | Insights 구성 요소 만들기 및 관리 |
> | Microsoft.Insights/webtests/* | 웹 테스트 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights 스냅샷 디버거
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Application Insights 스냅샷 디버거를 사용하여 수집한 디버그 스냅샷을 보고 다운로드할 수 있는 사용자 권한을 제공합니다. 이러한 사용 권한은 [소유자](#owner) 또는 [기여자](#contributor) 역할에 포함되지 않습니다. |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="automation-job-operator"></a>Automation 작업 연산자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Automation Runbook을 사용하여 작업을 만들고 관리합니다. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker 리소스를 읽습니다. |
> | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation 작업을 가져옵니다. |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation 작업을 계속합니다. |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation 작업을 중지합니다. |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation 작업 스트림을 가져옵니다. |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation 작업을 일시 중단합니다. |
> | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation 작업을 만듭니다. |
> | Microsoft.Automation/automationAccounts/jobs/output/read | 작업의 출력을 가져옵니다. |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="automation-operator"></a>Automation 운영자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Automation 운영자는 작업을 시작, 중지, 일시 중단 및 다시 시작할 수 있습니다. |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker 리소스를 읽습니다. |
> | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation 작업을 가져옵니다. |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation 작업을 계속합니다. |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation 작업을 중지합니다. |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation 작업 스트림을 가져옵니다. |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation 작업을 일시 중단합니다. |
> | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation 작업을 만듭니다. |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation 작업 일정을 가져옵니다. |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Azure Automation 작업 일정을 만듭니다. |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | 자동화 계정에 연결된 작업 영역을 가져옵니다. |
> | Microsoft.Automation/automationAccounts/read | Azure Automation 계정을 가져옵니다. |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook을 가져옵니다. |
> | Microsoft.Automation/automationAccounts/schedules/read | Azure Automation 일정 자산을 가져옵니다. |
> | Microsoft.Automation/automationAccounts/schedules/write | Azure Automation 일정 자산을 만들거나 업데이트합니다. |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Automation/automationAccounts/jobs/output/read | 작업의 출력을 가져옵니다. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="automation-runbook-operator"></a>Automation Runbook 연산자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Runbook 작업을 만들려면 Runbook 속성을 읽어보세요. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook을 가져옵니다. |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="avere-contributor"></a>Avere 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 수 만들고 Avere vFXT 클러스터를 관리 합니다. |
> | **Id** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | Microsoft.Network/virtualNetworks/subnets/read | 가상 네트워크 서브넷 정의를 가져옵니다. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 저장소 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/networkSecurityGroups/join/action | 네트워크 보안 그룹을 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹에 대한 리소스를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Blob 삭제 결과 반환 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blob 또는 Blob 목록 반환 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob 쓰기 결과 반환 |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="avere-operator"></a>Avere 연산자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Avere vFXT 클러스터에서 클러스터 관리에 사용 |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **actions** |  |
> | Microsoft.Compute/virtualMachines/read | 가상 머신의 속성을 가져옵니다. |
> | Microsoft.Network/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | Microsoft.Network/networkInterfaces/write | 네트워크 인터페이스를 만들거나 기존 네트워크 인터페이스를 업데이트합니다.  |
> | Microsoft.Network/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | Microsoft.Network/virtualNetworks/subnets/read | 가상 네트워크 서브넷 정의를 가져옵니다. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/networkSecurityGroups/join/action | 네트워크 보안 그룹을 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | 컨테이너 삭제 결과를 반환합니다. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | 컨테이너 목록을 반환합니다. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Blob 컨테이너 넣기의 결과를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Blob 삭제 결과 반환 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blob 또는 Blob 목록 반환 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob 쓰기 결과 반환 |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service 클러스터 관리자 역할
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 클러스터 관리자 자격 증명 작업을 나열합니다. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **actions** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 관리되는 클러스터의 clusterAdmin 자격 증명을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service 클러스터 사용자 역할
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 클러스터 사용자 자격 증명 작업을 나열합니다. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **actions** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 관리되는 클러스터의 clusterUser 자격 증명을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="azure-maps-data-reader-preview"></a>Azure Maps 데이터 판독기(미리 보기)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure 지도 계정에서 지도 관련 데이터를 읽을 수 있는 액세스 권한을 부여합니다. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **actions** |  |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Maps/accounts/data/read | Maps 계정에 데이터 읽기 액세스 권한을 부여합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="azure-stack-registration-owner"></a>Azure Stack 등록 소유자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure Stack 등록을 관리할 수 있습니다. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **actions** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Azure Stack Marketplace 제품에 대한 세부 정보 검색 |
> | Microsoft.AzureStack/registrations/products/read | Azure Stack Marketplace 제품의 속성 가져오기 |
> | Microsoft.AzureStack/registrations/read | Azure Stack 등록의 속성 가져오기 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="backup-contributor"></a>Backup 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 백업 서비스를 관리할 수 있지만, 자격 증명 모음을 만들고 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Network/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | 백업 관리에 대한 작업의 결과 관리 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Recovery Services 자격 증명 모음의 백업 패브릭 내에서 백업 컨테이너 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | 컨테이너 목록을 새로 고칩니다. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | 백업 작업 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 작업을 내보냅니다. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | 백업 관리와 관련된 메타데이터 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | 백업 관리 작업의 결과 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | 백업 정책 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | 백업할 수 있는 항목 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | 백업한 항목 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | 백업 항목을 보유하는 컨테이너 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Recovery Services 자격 증명 모음의 백업과 관련된 인증서 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | 자격 증명 모음과 관련된 확장 정보 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 등록된 ID 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/usages/* | Recovery Services 자격 증명 모음 만들기 및 사용 관리 |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Storage/storageAccounts/read | 저장소 계정의 목록을 반환하거나 지정된 저장소 계정의 속성을 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | 보호된 항목에 대한 작업의 유효성을 검사합니다. |
> | Microsoft.RecoveryServices/Vaults/write | 자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | 자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | 보호 가능한 컨테이너를 모두 가져옵니다. |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | 기능의 유효성을 검사합니다. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | 경고를 해결합니다. |
> | Microsoft.RecoveryServices/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | 지정된 작업의 작업 상태를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | 모든 백업 보호 의도를 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="backup-operator"></a>Backup 운영자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 백업 제거를 제외한 백업 서비스를 관리하고 자격 증명 모음 만들고 다른 사람에게 액세스 권한을 부여할 수 있습니다. |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Network/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 작업의 상태를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 보호 컨테이너에 대해 수행된 작업의 결과를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 보호 항목 Backup을 수행합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 보호 항목에 대해 수행된 작업의 결과를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 보호 항목에 대해 수행된 작업의 상태를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 프로비전합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 보호 항목의 복구 지점을 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 보호 항목의 복구 지점을 복원합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 취소합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 백업 보호 항목을 만듭니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 등록된 모든 컨테이너를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | 컨테이너 목록을 새로 고칩니다. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | 백업 작업 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 작업을 내보냅니다. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | 백업 관리 작업의 결과 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 정책 작업의 결과를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | 백업할 수 있는 항목 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 모든 보호 항목 목록을 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 구독에 속하는 컨테이너를 모두 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | 리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | 서비스 컨테이너 등록 작업을 사용하여 복구 서비스와 함께 컨테이너를 등록할 수 있습니다. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Storage/storageAccounts/read | 저장소 계정의 목록을 반환하거나 지정된 저장소 계정의 속성을 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | 보호된 항목에 대한 작업의 유효성을 검사합니다. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | 정책 작업의 상태를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | 등록된 컨테이너를 만듭니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | 컨테이너 내의 워크로드를 조회합니다. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | 자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | 백업 보호 의도 만들기 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | 백업 보호 의도를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | 보호 가능한 컨테이너를 모두 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | 컨테이너의 모든 항목을 가져옵니다. |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | 기능의 유효성을 검사합니다. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | 경고를 해결합니다. |
> | Microsoft.RecoveryServices/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | 지정된 작업의 작업 상태를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | 모든 백업 보호 의도를 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="backup-reader"></a>Backup 읽기 권한자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 백업 서비스를 볼 수 있지만 변경할 수는 없습니다. |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 작업의 상태를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 보호 컨테이너에 대해 수행된 작업의 결과를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 보호 항목에 대해 수행된 작업의 결과를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 보호 항목에 대해 수행된 작업의 상태를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 보호 항목의 복구 지점을 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 등록된 모든 컨테이너를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | 작업의 작업 결과를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | 모든 작업 개체를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 작업을 내보냅니다. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Recovery Services 자격 증명 모음의 Backup 작업 결과를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 정책 작업의 결과를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 모든 보호 항목 목록을 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 구독에 속하는 컨테이너를 모두 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | Microsoft.RecoveryServices/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Recovery Services 자격 증명 모음에 대한 저장소 구성을 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Recovery Services 자격 증명 모음에 구성을 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | 정책 작업의 상태를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | 자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | 백업 보호 의도를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | 컨테이너의 모든 항목을 가져옵니다. |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | 경고를 해결합니다. |
> | Microsoft.RecoveryServices/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | 지정된 작업의 작업 상태를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | 모든 백업 보호 의도를 나열합니다. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="billing-reader"></a>청구 읽기 권한자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 결제 데이터에 대해 읽기 권한 허용 |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Billing/*/read | 대금 청구 정보 읽기 |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="biztalk-contributor"></a>BizTalk 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | BizTalk Services를 관리할 수 있지만 액세스할 수는 없습니다. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk 서비스 만들기 및 관리 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="blockchain-member-node-access-preview"></a>블록체인 멤버 노드 액세스(미리 보기)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 블록체인 멤버 노드에 대한 권한 허용 |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **actions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | 기존 블록 체인 멤버 트랜잭션 노드를 나열 하거나 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | 블록 체인 멤버 트랜잭션 노드에 연결합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cdn-endpoint-contributor"></a>CD 엔드포인트 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | CDN 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cdn-endpoint-reader"></a>CDN 엔드포인트 독자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | CDN 엔드포인트를 볼 수 있지만 변경할 수는 없습니다. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cdn-profile-contributor"></a>CDN 프로필 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | CDN 프로필과 해당 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cdn-profile-reader"></a>CDN 프로필 독자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | CDN 프로필과 해당 엔드포인트를 볼 수 있지만 변경할 수는 없습니다. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="classic-network-contributor"></a>클래식 네트워크 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 기본 네트워크를 관리할 수 있지만 액세스할 수는 없습니다. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 읽기 권한 부여 |
> | Microsoft.ClassicNetwork/* | 클래식 네트워크 만들기 및 관리 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="classic-storage-account-contributor"></a>클래식 Storage 계정 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 클래식 Storage 계정을 관리할 수 있지만 여기에 액세스할 수는 없습니다. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 읽기 권한 부여 |
> | Microsoft.ClassicStorage/storageAccounts/* | 저장소 계정 만들기 및 관리 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>클래식 저장소 계정 키 운영자 서비스 역할
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 클래식 저장소 계정 키 운영자가 클래식 저장소 계정에서 키를 나열하고 다시 생성할 수 있습니다. |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **actions** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | 저장소 계정의 액세스 키를 나열합니다. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | 저장소 계정에 대한 기존 액세스 키를 다시 생성합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="classic-virtual-machine-contributor"></a>클래식 Virtual Machine 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 클래식 가상 머신을 관리할 수 있지만 가상 머신이나 연결된 가상 네트워크 또는 저장소 계정에 액세스할 수는 없습니다. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 읽기 권한 부여 |
> | Microsoft.ClassicCompute/domainNames/* | 클래식 계산 도메인 이름 만들기 및 관리 |
> | Microsoft.ClassicCompute/virtualMachines/* | 가상 머신 만들기 및 관리 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | 예약된 IP를 연결합니다. |
> | Microsoft.ClassicNetwork/reservedIps/read | 예약된 IP를 가져옵니다. |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | 가상 네트워크를 조인합니다. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | 가상 네트워크를 가져옵니다. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | 저장소 계정 디스크를 반환합니다. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | 저장소 계정 이미지를 반환합니다. (사용되지 않음, 대신 ‘Microsoft.ClassicStorage/storageAccounts/vmImages’ 사용) |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 저장소 계정의 액세스 키를 나열합니다. |
> | Microsoft.ClassicStorage/storageAccounts/read | 지정된 계정의 저장소 계정을 반환합니다. |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cognitive-services-contributor"></a>Cognitive Services 기여자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Cognitive Services의 키를 만들고, 읽고, 업데이트하고, 삭제 및 관리할 수 있습니다. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | 구독 기능을 가져옵니다. |
> | Microsoft.Features/providers/features/read | 지정된 리소스 공급자에서 구독의 기능을 가져옵니다. |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Insights/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | Microsoft.Insights/logDefinitions/read | 로그 정의 읽기 |
> | Microsoft.Insights/metricdefinitions/read | 메트릭 정의 읽기 |
> | Microsoft.Insights/metrics/read | 메트릭 읽기 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | Microsoft.Resources/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | Microsoft.Resources/subscriptions/read | 구독 목록을 가져옵니다. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cognitive-services-data-reader-preview"></a>Cognitive Services 데이터 판독기(미리 보기)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Cognitive Services 데이터를 읽을 수 있습니다. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **actions** |  |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cognitive-services-user"></a>Cognitive Services 사용자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Cognitive Services의 키를 읽고 나열할 수 있습니다. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **actions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | 키를 나열합니다. |
> | Microsoft.Insights/alertRules/read | 클래식 메트릭 경고를 읽습니다. |
> | Microsoft.Insights/diagnosticSettings/read | 리소스 진단 설정을 읽습니다. |
> | Microsoft.Insights/logDefinitions/read | 로그 정의 읽기 |
> | Microsoft.Insights/metricdefinitions/read | 메트릭 정의 읽기 |
> | Microsoft.Insights/metrics/read | 메트릭 읽기 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | Microsoft.Resources/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | Microsoft.Resources/subscriptions/read | 구독 목록을 가져옵니다. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB 계정 독자 역할
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure Cosmos DB 계정 데이터를 읽을 수 있습니다. Azure Cosmos DB 계정 관리는 [DocumentDB 계정 참가자](#documentdb-account-contributor)를 참조하세요. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 읽기 역할 및 역할 할당은 각 사용자에게 부여된 사용 권한을 읽을 수 있습니다. |
> | Microsoft.DocumentDB/*/read | 컬렉션 읽기 |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | 데이터베이스 계정 읽기 전용 키를 읽습니다. |
> | Microsoft.Insights/MetricDefinitions/read | 메트릭 정의 읽기 |
> | Microsoft.Insights/Metrics/read | 메트릭 읽기 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cosmos-db-operator"></a>Cosmos DB 연산자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure Cosmos DB 계정은 하지만에 데이터를 액세스를 관리할 수 있습니다. 계정 키 및 연결 문자열에 대 한 액세스를 차단합니다. |
> | **Id** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **actions** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Cosmos DB 데이터베이스 또는 계정의 컨테이너에 대한 복원 요청을 제출할 수 있습니다. |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **actions** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | 백업 구성하는 요청 제출 |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | 복원 요청 제출 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cost-management-contributor"></a>Cost Management 기여자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 비용을 확인하고 비용 구성(예: 예산, 내보내기)을 관리할 수 있음 |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **actions** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | 사용할 수 있는 청구 기간을 나열합니다. |
> | Microsoft.Resources/subscriptions/read | 구독 목록을 가져옵니다. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Advisor/configurations/read | 구성 가져오기 |
> | Microsoft.Advisor/recommendations/read | 권장 사항을 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="cost-management-reader"></a>Cost Management 읽기 권한자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 비용 데이터 및 구성(예: 예산, 내보내기)을 확인할 수 있음 |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **actions** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read | 사용할 수 있는 청구 기간을 나열합니다. |
> | Microsoft.Resources/subscriptions/read | 구독 목록을 가져옵니다. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Advisor/configurations/read | 구성 가져오기 |
> | Microsoft.Advisor/recommendations/read | 권장 사항을 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="data-box-contributor"></a>Data Box 기여자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 다른 사람에게 액세스 권한을 부여하는 것을 제외한 모든 항목을 Data Box 서비스에서 관리할 수 있습니다. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="data-box-reader"></a>Data Box 읽기 권한자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 주문하기나 주문 세부 정보 편집 및 다른 사용자에게 액세스 권한 부여 외에 Data Box 서비스를 관리할 수 있습니다. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | 주문과 관련된 암호화되지 않은 자격 증명을 나열합니다. |
> | Microsoft.Databox/locations/availableSkus/action | 이 메서드는 사용할 수 있는 SKU 목록을 반환합니다. |
> | Microsoft.Databox/locations/validateAddress/action | 배송 주소의 유효성을 검사하고, 있는 경우, 대체 주소를 제공합니다. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="data-factory-contributor"></a>Data Factory 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 데이터 팩터리를 만들고 관리하며 해당 하위 리소스도 만들고 관리합니다. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.DataFactory/dataFactories/* | 데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리합니다. |
> | Microsoft.DataFactory/factories/* | 데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리합니다. |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics 개발자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 사용자 자신의 작업을 제출, 모니터링 및 관리할 수 있지만 Data Lake Analytics 계정을 만들거나 삭제할 수는 없습니다. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | DataLakeAnalytics 계정을 삭제합니다. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 다른 사용자가 제출한 작업을 취소하는 권한을 부여합니다. |
> | Microsoft.DataLakeAnalytics/accounts/Write | DataLakeAnalytics 계정을 만들거나 업데이트합니다. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics 계정과 연결된 DataLakeStore 계정을 만들거나 업데이트합니다. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | DataLakeAnalytics 계정에서 DataLakeStore 계정을 연결 해제합니다. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | DataLakeAnalytics 계정과 연결된 Storage 계정을 만들거나 업데이트합니다. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | DataLakeAnalytics 계정에서 Storage 계정을 연결 해제합니다. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | 방화벽 규칙을 만들거나 업데이트합니다. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | 방화벽 규칙을 삭제합니다. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | 계산 정책을 만들거나 업데이트합니다. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | 계산 정책을 삭제합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="data-purger"></a>데이터 제거자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 분석 데이터를 제거할 수 있습니다. |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Actions** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Application Insights에서 데이터 삭제 |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | 작업 영역에서 지정된 데이터를 삭제합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="devtest-labs-user"></a>DevTest Lab 사용자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure DevTest Labs의 가상 머신을 연결, 시작, 다시 시작 및 종료할 수 있습니다. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Compute/availabilitySets/read | 가용성 집합의 속성을 가져옵니다. |
> | Microsoft.Compute/virtualMachines/*/read | 가상 머신(VM 크기, 런타임 상태, VM 확장 등)의 속성 읽기 |
> | Microsoft.Compute/virtualMachines/deallocate/action | 가상 머신을 끄고 Compute 리소스를 해제합니다. |
> | Microsoft.Compute/virtualMachines/read | 가상 머신의 속성을 가져옵니다. |
> | Microsoft.Compute/virtualMachines/restart/action | 가상 머신을 다시 시작합니다. |
> | Microsoft.Compute/virtualMachines/start/action | 가상 머신을 시작합니다. |
> | Microsoft.DevTestLab/*/read | 랩의 속성 읽기 |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | 랩에서 임의 클레임 가능 가상 머신을 클레임합니다. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | 랩에 가상 머신을 만듭니다. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | 현재 사용자를 랩에 대 한 올바른 프로필을 확인 합니다. |
> | Microsoft.DevTestLab/labs/formulas/delete | 수식을 삭제합니다. |
> | Microsoft.DevTestLab/labs/formulas/read | 수식을 읽습니다. |
> | Microsoft.DevTestLab/labs/formulas/write | 수식을 추가하거나 수정합니다. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | 랩 정책을 평가합니다. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | 기존 가상 머신의 소유권을 가져옵니다. |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | 해당 시작/중지 일정이 있는 경우 이를 나열합니다. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | 가상 머신에 대한 RDP 파일의 콘텐츠를 나타내는 문자열을 가져옵니다. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 부하 분산 장치 백 엔드 주소 풀을 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 부하 분산 장치 인바운드 nat 규칙을 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/networkInterfaces/*/read | 네트워크 인터페이스(예: 네트워크 인터페이스의 일부인 모든 부하 분산 장치)의 속성 읽기 |
> | Microsoft.Network/networkInterfaces/join/action | 네트워크 인터페이스를 가상 컴퓨터를 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | Microsoft.Network/networkInterfaces/write | 네트워크 인터페이스를 만들거나 기존 네트워크 인터페이스를 업데이트합니다.  |
> | Microsoft.Network/publicIPAddresses/*/read | 공용 IP 주소의 속성 읽기 |
> | Microsoft.Network/publicIPAddresses/join/action | 공용 ip 주소를 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Resources/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | Microsoft.Resources/deployments/read | 배포를 가져오거나 나열합니다. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Storage/storageAccounts/listKeys/action | 지정된 저장소 계정에 대한 액세스 키를 반환합니다. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | 가상 머신이 업데이트될 수 있는 사용 가능한 크기를 나열합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="dns-zone-contributor"></a>DNS 영역 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure DNS의 DNS 영역과 레코드 집합을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.Network/dnsZones/* | DNS 영역 및 레코드 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="documentdb-account-contributor"></a>DocumentDB 계정 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure Cosmos DB 계정을 관리할 수 있습니다. Azure Cosmos DB는 이전의 DocumentDB입니다. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB 계정 만들기 및 관리 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="event-hubs-data-owner"></a>Event Hubs 데이터 소유자

> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure Event Hubs 리소스에 대 한 전체 액세스를 허용합니다. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **actions** |  |
> | Microsoft.EventHubs/* | Event Hubs 네임 스페이스에 대 한 완전 한 관리 액세스를 허용합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.EventHubs/* | Event Hubs 네임 스페이스에 대 한 전체 데이터 액세스를 허용합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription 기여자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | EventGrid 이벤트 구독 작업을 관리할 수 있습니다. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | 항목 유형별로 전역 이벤트 구독을 나열합니다. |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | 지역 이벤트 구독을 나열합니다. |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | 항목 유형별로 지역 이벤트 구독을 나열합니다. |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription 읽기 권한자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | EventGrid 이벤트 구독을 읽을 수 있습니다. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.EventGrid/eventSubscriptions/read | EventSubscription을 읽습니다. |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | 항목 유형별로 전역 이벤트 구독을 나열합니다. |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | 지역 이벤트 구독을 나열합니다. |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | 항목 유형별로 지역 이벤트 구독을 나열합니다. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="hdinsight-cluster-operator"></a>HDInsight 클러스터 연산자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 읽기 및 HDInsight 클러스터 구성을 수정할 수 있습니다. |
> | **Id** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **actions** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | HDInsight 클러스터에 대 한 게이트웨이 설정을 가져옵니다. |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | HDInsight 클러스터에 대 한 게이트웨이 설정을 업데이트합니다 |
> | Microsoft.HDInsight/clusters/configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Resources/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="hdinsight-domain-services-contributor"></a>HDInsight 도메인 서비스 기여자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | HDInsight Enterprise Security Package에 필요한 도메인 서비스 관련 작업을 읽고, 만들고, 수정하고, 삭제할 수 있음 |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **actions** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="intelligent-systems-account-contributor"></a>지능형 시스템 계정 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 지능형 시스템 계정을 관리할 수 있지만 액세스할 수는 없습니다. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.IntelligentSystems/accounts/* | 지능형 시스템 계정 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="key-vault-contributor"></a>Key Vault 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Key Vault를 관리할 수 있지만 액세스할 수는 없습니다. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | 일시 삭제된 Key Vault를 제거합니다. |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="lab-creator"></a>랩 작성자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure Lab 계정 하에서 관리 랩을 만들고, 관리하고, 삭제할 수 있습니다. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | 랩 계정에서 랩을 만듭니다. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | 랩 계정에서 구성된 각 크기 범주에 대한 지역별 가용성 정보를 가져옵니다. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="log-analytics-contributor"></a>Log Analytics 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Log Analytics 참가자는 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 스토리지 계정 키 읽기, Automation 계정 생성 및 구성, 솔루션 추가 및 모든 Azure 리소스에 대한 Azure 진단을 구성하는 기능도 포함되어 있습니다. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **actions** |  |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 저장소 계정의 액세스 키를 나열합니다. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Insights/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | 지정된 저장소 계정에 대한 액세스 키를 반환합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="log-analytics-reader"></a>Log Analytics 독자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Log Analytics 독자는 모든 Azure 리소스에 대한 Azure 진단의 구성 보기를 비롯하여 모니터링 설정 보기 및 모든 모니터링 데이터를 보고 검색할 수 있습니다. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **actions** |  |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
> | Microsoft.OperationalInsights/workspaces/search/action | 검색 쿼리를 실행합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="logic-app-contributor"></a>논리 앱 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 있습니다 logic apps를 관리 하지만에 대 한 액세스를 변경 하지. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 저장소 계정의 액세스 키를 나열합니다. |
> | Microsoft.ClassicStorage/storageAccounts/read | 지정된 계정의 저장소 계정을 반환합니다. |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Insights/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | Microsoft.Insights/logdefinitions/* | 이 사용 권한은 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. 활동 로그의 로그 범주를 나열합니다. |
> | Microsoft.Insights/metricDefinitions/* | 메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다. |
> | Microsoft.Logic/* | Logic Apps 리소스를 관리합니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Storage/storageAccounts/listkeys/action | 지정된 저장소 계정에 대한 액세스 키를 반환합니다. |
> | Microsoft.Storage/storageAccounts/read | 저장소 계정의 목록을 반환하거나 지정된 저장소 계정의 속성을 가져옵니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Web/connectionGateways/* | 연결 게이트웨이를 만들고 관리합니다. |
> | Microsoft.Web/connections/* | 연결을 만들고 관리합니다. |
> | Microsoft.Web/customApis/* | 사용자 지정 API를 만들고 관리합니다. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | App Service 계획의 속성을 가져옵니다. |
> | Microsoft.Web/sites/functions/listSecrets/action | 비밀 Web Apps 함수를 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="logic-app-operator"></a>논리 앱 운영자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 읽기, 사용 및 logic apps를 사용 하지 않도록 설정 하지만 하지 편집 또는 업데이트할 수 있습니다. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/*/read | Insights 경고 규칙을 읽습니다. |
> | Microsoft.Insights/diagnosticSettings/*/read | Logic Apps에 대한 진단 설정을 가져옵니다. |
> | Microsoft.Insights/metricDefinitions/*/read | Logic Apps에 사용 가능한 메트릭을 가져옵니다. |
> | Microsoft.Logic/*/read | Logic Apps 리소스를 읽습니다. |
> | Microsoft.Logic/workflows/disable/action | 워크플로를 사용하지 않도록 설정합니다. |
> | Microsoft.Logic/workflows/enable/action | 워크플로를 사용하도록 설정합니다. |
> | Microsoft.Logic/workflows/validate/action | 워크플로의 유효성을 검사합니다. |
> | Microsoft.Resources/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | Microsoft.Resources/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Web/connectionGateways/*/read | 연결 게이트웨이를 읽습니다. |
> | Microsoft.Web/connections/*/read | 연결을 읽습니다. |
> | Microsoft.Web/customApis/*/read | 사용자 지정 API를 읽습니다. |
> | Microsoft.Web/serverFarms/read | App Service 계획의 속성을 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="managed-application-operator-role"></a>관리되는 애플리케이션 운영자 역할
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 관리되는 애플리케이션 리소스에서 작업을 읽고 수행할 수 있습니다. |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **actions** |  |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | Microsoft.Solutions/applications/read | 애플리케이션 목록을 검색합니다. |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="managed-applications-reader"></a>Managed Applications 읽기 권한자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 관리 앱 및 요청 JIT 액세스에서 리소스를 읽을 수 있습니다. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **actions** |  |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="managed-identity-contributor"></a>관리 ID 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 사용자 할당 ID를 만들고, 읽고, 업데이트하고, 삭제합니다. |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **actions** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="managed-identity-operator"></a>관리 ID 운영자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 사용자 할당 ID를 읽고 할당합니다. |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **actions** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="management-group-contributor"></a>관리 그룹 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 관리 그룹 참가자 역할 |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **actions** |  |
> | Microsoft.Management/managementGroups/delete | 관리 그룹을 삭제합니다. |
> | Microsoft.Management/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | Microsoft.Management/managementGroups/subscriptions/delete | 관리 그룹에서 구독의 연결을 해제합니다. |
> | Microsoft.Management/managementGroups/subscriptions/write | 기존 구독을 관리 그룹과 연결합니다. |
> | Microsoft.Management/managementGroups/write | 관리 그룹을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="management-group-reader"></a>관리 그룹 읽기 권한자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 관리 그룹 읽기 권한자 역할 |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **actions** |  |
> | Microsoft.Management/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="monitoring-contributor"></a>Monitoring Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. [Azure Monitor에서의 역할, 권한 및 보안 시작](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요. |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **actions** |  |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | 경고 규칙 읽기/쓰기/삭제 |
> | Microsoft.Insights/components/* | Application Insights 구성 요소 읽기/쓰기/삭제 |
> | Microsoft.Insights/DiagnosticSettings/* | 진단 설정 읽기/쓰기/삭제 |
> | Microsoft.Insights/eventtypes/* | 구독에서 활동 로그 이벤트(관리 이벤트)를 나열합니다. 이 권한은 활동 로그에 대한 프로그래밍 방식 및 포털 액세스 모두에 적용 가능합니다. |
> | Microsoft.Insights/LogDefinitions/* | 이 사용 권한은 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. 활동 로그의 로그 범주를 나열합니다. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | 메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다. |
> | Microsoft.Insights/Metrics/* | 리소스에 대한 메트릭을 읽습니다. |
> | Microsoft.Insights/Register/Action | Microsoft Insights 공급자를 등록합니다. |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Application Insights 웹 테스트 읽기/쓰기/삭제 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | 읽기/쓰기/삭제 log analytics 솔루션 팩 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | 읽기/쓰기/삭제 log analytics 저장 된 검색 합니다. |
> | Microsoft.OperationalInsights/workspaces/search/action | 검색 쿼리를 실행합니다. |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | 읽기/쓰기/삭제 log analytics 저장소 정보 구성 |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="monitoring-metrics-publisher"></a>모니터링 메트릭 게시자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure 리소스에 대한 게시 메트릭 사용 |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **actions** |  |
> | Microsoft.Insights/Register/Action | Microsoft Insights 공급자를 등록합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | 메트릭을 작성합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="monitoring-reader"></a>Monitoring Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 모든 모니터링 데이터(메트릭, 로그 등)를 읽을 수 있습니다. [Azure Monitor에서의 역할, 권한 및 보안 시작](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요. |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **actions** |  |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | Microsoft.OperationalInsights/workspaces/search/action | 검색 쿼리를 실행합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="network-contributor"></a>네트워크 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 네트워크를 관리할 수 있지만 액세스할 수는 없습니다. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.Network/* | 네트워크 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="new-relic-apm-account-contributor"></a>NewRelic APM 계정 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | New Relic Application Performance Management 계정 및 애플리케이션을 관리할 수 있지만 액세스할 수는 없습니다. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="reader-and-data-access"></a>읽기 권한자 및 데이터 액세스
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 모든 것을 볼 수 있지만, 저장소 계정 또는 포함된 리소스를 삭제하거나 만들 수는 없습니다. 또한 저장소 계정 키에 액세스하여 저장소 계정에 포함된 모든 데이터를 읽고 쓸 수 있습니다. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | 지정된 저장소 계정에 대한 액세스 키를 반환합니다. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | 지정된 저장소 계정에 대한 계정 SAS 토큰을 반환합니다. |
> | Microsoft.Storage/storageAccounts/read | 저장소 계정의 목록을 반환하거나 지정된 저장소 계정의 속성을 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="redis-cache-contributor"></a>Redis Cache 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Redis Cache를 관리할 수 있지만 액세스할 수는 없습니다. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Cache/redis/* | Redis 캐시 만들기 및 관리 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="resource-policy-contributor-preview"></a>리소스 정책 참가자(미리 보기)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | (미리 보기) 리소스 정책을 생성/수정하고, 지원 티켓을 만들고, 리소스/계층 구조를 읽을 수 있는 권한을 가진 EA의 백필된 사용자입니다. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **actions** |  |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | Microsoft.Authorization/policyassignments/* | 정책 할당을 만들고 관리합니다. |
> | Microsoft.Authorization/policydefinitions/* | 정책 정의를 만들고 관리합니다. |
> | Microsoft.Authorization/policysetdefinitions/* | 정책 집합을 만들고 관리합니다. |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="scheduler-job-collections-contributor"></a>Scheduler 작업 컬렉션 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Scheduler 작업 컬렉션을 관리할 수 있지만 액세스할 수는 없습니다. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Scheduler/jobcollections/* | 스케줄러 작업 컬렉션 만들기 및 관리 |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="search-service-contributor"></a>Search 서비스 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Search 서비스를 관리할 수 있지만 액세스할 수는 없습니다. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Search/searchServices/* | 검색 서비스 만들기 및 관리 |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="security-admin"></a>보안 관리자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Security Center에서만: 보안 정책 보기, 보안 상태 보기, 보안 정책 편집, 경고 및 권장 사항 보기, 경고 및 권장 사항 해제를 수행합니다. |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Authorization/policyAssignments/* | 정책 할당 만들기 및 관리 |
> | Microsoft.Authorization/policyDefinitions/* | 정책 정의 만들기 및 관리 |
> | Microsoft.Authorization/policySetDefinitions/* | 정책 집합 만들기 및 관리 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.Management/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | Microsoft.operationalInsights/workspaces/*/read | Log analytics 데이터 보기 |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="security-manager-legacy"></a>보안 관리자(레거시)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 레거시 역할입니다. 보안 관리자를 대신 사용하세요. |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.ClassicCompute/*/read | 클래식 가상 머신에 대한 구성 정보 읽기 |
> | Microsoft.ClassicCompute/virtualMachines/*/write | 클래식 가상 머신에 대한 구성 정보 쓰기 |
> | Microsoft.ClassicNetwork/*/read | 클래식 네트워크에 대한 구성 정보 읽기 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Security/* | 보안 구성 요소 및 정책 만들기 및 관리 |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="security-reader"></a>보안 판독기
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Security Center에서만: 권장 사항 및 경고를 보고, 보안 정책을 보고, 보안 상태를 볼 수 있지만 변경할 수는 없습니다. |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.operationalInsights/workspaces/*/read | Log analytics 데이터 보기 |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Security/*/read | 보안 구성 요소 및 정책 읽기 |
> | Microsoft.Support/* | 지원 티켓을 만들고 관리합니다. |
> | Microsoft.Management/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="service-bus-data-owner"></a>Service Bus 데이터 소유자

> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure Service Bus 리소스에 대 한 전체 액세스를 허용합니다. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **actions** |  |
> | Microsoft.ServiceBus/* | Service Bus 네임 스페이스에 대 한 완전 한 관리 액세스를 허용합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* | Service Bus 네임 스페이스에 대 한 전체 데이터 액세스를 허용합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="site-recovery-contributor"></a>Site Recovery 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 자격 증명 모음 만들기 및 역할 할당을 제외한 Site Recovery 서비스를 관리할 수 있습니다. |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.Network/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp는 서비스에서 사용하는 내부 작업입니다. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | 리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | 자격 증명 모음과 관련된 확장 정보 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 등록된 ID 만들기 및 관리 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | 복제 경고 설정 만들기 또는 업데이트 |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | 이벤트를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | 복제 패브릭 만들기 및 관리 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | 복제 작업 만들기 및 관리 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | 복제 정책 만들기 및 관리 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | 복구 계획 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Recovery Services 자격 증명 모음의 저장소 구성 만들기 및 관리 |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services 자격 증명 모음에 대한 경고 읽기 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Storage/storageAccounts/read | 저장소 계정의 목록을 반환하거나 지정된 저장소 계정의 속성을 가져옵니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="site-recovery-operator"></a>Site Recovery 운영자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 장애 조치(failover) 및 장애 복구(failback)를 수행할 수 있지만 다른 Site Recovery 관리 작업은 수행할 수 없습니다. |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.Network/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp는 서비스에서 사용하는 내부 작업입니다. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 경고 설정을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | 이벤트를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | 패브릭의 일관성을 검사합니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 패브릭을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | 게이트웨이를 다시 연결합니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | 패브릭용 인증서 갱신 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 네트워크를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 네트워크 매핑을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 보호 컨테이너를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 보호 가능한 항목을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 복구 지점을 적용합니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 장애 조치(Failover) 커밋 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 계획된 장애 조치(Failover) |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 보호된 항목을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 복제 복구 지점을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 복제를 복구합니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 보호된 항목을 다시 보호합니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | 테스트 장애 조치(Failover) |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 테스트 장애 조치(Failover) 정리 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 장애 조치(failover) |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 모바일 서비스를 업데이트합니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services 공급자를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 공급자를 새로 고칩니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 저장소 분류를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 저장소 분류 매핑을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | vCenter를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | 복제 작업 만들기 및 관리 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 정책을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 장애 조치(Failover) 커밋 복구 계획 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 계획된 장애 조치(Failover) 복구 계획 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 복구 계획을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 복구 계획을 다시 보호합니다. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 테스트 장애 조치(failover) 복구 계획 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 테스트 장애 조치(failover) 정리 복구 계획 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 장애 조치(Failover) 복구 계획 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services 자격 증명 모음에 대한 경고 읽기 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Storage/storageAccounts/read | 저장소 계정의 목록을 반환하거나 지정된 저장소 계정의 속성을 가져옵니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="site-recovery-reader"></a>Site Recovery 구독자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Site Recovery 상태를 볼 수 있지만 다른 관리 작업은 수행할 수 없습니다. |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 경고 설정을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | 이벤트를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 패브릭을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 네트워크를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 네트워크 매핑을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 보호 컨테이너를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 보호 가능한 항목을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 보호된 항목을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 복제 복구 지점을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services 공급자를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 저장소 분류를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 저장소 분류 매핑을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | vCenter를 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | 작업을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 정책을 읽습니다. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 복구 계획을 읽습니다. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="spatial-anchors-account-contributor"></a>Spatial Anchors 계정 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 계정에서 Spatial Anchors를 관리할 수 있지만 삭제할 수는 없습니다. |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **actions** |  |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | 공간 앵커 만들기 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | 공간 앵커 주변 검색 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | 공간 앵커 속성 가져오기 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | 공간 앵커를 찾습니다 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | 공간 앵커 Azure 서비스의 품질을 향상 시키기 위한 진단 데이터 제출 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | 공간 앵커 속성 업데이트 |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="spatial-anchors-account-owner"></a>Spatial Anchors 계정 소유자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 계정에서 Spatial Anchors를 관리할 수 있고 삭제도 할 수 있습니다. |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **actions** |  |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | 공간 앵커 만들기 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | 공간 앵커를 삭제 합니다. |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | 공간 앵커 주변 검색 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | 공간 앵커 속성 가져오기 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | 공간 앵커를 찾습니다 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | 공간 앵커 Azure 서비스의 품질을 향상 시키기 위한 진단 데이터 제출 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | 공간 앵커 속성 업데이트 |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="spatial-anchors-account-reader"></a>Spatial Anchors 계정 판독기
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 계정에서 Spatial Anchors의 속성을 찾고 읽을 수 있습니다. |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **actions** |  |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | 공간 앵커 주변 검색 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | 공간 앵커 속성 가져오기 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | 공간 앵커를 찾습니다 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | 공간 앵커 Azure 서비스의 품질을 향상 시키기 위한 진단 데이터 제출 |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="sql-db-contributor"></a>SQL DB 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | SQL 데이터베이스를 관리할 수 있지만 액세스할 수는 없습니다. 또한 보안 관련 정책이나 부모 SQL 서버를 관리할 수 없습니다. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | SQL 데이터베이스 만들기 및 관리 |
> | Microsoft.Sql/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Insights/metrics/read | 메트릭 읽기 |
> | Microsoft.Insights/metricDefinitions/read | 메트릭 정의 읽기 |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | 감사 정책 편집 |
> | Microsoft.Sql/servers/databases/auditingSettings/* | 감사 설정 편집 |
> | Microsoft.Sql/servers/databases/auditRecords/read | 데이터베이스 blob 감사 레코드를 검색합니다. |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | 연결 정책 편집 |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 데이터 마스킹 정책 편집 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | 보안 경고 정책 편집 |
> | Microsoft.Sql/servers/databases/securityMetrics/* | 보안 메트릭 편집 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="sql-managed-instance-contributor"></a>SQL 관리 인스턴스에 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | SQL 관리 되는 인스턴스를 관리 하는 데 필요한 수 있습니다 네트워크 구성에 있지만 다른 사용자에 게 액세스할 수 없습니다. |
> | **Id** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **actions** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Insights/metrics/read | 메트릭 읽기 |
> | Microsoft.Insights/metricDefinitions/read | 메트릭 정의 읽기 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="sql-security-manager"></a>SQL 보안 관리자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | SQL Server 및 데이터베이스의 보안과 관련된 정책을 관리할 수 있지만 여기에 액세스할 수는 없습니다. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | Microsoft 권한 부여 읽기 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 저장소 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 진행 되지 않습니다. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL 서버 감사 정책 만들기 및 관리 |
> | Microsoft.Sql/servers/auditingSettings/* | SQL 서버 감사 설정 만들기 및 관리 |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | 지정된 서버에 구성된 확장 서버 Blob 감사 정책의 세부 정보를 검색합니다. |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL 서버 데이터베이스 감사 정책 만들기 및 관리 |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL 서버 데이터베이스 감사 설정 만들기 및 관리 |
> | Microsoft.Sql/servers/databases/auditRecords/read | 감사 레코드 읽기 |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL 서버 데이터베이스 연결 정책 만들기 및 관리 |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL 서버 데이터베이스 데이터 마스킹 정책 만들기 및 관리 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 지정된 데이터베이스에 구성된 확장 Blob 감사 정책의 세부 정보를 검색합니다. |
> | Microsoft.Sql/servers/databases/read | 데이터베이스 목록을 가져오거나 지정된 데이터베이스에 대한 속성을 가져옵니다. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | 데이터베이스 스키마를 가져옵니다. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | 데이터베이스 열을 가져옵니다. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | 데이터베이스 테이블을 가져옵니다. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL 서버 데이터베이스 보안 경고 정책 만들기 및 관리 |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL 서버 데이터베이스 보안 메트릭 만들기 및 관리 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL 서버 보안 경고 정책 만들기 및 관리 |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="sql-server-contributor"></a>SQL Server 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | SQL Server 및 데이터베이스를 관리할 수 있지만 여기에 액세스할 수는 없으며, 해당하는 보안과 관련된 정책에도 액세스할 수 없습니다. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | SQL 서버 만들기 및 관리 |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Insights/metrics/read | 메트릭 읽기 |
> | Microsoft.Insights/metricDefinitions/read | 메트릭 정의 읽기 |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL 서버 감사 정책 편집 |
> | Microsoft.Sql/servers/auditingSettings/* | SQL 서버 감사 설정 편집 |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL 서버 데이터베이스 감사 정책 편집 |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL 서버 데이터베이스 감사 설정 편집 |
> | Microsoft.Sql/servers/databases/auditRecords/read | 감사 레코드 읽기 |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL 서버 데이터베이스 연결 정책 편집 |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL 서버 데이터베이스 데이터 마스킹 정책 편집 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL 서버 데이터베이스 보안 경고 정책 편집 |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL 서버 데이터베이스 보안 메트릭 편집 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL 서버 보안 경고 정책 편집 |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="storage-account-contributor"></a>Storage 계정 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 저장소 계정으로 관리할 수 있습니다. 공유 키 인증을 통해 데이터에 액세스 하는 계정 키에 대 한 액세스를 제공 합니다. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 모든 권한 부여 읽기 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Insights/diagnosticSettings/* | 진단 설정 관리 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 저장소 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 진행 되지 않습니다. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Storage/storageAccounts/* | 저장소 계정 만들기 및 관리 |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="storage-account-key-operator-service-role"></a>저장소 계정 키 운영자 서비스 역할
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 허용 목록 및 저장소 계정 액세스 키 다시 생성 합니다. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **actions** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | 지정된 된 저장소 계정에 대 한 액세스 키를 반환 합니다. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | 지정된 된 저장소 계정에 대 한 액세스 키 다시 생성 합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="storage-blob-data-contributor"></a>Storage Blob 데이터 Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 읽기, 쓰기 및 Azure Storage 컨테이너 및 blob을 삭제 합니다. 지정 된 데이터 작업에 필요한 되는 동작에 알아보려면 [blob 및 큐 데이터 작업을 호출 하는 것에 대 한 권한을](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)합니다. |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | 컨테이너를 삭제 합니다. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | 컨테이너 또는 컨테이너 목록을 반환 합니다. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | 컨테이너의 메타 데이터 또는 속성을 수정 합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Blob을 삭제합니다. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blob 또는 blob의 목록을 반환 합니다. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob에 작성 합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="storage-blob-data-owner"></a>Storage Blob 데이터 소유자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure Storage blob 컨테이너 및 POSIX 액세스 제어 할당을 포함 하 여 데이터에 대 한 전체 액세스를 제공 합니다. 지정 된 데이터 작업에 필요한 되는 동작에 알아보려면 [blob 및 큐 데이터 작업을 호출 하는 것에 대 한 권한을](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)합니다. |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | 컨테이너에 대 한 전체 권한입니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Blob에 대 한 전체 권한입니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="storage-blob-data-reader"></a>Storage Blob 데이터 Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 페이지를 읽고 Azure Storage 컨테이너 및 blob을 나열 합니다. 지정 된 데이터 작업에 필요한 되는 동작에 알아보려면 [blob 및 큐 데이터 작업을 호출 하는 것에 대 한 권한을](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)합니다. |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | 컨테이너 또는 컨테이너 목록을 반환 합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blob 또는 blob의 목록을 반환 합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="storage-queue-data-contributor"></a>Storage 큐 데이터 Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 읽기, 쓰기 및 Azure Storage 큐 및 큐 메시지를 삭제 합니다. 지정 된 데이터 작업에 필요한 되는 동작에 알아보려면 [blob 및 큐 데이터 작업을 호출 하는 것에 대 한 권한을](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)합니다. |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | 큐를 삭제 합니다. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | 큐 또는 큐 목록을 반환 합니다. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | 큐 메타 데이터 또는 속성을 수정 합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | 큐에서 하나 이상의 메시지를 삭제 합니다. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 보기 또는 큐에서 하나 이상의 메시지를 검색 합니다. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | 큐에 메시지를 추가 합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="storage-queue-data-message-processor"></a>Storage 큐 데이터 메시지 프로세서
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 피킹, 검색 및 Azure Storage 큐에서 메시지를 삭제 합니다. 지정 된 데이터 작업에 필요한 되는 동작에 알아보려면 [blob 및 큐 데이터 작업을 호출 하는 것에 대 한 권한을](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)합니다. |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **actions** |  |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 메시지를 보기 합니다. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | 검색 하 고 메시지를 삭제 합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="storage-queue-data-message-sender"></a>Storage 큐 데이터 메시지 보낸 사람
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure Storage 큐에 메시지를 추가 합니다. 지정 된 데이터 작업에 필요한 되는 동작에 알아보려면 [blob 및 큐 데이터 작업을 호출 하는 것에 대 한 권한을](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)합니다. |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **actions** |  |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | 큐에 메시지를 추가 합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="storage-queue-data-reader"></a>Storage 큐 데이터 Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 페이지를 읽고 Azure Storage 큐 및 큐 메시지를 나열 합니다. 지정 된 데이터 작업에 필요한 되는 동작에 알아보려면 [blob 및 큐 데이터 작업을 호출 하는 것에 대 한 권한을](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)합니다. |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | 큐 또는 큐 목록을 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 보기 또는 큐에서 하나 이상의 메시지를 검색 합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="support-request-contributor"></a>지원 요청 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 지원 요청을 만들고 관리할 수 있습니다. |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 읽기 권한 부여 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="traffic-manager-contributor"></a>Traffic Manager 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Traffic Manager 프로필을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="user-access-administrator"></a>사용자 액세스 관리자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **actions** |  |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | Microsoft.Authorization/* | 권한 부여 관리 |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="virtual-machine-administrator-login"></a>가상 머신 관리자 로그인
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 포털에서 Virtual Machines를 보고 관리자 권한으로 로그인합니다. |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Actions** |  |
> | Microsoft.Network/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | Microsoft.Network/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | Microsoft.Network/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
> | Microsoft.Network/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | 가상 머신에 일반 사용자로 로그인합니다. |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | 가상 머신에 Windows 관리자 또는 Linux 루트 사용자 권한으로 로그인합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="virtual-machine-contributor"></a>가상 머신 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 가상 머신을 관리할 수 있지만 가상머신이나 연결된 가상 네트워크 또는 저장소 계정에 액세스할 수는 없습니다. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 읽기 권한 부여 |
> | Microsoft.Compute/availabilitySets/* | 계산 가용성 집합 만들기 및 관리 |
> | Microsoft.Compute/locations/* | 계산 위치 만들기 및 관리 |
> | Microsoft.Compute/virtualMachines/* | 가상 머신 만들기 및 관리 |
> | Microsoft.Compute/virtualMachineScaleSets/* | 가상 머신 크기 집합 만들기 및 관리 |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | 응용 프로그램 게이트웨이 백 엔드 주소 풀을 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 부하 분산 장치 백 엔드 주소 풀을 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | 조인 하는 부하 분산 장치 인바운드 NAT 풀 합니다. 진행 되지 않습니다. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 부하 분산 장치 인바운드 nat 규칙을 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/loadBalancers/probes/join/action | 부하 분산 장치 프로브 사용을 허용합니다. 예를 들어 이 권한이 있으면 VM 확장 집합의 healthProbe 속성이 프로브를 참조할 수 있습니다. 진행 되지 않습니다. |
> | Microsoft.Network/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
> | Microsoft.Network/locations/* | 네트워크 위치 만들기 및 관리 |
> | Microsoft.Network/networkInterfaces/* | 네트워크 인터페이스 만들기 및 관리 |
> | Microsoft.Network/networkSecurityGroups/join/action | 네트워크 보안 그룹을 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/networkSecurityGroups/read | 네트워크 보안 그룹 정의를 가져옵니다. |
> | Microsoft.Network/publicIPAddresses/join/action | 공용 ip 주소를 조인합니다. 진행 되지 않습니다. |
> | Microsoft.Network/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | Microsoft.Network/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 진행 되지 않습니다. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | 백업 보호 의도 만들기 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 백업 보호 항목을 만듭니다. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | 보호 정책을 만듭니다. |
> | Microsoft.RecoveryServices/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | Microsoft.RecoveryServices/Vaults/write | 자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | 지정된 저장소 계정에 대한 액세스 키를 반환합니다. |
> | Microsoft.Storage/storageAccounts/read | 저장소 계정의 목록을 반환하거나 지정된 저장소 계정의 속성을 가져옵니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="virtual-machine-user-login"></a>가상 머신 사용자 로그인
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 포털에서 Virtual Machines를 보고 일반 사용자 권한으로 로그인합니다. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Actions** |  |
> | Microsoft.Network/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | Microsoft.Network/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | Microsoft.Network/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
> | Microsoft.Network/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | 가상 머신에 일반 사용자로 로그인합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="web-plan-contributor"></a>웹 계획 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 웹 사이트의 웹 계획을 관리할 수 있지만 액세스할 수는 없습니다. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 읽기 권한 부여 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Web/serverFarms/* | 서버 팜 만들기 및 관리 |
> | Microsoft.Web/hostingEnvironments/Join/Action | App Service Environment를 조인합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="website-contributor"></a>웹 사이트 참가자
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **설명** | 웹 사이트(웹 계획은 제외)를 관리할 수 있지만 액세스할 수는 없습니다. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **actions** |  |
> | Microsoft.Authorization/*/read | 읽기 권한 부여 |
> | Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
> | Microsoft.Insights/components/* | Insights 구성 요소 만들기 및 관리 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
> | Microsoft.Web/certificates/* | 웹 사이트 인증서 만들기 및 관리 |
> | Microsoft.Web/listSitesAssignedToHostName/read | 호스트 이름에 할당된 사이트의 이름을 가져옵니다. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | App Service 계획의 속성을 가져옵니다. |
> | Microsoft.Web/sites/* | 웹 사이트 만들기 및 관리(사이트 만들기도 관련 App Service 계획에 대한 쓰기 권한이 필요) |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 사용자 지정 역할](custom-roles.md)
- [RBAC 및 Azure Portal을 사용하여 Azure 리소스에 대한 액세스 관리](role-assignments-portal.md)
- [Azure Security Center의 권한](../security-center/security-center-permissions.md)
