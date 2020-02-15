---
title: '빠른 시작: PowerShell을 사용하여 Key Vault에서 비밀 설정 및 검색'
description: 이 빠른 시작에서는 PowerShell을 사용하여 Azure Key Vault에서 암호를 생성, 검색 및 삭제하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 1e2c4db66046b09b354753b7aaee617095bba552
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77151482"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Key Vault는 보안 비밀 저장소로 작동하는 클라우드 서비스입니다. 키, 암호, 인증서 및 기타 비밀을 안전하게 저장할 수 있습니다. Key Vault에 대한 자세한 내용을 보려면 [개요](key-vault-overview.md)를 살펴보세요. 이 빠른 시작에서는 PowerShell를 사용하여 Key Vault를 만듭니다. 그런 다음, 새로 만든 자격 증명 모음에 비밀을 저장합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

로컬에서 PowerShell을 설치하고 사용하도록 선택하려는 경우 이 자습서를 진행하려면 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. `$PSVersionTable.PSVersion` 명령을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`를 실행하여 Azure와 연결해야 합니다.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>주요 자격 증명 모음 만들기

다음으로 Key Vault를 만듭니다. 이 단계를 수행할 때는 몇 가지 정보가 필요합니다.

이 빠른 시작 전체에서 Key Vault의 이름으로 "Contoso KeyVault2"를 사용하되, 고유한 이름을 사용해야 합니다.

- **자격 증명 모음 이름**은 Contoso-Vault2입니다.
- **리소스 그룹 이름**은 ContosoResourceGroup입니다.
- **위치**는 미국 동부입니다.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

이 cmdlet의 출력에는 새로 만든 Key Vault의 속성이 표시됩니다. 아래에 나열된 두 개의 속성을 기록합니다.

* **자격 증명 모음 이름**: 이 예제에서는 **Contoso-Vault2**입니다. 다른 키 자격 증명 모음 cmdlet에 대해 이 이름을 사용합니다.
* **자격 증명 모음 URI**: 이 예제에서는 https://Contoso-Vault2.vault.azure.net/ 입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 애플리케이션은 URI를 사용해야 합니다.

자격 증명 모음을 만든 후 사용자의 Azure 계정은 이 새 자격 증명 모음에 대해 모든 작업을 수행할 수 있는 유일한 계정입니다.

![Key Vault 만들기 명령이 완료된 후 출력](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Key Vault에 비밀 추가

자격 증명 모음에 비밀을 추가하려면 몇 가지 단계만 수행하면 됩니다. 이 경우 애플리케이션에서 사용할 수 있는 암호를 추가합니다. 암호는 **ExamplePassword**로 지정되며 **hVFkk965BuUv** 값을 저장합니다.

먼저 다음 명령을 입력하여 **hVFkk965BuUv** 값을 보안 문자열로 변환합니다.

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

그런 다음, 아래의 PowerShell 명령을 입력하여 Key Vault에 **hVFkk965BuUv**라는 값을 가진 **ExamplePassword**라는 비밀을 만듭니다.

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'Contoso-Vault2' -Name 'ExamplePassword' -SecretValue $secretvalue
```

비밀에 들어 있는 값을 일반 텍스트로 보려면:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contoso-Vault2" -name "ExamplePassword").SecretValueText
```

지금까지 Key Vault를 만들고 비밀을 저장하고, 검색했습니다.

## <a name="clean-up-resources"></a>리소스 정리

 이 컬렉션의 다른 빠른 시작과 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다. 다른 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, Key Vault 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Key Vault를 만들어 비밀을 저장했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](key-vault-overview.md) 참조
- [Azure PowerShell Key Vault cmdlets](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault)에 대한 참조를 참조하세요.
- [키, 비밀 및 인증서](about-keys-secrets-and-certificates.md) 알아보기
- [Azure Key Vault 모범 사례](key-vault-best-practices.md) 검토
