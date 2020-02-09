---
title: 이미지 잠그기
description: 컨테이너 이미지 또는 리포지토리에 대한 특성을 설정 하 여 Azure container registry에서 삭제 하거나 덮어쓸 수 없습니다.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 93a99f65d5bb21d696ce24365e7c819d2c34a8b0
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75972925"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Azure container registry에서 컨테이너 이미지 잠그기

Azure container registry에서 이미지 버전 또는 리포지토리를 잠가 삭제 하거나 업데이트할 수 없습니다. 이미지나 리포지토리를 잠그려면 Azure CLI 명령 [az acr repository update][az-acr-repository-update]를 사용 하 여 해당 특성을 업데이트 합니다. 

이 문서에서는 Azure Cloud Shell 또는 로컬 (버전 2.0.55 이상 권장)에서 Azure CLI를 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

> [!IMPORTANT]
> 이 문서는 전체 레지스트리를 잠그는 경우에는 적용 되지 않습니다. 예를 들어 Azure Portal의 **설정 > 잠금** 또는 Azure CLI `az lock` 명령을 사용 합니다. 레지스트리 리소스를 잠그면 리포지토리에서 데이터를 생성, 업데이트 또는 삭제할 수 없습니다. 레지스트리를 잠그면 복제를 추가 또는 삭제 하거나 레지스트리 자체를 삭제 하는 등의 관리 작업에만 영향을 줍니다. [예기치 않은 변경을 방지 하기 위해 리소스 잠금](../azure-resource-manager/management/lock-resources.md)에 대한 자세한 정보.

## <a name="scenarios"></a>시나리오

기본적으로 Azure Container Registry의 태그가 지정 된 이미지는 *변경*가능 하므로 적절 한 사용 권한이 있으면 동일한 태그가 있는 이미지를 반복적으로 업데이트 하 고 레지스트리로 푸시할 수 있습니다. 필요에 따라 컨테이너 이미지를 [삭제할](container-registry-delete.md) 수도 있습니다. 이 동작은 이미지를 개발할 때 레지스트리의 크기를 유지 해야 하는 경우에 유용 합니다.

그러나 컨테이너 이미지를 프로덕션 환경에 배포 하는 경우에는 *변경할* 수 없는 컨테이너 이미지가 필요할 수 있습니다. 변경할 수 없는 이미지는 실수로 삭제 하거나 덮어쓸 수 없는 이미지입니다.

레지스트리의 태그 및 버전 이미지에 대한 전략의 컨테이너 이미지에 태그를 지정 하 고 버전을 지정 하기 [위한 권장 사항을](container-registry-image-tag-version.md) 참조 하세요.

[Az acr repository update][az-acr-repository-update] 명령을 사용 하 여 리포지토리 특성을 설정 하 여 다음을 수행할 수 있습니다.

* 이미지 버전 또는 전체 리포지토리 잠그기

* 이미지 버전 또는 리포지토리를 삭제 하지 않고 보호 하지만 업데이트 허용

* 이미지 버전 또는 전체 리포지토리에서 읽기 (끌어오기) 작업 차단

예제는 다음 섹션을 참조 하세요. 

## <a name="lock-an-image-or-repository"></a>이미지 또는 리포지토리 잠금 

### <a name="show-the-current-repository-attributes"></a>현재 리포지토리 특성 표시
리포지토리의 현재 특성을 보려면 다음 [az acr repository show][az-acr-repository-show] 명령을 실행 합니다.

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>현재 이미지 특성 표시
태그의 현재 특성을 보려면 다음 [az acr repository show][az-acr-repository-show] 명령을 실행 합니다.

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>태그로 이미지 잠그기

Myrepo */myrepo:* *myrepo*의 태그 이미지를 잠그려면 다음 [az acr repository update][az-acr-repository-update] 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>매니페스트 다이제스트로 이미지 잠그기

매니페스트 다이제스트 (`sha256:...`로 표시 되는 SHA-256 hash)로 식별 되는 *myrepo/myrepo* 이미지를 잠그려면 다음 명령을 실행 합니다. 하나 이상의 이미지 태그와 연결 된 매니페스트 다이제스트를 찾으려면 [az acr repository show-manifest][az-acr-repository-show-manifests] 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>리포지토리 잠금

*Myrepo/myrepo* 리포지토리 및 모든 이미지를 잠그려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>삭제할 이미지 또는 리포지토리 보호

### <a name="protect-an-image-from-deletion"></a>삭제에서 이미지 보호

*Myrepo/myrepo: 태그* 이미지를 업데이트 하지만 삭제 하지 않도록 허용 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>저장소에서 삭제 방지

다음 명령은 *myrepo/myrepo* 리포지토리를 삭제할 수 없도록 설정 합니다. 개별 이미지를 여전히 업데이트 하거나 삭제할 수 있습니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>이미지 또는 리포지토리에 대한 읽기 작업 방지

*Myrepo/myrepo: tag* 이미지에서 읽기 (끌어오기) 작업을 방지 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

*Myrepo/myrepo* 리포지토리의 모든 이미지에 대한 읽기 작업을 방지 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>이미지 또는 리포지토리 잠금 해제

삭제 하 고 업데이트할 수 있도록 *myrepo/myrepo: tag* 이미지의 기본 동작을 복원 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

삭제 및 업데이트할 수 있도록 *myrepo/myrepo* 리포지토리 및 모든 이미지의 기본 동작을 복원 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 [az acr repository update][az-acr-repository-update] 명령을 사용 하 여 리포지토리의 이미지 버전 삭제 또는 업데이트를 방지 하는 방법을 배웠습니다. 추가 특성을 설정 하려면 [az acr repository update][az-acr-repository-update] 명령 참조를 참조 하세요.

이미지 버전 또는 리포지토리에 대해 설정 된 특성을 보려면 [az acr repository show][az-acr-repository-show] 명령을 사용 합니다.

삭제 작업에 대한 자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제][container-registry-delete]를 참조 하세요.

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

