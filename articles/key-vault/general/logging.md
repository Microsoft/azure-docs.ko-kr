---
title: Azure Key Vault 로깅 | Microsoft Docs
description: 사용자가 제공하는 정보를 Azure 스토리지 계정에 저장하도록 Azure Key Vault에서 로깅을 설정하여 키 자격 증명 모음에 대한 액세스를 모니터링하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: e9507525dc2c52f584bd7883a12da401b5999f50
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585919"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault 로깅

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

하나 이상의 키 자격 증명 모음을 만든 후에는 키 자격 증명 모음에 액세스하는 방법, 시기 및 사용자를 모니터링하려고 할 수도 있습니다. 이 작업은 제공하는 정보를 Azure 스토리지 계정에 저장하는 Azure Key Vault에서 로깅을 사용하도록 설정하여 수행할 수 있습니다. 지정한 스토리지 계정에 대해 **insights-logs-auditevent**라는 새 컨테이너가 자동으로 만들어집니다. 여러 키 자격 증명 모음에 대한 로그를 수집하는 데 이 동일한 스토리지 계정을 사용할 수 있습니다.

키 자격 증명 모음 작업 후 최대 10분 동안 로깅 정보에 액세스할 수 있습니다. 대부분의 경우 이것보다 빠릅니다.  스토리지 계정의 로그 관리에 따라 다릅니다.

* 표준 Azure 액세스 제어 메서드를 사용하여 액세스할 수 있는 사용자를 제한하여 로그를 보호합니다.
* 더 이상 스토리지 계정에 유지하지 않으려는 로그를 삭제합니다.

이 자습서를 사용하여 Azure Key Vault 로깅을 시작할 수 있습니다. 스토리지 계정을 만들고, 로깅을 사용하도록 설정하고, 수집된 로그 정보를 해석합니다.  

> [!NOTE]
> 이 자습서는 키 자격 증명 모음, 키 또는 암호를 만드는 방법에 대한 지침을 다루지 않습니다. 자세한 내용은 [Azure Key Vault란?](overview.md)을 참조하세요). 또는 플랫폼 간 Azure CLI 지침은 [이 해당 자습서](manage-with-cli2.md)를 참조하세요).
>
> 이 문서에서는 진단 로깅을 업데이트하기 위한 Azure PowerShell 지침을 제공합니다. 또한 Azure Portal의 **진단 로그** 섹션에서 Azure Monitor를 사용하여 진단 로깅을 업데이트할 수도 있습니다. 
>

Key Vault에 대한 개요 정보는 [Azure Key Vault란?](overview.md)을 참조하세요). Key Vault를 사용할 수 있는 위치에 대한 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

* 사용하고 있는 기존 키 자격 증명 모음  
* Azure PowerShell(최소 버전 1.0.0). Azure PowerShell을 설치하고 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/)을 참조하세요. Azure PowerShell이 이미 설치되어 있고 버전을 알 수 없는 경우 Azure PowerShell 콘솔에서 `$PSVersionTable.PSVersion`을 입력합니다.  
* 키 자격 증명 모음 로그에 대한 Azure의 충분한 스토리지.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>키 자격 증명 모음 구독에 연결

키 로깅을 설정하는 첫 번째 단계는 Azure PowerShell에서 로깅하려는 키 자격 증명 모음을 가리키는 것입니다.

Azure PowerShell 세션을 시작하고 다음 명령을 사용하여 Azure 계정에 로그인합니다.  

```powershell
Connect-AzAccount
```

팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. Azure PowerShell에서 이 계정과 연결된 모든 구독을 가져옵니다. PowerShell은 기본적으로 첫 번째 구독을 사용합니다.

키 자격 증명 모음을 만드는 데 사용한 구독을 지정해야 할 수도 있습니다. 다음 명령을 입력하여 계정에 대한 구독을 확인합니다.

```powershell
Get-AzSubscription
```

