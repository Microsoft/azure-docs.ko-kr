---
title: 관리 id VM 확장 사용 중지-Azure AD
description: VM 확장 사용을 중지 하 고 인증에 IMDS (Azure Instance Metadata Service) 사용을 시작 하는 단계별 지침을 설명 합니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: barclayn
ms.openlocfilehash: 5b298767f9814f76dd606bab29bd0b245dad6937
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260189"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>가상 컴퓨터 관리 id 확장 사용을 중지 하 고 Azure Instance Metadata Service 사용을 시작 하는 방법

## <a name="virtual-machine-extension-for-managed-identities"></a>관리 id에 대 한 가상 머신 확장

관리 id에 대 한 가상 머신 확장은 가상 머신 내에서 관리 되는 id에 대 한 토큰을 요청 하는 데 사용 됩니다. 워크플로는 다음 단계로 구성 됩니다.

1. 첫째, 리소스 내 작업은 로컬 끝점을 호출 `http://localhost/oauth2/token` 하 여 액세스 토큰을 요청 합니다.
2. 그런 다음 가상 머신 확장은 관리 되는 id에 대 한 자격 증명을 사용 하 여 Azure AD에서 액세스 토큰을 요청 합니다. 
3. 액세스 토큰은 호출자에 게 반환 되며, Azure Key Vault 또는 Azure Storage와 같이 Azure AD 인증을 지 원하는 서비스에 인증 하는 데 사용할 수 있습니다.

다음 섹션에서 설명 하는 몇 가지 제한 사항으로 인해 관리 되는 id VM 확장은 IMDS (Azure Instance Metadata Service)에서 동일한 끝점을 사용 하기 위해 더 이상 사용 되지 않습니다.

### <a name="provision-the-extension"></a>확장 프로 비전 

