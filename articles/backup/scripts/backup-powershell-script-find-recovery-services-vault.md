---
title: PowerShell 스크립트 - 스토리지 계정에 대한 자격 증명 모음 찾기
description: Azure PowerShell 스크립트를 사용하여 스토리지 계정이 등록된 Recovery Services 자격 증명 모음을 찾는 방법에 대해 알아봅니다.
ms.topic: sample
ms.date: 1/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40859c1ea05210d27fcdcf33ba9d4f961965ea22
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89075701"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>스토리지 계정이 등록된 Recovery Services 자격 증명 모음을 찾는 PowerShell 스크립트

이 스크립트를 사용하면 스토리지 계정이 등록된 Recovery Services 자격 증명 모음을 찾을 수 있습니다.

## <a name="sample-script"></a>샘플 스크립트

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>스크립트를 실행하는 방법

1. 위의 스크립트를 사용자가 선택한 이름으로 머신에 저장합니다. 이 예제에서는 *FindRegisteredStorageAccount.ps1*로 저장했습니다.
2. 다음 매개 변수를 제공하여 스크립트를 실행합니다.

    * **-ResourceGroupName** - 스토리지 계정의 리소스 그룹
    * **-StorageAccountName** - 스토리지 계정 이름
    * **-SubscriptionID** - 스토리지 계정이 있는 구독 ID입니다.

다음 예제에서는 *afsaccount* 스토리지 계정이 등록된 Recovery Services 자격 증명 모음을 찾으려고 시도합니다.

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>출력

출력에는 스토리지 계정이 등록된 Recovery Services 자격 증명 모음의 전체 경로가 표시됩니다. 샘플 출력은 다음과 같습니다.

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>다음 단계

[Azure Portal에서 Azure File 공유를 백업](../backup-afs.md)하는 방법 알아보기
