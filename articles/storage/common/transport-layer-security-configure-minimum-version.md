---
title: 들어오는 요청에 대해 필요한 최소 버전의 TLS (Transport Layer Security)를 적용 합니다.
titleSuffix: Azure Storage
description: Azure Storage에 대 한 요청을 수행 하는 클라이언트에 대해 최소 버전의 TLS (전송 계층 보안)가 필요 하도록 저장소 계정을 구성 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/29/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 2439bec08c16ce109b271844dc72b8fd2569aa07
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88755911"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>저장소 계정에 대 한 요청에 필요한 최소 버전의 TLS (Transport Layer Security)를 적용 합니다.

클라이언트 응용 프로그램과 Azure Storage 계정 간의 통신은 TLS (전송 계층 보안)를 사용 하 여 암호화 됩니다. TLS는 인터넷을 통해 클라이언트와 서비스 간에 개인 정보 및 데이터 무결성을 보장 하는 표준 암호화 프로토콜입니다. TLS에 대 한 자세한 내용은 [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)을 참조 하세요.

현재 Azure Storage는 세 가지 버전의 TLS 프로토콜 (1.0, 1.1 및 1.2)을 지원 합니다. Azure Storage는 공용 HTTPS 끝점에서 TLS 1.2를 사용 하지만 TLS 1.0 및 TLS 1.1은 이전 버전과의 호환성을 위해 계속 지원 됩니다.

기본적으로 Azure Storage 계정은 클라이언트에서 가장 오래 된 TLS, TLS 1.0 버전의 데이터를 보내고 받을 수 있도록 허용 합니다. 엄격한 보안 조치를 적용 하려면 클라이언트가 최신 버전의 TLS를 사용 하 여 데이터를 전송 및 수신 하도록 저장소 계정을 구성할 수 있습니다. 저장소 계정에 최소 버전의 TLS가 필요한 경우 이전 버전을 사용 하 여 만든 모든 요청은 실패 합니다.

이 문서에서는 끌어서 저장소 계정에 대 한 보안 TLS를 지속적으로 관리 하는 끌기 (검색-재구성-감사-관리) 프레임 워크를 사용 하는 방법을 설명 합니다.

클라이언트 응용 프로그램에서 요청을 보낼 때 특정 버전의 TLS를 지정 하는 방법에 대 한 자세한 내용은 [클라이언트 응용 프로그램에 대 한 tls (Transport Layer Security) 구성](transport-layer-security-configure-client-version.md)을 참조 하세요.

## <a name="detect-the-tls-version-used-by-client-applications"></a>클라이언트 응용 프로그램에서 사용 하는 TLS 버전 검색

저장소 계정에 최소 TLS 버전을 적용 하면 이전 버전의 TLS로 데이터를 전송 하는 클라이언트의 요청을 거부할 위험이 있습니다. 최소 TLS 버전을 구성 하는 것이 클라이언트 응용 프로그램에 영향을 줄 수 있는 방법을 이해 하려면 사용 중인 TLS 클라이언트 응용 프로그램 버전을 검색 하는 시간 간격 후에 Azure Storage 계정에 대 한 로깅을 사용 하도록 설정 하 고 로그를 분석 하는 것이 좋습니다.

Azure Storage 계정에 요청을 기록 하 고 클라이언트에서 사용 하는 TLS 버전을 확인 하려면 Azure Monitor (미리 보기)에서 Azure Storage 로깅을 사용할 수 있습니다. 자세한 내용은 [Azure Storage 모니터링](monitor-storage.md)을 참조 하세요.

Azure Monitor Azure Storage 로깅은 로그 쿼리를 사용 하 여 로그 데이터를 분석 합니다. 로그를 쿼리하려면 Azure Log Analytics 작업 영역을 사용할 수 있습니다. 로그 쿼리에 대해 자세히 알아보려면 [자습서: Log Analytics 쿼리 시작](../../azure-monitor/log-query/get-started-portal.md)을 참조 하세요.

Azure Monitor를 사용 하 여 Azure Storage 데이터를 기록 하 고 Azure Log Analytics를 사용 하 여 분석 하려면 먼저 요청 유형과 데이터를 기록할 저장소 서비스를 나타내는 진단 설정을 만들어야 합니다. Azure Portal에서 진단 설정을 만들려면 다음 단계를 수행 합니다.

