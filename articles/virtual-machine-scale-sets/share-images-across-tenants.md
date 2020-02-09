---
title: Azure에서 테넌트 간에 갤러리 이미지 공유
description: 공유 이미지 갤러리를 사용 하 여 Azure 테넌트 간에 VM 이미지를 공유 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276332"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Azure 테넌트 간에 갤러리 VM 이미지 공유

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Azure CLI를 사용 하 여 크기 집합 만들기

AppID, 앱 키 및 테넌트 1의 ID를 사용 하 여 테넌트 1에 대 한 서비스 주체를 로그인 합니다. 필요한 경우 `az account show --query "tenantId"`를 사용 하 여 테넌트 Id를 가져올 수 있습니다.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
AppID, 앱 키 및 테넌트 2의 ID를 사용 하 여 테넌트 2에 대 한 서비스 주체를 로그인 합니다.

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

확장 집합을 만듭니다. 예제의 정보를 사용자 고유의으로 바꿉니다.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>다음 단계

문제 발생 시에는 [공유 이미지 갤러리 문제 해결](troubleshooting-shared-images.md)을 수행할 수 있습니다.
