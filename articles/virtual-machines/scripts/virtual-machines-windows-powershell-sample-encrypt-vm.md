---
title: Azure PowerShell 스크립트 샘플 - Windows VM 암호화 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - Windows VM 암호화
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.openlocfilehash: a75ff2d1c03abc0032c647f6cc5ec4721d359a02
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924501"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Azure PowerShell을 사용하여 Windows 가상 머신 암호화

이 스크립트는 보안 Azure Key Vault, 암호화 키, Azure Active Directory 서비스 주체 및 Windows VM(가상 머신)을 만듭니다. 그런 다음 Key Vault의 암호화 키 및 서비스 주체 자격 증명을 사용하여 VM을 암호화합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) | 암호화 키와 같은 보안 데이터를 저장하기 위한 Azure Key Vault를 만듭니다. |
| [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) | Key Vault에서 암호화 키를 만듭니다. |
| [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) | 암호화 키를 안전하게 인증하고 암호화 키에 대한 액세스를 제어하기 위한 Azure Active Directory 서비스 주체를 만듭니다. |
| [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | 서비스 주체에 암호화 키에 대한 액세스 권한을 부여하기 위해 Key Vault에 대한 사용 권한을 설정합니다. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | 가상 머신을 만들고 네트워크 카드, 가상 네트워크, 서브넷 및 네트워크 보안 그룹에 연결합니다. 이 명령은 포트 80을 열고 관리자 자격 증명도 설정합니다. |
| [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault) | Key Vault에 대한 필수 정보를 가져옵니다. |
| [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) | 서비스 주체 자격 증명 및 암호화 키를 사용하여 VM에 대해 암호화를 사용하도록 설정합니다. |
| [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus) | VM 암호화 프로세스의 상태를 표시합니다. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Windows VM 설명서](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
