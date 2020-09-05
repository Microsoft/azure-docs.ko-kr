---
title: Azure PowerShell-VM 호스트에서 종단 간 암호화를 사용 하도록 설정 합니다.
description: 호스트에서 암호화를 사용 하 여 Azure Vm에 대 한 종단 간 암호화를 사용 하도록 설정 하는 방법입니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6a352ecc2d2b02f03e2b55f7c5896ac905077921
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814796"
---
# <a name="use-the-azure-powershell-module-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Azure PowerShell 모듈을 사용 하 여 호스트에서 암호화를 사용 하 여 종단 간 암호화를 사용 하도록 설정 합니다.

호스트에서 암호화를 사용 하도록 설정 하면 VM 호스트에 저장 된 데이터는 미사용 및 저장소 서비스로 암호화 된 흐름으로 암호화 됩니다. 호스트의 암호화 및 기타 관리 되는 디스크 암호화 유형에 대 한 개념 정보는 호스트의 [암호화-VM 데이터에 대 한 종단 간 암호화](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)를 참조 하세요.

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>지원되는 지역

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>지원되는 VM 크기

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

VM 크기를 프로그래밍 방식으로 찾을 수도 있습니다. 프로그래밍 방식으로 검색 하는 방법에 대 한 자세한 내용은 [지원 되는 VM 크기 찾기](#finding-supported-vm-sizes) 섹션을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

호스트에서 Vm 또는 가상 머신 확장 집합에 대 한 암호화를 사용할 수 있으려면 구독에서 기능을 사용 하도록 설정 해야 합니다. 구독에 사용하도록 설정된 기능을 가져오려면 구독 ID를 사용하여 이메일을 encryptionAtHost@microsoft .com에 보냅니다.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault 및 Diskset 만들기

기능을 사용 하도록 설정한 후에는 Azure Key Vault 및 Disk를 설정 해야 합니다 (아직 없는 경우).

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>VM 및 가상 머신 확장 집합에 연결 된 디스크에 대해 호스트에서 암호화 사용

API 버전 **2020-06-01** 이상을 사용 하 여 vm 또는 가상 머신 확장 집합의 securityprofile에 새 속성 EncryptionAtHost를 설정 하 여 호스트에서 암호화를 사용 하도록 설정할 수 있습니다.

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>샘플 스크립트

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>호스트에서 고객이 관리 하는 키를 사용 하 여 VM에 연결 된 디스크에 대 한 암호화 사용

이전에 만든 DiskEncryptionSet의 리소스 URI를 사용 하 여 관리 디스크를 사용 하는 VM을 만듭니다.

,,,,, 및를 바꾼 `<yourPassword>` `<yourVMName>` `<yourVMSize>` `<yourDESName>` `<yoursubscriptionID>` `<yourResourceGroupName>` `<yourRegion>` 다음 스크립트를 실행 합니다.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-diskEncryptionSetId "/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" `
-region "<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>플랫폼 관리 키를 사용 하 여 VM에 연결 된 디스크의 호스트에서 암호화 사용

`<yourPassword>`,, `<yourVMName>` , 및를 바꾼 `<yourVMSize>` `<yourResourceGroupName>` `<yourRegion>` 다음 스크립트를 실행 합니다.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-region "<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>지원 되는 VM 크기 찾기

레거시 VM 크기는 지원 되지 않습니다. 다음 중 하나를 수행 하 여 지원 되는 VM 크기의 목록을 찾을 수 있습니다.

[리소스 SKU API](/rest/api/compute/resourceskus/list) 를 호출 하 고 `EncryptionAtHostSupported` 기능이 **True**로 설정 되었는지 확인 합니다.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

또는 [AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) PowerShell cmdlet을 호출 합니다.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>다음 단계

이러한 리소스를 만들고 구성 했으므로 이제 이러한 리소스를 사용 하 여 관리 디스크를 보호할 수 있습니다. 다음 링크에는 관리 디스크를 보호 하는 데 사용할 수 있는 각각의 시나리오를 포함 하는 예제 스크립트가 포함 되어 있습니다.

[Azure Resource Manager 템플릿 샘플](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
