---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/13/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 36c4757feb367fd39ae94640cb8e8a0f1714a0d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542494"
---
## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 예제를 완료하려면 일반화된 VM의 기존 관리 이미지가 있어야 합니다. 자세한 내용은 [자습서: Azure CLI 2.0을 사용하여 Azure VM의 사용자 지정 이미지 만들기](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 

## <a name="preview-register-the-feature"></a>미리 보기: 기능 등록

공유 이미지 갤러리는 미리 보기 상태이지만 기능을 등록해야 사용할 수 있습니다. 공유 이미지 갤러리 기능을 등록하려면:

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

기능에 등록하는 데 몇 분 정도 걸릴 수 있습니다. 다음을 사용하여 진행률을 확인할 수 있습니다.

```azurecli-interactive
az provider show -n Microsoft.Compute
```

## <a name="create-an-image-gallery"></a>이미지 갤러리 만들기 

이미지 갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 갤러리 이름은 구독 내에서 고유해야 합니다. [az sig create](/cli/azure/sig#az-sig-create)를 사용하여 이미지 갤러리를 만드세요. 다음 예에서는 *myGalleryRG*에 *myGallery*라는 갤러리를 만듭니다.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>이미지 정의 만들기

[az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)를 사용하여 갤러리에 초기 이미지 정의를 만듭니다.

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```

## <a name="create-an-image-version"></a>이미지 버전 만들기 
 
[az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create)을 사용하여 필요에 따라 이미지의 버전을 만듭니다. 이미지 버전을 만드는 기준선으로 사용할 관리되는 이미지의 ID를 전달해야 합니다. [az image list](/cli/azure/image?view#az-image-list)를 사용하여 리소스 그룹에 있는 이미지에 대한 정보를 가져올 수 있습니다. 이 예제에서는 이미지의 버전은 *1.0.0* 스러운에서 5 개의 복제본을 만드는 것은 합니다 *미국 중서부* 지역, 1 개 복제본에서는 *미국 중남부* 지역 및 1 복제본에는 *미국 동부 2* 지역입니다.

```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 5 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