그런 다음, 로깅하려는 키 자격 증명 모음과 연결된 구독을 지정하려면 다음을 입력합니다.

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

PowerShell에서 올바른 구독을 가리키는 것은 특히 계정과 연결된 여러 구독이 있는 경우 중요한 단계입니다. Azure PowerShell 구성에 관한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요.

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>로그에 대한 스토리지 계정 만들기

로그에 대해 기존 스토리지 계정을 사용할 수 있지만 Key Vault 로그 전용 스토리지 계정을 만듭니다. 나중에 이를 지정해야 하는 경우 편의를 위해 세부 정보를 **sa**라는 변수에 저장합니다.

추가로 쉽게 관리하기 위해 키 자격 증명 모음이 포함된 것과 동일한 리소스 그룹을 사용합니다. [시작 자습서](../secrets/quick-create-cli.md)에서 이 리소스 그룹의 이름은 **ContosoResourceGroup** 이며, 동아시아 위치를 계속 사용합니다. 해당하는 경우 이러한 값을 사용자 고유의 값으로 바꿉니다.

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> 기존 스토리지 계정을 사용하려면 키 자격 증명 모음과 동일한 구독을 사용해야 합니다. 그리고 클래식 배포 모델 대신 Azure Resource Manager 배포 모델을 사용해야 합니다.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>로그에 대한 키 자격 증명 모음 식별

[시작 자습서](../secrets/quick-create-cli.md)에서 키 자격 증명 모음 이름은 **ContosoKeyVault**였습니다. 이 이름을 계속 사용하고 세부 정보를 **kv**라는 변수에 저장합니다.

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging-using-azure-powershell"></a><a id="enable"></a>Azure PowerShell을 통해 로깅 사용

Key Vault에 대한 로깅을 사용하도록 설정하려면 새 스토리지 계정 및 키 자격 증명 모음에 대해 만든 변수와 함께 **Set-AzDiagnosticSetting** cmdlet을 사용합니다. 또한 **-Enabled** 플래그를 **$true**로 설정하고 범주를 `AuditEvent`(Key Vault 로깅에 대한 유일한 범주)로 설정합니다.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

출력은 다음과 같이 표시됩니다.

```output
StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccountContosoKeyVaultLogs
ServiceBusRuleId   :
StorageAccountName :
    Logs
    Enabled           : True
    Category          : AuditEvent
    RetentionPolicy
    Enabled : False
    Days    : 0
```

이 출력에서는 이제 키 자격 증명 모음에 대한 로깅을 사용하도록 설정되었음을 확인하고, 정보를 스토리지 계정에 저장합니다.

필요에 따라 오래된 로그가 자동으로 삭제되도록 로그 보존 정책을 설정할 수 있습니다. 예를 들어 **-RetentionEnabled** 플래그를 **$true**로 설정하여 보존 정책을 설정하고, 90일보다 오래된 로그가 자동으로 삭제되도록 **-RetentionInDays** 매개 변수를 **90**으로 설정합니다.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

다음이 로깅됩니다.

* 인증된 모든 REST API 요청(예: 액세스 권한, 시스템 오류 또는 잘못된 요청으로 인해 실패한 요청)
* 키 자격 증명 모음 자체에 대한 작업(예: 키 자격 증명 모음 액세스 정책 만들기, 삭제, 설정 및 태그와 같은 키 자격 증명 모음 특성 업데이트)
* 다음을 포함하여 키 자격 증명 모음의 키 및 비밀에 대한 작업
  * 이러한 키 또는 비밀 만들기, 수정 또는 삭제
  * 서명, 확인, 암호화, 암호 해독, 키 래핑 및 래핑 해제, 비밀 가져오기, 키 및 비밀(및 해당 버전) 나열
* 401 응답이 발생하는 인증되지 않은 요청. 예를 들어 전달자 토큰이 없거나, 형식이 잘못되었거나 만료되었거나, 잘못된 토큰이 있는 요청입니다.  

