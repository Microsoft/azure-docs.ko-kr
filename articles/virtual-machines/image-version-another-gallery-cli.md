---
title: CLI를 사용 하 여 다른 갤러리에서 이미지 버전 복사
description: Azure CLI를 사용 하 여 다른 갤러리에서 이미지 버전을 복사 합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e8aabcd1c68272a78b3c1fe88913c5a62496f681
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225838"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Azure CLI를 사용 하 여 다른 갤러리에서 이미지 복사

조직에 여러 갤러리가 있는 경우 다른 갤러리에 저장 된 기존 이미지 버전에서 이미지 버전을 만들 수도 있습니다. 예를 들어 새 이미지를 만들고 테스트 하기 위한 개발 및 테스트 갤러리가 있을 수 있습니다. 프로덕션 환경에서 사용할 준비가 되 면이 예제를 사용 하 여 프로덕션 갤러리에 복사할 수 있습니다. [Azure PowerShell](image-version-another-gallery-powershell.md)를 사용 하 여 다른 갤러리의 이미지에서 이미지를 만들 수도 있습니다.



## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 기존 원본 갤러리, 이미지 정의 및 이미지 버전이 있어야 합니다. 대상 갤러리도 있어야 합니다. 

원본 이미지 버전은 대상 갤러리가 있는 지역에 복제 되어야 합니다. 

이 문서를 진행할 때 필요한 경우 리소스 이름을 바꿉니다.



## <a name="get-information-from-the-source-gallery"></a>원본 갤러리에서 정보 가져오기

새 갤러리에서 복사본을 만들 수 있도록 원본 이미지 정의의 정보가 필요 합니다.

[Az sig list](/cli/azure/sig#az-sig-list) 를 사용 하 여 사용 가능한 이미지 갤러리에 대 한 정보를 나열 하 여 원본 갤러리에 대 한 정보를 찾습니다.

```azurecli-interactive 
az sig list -o table
```

[Az sig 이미지 정의 목록을](/cli/azure/sig/image-definition#az-sig-image-definition-list)사용 하 여 갤러리에서 이미지 정의를 나열 합니다. 이 예제에서는 *myGalleryRG* 리소스 그룹의 *mygallery* 라는 갤러리에서 이미지 정의를 검색 합니다.

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

갤러리에 있는 이미지 버전을 나열 하 고, [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) 를 사용 하 여 새 갤러리에 복사 하려는 이미지 버전을 찾습니다. 이 예제에서는 *Myimagedefinition* 이미지 정의의 일부인 모든 이미지 버전을 찾습니다.

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

필요한 정보가 모두 있으면 [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show)를 사용 하 여 원본 이미지 버전의 ID를 가져올 수 있습니다.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>이미지 정의 만들기 

원본 이미지 버전의 이미지 정의와 일치 하는 이미지 정의를 만들어야 합니다. [Az sig 이미지 정의 표시](/cli/azure/sig/image-definition#az-sig-image-definition-show)를 사용 하 여 새 갤러리에서 이미지 정의를 다시 만드는 데 필요한 모든 정보를 볼 수 있습니다.

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

출력은 다음과 비슷할 것입니다.

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

위의 출력 정보를 사용 하 여 새 갤러리에서 새 이미지 정의를 만듭니다.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>이미지 버전 만들기

[Az image gallery 만들기-이미지-버전](/cli/azure/sig/image-version#az-sig-image-version-create)을 사용 하 여 버전을 만듭니다. 이미지 버전을 만드는 기준선으로 사용할 관리되는 이미지의 ID를 전달해야 합니다. [az image list](/cli/azure/image?view#az-image-list)를 사용하여 리소스 그룹에 있는 이미지에 대한 정보를 가져올 수 있습니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 예제에서 이미지의 버전은 *1.0.0* 이며, 지역 중복 저장소를 사용 하 여 *미국 동부* 지역에서 *미국* 동부 지역에 1 개의 복제본을 만들려고 합니다.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> 동일한 관리 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> `--storage-account-type  premium_lrs`이미지 버전을 만들 때를 추가 하 여, 또는 [영역 중복 저장소](../storage/common/storage-redundancy.md) 를 추가 하 여 Premium storage에 이미지를 저장할 수도 있습니다 `--storage-account-type  standard_zrs` .
>

## <a name="next-steps"></a>다음 단계

[일반화](vm-generalized-image-version-cli.md) 된 이미지 버전 또는 [특수](vm-specialized-image-version-cli.md) 이미지 버전에서 VM을 만듭니다.

또한 [Azure 이미지 작성기 (미리 보기)](./linux/image-builder-overview.md) 를 사용 하면 이미지 버전 생성을 자동화 하는 데 도움이 될 수 있으며, [기존 이미지 버전에서 새 이미지 버전을](./linux/image-builder-gallery-update-image-version.md)업데이트 하 고 만드는 데에도 사용할 수 있습니다. 

구매 계획 정보를 제공 하는 방법에 대 한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조 하세요.
