---
title: Azure Container Registry에 이미지를 잠그려면
description: 삭제 하거나 Azure container registry에 덮어쓸 수 있도록 컨테이너 이미지 또는 리포지토리에 대 한 특성을 설정 합니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: ebbfaba158e7ddb669111f097eb1adde2373aa6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60828650"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Azure container registry에서 컨테이너 이미지를 잠그려면

Azure container registry에서 삭제 하거나 업데이트할 수 있도록 리포지토리 또는 이미지 버전을 잠글 수 있습니다. Azure CLI 명령을 사용 하 여 해당 특성을 업데이트 이미지 또는 리포지토리를 잠그려면 [az acr 리포지토리 업데이트][az-acr-repository-update]합니다. 

이 문서에서는 Azure Cloud Shell에서 Azure CLI를 실행 하는 또는 로컬로 (2.0.55 버전 또는 이상 권장). `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

## <a name="scenarios"></a>시나리오

Azure Container Registry에서 태그가 지정 된 이미지는 기본적으로 *변경할 수 있는*이므로 적절 한 권한이 있는 반복적으로 업데이트 하 고 동일한 태그를 사용 하 여 이미지를 레지스트리로 밀어넣기. 컨테이너 이미지를 수도 있습니다 [삭제](container-registry-delete.md) 필요에 따라 합니다. 이 동작은 이미지를 개발 하 고 레지스트리에 대 한 크기를 유지 해야 하는 경우에 유용 합니다.

그러나 프로덕션에 컨테이너 이미지를 배포할 때 해야 할 수 있습니다는 *변경할 수 없는* 컨테이너 이미지입니다. 변경할 수 없는 이미지는는 실수로 삭제 하거나 덮어쓸 수 없습니다. 사용 된 [az acr 리포지토리 업데이트] [ az-acr-repository-update] 수 있도록 저장소 특성을 설정 하는 명령:

* 이미지 버전 또는 전체 리포지토리를 잠그려면

* 삭제에서 이미지 버전 또는 저장소를 보호 하지만 업데이트 허용

* 이미지 버전에는 전체 리포지토리 읽기 (끌어오기) 작업 방지

다음 예 섹션을 참조 하세요.

## <a name="lock-an-image-or-repository"></a>이미지 또는 리포지토리 잠금 

### <a name="show-the-current-repository-attributes"></a>현재 저장소 특성을 표시
저장소의 현재 속성을 보려면 다음을 실행 [az acr 리포지토리 show] [ az-acr-repository-show] 명령:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>현재 이미지 특성 표시
태그의 현재 속성을 확인 하려면 다음을 실행 [az acr 리포지토리 show] [ az-acr-repository-show] 명령:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>이미지 태그로 잠금

잠금에는 *myrepo / myimage:tag* 에서 이미지 *myregistry*, 다음을 실행 합니다 [az acr 리포지토리 업데이트] [ az-acr-repository-update] 명령:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>이미지 매니페스트 다이제스트 잠금

잠금에는 *myrepo/myimage* 매니페스트 다이제스트로 식별 되는 이미지 (로 표시 되는 SHA-256 해시 `sha256:...`), 다음 명령을 실행 합니다. (하나 이상의 이미지 태그와 연결 된 매니페스트 다이제스트를 찾으려면 다음을 실행 합니다 [az acr 리포지토리 표시 매니페스트] [ az-acr-repository-show-manifests] 명령입니다.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>잠금 저장소

잠금에는 *myrepo/myimage* 리포지토리 및 모든 이미지에서 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>이미지 또는 리포지토리 삭제 되지 않도록 보호

### <a name="protect-an-image-from-deletion"></a>이미지 삭제 되지 않도록 보호

수 있도록 합니다 *myrepo / myimage:tag* 이미지를 업데이트할 수 있지만 삭제 되지 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>리포지토리 삭제 되지 않도록 보호

다음 명령 집합을 *myrepo/myimage* 리포지토리를 삭제할 수 없습니다. 여전히 개별 이미지 업데이트 하거나 삭제할 수 있습니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>이미지 또는 리포지토리에 대 한 읽기 작업 방지

읽기 (끌어오기) 작업을 하지 못하도록 합니다 *myrepo / myimage:tag* 이미지에서 다음 명령을 실행:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

모든 이미지에 대 한 읽기 작업을 방지 하기 위해 합니다 *myrepo/myimage* 리포지토리에서 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>이미지 또는 리포지토리를 잠금 해제

기본 동작을 복원 하는 *myrepo / myimage:tag* 삭제 및 업데이트 수 있도록 이미지 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

기본 동작을 복원 하는 *myrepo/myimage* 삭제 및 업데이트 수 있도록 다음 명령을 실행 하 고 모든 이미지 리포지토리:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용에 대 한 학습을 [az acr 리포지토리 업데이트] [ az-acr-repository-update] 리포지토리에서 이미지 버전 업데이트 또는 삭제를 방지 하기 위해 명령입니다. 참조 추가 특성을 설정 합니다 [az acr 리포지토리 업데이트] [ az-acr-repository-update] 명령 참조 합니다.

리포지토리 또는 이미지 버전을 설정 하는 특성을 확인 하려면 사용 합니다 [az acr 리포지토리 표시] [ az-acr-repository-show] 명령입니다.

삭제 작업에 대 한 자세한 내용은 참조 하세요 [Azure Container Registry에 컨테이너 이미지를 삭제할][container-registry-delete]합니다.

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

