---
title: Azure Policy를 사용하여 VM 확장 설치 제한 | Microsoft Docs
description: Azure Policy를 사용하여 VM 확장 배포를 제한합니다.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: 1f71276c25e3ec1e5791d9b35f89aa95190c6afd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543260"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Azure Policy를 사용하여 Linux VM의 확장 설치 제한

Linux VM에서 특정 확장을 사용하거나 설치하지 못하도록 하려면 CLI를 사용하여 리소스 그룹 내의 VM 확장을 제한하는 Azure 정책을 만들 수 있습니다. 

이 자습서에서는 지속적으로 최신 버전으로 업데이트되는 Azure Cloud Shell 내의 CLI를 사용합니다. Azure CLI를 로컬에서 실행하려면 버전 2.0.26 이상을 설치해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="create-a-rules-file"></a>규칙 파일 만들기

설치할 수 있는 확장을 제한하려면 확장을 식별하는 논리를 제공하는 [규칙](../../governance/policy/concepts/definition-structure.md#policy-rule)이 있어야 합니다.

이 예제에서는 Azure Cloud Shell에서 규칙 파일을 만들어 ‘Microsoft.OSTCExtensions’에 의해 게시된 확장 설치를 거부하는 방법을 보여 줍니다. 그러나 CLI에서 로컬로 작업하는 경우 로컬 파일을 만들고 경로(~/clouddrive)를 컴퓨터에 있는 로컬 파일의 경로로 바꿀 수도 있습니다.

[bash Cloud Shell](https://shell.azure.com/bash)에서 다음을 입력합니다.

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

다음 .json을 복사하여 파일에 붙여넣습니다.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

완료되면 **Esc** 키를 누르고 **:wq**를 입력하여 파일을 저장하고 닫습니다.


## <a name="create-a-parameters-file"></a>매개 변수 파일 만들기

또한 블록에 확장 목록을 전달하는 데 사용할 구조를 만드는 [매개 변수](../../governance/policy/concepts/definition-structure.md#parameters)도 필요합니다. 

이 예제에서는 Cloud Shell에서 Linux VM용 매개 변수 파일을 만드는 방법을 보여 줍니다. 그러나 CLI에서 로컬로 작업하는 경우 로컬 파일을 만들고 경로(~/clouddrive)를 컴퓨터에 있는 로컬 파일의 경로로 바꿀 수도 있습니다.

[bash Cloud Shell](https://shell.azure.com/bash)에서 다음을 입력합니다.

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

다음 .json을 복사하여 파일에 붙여넣습니다.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

완료되면 **Esc** 키를 누르고 **:wq**를 입력하여 파일을 저장하고 닫습니다.

## <a name="create-the-policy"></a>정책 만들기

정책 정의는 사용하려는 구성을 저장하는 데 사용되는 개체입니다. 정책 정의는 규칙 및 매개 변수 파일을 사용하여 정책을 정의합니다. [az policy definition create](/cli/azure/role/assignment?view=azure-cli-latest)를 사용하여 정책 정의를 만듭니다.

이 예제에서 규칙 및 매개 변수는 사용자가 만든 파일이며 Cloud Shell에 .json 파일로 저장됩니다.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>정책 할당

이 예제에서는 [az policy assignment create](/cli/azure/policy/assignment)를 사용하여 리소스 그룹에 정책을 할당합니다. **myResourceGroup** 리소스 그룹에 생성된 VM은 Linux VM 액세스 또는 Linux용 사용자 지정 스크립트 확장을 설치할 수 없습니다. 정책을 할당하려면 리소스 그룹이 있어야 합니다.

[az account list](/cli/azure/account?view=azure-cli-latest)를 사용하여 예제의 ID 대신 사용할 구독 ID를 가져옵니다.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>정책 테스트

새 VM을 만들고 새 사용자를 추가하여 정책을 테스트합니다.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

VM 액세스 확장을 사용하여 **myNewUser**라는 새 사용자를 만듭니다.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>할당 제거

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>정책 제거

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Policy](../../governance/policy/overview.md)를 참조하세요.