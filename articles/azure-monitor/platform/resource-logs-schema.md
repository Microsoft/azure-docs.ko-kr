---
title: Azure 리소스 로그 지원 서비스 및 스키마
description: Azure 리소스 로그에 대해 지원 되는 서비스 및 이벤트 스키마를 이해 합니다.
ms.subservice: logs
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: acd661467e90b4915daa2f3eb31f34fd350dc9f5
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226466"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Azure 리소스 로그의 공통 및 서비스별 스키마

> [!NOTE]
> 이전에는 리소스 로그를 진단 로그 라고 했습니다. 이 이름은 2019 년 10 월에 변경 되었으며 Azure 리소스를 포함 하도록 이동 Azure Monitor 이동 했습니다. 또한이 문서에 나열 될 때까지 수집할 수 있는 리소스 로그 범주의 목록입니다. [리소스 로그 범주로](resource-logs-categories.md)이동 되었습니다. 

[Azure Monitor 리소스 로그](./platform-logs-overview.md) 는 해당 서비스 또는 리소스의 작업을 설명 하는 Azure 서비스에서 내보낸 로그입니다. Azure Monitor를 통해 제공 되는 모든 리소스 로그는 일반적인 최상위 스키마를 공유 하며 각 서비스는 고유한 이벤트의 고유한 속성을 내보낼 수 있는 유연성을 제공 합니다.

리소스 종류(`resourceId` 속성에 제공) 및 `category`가 조합되어 스키마를 고유하게 식별합니다. 이 문서에서는 리소스 로그의 최상위 스키마와 각 서비스에 대 한 schemata 링크를 설명 합니다.


## <a name="top-level-common-schema"></a>최상위 공용 스키마