## <a name="enable-logging-using-azure-cli"></a>Azure CLI를 통해 로깅 사용

```azurecli
az login

az account set --subscription {AZURE SUBSCRIPTION ID}

az provider register -n Microsoft.KeyVault

az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="access-your-logs"></a><a id="access"></a>로그에 액세스

Key Vault 로그는 제공한 스토리지 계정의 **insights-logs-auditevent** 컨테이너에 저장됩니다. 로그를 보려면 Blob을 다운로드해야 합니다.

먼저, 컨테이너 이름에 대한 변수를 만듭니다. 이 변수는 연습의 나머지 부분에서 사용합니다.

```powershell
$container = 'insights-logs-auditevent'
```

이 컨테이너의 모든 Blob을 나열하려면 다음을 입력합니다.

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

출력은 다음과 비슷합니다.

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

이 출력에서 확인할 수 있듯이 Blob은 명명 규칙 `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`을 따릅니다.

날짜 및 시간 값은 UTC를 사용합니다.

동일한 스토리지 계정을 사용하여 여러 리소스에 대한 로그를 수집할 수 있으므로 Blob 이름의 전체 리소스 ID는 필요한 Blob에 액세스하거나 다운로드하는 데 유용합니다. 하지만 그 전에 먼저 모든 Blob을 다운로드하는 방법을 다룹니다.

Blob을 다운로드할 폴더를 만듭니다. 예를 들면 다음과 같습니다.

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

그런 다음 모든 Blob 목록을 가져옵니다.  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

**Get-AzStorageBlobContent**를 통해 이 목록을 파이프하여 Blob을 대상 폴더로 다운로드합니다.

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

이 두 번째 명령을 실행할 때 Blob 이름의 **/** 구분 기호는 대상 폴더 아래에 전체 폴더 구조를 만듭니다. 이 구조를 사용하여 Blob을 다운로드하고 파일로 저장합니다.

선택적으로 Blob을 다운로드하려면 와일드카드를 사용합니다. 예를 들면 다음과 같습니다.

* 여러 키 자격 증명 모음이 있고 CONTOSOKEYVAULT3이라는 하나의 키 자격 증명 모음에 대한 로그를 다운로드하려는 경우:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 리소스 그룹이 여러 개이고 하나의 리소스 그룹에 대한 로그를 다운로드하려는 경우 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`을(를) 사용합니다.

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 2019년 1월의 모든 로그를 다운로드하려면 `-Blob '*/year=2019/m=01/*'`을 사용합니다.

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

이제 로그에 있는 것을 확인할 준비가 되었습니다. 그러나 이 작업을 진행하기 전에 추가적으로 다음 두 가지 명령을 알아야 합니다.

