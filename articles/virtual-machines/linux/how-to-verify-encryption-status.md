---
title: Linux의 암호화 상태 확인 - Azure Disk Encryption
description: 이 문서에서는 플랫폼 및 OS 수준에서 암호화 상태를 확인하는 방법을 설명합니다.
author: kailashmsft
ms.service: security
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e39a230c71cf48422220768adfa8de91cbaa6692
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072845"
---
# <a name="verify-encryption-status-for-linux"></a>Linux의 암호화 상태 확인 

이 문서의 범위는 Azure Portal, PowerShell, Azure CLI 또는 VM(가상 머신)의 운영 체제를 사용하여 가상 머신의 암호화 상태를 확인하는 방법을 포함합니다. 

암호화 상태는 다음과 같은 방법을 통해 암호화 중에 또는 후에 확인할 수 있습니다.

- 특정 VM에 연결된 디스크 확인. 
- 디스크의 연결 여부와 관계없이 각 디스크의 암호화 설정 쿼리.

이 시나리오는 Azure Disk Encryption 이중 패스 및 단일 패스 확장에 적용됩니다. Linux 배포는 이 시나리오의 유일한 환경입니다.

>[!NOTE] 
>문서 전체에서 변수가 사용됩니다. 상황에 맞게 변수를 바꾸세요.

## <a name="portal"></a>포털

Azure Portal의 **확장** 섹션의 목록에서 Azure Disk Encryption 확장을 선택합니다. **상태 메시지**의 정보는 현재 암호화 상태를 나타냅니다.