| 이름 | 필수/선택 | 설명 |
|---|---|---|
| time | 필수 | 이벤트의 타임스탬프(UTC)입니다. |
| resourceId | 필수 | 이벤트를 내보낸 리소스의 리소스 ID입니다. 테넌트 서비스의 경우 /tenants/tenant-id/providers/provider-name의 형태입니다. |
| tenantId | 테넌트 로그에 필요 | 이 이벤트가 연결된 Active Directory 테넌트의 테넌트 ID입니다. 이 속성은 테넌트 수준 로그에만 사용되며 리소스 수준 로그에는 나타나지 않습니다. |
| operationName | 필수 | 이 이벤트가 나타내는 작업의 이름입니다. 이벤트가 RBAC 작업을 나타내는 경우 RBAC 작업 이름 (예: Microsoft Storage/storageAccounts/blobServices/blob/Read)입니다. 실제로 문서화된 리소스 관리자 작업은 아니지만, 일반적으로 리소스 관리자 작업 형태로 모델링됩니다(`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| operationVersion | 선택 사항 | API (예:)를 사용 하 여 operationName을 수행한 경우 작업에 연결 된 api 버전 `http://myservice.windowsazure.net/object?api-version=2016-06-01` 입니다. 이 작업에 해당하는 API가 없으면, 버전은 작업과 연결된 속성이 나중에 변경될 경우, 해당 작업의 버전을 나타냅니다. |
| category | 필수 | 이벤트의 로그 범주입니다. 범주는 특정 리소스에 대해 로그를 사용하거나 사용하지 않도록 설정할 수 있는 세분성입니다. 이벤트의 속성 Blob에 표시되는 속성은 특정 로그 범주 및 리소스 종류 내에서 동일합니다. 일반적인 로그 범주는 "감사" "작동" "실행" 및 "요청"입니다. |
| resultType | 선택 사항 | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| resultSignature | 선택 사항 | 이벤트의 하위 상태입니다. 이 작업이 REST API 호출에 해당 하는 경우이 필드는 해당 REST 호출의 HTTP 상태 코드입니다. |
| resultDescription | 선택 사항 | 이 작업에 대 한 정적 텍스트 설명입니다 (예: "저장소 파일 가져오기"). |
| durationMS | 선택 사항 | 밀리초 단위의 작업 기간입니다. |
| callerIpAddress | 선택 사항 | 작업이 공개적으로 사용 가능한 IP 주소를 가진 엔터티에서 가져온 API 호출에 해당 하는 경우 호출자 IP 주소입니다. |
| correlationId | 선택 사항 | 관련 이벤트 집합을 그룹화하는 데 사용되는 GUID입니다. 일반적으로 두 이벤트에 동일한 operationName이 있지만 두 개의 다른 상태 (예: "Started" 및 "Succeeded")가 있는 경우 동일한 상관 관계 ID를 공유 합니다. 이벤트 간의 다른 관계를 나타낼 수도 있습니다. |
| identity | 선택 사항 | 작업을 수행한 사용자 또는 애플리케이션의 ID를 설명하는 JSON Blob입니다. 일반적으로이 필드에는 active directory의 권한 부여 및 클레임/JWT 토큰이 포함 됩니다. |
| Level | 선택 사항 | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| 위치 | 선택 사항 | 이벤트를 내보내는 리소스의 지역 (예: "미국 동부" 또는 "프랑스 남부") |
| properties | 선택 사항 | 이 특정 범주의 이벤트와 관련된 확장 속성입니다. 모든 custom/unique 속성은 스키마의이 "파트 B" 안에 배치 해야 합니다. |

## <a name="service-specific-schemas"></a>서비스별 스키마

리소스 로그의 스키마는 리소스 및 로그 범주에 따라 달라 집니다. 이 목록에는 사용 가능한 리소스 로그와 서비스 및 범주 관련 스키마 (사용 가능한 경우)에 대 한 링크를 만드는 서비스가 표시 됩니다. 이 목록은 새 서비스가 추가 될 때마다 변경 됩니다. 따라서 아래에 필요한 항목이 표시 되지 않으면 검색 엔진을 사용 하 여 추가 설명서를 검색 하십시오. 이 문서에서 GitHub 문제를 자유롭게 열어서 업데이트할 수 있습니다.

| 서비스 | 스키마 및 문서 |
| --- | --- |
| Azure Active Directory | [개요](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [감사 로그 스키마](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) 및 [로그인 스키마](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://docs.microsoft.com/azure/analysis-services/analysis-services-logging |
| API Management | [API Management 리소스 로그](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Application Gateway |[Application Gateway에 대 한 로깅](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation에 대 한 Log analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 로깅](../../batch/batch-diagnostics.md) |
| Cognitive Services | [Azure Cognitive Services에 대 한 로깅](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Azure Container Registry에 대 한 로깅](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [CDN에 대 한 Azure 로그](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 로깅](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [Azure Monitor를 사용 하 여 데이터 팩터리 모니터링](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Azure Data Lake Analytics에 대 한 로그 액세스](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Azure Data Lake Store에 대 한 로그 액세스](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Data Explorer | [Azure 데이터 탐색기 로그](/azure/data-explorer/using-diagnostic-logs) |
| Azure Database for MySQL | [Azure Database for MySQL 진단 로그](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL 로그](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Azure Databricks의 진단 로깅](https://github.com/MicrosoftDocs/databricks-pr/blob/live/databricks/administration-guide/account-settings/azure-diagnostic-logs.md) |
| Digital Twins | [Digital Twins 진단 설정](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Event Hubs |[Azure Event Hubs 로그](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express 경로 | 스키마를 사용할 수 없음 |
| Azure Firewall | 스키마를 사용할 수 없음 |
| IoT Hub | [IoT Hub 작업](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault 로깅](../../key-vault/general/logging.md) |
| Kubernetes Service |[Azure Kubernetes 로깅](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Azure Load Balancer에 대한 Log analytics](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 사용자 지정 추적 스키마](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 네트워크 보안 그룹 |[NSG(네트워크 보안 그룹)에 대한 로그 분석](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Azure DDoS Protection 표준 관리](../../virtual-network/manage-ddos-protection.md) |
| Power BI 전용 | [Azure의 Power BI Embedded에 대 한 로깅](/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Azure Backup에 대 한 데이터 모델](../../backup/backup-azure-reports-data-model.md)|
| 검색 |[검색 트래픽 분석 설정 및 사용](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus 로그](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database 로깅](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[작업 로그](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager 로그 스키마](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| 가상 네트워크 | 스키마를 사용할 수 없음 |
| Virtual Network 게이트웨이 | 스키마를 사용할 수 없음 |



## <a name="next-steps"></a>다음 단계

* [수집할 수 있는 리소스 로그 범주를 참조 하세요.](resource-logs-categories.md)
* [리소스 로그에 대해 자세히 알아보기](./platform-logs-overview.md)
* [**Event Hubs** 에 대 한 리소스 리소스 로그 스트림](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Monitor REST API를 사용 하 여 리소스 로그 진단 설정 변경](/rest/api/monitor/diagnosticsettings)
* [Azure Storage에서 Log Analytics를 사용하여 로그 분석](./resource-logs.md#send-to-log-analytics-workspace)

