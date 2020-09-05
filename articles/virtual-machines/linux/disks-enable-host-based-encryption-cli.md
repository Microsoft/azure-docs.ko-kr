---
title: 호스트 Azure CLI 관리 디스크에서 암호화를 사용 하 여 종단 간 암호화 사용
description: 호스트의 암호화를 사용 하 여 Azure managed disks에서 종단 간 암호화를 사용 하도록 설정 합니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ff56654981ef69648b1fa7ad11a8681c887289f6
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816969"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Azure CLI를 사용 하 여 호스트에서 암호화를 사용 하 여 종단 간 암호화를 사용 하도록 설정 합니다.

호스트에서 암호화를 사용 하도록 설정 하면 VM 호스트에 저장 된 데이터는 미사용 및 저장소 서비스로 암호화 된 흐름으로 암호화 됩니다. 호스트의 암호화 및 기타 관리 되는 디스크 암호화 유형에 대 한 개념 정보는 호스트의 [암호화-VM 데이터에 대 한 종단 간 암호화](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)를 참조 하세요.

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>지원되는 지역

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>지원되는 VM 크기

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

VM 크기를 프로그래밍 방식으로 찾을 수도 있습니다. 프로그래밍 방식으로 검색 하는 방법에 대 한 자세한 내용은 [지원 되는 VM 크기 찾기](#finding-supported-vm-sizes) 섹션을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

호스트에서 Vm 또는 가상 머신 확장 집합에 대 한 암호화를 사용할 수 있으려면 구독에서 기능을 사용 하도록 설정 해야 합니다. 구독 Id를 사용 하 여에 전자 메일을 보내 encryptionAtHost@microsoft.com 구독에 대해 사용 하도록 설정 된 기능을 가져옵니다.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault 및 Diskset 만들기

기능을 사용 하도록 설정한 후에는 Azure Key Vault 및 Disk를 설정 해야 합니다 (아직 없는 경우).

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>예제

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>고객이 관리 하는 키를 사용 하 여 호스트에서 암호화를 사용 하 여 VM을 만듭니다. 

고객이 관리 하는 키를 사용 하 여 OS 및 데이터 디스크의 캐시를 암호화 하기 위해 앞에서 만든 DiskEncryptionSet의 리소스 URI를 사용 하 여 관리 디스크를 사용 하는 VM을 만듭니다. 임시 디스크는 플랫폼 관리 키를 사용 하 여 암호화 됩니다. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>플랫폼 관리 키를 사용 하 여 호스트에서 사용 되는 암호화를 사용 하 여 VM을 만듭니다. 

플랫폼 관리 키를 사용 하 여 OS/데이터 디스크 및 임시 디스크의 캐시를 암호화 하는 데 사용할 수 있는 암호화를 사용 하 여 VM을 만듭니다. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>호스트에서 암호화를 사용 하도록 VM을 업데이트 합니다. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>호스트에서 VM에 대 한 암호화 상태를 확인 합니다.

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>고객이 관리 하는 키를 사용 하 여 호스트에서 암호화를 사용 하 여 가상 머신 확장 집합을 만듭니다. 

앞에서 만든 DiskEncryptionSet의 리소스 URI를 사용 하 여 관리 디스크를 사용 하 여 가상 머신 확장 집합을 만들고, 고객이 관리 하는 키를 사용 하 여 OS 및 데이터 디스크의 캐시를 암호화 합니다. 임시 디스크는 플랫폼 관리 키를 사용 하 여 암호화 됩니다. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>플랫폼 관리 키를 사용 하 여 호스트에서 사용 되는 암호화를 사용 하 여 가상 머신 확장 집합을 만듭니다. 

플랫폼 관리 키를 사용 하 여 OS/데이터 디스크 및 임시 디스크의 캐시를 암호화 하는 데 사용할 수 있는 암호화를 사용 하 여 가상 머신 확장 집합을 만듭니다. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>호스트에서 암호화를 사용 하도록 가상 컴퓨터 확장 집합을 업데이트 합니다. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>호스트에서 가상 머신 확장 집합에 대 한 암호화 상태를 확인 합니다.

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
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