관리 id가 있는 가상 머신 또는 가상 머신 확장 집합을 구성 하는 경우 필요에 따라 `-Type` [AzVMExtension](/powershell/module/az.compute/set-azvmextension) cmdlet의 매개 변수를 사용 하 여 Azure resources VM 확장에 대 한 관리 되는 id를 프로 비전 하도록 선택할 수 있습니다. `ManagedIdentityExtensionForWindows` `ManagedIdentityExtensionForLinux` 가상 컴퓨터의 형식에 따라 또는를 전달 하 고 매개 변수를 사용 하 여 이름을 지정할 수 있습니다 `-Name` . `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 엔드포인트에서 사용하는 포트를 지정합니다.

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Azure Resource Manager 또한 템플릿에 대 한 섹션에 다음 JSON을 추가 하 여 VM 확장을 프로 비전 할 수 있습니다 `resources` ( `ManagedIdentityExtensionForLinux` Linux 버전의 이름 및 형식 요소에 사용).

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
    "apiVersion": "2018-06-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.ManagedIdentity",
        "type": "ManagedIdentityExtensionForWindows",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "port": 50342
        }
    }
}
```
    
    
Virtual machine scale sets를 사용 하 여 작업 하는 경우 [AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet을 사용 하 여 Azure 리소스에 대 한 관리 id 가상 머신 확장 집합 확장을 프로 비전 할 수도 있습니다. `ManagedIdentityExtensionForWindows` `ManagedIdentityExtensionForLinux` 가상 머신 확장 집합의 유형에 따라 또는 중 하나를 전달 하 고 매개 변수를 사용 하 여 이름을 지정할 수 있습니다 `-Name` . `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 엔드포인트에서 사용하는 포트를 지정합니다.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Azure Resource Manager 배포 템플릿을 사용 하 여 가상 머신 확장 집합 확장을 프로 비전 하려면 템플릿의 섹션에 다음 JSON을 추가 `extensionpProfile` `ManagedIdentityExtensionForLinux` 합니다 (Linux 버전의 이름 및 유형 요소에 사용).

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "ManagedIdentityWindowsExtension",
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                },
                "protectedSettings": {}
            }
        }
```

DNS 조회 오류로 인해 가상 컴퓨터 확장의 프로 비전이 실패할 수 있습니다. 이 경우 가상 컴퓨터를 다시 시작 하 고 다시 시도 하세요. 

### <a name="remove-the-extension"></a>확장 제거 
확장 프로그램을 제거 하려면 `-n ManagedIdentityExtensionForWindows` `-n ManagedIdentityExtensionForLinux` [az vm extension delete](/cli/azure/vm/)를 사용 하 여 가상 컴퓨터의 형식에 따라 또는 스위치를 사용 하거나, Azure CLI를 사용 하 여 가상 컴퓨터 확장 집합에 대 한 [az vmss Extension delete](/cli/azure/vmss) 를 사용 하거나 `Remove-AzVMExtension` Powershell을 사용 합니다.

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>가상 머신 확장을 사용 하 여 토큰 획득

다음은 Azure 리소스 VM 확장 끝점에 대 한 관리 되는 id를 사용 하는 샘플 요청입니다.

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 요소 | 설명 |
| ------- | ----------- |
| `GET` | HTTP 동사는 엔드포인트에서 데이터를 검색한다는 것을 나타냅니다. 이 경우에는 OAuth 액세스 토큰입니다. | 
| `http://localhost:50342/oauth2/token` | Azure 리소스에 대한 관리 ID 엔드포인트입니다. 여기서 50342는 기본 포트이며 구성 가능합니다. |
| `resource` | 쿼리 문자열 매개 변수는 대상 리소스의 앱 ID URI를 나타냅니다. 또한 발급된 토큰의 `aud` (대상) 클레임에서 표시됩니다. 이 예제에서는 Azure Resource Manager에 액세스할 수 있는 토큰을 요청합니다. 여기에는 `https://management.azure.com/`이라는 앱 ID URI가 포함됩니다. |
| `Metadata` | SSRF(서버 쪽 요청 위조) 공격에 대한 완화 수단으로 Azure 리소스에 대한 관리 ID에서 HTTP 요청 헤더 필드가 필요합니다. 이 값은 모두 소문자이며 "true"로 설정되어야 합니다.|
| `object_id` | (선택 사항) 토큰을 원하는 관리 ID의 object_id를 나타내는 쿼리 문자열 매개 변수입니다. VM에 여러 사용자 할당 관리 ID가 있는 경우 필수입니다.|
| `client_id` | (선택 사항) 토큰을 원하는 관리 ID의 client_id를 나타내는 쿼리 문자열 매개 변수입니다. VM에 여러 사용자 할당 관리 ID가 있는 경우 필수입니다.|


샘플 응답:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 요소 | Description |
| ------- | ----------- |
| `access_token` | 요청된 액세스 토큰입니다. 보안이 설정된 REST API를 호출할 때 토큰은 호출자를 인증하는 API를 허용하는 "전달자" 토큰으로 `Authorization` 요청 헤더 필드에 포함됩니다. | 
| `refresh_token` | Azure 리소스에 대한 관리 ID에서 사용되지 않습니다. |
| `expires_in` | 액세스 토큰의 발급 시간부터 만료 이전까지 계속 유효한 시간(초)입니다. 발급 시간은 토큰의 `iat` 클레임에서 확인할 수 있습니다. |
| `expires_on` | 액세스 토큰이 만료되는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `exp` 클레임에 해당함). |
| `not_before` | 액세스 토큰이 적용되고 허용될 수 있는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `nbf` 클레임에 해당함). |
| `resource` | 액세스 토큰이 요청되는 리소스는 요청의 `resource` 쿼리 문자열 매개 변수와 일치합니다. |
| `token_type` | 토큰의 형식은 "전달자" 액세스 토큰입니다. 즉, 리소스가 이 토큰의 전달자에 액세스 권한을 제공할 수 있습니다. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>가상 머신 확장 문제 해결 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>오류가 발생 한 후 가상 머신 확장을 다시 시작 합니다.

Windows 및 특정 버전의 Linux에서 확장이 중지한 경우 다음 cmdlet 사용하여 수동으로 다시 시작할 수 있습니다.

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

위치: 
- Windows 용 확장 이름 및 형식: `ManagedIdentityExtensionForWindows`
- Linux에 대 한 확장 이름 및 형식: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Azure 리소스에 대한 관리 ID 확장에 대해 스키마 내보내기를 시도할 때 "자동화 스크립트"가 실패함

가상 컴퓨터에서 Azure 리소스에 대 한 관리 id를 사용 하는 경우 가상 컴퓨터 또는 해당 리소스 그룹에 대해 "Automation 스크립트" 기능을 사용 하려고 하면 다음 오류가 표시 됩니다.

