---
title: 구독에 관리 디스크 스냅샷 복사 - CLI 샘플
description: Azure CLI 스크립트 샘플 - CLI를 사용하여 관리 디스크의 스냅샷을 동일한 구독이나 다른 구독으로 복사(또는 이동)
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ce2eaac6189e3b9c0550948eddfddd018deac90b
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051845"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>CLI를 사용하여 관리 디스크의 스냅샷을 동일한 구독이나 다른 구독으로 복사

이 스크립트는 관리 디스크의 스냅샷을 동일한 구독이나 다른 구독으로 복사합니다. 다음 시나리오에서 이 스크립트를 사용하세요.

1. Premium Storage(Premium_LRS)의 스냅샷을 Standard Storage(Standard_LRS 또는 Standard_ZRS)로 마이그레이션하여 비용을 절감합니다.
1. 로컬 중복 저장소(Premium_LRS, Standard_LRS)의 스냅샷을 영역 중복 저장소(Standard_ZRS)로 마이그레이션하여 ZRS 스토리지의 높은 안정성을 누립니다.
1. 더 오래 보존하기 위해 스냅샷을 같은 지역의 다른 구독으로 이동합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 원본 스냅샷의 ID를 사용하여 대상 구독에 스냅샷을 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | 스냅샷의 이름 및 리소스 그룹 속성을 사용하여 스냅샷의 모든 속성을 가져옵니다. Id 속성은 스냅샷을 다른 구독으로 복사하는 데 사용됩니다.  |
| [az snapshot create](/cli/azure/snapshot) | 부모 스냅샷의 Id와 이름을 사용하여 다른 구독에 스냅샷을 만들어 스냅샷을 복사합니다.  |

## <a name="next-steps"></a>다음 단계

[스냅샷에서 가상 머신 만들기](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 가상 머신 및 관리 디스크 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
