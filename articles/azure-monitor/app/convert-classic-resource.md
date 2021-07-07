---
title: Azure Monitor Application Insights 클래식 리소스를 작업 영역 기반 리소스로 마이그레이션 | Microsoft Docs
description: Azure Monitor Application Insights 클래식 리소스를 새 작업 영역 기반 모델로 업그레이드 하는 데 필요한 단계에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5791abe33dee2e62aadb00ae1024338e1e44a900
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584253"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>작업 영역 기반 Application Insights 리소스로 마이그레이션

이 가이드에서는 클래식 Application Insights 리소스를 작업 영역 기반 리소스로 마이그레이션하는 과정을 안내 합니다. 작업 영역 기반 리소스는 Application Insights와 Log Analytics 간의 완전한 통합을 지원합니다. 작업 영역 기반 리소스는 일반적인 Log Analytics 작업 영역에 Application Insights 원격 분석을 보냅니다 .이를 통해 응용 프로그램, 인프라 및 플랫폼 로그를 통합 된 단일 위치에 유지 하면서 [Azure Monitor의 최신 기능](#new-capabilities) 에 액세스할 수 있습니다.

작업 영역 기반 리소스를 사용 하면 리소스에서 일반적인 Azure 역할 기반 액세스 제어 (Azure RBAC)를 사용할 수 있으며 앱 간/작업 영역 쿼리가 필요 하지 않습니다.

**작업 영역 기반 리소스는 현재 모든 상용 지역 및 Azure 미국 정부에서 사용할 수 있습니다.**

## <a name="new-capabilities"></a>새로운 기능

작업 영역 기반 Application Insights를 사용 하면 다음을 포함 하 여 Azure Monitor 및 Log Analytics의 모든 최신 기능을 활용할 수 있습니다.

* [CMK (고객 관리 키](../logs/customer-managed-keys.md) )는 사용자만 액세스할 수 있는 암호화 키를 사용 하 여 저장소 데이터 암호화를 제공 합니다.
* [Azure Private Link](../logs/private-link-security.md)를 사용하면 프라이빗 엔드포인트를 사용하여 Azure PaaS 서비스를 가상 네트워크에 안전하게 연결할 수 있습니다.
* [Profiler 및 스냅숏 디버거에 대 한 사용자 고유의 저장소 (BYOS)](./profiler-bring-your-own-storage.md) 를 사용 하면 Application Insights Profiler 및 스냅숏 디버거와 연결 된 모든 데이터에 대 한 전체 암호화 정책, 수명 관리 정책 및 네트워크 액세스를 완벽 하 게 제어할 수 있습니다. 
* [용량 예약 계층](../logs/manage-cost-storage.md#pricing-model) 을 사용 하면 종 량 제 가격과 비교 하 여 25%까지 절감할 수 있습니다. 
* Log Analytics 스트리밍 수집을 통한 더 빠른 데이터 수집

## <a name="migration-process"></a>마이그레이션 프로세스

작업 영역 기반 리소스로 마이그레이션하면 클래식 리소스의 저장소에서 새 작업 영역 기반 저장소로 데이터가 전송 되지 않습니다. 마이그레이션을 선택 하면 기존 리소스 데이터에 대 한 액세스를 유지 하면서 새 데이터가 Log Analytics 작업 영역에 기록 되는 위치가 변경 됩니다. 

클래식 리소스 데이터는 유지 되 고 클래식 Application Insights 리소스의 보존 설정이 적용 됩니다. 모든 새 데이터 수집 사후 마이그레이션에는 연결 된 Log Analytics 작업 영역의 [보존 설정이](../logs/manage-cost-storage.md#change-the-data-retention-period) 적용 됩니다 .이 설정은 [데이터 형식에 따라 다른 보존 설정](../logs/manage-cost-storage.md#retention-by-data-type)도 지원 합니다.
마이그레이션 프로세스는 **영구적 이며 되돌릴 수 없습니다**. 리소스를 작업 영역 기반 Application Insights로 마이그레이션하면 항상 작업 영역 기반 리소스가 됩니다. 그러나 마이그레이션한 후에는 필요에 따라 대상 작업 영역을 자주 변경할 수 있습니다. 

> [!NOTE]
> 작업 영역 기반 Application Insights 리소스에 대 한 데이터 수집 및 보존은 데이터가 있는 [Log Analytics 작업 영역을 통해 청구](../logs/manage-cost-storage.md) 됩니다. 마이그레이션 전에 데이터 수집에서 클래식 Application Insights 리소스로 데이터 보존을 90 일 이상 선택한 경우에는 해당 Application Insights 리소스를 통해 데이터 보존 비용이 계속 청구 됩니다. 작업 영역 기반 Application Insights 리소스에 대한 요금 청구에 대해 [자세히 알아봅니다]( ./pricing.md#workspace-based-application-insights).

기존 리소스를 마이그레이션할 필요가 없으며 대신 새 작업 영역 기반 Application Insights 리소스를 만들려는 경우 [작업 영역 기반 리소스 생성 가이드](create-workspace-resource.md)를 사용 합니다.

## <a name="pre-requisites"></a>필수 구성 요소 

- 액세스 제어 모드가 설정으로 설정 된 Log Analytics 작업 영역 **`use resource or workspace permissions`** 입니다. 

    - 작업 영역 기반 Application Insights 리소스는 전용 설정으로 설정 된 작업 영역과 호환 되지 않습니다 **`workspace based permissions`** . Log Analytics 작업 영역 access control에 대 한 자세한 내용은 [액세스 제어 모드 구성 Log Analytics](../logs/manage-access.md#configure-access-control-mode) 을 참조 하세요.

    - 기존 Log Analytics 작업 영역이 아직 없는 경우 [Log Analytics 작업 영역 만들기 설명서를 참조하세요](../logs/quick-create-workspace.md).
    
- 연속 내보내기는 작업 영역 기반 리소스에 대해 지원 되지 않으므로 사용 하지 않도록 설정 해야 합니다.
마이그레이션이 완료 되 면 [진단 설정을](../essentials/diagnostic-settings.md) 사용 하 여 저장소 계정에 대 한 데이터 보관 또는 Azure 이벤트 허브로의 스트리밍을 구성할 수 있습니다.  

-   >  Log Analytics 작업 영역에 대 한 일반 **사용량 및 예상 비용**  >  **데이터 보존** 에서 현재 보존 설정을 확인 합니다. 이 설정은 Application Insights 리소스를 마이그레이션한 후 새 수집 데이터가 저장 되는 기간에 영향을 줍니다. 현재 Application Insights 데이터를 기본 90 일 보다 오래 저장 하 고이 더 큰 보존 기간을 유지 하려는 경우 작업 영역 보존 설정을 조정 해야 할 수 있습니다.

## <a name="migrate-your-resource"></a>리소스 마이그레이션

이 섹션에서는 클래식 Application Insights 리소스를 새 작업 영역 기반 리소스 종류로 마이그레이션하는 과정을 안내 합니다.

1. Application Insights 리소스에서 왼쪽 메뉴 모음의 **구성** 제목 아래에 있는 **속성** 을 선택 합니다.

    ![빨간색 상자에 강조 표시 된 속성](./media/convert-classic-resource/properties.png)

2. **`Migrate to Workspace-based`** 을 선택합니다.
    
     ![리소스 마이그레이션 단추](./media/convert-classic-resource/migrate.png)

3. 모든 향후 수집 Application Insights 원격 분석을 저장 하려는 Log Analytics 작업 영역을 선택 합니다.

     ![대상 작업 영역을 선택 하는 옵션을 포함 하는 마이그레이션 마법사 UI](./media/convert-classic-resource/migration.png)
    

리소스가 마이그레이션되면 **개요** 창에 해당 작업 영역 정보가 표시 됩니다.

![작업 영역 이름](./media/create-workspace-resource/workspace-name.png)

파란색 링크 텍스트를 클릭하면 새 통합 작업 영역 쿼리 환경을 활용할 수 있는 관련 Log Analytics 작업 영역으로 이동합니다.

## <a name="understanding-log-queries"></a>로그 쿼리 이해

Application Insights 환경 내에서 Application Insights 클래식 리소스 쿼리, 통합 문서 및 로그 기반 경고에 대해 계속 이전 버전과 완전히 호환됩니다. 

[새 작업 영역 기반 테이블 구조/스키마](apm-tables.md)에 대 한 쿼리를 작성 하려면 먼저 Log Analytics 작업 영역으로 이동 해야 합니다. 

작업 영역 내의 Log Analytics UI에서 직접 쿼리하면 마이그레이션 후 수집 된 데이터만 표시 됩니다. 통합 쿼리 환경에서 마이그레이션 후 클래식 Application Insights 데이터 + 새 데이터 수집를 모두 보려면 마이그레이션된 Application Insights 리소스 내에서 로그 (분석) 쿼리 뷰를 사용 합니다.

## <a name="programmatic-resource-migration"></a>프로그래밍 방식 리소스 마이그레이션

### <a name="azure-cli"></a>Azure CLI

미리 보기 Application Insights Azure CLI 명령에 액세스하려면 먼저 다음을 실행해야 합니다.

```azurecli
 az extension add -n application-insights
```

`az extension add` 명령을 실행하지 않으면 다음 오류 메시지가 표시됩니다. `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

이제 다음을 실행하여 Application Insights 리소스를 만들 수 있습니다.

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>예제

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

이 명령에 대한 전체 Azure CLI 설명서는 [Azure CLI 설명서](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-update)를 참조하세요.

### <a name="azure-powershell"></a>Azure PowerShell

`Update-AzApplicationInsights`PowerShell 명령은 현재 클래식 Application Insights 리소스를 작업 영역 기반으로 마이그레이션하는 작업을 지원 하지 않습니다. PowerShell을 사용하여 작업 영역 기반 리소스를 만들려면 아래 Azure Resource Manager 템플릿을 사용하고 PowerShell을 사용하여 배포할 수 있습니다.

### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

#### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>매개 변수 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>연결된 작업 영역 수정

작업 영역 기반 Application Insights 리소스를 만든 후에는 연결된 Log Analytics 작업 영역을 수정할 수 있습니다.

Application Insights 리소스 창에서 **속성**  >  **작업 영역** Log Analytics 작업 영역 변경을 선택  >  합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="access-mode"></a>액세스 모드

**오류 메시지:** *선택한 작업 영역은 작업 영역 기반 액세스 모드로 구성 되어 있습니다. 일부 APM 기능이 영향을 받을 수 있습니다. 작업 영역 설정에서 다른 작업 영역을 선택 하거나 리소스 기반 액세스를 허용 합니다. CLI를 사용 하 여이 오류를 재정의할 수 있습니다.* 

작업 영역 기반 Application Insights 리소스가 제대로 작동 하도록 하려면 대상 Log Analytics 작업 영역의 액세스 제어 모드를 **리소스 또는 작업 영역 권한** 설정으로 변경 해야 합니다. 이 설정은 Log Analytics 작업 영역 UI의 **속성**  >  **액세스 제어 모드** 에 있습니다. 자세한 지침은 [Log Analytics access control mode 구성 지침](../logs/manage-access.md#configure-access-control-mode)을 참조 하세요. 액세스 제어 모드가 전용 **필요 작업 영역 권한** 설정으로 설정 된 경우 포털 마이그레이션 환경을 통한 마이그레이션은 차단 된 상태로 유지 됩니다.

현재 대상 작업 영역에 대 한 보안 이유로 액세스 제어 모드를 변경할 수 없는 경우 마이그레이션에 사용할 새 Log Analytics 작업 영역을 만드는 것이 좋습니다. 

### <a name="continuous-export"></a>연속 내보내기

**오류 메시지:** *계속 하려면 연속 내보내기를 사용 하지 않도록 설정 해야 합니다. 마이그레이션 후에는 내보내기에 대 한 진단 설정을 사용 합니다.* 

작업 영역 기반 리소스에는 레거시 연속 내보내기 기능이 지원되지 않습니다. 마이그레이션하기 전에 연속 내보내기를 사용 하지 않도록 설정 해야 합니다.

1. Application Insights 리소스 보기의 **구성** 제목에서 **연속 내보내기** 를 선택 합니다.

    ![연속 내보내기 메뉴 항목](./media/convert-classic-resource/continuous-export.png)

2. **사용 안 함** 을 선택합니다.

    ![연속 내보내기 사용 안 함 단추](./media/convert-classic-resource/disable.png)

- 사용 안 함을 선택 하면 마이그레이션 UI로 다시 이동할 수 있습니다. 연속 내보내기 편집 페이지에 설정이 저장 되지 않는다는 메시지가 표시 되는 경우 연속 내보내기를 사용 하거나 사용 하지 않도록 설정 하는 것과 관련이 없으므로이 프롬프트에 대해 확인을 선택할 수 있습니다.

- Application Insights 리소스를 작업 영역 기반으로 성공적으로 마이그레이션한 후에는 진단 설정을 사용 하 여에서 제공 하는 데 사용 되는 연속 내보내기 기능을 대체할 수 있습니다. Application Insights 리소스 내에서 **진단 설정**  >  **추가 진단 설정** 을 선택 합니다. 모든 테이블 또는 테이블 하위 집합을 선택하여 스토리지 계정에 보관하거나 Azure Event Hub로 스트리밍할 수 있습니다. 진단 설정에 대 한 자세한 지침은 [Azure Monitor 진단 설정 지침](../essentials/diagnostic-settings.md)을 참조 하세요.

### <a name="retention-settings"></a>보존 설정

**경고 메시지:** *사용자 지정 된 Application Insights 보존 설정은 작업 영역으로 전송 된 데이터에 적용 되지 않습니다. 이는 별도로 다시 구성 해야 합니다.*

마이그레이션하기 전에 변경 작업을 수행할 필요는 없지만,이 메시지는 현재 Application Insights 보존 설정이 기본 90 일 보존 기간으로 설정 되어 있지 않은지 경고 하는 것입니다. 이 경고 메시지는 마이그레이션 전에 Log Analytics 작업 영역에 대 한 보존 설정을 수정 하 고 새 데이터 수집을 시작 하는 것을 의미 합니다. 

  >  Log Analytics UI 내에서 일반 **사용량 및 예상 비용**  >  **데이터 보존** 에서 Log Analytics에 대 한 현재 보존 설정을 확인할 수 있습니다. 이 설정은 Application Insights 리소스를 마이그레이션한 후 새 수집 데이터가 저장 되는 기간에 영향을 줍니다.

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색](../essentials/metrics-charts.md)
* [분석 쿼리 작성](../logs/log-query-overview.md)
