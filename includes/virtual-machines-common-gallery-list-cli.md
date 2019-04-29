---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7fd5b2051f81a5dc34270a608c1518e8a11678b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542472"
---
## <a name="using-rbac-to-share-images"></a>RBAC를 사용하여 이미지 공유

RBAC(역할 기반 액세스 제어)를 사용하여 구독에서 이미지를 공유할 수 있습니다. 이미지 버전에 대한 읽기 권한을 갖고 있는(구독 포함) 모든 사용자는 이미지 버전을 사용하여 가상 머신을 배포할 수 있습니다.

RBAC를 사용하여 리소스를 공유하는 방법에 대한 자세한 내용은 [RBAC 및 Azure CLI를 사용하여 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)를 참조하세요.


## <a name="list-information"></a>정보 나열

[az sig list](/cli/azure/sig#az-sig-list)를 사용하여 사용 가능한 이미지 갤러리에 대한 위치, 상태 및 기타 정보를 가져올 수 있습니다.

```azurecli-interactive 
az sig list -o table
```

[az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list)를 사용하여 OS 유형 및 상태에 대한 정보를 포함한 갤러리의 이미지 정의를 나열합니다.

```azurecli-interactive 
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

[az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list)를 사용하여 갤러리의 공유 이미지 버전을 나열합니다.

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myGalleryImage -o table
```

[az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show)를 사용하여 이미지 버전의 ID를 가져옵니다.

```
az sig image-version show \
-g myGalleryRG \     
-r myGallery \     
-i myGalleryImage \     
--gallery-image-version-name 1.0.0 \     
--query "id"
```