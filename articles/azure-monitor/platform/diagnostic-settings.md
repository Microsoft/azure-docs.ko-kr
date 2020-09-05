---
title: 플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기
description: 진단 설정을 사용 하 여 Azure Monitor 로그, Azure storage 또는 Azure Event Hubs에 Azure Monitor 플랫폼 메트릭 및 로그를 보냅니다.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: 74e0a63da87a79cbd582cd6da5992251fc256504
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135439"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기
Azure 활동 로그 및 리소스 로그를 포함 한 azure의 [플랫폼 로그](platform-logs-overview.md) 는 azure 리소스 및 해당 리소스가 종속 된 azure 플랫폼에 대 한 자세한 진단 및 감사 정보를 제공 합니다. [플랫폼 메트릭은](data-platform-metrics.md) 기본적으로 수집 되며 일반적으로 Azure Monitor 메트릭 데이터베이스에 저장 됩니다. 이 문서에서는 플랫폼 메트릭 및 플랫폼 로그를 다른 대상으로 보내기 위한 진단 설정을 만들고 구성 하는 방법에 대해 자세히 설명 합니다.

> [!IMPORTANT]
> 활동 로그에 대 한 진단 설정을 만들기 전에 먼저 레거시 구성을 사용 하지 않도록 설정 해야 합니다. 자세한 내용은 [레거시 컬렉션 메서드](activity-log.md#legacy-collection-methods) 를 참조 하세요.

각 Azure 리소스에는 다음 조건을 정의 하는 자체 진단 설정이 필요 합니다.

- 설정에 정의된 대상으로 전송되는 로그 및 메트릭 데이터의 범주. 사용 가능한 범주는 리소스 유형에 따라 달라집니다.
- 로그를 보낼 하나 이상의 대상입니다. 현재 대상에는 Log Analytics 작업 영역, Event Hubs 및 Azure Storage가 포함됩니다.

단일 진단 설정은 각 대상 중 하나만 정의할 수 있습니다. 데이터를 2개 이상의 특정 대상 유형(예: 두 개의 다른 Log Analytics 작업 영역) 으로 보내려면 여러 개의 설정을 만듭니다. 각 리소스는 진단 설정을 5개까지 포함할 수 있습니다.

다음 비디오에서는 진단 설정으로 플랫폼 로그를 라우팅하는 방법을 보여 줍니다.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [플랫폼 메트릭은](metrics-supported.md) [Azure Monitor 메트릭에](data-platform-metrics.md)자동으로 전송 됩니다. 진단 설정은 특정 제한 사항이 있는 [로그 쿼리](../log-query/log-query-overview.md) 를 사용 하 여 다른 모니터링 데이터를 분석 하기 위해 특정 Azure 서비스에 대 한 메트릭을 Azure Monitor 로그에 전송 하는 데 사용할 수 있습니다. 
>  
>  
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다. *예*: 블록 체인의 ' IOReadBytes ' 메트릭은 노드 수준별로 탐색 하 고 차트로 표시할 수 있습니다. 그러나 진단 설정을 통해 내보낸 메트릭은 모든 노드에 대 한 모든 읽기 바이트로를 나타냅니다. 또한 내부 제한으로 인해 모든 메트릭을 Azure Monitor Logs/Log Analytics으로 내보낼 수 없습니다. 자세한 내용은 내보낼 수 있는 [메트릭 목록](metrics-supported-export-diagnostic-settings.md)을 참조 하십시오. 
>  
>  
> 특정 메트릭에 대 한 이러한 제한을 해결 하려면 [REST API 메트릭을](/rest/api/monitor/metrics/list) 사용 하 여 수동으로 추출 하 고 [AZURE MONITOR 데이터 수집기 API](data-collector-api.md)를 사용 하 여 Azure Monitor 로그로 가져오는 것이 좋습니다.  


## <a name="destinations"></a>Destinations
다음 표의 대상에 플랫폼 로그 및 메트릭을 보낼 수 있습니다. 

| 대상 | Description |
|:---|:---|
| [Log Analytics 작업 영역](design-logs-deployment.md) | 로그 및 메트릭을 Log Analytics 작업 영역으로 보내면 강력한 로그 쿼리를 사용 하 여 Azure Monitor 수집 된 다른 모니터링 데이터로 분석 하 고 경고 및 시각화와 같은 기타 Azure Monitor 기능을 활용할 수 있습니다. |
| [Event Hubs](/azure/event-hubs/) | 로그 및 메트릭을 Event Hubs로 보내면 타사 SIEMs 및 기타 log analytics 솔루션과 같은 외부 시스템으로 데이터를 스트리밍할 수 있습니다.  |
| [Azure storage 계정](/azure/storage/blobs/) | Azure storage 계정에 로그 및 메트릭을 보관 하는 것은 감사, 정적 분석 또는 백업에 유용 합니다. Azure Monitor 로그 및 Log Analytics 작업 영역에 비해 Azure storage는 비용이 적고 로그가 무기한으로 보관 될 수 있습니다.  |


### <a name="destination-requirements"></a>대상 요구 사항

진단 설정을 만들기 전에 진단 설정의 대상을 만들어야 합니다. 설정을 구성 하는 사용자에 게 두 구독에 대 한 적절 한 RBAC 액세스 권한이 있는 한, 대상은 로그를 보내는 리소스와 동일한 구독을가지고 있지 않아도 됩니다. 다음 표에서는 지역 제한을 포함 하 여 각 대상에 대 한 고유한 요구 사항을 제공 합니다.

| 대상 | 요구 사항 |
|:---|:---|
| Log Analytics 작업 영역 | 작업 영역은 모니터링 되는 리소스와 동일한 지역에 있을 필요가 없습니다.|
| 이벤트 허브(영문) | 네임 스페이스에 대 한 공유 액세스 정책은 스트리밍 메커니즘이 포함 하는 사용 권한을 정의 합니다. Event Hubs로 스트리밍하려면 관리, 보내기 및 수신 권한이 필요 합니다. 스트리밍을 포함 하도록 진단 설정을 업데이트 하려면 해당 Event Hubs 권한 부여 규칙에 대 한 ListKey 권한이 있어야 합니다.<br><br>리소스가 지역별 인 경우 이벤트 허브 네임 스페이스는 모니터링 되는 리소스와 동일한 지역에 있어야 합니다. |
| Azure Storage 계정 | 데이터에 대 한 액세스를 더 효율적으로 제어할 수 있도록 저장 된 다른 데이터를 모니터링 하지 않는 기존 저장소 계정을 사용해 서는 안 됩니다. 그러나 활동 로그와 리소스 로그를 함께 보관 하는 경우 동일한 저장소 계정을 사용 하 여 모든 모니터링 데이터를 중앙 위치에 유지 하도록 선택할 수 있습니다.<br><br>데이터를 변경할 수 없는 저장소로 보내려면 [Blob 저장소에 대 한 불변성 정책 설정 및 관리](../../storage/blobs/storage-blob-immutability-policies-manage.md)에 설명 된 대로 저장소 계정에 대 한 변경할 수 없는 정책을 설정 합니다. 보호 된 추가 blob 쓰기 사용을 비롯 하 여이 문서의 모든 단계를 수행 해야 합니다.<br><br>리소스가 지역별 인 경우 저장소 계정은 모니터링 되는 리소스와 동일한 지역에 있어야 합니다. |

> [!NOTE]
> Azure Data Lake Storage Gen2 계정은 현재 Azure Portal에서 유효한 옵션으로 나열될 수 있지만 진단 설정의 대상으로 지원되지 않습니다.



## <a name="create-in-azure-portal"></a>Azure Portal에서 만들기

Azure Monitor 메뉴 또는 리소스의 메뉴에서 Azure Portal 진단 설정을 구성할 수 있습니다.

1. Azure Portal에서 진단 설정을 구성 하는 위치는 리소스에 따라 달라 집니다.

   - 단일 리소스의 경우 리소스 메뉴의 **모니터** 에서 **진단 설정** 을 클릭 합니다.

        ![진단 설정](media/diagnostic-settings/menu-resource.png)

   - 하나 이상의 리소스에 대해 Azure Monitor 메뉴의 **설정** 에서 **진단 설정** 을 클릭 한 다음 리소스를 클릭 합니다.

      ![진단 설정](media/diagnostic-settings/menu-monitor.png)

   - 활동 로그의 경우 **Azure Monitor** 메뉴에서 **활동 로그** 를 클릭 한 다음 **진단 설정**을 클릭 합니다. 활동 로그에 대 한 레거시 구성은 사용 하지 않도록 설정 해야 합니다. 자세한 내용은 [기존 설정 사용 안 함](./activity-log.md#legacy-collection-methods) 을 참조 하세요.

        ![진단 설정](media/diagnostic-settings/menu-activity-log.png)

2. 선택한 리소스에 설정이 없는 경우, 설정을 만들라는 메시지가 표시됩니다. **진단 설정 추가**를 클릭합니다.

   ![진단 설정 추가 - 기존 설정 없음](media/diagnostic-settings/add-setting.png)

   리소스에 기존 설정이 있는 경우 이미 구성 된 설정 목록이 표시 됩니다. **진단 설정 추가** 를 클릭 하 여 새 설정을 추가 하거나 **설정을 편집** 하 여 기존 항목을 편집 합니다. 각 설정에는 대상 유형 중 하나만 지정할 수 있습니다.

   ![진단 설정 추가 - 기존 설정](media/diagnostic-settings/edit-setting.png)

3. 아직 없는 경우 설정 이름을 지정 합니다.

    ![진단 설정 추가](media/diagnostic-settings/setting-new-blank.png)

4. **범주 세부 정보 (라우팅할 항목)** -나중에 지정 된 대상으로 보내려는 각 데이터 범주에 대 한 확인란을 선택 합니다. 범주 목록은 각 Azure 서비스 마다 다릅니다.

     - **Allmetrics** 리소스의 플랫폼 메트릭을 로그 형식으로 Azure 로그 저장소로 라우팅합니다. 이러한 메트릭은 일반적으로 Azure Monitor 메트릭 시계열 데이터베이스로만 전송 됩니다. Azure Monitor 로그 저장소 (Log Analytics를 통해 검색할 수 있는)에 전송 하 여 다른 로그에서 검색 하는 쿼리에 통합할 수 있습니다. 일부 리소스 종류에는이 옵션을 사용 하지 못할 수 있습니다. 지원 되는 경우 지원 되는 [메트릭 Azure Monitor](metrics-supported.md) 리소스 유형에 대해 수집 되는 메트릭을 나열 합니다.

       > [!NOTE]
       > 이 문서의 앞부분에서 Azure Monitor 로그에 대 한 라우팅 메트릭을 확인 하는 방법을 참조 하세요.  


     - **로그** 에는 리소스 종류에 따라 사용 가능한 다양 한 범주가 나열 됩니다. 대상으로 라우팅할 모든 범주를 선택 합니다.

5. **대상 세부 정보** -각 대상에 대 한 확인란을 선택 합니다. 각 확인란을 선택 하면 추가 정보를 추가할 수 있는 옵션이 표시 됩니다.

      ![Log Analytics 또는 Event Hubs으로 보내기](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** -구독 및 작업 영역을 입력 합니다.  작업 영역이 없으면 작업 영역을 [만든 후 계속 진행](../learn/quick-create-workspace.md)해야 합니다.

    1. **Event hubs** -다음 조건을 지정 합니다.
       - 이벤트 허브가 포함 된 구독
       - 이벤트 허브 네임 스페이스-아직 없는 경우 [하나 만들어야](../../event-hubs/event-hubs-create.md) 합니다.
       - 모든 데이터를 보낼 이벤트 허브 이름 (선택 사항)입니다. 이름을 지정 하지 않으면 각 로그 범주에 대해 이벤트 허브가 생성 됩니다. 여러 범주를 전송 하는 경우 생성 되는 이벤트 허브 수를 제한 하는 이름을 지정할 수 있습니다. 자세한 내용은 [Azure Event Hubs 할당량 및 제한을](../../event-hubs/event-hubs-quotas.md) 참조 하세요.
       - 이벤트 허브 정책 (옵션) 정책은 스트리밍 메커니즘에 포함 된 사용 권한을 정의 합니다. 자세한 내용은 [이벤트 허브 기능](../../event-hubs/event-hubs-features.md#publisher-policy)을 참조 하세요.

    1. **저장소** -구독, 저장소 계정 및 보존 정책을 선택 합니다.

        ![저장소로 보내기](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > 보존 정책을 0으로 설정 하 고 예약 된 작업을 사용 하 여 저장소에서 데이터를 수동으로 삭제 하 여 나중에 혼동을 피할 수 있습니다.
        >
        > 첫째, 보관에 저장소를 사용 하는 경우 일반적으로 365 일 넘게 데이터를 사용 하려고 합니다. 둘째, 0 보다 큰 보존 정책을 선택 하는 경우 만료 날짜는 저장소 시 로그에 연결 됩니다. 저장 한 후에는 해당 로그의 날짜를 변경할 수 없습니다.
        >
        > 예를 들어, *WorkflowRuntime* 에 대 한 보존 정책을 180 일로 설정 하 고 24 시간 후에 365 일로 설정 하면, 처음 24 시간 동안 저장 된 로그는 180 일 후 자동으로 삭제 되는 반면, 해당 형식의 모든 후속 로그는 365 일 후에 자동으로 삭제 됩니다. 나중에 보존 정책을 변경 해도 처음 24 시간 동안 로그가 365 일 동안 유지 되지 않습니다.

6. **저장**을 클릭합니다.

잠시 후 새 설정이이 리소스에 대 한 설정 목록에 표시 되 고, 새 이벤트 데이터가 생성 될 때 로그가 지정 된 대상으로 스트리밍됩니다. 이벤트가 내보내지는 시간과 [Log Analytics 작업 영역에 표시](data-ingestion-time.md)되는 시간 사이에는 최대 15 분이 걸릴 수 있습니다.

## <a name="create-using-powershell"></a>PowerShell을 사용 하 여 만들기

[Azure PowerShell](../samples/powershell-samples.md)를 사용 하 여 진단 설정을 만들려면 [AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet을 사용 합니다. 매개 변수에 대 한 설명은이 cmdlet에 대 한 설명서를 참조 하세요.

> [!IMPORTANT]
> Azure 활동 로그에는이 방법을 사용할 수 없습니다. 대신 [리소스 관리자 템플릿을 사용 하 여 Azure Monitor에서 진단 설정 만들기](diagnostic-settings-template.md) 를 사용 하 여 리소스 관리자 템플릿을 만들고 PowerShell을 사용 하 여 배포 합니다.

다음은 세 가지 대상을 모두 사용 하 여 진단 설정을 만드는 예제 PowerShell cmdlet입니다.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Azure CLI를 사용 하 여 만들기

[Az monitor 진단-설정 만들기](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) 명령을 사용 하 여 [Azure CLI](/cli/azure/monitor?view=azure-cli-latest)를 사용 하 여 진단 설정을 만듭니다. 매개 변수에 대 한 설명은이 명령의 설명서를 참조 하세요.

> [!IMPORTANT]
> Azure 활동 로그에는이 방법을 사용할 수 없습니다. 대신 [리소스 관리자 템플릿을 사용 하 여 Azure Monitor에서 진단 설정 만들기](diagnostic-settings-template.md) 를 사용 하 여 리소스 관리자 템플릿을 만들고 CLI를 사용 하 여 배포 합니다.

다음은 세 개의 대상을 모두 사용 하 여 진단 설정을 만드는 예제 CLI 명령입니다.

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 만들기
리소스 관리자 템플릿을 사용 하 여 진단 설정을 만들거나 업데이트 [Azure Monitor의 진단 설정에 대 한 리소스 관리자 템플릿 예제](../samples/resource-manager-diagnostic-settings.md) 를 참조 하세요.

## <a name="create-using-rest-api"></a>REST API를 사용하여 만들기
[Azure Monitor REST API](/rest/api/monitor/)를 사용 하 여 진단 설정을 만들거나 업데이트 하려면 [진단 설정](/rest/api/monitor/diagnosticsettings) 을 참조 하세요.

## <a name="create-using-azure-policy"></a>Azure Policy를 사용 하 여 만들기
각 Azure 리소스에 대 한 진단 설정을 만들어야 하기 때문에 각 리소스가 생성 될 때마다 진단 설정을 자동으로 만드는 데 Azure Policy 사용할 수 있습니다. 자세한 내용은 [Azure Policy를 사용 하 여 규모에 Azure Monitor 배포](../deploy-scale.md) 를 참조 하세요.


## <a name="next-steps"></a>다음 단계

- [Azure platform Logs에 대해 자세히 알아보세요.](platform-logs-overview.md)