1. [Azure Monitor 미리 보기에서 Azure Storage 로그인](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)에 등록 합니다.
1. Azure Storage 계정을 포함 하는 구독에 새 Log Analytics 작업 영역을 만듭니다. 저장소 계정에 대 한 로깅을 구성한 후 Log Analytics 작업 영역에서 로그를 사용할 수 있습니다. 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요.
1. Azure Portal의 스토리지 계정으로 이동합니다.
1. 모니터링 섹션에서 **진단 설정 (미리 보기)** 을 선택 합니다.
1. 요청을 기록 하려는 Azure Storage 서비스를 선택 합니다. 예를 들어 blob 저장소에 요청을 기록 하려면 **blob** 을 선택 합니다.
1. **진단 설정 추가**를 선택 합니다.
1. 진단 설정의 이름을 제공 합니다.
1. **범주 세부 정보**의 **로그** 섹션에서 로깅할 요청 형식을 선택 합니다. 읽기, 쓰기 및 삭제 요청을 기록할 수 있습니다. 예를 들어 **StorageRead** 및 **storagewrite** 를 선택 하면 선택한 서비스에 대 한 읽기 및 쓰기 요청이 기록 됩니다.
1. **대상 세부 정보**에서 **Log Analytics 보내기를**선택 합니다. 다음 이미지와 같이 앞에서 만든 구독 및 Log Analytics 작업 영역을 선택 합니다.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="요청 로깅에 대 한 진단 설정을 만드는 방법을 보여 주는 스크린샷":::

진단 설정을 만든 후에는 해당 설정에 따라 저장소 계정에 대 한 요청이 나중에 기록 됩니다. 자세한 내용은 [Azure에서 리소스 로그 및 메트릭을 수집 하는 진단 설정 만들기](../../azure-monitor/platform/diagnostic-settings.md)를 참조 하세요.