![상태, 버전, 상태 메시지가 강조 표시된 포털 확인](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

확장 목록에 해당하는 Azure Disk Encryption 확장 버전이 표시됩니다. 버전 0.x는 Azure Disk Encryption 이중 패스에 대응되고, 버전 1.x는 Azure Disk Encryption 단일 패스에 대응됩니다.

확장을 선택하고 **자세한 상태 보기**를 선택하여 자세한 정보를 볼 수 있습니다. 암호화 프로세스의 자세한 상태가 JSON 형식으로 표시됩니다.

![“자세한 상태 보기” 링크가 강조 표시된 포털 확인](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![JSON 형식으로 된 자세한 상태](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

암호화 상태를 확인하는 또 다른 방법은 **디스크 설정** 섹션을 살펴보는 것입니다.

![OS 디스크 및 데이터 디스크의 암호화 상태](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> 이 상태는 디스크가 실제로 OS 수준에서 암호화되었음을 나타내는 것이 아니라 디스크에 암호화 설정이 스탬프되어 있음을 나타냅니다.
>
> 디스크는 그 설계상 먼저 스탬프가 적용되고 나중에 암호화됩니다. 암호화 프로세스가 실패하면 해당 디스크는 스탬프는 적용되었으나 암호화되지는 않을 수 있습니다. 
>
> 디스크가 실제로 암호화되었는지 확인하려면 OS 수준에서 각 디스크의 암호화를 이중으로 확인할 수 있습니다.

## <a name="powershell"></a>PowerShell

다음 PowerShell 명령을 사용하여 암호화된 VM의 일반 암호화 상태를 확인할 수 있습니다.

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![PowerShell의 일반 암호화 상태](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

다음 PowerShell 명령을 사용하여 각 디스크의 암호화 설정을 캡처할 수 있습니다.

### <a name="single-pass"></a>단일 패스
단일 패스에서 암호화 설정은 각 디스크(OS 및 데이터)에 스탬프됩니다. 다음과 같이 단일 패스에서 OS 디스크의 암호화 설정을 캡처할 수 있습니다.

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![OS 디스크의 암호화 설정](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

디스크에 스탬프된 암호화 설정이 없으면 출력이 비어 있게 됩니다.

![빈 출력](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

다음 명령을 사용하여 데이터 디스크의 암호화 설정을 캡처합니다.

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![데이터 디스크의 암호화 설정](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>이중 패스
이중 패스에서 암호화 설정은 개별 디스크가 아니라 VM 모델에 스탬프됩니다.

암호화 설정이 이중 패스에 스탬프되었는지 확인하려면 다음 명령을 사용합니다.

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![이중 패스의 암호화 설정](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>연결되지 않은 디스크

VM에 연결되지 않은 디스크의 암호화 설정을 확인합니다.

### <a name="managed-disks"></a>관리 디스크
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>Azure CLI

다음 Azure CLI 명령을 사용하여 암호화된 VM의 일반 암호화 상태를 확인할 수 있습니다.

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Azure CLI에서 가져온 일반 암호화 상태 ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>단일 패스
다음 Azure CLI 명령을 사용하여 각 디스크의 암호화 설정을 확인할 수 있습니다.

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![데이터 암호화 설정](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> 디스크에 스탬프된 암호화 설정이 없는 경우 **디스크가 암호화되지 않음** 텍스트가 표시됩니다.

다음 명령을 사용하여 자세한 상태 및 암호화 설정을 가져옵니다.

OS 디스크:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OS 디스크의 자세한 상태 및 암호화 설정](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

데이터 디스크:

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![데이터 디스크의 자세한 상태 및 암호화 설정](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>이중 패스

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Azure CLI를 통한 이중 패스의 일반 암호화 설정](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

OS 디스크의 VM 모델 스토리지 프로필에서 암호화 설정을 확인할 수도 있습니다.

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Azure CLI를 통한 이중 패스의 VM 프로필](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>연결되지 않은 디스크

VM에 연결되지 않은 디스크의 암호화 설정을 확인합니다.

### <a name="managed-disks"></a>관리 디스크

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>관리되지 않는 디스크

비관리 디스크는 Azure 스토리지 계정에 페이지 Blob으로 저장된 VHD 파일입니다.

특정 디스크에 대한 세부 정보를 가져오려면 다음을 제공해야 합니다.

- 디스크를 포함하는 스토리지 계정의 ID.
- 해당 스토리지 계정의 연결 문자열.
- 디스크를 저장하는 컨테이너의 이름.
- 디스크 이름.

다음 명령은 모든 스토리지 계정의 모든 ID를 나열합니다.

```bash
az storage account list --query [].[id] -o tsv
```
스토리지 계정 ID는 다음과 같은 형식으로 나열됩니다.

/subscriptions/ \<subscription id> /Ssourceg/ \<resource group name> /providers/Microsoft.Storage/storageAccounts/\<storage account name>

적절한 ID를 선택하고 변수에 저장합니다.
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

다음 명령은 하나의 특정 스토리지 계정의 연결 문자열을 가져와서 변수에 저장합니다.

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

다음 명령은 스토리지 계정에 있는 모든 컨테이너를 나열합니다.
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
디스크에 사용되는 컨테이너는 일반적으로 “vhd”로 지정됩니다.

컨테이너 이름을 변수에 저장합니다. 
```bash
ContainerName="name of the container"
```

다음 명령을 사용하여 특정 컨테이너의 모든 Blob을 나열합니다.
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
쿼리하려는 디스크를 선택하고 그 이름을 변수에 저장합니다.
```bash
DiskName="diskname.vhd"
```
디스크 암호화 설정 쿼리:
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>운영 체제
OS 디스크가 아니라 데이터 디스크 파티션이 암호화되었는지 확인합니다.

암호화된 파티션 또는 디스크는 **crypt** 형식으로 표시됩니다. 암호화되지 않은 경우에는 **part/disk** 형식으로 표시됩니다.

``` bash
lsblk
```

![파티션의 OS crypt 계층](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

다음 **lsblk** 변형을 사용하여 더 자세한 정보를 얻을 수 있습니다. 

확장 프로그램에 의해 탑재되는 **crypt** 형식 계층을 볼 수 있습니다. 다음 예제에서는 **crypto\_LUKS FSTYPE**을 갖는 논리 볼륨과 일반 디스크를 보여 줍니다.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![논리 볼륨 및 일반 디스크의 OS crypt 계층](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

추가 단계로, 데이터 디스크에 로드된 키가 있는지 여부를 확인할 수 있습니다.

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

**crypt**로 나열된 **dm** 디바이스가 있는지도 확인할 수 있습니다.

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)
