---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: 0850bd22faf6300c1c87918b88bfb4e74b0e7c76
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720352"
---
Azure Disk Encryption은 [Azure CLI](/cli/azure) 및 [Azure PowerShell](/powershell/azure/new-azureps-module-az)을 통해 사용하고 관리할 수 있습니다. 이렇게 하려면 도구를 로컬에 설치하고 Azure 구독에 연결해야 합니다.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure)은 Azure 리소스를 관리하기 위한 명령줄 도구입니다. CLI는 데이터를 유연하게 쿼리하고, 장기 실행 작업을 비차단 프로세스로 지원하고, 쉽게 스크립팅할 수 있도록 설계되었습니다. [Azure CLI 설치](/cli/azure/install-azure-cli)의 단계에 따라 로컬에 설치할 수 있습니다.

[Azure CLI에서 Azure 계정에 로그인](/cli/azure/authenticate-azure-cli)하려면 [az login](/cli/azure/reference-index#az_login) 명령을 사용합니다.

```azurecli
az login
```

로그인할 테넌트를 선택하려면 다음을 사용합니다.
    
```azurecli
az login --tenant <tenant>
```

구독이 여러 개이고 특정 구독을 지정하려는 경우 [az account list](/cli/azure/account#az_account_list)를 사용하여 구독 목록을 가져오고 [az account set](/cli/azure/account#az_account_set)으로 지정합니다.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

자세한 내용은 [Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요. 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell Az 모듈](/powershell/azure/new-azureps-module-az)은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](../articles/azure-resource-manager/management/overview.md) 모델을 사용하는 cmdlet 집합을 제공합니다. 이 모듈은 브라우저에서 [Azure Cloud Shell](../articles/cloud-shell/overview.md)을 통해 사용하거나 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)의 지침에 따라 로컬 머신에 설치할 수 있습니다. 

이미 로컬에 설치되어있는 경우 최신 버전의 Azure PowerShell SDK 버전을 사용하여 Azure Disk Encryption을 구성해야 합니다. 최신 버전의 [Azure PowerShell 릴리스](https://github.com/Azure/azure-powershell/releases)를 다운로드합니다.

[Azure PowerShell을 사용하여 Azure 계정에 로그인](/powershell/azure/authenticate-azureps)하려면 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용합니다.

```powershell
Connect-AzAccount
```

여러 구독이 있으며 그 중에서 하나의 구독을 지정하려면 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) cmdlet을 사용하여 해당 구독을 나열한 다음, [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용합니다.

```powershell
Set-AzContext -Subscription <SubscriptionId>
```

[Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) cmdlet을 실행하면 올바른 구독이 선택되었는지 확인됩니다.

Azure Disk Encryption cmdlet이 설치되어 있는지 확인하려면 [Get-command](/powershell/module/microsoft.powershell.core/get-command) cmdlet을 사용합니다.
     
```powershell
Get-command *diskencryption*
```
자세한 내용은 [Azure PowerShell 시작](/powershell/azure/get-started-azureps)을 참조하세요.