Azure Monitor의 Azure Storage 로그에서 사용할 수 있는 필드에 대 한 참조는 [리소스 로그 (미리 보기)](monitor-storage-reference.md#resource-logs-preview)를 참조 하세요.

### <a name="query-logged-requests-by-tls-version"></a>TLS 버전에 의해 기록 된 요청 쿼리

Azure Monitor Azure Storage 로그에는 저장소 계정에 요청을 보내는 데 사용 되는 TLS 버전이 포함 됩니다. **Tlsversion** 속성을 사용 하 여 로그 된 요청의 TLS 버전을 확인 합니다.

지난 7 일간 여러 버전의 TLS를 사용 하 여 Blob 저장소에 대해 수행 된 요청 수를 확인 하려면 Log Analytics 작업 영역을 엽니다. 다음 쿼리를 새 로그 쿼리에 붙여넣고 실행 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

결과에는 각 TLS 버전을 사용한 요청 수가 표시 됩니다.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="TLS 버전을 반환 하는 log analytics 쿼리 결과를 보여 주는 스크린샷":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>호출자 IP 주소 및 사용자 에이전트 헤더에 의해 기록 된 요청 쿼리

Azure Monitor의 Azure Storage 로그에는 저장소 계정에 액세스 한 클라이언트 응용 프로그램을 평가 하는 데 도움이 되는 호출자 IP 주소 및 사용자 에이전트 헤더도 포함 되어 있습니다. 이러한 값을 분석 하 여 최신 버전의 TLS를 사용 하도록 클라이언트 응용 프로그램을 업데이트 해야 하는지 여부 또는 최소 TLS 버전으로 전송 되지 않은 클라이언트의 요청을 실패할 수 있는지 여부를 결정할 수 있습니다.

지난 7 일간 tls 1.2 이상 버전을 사용 하 여 요청을 수행한 클라이언트를 확인 하려면 다음 쿼리를 새 로그 쿼리에 붙여넣고 실행 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>최소 버전의 TLS로 보안 위험 해결

이전 버전의 TLS를 사용 하는 클라이언트의 트래픽이 최소화 되거나 이전 버전의 TLS로 생성 된 요청에 실패할 경우 저장소 계정에 최소 TLS 버전 적용을 시작할 수 있습니다. 클라이언트에서 저장소 계정에 대 한 요청을 수행 하기 위해 최소 버전의 TLS를 사용 하도록 요구 하는 것은 데이터에 대 한 보안 위험을 최소화 하기 위한 전략의 일부입니다.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>저장소 계정에 대 한 최소 TLS 버전 구성

저장소 계정에 대 한 최소 TLS 버전을 구성 하려면 계정에 대 한 **최소 버전을** 설정 합니다. 이 속성은 Azure Resource Manager 배포 모델을 사용 하 여 만든 모든 저장소 계정에 사용할 수 있습니다. Azure Resource Manager 배포 모델에 대 한 자세한 내용은 [Storage 계정 개요](storage-account-overview.md)를 참조 하세요.

> [!NOTE]
> 이 **속성은** 기본적으로 설정 되지 않으며 명시적으로 설정할 때까지 값을 반환 하지 않습니다. 저장소 계정에서는 속성 값이 **null**인 경우 TLS 버전 1.0 이상으로 전송 된 요청을 허용 합니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal를 사용 하 여 저장소 계정에 대 한 최소 TLS 버전을 구성 하려면 다음 단계를 수행 합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **구성** 설정을 선택 합니다.
1. **최소 tls 버전**에서 다음 이미지에 표시 된 것 처럼 드롭다운을 사용 하 여이 저장소 계정의 데이터에 액세스 하는 데 필요한 최소 버전의 tls를 선택 합니다.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Azure Portal에서 TLS의 최소 버전을 구성 하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 저장소 계정에 대 한 최소 TLS 버전을 구성 하려면 [Azure PowerShell 버전 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) 이상을 설치 합니다. 다음으로 새 또는 기존 저장소 계정에 대 한 이상 **버전** 속성을 구성 합니다. 이상 값에 **대 한** 유효한 값은 `TLS1_0` , `TLS1_1` 및 `TLS1_2` 입니다.

다음 예에서는 저장소 계정을 만들고, **이를 tls 1.1로 설정** 하 고, 계정을 업데이트 하 고, 다음 **버전** 을 tls 1.2로 설정 합니다. 또한이 예제에서는 각 사례에서 속성 값을 검색 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName \
    -AccountName $accountName \
    -Location $location \
    -SkuName Standard_GRS \
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName \
    -AccountName $accountName \
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 저장소 계정에 대 한 최소 TLS 버전을 구성 하려면 Azure CLI 버전 2.9.0 이상을 설치 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 다음으로 새 또는 기존 저장소 계정에 대 한 이상 **버전** 속성을 구성 합니다. 이상 값에 **대 한** 유효한 값은 `TLS1_0` , `TLS1_1` 및 `TLS1_2` 입니다.

다음 예제에서는 저장소 계정을 만들고, 이상 **버전** 을 TLS 1.1으로 설정 합니다. 그런 다음 계정을 업데이트 하 고, **버전** 속성을 TLS 1.2로 설정 합니다. 또한이 예제에서는 각 사례에서 속성 값을 검색 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[템플릿](#tab/template)

템플릿을 사용 하 여 저장소 계정에 대 한 최소 TLS 버전을 구성 하려면 다음 **버전** 속성을, 또는로 설정 하 여 템플릿을 `TLS1_0` 만듭니다 `TLS1_1` `TLS1_2` . 다음 단계에서는 Azure Portal에서 템플릿을 만드는 방법을 설명 합니다.

1. Azure Portal에서 **리소스 만들기**를 선택 합니다.
1. **Marketplace 검색**에서 **템플릿 배포**를 입력 하 고 **enter**키를 누릅니다.
1. **템플릿 배포 (사용자 지정 템플릿을 사용 하 여 배포) (미리 보기)** 를 선택 하 고 **만들기**를 선택한 다음 **편집기에서 사용자 고유의 템플릿 빌드**를 선택 합니다.
1. 템플릿 편집기에서 다음 JSON을 붙여넣어 새 계정을 만들고 최소 TLS 버전을 TLS 1.2로 설정 합니다. 꺾쇠 괄호 안의 자리 표시자를 사용자 고유의 값으로 대체 해야 합니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. 템플릿을 저장하는 경우
1. 리소스 그룹 매개 변수를 지정 하 고 **검토 + 만들기** 단추를 선택 하 여 템플릿을 배포 하 고, **버전** 속성이 구성 된 저장소 계정을 만듭니다.

---

> [!NOTE]
> 저장소 계정에 대 한 최소 TLS 버전을 업데이트 한 후에는 변경이 완전히 전파 되기까지 최대 30 초가 걸릴 수 있습니다.

최소 TLS 버전을 구성 하려면 Azure Storage 리소스 공급자의 버전 2019-04-01 이상이 필요 합니다. 자세한 내용은 [Azure Storage 리소스 공급자 REST API](/rest/api/storagerp/)를 참조 하세요.

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>여러 계정에 필요한 최소 TLS 버전 확인

최적의 성능을 가진 일련의 저장소 계정에서 필요한 최소 TLS 버전을 확인 하려면 Azure Portal에서 Azure 리소스 그래프 탐색기를 사용할 수 있습니다. 리소스 그래프 탐색기를 사용 하는 방법에 대해 자세히 알아보려면 [빠른 시작: Azure 리소스 그래프 탐색기를 사용 하 여 첫 번째 리소스 그래프 쿼리 실행](/azure/governance/resource-graph/first-query-portal)을 참조 하세요.

리소스 그래프 탐색기에서 다음 쿼리를 실행 하면 저장소 계정 목록이 반환 되 고 각 계정에 대 한 최소 TLS 버전이 표시 됩니다.

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>클라이언트에서 최소 TLS 버전 테스트

저장소 계정에 필요한 최소 TLS 버전이 이전 버전의 호출을 금지 하는지 테스트 하려면 이전 버전의 TLS를 사용 하도록 클라이언트를 구성할 수 있습니다. 특정 버전의 TLS를 사용 하도록 클라이언트를 구성 하는 방법에 대 한 자세한 내용은 [클라이언트 응용 프로그램에 대 한 tls (Transport Layer Security) 구성](transport-layer-security-configure-client-version.md)을 참조 하세요.

클라이언트가 계정에 대해 구성 된 최소 TLS 버전을 충족 하지 않는 TLS 버전을 사용 하 여 저장소 계정에 액세스 하는 경우 Azure Storage 오류 코드 400 오류 (잘못 된 요청)가 반환 되 고 사용 된 TLS 버전이이 저장소 계정에 대해 요청을 수행 하는 데 허용 되지 않음을 나타내는 메시지가 반환 됩니다.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Azure Policy를 사용 하 여 규정 준수 감사

저장소 계정이 많은 경우 감사를 수행 하 여 조직에서 요구 하는 최소 버전의 TLS에 대해 모든 계정이 구성 되어 있는지 확인 하는 것이 좋습니다. 준수를 위해 저장소 계정 집합을 감사 하려면 Azure Policy을 사용 합니다. Azure Policy은 Azure 리소스에 규칙을 적용 하는 정책을 만들고 할당 하 고 관리 하는 데 사용할 수 있는 서비스입니다. Azure Policy는 회사 표준 및 서비스 수준 계약을 준수 하는 리소스를 유지 하는 데 도움이 됩니다. 자세한 내용은 [Azure Policy 개요](../../governance/policy/overview.md)를 참조하세요.

### <a name="create-a-policy-with-an-audit-effect"></a>감사 효과를 사용 하 여 정책 만들기

Azure Policy은 리소스에 대해 정책 규칙을 평가할 때 발생 하는 작업을 결정 하는 효과를 지원 합니다. 감사 효과는 리소스가 호환 되지 않는 경우 경고를 생성 하지만 요청을 중지 하지는 않습니다. 효과에 대 한 자세한 내용은 [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)를 참조 하세요.

Azure Portal의 최소 TLS 버전에 대 한 감사 효과를 사용 하 여 정책을 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동 합니다.
1. **제작** 섹션에서 **정의**를 선택 합니다.
1. **정책 정의 추가** 를 선택 하 여 새 정책 정의를 만듭니다.
1. **정의 위치** 필드의 경우 **자세히** 단추를 선택 하 여 감사 정책 리소스가 있는 위치를 지정 합니다.
1. 정책의 이름을 지정 합니다. 설명 및 범주를 선택적으로 지정할 수 있습니다.
1. **정책 규칙**에서 다음 정책 정의를 **policyrule** 섹션에 추가 합니다.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
              "equals": "TLS1_2"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. 해당 정책을 저장합니다.

### <a name="assign-the-policy"></a>정책 할당

그런 다음 리소스에 정책을 할당 합니다. 정책의 범위는 해당 리소스 및 그 아래에 있는 리소스에 해당 합니다. 정책 할당에 대 한 자세한 내용은 [Azure Policy 할당 구조](../../governance/policy/concepts/assignment-structure.md)를 참조 하세요.

정책을 Azure Portal 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동 합니다.
1. **제작** 섹션에서 **할당**을 선택 합니다.
1. **정책 할당** 을 선택 하 여 새 정책 할당을 만듭니다.
1. **범위** 필드에서 정책 할당의 범위를 선택 합니다.
1. **정책 정의** 필드에서 **자세히** 단추를 선택한 다음, 목록에서 이전 섹션에서 정의한 정책을 선택 합니다.
1. 정책 할당의 이름을 제공 합니다. 설명은 선택 사항입니다.
1. **정책 적용** 을 *사용*으로 설정 된 상태로 둡니다. 이 설정은 감사 정책에 영향을 주지 않습니다.
1. **검토 + 만들기** 를 선택 하 여 할당을 만듭니다.

### <a name="view-compliance-report"></a>준수 보고서 보기

정책을 할당 한 후에는 준수 보고서를 볼 수 있습니다. 감사 정책에 대 한 준수 보고서는 정책을 준수 하지 않는 저장소 계정에 대 한 정보를 제공 합니다. 자세한 내용은 [정책 준수 데이터 가져오기](../../governance/policy/how-to/get-compliance-data.md)를 참조 하세요.

정책 할당을 만든 후 준수 보고서를 사용할 수 있게 되는 데 몇 분 정도 걸릴 수 있습니다.

Azure Portal에서 준수 보고서를 보려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동 합니다.
1. **준수**를 선택 합니다.
1. 이전 단계에서 만든 정책 할당의 이름에 대 한 결과를 필터링 합니다. 이 보고서에는 정책을 준수 하지 않는 리소스의 수가 표시 됩니다.
1. 정책을 준수 하지 않는 저장소 계정 목록을 포함 하 여 추가 세부 정보에 대 한 보고서를 드릴 다운할 수 있습니다.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="최소 TLS 버전의 감사 정책에 대 한 준수 보고서를 보여 주는 스크린샷":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Azure Policy를 사용 하 여 최소 TLS 버전을 적용 합니다.

Azure Policy는 Azure 리소스가 요구 사항 및 표준을 준수 하도록 하 여 클라우드 거 버 넌 스를 지원 합니다. 조직의 저장소 계정에 대 한 최소 TLS 버전 요구 사항을 적용 하기 위해 정책에 의해 결정 된 것 보다 이전 버전의 TLS로 최소 TLS 요구 사항을 설정 하는 새 저장소 계정을 만들지 못하도록 하는 정책을 만들 수 있습니다. 또한이 정책은 해당 계정에 대 한 최소 TLS 버전 설정이 정책을 준수 하지 않는 경우 기존 계정에 대 한 모든 구성 변경을 방지 합니다.

적용 정책은 거부 효과를 사용 하 여 최소 TLS 버전이 조직의 표준에 더 이상 부합 하지 않도록 저장소 계정을 만들거나 수정 하는 요청을 방지 합니다. 효과에 대 한 자세한 내용은 [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)를 참조 하세요.

TLS 1.2 보다 작은 최소 TLS 버전에 대해 거부 효과가 적용 된 정책을 만들려면 [Azure Policy를 사용 하 여 규정 준수 감사](#use-azure-policy-to-audit-for-compliance)에 설명 된 것과 동일한 단계를 수행 하지만 정책 정의의 **policyrule** 섹션에서 다음 JSON을 제공 합니다.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
          "equals": "TLS1_2"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

거부 효과를 사용 하 여 정책을 만들고 범위에 할당 한 후 사용자는 최소 TLS 버전 1.2 보다 오래 된 저장소 계정을 만들 수 없습니다. 현재 1.2 보다 오래 된 최소 TLS 버전을 필요로 하는 기존 저장소 계정에 대 한 구성을 변경할 수 없습니다. 이렇게 하려고 하면 오류가 발생 합니다. 계정 만들기 또는 구성을 계속 하려면 저장소 계정에 필요한 최소 TLS 버전을 1.2으로 설정 해야 합니다.

다음 이미지는 거부 효과가 있는 정책에서 최소 tls 버전을 TLS 1.2로 설정 해야 하는 경우 최소 TLS 버전을 TLS 1.0 (새 계정에 대 한 기본값)로 설정 하 여 저장소 계정을 만들려는 경우 발생 하는 오류를 보여 줍니다.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="정책을 위반 하 여 저장소 계정을 만들 때 발생 하는 오류를 보여 주는 스크린샷":::

## <a name="network-considerations"></a>네트워크 고려 사항

클라이언트에서 저장소 계정에 요청을 보내면 클라이언트는 요청을 처리 하기 전에 먼저 저장소 계정의 공용 끝점에 대 한 연결을 설정 합니다. 최소 TLS 버전 설정은 연결이 설정 된 후에 선택 됩니다. 요청에서 설정에 지정 된 것 보다 이전 버전의 TLS를 사용 하는 경우 연결에 계속 성공 하지만 요청이 결국 실패 하 게 됩니다. Azure Storage에 대 한 공용 끝점에 대 한 자세한 내용은 [리소스 URI 구문](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [클라이언트 응용 프로그램에 대 한 TLS (전송 계층 보안) 구성](transport-layer-security-configure-client-version.md)
- [Blob Storage에 대한 보안 권장 사항](../blobs/security-recommendations.md)