![Azure 리소스에 대한 관리 ID 자동화 스크립트 내보내기 오류](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Azure 리소스에 대 한 관리 id 가상 머신 확장은 현재 해당 스키마를 리소스 그룹 템플릿으로 내보내는 기능을 지원 하지 않습니다. 결과적으로 생성된 템플릿은 리소스에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정하는 구성 매개 변수를 표시하지 않습니다. [템플릿을 사용 하 여 azure 가상 머신에서 azure 리소스에 대 한 관리 되는 Id 구성](qs-configure-template-windows-vm.md)의 예제를 수행 하 여 이러한 섹션을 수동으로 추가할 수 있습니다.

Azure 리소스에 대 한 관리 되는 id 가상 머신 확장에 스키마 내보내기 기능을 사용할 수 있는 경우 (1 월 2019에서 사용 중단 예정) [VM 확장을 포함 하는 리소스 그룹 내보내기](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)에 나열 됩니다.

## <a name="limitations-of-the-virtual-machine-extension"></a>가상 컴퓨터 확장의 제한 사항 

가상 컴퓨터 확장을 사용 하는 데는 몇 가지 주요 제한 사항이 있습니다. 

 * 가장 심각한 제한 사항은 토큰을 요청 하는 데 사용 되는 자격 증명이 가상 컴퓨터에 저장 된다는 것입니다. 가상 컴퓨터를 성공적으로 위반 하는 공격자는 자격 증명을 탈취 수 있습니다. 
 * 또한 가상 머신 확장은 여러 Linux 배포판에서 지원 되지 않으며, 이러한 각 배포에서 확장을 수정, 빌드 및 테스트 하는 데 상당한 개발 비용이 듭니다. 현재 지원 되는 Linux 배포판은 다음과 같습니다. 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * 가상 컴퓨터 확장도 프로 비전 해야 하므로 관리 되는 id를 사용 하 여 가상 컴퓨터를 배포 하는 경우 성능에 영향을 줍니다. 
 * 마지막으로 가상 머신 확장은 가상 컴퓨터당 32 사용자 할당 관리 id만 지원할 수 있습니다. 

## <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

[IMDS (Azure Instance Metadata Service)](../../virtual-machines/windows/instance-metadata-service.md) 는 가상 머신을 관리 하 고 구성 하는 데 사용할 수 있는 실행 중인 가상 머신 인스턴스에 대 한 정보를 제공 하는 REST 끝점입니다. 끝점은 `169.254.169.254` 가상 머신 내 에서만 액세스할 수 있는 잘 알려진 라우팅할 수 없는 IP 주소 ()에서 사용할 수 있습니다.

Azure IMDS를 사용 하 여 토큰을 요청 하는 경우 몇 가지 이점이 있습니다. 

1. 서비스가 가상 컴퓨터 외부에 있으므로 관리 되는 id에 사용 되는 자격 증명이 더 이상 가상 컴퓨터에 존재 하지 않습니다. 대신, Azure 가상 머신의 호스트 컴퓨터에서 호스트 되 고 보호 됩니다.   
2. Azure IaaS에서 지원 되는 모든 Windows 및 Linux 운영 체제는 관리 되는 id를 사용할 수 있습니다.
3. VM 확장을 더 이상 프로 비전 할 필요가 없으므로 배포는 더 빠르고 간편 합니다.
4. IMDS 끝점을 사용 하면 최대 1000 사용자 할당 관리 id를 단일 가상 머신에 할당할 수 있습니다.
5. 가상 머신 확장을 사용 하는 것과는 반대로 IMDS를 사용 하는 요청에 대 한 중요 한 변경 내용이 없으므로 현재 가상 머신 확장을 사용 하는 기존 배포를 통해 매우 간단 하 게 포트를 사용할 수 있습니다.

이러한 이유로 가상 머신 확장이 사용 되지 않는 경우 Azure IMDS 서비스는 토큰을 요청 하는 서 실제로 방법이 됩니다. 


## <a name="next-steps"></a>다음 단계

* [Azure 가상 머신에서 Azure 리소스에 대 한 관리 id를 사용 하 여 액세스 토큰을 가져오는 방법](how-to-use-vm-token.md)
* [Azure Instance Metadata Service](../../virtual-machines/windows/instance-metadata-service.md)