---
title: Azure CLI 스크립트 샘플 - 2개 가상 네트워크 피어링 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 2개 가상 네트워크 피어링
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 072d62f925d4f4024aa17d648a56dc6f37de00f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848438"
---
# <a name="peer-two-virtual-networks"></a>2개 가상 네트워크 피어링

이 스크립트는 Azure 네트워크를 통해 동일한 지역에서 두 가상 네트워크를 만들고 연결합니다. 스크립트를 실행한 후에 두 개의 가상 네트워크 간 피어링을 만듭니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 컴퓨터 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Azure Virtual Network 및 서브넷을 만듭니다. |
| [az network vnet peering create](https://docs.microsoft.com/cli/azure/network/vnet/peering) | 두 가상 네트워크 간에 피어링을 만듭니다.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 네트워킹 CLI 스크립트 샘플은 [Azure 네트워킹 개요 설명서](../cli-samples.md)에서 찾을 수 있습니다.
