---
title: Azure CLI 스크립트 샘플 - 랩에서 가상 머신 시작 | Microsoft Docs
description: 이 Azure CLI 스크립트는 Azure DevTest Labs의 랩에서 가상 머신을 시작합니다.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 793027916fb0d923cfd4052a4ecfc36fb24c7db7
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136130"
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Azure CLI를 사용하여 Azure DevTest Labs의 랩에서 가상 머신 시작

이 Azure CLI 스크립트는 랩에서 VM(가상 머신)을 시작합니다. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [az lab vm start](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-start) | 랩에서 VM(가상 머신)을 시작합니다. 이 작업은 완료하는 데 시간이 걸릴 수 있습니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure Lab Services CLI 스크립트 샘플은 [Azure Lab Services CLI 샘플](../samples-cli.md)에서 확인할 수 있습니다.
