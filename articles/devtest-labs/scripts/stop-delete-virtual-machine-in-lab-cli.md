---
title: Azure CLI - 랩에서 가상 머신 중지 및 삭제
description: 이 문서에서는 Azure DevTest Labs의 랩에서 가상 머신을 중지하고 삭제하는 Azure CLI 스크립트를 제공합니다.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 45d5b3453e29571760d47d52a1a21288be31ea90
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136129"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Azure CLI를 사용하여 Azure DevTest Labs의 랩에서 가상 머신 중지 및 삭제

이 Azure CLI 스크립트는 랩에서 VM(가상 머신)을 중지하고 삭제합니다. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | 랩에서 VM(가상 머신)을 중지합니다. 이 작업은 완료하는 데 시간이 걸릴 수 있습니다. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | 랩에서 VM(가상 머신)을 삭제합니다. 이 작업은 완료하는 데 시간이 걸릴 수 있습니다. |


## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure Lab Services CLI 스크립트 샘플은 [Azure Lab Services CLI 샘플](../samples-cli.md)에서 확인할 수 있습니다.