* 주요 자격 증명 모음 리소스의 진단 설정 상태를 쿼리하려면: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* 주요 자격 증명 모음 리소스의 로깅을 사용하지 않으려면: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`


## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>Key Vault 로그 해석

개별 Blob은 JSON Blob 형식으로 텍스트로 저장됩니다. 로그 항목 예제를 살펴보겠습니다. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

다음 표에는 필드 이름 및 설명이 나와 있습니다.

| 필드 이름 | Description |
| --- | --- |
| **time** |UTC 형식의 날짜 및 시간입니다. |
| **resourceId** |Azure Resource Manager 리소스 ID입니다. 키 자격 증명 모음 로그의 경우 이는 항상 키 자격 증명 모음 리소스 ID입니다. |
| **operationName** |다음 표에 설명된 대로 작업의 이름입니다. |
| **operationVersion** |클라이언트에서 요청한 REST API 버전입니다. |
| **category** |결과 유형입니다. Key Vault 로그의 경우 `AuditEvent`는 사용 가능한 단일 값입니다. |
| **resultType** |REST API 요청의 결과입니다. |
| **resultSignature** |HTTP 상태입니다. |
| **resultDescription** |사용 가능한 경우 결과에 대한 추가 설명입니다. |
| **durationMs** |밀리초 단위로 REST API 요청을 처리하는 데 걸린 시간입니다. 네트워크 대기 시간을 포함하지 않으므로 클라이언트 쪽에서 측정한 시간은 이 시간과 일치하지 않을 수 있습니다. |
| **callerIpAddress** |요청한 클라이언트의 IP 주소입니다. |
| **correlationId** |클라이언트가 서비스 쪽(키 자격 증명 모음) 로그를 사용하여 클라이언트 쪽 로그 상관 관계를 지정하도록 전달할 수 있는 선택적 GUID입니다. |
| **identity** |REST API 요청에 제공된 토큰의 ID입니다. Azure PowerShell cmdlet에서 발생하는 요청의 경우와 마찬가지로 일반적으로 "사용자", "서비스 주체" 또는 "사용자+appId"의 조합입니다. |
| **properties** |작업(**operationName**)에 따라 달라지는 정보입니다. 대부분의 경우 이 필드에는 클라이언트 정보(클라이언트에서 전달한 사용자 에이전트 문자열), 정확한 REST API 요청 URI 및 HTTP 상태 코드가 포함됩니다. 또한 개체가 요청의 결과로 반환되면(예: **KeyCreate** 또는 **VaultGet**) 키 URI(`id`로), 자격 증명 모음 URI 또는 비밀 URI도 포함됩니다. |

**operationName** 필드 값은 *ObjectVerb* 형식입니다. 예를 들면 다음과 같습니다.

* 모든 키 자격 증명 모음 작업은 `Vault<action>` 형식입니다(예: `VaultGet` 및 `VaultCreate`).
* 모든 키 작업은 `Key<action>` 형식입니다(예: `KeySign` 및 `KeyList`).
* 모든 비밀 작업은 `Secret<action>` 형식입니다(예: `SecretGet` 및 `SecretListVersions`).

다음 표에는 **operationName** 값 및 해당 REST API 명령이 나와 있습니다.

| operationName | REST API 명령 |
| --- | --- |
| **인증** |Azure Active Directory 엔드포인트를 통해 인증 |
| **VaultGet** |[키 자격 증명 모음에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[키 자격 증명 모음 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[키 자격 증명 모음 삭제](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[키 자격 증명 모음 업데이트](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[리소스 그룹의 모든 키 자격 증명 모음 목록](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[키 만들기](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[키에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[자격 증명 모음으로 키 가져오기](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[키 백업](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[키 삭제](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[키 복원](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[키로 서명](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[키로 확인](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[키 래핑](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[키 래핑 취소](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[키로 암호화](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[키로 암호 해독](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[키 업데이트](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[자격 증명 모음에 키 나열](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[키 버전 나열](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[암호 만들기](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[비밀 가져오기](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[암호 업데이트](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[암호 삭제](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[자격 증명 모음에 암호 나열](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[암호 버전 나열](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Azure Monitor 로그 사용

Azure Monitor 로그에서 Key Vault 솔루션을 사용하여 Key Vault `AuditEvent` 로그를 검토할 수 있습니다. Azure Monitor 로그에서 로그 쿼리를 사용하여 데이터를 분석하고 필요한 정보를 가져옵니다. 

이를 설정하는 방법을 포함한 자세한 내용은 [Azure Monitor의 Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)를 참조하세요.

## <a name="next-steps"></a><a id="next"></a>다음 단계

.NET 웹 애플리케이션에서 Azure Key Vault를 사용하는 자습서는 [웹 애플리케이션에서 Azure Key Vault 사용](tutorial-net-create-vault-azure-web-app.md)을 참조하세요.

프로그래밍 참조는 [Azure Key Vault 개발자 가이드](developers-guide.md)를 참조하세요.

Azure Key Vault용 Azure PowerShell 1.0 cmdlet 목록은 [Azure Key Vault cmdlet](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)을 참조하세요.
