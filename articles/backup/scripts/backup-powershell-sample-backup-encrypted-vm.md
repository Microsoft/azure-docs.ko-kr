---
title: PowerShell 스크립트 샘플 - Azure VM 백업
description: 이 문서에서는 Azure PowerShell 스크립트 샘플을 사용하여 Azure 가상 머신을 백업하는 방법을 알아봅니다.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: c45ee26bef0f9afbb0b9d03f1dcafee21a894f8a
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757560"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>PowerShell을 사용하여 암호화된 Azure 가상 머신 백업

이 스크립트는 암호화된 Azure 가상 머신에 대한 GRS(지역 중복 스토리지)를 사용하여 Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음에 기본 보호 정책이 적용됩니다. 이 정책은 가상 머신에 대한 일별 백업을 생성하고 30일 동안 각 백업을 유지합니다. 또한 스크립트는 가상 머신에 대한 초기 복구 지점을 트리거하고 해당 복구 지점을 365일 동안 유지합니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | 백업을 저장할 Recovery Services 자격 증명 모음을 만듭니다. |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Recovery Services 자격 증명 모음에 백업 스토리지 속성을 설정합니다. |
| [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Recovery Services 자격 증명 모음에서 일정 정책과 보존 정책을 사용하여 보호 정책을 만듭니다. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | 서비스 주체에 암호화 키에 대한 액세스 권한을 부여하기 위해 Key Vault에 대한 사용 권한을 설정합니다. |
| [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | 지정된 백업 보호 정책을 사용하여 항목에 대한 백업을 활성화합니다. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| 기존 백업 보호 정책을 수정합니다. |
| [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | 백업 일정에 연결되지 않은 보호된 Azure Backup 항목의 백업을 시작합니다. |
| [Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Azure Backup 작업이 완료될 때까지 기다립니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/new-azureps-module-az)를 참조하세요.
