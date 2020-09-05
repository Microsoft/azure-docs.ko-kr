---
title: Windows 용 Azure Key Vault VM 확장
description: 가상 머신 확장을 사용하여 가상 머신에 Key Vault 비밀의 자동 새로 고침을 수행하는 에이전트를 배포합니다.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 70dcee1cce49c658a60e98821a3ce60ec443408a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932579"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Windows용 Key Vault 가상 머신 확장

Key Vault VM 확장은 Azure Key Vault에 저장된 인증서의 자동 새로 고침을 제공합니다. 특히 확장은 키 자격 증명 모음에 저장된 인증서에 대해 관찰된 목록을 모니터링하고, 변경 내용이 검색되면 해당 인증서를 검색 및 설치합니다. 이 문서에서는 Windows용 Key Vault VM 확장에 지원되는 플랫폼, 구성 및 배포 옵션에 대해 자세히 설명합니다. 

### <a name="operating-system"></a>운영 체제

Key Vault VM 확장은 다음 버전의 Windows를 지원 합니다.

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

### <a name="supported-certificate-content-types"></a>지원되는 인증서 콘텐츠 형식

- PKCS #12
- PEM

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Key Vault VM 확장에 대한 스키마를 보여 줍니다. 확장에는 보호 된 설정이 필요 하지 않습니다. 모든 설정이 공용 정보로 간주 됩니다. 확장에는 모니터링 되는 인증서의 목록, 폴링 빈도 및 대상 인증서 저장소가 필요 합니다. 특히 다음에 대한 내용을 설명합니다.  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> 관찰된 인증서 URL은 `https://myVaultName.vault.azure.net/secrets/myCertName` 형식이어야 합니다.
> 
> `/secrets` 경로가 프라이빗 키를 포함하여 전체 인증서를 반환하지만 `/certificates` 경로에서는 반환하지 않기 때문입니다. 인증서에 대한 자세한 내용은 여기에서 찾을 수 있습니다. [Key Vault 인증서](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> ' AuthenticationSettings ' 속성은 **사용자가 할당 한 id**를 가진 vm에만 **필요** 합니다.
> Key Vault에 대 한 인증에 사용할 id를 지정 합니다.


### <a name="property-values"></a>속성 값

| 속성 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | 문자열 |
| type | KeyVaultForWindows | 문자열 |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | 문자열 |
| certificateStoreName | MY | 문자열 |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine 또는 CurrentUser (대/소문자 구분) | 문자열 |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | 문자열 배열
| msiEndpoint | http://169.254.169.254/metadata/identity | 문자열 |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | 문자열 |


## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 배포 후에 인증서를 새로 고칠 필요가 있는 하나 이상의 가상 머신을 배포하는 경우 템플릿을 사용하는 것이 좋습니다. 확장은 개별 VM 또는 가상 머신 확장 집합에 배포할 수 있습니다. 스키마와 구성은 두 템플릿 형식 모두에 공통적으로 적용됩니다. 

가상 머신 확장에 대한 JSON 구성은 템플릿의 가상 머신 리소스 조각, 특히 가상 머신 템플릿의 `"resources": []` 개체 및 `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` 개체의 가상 머신 확장 집합 내에 중첩되어야 합니다.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Azure PowerShell 배포
> [!WARNING]
> PowerShell 클라이언트는 `\` settings.js에를 추가 하 여 `"` akvvm_service 오류로 인해 실패 하는 경우가 많습니다. `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Azure PowerShell은 기존 가상 머신 또는 가상 머신 확장 집합에 Key Vault VM 확장을 배포하는 데 사용할 수 있습니다. 

* VM에 확장을 배포하려면 다음과 같습니다.
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 가상 머신 확장 집합에 확장 배포:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI는 기존 가상 머신 또는 가상 머신 확장 집합에 Key Vault VM 확장을 배포하는 데 사용할 수 있습니다. 
 
* VM에 확장을 배포하려면 다음과 같습니다.
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* 가상 머신 확장 집합에 확장 배포:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

다음 제한 사항/요구 사항에 주의하세요.
- Key Vault 제한 사항:
  - 배포 시점에 있어야 합니다. 
  - MSI를 사용 하 여 VM/VMSS Id에 대 한 Key Vault 액세스 정책을 설정 합니다.


## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure PowerShell 또는 Azure Portal을 통해 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure PowerShell을 사용하여 다음 명령을 실행합니다.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

확장 실행 출력은 다음 파일에 기록됩니다.

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
