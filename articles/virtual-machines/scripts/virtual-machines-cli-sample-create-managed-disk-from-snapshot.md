---
title: 스냅샷에서 관리 디스크 만들기(Linux) - CLI 샘플
description: Azure CLI 스크립트 샘플 - 스냅샷에서 관리 디스크 만들기
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 7c5e9e16b81c0befb6524a284d63fb2e106bff7d
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322779"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli-linux"></a>CLI를 사용하여 스냅샷에서 관리 디스크 만들기(Linux)

이 스크립트는 스냅샷에서 관리 디스크를 만듭니다. 이를 사용하여 OS 및 데이터 디스크의 스냅샷에서 가상 머신을 복원합니다. 각 스냅샷에서 OS 및 데이터 관리 디스크를 만든 다음, 관리 디스크를 연결하여 새 가상 머신을 만듭니다. 스냅샷에서 만든 데이터 디스크를 연결하여 기존 VM의 데이터 디스크를 복원할 수도 있습니다.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 스냅샷에서 관리 디스크를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | 스냅샷의 이름 및 리소스 그룹 속성을 사용하여 스냅샷의 모든 속성을 가져옵니다. Id 속성은 관리 디스크를 만드는 데 사용됩니다.  |
| [az disk create](/cli/azure/disk) | 관리 스냅샷의 스냅샷 Id를 사용하여 관리 디스크 만들기 |

## <a name="next-steps"></a>다음 단계

[관리 디스크를 OS 디스크로 연결하여 가상 머신 만들기](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 가상 머신 및 관리 디스크 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
